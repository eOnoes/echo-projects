# Phase 6 Receipt — GSQ / RCO Tooling Investigation

**Project:** phi-q
**Phase:** 6 — method revision (pre-build investigation)
**Date:** 2026-09-25
**Purpose:** Determine whether ISTA-DASLab's GSQ + RCO can be applied to Phi-4-mini,
and whether the method supersedes our QAD approach.

---

## 1. Why this investigation happened

Eddie clarified the original intent behind phi-q:

> "This is what I was wanting to do to the phi-4."

i.e. produce a **task-lossless low-bit** Phi-4-mini of the kind ISTA-DASLab published for
Qwen3.8-27B. That reframes our Phase 4b negative result: we attempted the right idea with
cruder instruments.

## 2. What GSQ is

**GSQ — Gumbel-Softmax Quantization** (arXiv:2604.18556; ISTA / ETH Zürich / TU Wien / Red Hat AI)

Post-training scalar quantization that **learns** the quantization grid rather than using a fixed
one. Two stages:

```text
1. GPTQ initialization      second-order Hessian info from calibration activations
                            -> initial quantized weights + per-group scales
2. Gumbel-Softmax refine    per-coordinate logits over grid values, trained with a
                            differentiable Gumbel-Softmax relaxation; temperature
                            annealed 2.0 -> 0.05, logit scale 100 -> 500
```

**The critical architectural detail for us:** it works **layer by layer**, saving each completed
layer as compressed shards and **offloading it to a meta device**. Models far larger than VRAM can
be quantized. Peak memory is closer to *one layer's* quantizer state than the whole model's.

Supported codebooks: 1-bit, 2-bit, 3-bit, 4-bit, and `"ternary"` (~1.58-bit).

**Claim that matters:** GSQ "closes most of the accuracy gap between simple scalar PTQ (GPTQ, QuIP,
EfficientQAT) and second-wave vector/trellis methods (QTIP, AQLM, PV-Tuning) at 2–3 bits per
parameter."

That gap is exactly what we hit in Phase 4b.

**It can also refine existing GGUF K-Quants in-format.** Published result: refining a Qwen3-8B
Unsloth GGUF moved the aggressive Q2_K setting from avg score 50.03 → **56.28**, and the result
"runs unchanged on llama.cpp / Ollama."

## 3. What RCO is

**RCO — Riemannian Constrained Optimization** (arXiv:2605.00649; Helcig and Alistarh)

Solves the *allocation* problem: pick one of K quantization types for each of N tensors to minimize
the **true task loss** subject to an **exact** size budget.

This is the piece that replaces our sensitivity-map allocation. Key differences from what we did:

| | our Phase 3–4b | RCO |
|---|---|---|
| objective | weight-space reconstruction error | **task loss directly** |
| constraint | none (we tuned by hand) | **exact budget, held by construction** |
| search | manual layer grading | gradient-based on a Riemannian manifold |
| granularity | whole layers | per tensor |

RCO's budget is enforced by tangent projection (every Adam step is budget-preserving) plus a
retraction that binary-searches a scalar to machine precision. The discrete forward pass uses
Gumbel-STE with an exact multiple-choice knapsack solver.

**This directly explains our Phase 4b failure.** We optimized the wrong objective (reconstruction
error, which we ourselves flagged as "the wrong instrument"), and we allocated by hand at
whole-layer granularity, which forced us to cross the 3-bit cliff.

## 4. Feasibility findings

### 4a. GPU requirement — likely OK

