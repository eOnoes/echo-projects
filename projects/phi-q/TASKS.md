# Phi-4-mini Quant Task Ledger

## Current task

Build the Phase 1 evaluation harness and reproduce the three frozen baselines.

**Status:** READY
**Owner:** Echo
**Approval required:** No
**Evidence required:** Harness specification + reproduced baselines + harness choice recorded

## Queue

### Phase 0 — COMPLETE 2026-09-25
- [x] Pin and record the model revision
- [x] Hash all existing quantization artifacts
- [x] Transcribe prior baseline numbers into `EVIDENCE.md`
- [x] Receipt: `receipts/P0-freeze-20260925.md`

### Phase 1
- [ ] Decide and record the reference harness
- [ ] Build deterministic evaluation harness
- [ ] Reproduce FP16 baseline (llama.cpp 5.0863)
- [ ] Reproduce Q4_K_M baseline (llama.cpp 5.2827)
- [ ] Reproduce PyTorch baseline (12.0361) or explain divergence

### Phase 2
- [ ] Review the uncatalogued parity-harness evidence before re-deriving anything
- [ ] Re-run ternary quantization in a new namespace
- [ ] Measure attention-only and MLP-only cases
- [ ] Classify the failure mode

### Phase 3
- [ ] Apply learned scalar quantization at 3-bit
- [ ] Apply learned scalar quantization at 2-bit
- [ ] Record quality-versus-bits curve

### Phase 4
- [ ] Measure per-tensor sensitivity
- [ ] Define allocation rule
- [ ] Sweep allocation fraction under fixed average budget

### Phase 5
- [ ] Bounded LoRA fine-tune
- [ ] Re-quantize adapted weights
- [ ] Compare against quantize-alone

### Phase 7
- [ ] Export GGUF
- [ ] Load and generate in a local runtime

## Rules

- Only one task may be `IN_PROGRESS`.
- Every completed task requires evidence.
- A blocked task must state exactly what is needed.
- A successful command is not proof of a successful outcome.
