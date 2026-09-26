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

## Compute escalation protocol

Local VRAM budget is approximately **10.6 GB free**. Phases 0, 1, 2, 4, 5, and 7 are expected
to fit. Phase 3 is the identified risk because gradient-based quantization requires a backward
pass at roughly 2–3× forward memory.

Escalation to a rented GPU happens only when one of these is demonstrably true:

- A required run cannot complete locally due to memory, with the failure recorded.
- A projected sweep exceeds the local time budget recorded in `TASKS.md`.
- A phase requires gradient-based quantization beyond the local budget.

**Every escalation proposal must state all of the following:**

| Field | Required content |
|---|---|
| Card and VRAM | Exact GPU type being rented |
| Hourly rate | Current price at proposal time |
| Estimated hours | Bounded, not open-ended |
| Maximum total cost | Hard ceiling |
| Runs unblocked | Exactly which measurements this enables |
| Evidence plan | What is captured and hash-verified before the pod is released |
| Stop trigger | The condition that ends the session |

No paid resource is created before Eddie approves the proposal. The pod is stopped immediately
when the bounded run finishes. Evidence is mirrored and hash-verified locally before release.

## Phase 0 — Freeze and preserve

**Objective:** Hash and preserve every relevant artifact and prior result.

**Exit gate:**
- Model revision pinned and recorded.
- All existing quantization artifacts hashed.
- Prior baseline numbers recorded verbatim from existing receipts.
- No artifact modified.

## Phase 1 — Establish the measurement rig

**Objective:** One deterministic evaluation harness that reproduces the recorded baselines.

**Harness decision required first:** the prior evidence contains two incompatible harnesses.
Phase 1 must pick one as the project reference and state the choice explicitly.

| Option | Baseline | Pros | Cons |
|---|---|---|---|
| llama.cpp `perplexity_v2` | 5.0863 | Matches the frozen GGUF controls; fast; already specified | Only works on GGUF artifacts |
| PyTorch / Transformers bf16 | 12.0361 | Works on uncompressed weights; needed for quantization work | Slower; heavier |

**Recommendation:** use llama.cpp for GGUF controls, PyTorch for weight-level work, and
**never compare across the two**. Record the harness on every number.

**Exit gate:**
- FP16 reproduces the recorded 5.0863 (llama.cpp) within ±0.5%.
- Q4_K_M reproduces the recorded 5.2827 (llama.cpp) within ±0.5%.
- PyTorch harness reproduces 12.0361 within ±0.5%, or the divergence is explained.
- Chunk width, context, batch size, corpus, and seed are recorded.
- Harness produces identical output on repeated runs.
- Every measured number records which harness produced it.

## Phase 2 — Reproduce and classify the ternary failure

> **CORRECTED 2026-09-25.** This phase's premise was wrong. See
> `receipts/P2-evidence-reconciliation-20260925.md`. There are **two** ternary efforts, not one,
> and the second one's failed quality comparison was caused by a broken benchmark that defeated
> the unquantized model as well. The corrected objective and gate are below.

**Original objective (superseded):** Confirm the collapse is reproducible and determine its cause.

**Corrected objective:** Establish a *valid* baseline first, then determine what the two ternary
efforts actually established.

### Corrected steps

1. **Fix the prompt protocol.** Establish a chat-template-correct harness for Phi-4-mini and
   confirm the **unquantized F16 model answers correctly on real prompts**. Until the baseline
   answers, no quantization comparison has meaning.
2. **Re-measure Line B** (strict group-128 PQ2_0, `1,368,849,920` bytes) against that working
   baseline. The old comparison cannot be rescued — it must be re-run.
3. **Reconcile Line A vs Line B.** Line A ternarized projections directly and collapsed to
   44,176 PPL. Line B uses strict group-128 PQ2_0 with per-group scales and produced a runnable
   artifact. The difference — group scales — is the leading candidate explanation and is
   directly testable at matched bit budgets.
4. **Defer the QAT decision.** A pilot spec already exists
   (`<TERNARY_LAB_ROOT>/prism-pq2-recovery/QAT-PILOT-SPEC.md`, never launched). Do not launch it
   until steps 1–3 say whether training is needed at all.

