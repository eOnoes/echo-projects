# Receipt — K2-Horizon MoVA-36B-A4B: local deployment investigation

**Project:** k2-horizon-local (new)
**Date:** 2026-09-26
**Status:** COMPLETE — question answered, with an honest negative on the original expectation.
**Compute used:** local RTX 4070 only. **No paid resource was created at any point.**

---

## 1. The question

Can `K2-Horizon-MoVA-36B-A4B-Q4_K_M` (22.37 GB / 20.82 GiB) be loaded and run
usably on the local box (Ryzen 9 7900X, 63 GB RAM, RTX 4070 12 GB)?

**Answer: it loads and runs, but it is roughly 4x slower than the hardware's
bandwidth allows, and 30 t/s is not reachable CPU-only on this machine.**

## 2. Headline measurements

All on the **official** runtime, 12 threads, `-ngl 0`, clean memory
(`~42 GB available`), 3 reps, `-p 512 -n 128`:

```text
                                pp512       tg128     read/token    effective BW
K2-Horizon 7B Q6_K (dense)     77.38       5.83      6.88 GiB      ~40 GB/s
K2-Horizon 36B-A4B Q4_K_M      71.26       4.00      ~2.4 GB       ~9.6 GB/s

build: 42adf01 (1)   <- hash-traceable
```

**The comparison is the finding.** The dense model pushes **40 GB/s** through this
CPU. The MoE pushes **9.6 GB/s** — while reading *less* data per token. Same
binary, same box, same thread count.

## 3. Environment / state corrections from this session

### 3.1 The model DOES load (earlier claim of failure was wrong)

```text
load time           2 min 43 s for 22.37 GB
thread pool         12 threads initialised
architecture        k2-horizon accepted, weights read
exit                0 on the load path; the only failure was the CLI chat template
```

The first load "failure" was `this custom template is not supported, try using
--jinja` — which fires **after** weights are resident. Fix: `--jinja` or `-no-cnv`.

### 3.2 The GGUF expert configuration (read from the header, not the name)

```text
k2-horizon.block_count                  48
k2-horizon.leading_dense_block_count     3
k2-horizon.embedding_length           2560
k2-horizon.expert_count                100
k2-horizon.expert_used_count             8     <- 8, not 2
k2-horizon.expert_shared_count           1
k2-horizon.expert_feed_forward_length  768
k2-horizon.moe_every_n_layers            1

k2-horizon.attention.value_expert_count          64
k2-horizon.attention.value_expert_used_count      4    <- MoVA: attention routes too
k2-horizon.attention.value_length               128
```

`A4B` checks out: ~4.3B active per token (2.39B expert + dense + attention).
**The config does not explain the shortfall** — the arithmetic says ~18 t/s
ceiling, not 4.

## 4. What was ruled out — and how

Every hypothesis was tested. Four were refuted, two of them mine.

| Hypothesis | Test | Verdict |
|---|---|---|
| Model won't load on this box | ran it | **WRONG** — loads in 2m43s, exit 0 |
| `--no-mmap` is the residency flag | `--help` | **WRONG** — removed; use `--load-mode`. Rejection printed help + exit 0 |
| Model not resident in RAM | WS vs private-memory | **PARTLY WRONG** — mmap pages count in *working set*, not private memory. My first read was off |
| Debug/scalar build is the cause | CMakeCache | **REFUTED** — Release, `-O3 -DNDEBUG`, AVX2/AVX512 in binary |
| Missing K-quant kernels in `ggml-et` | CMakeCache | **REFUTED** — `GGML_ET:BOOL=OFF`, not even compiled |
| Q4_K lacks a fast `mul_mat_id` path | repack.cpp traits table | **REFUTED** — `q4_K_8x4/8x8/16x1` all present |
| Memory pressure caused the slow tg | freed 43 GB, re-ran | **REFUTED** — pp 60.24→101.33 (+68%), tg 3.84→4.08 (+6%) |
| Wrong fork / wrong llama.cpp | cloned official, diffed source | **REFUTED** — source diff count **0**; identical to what we were using |
| The `-opt` sibling build is better | benchmarked it | **REFUTED** — **segfaults** (exit 139) on the real checkpoint |

**The wrong-fork hypothesis was the user's instinct and my best lead, and it was
wrong.** The upstream fork named by the model's README (`MBZUAI-IFM/llama.cpp`
@ `model/K2Horizon`, HEAD `42adf01`, 2026-09-17) is **source-identical** to the
build already on disk. Verified by `diff -rq src` → **0 differences**.

## 5. Root cause, stated with appropriate confidence

