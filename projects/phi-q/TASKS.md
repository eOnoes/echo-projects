# Phi-4-mini Quant Task Ledger

## Current task

Await Eddie's review and approval of this packet.

**Status:** BLOCKED_PENDING_APPROVAL
**Owner:** Eddie
**Evidence required:** Written approval

## Queue

### Phase 0
- [ ] Pin and record the model revision
- [ ] Hash all existing quantization artifacts
- [ ] Transcribe prior baseline numbers into `EVIDENCE.md`

### Phase 1
- [ ] Build deterministic evaluation harness
- [ ] Reproduce FP16 baseline
- [ ] Reproduce Q4 baseline

### Phase 2
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
