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

## Phase 2 findings — evidence reconciliation (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P2-001 | The "uncatalogued evidence" is 851 files, not ~90 | PROVEN | `receipts/parity-harness/` file count |
| P2-002 | There are TWO separate ternary efforts, not one | PROVEN | Line A collapse vs Line B Prism pipeline |
| P2-003 | Line B produced a strict group-128 PQ2_0 artifact of 1,368,849,920 bytes | PROVEN | `P2-evidence-reconciliation-20260925.md` §3 |
| P2-004 | Line B reduction is 82.18% (7,680,694,816 → 1,368,849,920) | PROVEN | same |
| P2-005 | Line B's artifact loads and runs on CUDA | PROVEN | `v23-diagnostic-handoff-20260916.md` |
| P2-006 | The apparent CUDA crash was a launcher timeout, not a real crash | PROVEN | `v23` |
| P2-007 | **Line B's quality comparison is INVALID — the unquantized F16 baseline also failed** | PROVEN | `v25-quality-speed-analysis-20260916.md` |
| P2-008 | "Ternarization destroyed Phi-4-mini quality" is NOT established for Line B | PROVEN | follows from P2-007 |
| P2-009 | F16 and PQ2_0 achieved 10/10 semantic agreement on a locked fixture | PROVEN | `v28-deviation-analysis-20260916.md` |
| P2-010 | A QAT pilot spec exists and was never launched | PROVEN | `prism-pq2-recovery/QAT-PILOT-SPEC.md` |
| P2-011 | The QAT spec pins the same revision as this project | PROVEN | `75becc471c56fc34761ec998615ca6f8535c5a61` |

### Hypothesis register

| ID | Hypothesis | State | Test |
|---|---|---|---|
| H-001 | Fine-tuning hardens vs relocates sensitive regions | UNRESOLVED | Phase 5b |
| H-002 | Group-128 scales, not the ternary codes, carry the capacity. Per-tensor strict ternary collapses; per-group-128 does not. | **REFUTED by weight-space error** — grouping improves reconstruction by only 5.5%. Caveat: this metric is invalid for ternary, so the question stands open on functional grounds. | Line A vs Line B at matched bit budgets |
| H-003 | The prior quality-loss measurements were protocol artifacts, not quantization loss | UNRESOLVED | fix the protocol, re-measure F16 and PQ2_0 |

## Phase 3 findings — sensitivity map (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P3-001 | Relative reconstruction error computed for all 128 quantizable tensors | PROVEN | `staging/sensitivity-map.json` |
| P3-002 | Group size (per-tensor → group-32) improves ternary weight error by only 5.5% | PROVEN | `P3-sensitivity-map-20260925.md` §2 |
| P3-003 | **H-002 REFUTED by weight-space error** — grouping is near-irrelevant to reconstruction error | PROVEN | same |
| P3-004 | Ternary weight error is ≈0.51–0.54 for every scheme including group-32 | PROVEN | same |
| P3-005 | Weight-space error is an INVALID predictor of ternary functional damage | INFERRED | §3 — 51% error cannot map linearly to capability when trained-ternary models work there |
| P3-006 | Uniform precision halves error per bit: 3b 0.278, 4b 0.119, 6b 0.027, 8b 0.0066 | PROVEN | §4 |
| P3-007 | Sensitivity is flat across projections (under 1% spread) | PROVEN | §5 |
| P3-008 | Sensitivity is spiked at layer boundaries: L0 0.5228, L31 0.5188 vs ≈0.5130 mid | PROVEN | §5 |
| P3-009 | Kurtosis predicts sensitivity: layer-level r=0.816, tensor-level r=0.623 | PROVEN | §5 |
| P3-010 | Candidate allocation at 4/5/6-bit yields ~1.81 GB, 76.4% reduction | PROVEN (arithmetic) | §6 |
| P3-011 | The candidate allocation preserves quality | **UNVERIFIED** — derived from a proxy | §8 |

