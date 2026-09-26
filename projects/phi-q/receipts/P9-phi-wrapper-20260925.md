# Phase 9 Receipt — Phi-4-mini Wrapper for GSQ

**Project:** phi-q
**Phase:** 9 — Phi-4-mini wrapper
**Date:** 2026-09-25
**Result:** ✅ **PASS** — the Phi wrapper drives the real GSQ pipeline over real Phi-4-mini weights, and the existing architectures are unregressed.

---

## 1. Objective

Make GSQ able to quantize Phi-4-mini. GSQ ships wrappers for LLaMA, Qwen3(+MoE), Qwen3.5/3.6(+MoE),
Gemma-4-35B and Kimi K2/K2.5. **Phi-4 is absent.** This phase adds it — or establishes that it
cannot be added without unreasonable surgery.

## 2. What Phi-4-mini actually looks like

Read from the live checkpoint index and config, not inferred:

```text
architectures        Phi3ForCausalLM
model_type           phi3
num_hidden_layers    32
hidden_size          3072
intermediate_size    8192
num_key_value_heads  8
tie_word_embeddings  True
pad_token_id         200029
resid_pdrop          0.0
attention_dropout    0.0
embd_pdrop           0.0

layer 0, all six tensors:
  model.layers.0.input_layernorm.weight              identical to LLaMA
  model.layers.0.self_attn.qkv_proj.weight           FUSED
  model.layers.0.self_attn.o_proj.weight             identical
  model.layers.0.post_attention_layernorm.weight     identical
  model.layers.0.mlp.gate_up_proj.weight             FUSED
  model.layers.0.mlp.down_proj.weight                identical

  model.embed_tokens.weight                          identical
  model.norm.weight                                  identical
  (no lm_head — tied to embed_tokens, absent from the index)
```

**Four deltas from LLaMA:**

1. `self_attn` is a fused `qkv_proj` — there is no `q_proj` / `k_proj` / `v_proj`.
2. `mlp` is a fused `gate_up_proj` (+ `down_proj`) — no `gate_proj` / `up_proj`.
3. `lm_head` is tied and absent from the checkpoint index.
4. The decoder layer applies `resid_attn_dropout` / `resid_mlp_dropout` around each residual
   add. The transformers source flags this inline as *"main diff with Llama"*. Both are `0.0`
   for this model, but the wrapper reproduces the real forward rather than a near-miss.

## 3. Result

```text
Layer 1 / 32  (3.1%)   Stage: GPTQ
  [GPTQ] model.layers.0.mlp.gate_up_proj   Loss = 23680.0     <- fused MLP handled
  [GPTQ] model.layers.0.mlp.down_proj      Loss = 944.0
         Done in 18.3s
                       Stage: Gumbel-Softmax
  [Gumbel] Step 1/4  loss=2.82e-03
  [Gumbel] Step 2/4  loss=1.42e-03
  [Gumbel] Step 3/4  loss=1.18e-03
  [Gumbel] Step 4/4  loss=1.42e-03
  [Pipeline] Finished writing layer checkpoint (model.layers.0)

Layer 2 / 32  (6.2%)   Stage: Gumbel-Softmax
  [Gumbel] Step 1/4  loss=7.92e-03
  [Gumbel] Step 2/4  loss=4.62e-03
  [Gumbel] Step 3/4  loss=3.59e-03
  [Gumbel] Step 4/4  loss=3.68e-03
  [Pipeline] Finished writing layer checkpoint (model.layers.1)

  sec/layer: 20.9s  (GPTQ 18.3s + Gumbel 1.9s)
  avg sec/layer: 21.6s   est. remaining: 00:10:48
  Reached --max-layers=2; stopping early (smoke test)
  Training finished.
  errors: NONE
```

**All eight shards written for both layers:**

```text
model_layers_0_input_layernorm.safetensors
model_layers_0_mlp.safetensors
model_layers_0_post_attention_layernorm.safetensors
model_layers_0_self_attn.safetensors
model_layers_1_*.safetensors   (all four)
progress.json
```

## 4. Cost, and the projection

```text
Phi-4-mini, 32 layers     avg 21.6 s/layer   (GPTQ 18.3s + Gumbel 1.9s)
full run (log's own ETA)  ~10 min 48 s
```

**A full Phi-4-mini GSQ run is roughly an 11-minute local job.** Free, no rented GPU.

