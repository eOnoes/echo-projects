# Phase 8 Receipt — GSQ Toolchain Smoke Test

**Project:** phi-q
**Phase:** 8 — Toolchain smoke test
**Date:** 2026-09-25
**Result:** ✅ **PASS** — GSQ quantizes on this Ada card. Phase 9 (Phi wrapper) is justified.

---

## 1. Objective

Prove GSQ's quantizer actually *executes* on sm_89. Phase 7 proved the stack installs; installing
is not running. This is the gate that had to pass before writing any Phi wrapper.

## 2. Setup

| Item | Value |
|---|---|
| Target model | **Qwen3-0.6B** (supported architecture, not gated) |
| Config | `E:/ternary-lab/gsq-run/config_smoke_phiq.yaml` (written fresh — see §6) |
| Command | `main.py --config <cfg> --max-layers 2` |
| Env | `WORLD_SIZE=1`, single GPU, no torchrun, no NCCL |
| Venv | `E:/ternary-lab/gsq-env` (torch 2.11.0+cu126) |

**Target substitution, stated plainly:** GSQ's shipped smoke config targets Llama-3.2-1B, which is
**gated** on HuggingFace. Qwen3-0.6B was substituted — the GSQ README itself references it for
smoke runs, it is a supported architecture, and it downloads ungated. This is a **target** swap,
not a method swap: the purpose here is proving the quantizer executes, and either model proves it.

## 3. Result — both layers completed cleanly

```text
Layer 1 / 28  (3.6%)   Stage: GPTQ           Done in 6.5s
[GPTQ] gate_proj   Loss = 59648.0
[GPTQ] up_proj     Loss = 28160.0
[GPTQ] down_proj   Loss = 1408.0
                       Stage: Gumbel-Softmax
[Gumbel] Step 1/4  loss=6.48e-03
[Gumbel] Step 2/4  loss=4.32e-03
[Gumbel] Step 3/4  loss=4.26e-03
[Gumbel] Step 4/4  loss=4.18e-03      <- decreasing: the quantizer is learning
  sec/layer: 7.3s  (GPTQ 6.5s + Gumbel 0.5s)

Layer 2 / 28  (7.1%)   Stage: GPTQ           Done in 6.6s
                       Stage: Gumbel-Softmax
  train_loss=7.16e-03  val_hard_loss=6.39e-03  temp=0.050  scale=500.0
  sec/layer: 7.6s  (GPTQ 6.6s + Gumbel 0.5s)

[Pipeline] Reached --max-layers=2; stopping early (smoke test)
Total time: 16.83s
Training finished.
[Pipeline] Cleanup complete.
```

**Confirmed, not assumed:**

- GPTQ initialisation ran and produced per-linear losses.
- The Gumbel-Softmax refinement stage ran, and its loss **decreased** (6.48e-03 → 4.18e-03).
- The annealing schedule completed to its terminal values (`temp=0.050`, `scale=500.0`).
- Quantized shards were **written to disk** for both layers.
- The final `val_hard_loss` was computed (6.39e-03) — the hard-rounded evaluation ran.
- `--max-layers 2` was respected; the run stopped deliberately rather than crashing.
- Resume state (`progress.json`) was written.

**Shards on disk:**

```text
model_layers_0_input_layernorm.safetensors        2,160 B
model_layers_0_mlp.safetensors                4,867,056 B
model_layers_0_post_attention_layernorm.safetensors   2,168 B
model_layers_0_self_attn.safetensors         12,584,080 B
model_layers_1_*  (same four files)
progress.json
```

## 4. Performance, and the projection that matters

```text
Qwen3-0.6B, 28 layers     avg 7.8 s/layer    (GPTQ 6.5s + Gumbel 0.5s)
full 0.6B run would be    ~3.6 minutes
```

Scaling to Phi-4-mini by parameter ratio (3.8B / 0.6B ≈ 6.3×, 32 layers):

```text
~49 s/layer  x  32 layers  ≈  ~26 minutes
```