**Corrected exit gate:**
- F16 baseline answers real prompts correctly under a documented chat template.
- Line B re-measured against that baseline.
- Line A vs Line B explained at matched bit budgets.
- H-002 and H-003 resolved to PROVEN or DISPROVEN.

**Original exit gate (superseded):**
- ~~Ternary quantization re-run in a new namespace.~~
- ~~Collapse reproduced or divergence documented.~~
- ~~Failure classified as storage-format misuse, precision limit, or unresolved.~~
- ~~Attention-only and MLP-only cases measured again.~~

## Phase 3 — Learned quantization at 2–3 bit  *(historical — attempted, superseded)*

**Objective (as written):** Replace naive rounding with a learned or gradient-guided scalar quantizer.

**Candidates considered:**

| Method | Best at | Notes |
|---|---|---|
| Round-to-nearest | — | Control only. Not expected to be competitive. |
| GPTQ | 3–4 bit | Widely available, mature tooling |
| AWQ | 4 bit | Activation-aware, weaker below 4 bit |
| GSQ-class (Gumbel-Softmax grid learning) | 2–3 bit | Designed for the low-bit regime we are targeting |

**What actually happened:** Phase 3 built a *sensitivity map* and Phase 4 ran a *uniform bit-budget
sweep* — both with fixed llama.cpp formats. No learned quantizer was implemented. The uniform
curve is real and useful; the "learned quantizer" objective was never met.

**Uniform curve produced (PROVEN, retained):**

```text
Q2_K     1.734 GB   7.1262   +40.96%
Q3_K_M   2.122 GB   5.5222    +9.24%   <- the cliff
Q4_K_M   2.494 GB   5.2206    +3.27%   <- sweet spot
Q5_K_M   2.815 GB   5.1591    +2.05%
Q6_K     3.156 GB   5.1552    +1.98%
Q8_0     4.085 GB   5.0458    -0.19%
FP16     7.680 GB   5.0553        —
```

## Phase 4 — Mixed-precision allocation  *(historical — attempted, PARKED)*

**Objective (as written):** Allocate bits by sensitivity under a fixed average budget.

**What actually happened:** Built a layer-graded allocation from the Phase 3 sensitivity map and
measured it size-matched against a uniform control. **It lost.**

```text
control   Q4_K_S   2.3456 GB   5.3568
treatment hetero   2.3425 GB   5.3961   (+0.73% — LOSES)
Q4_K_M (llama.cpp) 2.4940 GB   5.2206   (beats both)
```

Parked with a measured reason (D-020). **Root cause identified in Phase 6:** the objective was
wrong (weight-space reconstruction error, not task loss), the quantizer was a fixed grid rather
than learned, and the granularity was whole layers, which forced the allocation across the 3-bit
cliff. The concept was not refuted — the instruments were.

## Phase 5 / 5b — Fine-tune then quantize  *(historical — QAD attempted, superseded)*

**Objective (as written):** Test whether adapting the weights first improves the quantized result.

**What actually happened:** Built and verified a QAD environment (isolated venv, CUDA torch,
Unsloth). Stage-1 smoke test **passed** — but also proved a single-pass teacher+student design
does **not** fit local VRAM (needs est. 12,574 MiB; 3,786 MiB free). A two-pass design was
identified that does fit.

**Superseded in Phase 6.** GSQ avoids the damage rather than training a model to tolerate it, and
removes the corpus requirement entirely. The environment work is retained as reusable
infrastructure.

## Phase 6 — QAT escalation  *(historical — superseded)*

Superseded by the GSQ route. Kept for the record: no ternary training was ever launched, and the
pre-existing `QAT-PILOT-SPEC.md` was never started.

---

# Revised route — GSQ + RCO

The forward plan from Phase 6 onward. Phases below are the *current* waterfall.

## Phase 6 — Method revision  ✅ COMPLETE 2026-09-25

**Objective:** Replace naive allocation and QAD with the method designed for this problem.

**Outcome:** **GSQ + RCO adopted** (D-022). GSQ learns per-coordinate grid assignments and
per-group scales via a Gumbel-Softmax relaxation; RCO assigns per-tensor quantization types
against the true task loss under an exact size budget.

**Evidence:** `receipts/P6-gsq-rco-investigation-20260925.md`

## Phase 6b — Wrapper feasibility  ✅ COMPLETE 2026-09-25

**Objective:** Determine what a Phi-4 architecture wrapper must implement for GSQ.

