# Phase 2 Finding — Evidence Reconciliation

**Project:** phi-q
**Phase:** 2 — Reproduce and classify the ternary failure
**Date:** 2026-09-25
**Result:** PREMISE CORRECTED — the "ternary failure" is not a proven precision limit
**GPU used:** none (review only)
**Artifacts modified:** none

---

## 1. Why this note exists

Phase 2 was scoped as *"reproduce and classify the ternary failure."* Before reproducing
anything, the plan required reviewing the uncatalogued `parity-harness/` evidence.

That review found **851 files** (not the ~90 first noted), spanning 2026-09-14 to 09-16. They
contain a **second, more advanced ternary line** that the project record did not reflect. The
premise of Phase 2 is therefore wrong, and correcting it is cheaper than reproducing an
experiment whose meaning is already in question.

## 2. There are two separate ternary efforts, not one

| | Line A — raw projection ternarization | Line B — Prism strict PQ2_0 |
|---|---|---|
| What | Ternarize attention / MLP / all projections in PyTorch | Full pipeline: strict group-128 ternary → GGUF |
| Result | Collapse to 44,176 PPL (all four projections) | 7.68 GB → 1.37 GB artifact, loads and runs |
| Evidence | `receipts/ternary-milestone-layer-localization.html` | `prism-pq2-recovery/` |
| Status | Catastrophic | **Measured as working; quality comparison invalid** |

The project record only carried Line A. Line A's collapse is real and remains valid.

Line B is materially different and was not reflected at all.

## 3. Line B artifacts (previously uncatalogued)

All under `E:/ternary-lab/prism-pq2-recovery/artifacts/`:

| Artifact | Bytes | SHA-256 |
|---|---|---|
| `phi4-mini-PQ2_0.gguf` | 1,368,849,920 | `4e9378fcf7512faff3c9976b632e52195339c6f460be009860f390bee03a530c` |
| `phi4-strict-PQ2_0.gguf` | 1,368,849,952 | `4f707883e75f09178e5d5b9f4fa0a492b4f03e9c7071bdb84585a83e7ad9e648` |
| `phi4-strict-dequant-f16.gguf` | 7,680,694,816 | `2730d388a9c557af1ccdbd8ae044f82549a7f6eb76c3bc05153cb0a12949810a` |
| `layer0-o-proj-strict-pq2.bin` | 2,506,752 | `c8598b1a524568b7323f91a0369f9b44656629f228693c0f55e1dc1b8f4731dd` |

Also present: dequantized HF checkpoints (`phi4-strict-dequant-hf`, `-hf-v2`), a Prism source
tree, and two test scripts (`strict_pq2_parity.py`, `transform_hf_strict.py`).

**Reduction: 7,680,694,816 → 1,368,849,920 bytes = 82.18%.**

The conversion source is `phi4-arm-b-f16-longrope-factors-v2.gguf`
(SHA-256 `130621ac18aad9357daf01c40769138dad58e743413f680d7ea8e22015dc4803`,
7,680,694,016 bytes) — an F16 Phi-4-mini carrying LongRoPE factors.

## 4. The critical finding: Line B's quality comparison was invalid

From `v25-quality-speed-analysis-20260916.md`, verbatim:

> "The original F16 baseline produced repetitive fragments instead of valid answers for the
> real-world prompts. ... Because the original baseline failed to answer the benchmark, the
> batch cannot measure intelligence loss or answer-quality degradation. A zero score for both
> models would measure the prompt/interface configuration, not the model representation."

Observed fragments included `Mar`, `pr`, `Michael`, `rerere`.

**This is decisive.** The unquantized F16 model failed the same benchmark the ternary model
failed. The benchmark was measuring a broken prompt protocol, not quantization damage.

The required correction was identified and not yet applied: chat-template/conversation mode,
tokenizer and prompt formatting, stop/EOS handling, system/user wrapper, and whether
`--simple-io` altered the prompt path.

**Consequence: the claim "ternarization destroyed quality on Phi-4-mini" is NOT established for
Line B.** It remains established for Line A, which is a different experiment.

## 5. What Line B did establish

| Claim | State |
|---|---|
| Strict group-128 PQ2_0 conversion completes and produces a loadable artifact | PROVEN |
| The artifact runs on CUDA and reaches token generation | PROVEN (`v23`) |
| An apparent CUDA crash was a launcher timeout, not a real crash | PROVEN (`v23`) |
| Size reduction is 82.18% | PROVEN |
| F16 and PQ2_0 agree exactly on factual/arithmetic smoke prompts | PROVEN (`v28`) |
| 10/10 semantic agreement on a locked fixture (F16 vs PQ2_0) | PROVEN (`v28`) |
| 36/36 operational cells returned zero | PROVEN (`v24`) |
| Real-world quality comparison | **INVALID — baseline harness broken** |
| Any general intelligence-loss percentage | NOT ESTABLISHED |
| Any universal speedup | NOT ESTABLISHED |

Note the tension in the record: `v28` reports 10/10 agreement on a locked fixture, while `v25`
reports both models producing garbage on real-world prompts. Both are honest; they measured
different protocol conditions. The difference is the whole point — **protocol dominates**.

## 6. A QAT pilot already exists as a specification

