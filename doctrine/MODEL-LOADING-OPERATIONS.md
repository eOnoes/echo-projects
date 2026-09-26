# Model Loading — Operations Doctrine

**Status:** ACTIVE REFERENCE
**Scope:** Loading and running any GGUF checkpoint on Eddie's local box with llama.cpp
or a llama.cpp-derived fork.

**This document is the authority for HOW a model gets loaded.** If a load command
contradicts this document, the document wins until it is amended.

---

## 1. The hardware envelope

```text
CPU       AMD Ryzen 9 7900X   12 cores / 24 threads
RAM       63.1 GB total       (assume ~34-37 GB usable with desktop apps running)
GPU       NVIDIA RTX 4070     12,282 MiB (12 GB), compute 8.9 (sm_89)
STORAGE   E:\ (vault + tools)
```

**The governing constraint:** a 12 GB card cannot hold a large model. Everything
here is about the RAM/VRAM hierarchy, not about "will it fit."

```text
VRAM is scarce and fast.  RAM is plentiful and slow.
=> the art is deciding WHAT lives where.
```

---

## 2. The five proper flags

These are the flags that matter on this box. Verified against the fork's own
`--help` output, not assumed.

### 1. `-ngl N` / `--n-gpu-layers N`

How many transformer layers get offloaded to VRAM.

```text
dense model        → the maximum N that fits, worked DOWN until VRAM is under budget
MoE model          → usually LOW or 0; see -cmoe, which is the better lever
tool               → nvidia-smi before and after. Never guess.
```

### 2. `-cmoe` / `--cpu-moe`  and  `-ncmoe` / `--n-cpu-moe`

**These are the MoE placement flags — and they do NOT live in the same tools.**

```text
-cmoe  / --cpu-moe        llama-completion, llama-cli     NOT in llama-bench
-ncmoe / --n-cpu-moe N    llama-bench AND llama-completion
```

**VERIFY PER TOOL.** The binaries in this project have different flag sets.
A flag that works in `llama-completion` may not exist in `llama-bench`, and a
rejected flag prints help and exits 0 - so the test silently does nothing while
looking successful. Confirm every flag against that binary's own `--help`.

```text
-cmoe            keep ALL MoE weights in system RAM (run-time tools)
-ncmoe N         keep MoE weights of the FIRST N layers in CPU (both tools)
```

**On a 12 GB card the placement that matters is the parameter split, not the
layer count.** Measured on K2-Horizon 36B-A4B:

```text
attention + MoVA value experts   ~9.3 B params   ~5.2 GB   <- fits VRAM easily
routed FFN experts              ~26.5 B params  ~15.0 GB   <- must stay in RAM
```

So the target is **attention+MoVA on GPU, experts in RAM**. Bare `-ngl 99` does
NOT achieve this — it offloads whole layers until VRAM fills, putting expert
weights on the GPU while leaving attention on the CPU.

`-ncffn` / `--n-cpu-ffn` was documented here earlier and does **not** exist in
either binary's `--help` on this build. Removed as unverified.

### 3. `-fa` / `--flash-attn [on|off|auto]`

Flash attention. Default `auto`.

```text
ON   → lower VRAM for the KV cache, faster attention
       REQUIRED by some models' published recipes (K2-Horizon asks for FA3)
OFF  → use when a model misbehaves with it; a real diagnostic step
```

### 4. `-t N` / `--threads N`

```text
7900X = 12 physical cores / 24 logical.
START at 12 (physical cores). Test 24. llama.cpp performance is usually flat
between them and can DEGRADE at 24 from SMT contention.
NEVER leave this unset on a benchmark - it is a reported parameter.
```

### 5. `-ot` / `--override-tensor <pattern>=<buffer type>`

**The precision scalpel.** Place individual tensors on individual backends.

```text
This is the flag that makes a 12 GB card useful for a 22 GB model.
Rather than all-or-nothing offload, you place attention on GPU and
experts in RAM, and you can tune it per layer.

Example shape (pattern syntax varies by build - check --help):
  -ot "blk\.\d+\.ffn_(gate|up|down)_exps=CPU"
```

---

## 3. Supporting flags (not in the five, but load-bearing)

```text
-m MODEL        the model path (Windows forward-slash form: E:/path/to.gguf)
-c N            context size. Memory scales with this. Raise deliberately.
-n N            tokens to generate
-no-cnv         plain completion mode.                            <-- SEE §5.1
--jinja         use the model's own Jinja chat template           <-- SEE §5.1
-lm, --load-mode <auto|none|mmap|mlock|mmap+mlock|dio>
                residency control. Replaces the removed --no-mmap. <-- SEE §5.2
-r N            benchmark repetitions
-p N            prompt size for benchmarking
```

