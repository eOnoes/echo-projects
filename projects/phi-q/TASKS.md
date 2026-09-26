# Phi-4-mini Quant Task Ledger

## Current task

Phase 2 — review the uncatalogued parity-harness evidence, then reproduce and classify the
ternary failure in a new namespace.

**Status:** READY
**Owner:** Echo
**Approval required:** No
**Evidence required:** Parity-harness review note + reproduced ternary numbers + classification

## Queue

### Phase 0 — COMPLETE 2026-09-25
- [x] Pin and record the model revision
- [x] Hash all existing quantization artifacts
- [x] Transcribe prior baseline numbers into `EVIDENCE.md`
- [x] Receipt: `receipts/P0-freeze-20260925.md`

### Phase 1 — COMPLETE 2026-09-25
- [x] Corpus identified and hash-recorded (README, 7,994 tokens)
- [x] Harness algorithm read from source
- [x] Binary built from local source, hashed, versioned
- [x] FP16 baseline measured: 5.0553
- [x] Q4_K_M baseline measured: 5.2206
- [x] Deviation from historical explained (−0.61% / −1.18%)
- [x] Receipt: `receipts/P1-measurement-rig-20260925.md`

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
