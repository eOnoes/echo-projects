# Phi-4-mini Quant Evidence Register

## Frozen prior evidence (recorded verbatim, not re-derived)

**Harness warning:** two different harnesses appear below and their numbers are not
comparable. Always check the Harness column before comparing.

| ID | Claim | Harness | State | Source |
|---|---|---|---|---|
| E-001 | FP16 GGUF geometric PPL = 5.0863 | llama.cpp ppl_v2 | PROVEN | prior milestone receipt |
| E-002 | Q4_K_M geometric PPL = 5.2827 (+3.86%) | llama.cpp ppl_v2 | PROVEN | prior milestone receipt |
| E-003 | Attention-only ternarization → PPL 59.87 | PyTorch bf16 | PROVEN | prior localization receipt |
| E-004 | MLP-only ternarization → PPL 40.48 | PyTorch bf16 | PROVEN | prior localization receipt |
| E-005 | All four projections ternarized → PPL 44,176 | PyTorch bf16 | PROVEN | prior localization receipt |
| E-006 | Collapse begins at the first complete ternarized block | PyTorch bf16 | PROVEN | prior localization receipt |
| E-007 | Combined-substitution error is strongly nonlinear | derived | INFERRED | derived from E-003..E-005 |
| E-008 | No-training strict ternary is not useful | both | PROVEN | prior milestone conclusion |
| E-009 | PyTorch harness FP16 baseline = 12.0361 | PyTorch bf16 | PROVEN | recovered from layer-sweep receipts |
| E-010 | Layer-count degradation is explosive, not gradual | PyTorch bf16 | PROVEN | recovered layer sweeps 1/2/4/8/16/32 |

## Phase 0 findings (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P0-001 | All required artifacts are present locally; no download needed | PROVEN | `P0-freeze-20260925.md` |
| P0-002 | Frozen artifacts are unchanged since prior work (3 hash matches) | PROVEN | `P0-freeze-20260925.md` §3 |
| P0-003 | Recorded baselines reproduce exactly from raw per-chunk data | PROVEN | `P0-freeze-20260925.md` §4 |
| P0-004 | Baseline harness fully specified and reproducible | PROVEN | `P0-freeze-20260925.md` §4 |
| P0-005 | **E-001/E-002 and E-003/E-004/E-005 are on different harnesses and must not be compared** | PROVEN | `P0-freeze-20260925.md` §5 |
| P0-006 | Ternary ratios vs their own baseline: 4.97×, 3.36×, 3,670× | PROVEN | `P0-freeze-20260925.md` §5 |
| P0-007 | Uncatalogued parity-harness evidence set exists (~90 receipts) | PROVEN | `P0-freeze-20260925.md` §8 |

## Phase 1 findings (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P1-001 | Corpus is the model README; tokenizes to exactly 7,994 tokens | PROVEN | `P1-measurement-rig-20260925.md` §1 |
| P1-002 | Harness algorithm: `n_chunk = ceil((ntokens - n_ctx) / ppl_stride)` | PROVEN | read from perplexity.cpp |
| P1-003 | Original harness used `n_ctx=768`, stride 1024 | PROVEN | derived arithmetic + log match |
| P1-004 | FP16 PPL on our harness = 5.0553 | PROVEN | `P1-measurement-rig-20260925.md` §5 |
| P1-005 | Q4_K_M PPL on our harness = 5.2206 | PROVEN | `P1-measurement-rig-20260925.md` §5 |
| P1-006 | Q4 penalty = +3.27% on our harness | PROVEN | `P1-measurement-rig-20260925.md` §5 |
| P1-007 | Deviation from historical is −0.61% / −1.18%, directional and explained | PROVEN | larger window lowers PPL |
| P1-008 | Original exact reproduction requires a binary that is not on this machine | PROVEN | parameter combination inexpressible |
| P1-009 | Phase 1 gate (±0.5%) is not achievable as written | PROVEN | observed −0.61% / −1.18% |

**Baseline of record for this project:**

```text
FP16     5.0553
Q4_K_M   5.2206
binary   f88a3a5110dba2e666dc5e86854149effa7e8817dd7a8ae15a0fab6e5dd3ab89
corpus   03ba3dd2a779b2ddb2d37d6571900a58423ab95eb02bf9915e259432b78ee6b1
```

All subsequent comparisons use this baseline. Historical figures are cross-reference only.

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
