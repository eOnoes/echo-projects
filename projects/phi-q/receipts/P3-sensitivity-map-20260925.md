# Phase 3 Receipt — Per-Tensor Sensitivity Map

**Project:** phi-q
**Phase:** 3 — Sensitive-tensor localization
**Date:** 2026-09-25
**Result:** COMPLETE — one hypothesis refuted, one method limit identified
**GPU used:** none (CPU only)
**Artifacts modified:** none (read-only on the checkpoint)

---

## 1. Method

For all **128** quantizable weight tensors (32 layers × 4 projections), simulate group-wise
quantization and measure relative reconstruction error:

```text
rel_err = ||W - W_hat||_F / ||W||_F
```

Schemes evaluated:

| Scheme | Definition |
|---|---|
| `pertensor` | one absmean scale for the entire tensor, codes {−1,0,+1} |
| `g256 / g128 / g64 / g32` | per-group absmean scale, codes {−1,0,+1}, group along input dim |
| `3/4/6/8-bit` | symmetric uniform, `2^(b−1) − 1` magnitude levels, absmax per group 128 |

Script: `staging/sensitivity_map.py` · Output: `staging/sensitivity-map.{json,csv}`

**Sanity check built in:** at 2 bits the uniform quantizer reduces to absmax ternary by
construction. That identity caught an earlier bug in this script (see §7).

## 2. H-002 is NOT supported

H-002 claimed *group scales, not the ternary codes, carry the representational capacity*.

| Scheme | Mean relative error | vs per-tensor |
|---|---|---|
| per-tensor | 0.5363 | — |
| group 256 | 0.5161 | 3.8% better |
| group 128 | 0.5138 | 4.2% better |
| group 64 | 0.5116 | 4.6% better |
| group 32 | 0.5070 | 5.5% better |

Grouping from per-tensor all the way to group-32 improves reconstruction error by **5.5%**.
That is far too small to explain the difference between a collapsed model (Line A, 44,176 PPL)
and a runnable one (Line B).

**Verdict: H-002 REFUTED, by this metric.** Group size is close to irrelevant to weight-space
reconstruction error in this model.

## 3. The method's limit — and why §2 must be read carefully

Ternary error is ≈ **0.51–0.54 for every scheme**, including group-32. Half the weight energy is
discarded in all cases.

A 51% weight reconstruction error cannot be a linear predictor of capability, because BitNet-class
models work in exactly that regime — they are *trained* into it. Post-hoc ternary always moves the
weights enormously; what determines whether the model survives is whether the movement aligns with
directions the model is insensitive to, not how large the movement is.

**Therefore: this metric is a valid comparator for uniform bit budgets and an INVALID predictor
of ternary functional damage.**

Stated plainly: §2 refutes H-002 *as measured on weight-space error*. It does not prove group
scales are worthless to the runtime, because weight-space error is the wrong instrument for that
question. The question remains open, but it cannot be settled here.

This is recorded rather than papered over — a metric that silently fails on one of the two things
it was pointed at is a trap for the next reader.

## 4. Uniform precision behaves classically

Unlike ternary, uniform quantization is exactly what this metric was built to compare:

| Scheme | Mean rel err | vs ternary |
|---|---|---|
| ternary g128 | 0.5138 | — |
| 3-bit g128 | 0.2781 | 1.8× better |
| 4-bit g128 | 0.1193 | 4.3× better |
| 6-bit g128 | 0.0269 | 19.1× better |
| 8-bit g128 | 0.0066 | 78.1× better |

Error roughly halves per added bit. Clean, monotone, and usable for allocation.

## 5. Sensitivity is FLAT across projections and SPIKED at the edges

Per-projection mean ternary error:

| Projection | Params | ternary g128 | 4-bit | kurtosis |
|---|---|---|---|---|
| `mlp.down_proj` | 805,306,368 | 0.5161 | 0.1201 | 3.93 |
| `self_attn.qkv_proj` | 503,316,480 | 0.5155 | 0.1209 | 4.83 |
| `mlp.gate_up_proj` | 1,610,612,736 | 0.5122 | 0.1183 | 3.48 |
| `self_attn.o_proj` | 301,989,888 | 0.5117 | 0.1178 | 4.81 |

Spread across projections is **under 1%**. There is no projection that clearly deserves a
different bit budget than its neighbours.

The signal is in the **layers**, and it is at the boundaries:

| Layer | Mean ternary err | Kurtosis | Outlier frac |
|---|---|---|---|
| **0** | **0.5228** | **9.08** | 3.81e−04 |
| **31** | **0.5188** | **11.74** | 2.86e−04 |
| 30 | 0.5169 | 4.49 | 7.93e−05 |
| 29 | 0.5146 | 3.70 | 3.73e−05 |
| 1 | 0.5143 | 4.82 | 9.91e−05 |
| 2–28 (typical) | ≈0.5123–0.5140 | 3.25–6.50 | 1.3e−05 – 7.1e−05 |

```text
edge layers (0, 1, 30, 31)  mean ternary err 0.5182
middle layers (2–29)        mean ternary err 0.5132
```

**Kurtosis predicts sensitivity.** Layer-level correlation `r = 0.816`; tensor-level `r = 0.623`.
Outlier fraction tracks it. Both are cheap, and both are usable as allocation signals.

**Interpretation:** the first and last layers carry the largest weight outliers and resist
quantization most. This matches the Line A finding that layer-0 damage was catastrophic.

## 6. Candidate allocation (to be validated, not yet accepted)

Because projections are uniformly sensitive and layers are not, the natural design is
**layer-graded, projection-blind**:

| Tier | Layers | Params | Bits | Size |
|---|---|---|---|---|
| edge | 0, 1, 30, 31 | 403M | 6-bit | 0.30 GB |
| near-edge | 2, 3, 28, 29 | 403M | 5-bit | 0.25 GB |
| middle | 4–27 | 2,416M | 4-bit | 1.21 GB |
| embeddings + norms | — | 197M | FP16 | 0.05 GB |

```text
F16 reference   7.68 GB
candidate       1.81 GB
reduction       76.4%
```

**This is a candidate, not a result.** It is derived from a proxy, and §3 explains why that
proxy is weak exactly where it matters most.

## 7. Bug found and fixed during this phase

The first run reported 2-bit scoring *worse* than ternary — impossible, since 2-bit has more
levels. Cause: the symmetric quantizer allowed reconstructed values to overshoot the group
absmax (`step = 2·scale/(nlev−1)` with a clipping range that reached beyond ±scale), so it added
error instead of removing it. Fixed to `step = scale / (2^(b−1) − 1)`.

The corrected version has a built-in identity — at 2 bits it must reproduce absmax ternary
exactly — which is now the regression check.

The run was killed before writing output, so no invalid artifact was produced.

## 8. What this phase did NOT establish

- Nothing about functional quality. No perplexity, no generation, no agreement rate.
- No ternary verdict. §3 explains why this instrument cannot deliver one.
- No confirmation that the §6 allocation preserves quality. It is a prior, not a measurement.

## 9. Next step

The proxy has done its job: it says **weight outliers live at the layer boundaries, and the
projections are interchangeable.** The next step is the ground-truth instrument — empirical
per-tier ablation against the frozen baseline (FP16 5.0553 / Q4 5.2206), quantizing one tier
at a time and measuring PPL delta.

That converts the §6 candidate from a prior into a measurement. It is local, free, and bounded.
