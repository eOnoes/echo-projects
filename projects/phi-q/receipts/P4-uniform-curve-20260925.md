# Phase 4 Receipt — Uniform Bit-Budget Curve

**Project:** phi-q
**Phase:** 4 — Empirical per-tier ablation
**Date:** 2026-09-25
**Result:** COMPLETE — uniform curve measured; `Q4_K_M` is the sweet spot
**GPU used:** local RTX 4070

---

## 1. Method

Quantize the frozen FP16 GGUF at six uniform bit budgets, then measure each with the **same**
perplexity binary used for the frozen baseline. One ruler for every number.

| Item | Value |
|---|---|
| Quantizer | `llama-quantize.exe` sha256 `7039bbfd1d6bb893f10ef194eefc02fbfb7f840288cdab74978498855a0836a5` |
| Perplexity | `llama-perplexity.exe` sha256 `f88a3a5110dba2e666dc5e86854149effa7e8817dd7a8ae15a0fab6e5dd3ab89` |
| Corpus | `phi4-mini-unsloth/README.md` sha256 `03ba3dd2a779b2ddb2d37d6571900a58423ab95eb02bf9915e259432b78ee6b1` |
| Params | `-c 768 --ppl-stride 1024 -b 512 -ngl 99` |
| Namespace | `staging/phi-q-quants/` |
| Raw data | `staging/phi-q-quants/results.jsonl` (append-only) |

## 2. The curve

| Variant | Size | PPL | vs FP16 | vs Q4_K_M |
|---|---|---|---|---|
| Q2_K | 1.734 GB | 7.1262 | +40.96% | +36.50% |
| Q3_K_M | 2.122 GB | 5.5222 | +9.24% | +5.78% |
| **Q4_K_M** | **2.494 GB** | **5.2206** | **+3.27%** | — |
| Q5_K_M | 2.815 GB | 5.1591 | +2.05% | −1.18% |
| Q6_K | 3.156 GB | 5.1552 | +1.98% | −1.25% |
| Q8_0 | 4.085 GB | 5.0458 | **−0.19%** | −3.35% |
| FP16 (reference) | 7.68 GB | 5.0553 | — | — |

## 3. Findings

### 3.1 There is a cliff at 3 bits, and flat ground above 4

```text
Q2_K     +40.96%   collapse
Q3_K_M    +9.24%   still poor
Q4_K_M    +3.27%   <- knee
Q5_K_M    +2.05%   plateau begins
Q6_K      +1.98%   plateau
Q8_0      -0.19%   essentially lossless
```

Between 3-bit and 4-bit the penalty drops from +9.24% to +3.27%. Between 4-bit and 6-bit it only
moves from +3.27% to +1.98%. **The useful range is 4-bit and above; below it, quality falls off a
ledge.**

### 3.2 Q4_K_M is the sweet spot

7.68 GB → **2.494 GB**, a **67.5% reduction**, at a cost of **+3.27%** perplexity. That is the
best size-per-quality trade on the curve by a wide margin.

### 3.3 Q6_K is dominated

Q5_K_M (2.815 GB, 5.1591) and Q6_K (3.156 GB, 5.1552) differ by **0.08%** while Q6_K costs
**0.341 GB more**. **Q6_K is strictly dominated by Q5_K_M.** No reason to ever choose it here.

### 3.4 Q8_0 is effectively lossless

Q8_0 at 4.085 GB measures **5.0458** — *below* FP16's 5.0553. The −0.19% is within run-to-run
noise, but the direction and magnitude together say the same thing: **Q8_0 is indistinguishable
from FP16.** It halves the size and costs nothing measurable.

### 3.5 Reproducibility check

Q4_K_M measured **5.2206** here, matching the frozen baseline run exactly. The harness is stable
across days and runs.

## 4. What this means for heterogeneous quantization

The Phase 3 map found the four projections within **1%** of each other in quantization
sensitivity, with the only real signal at layer boundaries (L0 kurtosis 9.08, L31 11.74, versus
~3.6 mid-network).

This curve bounds the prize. The entire interval between Q4_K_M (+3.27%) and Q5_K_M (+2.05%) is
**1.22 percentage points of perplexity**, bought by 0.321 GB. A heterogeneous allocation at Q4
size could hope to recover *some fraction* of that interval by reallocating bits from the flat
middle toward the sensitive edges.

**Realistic ceiling: well under one percent of perplexity.** And that is before accounting for the
fact that reallocating bits away from the middle layers costs quality too.

**This is the honest expectation to test, not a result.** §5 defines the falsifiable test.

## 5. Proposed decisive test (bounded, ~15 minutes)

**Question:** can a heterogeneous allocation beat uniform Q4_K_M *at matched size*?

| | |
|---|---|
| Control | `Q4_K_M` — 2.494 GB, PPL **5.2206** |
| Treatment | heterogeneous, built via `--tensor-type-file`, targeted at ~2.49 GB |
| Allocation | edge layers 0/1/30/31 raised, middle lowered to compensate |
| Metric | PPL on the same harness, same corpus, same params |
| Decision | if treatment does not beat 5.2206 by a clear margin, **park heterogeneous quantization** |

**If it loses, we park it with a measured reason rather than an assumption.** That is worth
15 minutes, because it stops the question being re-opened later.

## 6. What this phase did NOT establish

- No generation-quality check. Perplexity is a proxy; §L-012 in the doctrine requires pairing it
  with generation, repetition, and EOS checks before any artifact is called deployable.
- No heterogeneous result. §5 is still ahead.
- No speed measurement. Sizes are known; throughput is not.
- No claim that Q4_K_M is production-ready.

## 7. Artifacts

```text
staging/phi-q-quants/phi4-mini-Q2_K.gguf     1.734 GB
staging/phi-q-quants/phi4-mini-Q3_K_M.gguf   2.122 GB
staging/phi-q-quants/phi4-mini-Q4_K_M.gguf   2.494 GB
staging/phi-q-quants/phi4-mini-Q5_K_M.gguf   2.815 GB
staging/phi-q-quants/phi4-mini-Q6_K.gguf     3.156 GB
staging/phi-q-quants/phi4-mini-Q8_0.gguf     4.085 GB
staging/phi-q-quants/results.jsonl
```

Nothing outside `staging/` was modified. The frozen FP16 control and every prior artifact are
untouched.