### Build-time knobs that change runtime speed

```text
GGML_OPENMP        ON  -> thread the CPU path with OpenMP. Check it.
GGML_NATIVE        ON  -> -march=native. With this ON, the individual
                          GGML_AVX2/AVX512 flags read OFF and should be ignored.
GGML_CPU_REPACK    ON  -> enables the fast repacked CPU kernels
CMAKE_BUILD_TYPE   Release + -O3 -DNDEBUG. A Debug build is ~10x slower.
```

**Verify these before blaming the model.** All four were correct on the build
measured here - which is why the cause lay elsewhere (§5.3).


---

## 4. Recipes by model class

### Class A — small dense (fits entirely in VRAM)
*e.g. Phi-4-mini Q4_K_M at 2.5 GB*

```text
-ngl 99 -fa auto -t 12
Everything on GPU. Fastest possible. No RAM involvement.
```

### Class B — large dense (exceeds VRAM, fits RAM)
*e.g. a 20-30B dense quant at 12-18 GB*

```text
-ngl <N layers that fit> -fa auto -t 12 --no-mmap
Partial offload. Sweep N upward until VRAM is ~1-2 GB from full.
-cmoe does NOT apply - this is not a MoE.
```

### Class C — MoE (exceeds VRAM, fits RAM)  ← the K2-Horizon case
*e.g. K2-Horizon 36B-A4B Q4_K_M at 20.82 GiB*

```text
-ngl 99 -cmoe -fa auto -t 12 --no-mmap
       ^^^^^^ experts to RAM, attention + dense to VRAM

If -ngl 99 overflows VRAM, step DOWN:
  -ngl 24 -cmoe            most layers' attention on GPU
  -ncmoe 20                only the first 20 layers' experts in RAM
  -ot ...                  tensor-level surgery as a last resort
```

**Do not skip `--no-mmap` on this class.** See §5.2 — it is the difference
between disk-bound and RAM-bound, and it is a 5-10x effect.

---

## 5. Known pitfalls — each one cost real time

### 5.1 The chat template abort

```text
Symptom   terminate called after throwing an instance of 'std::runtime_error'
          what():  this custom template is not supported, try using --jinja
Cause     the GGUF carries a template the build's built-in list does not know
Fix       add --jinja      (use the model's own template)
       or -no-cnv         (skip conversation mode, plain completion)
NOT a model problem. The weights loaded fine - this fires AFTER loading.
```

### 5.2 The mmap trap

```text
Symptom   generation is several times slower than RAM bandwidth allows
Cause     llama.cpp mmaps the GGUF by default. The OS demand-pages it. If the
          file is not resident, reads land on disk.
Fix       force full residency. THE FLAG NAME DEPENDS ON THE BUILD:
            -lm, --load-mode <auto|none|mmap|mlock|mmap+mlock|dio>
            -mmp, --mmap <0|1>        (DEPRECATED in favour of --load-mode)
          --no-mmap WAS REMOVED in newer builds and will print help + exit 0.
```

**Check `--help` before assuming a flag exists.** A rejected flag on this build
prints the full help text and exits **0**, which reads like success. That cost
time here: a whole benchmark leg was gone and reported as "exit=0".

**Residency check:** a model whose working set is far below its file size is not
resident. Note that mmap'd pages count toward *working set* but not *private*
memory — so `PrivateMemorySize` alone will mislead you.

### 5.3 The wrong-binary trap  ← THE EXPENSIVE ONE

```text
Symptom   a model runs, but throughput is ~10x below the arithmetic
Cause     MULTIPLE FORKED BUILDS of the same runtime exist on disk.
          A sibling directory may hold a newer, optimized, or fixed build.
          Benchmarking the plain fork while an "-opt" build sits next to it
          measures the wrong artifact entirely.
```

**Before benchmarking ANY model on a fork, inventory the siblings:**

```bash
ls -d <projects-root>/*<arch>*            # every project for this architecture
find <projects-root> -name "llama-bench.exe"   # every built binary
sha256sum .../llama-bench.exe             # prove they are different artifacts
ls -d <project>/build-*                   # every build config
grep -iE 'OPENMP|AVX|REPACK|ET:' <build>/CMakeCache.txt
```

**Record the binary hash with every benchmark.** `build: unknown (0)` from
llama-bench means the number cannot be tied to a binary — attach the hash
yourself or the result is unverifiable.

**Evidence that two builds differ** (real case): 12.5 MB vs 39.0 MB,
`GGML_OPENMP` OFF vs ON, and one containing a source comment that it *replaces
a crashing attention path* for the architecture in question.

### 5.4 Memory pressure, and how it distorts benchmarks

