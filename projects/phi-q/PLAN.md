# Phi-4-mini Quant Execution Plan

## Phase 0 — Freeze and preserve

**Objective:** Hash and preserve every relevant artifact and prior result.

**Exit gate:**
- Model revision pinned and recorded.
- All existing quantization artifacts hashed.
- Prior baseline numbers recorded verbatim from existing receipts.
- No artifact modified.

## Phase 1 — Establish the measurement rig

**Objective:** One deterministic evaluation harness that reproduces the recorded baselines.

**Exit gate:**
- FP16 perplexity reproduces the recorded value within stated tolerance.
- Q4 perplexity reproduces the recorded value within stated tolerance.
- Corpus, chunking, and seed are fixed and documented.
- Harness produces identical output on repeated runs.

## Phase 2 — Reproduce and classify the ternary failure

**Objective:** Confirm the collapse is reproducible and determine its cause.

**Exit gate:**
- Ternary quantization re-run in a new namespace.
- Collapse reproduced or divergence documented.
- Failure classified as storage-format misuse, precision limit, or unresolved.
- Attention-only and MLP-only cases measured again.

## Phase 3 — Learned quantization at 2–3 bit

**Objective:** Replace naive rounding with a learned or gradient-guided scalar quantizer.

**Exit gate:**
- 3-bit uniform learned quantization measured.
- 2-bit uniform learned quantization measured.
- Results compared against naive rounding at equal bits.
- Quality-versus-bits curve recorded.

## Phase 4 — Mixed-precision allocation

**Objective:** Allocate bits by sensitivity under a fixed average budget.

**Exit gate:**
- Per-tensor sensitivity measured with a documented method.
- Allocation rule defined before the sweep.
- Mixed precision beats uniform quantization at equal average bits, or the negative result is documented.
- Allocation fraction swept one variable at a time.

## Phase 5 — Fine-tune then quantize

**Objective:** Test whether adapting the weights first improves the quantized result.

**Exit gate:**
- Bounded LoRA fine-tune completed with recorded hyperparameters.
- Same Phase 3 and Phase 4 quantization re-run on the adapted weights.
- Improvement measured against quantize-alone, or negative result documented.

## Phase 5b — Sensitivity re-map on adapted weights

**Objective:** Determine whether fine-tuning moves the sensitive regions, and whether it
hardens or softens the model against quantization.

**Rationale:** Phase 4's map is measured on base weights. Sensitivity depends on the actual
weight distribution and activation outliers, so the map may not transfer to the adapted model.
Fine-tuning also tends to sharpen weights and enlarge outliers, which opposes quantization.
The net direction is not assumed — it is measured.

**Exit gate:**
- Phase 4 sensitivity map re-run on the fine-tuned weights using the same method.
- Base map and adapted map compared; agreement or divergence recorded.
- Bounded hypothesis test: quantize base weights and fine-tuned weights at the same fixed
  bit-width, measure each perplexity delta, and record which delta is smaller.
- Result labeled PROVEN, INFERRED, or UNRESOLVED. No direction is assumed in advance.

## Phase 6 — QAT escalation (conditional)

**Objective:** Only if Phases 3–5 leave quality unacceptable, test quantization-aware training.

**Exit gate:**
- Explicit Eddie approval obtained before any ternary training.
- Bounded 4-bit QAT attempted first as the cheapest step.
- Ternary-capable training treated as a separate, independently approved project.

## Phase 7 — Runtime validation

**Objective:** Prove the artifact actually loads and generates.

**Exit gate:**
- GGUF export succeeds.
- Artifact loads in a llama.cpp-family runtime.
- Bounded generation produces coherent output.
- Repetition and EOS behavior recorded.
- Perplexity is not used as the sole quality claim.

## Phase 8 — Closure

**Exit gate:**
- Acceptance criteria met or limitations documented.
- Evidence register complete.
- Closure report written.
- Release decision explicitly stated.

## Failure route

```text
any phase → PRESERVE_FAILURE → classify → retry only after a proven input changes
```

No phase may be skipped because a later result looks promising.
