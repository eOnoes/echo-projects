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

Phase 4 — Empirical per-tier ablation.

## Current status

RUNNING

## Current next action

Quantize one tier at a time and measure PPL against the frozen baseline, converting the
Phase 3 candidate allocation from a prior into a measurement. Local GPU only.

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