**The bottleneck is llama.cpp's CPU Mixture-of-Experts path at batch=1.**

Reasoning from the measurements:

```text
dense 7B  : reads the WHOLE model per token (6.88 GiB) -> 5.83 t/s -> 40 GB/s
MoE 36B   : reads ~2.4 GB per token (8/100 experts)    -> 4.00 t/s -> 9.6 GB/s

The MoE reads 2.9x LESS data per token and is 1.46x SLOWER end-to-end.
=> per-byte efficiency of the MoE path is ~4x worse than the dense path.
```

Mechanism (consistent with llama.cpp internals, not proven by profiling here):
with 100 experts and a single token, each layer performs 8 separate small
scattered GEMVs, with OpenMP synchronisation per expert and poor prefetch
locality. There is no batching to amortise it during generation.

**Confidence: the measurement is solid; the mechanism is inference, labelled as
such.** It was not confirmed by a profiler.

## 6. What this means for expectations

```text
CPU ceiling by bandwidth    ~18 t/s  (MoE at the dense path's achieved 40 GB/s)
CPU delivered                4.00 t/s
30 t/s requires             72 GB/s  = 80% of DDR5-5600 peak. Not achievable.
```

**30 t/s is not reachable CPU-only on this machine, by either path.** The
realistic CPU ceiling is ~15-18 t/s and the MoE path currently delivers ~4.

## 7. The remaining lever: the CUDA build

`build-cuda/` exists but has **no binaries** — blocked at missing `cl.exe`.

The build that would matter:

```text
-ngl 99 -cmoe -fa auto -t 12      experts in RAM, attention + dense on the 4070
```

Rationale: `-cmoe` is purpose-built for exactly this shape on a 12 GB card, and a
GPU build moves the expert gather off the CPU's serial path. **That is the only
change with a plausible chance of a large jump.** It requires resolving the
Visual Studio toolchain issue.

**Not attempted tonight. No claim is made about its outcome.**

## 8. Verified facts worth keeping

```text
1. The correct runtime is MBZUAI-IFM/llama.cpp @ model/K2Horizon (HEAD 42adf01)
   - named in the GGUF repo README
2. It builds clean on Windows/MinGW with: Release, Ninja, -march=native,
   GGML_OPENMP=ON, and -D_WIN32_WINNT=0x0A00 (REQUIRED - cpp-httplib fails without it)
3. Build it with the D:/Mingw64/mingw64 toolchain (gcc 14.2.0, ucrt-posix-seh)
4. K2-Horizon-4B-Q8_0 and K2-Horizon-7B-Q6_K dense members are also present locally
   and make excellent fast controls (seconds to load vs minutes)
5. MoVA is real and routed: attention.value_expert_count=64, used=4
6. RAM headroom matters: closing Chrome + ChatGPT desktop moved available memory
   0.0 GB -> 43.5 GB and changed pp512 by +68%
```

## 9. Mistakes made, recorded

```text
1. Never read the model's own README. It named the required fork and linked it.
   I reverse-engineered the architecture from source instead. ~1 hour lost.
2. Benchmarked a 22 GB model with -p 64 -n 32 -r 1 (the minimum possible test)
   and treated the result as a measurement rather than a smoke test.
3. Claimed the model was "not resident" from a private-memory reading alone.
4. Re-typed a cmake command from memory and omitted -D_WIN32_WINNT=0x0A00,
   which the working build's own CMakeCache.txt contained.
5. Concluded the -opt build was broken-after earlier, then nearly concluded it
   was fine when my 60s timeout silently killed a load that needs 2m43s.
```

**The pattern in 1, 4, and 5: a known-good artifact was on disk and I
reconstructed it by hand instead of reading it.**

## 10. Artifacts

```text
E:/Echo-DB/projects/k2horizon-official/          official fork, built (build-cpu/bin/)
    sha256 llama-bench.exe 0a84d2b9e72ed81b83a78ae469f899f2518bd725412cbd2e3d93a9f47039bb3a
E:/Echo-DB/models/K2-Horizon-MoVA-36B-A4B-GGUF/K2-Horizon-MoVA-36B-A4B-Q4_K_M.gguf
E:/Echo-DB/models/K2-Horizon/K2-Horizon-7B-Q6_K.gguf          (control)
E:/Echo-DB/models/K2-Horizon/K2-Horizon-4B-Q8_0.gguf
E:/ternary-lab/staging/k2-official-bench.log
E:/ternary-lab/staging/official-build2.log
E:/ternary-lab/staging/read_gguf_meta.py
E:/Echo-DB/echo-projects/doctrine/MODEL-LOADING-OPERATIONS.md
```
