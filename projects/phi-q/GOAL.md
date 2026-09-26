# Goal: phi-q

**Project ID:** `phi-q`
**Full name:** Phi-4-mini Mixed-Precision Quantization Recovery
**Status:** RUNNING
**Owner:** Eddie
**Manager:** Echo
**Executor:** Echo (local RTX 4070)
**Created:** 2026-09-25
**Last verified:** 2026-09-25

## Project keyword

```text
Goal: phi-q
```

### Accepted aliases

Speech-to-text may render the keyword differently. All of the following resolve to this project:

```text
phi-q        phi q        phiQ        Phi-Q
phi cue      fi q         f q         phi-q project
phi4 mini quant            phi4-mini-quant
```

The manager resolves any of these to project ID `phi-q` and reads this file first.

## One-sentence goal

Produce the smallest Phi-4-mini-instruct artifact that retains usable knowledge, by replacing naive ternary post-training quantization with salience-driven mixed-precision quantization, and by establishing the correct fine-tune/quantize ordering.

## Why this exists

A prior effort quantized Phi-4-mini-instruct to ternary (`TQ1_0`, `TQ2_0`) with no training. Perplexity collapsed from a 5.09 FP16 baseline to 44,176 when all projections were ternarized. That work produced useful failure evidence but no usable model.

The research audit confirms the cause: post-training scalar quantization plateaus at roughly 3–4 bits per parameter and degrades sharply at or below 2 bits, and strict ternary requires training. The prior attempt applied a ternary *storage format* to a model that had never been trained to ternary.

## Definition of done

This project is complete when:

- The FP16 and Q4 baselines are reproduced within a stated tolerance under one fixed evaluation harness.
- The prior ternary collapse is reproduced and classified with receipts.
- Uniform quantization at 3-bit and 2-bit is measured against the frozen baseline.
- A per-tensor mixed-precision allocation is implemented and shown to beat uniform quantization at equal average bits per weight.
- The effect of fine-tune-then-quantize ordering is measured against quantize-alone.
- At least one GGUF artifact is exported and loaded in a llama.cpp-family runtime.
- Every claim is labeled PROVEN, INFERRED, UNRESOLVED, or DISPROVEN.
- A closure report exists with explicit limitations.

## Explicit non-goals

- Do not attempt strict ternary conversion of the dense model in this project.
- Do not retrain from scratch.
- Do not delete, mutate, or overwrite any prior artifact, receipt, or model file.
- Do not purchase cloud compute without separate approval.
- Do not publish models or artifacts externally.

## Current phase

Phase 10 — COMPLETE. The question is answered, and the answer is NEGATIVE.

## Current status

COMPLETE_WITH_NEGATIVE_RESULT

## Headline

```text
                        PPL       harness
FP16 baseline          6.9705     fixed (this project)
GSQ 2-bit             14.6434     fixed (this project)
                     --------
                     +110.1%
```

GSQ, as configured and run here, does **not** beat llama.cpp's K-quants on Phi-4-mini. The
export is faithful (dequantized weights correlate 0.88-0.90 with the original), so the number
is real rather than an artifact.

## Current next action

Do NOT proceed to RCO allocation (Phase 11). Tuning allocation on top of a configuration that
is not competitive would be measuring noise. Instead, in order of value:

1. **Report the `trainer.py` shard-grouping bug upstream** — it is LLaMA-specific and fails
   silently for any MLP not named gate_proj/up_proj/down_proj. A genuine contribution
   independent of this result.
2. **Re-run the experiment on a LLaMA-family model.** GSQ ships an exercised LLaMA wrapper, so
   the toolchain risk is zero. If GSQ beats Q4_K_M at matched size there, the method is
   vindicated and Phi-4-mini was simply the wrong test bed. **Highest value, nearly free.**
3. **Run the full calibration recipe** (4096x4096, ~35 h local or a bounded rental) before any
   final claim about the method. Current run used 1/16th.
4. **Fix the export**: quantize attention, and emit true 2-bit containers (currently 4-bit,
   wasting 2x space).

## Bounding caveats

- Calibration was **1/16th** of GSQ's shipped recipe (1024x1024 vs 4096x4096).
- **Phi-4-mini is 3.8B and dense**; GSQ's published results are 8B-1T. Small dense models have
  far less redundancy. The negative result may reflect the regime, not the method.
- The K-quant comparison is cross-ruler (see W-002); ratios against own-baseline are
  comparable in character, absolute perplexities are not.

Receipt: `receipts/P10-gsq-experiment-20260926.md`

## Phase 9 result — wrapper PASSED