> "GSQ has only been tested on NVIDIA Hopper (H100, sm_90). Training itself should work on any
> reasonably modern CUDA GPU (we've also run training on L40S/Ada)."

Our RTX 4070 is **Ada (sm_89)**. Training is plausible. The vLLM/lm-eval serving step wants
sm ≥ 80, which Ada satisfies, though there is a known Ada limitation for *fused MoE* Marlin
kernels — not relevant to a dense model.

The README's listed reference configs (1× H100 for Llama-3.1-8B) are convenience, not a hard
floor, given per-layer offloading.

### 4b. THE BLOCKER — Phi-4 is not a supported architecture

GSQ ships architecture wrappers for:

```text
LLaMA (dense)          Qwen3 (dense)        Qwen3-MoE
Qwen3.5/3.6 (dense)    Qwen3.5/3.6-MoE      Gemma-4-35B
Kimi K2 / K2.5 (MoE)
```

**Phi-4 / Phi-3 is not in the list.** phi-q's target model is unsupported out of the box.

This is the single most important finding of this phase, and it is the thing that decides whether
phi-q can be revived as specified or must pivot.

**Assessment (INFERRED, not verified):** Phi-4-mini is dense with standard attention and a gated
MLP — structurally close to the LLaMA wrapper's target. A Phi wrapper is *plausible*, but it is
real architecture-level engineering, not configuration. It must be attempted and measured, not
assumed.

### 4c. A measured data point relevant to Eddie's hardware question

A third-party measurement table for the Qwen3.8-27B GSQ-RCO quants reports:

```text
IQ3_S   11.8 GB   ~40 tok/s on an RTX 5060 Ti
```

That is on the **5060 Ti** — the card Eddie was considering. It is a real measurement, not an
estimate, and it is consistent with MTP-accelerated decoding (plain 5060 Ti bandwidth maths gives
~38 GB/s theoretical, so 40 tok/s implies speculative decoding is active).

Anchor for our Phase 5 TPS estimates: the 5070 Ti has ~2× the bandwidth (896 vs 448 GB/s), so
proportionally higher.

## 5. What this does to the QAD plan

**GSQ supersedes QAD for this goal.**

```text
QAD (our Phase 5 plan)   train the model to TOLERATE quantization
                         needs: corpus, LoRA, training loop, hours of GPU
                         result: recovers some damage that was already done

GSQ (this phase)         quantize BETTER in the first place
                         needs: calibration data, per-layer optimization
                         result: less damage to begin with
```

Both are legitimate, but for "task-lossless low-bit Phi-4-mini" GSQ attacks the cause. It also
removes the corpus question entirely and, because it is layer-by-layer, may be *less* demanding on
VRAM than the QAD pipeline that failed to fit.

The Phase 5 environment work is not wasted — the isolated `qad-env` venv, the CUDA torch fix, and
the verified toolchain are all reusable infrastructure.

## 6. Honest status of the claims

| Claim | State |
|---|---|
| GSQ/RCO are the right method class for this goal | PROVEN (published results on Qwen3.8-27B) |
| GSQ works on Ada-class GPUs for training | PROVEN (README states it) |
| Phi-4 is unsupported by GSQ as shipped | PROVEN (absent from wrapper list) |
| A Phi wrapper can be written to fit GSQ | **UNVERIFIED — must be attempted** |
| Our local 12 GB card can run GSQ on a 3.8B model | **UNVERIFIED — per-layer offload suggests yes, not measured** |
| GSQ on Phi-4-mini would reach near-lossless at 3 bpw | **UNKNOWN — the actual experiment** |

## 7. Recommended next step

**Bounded feasibility spike, before any plan commitment:**

1. Clone GSQ and read the wrapper interface — determine what a Phi wrapper must implement.
2. Run GSQ's documented smoke test (`--max-layers N` on a small model) to confirm the toolchain
   runs on our Ada card at all.
3. Only then decide: write the Phi wrapper, or pivot to a supported model.

If step 2 fails, GSQ is not locally runnable and the whole path needs a rented GPU — which is a
costed decision for Eddie, not a silent substitution.

## 8. References

```text
https://github.com/IST-DASLab/GSQ      (27 stars, Apache-2.0)
https://github.com/IST-DASLab/RCO      (Apache-2.0)
https://arxiv.org/abs/2604.18556       GSQ paper
https://arxiv.org/abs/2605.00649       RCO paper
https://huggingface.co/ISTA-DASLab/Qwen3.8-27B-GSQ-RCO-GGUF
```