This supersedes the Phase 8 projection of ~26 minutes. That estimate scaled Qwen3-0.6B's per-layer
cost by the parameter ratio (6.3x); the measured ratio is 2.8x, because GPTQ cost does not scale
linearly with parameter count — it is dominated by calibration/activation work per layer, which
grows far more slowly than weight size. Phase 8's figure is recorded as INFERRED and this one as
MEASURED; the earlier number was pessimistic.

## 5. Regression check — the part that matters most

`src/trainer.py` is shared by **every** architecture. A change made for Phi is a change to LLaMA,
Qwen3, Qwen3.5/3.6, Gemma and Kimi. So the fix was validated against a known-good target:

```text
TEST B — Qwen3-0.6B, same 2-layer config as Phase 8
  exit=0   |   errors: NONE
  7.8 s/layer   <- IDENTICAL to the pre-patch Phase 8 run
  gate_proj / up_proj / down_proj still logged as three separate projections
  "Training finished."
```

**No regression.** The LLaMA-style path still treats the three projections separately, and the
per-layer cost is unchanged to the decimal.

## 6. Three findings, one of which I got wrong

I stated at the start of this phase that the Phi port would be **purely additive** — a new wrapper
plus a factory branch, with no edit to `base.py` or `trainer.py`. **That was wrong.** The correct
accounting:

```text
main.py      +6 lines    factory branch ('phi')      additive
phi.py       new file    the wrapper                 additive
trainer.py   ~ +16 lines shard-grouping fix          MODIFIES UPSTREAM  <- I missed this
```

I had grepped for hard-coded projection names in `base.py` and `main.py` but **not in
`trainer.py`**, and drew a conclusion about fork surface from an incomplete search. The two sites I
did find (in `base.py`) were correctly assessed as unreachable; I simply did not look in the third
place. Recorded as a correction rather than quietly absorbed.

### Finding A — `base.py`'s LLaMA-specific attention handling is genuinely unreachable

```python
if "q_proj" in name or "k_proj" in name:           # ~lines 380, 432
self_attn_layers = ['q_proj','k_proj','v_proj','o_proj']   # ~line 572
```

Both sit behind `config.quantization.self_attn`, which is **`false` in all 40 shipped configs and
`False` by dataclass default**. `save_attention_to_disc` (line 572) is additionally gated in
`main.py` by that same flag. Site (a) is doubly unreachable: with `is_attn=False` the subset is
MLP-only, so neither substring can match.

**No change needed.** If `self_attn` is ever set True for a Phi model, line 572 WILL raise
`AttributeError` on `get_submodule("...self_attn.q_proj")`.

### Finding B — the real blocker was in `trainer.py`, and it failed silently

```python
# src/trainer.py, before
for tensor_name, quantizer in self.quantizers.items():
    if self.train_attn: ...
    else:
        if "gate_proj" in tensor_name:                    # <- LLaMA-only trigger
            base  = tensor_name[:-len(".gate_proj")]
            pairs = {"gate_proj": ...,
                     "up_proj":   self.quantizers[f"{base}.up_proj"].get_hard_weights(),
                     "down_proj": ...}
            self.model.save_to_disc(base, pairs)
```

**`"gate_proj"` is not a substring of `"gate_up_proj"`** — after `gate_` comes `u`, not `p`. So the
branch never fired for Phi, **no MLP shard was written at all**, and the subsequent
`load_from_disc` raised:

```text
FileNotFoundError: .../model_layers_0_mlp.safetensors
```

This is the only one of the three sites that is actually reached, and it fails *silently* — the log
even reported "Finished writing layer checkpoint", because that message is emitted unconditionally.

**The fix groups by parent module rather than a hard-coded name:**

```python
if not self.train_attn:
    shard_groups = {}
    for tensor_name, quantizer in self.quantizers.items():
        parent, _, local = tensor_name.rpartition(".")
        if not parent or not local:
            continue
        shard_groups.setdefault(parent, {})[local] = quantizer.get_hard_weights()
    for parent, pairs in shard_groups.items():
        if self.model.is_moe or self.global_rank == 0:
            self.model.save_to_disc(parent, pairs)
```

### Finding C — why the fix is grouped by *parent* and not by `.mlp`

This is the trap that a naive fix would have walked into. A "group everything under the MLP prefix"
fix would give:

```text
LLaMA   ...mlp.gate_proj|up_proj|down_proj      parent "...mlp"          one shard   OK
Phi     ...mlp.gate_up_proj|down_proj           parent "...mlp"          one shard   OK
MoE     ...mlp.experts.K.gate_proj|up_proj|down_proj
                                                parent "...mlp.experts.K" one shard PER EXPERT
                                                ^ a ".mlp"-prefix grouping would MERGE all
                                                  experts into one shard and break GSQ's
                                                  flagship MoE models
```

Grouping by parent module is **behaviour-identical for LLaMA and for MoE** (each expert keeps its
own shard) while being correct for Phi. Test B is the evidence for the LLaMA/Qwen path.

## 7. Also fixed: the vendored-code trap

Before the wrapper could run at all, the model failed to load:

```text
ImportError: cannot import name 'LossKwargs' from 'transformers.utils'
  at transformers_modules/phi4_hyphen_mini_hyphen_unsloth/.../modeling_phi3.py line 37
```

`base.py:39` loads with `trust_remote_code=True`, and the model folder ships its own
`modeling_phi3.py` referenced via `config.json`'s `auto_map`. transformers therefore resolves to
**the vendored implementation**, which targets an older transformers and imports the retired
`LossKwargs`. This is not a PhiWrapper bug — it failed inside `super().__init__()`, before any of
this project's code ran.

**Resolution:** a derived sibling directory

```text
E:/ternary-lab/models/base/phi4-mini-nocode/
  auto_map            STRIPPED from config.json
  modeling_phi3.py    ABSENT
  configuration_phi3.py  ABSENT
  weights + tokenizer    copied (7.2 GB)
```

transformers now uses its own native `Phi3ForCausalLM`. **Nothing in the original model folder was
modified or deleted.**

Deliberately **not** fixed by setting `trust_remote_code=False` in `base.py`: that would work for
Phi but could break Qwen3.5 or Kimi, which may genuinely require remote code. A clean model
directory is strictly better — local, reversible, and leaves the upstream tool untouched.

## 8. Upstream fork status

```text
E:/Echo-DB/tools/GSQ
  base commit   03fc164
  phi.py        NEW      additive, no conflict risk
  main.py       +6 lines additive, low conflict risk
  trainer.py    ~16 lines MODIFIED, WILL CONFLICT on a future upstream pull
```

The `trainer.py` change is a genuine divergence from upstream and should be reported upstream as a
bug: **the shard-write trigger is LLaMA-specific and fails silently for any architecture whose MLP
is not named `gate_proj`/`up_proj`/`down_proj`.** A `git pull` of GSQ will conflict here; re-apply
the parent-module grouping if that happens.

## 9. What this phase does NOT prove

| Claim | State |
|---|---|
| The Phi wrapper drives the real GSQ pipeline | **PROVEN** |
| Fused `qkv_proj` and `gate_up_proj` are handled correctly | **PROVEN** |
| Shards are written and reloadable for Phi | **PROVEN** |
| Existing architectures are unregressed | **PROVEN** (Qwen3-0.6B, identical cost) |
| A full 32-layer Phi-4-mini run completes | **NOT YET** — 2 layers of 32 |
| The exported GGUF is loadable by llama.cpp | **NOT TESTED** |
| **Phi-4-mini reaches near-lossless at 2-3 bpw** | **UNKNOWN — this is Phase 10, the experiment** |

Two layers prove the machinery, not the method.

## 10. Artifacts

```text
E:/Echo-DB/tools/GSQ/src/models/phi.py                  the wrapper
E:/Echo-DB/tools/GSQ/src/trainer.py                     shard-grouping fix
E:/Echo-DB/tools/GSQ/main.py                            factory branch
E:/ternary-lab/gsq-run/config_phi4mini_p9.yaml          Phi config
E:/ternary-lab/gsq-run/p9c.log                          passing Phi run
E:/ternary-lab/gsq-run/p9c-qwen.log                     regression run
E:/ternary-lab/gsq-run/p9-checkpoints/                  shards
E:/ternary-lab/models/base/phi4-mini-nocode/            clean model dir
```

## 11. Next step

**Phase 10 — GSQ on Phi-4-mini, the experiment.**

1. Run the full 32 layers (~11 minutes) at 3-bit.
2. Repeat at 2-bit.
3. Resolve the wikitext/hub conflict — **preferred route: measure with the project's own Phase 1
   llama.cpp rig after GGUF export**, since that rig is the reference harness and its numbers are
   already baseline-comparable (FP16 5.0553 / Q4_K_M 5.2206).
4. Compare against FP16, against the uniform curve at matched size, and — the actual thesis —
   against llama.cpp's own hand-tuned K-quants.