```text
Phi-4-mini, 2 of 32 layers
  Layer 1/32  GPTQ 18.3s  ->  Gumbel 2.82e-03 -> 1.42e-03 -> 1.18e-03
  Layer 2/32              ->  Gumbel 7.92e-03 -> 4.62e-03 -> 3.59e-03
  21.6 s/layer   |  all 8 shards written  |  errors: NONE
  full 32-layer run projected at ~11 minutes, local and free

Regression: Qwen3-0.6B re-run at 7.8 s/layer — identical to pre-patch. No regression.
```

The port required a **real fork of `trainer.py`**, not just a new wrapper. Its shard-write trigger
was keyed on the literal substring `"gate_proj"`, which is not a substring of Phi's fused
`gate_up_proj` — so no MLP shard was written and the run died on `FileNotFoundError`. The fix
groups by **parent module**, which is behaviour-identical for LLaMA and for MoE (per-expert shards
preserved) and correct for Phi. I had claimed the port would be purely additive; **that was wrong**,
and it is recorded as such in the receipt.

Receipt: `receipts/P9-phi-wrapper-20260925.md`

## Phase 8 result — toolchain PASSED

```text
Layer 1/28  GPTQ 6.5s -> Gumbel loss 6.48e-03 -> 4.18e-03 (decreasing)
Layer 2/28  GPTQ 6.6s -> Gumbel -> val_hard_loss 6.39e-03
            shards written for both layers; --max-layers 2 respected
Total time: 16.83s   avg 7.8 s/layer on Qwen3-0.6B
GPU after exit: 1183 MiB (baseline) — no leaked context
```

Projected Phi-4-mini full run: **~26 minutes**, local and free. INFERRED, not measured.

Two upstream GSQ bugs found (shipped smoke config raises; ppl eval cannot be disabled by period),
and one real Phase 10 blocker: the wikitext/hub version conflict blocks the in-loop perplexity
eval. Preferred resolution is to measure with the project's own Phase 1 llama.cpp rig after GGUF
export, since that rig is the reference harness and baseline-comparable.

Receipt: `receipts/P8-toolchain-smoke-20260925.md`

## Method revision (Phase 6, complete)

## Phase 7 result — environment PASSED

```text
torch               2.11.0+cu126  |  cuda True
device              RTX 4070, sm_89 (Ada), 12282 MiB
transformers        5.17.0        |  Phi3ForCausalLM available: True
compressed-tensors  0.19.0        |  accelerate 1.15.0  |  lion-pytorch ok

GSQ source imports: BaseModelWrapper OK, LLaMAWrapper OK, gumbel_quantizer OK
```

The critical unknown is closed: a Windows CUDA build of the exactly-pinned `torch==2.11.0`
exists. **No escalation needed** — GSQ is locally runnable and free.

Receipt: `receipts/P7-environment-20260925.md`

## Method revision (Phase 6, complete)

GSQ + RCO replaces both naive allocation and QAD. See `receipts/P6-gsq-rco-investigation-20260925.md`.
Phase 4b's negative result was instrument-limited, not idea-limited. The Phi wrapper delta is small
(8 of 10 module paths match the existing LLaMA wrapper) — see
`receipts/P6b-wrapper-feasibility-20260925.md`.

## The blocker, stated plainly

Phi-4-mini is **not a supported architecture** in GSQ. Its wrappers cover LLaMA, Qwen3,
Qwen3-MoE, Qwen3.5/3.6 (+MoE), Gemma-4-35B, and Kimi K2/K2.5. Phi-4/Phi-3 is absent.

