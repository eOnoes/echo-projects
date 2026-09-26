# Phi-4-mini Quant Handoff

## Resume command

```text
Goal: phi-q
```

## Current state

Project packet created. No compute started. Awaiting Eddie's approval of scope, authority, and plan.

## Last completed action

Research audit confirming the compression approach and correcting the quantization order. Packet written 2026-09-25.

## Frozen baseline

```text
FP16 geometric PPL:   5.0863
Q4_K_M geometric PPL: 5.2827  (+3.86%)
Ternary all-proj PPL: 44,176  (collapse)
```

## Current blocker

Approval. No evaluation, quantization, training, or export is authorized yet.

## Next safe action

On approval, begin Phase 0: pin the model revision, hash all existing artifacts, and transcribe prior baselines. No GPU work in Phase 0.

## Do not do yet

- Do not re-quantize anything.
- Do not start training.
- Do not overwrite or delete prior artifacts.
- Do not create paid cloud resources.
- Do not attempt strict ternary training.

## Resume success condition

The next session must update `EVIDENCE.md` and `TASKS.md` with Phase 0 results before moving to Phase 1.