`prism-pq2-recovery/QAT-PILOT-SPEC.md` — status: *"READY FOR HUMAN/GO-NO-GO REVIEW - NOT LAUNCHED"*.

It specifies five arms (FP16 reference, Q4_K_M control, absmean strict ternary with no training,
learned positive group scales with STE, learned scales plus distillation against a frozen FP16
teacher), three stages (startup gate, 100-step smoke, 1,000-step bounded recovery, export gate),
explicit early-stop criteria, and a hard **$100** ceiling.

It pins the same model revision as this project: `75becc471c56fc34761ec998615ca6f8535c5a61`.

**This is substantially Phase 6 of the phi-q plan, already written and never run.**

## 7. Correction to the phi-q plan

The plan must change. The old Phase 2 (*reproduce and classify the ternary failure*) assumed a
single failure with a single cause. The evidence says otherwise.

**Corrected Phase 2:**

1. **Fix the prompt protocol first.** Establish a Phi-4-mini chat-template-correct harness and
   confirm the *unquantized F16* model answers correctly on real prompts. Until the baseline
   answers, no quantization comparison means anything.
2. **Re-measure Line B** (strict PQ2_0) against a *working* baseline. The old comparison cannot
   be rescued — it must be re-run.
3. **Reconcile Line A** (raw ternarization collapse) against Line B. Line A ternarized
   projections directly; Line B uses strict group-128 PQ2_0 with per-group scales. The
   difference between them — group scales — is likely the entire explanation, and that is
   directly testable.
4. **Defer the QAT pilot decision** until steps 1–3 say whether training is even needed.

**New hypothesis, replacing the old framing:**

| ID | Hypothesis | Test |
|---|---|---|
| H-002 | Group-128 scales, not ternary codes, carry the representational capacity. Strict ternary per-tensor collapses; strict ternary per-group-128 does not. | Compare Line A vs Line B at matched bit budgets |
| H-003 | The prior "quality loss" measurements were protocol artifacts, not quantization loss. | Fix the protocol, re-measure F16 and PQ2_0 |

## 8. Scope note

Nothing was modified, moved, or deleted. The Prism artifacts, the Prism source tree, the QAT
spec, and all 851 parity-harness files remain exactly where they were. This note is a reading
of existing evidence.

## 9. Prompt protocol — FIXED and validated

The protocol defect identified in `v25` was reproduced and corrected.

**Broken protocol** (raw completion, no chat template): produced nothing or fragments.

**Working protocol** (`-sys` + single-turn conversation mode, template from the model):

| Prompt | F16 output | Verdict |
|---|---|---|
| "What is the capital of France?" | "The capital of France is Paris." | CORRECT |
| "What is 17 times 19?" | "17 times 19 equals 323." | CORRECT |
| "What is 15% of 240?" | "240 * 0.15 = 36 ... So, 15% of 240 is 36." | CORRECT |
| "Write a Python function that adds two numbers." | valid `def add_two_numbers(num1, num2)` | CORRECT |

These are the same categories that failed in the prior campaign. **H-003's precondition is
satisfied: the unquantized baseline answers correctly under a documented protocol.**

## 10. Ternary artifacts under the corrected protocol

Both Prism artifacts were run through the Prism runtime with the same chat-template protocol:

| Artifact | Output |
|---|---|
| `phi4-mini-PQ2_0.gguf` | `samples samples ... laps laps blocks blocks` — repetition |
| `phi4-strict-PQ2_0.gguf` | `510510510510510510...` — repetition |

**This result is NOT reported as a finding, because the control failed.**

The control required running F16 through the *same* Prism runtime. It aborted:

```text
Prism runtime loading stock F16 GGUF  ->  exit 1 at "fitting params to device"
```

Without that control, the difference between the F16 run and the ternary runs is confounded by
the runtime, not just the model. Reporting "the ternary model is broken" from these two runs
would repeat exactly the error recorded as W-002 (comparing numbers from different rulers).

## 11. Phase 2 outcome — CAPPED

**Status: CLOSED WITH UNRESOLVED ITEM.**

| Item | Result |
|---|---|
| Premise corrected | DONE |
| Two ternary lines identified and separated | DONE |
| Prompt protocol fixed and validated on F16 | DONE |
| Line B re-measured against a working baseline | **NOT ACHIEVED — clean control not obtainable locally** |
| H-002 (group scales carry capacity) | UNRESOLVED |
| H-003 (prior losses were protocol artifacts) | PARTIALLY SUPPORTED — protocol defect proven, but it does not follow that the artifacts are correct |

**Decision (D-016):** cap the ternary reconciliation rather than chase a control that local
tooling cannot produce. The remaining question — is the old ternary artifact good or bad — is
answerable later, and more cheaply, by re-running Line B's export from source once the
mixed-precision design exists. It does not block the main line.

**The main line resumes at the sensitivity map on F16: identify which layers and tensors are
most sensitive, then allocate precision where it is needed.** That is the work the project was
actually created for.

## 12. Scope note

Nothing was modified, moved, or deleted. New files created are analysis logs under
`staging/`. The Prism artifacts, the Prism source tree, the QAT spec, and all 851
parity-harness files remain exactly where they were.
