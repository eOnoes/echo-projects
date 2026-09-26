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
--no-mmap       load fully into RAM instead of demand-paging      <-- SEE §5.2
-r N            benchmark repetitions
-p N            prompt size for benchmarking
```

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

### 5.2 The mmap trap  ← THE EXPENSIVE ONE

```text
Symptom   generation is 5-10x slower than the hardware's RAM bandwidth allows
Cause     llama.cpp mmaps the GGUF by default. The OS demand-pages it. If the
          file is not resident, EVERY EXPERT READ DURING GENERATION HITS DISK.
Diagnosis check free RAM while the model is loaded:
          a 22 GB model that consumes 1.5 GB of RAM is NOT LOADED.
Fix       --no-mmap     (force full residency; slower startup, far faster generation)
Also      verify with nvidia-smi / free RAM that the model is actually resident
```

**Rule: before concluding a model is "slow," prove it is resident.** An
unresident model's benchmark measures the disk, not the model.

### 5.3 Silent exit with no output (Windows)

```text
Symptom   exit code with ZERO log output, sometimes 127 or a crash
Cause     MinGW runtime DLLs not on PATH for a MinGW-built binary
Fix       export PATH="/d/Mingw64/mingw64/bin:/c/Program Files/Git/mingw64/bin:$PATH"
Also      127 = "command not found" class error - check the binary path AND the DLLs
```

### 5.4 Build hash "unknown (0)"

```text
llama-bench reporting build: unknown (0) means the build was not hash-stamped.
Consequence: a benchmark number that CANNOT be tied to a specific binary.
             Reproducibility is broken by construction.
Action: record the binary's own hash alongside any benchmark result.
```

### 5.5 VRAM not released after a run

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