## Phase 4 findings — uniform bit-budget curve (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P4-001 | Q2_K: 1.734 GB, PPL 7.1262 (+40.96%) | PROVEN | `P4-uniform-curve-20260925.md` §2 |
| P4-002 | Q3_K_M: 2.122 GB, PPL 5.5222 (+9.24%) | PROVEN | same |
| P4-003 | Q4_K_M: 2.494 GB, PPL 5.2206 (+3.27%) — 67.5% smaller than FP16 | PROVEN | same |
| P4-004 | Q5_K_M: 2.815 GB, PPL 5.1591 (+2.05%) | PROVEN | same |
| P4-005 | Q6_K: 3.156 GB, PPL 5.1552 (+1.98%) | PROVEN | same |
| P4-006 | Q8_0: 4.085 GB, PPL 5.0458 (−0.19%) — effectively lossless | PROVEN | same |
| P4-007 | **Q6_K is strictly dominated by Q5_K_M** (0.08% PPL for +0.341 GB) | PROVEN | §3.3 |
| P4-008 | There is a quality cliff between 3-bit and 4-bit; the curve is flat above 4-bit | PROVEN | §3.1 |
| P4-009 | Q4_K_M reproduces at exactly 5.2206, matching the frozen baseline run | PROVEN | §3.5 |
| P4-010 | The entire Q4→Q5 quality interval is 1.22 percentage points, for 0.321 GB | PROVEN | §4 |
| P4-011 | Heterogeneous allocation at Q4 size can recover "well under 1%" of perplexity | **ESTIMATE, not measured** | §4 |

## Phase 4b findings — decisive heterogeneous test (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P4B-001 | Heterogeneous allocation was built size-matched to control: 2.3425 vs 2.3456 GB | PROVEN | `P4b-heterogeneous-test-20260925.md` §3 |
| P4B-002 | Treatment PPL 5.3961 vs control Q4_K_S 5.3568 at matched size | PROVEN | same |
| P4B-003 | **Heterogeneous allocation LOSES by +0.73% at matched size** | PROVEN | same |
| P4B-004 | The reallocation crossed the 3-bit cliff, paying a large loss for a small gain | INFERRED | §4 — Q3_K_M measured +9.24% vs Q4_K_M +3.27% |
| P4B-005 | The sensitivity differential is too small to pay for size-neutral reallocation | INFERRED | §4 |
| P4B-006 | **llama.cpp's own Q4_K_M mix beats both arms** (5.2206 vs 5.3568 / 5.3961) | PROVEN | §5 |
| P4B-007 | Heterogeneous quantization is parked for this model, with a measured reason | DECIDED | D-020 |
| P4B-008 | The remaining quality gap is a training problem, not an allocation problem | INFERRED | §7 |

## Phase 5 findings — stage 1 smoke test (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P5-001 | Toolchain works end to end: model loads, fake-quant runs, KL computes | PROVEN | `P5-smoke-test-20260925.md` §2 |
| P5-002 | Phi-4-mini FP16 occupies 7316.6 MiB; 3786 MiB free on a 12282 MiB card | PROVEN | §2 |
| P5-003 | **Single-pass teacher+student co-residency does NOT fit locally** (needs est. 12574 MiB) | PROVEN | §3 |
| P5-004 | Teacher-only pass fits (~7716 MiB peak) | PROVEN | §4 |
| P5-005 | 4-bit student + LoRA pass fits (est. 5-6 GB) | ESTIMATE | §4 |
| P5-006 | **Two-pass pipeline (teacher writes targets, student trains against them) fits locally** | INFERRED | §4 |
| P5-007 | KL=205.068 measured on random tokens is a machinery check, NOT a quality metric | PROVEN | §5 |
| P5-008 | Environment: isolated uv venv, torch 2.12.1+cu126, unsloth 2026.9.11 | PROVEN | §7 |
| P5-009 | PyPI torch on Windows is CPU-only; CUDA builds need the PyTorch index | PROVEN | §7 |