```text
Symptom   prefill (pp) numbers far below expectation; system feels stalled
Cause     Windows "Available MBytes" at or near 0. The OS trims the standby
          cache and evicts the model's own pages mid-run.
Check     Get-Counter '\Memory\Available MBytes'
          Standby cache near zero with a large model loaded = thrashing
Fix       close background memory hogs BEFORE loading. Browser processes
          (one per tab/helper) and desktop chat apps are the usual offenders.
          Releasing them can return far more than their nominal footprint,
          because Windows releases compressed memory and standby cache too.
```

**This is not a footnote — it changed the measured numbers materially:**

```text
                        pp512         tg128
under memory pressure    60.24         3.84
after freeing ~43 GB    101.33         4.08      <- +68% prefill
```

**Always record `Available MBytes` alongside a benchmark**, or a starved box
gets recorded as a slow model.

### 5.5 Read the pp/tg asymmetry — it localizes the bottleneck

```text
pp  healthy, tg terrible      -> per-token path (expert gather, routing, attention
                                 per-token work). NOT raw bandwidth, NOT compute.
pp  and tg both bad           -> load/placement problem (residency, offload, threads)
tg WORSENS with more threads  -> a serialized per-token cost
pp and tg both scale          -> genuinely compute/bandwidth bound; accept it
```

**A memory-bandwidth-bound workload is thread-insensitive, not thread-degraded.**
If adding threads makes generation slower while making prefill faster, the cost
is not bandwidth. That single observation points at the right subsystem faster
than any amount of profiling.


### 5.6 Different binaries have different flag sets

```text
Symptom   a flag documented in one tool silently does nothing in another
Example   -cmoe / --cpu-moe exists in llama-completion and llama-cli
          BUT NOT in llama-bench - and llama-bench has -ncmoe, which they share
Worse     a rejected flag prints the FULL help text and exits 0, so an
          aborted test looks like a successful one
Rule      verify every flag against THE SPECIFIC BINARY you are invoking,
          with that binary's own --help. Never carry a flag between tools.
```

Real cost: a benchmark leg was written up as "tested, no gain" when in fact
the flag had been rejected and **the test never ran**.

### 5.7 Thread count: fewer than you think on a multi-CCD CPU

```text
Measured on Ryzen 9 7900X (2 CCDs x 6 cores, 32 MB L3 each), CUDA build,
K2-Horizon 36B-A4B, -ngl 99:

  -t 12   11.95 t/s
  -t  8   12.78
  -t  6   13.10     <- peak, matches ONE CCD
  -t 24    2.49     <- catastrophically worse (CPU-only build)

On a chiplet CPU the default (all threads) can be WORSE than one CCD's worth,
because cross-CCD traffic crosses the infinity fabric. Sweep the thread count;
do not assume more is better.
```

**And do not assume flag wins are additive.** `-t 6` alone gave 13.10; `-t 6`
plus explicit `-fa on` gave 13.03 - within noise. Stacking "known wins" without
re-measuring produces confident folklore.


### 5.8 Silent exit with no output (Windows)

```text
Symptom   exit code with ZERO log output, sometimes 127 or a crash
Cause     MinGW runtime DLLs not on PATH for a MinGW-built binary
Fix       export PATH="/d/Mingw64/mingw64/bin:/c/Program Files/Git/mingw64/bin:$PATH"
Also      127 = "command not found" class error - check the binary path AND the DLLs
```

### 5.9 Build hash "unknown (0)"

```text
llama-bench reporting build: unknown (0) means the build was not hash-stamped.
Consequence: a benchmark number that CANNOT be tied to a specific binary.
             Reproducibility is broken by construction.
Action: record the binary's own hash alongside any benchmark result.
```

### 5.10 VRAM not released after a run

```text
After every run, confirm:  nvidia-smi --query-gpu=memory.used --format=csv,noheader
Baseline on this box is ~1165 MiB (Windows desktop).
Anything above ~1300 MiB with no work running = a process is still holding VRAM.
```

---

## 6. Pre-flight checklist (run before every load)

```text
[ ] Model file hash verified against its manifest
[ ] Free RAM   >= model size + 4 GB headroom
[ ] VRAM free  = 12282 - ~1165 baseline
[ ] MinGW DLLs on PATH (Windows / MinGW builds)
[ ] Correct binary: the FORK if the arch needs it, stock llama.cpp otherwise
[ ] --no-mmap for anything resident in RAM
[ ] -t set explicitly
[ ] Command line CAPTURED for the receipt
```

## 7. Post-flight checklist

```text
[ ] exit code recorded
[ ] throughput recorded (pp / tg) with the test size AND thread count
[ ] free RAM + VRAM after, confirming clean release
[ ] binary hash recorded alongside the numbers
[ ] result filed as a receipt, not a chat message
```