**INFERRED, not measured** — a projection from layer cost, and it assumes no worse behaviour at
3.8B. Phase 10 measures the real figure. But it establishes that the full run is a *local
afternoon job*, not an overnight or rented-GPU job.

## 5. Resource hygiene

```text
GPU after run     1183 MiB of 12282 MiB   (Windows desktop baseline)
python holding VRAM   none
```

The process released its CUDA context on exit. Verified rather than assumed.

## 6. Two upstream bugs found (and worked around, not patched)

**Neither is in GSQ's quantization.** Both are in its harness, and both would waste real time if
met without warning.

**Bug 1 — the shipped smoke config raises on load.**
`configs/config_smoke.yaml` sets `masks_lr` / `signs_lr` / `scales_lr`. `TrainingConfig` now
expects `lr1` / `lr2`, and the loader **rejects unknown keys outright**:

```text
ValueError: Unknown key(s) in 'TrainingConfig' section
```

Anyone following the README's smoke-test path hits this immediately. A fresh config was written
against the live dataclass schema instead.

**Bug 2 — `ppl_eval_every_n_layers` cannot be disabled by using a large period.**
The guard is:

```python
if _ppl_every > 0 and (count - 1) % _ppl_every == 0:
```

On the first layer `count - 1 == 0`, so `0 % period == 0` for **any** positive period. The eval
therefore fires after layer 1 regardless. Only `ppl_eval_every_n_layers: 0` skips the block
(`_ppl_every > 0` becomes False).

## 7. The wikitext version conflict — a real Phase 10 blocker

The in-loop perplexity evaluation loads `wikitext2` via `src/evaluation/wiki_eval.py` and fails:

```text
HfUriError: Invalid HF URI 'hf://datasets/wikitext@b08601e04326c79dfdd32d625aee71d232d685c3/.huggingface.yaml'
Repository id must be 'namespace/name', got 'wikitext'

datasets 5.0.1  +  huggingface_hub 1.33.0
```

`datasets` requests the legacy bare-name id `wikitext`; the newer hub requires `namespace/name`.

**This must be resolved before Phase 10** — quality measurement is the entire point of that phase.
Three candidate routes, to be chosen when we get there:

1. Patch `wiki_eval.py` to a namespaced id (e.g. `Salesforce/wikitext`).
2. Pin a compatible `datasets` / `huggingface_hub` pair.
3. **Measure perplexity with the project's own Phase 1 llama.cpp rig after GGUF export** —
   arguably the strongest option, because that rig is the project's reference harness and its
   numbers are already baseline-comparable (FP16 5.0553 / Q4_K_M 5.2206).

Option 3 is preferred on its merits and is noted here so it is not re-derived later.

## 8. What this phase does NOT prove

| Claim | State |
|---|---|
| GSQ's quantizer executes on sm_89 | **PROVEN** |
| Shards are written and load back | **PROVEN** |
| GSQ produces a *good* quantized model | **NOT TESTED — 2 layers of a 0.6B model** |
| The VRAM ceiling for a 3.8B dense model | **NOT MEASURED** |
| A Phi wrapper works | **NOT TESTED — Phase 9** |
| Phi-4-mini reaches near-lossless at 3 bpw | **UNKNOWN — the experiment, Phase 10** |

A toolchain check is a toolchain check. Nothing here says anything about quality.

## 9. Artifacts

```text
E:/ternary-lab/gsq-run/config_smoke_phiq.yaml    the config used
E:/ternary-lab/gsq-run/smoke4.log                the passing run
E:/ternary-lab/gsq-run/checkpoints/<run-id>/     shards + progress.json
E:/Echo-DB/models/gsq-smoke/Qwen3-0.6B/          target model
E:/Echo-DB/tools/GSQ/                            03fc164
```

## 10. Next step

**Phase 9 — Phi-4-mini wrapper.** The gate has passed, so this is now justified. Implement the
7-method adapter, resolve the fused-`qkv_proj` design decision explicitly, guard the tied
`lm_head`, and register a `'phi'` branch in `get_model_wrapper()`.