## Phase 6 findings — GSQ / RCO tooling investigation (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P6-001 | GSQ learns per-coordinate grid + per-group scales via Gumbel-Softmax; GPTQ init then refinement | PROVEN | `P6-gsq-rco-investigation-20260925.md` §2 |
| P6-002 | GSQ is layer-by-layer with meta-device offload — quantizes beyond VRAM | PROVEN | §2 |
| P6-003 | GSQ can refine existing GGUF K-Quants in-format (Qwen3-8B Q2_K 50.03 → 56.28) | PROVEN | §2 |
| P6-004 | RCO assigns per-tensor types against true task loss under an exact budget | PROVEN | §3 |
| P6-005 | GSQ trains on Ada-class GPUs (sm_89) per its own README | PROVEN | §4a |
| P6-006 | **Phi-4 is not a supported architecture in GSQ** | PROVEN | §4b |
| P6-007 | Our Phase 4b failure is explained by objective + granularity, not by the concept | INFERRED | §3 |
| P6-008 | 5060 Ti measured ~40 tok/s on the 11.8 GB IQ3_S build, MTP active | PROVEN (third-party) | §4c |
| P6-009 | A Phi wrapper can be written to fit GSQ | **UNVERIFIED** | §6 |
| P6-010 | GSQ on Phi-4-mini reaches near-lossless at 3 bpw | **UNKNOWN — the experiment** | §6 |

## Phase 7 findings — GSQ environment build (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P7-001 | Isolated venv created at `E:/ternary-lab/gsq-env`, Python 3.12.13 | PROVEN | `P7-environment-20260925.md` §3 |
| P7-002 | torch 2.11.0+cu126 installs and reports CUDA True on Windows | PROVEN | §3 |
| P7-003 | **A Windows CUDA build of the exactly-pinned torch==2.11.0 exists** | PROVEN | §4 |
| P7-004 | All core imports resolve: transformers 5.17.0, accelerate 1.15.0, datasets 5.0.1, safetensors 0.8.0, compressed-tensors 0.19.0, lion-pytorch | PROVEN | §3 |
| P7-005 | GSQ's own source imports (BaseModelWrapper, LLaMAWrapper, gumbel_quantizer) | PROVEN | §3 |
| P7-006 | Phi3ForCausalLM is available in transformers 5.17.0 | PROVEN | §3 |
| P7-007 | vLLM/ray/lm-eval/lighteval/humming-kernels are not needed for quantization | INFERRED | §2 |
| P7-008 | **No compute escalation is triggered — GSQ runs locally** | PROVEN | §6 |
| P7-009 | GSQ can actually quantize a model on sm_89 | **UNVERIFIED — Phase 8** | §7 |

## Phase 10 findings — GSQ on Phi-4-mini: the experiment (2026-09-25/26)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P10-001 | **GSQ 2-bit on Phi-4-mini: PPL 14.6434 vs FP16 6.9705 = +110.1%** | PROVEN | `P10-gsq-experiment-20260926.md` §2 |
| P10-002 | **GSQ 2-bit does NOT beat llama.cpp K-quants on Phi-4-mini** | **REFUTED (the thesis)** | §2, §9 |
| P10-003 | The export is faithful — dequantized weights correlate 0.88-0.90 with original FP16 | PROVEN | §3b |
| P10-004 | The quantizer emits genuine 2-bit weights — 4 distinct codes {6,7,8,9}, zero-point 8 | PROVEN | §3a |
| P10-005 | Corruption hypothesis (scrambled weights) | **REFUTED** | §3c |
| P10-006 | Negative scales = corruption | **REFUTED** — clean 50/50 sign convention | §3c |
| P10-007 | The serializer pads 2-bit values into 4-bit containers (2x space waste) | PROVEN | §4 |
| P10-008 | Attention was never quantized (`self_attn: false`) — MLP only | PROVEN | §4 |
| P10-009 | Assembled size 3.81 GB = 2.2x Q2_K's 1.73 GB while quantizing less of the model | PROVEN | §4 |
| P10-010 | The first Phase 10 attempt was invalid (smoke-grade config); invalidated, not reported | PROVEN | §6 |
| P10-011 | GSQ's README claims GGUF in-format refinement; **no GGUF code exists in the repo** | PROVEN | §7 |
| P10-012 | The assembled checkpoint loads and is measurable on Windows without vLLM | PROVEN | §8 |
| P10-013 | A full-recipe (16x calibration) run would change the verdict | **UNKNOWN** — largest caveat | §5 |
| P10-014 | The method works at 8B-1T as published | **NOT TESTED HERE** — Phi-4-mini is out-of-regime | §5 |