---

## 8. Standing rules

1. **One ruler.** A number without its exact command, test size, thread count and
   binary hash is an anecdote, not a measurement. (See W-002 - cross-ruler error.)
2. **Prove residency before declaring slowness.**
3. **Never report a benchmark from a cold, minimum-size test.** `-p 64 -n 32 -r 1`
   is not a benchmark, it is a smoke test.
4. **Unload and verify.** Check `nvidia-smi` after every run.
5. **Stock llama.cpp first.** Only use a fork when the architecture demands it,
   and record which fork and which build.
6. **INVENTORY SIBLING BUILDS BEFORE BENCHMARKING.** When a model needs a fork,
   there may be several forks for the same architecture. Check for an optimized
   or fixed sibling and hash every candidate binary. (§5.3)
7. **Check `--help` before trusting a flag.** A rejected flag can print help and
   exit 0, which looks like success.
8. **Record memory state with the numbers.** `Available MBytes` during the run,
   or a starved box gets recorded as a slow model. (§5.4)
9. **Read the pp/tg asymmetry before theorizing.** It localizes the bottleneck
   to compute, bandwidth, residency, or a serialized per-token cost. (§5.5)
10. **READ THE MODEL'S OWN DOCUMENTATION FIRST.** The HF model card and README
   are the authority on how a model is meant to run - including which runtime
   fork is required. The K2-Horizon README named the required fork and linked
   it, and an hour of source archaeology happened because nobody read it.
   Check the README *before* touching source, building, or benchmarking.
11. **COPY A WORKING CONFIGURATION, DO NOT RECONSTRUCT IT.** When a build works,
   its `CMakeCache.txt` (or equivalent) is the authoritative record of how to
   configure it. Re-typing a build command from memory omits flags - here
   `-D_WIN32_WINNT=0x0A00`, without which `cpp-httplib` refuses to compile.
12. **A SMALL MODEL IS THE CONTROL.** Before benchmarking a large model on a
   runtime, run a small model of the **same family** on it. It loads in seconds
   instead of minutes, and it separates "the runtime is broken" from "this model
   is inherently slow." The dense 7B was what finally explained the 36B.
13. **Prove a hypothesis by measurement, not by code reading.** Reading source
   produced four confident-and-wrong conclusions in one session. Each was
   settled in minutes once actually run.

## 9. Session log — what was actually learned, and where I was wrong

Recorded so the same ground is not re-covered, and so the incorrect
intermediate conclusions are not mistaken for findings.

```text
CLAIM                                VERDICT    WHY
model won't load on this box         WRONG      it loads in 2m43s, 22.37 GB, exit 0
--no-mmap is the residency flag      WRONG      removed; use --load-mode. Flag
                                                rejection printed help and exit 0
the model is not resident            PARTLY     mmap'd pages count in working set,
                                                not private memory - my read was off
Debug/scalar build is the cause      REFUTED    Release, -O3, AVX2/AVX512 in binary
missing K-quant kernels in ggml-et   REFUTED    GGML_ET:BOOL=OFF - not even compiled
Q4_K lacks a fast mul_mat_id path    REFUTED    q4_K_8x4/8x8/16x1 traits all present
memory pressure is the tg cause      REFUTED    tg 3.84 -> 4.08 after freeing 43 GB
                                                (pp moved a lot, tg did not)
the -opt sibling build is better     REFUTED    it SEGFAULTS (exit 139) on the
                                                real checkpoint
wrong fork / wrong llama.cpp         REFUTED    source diff vs the official
                                                MBZUAI-IFM fork = 0. We were on
                                                the right build all along.
```

**Resolution of the open item:** the model's README named the required fork
(`MBZUAI-IFM/llama.cpp` @ `model/K2Horizon`). It was cloned, built, and diffed
against the build already on disk — **source diff count 0.** The wrong-fork
hypothesis, which was the strongest lead of the session, was **wrong**.

**Final measured result** (official runtime, 12 threads, clean memory):

```text
                              pp512     tg128     read/token   effective BW
dense 7B Q6_K                77.38     5.83      6.88 GiB     ~40 GB/s
MoE 36B-A4B Q4_K_M           71.26     4.00      ~2.4 GB      ~9.6 GB/s
```

The MoE reads 2.9x less per token and runs 1.46x slower. **The bottleneck is
llama.cpp's CPU MoE path at batch=1, not the build.** See
`projects/k2-horizon-local/receipts/K2H-load-investigation-20260926.md`.


**The pattern worth noticing:** every one of those was checked rather than
assumed, and four were wrong. That is the point of checking. Two of the errors
were mine, and one of them (the residency claim) was published in an earlier
draft of this document before being corrected here.

