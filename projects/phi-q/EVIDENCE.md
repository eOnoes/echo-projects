# Phi-4-mini Quant Evidence Register

## Frozen prior evidence (recorded verbatim, not re-derived)

| ID | Claim | State | Source |
|---|---|---|---|
| E-001 | FP16 GGUF geometric PPL = 5.0863 | PROVEN | prior milestone receipt |
| E-002 | Q4_K_M geometric PPL = 5.2827 (+3.86%) | PROVEN | prior milestone receipt |
| E-003 | Attention-only ternarization → PPL 59.87 | PROVEN | prior layer-0 localization receipt |
| E-004 | MLP-only ternarization → PPL 40.48 | PROVEN | prior layer-0 localization receipt |
| E-005 | All four projections ternarized → PPL 44,176 | PROVEN | prior layer-0 localization receipt |
| E-006 | Collapse begins at the first complete ternarized block | PROVEN | prior layer-0 localization receipt |
| E-007 | Combined-substitution error is strongly nonlinear | INFERRED | derived from E-003, E-004, E-005 |
| E-008 | No-training strict ternary is not useful | PROVEN | prior milestone conclusion |

## Research audit evidence

| ID | Claim | State | Source |
|---|---|---|---|
| E-101 | Scalar PTQ (GPTQ/AWQ class) plateaus at 3–4 bits per parameter | PROVEN | GSQ paper, arXiv 2604.18556 |
| E-102 | BitNet b1.58 was trained from scratch, not quantized post hoc | PROVEN | Microsoft Foundry Labs / JMLR BitNet paper |
| E-103 | PTQ works acceptably to ~4 bits and degrades sharply at ≤2 bits | PROVEN | published technical summaries |
| E-104 | Per-tensor mixed precision in GGUF form is a shipped technique | PROVEN | GSQ-RCO-GGUF collection |
| E-105 | Salience-driven mixed-precision allocation improves low-bit quality | PROVEN | SliM-LLM, ICML 2025 |
| E-106 | Partially binarized LLMs retain quality by keeping salient weights high-precision | PROVEN | PB-LLM, arXiv 2310.00034 |
| E-107 | Unsloth supports native QAT and recovers up to ~70% of lost accuracy | PROVEN | Unsloth QAT documentation |
| E-108 | Best-practice recipe: LoRA → merge → selective QAT → AWQ | INFERRED | community benchmark results |
| E-109 | `TQ1_0`/`TQ2_0` are 1.6875 and 2.0625 bpw formats for already-ternary models | PROVEN | llama.cpp ternary packing PR |

## Project evidence

| ID | Claim | State | Evidence |
|---|---|---|---|
| P-001 | Baselines reproduced within tolerance | UNRESOLVED | Phase 1 pending |
| P-002 | Ternary failure reproduces | UNRESOLVED | Phase 2 pending |
| P-003 | Learned quantization beats naive rounding at equal bits | UNRESOLVED | Phase 3 pending |
| P-004 | Mixed precision beats uniform at equal average bits | UNRESOLVED | Phase 4 pending |
| P-005 | Fine-tune-then-quantize beats quantize-alone | UNRESOLVED | Phase 5 pending |
| P-006 | Exported artifact generates coherently | UNRESOLVED | Phase 7 pending |
| P-007 | Fine-tuning moves the sensitive regions | UNRESOLVED | Phase 5b pending |
| P-008 | Fine-tuning hardens or softens the model against quantization | UNRESOLVED | Phase 5b pending |

## Open hypotheses (must not be assumed true)

| ID | Hypothesis | Competing view | Test |
|---|---|---|---|
| H-001 | A better-trained model yields a better quantized model | A better-trained model is harder to quantize because outliers grow | Phase 5b direct delta comparison |
| H-002 | Sensitivity maps transfer from base to fine-tuned weights | Fine-tuning relocates the sensitive regions | Phase 5b map comparison |

## Rules

- `PROVEN` requires a test, receipt, or authoritative source.
- `INFERRED` means plausible but not directly verified.
- `UNRESOLVED` means insufficient evidence.
- `DISPROVEN` means a bounded test failed.
- Prior evidence is transcribed, never re-derived or overwritten.