So phi-q cannot be revived as specified without either writing a Phi architecture wrapper
(real engineering, plausible because Phi-4-mini is a dense standard-attention gated-MLP model
close to the LLaMA wrapper's target — but **unverified**) or pivoting to a supported model.

## Current next action

Bounded feasibility spike, in this order:

1. Clone GSQ; read the wrapper interface; determine exactly what a Phi wrapper must implement.
2. Run GSQ's documented smoke test (`--max-layers N`) to prove the toolchain runs on this
   Ada card (RTX 4070, sm_89) at all.
3. Only then decide: write the Phi wrapper, or pivot to a supported model.

If step 2 fails, GSQ cannot run locally and the path needs a rented GPU — a costed decision
for Eddie, never a silent substitution.

## Why the method changed (Phase 6)

Phase 4b's negative result was **instrument-limited, not idea-limited**.

```text
             our Phase 3-4b                    GSQ + RCO
objective    weight-space reconstruction error  task loss directly
quantizer    naive round-to-nearest            learned grid (Gumbel-Softmax)
allocation   manual, whole layers              gradient search, per tensor
constraint   none                              exact size budget, by construction
```

We optimised the wrong objective with a fixed grid, and forced the allocation across the 3-bit
cliff. ISTA optimises the task loss with a learned quantizer under an exact budget and reaches
**task-lossless at 3.50 bpw** on a 27B.

GSQ supersedes the QAD plan: QAD trains a model to *tolerate* damage already done; GSQ avoids
the damage. It also removes the corpus question and, via per-layer offloading, may be lighter on
VRAM than the QAD pipeline that did not fit.

## Phase 6 findings

| ID | Claim | State |
|---|---|---|
| P6-001 | GSQ learns per-coordinate grid + per-group scales via Gumbel-Softmax; GPTQ init then refinement | PROVEN |
| P6-002 | GSQ is layer-by-layer with meta-device offload — works beyond VRAM | PROVEN |
| P6-003 | GSQ can refine existing GGUF K-Quants in-format (Qwen3-8B Q2_K 50.03 → 56.28) | PROVEN |
| P6-004 | RCO assigns per-tensor types against true task loss under an exact budget | PROVEN |
| P6-005 | GSQ trains on Ada-class GPUs (sm_89) per its own README | PROVEN |
| P6-006 | **Phi-4 is not a supported architecture** | PROVEN |
| P6-007 | Our Phase 4b failure is explained by objective + granularity, not by the concept | INFERRED |
| P6-008 | 5060 Ti measured ~40 tok/s on the 11.8 GB IQ3_S build (MTP active) | PROVEN (third-party) |
| P6-009 | A Phi wrapper can be written to fit GSQ | **UNVERIFIED** |
| P6-010 | GSQ on Phi-4-mini reaches near-lossless at 3 bpw | **UNKNOWN — the experiment** |

## Phase 4b outcome — heterogeneous PARKED

```text
control   Q4_K_S   2.3456 GB   5.3568
treatment hetero   2.3425 GB   5.3961   (+0.73% — LOSES)
Q4_K_M (llama.cpp) 2.4940 GB   5.2206   (beats both)
```

Built the allocation from the sensitivity map, measured it, and it lost at matched size.
llama.cpp's own mix beats it for free. Parked with a measured reason (D-020). The remaining
quality gap is a training problem, not an allocation problem (D-021).

## Uniform curve (Phase 4, complete)

```text
Q2_K     1.734 GB   7.1262   +40.96%
Q3_K_M   2.122 GB   5.5222    +9.24%
Q4_K_M   2.494 GB   5.2206    +3.27%   <- sweet spot, 67.5% smaller than FP16
Q5_K_M   2.815 GB   5.1591    +2.05%
Q6_K     3.156 GB   5.1552    +1.98%   (dominated by Q5_K_M)
Q8_0     4.085 GB   5.0458    -0.19%   (effectively lossless)
FP16     7.68  GB   5.0553        --
```

## Sensitivity map result (Phase 3)

```text
projection sensitivity   flat (under 1% spread across all four)
layer sensitivity        spiked at boundaries: L0 0.5228, L31 0.5188 vs ~0.5130 mid
kurtosis predicts it     r = 0.816 layer-level, 0.623 tensor-level
candidate allocation     layer-graded 6/5/4-bit -> ~1.81 GB, 76.4% reduction
```

## Baseline of record

```text
FP16     5.0553      (binary f88a3a51..., corpus 03ba3dd2...)
Q4_K_M   5.2206      (+3.27%)
```

## Linked documents

- Charter: `CHARTER.md`
- Authority: `AUTHORITY.md`
- Plan: `PLAN.md`
- Tasks: `TASKS.md`
- Evidence: `EVIDENCE.md`
- Handoff: `HANDOFF.md`
- Closure: `CLOSURE.md`
- Overrides: `PROJECT-OVERRIDES.md`

## Decision log

- 2026-09-25: Project created. Scope limited to mixed-precision quantization recovery, not strict ternary.
- 2026-09-25: Research audit confirms PTQ plateau at 3–4 bpw and sharp loss at ≤2 bpw.
- 2026-09-25: `TQ1_0`/`TQ2_0` identified as ternary storage formats for already-ternary models, not general compression methods.
- 2026-09-25: Fine-tune-then-quantize confirmed as the correct order for moderate bit-widths; ternary requires coupled training.
- 2026-09-25: Phase 5 corrected from 16-bit LoRA to QLoRA — local VRAM cannot fit 16-bit training of a 3.8B model.
- 2026-09-25: Compute escalation protocol added; paid resources require a formatted proposal.
- 2026-09-25: **Phase 0 PASSED.** All artifacts local and hash-verified. Baselines reproduce exactly.
- 2026-09-25: **Harness correction discovered.** Prior evidence mixes two incompatible harnesses
  (llama.cpp FP16 = 5.0863, PyTorch FP16 = 12.0361). The old "5.0863 → 44,176" framing is
  cross-harness and retracted. Phase 1 must pin one reference harness.

## Commands

- Resume: `Goal: phi-q`
- Status: `Goal: phi-q — status`
- Pause: `Goal: phi-q — pause`
- Close: `Doctrine: close phi-q`