## Phase 9 findings — Phi-4-mini wrapper (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P9-001 | **The Phi wrapper drives the real GSQ pipeline over real Phi-4-mini weights** | PROVEN | `P9-phi-wrapper-20260925.md` §3 |
| P9-002 | Fused `self_attn.qkv_proj` is handled correctly | PROVEN | §3 |
| P9-003 | Fused `mlp.gate_up_proj` is handled correctly | PROVEN | §3 |
| P9-004 | Shards (incl. `model_layers_N_mlp`) are written and reloadable for Phi | PROVEN | §3 |
| P9-005 | **Existing architectures are unregressed** — Qwen3-0.6B identical 7.8 s/layer | PROVEN | §5 |
| P9-006 | Phi layer cost 21.6 s/layer (GPTQ 18.3s + Gumbel 1.9s) | PROVEN | §4 |
| P9-007 | Full 32-layer Phi run ≈ 11 min local | PROVEN (log's own ETA) | §4 |
| P9-008 | Phase 8's ~26 min projection was pessimistic; true scaling is 2.8x not 6.3x | PROVEN | §4 |
| P9-009 | `base.py` LLaMA-specific attention sites are unreachable (`self_attn` false in all 40 configs) | PROVEN | §6 |
| P9-010 | **`trainer.py` shard-write trigger was LLaMA-specific and failed SILENTLY for Phi** | PROVEN | §6 |
| P9-011 | A `.mlp`-prefix grouping fix would have merged MoE experts — parent-module grouping is correct | PROVEN (reasoning + Test B) | §6 |
| P9-012 | Vendored `modeling_phi3.py` + `auto_map` shadowed transformers' native Phi-3 | PROVEN | §7 |
| P9-013 | **CORRECTION: the port was NOT purely additive** — `trainer.py` is a genuine upstream fork | PROVEN | §6 |
| P9-014 | A full 32-layer Phi-4-mini run completes | **NOT TESTED — 2 of 32 layers** | §9 |
| P9-015 | GSQ produces near-lossless Phi-4-mini at 2-3 bpw | **UNKNOWN — Phase 10, the experiment** | §9 |

## Phase 8 findings — GSQ toolchain smoke test (2026-09-25)

| ID | Claim | State | Evidence |
|---|---|---|---|
| P8-001 | **GSQ's quantizer executes on sm_89** — GPTQ init and Gumbel refinement both ran | PROVEN | `P8-toolchain-smoke-20260925.md` §3 |
| P8-002 | Gumbel loss decreased 6.48e-03 → 4.18e-03 — the quantizer learns | PROVEN | §3 |
| P8-003 | Annealing completed to terminal values (temp=0.050, scale=500.0) | PROVEN | §3 |
| P8-004 | Quantized shards written to disk for both layers | PROVEN | §3 |
| P8-005 | `--max-layers 2` respected; run ended deliberately, not by crash | PROVEN | §3 |
| P8-006 | Layer cost 7.8 s/layer on Qwen3-0.6B (GPTQ 6.5s + Gumbel 0.5s) | PROVEN | §4 |
| P8-007 | Projected Phi-4-mini full run ≈ 26 minutes locally | **INFERRED — projection, not measured** | §4 |
| P8-008 | GPU returned to baseline after exit (1183 MiB); no leaked CUDA context | PROVEN | §5 |
| P8-009 | Upstream bug: `configs/config_smoke.yaml` uses retired keys and raises on load | PROVEN | §6 |
| P8-010 | Upstream bug: `ppl_eval_every_n_layers` cannot be disabled by a large period | PROVEN | §6 |
| P8-011 | **wikitext/hub version conflict blocks the ppl eval — a Phase 10 blocker** | PROVEN | §7 |
| P8-012 | GSQ produces a *good* quantized model | **NOT TESTED — 2 layers of a 0.6B** | §8 |
| P8-013 | A Phi wrapper works | **NOT TESTED — Phase 9** | §8 |

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
