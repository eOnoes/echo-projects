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

### 2. `-cmoe` / `--cpu-moe`

**Keep all Mixture-of-Experts weights in system RAM.**

```text
This is THE flag for MoE models on this box.
The experts are the bulk of the parameters but only a fraction are active per
token, so holding them in RAM costs bandwidth, not capacity.
```

Companion flags, for partial placement:

```text
-ncmoe N   / --n-cpu-moe N    MoE weights of the first N layers -> CPU
-ncffn N   / --n-cpu-ffn N    dense FFN weights of first N layers -> CPU
                              (for DENSE models; use -ncmoe for MoE experts)
```

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


### 5.6 Silent exit with no output (Windows)

```text
Symptom   exit code with ZERO log output, sometimes 127 or a crash
Cause     MinGW runtime DLLs not on PATH for a MinGW-built binary
Fix       export PATH="/d/Mingw64/mingw64/bin:/c/Program Files/Git/mingw64/bin:$PATH"
Also      127 = "command not found" class error - check the binary path AND the DLLs
```

### 5.7 Build hash "unknown (0)"

```text
llama-bench reporting build: unknown (0) means the build was not hash-stamped.
Consequence: a benchmark number that CANNOT be tied to a specific binary.
             Reproducibility is broken by construction.
Action: record the binary's own hash alongside any benchmark result.
```

### 5.8 VRAM not released after a run

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
wrong fork is the cause              OPEN       a second, optimized k2-horizon build
                                                exists on disk and has not been
                                                benchmarked yet
```

**The pattern worth noticing:** every one of those was checked rather than
assumed, and four were wrong. That is the point of checking. Two of the errors
were mine, and one of them (the residency claim) was published in an earlier
draft of this document before being corrected here.

