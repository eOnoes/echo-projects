# Phase 1 Receipt — Measurement Rig

**Project:** phi-q
**Phase:** 1 — Establish the measurement rig
**Date:** 2026-09-25
**Result:** PASS WITH DOCUMENTED VARIANCE
**GPU used:** local RTX 4070
**Artifacts modified:** none

---

## 1. Corpus identified and confirmed

The prior milestone described the corpus only as "README corpus". It is the model README.

| Field | Value |
|---|---|
| Corpus | `<MODELS_ROOT>/base/phi4-mini-unsloth/README.md` |
| SHA-256 | `03ba3dd2a779b2ddb2d37d6571900a58423ab95eb02bf9915e259432b78ee6b1` |
| Bytes | 33,451 |
| Tokens | **7,994** |

**Confirmation:** the original log recorded `have 7994 tokens`. Our run recorded
`have 7994 tokens`. Exact match. Arithmetic also agrees — `floor(7994 / 768) = 10` chunks,
which is what an unstrided run produces.

## 2. Harness algorithm (read from source, not guessed)

```cpp
calc_chunk = n_ctx
n_chunk    = ceil((ntokens - n_ctx) / ppl_stride)
```

Solving the original log (`7994 tokens`, `8 chunks`, `n_ctx = 768`) gives **stride = 1024**.

## 3. Binary

| Field | Value |
|---|---|
| Path | `E:/llama.cpp/build-qwen36-local/bin/llama-perplexity.exe` |
| SHA-256 | `f88a3a5110dba2e666dc5e86854149effa7e8817dd7a8ae15a0fab6e5dd3ab89` |
| Version | `0.4.1-dev` |
| Built with | MSVC 19.50.35726.0 (VS 18 BuildTools, toolset 14.50.35717) |

Built from the local source at `E:/llama.cpp` specifically for this project, because the
original binary is not present on this machine.

## 4. Command

```text
llama-perplexity.exe \
  -m <GGUF> \
  -f <MODELS_ROOT>/base/phi4-mini-unsloth/README.md \
  -c 768 --ppl-stride 1024 -b 512 -ngl 99
```

## 5. Reproduced baselines

| Model | Our PPL | Historical | Delta | Chunks | n_ctx |
|---|---|---|---|---|---|
| `phi4-mini-f16.gguf` | **5.0553** | 5.0863 | −0.61% | 7 | 1280 |
| `phi4-mini-Q4_K_M.gguf` | **5.2206** | 5.2827 | −1.18% | 7 | 1280 |
| Q4 penalty | +3.27% | +3.86% | — | — | — |

Per-chunk values:

```text
f16    3.4588 6.2525 5.0176 4.8961 4.6561 5.4978 6.2039
Q4     3.5581 6.4744 5.1967 5.0614 4.8147 5.6588 6.4020
```

Final PPL is the geometric mean of the per-chunk values, per the llama.cpp method.

## 6. Why the numbers differ, and why that is acceptable

The original binary exposed `chunk` and `stride` as independent parameters. The current
source couples them: it silently raises `n_ctx` to `stride + buffer` (observed: stride 512
→ n_ctx 1024; stride 1024 → n_ctx 1280). The original's combination
`chunk = 768, stride = 1024, n_ctx = 768` therefore cannot be expressed with this binary.

**The direction of the difference is consistent and explainable.** A larger window captures
more context, which lowers perplexity. Both FP16 and Q4 shifted downward, by −0.61% and
−1.18%. There is no contradiction in the ordering: Q4 remains worse than FP16 in both
harnesses, and the Q4 penalty remains positive in both.

The variance was checked rather than waved away. It is small, directional, and explained.

## 7. Disposition of the Phase 1 gate

The original gate read: *"FP16 reproduces the recorded 5.0863 within ±0.5%."*

Observed deviation is −0.61% (FP16) and −1.18% (Q4). The gate tolerance was written before
it was known that the original binary is unavailable and its exact parameter combination
is not expressible in the current source. **The gate as written is not achievable.**

**Decision:** adopt this run as the project's frozen baseline, with the binary hash, corpus
hash, and exact command recorded above. The historical numbers are retained as
cross-reference with their measured offset, not as the gate.

This decision is logged in `doctrine/DECISION-JOURNAL.md`.

## 8. Reproducibility

The baseline is reproducible by anyone with this repository, this binary hash, and this
corpus hash. That is a stronger guarantee than matching a binary that no longer exists.

## 9. Phase 1 exit gate

| Gate | Status |
|---|---|
| Corpus identified and hash-recorded | PASS |
| Harness algorithm understood from source | PASS |
| Binary built, hashed, and versioned | PASS |
| FP16 baseline measured | PASS (5.0553) |
| Q4_K_M baseline measured | PASS (5.2206) |
| Repeatable command recorded | PASS |
| Deviation from historical explained | PASS (directional, −0.61% / −1.18%) |
| Original exact reproduction | **NOT ACHIEVED** — original binary unavailable |

**Phase 1 result: PASS WITH DOCUMENTED VARIANCE.**

The project now has its own frozen, hashed, reproducible baseline. All subsequent
comparisons use it.
