# Phi-4-mini Quant Execution Plan

## Hardware and environment constraints

| Resource | Value | Implication |
|---|---|---|
| Local GPU | RTX 4070, 12,282 MiB total, ~10.8 GB free | Fits 4-bit inference and QLoRA. Does not reliably fit 16-bit LoRA on a 3.8B model. |
| Compute capability | 8.9 (Ada) | Supports bf16, flash-attention, and current quant kernels |
| Free disk (E:) | ~2.5 TB | Sufficient for artifacts, merged models, and exports |
| Existing artifacts | `phi4-mini-tq1_0.gguf` (1.92 GB), `phi4-mini-tq2_0.gguf` (2.07 GB) | Frozen. Read-only. Never overwritten. |

Any phase that requires more headroom than this must be escalated to Eddie for approval
before a paid resource is created.

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
- FP16 perplexity reproduces the recorded **5.0863** within **±0.5%** (5.061–5.112).
- Q4_K_M perplexity reproduces the recorded **5.2827** within **±0.5%** (5.256–5.309).
- A delta between 0.5% and 2% must be investigated and explained before proceeding.
- A delta above 2% stops the project and triggers harness forensics.
- Corpus, chunk width, context length, and seed are fixed and documented.
- Harness produces byte-identical output on repeated runs.
- Evaluation artifact hash and runtime build hash are recorded.
- GPU identity is confirmed free of conflicting processes before measurement.

## Phase 2 — Reproduce and classify the ternary failure

**Objective:** Confirm the collapse is reproducible and determine its cause.

**Exit gate:**
- Ternary quantization re-run in a new namespace.
- Collapse reproduced or divergence documented.
- Failure classified as storage-format misuse, precision limit, or unresolved.
- Attention-only and MLP-only cases measured again.

## Phase 3 — Learned quantization at 2–3 bit

**Objective:** Replace naive rounding with a learned or gradient-guided scalar quantizer.

**Candidate quantizers, cheapest first:**

| Method | Best at | Notes |
|---|---|---|
| Round-to-nearest | — | Control only. Not expected to be competitive. |
| GPTQ | 3–4 bit | Widely available, mature tooling |
| AWQ | 4 bit | Activation-aware, weaker below 4 bit |
| GSQ-class (Gumbel-Softmax grid learning) | 2–3 bit | Designed for the low-bit regime we are targeting |

**Exit gate:**
- Round-to-nearest control measured at identical bit-widths.
- At least two learned quantizers measured at 3-bit.
- Best-performing method carried forward to 2-bit.
- Results compared against naive rounding at equal bits.
- Quality-versus-bits curve recorded with the frozen baseline as reference.

## Phase 4 — Mixed-precision allocation

**Objective:** Allocate bits by sensitivity under a fixed average budget.

**Exit gate:**
- Per-tensor sensitivity measured with a documented method.
- Allocation rule defined before the sweep.
- Mixed precision beats uniform quantization at equal average bits, or the negative result is documented.
- Allocation fraction swept one variable at a time.

## Phase 5 — Fine-tune then quantize

**Objective:** Test whether adapting the weights first improves the quantized result.

**Hardware constraint:** A 3.8B model at FP16 is ~7.6 GB of weights. The local GPU has ~10.8 GB
free, so 16-bit LoRA fine-tuning does not fit reliably. Use **QLoRA** — 4-bit base, LoRA
adapters trained, then merged back to FP16. This is the standard pipeline for this hardware
class, and merging restores FP16 weights for the quantization phases that follow.

**Exit gate:**
- Training method recorded as QLoRA, with the base quantization scheme stated.
- Bounded LoRA fine-tune completed with recorded hyperparameters and time budget.
- Adapter merged; merged-model hash recorded and distinct from the base checkpoint.
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