**Outcome:** The delta is **small**. Eight of ten Phi-4-mini module paths are byte-identical to
GSQ's existing LLaMA wrapper. Three real deltas: fused `qkv_proj`, fused `gate_up_proj`, and a
tied `lm_head` absent from the checkpoint. The blocker **moved** from "Phi is unsupported" to
"can the stack be installed on Windows at all."

**Evidence:** `receipts/P6b-wrapper-feasibility-20260925.md`

## Phase 7 — Environment build  ⏳ IN PROGRESS

**Objective:** Prove the GSQ stack installs and runs on this machine.

**Method:** Isolated venv, **dependency subset excluding vLLM / ray / lm-eval / lighteval /
humming-kernels** — those are the serving and evaluation path, which is Linux-oriented and not
required for quantization.

**Exit gate:**
- Venv created; torch reports CUDA available.
- `transformers`, `accelerate`, `datasets`, `safetensors`, `compressed-tensors`, `lion-pytorch`
  import cleanly.
- The failure mode, if any, is recorded verbatim rather than worked around silently.

**If this gate fails:** GSQ cannot run locally. That becomes a costed escalation proposal for
Eddie — Linux host or rented GPU — and the proposal follows the compute-escalation protocol
already defined at the top of this file. **No silent substitution of an approximate method.**

## Phase 8 — Toolchain smoke test

**Objective:** Prove GSQ actually quantizes something on this card, before touching Phi.

**Method:** GSQ's own dry run on a **supported** architecture —
`SMOKE_TEST=1 bash scripts/run.sh` (2-layer) or `--max-layers 2` — against a small model such as
Qwen3-0.6B.

**Exit gate:**
- A 2-layer quantization run completes on the local Ada card (sm_89).
- Output shards are written and loadable.
- Peak VRAM recorded.
- Runtime recorded, used to project the full Phi-4-mini run.

## Phase 9 — Phi-4-mini wrapper

**Objective:** Implement the 7-method adapter plus the name-based registration.

**Exit gate:**
- `PhiWrapper` implements all abstract methods; `get_layer_module`, `_layer_prefixes`,
  `get_mlp_input`, `get_mlp_output`, and `move_embed_to` port from the LLaMA wrapper.
- Fused `qkv_proj` handled with an **explicit, documented decision** on whether it takes the
  attention path or the general path — the base class's `q_proj`/`k_proj` substring check does
  not match it, and that must be resolved deliberately, not by accident.
- Tied `lm_head` guarded (absent from the checkpoint index).
- `'phi'` branch added to `get_model_wrapper()`.
- Wrapper loads the model and reports layer count and parameter count matching the checkpoint.

## Phase 10 — GSQ on Phi-4-mini  *(the experiment)*

**Objective:** Answer the actual question — can Phi-4-mini reach task-lossless at ~3 bpw the way
the 27B did?

**Exit gate:**
- At least two bit budgets run (e.g. 3-bit and 2-bit).
- Perplexity measured on the Phase 1 rig, same corpus, same binary.
- Compared against the frozen FP16 baseline and against the uniform curve at matched size.
- Result labeled PROVEN, INFERRED, or UNRESOLVED. **A negative result is a valid result** and is
  reported as such, not buried.

## Phase 11 — RCO allocation

**Conditional on Phase 10.** RCO assigns per-tensor types under an exact budget using the task
loss. Run only if Phase 10 shows the learned quantizer alone leaves headroom worth allocating.

**Exit gate:**
- RCO run at a stated size budget.
- Allocation dump inspected and compared against the Phase 3 sensitivity map.
- Measured against uniform GSQ at matched size.

## Phase 12 — Export and runtime validation

**Exit gate:**
- GGUF export succeeds in a standard format.
- Artifact loads in the llama.cpp already built and hashed in Phase 1.
- Bounded generation produces coherent output; repetition and EOS behavior recorded.
- Perplexity is not used as the sole quality claim.

## Phase 13 — Closure

**Exit gate:**
- Acceptance criteria met or limitations documented.
- Evidence register complete.
- Retrospective written (required by `CLOSURE.md`).
- Release decision explicitly stated.

## Failure route

```text
any phase → PRESERVE_FAILURE → classify → retry only after a proven input changes
```

No phase may be skipped because a later result looks promising.

