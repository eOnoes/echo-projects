# Phase 6b Receipt — GSQ Wrapper Feasibility Spike

**Project:** phi-q
**Phase:** 6b — read the wrapper interface, determine the Phi-4 delta
**Date:** 2026-09-25
**Result:** A Phi wrapper is a SMALL, well-scoped delta. Environment is the bigger risk.

---

## 1. What was done

Cloned GSQ (`03fc164`) and RCO (`9a1e09c`) into `E:\Echo-DB\tools\`. Read:

```text
src/models/base.py       the abstract wrapper contract
src/models/llama.py      the simplest concrete wrapper (dense, 177 lines)
main.py                  get_model_wrapper() — the selection logic
configs/                 available run configs
pyproject.toml           dependencies
```

## 2. The wrapper contract

`BaseModelWrapper` requires 7 abstract methods plus two attributes:

```text
get_mlp_input(layer_input)      route input through layernorm+attention, residual add
get_mlp_output(mlp_input)       layernorm + mlp, residual add
get_layer_module(idx)           return decoder layer i
move_embed_to(device)           move / offload embeddings
move_output_heads_to(device)    move / offload norm + lm_head
_layer_prefixes(layer_name)     -> {"non_mlp": [...], "mlp": [...]}
ppl_evaluation(read_from_disk)  perplexity loop
attrs:  self.layer_prefix, self.num_layers
```

The base class handles everything else: GPTQ calibration, the Gumbel quantizers, layer
offload/restore, save/load shards, distributed hooks, the training loop.

## 3. THE DELTA — Phi-4-mini vs the LLaMA template

Phi-4-mini is `Phi3ForCausalLM`, 32 layers, hidden 3072, intermediate 8192,
24 heads / 8 KV heads, vocab 200064, `tie_word_embeddings: true`.
194 parameters total (6 per layer x 32 + embed_tokens + norm).

Actual parameter names, read from the local checkpoint index:

```text
model.embed_tokens.weight
model.norm.weight
model.layers.N.input_layernorm.weight
model.layers.N.self_attn.qkv_proj.weight        <- FUSED q+k+v
model.layers.N.self_attn.o_proj.weight
model.layers.N.post_attention_layernorm.weight
model.layers.N.mlp.gate_up_proj.weight          <- FUSED gate+up
model.layers.N.mlp.down_proj.weight
```

Against the LLaMA wrapper's expectations:

| LLaMA | Phi-4-mini | Match |
|---|---|---|
| `model.layers.N` | `model.layers.N` | identical |
| `input_layernorm` | `input_layernorm` | identical |
| `post_attention_layernorm` | `post_attention_layernorm` | identical |
| `self_attn.o_proj` | `self_attn.o_proj` | identical |
| `mlp.down_proj` | `mlp.down_proj` | identical |
| `model.embed_tokens` | `model.embed_tokens` | identical |
| `model.norm` | `model.norm` | identical |
| `self_attn.q_proj/k_proj/v_proj` | `self_attn.qkv_proj` | **FUSED** |
| `mlp.gate_proj/up_proj` | `mlp.gate_up_proj` | **FUSED** |
| `lm_head` | (tied to embed_tokens) | **ABSENT** |

**Eight of ten module paths are byte-identical to LLaMA.** `get_layer_module`,
`_layer_prefixes`, `get_mlp_input`, `get_mlp_output`, `move_embed_to` all port unchanged.

Three real deltas:

1. **Fused `qkv_proj`.** The base class special-cases attention by name:
   `if "q_proj" in name or "k_proj" in name:` — routing those straight to
   `update_quantized_weights` instead of the GSQ trainer. `qkv_proj` matches neither
   substring, so the attention layer would silently take the *other* path. This is a
   **design decision** (how should a fused qkv be treated?), not just plumbing.
2. **Fused `gate_up_proj`.** Probably benign — the MLP path iterates `nn.Linear` children,
   and one fused Linear is still one Linear. Needs confirmation.
3. **Tied `lm_head`.** `move_output_heads_to` requests `lm_head` from the checkpoint index,
   which does not contain it. Needs a guard or an alias to `embed_tokens`.

Also to register: a `'phi' in name_lower` branch in `get_model_wrapper()`, which currently
selects purely by **substring match on the model name/path**, not by architecture.

## 4. Environment — the larger risk

`pyproject.toml` pins aggressively and pulls a lot:

```text
torch==2.11.0          (pinned exactly)
torchvision==0.26.0    (pinned exactly)
vllm==0.20.2           (pinned exactly)
ray, lm-eval[api], lighteval, humming-kernels, wandb, compressed-tensors,
lion-pytorch, tiktoken, ninja, matplotlib
```

**vLLM does not support Windows.** But it is the *serving / evaluation* path only — the
README frames vLLM as the serving step, and its Ada limitation is explicitly "a vLLM-upstream
gap, not a GSQ one." Core quantization appears not to need it.

So the install plan is a **subset**: transformers, accelerate, datasets, safetensors,
compressed-tensors, lion-pytorch, tqdm, pyyaml, python-dotenv, tiktoken, numpy, torch — and
**not** vllm / ray / lm-eval / lighteval / humming-kernels.

Open questions, all unverified:
- does `compressed-tensors` install cleanly on Windows
- does `humming-kernels` matter for quantization or only for serving (assumed: serving only)
- is `torch==2.11.0` available as a Windows cu126/cu128 build, or must we relax the pin
- does the `transformers` version GSQ expects still ship a compatible `Phi3ForCausalLM`

## 5. Smoke test available

GSQ ships a cheap dry run, which is exactly the step-2 check:

```bash
SMOKE_TEST=1 bash scripts/run.sh              # 2-layer smoke test
uv run python main.py --config configs/local/config.yaml --max-layers 2
```

`configs/config_smoke.yaml` exists. The README references a CPU-size **Qwen3-0.6B 2-bit
smoke run**, which is a suitable supported-architecture target for proving the toolchain
before touching Phi.

**Ada note:** sm_89 is fine for training (README states they ran on L40S/Ada). The sm_89
caveat applies only to vLLM MoE serving.

## 6. Honest verdict

| Claim | State |
|---|---|
| Wrapper contract is 7 abstract methods + 2 attrs | PROVEN |
| 8 of 10 Phi-4 module paths match the LLaMA template | PROVEN (read from the real checkpoint) |
| Fused qkv requires a real design decision | PROVEN |
| A Phi wrapper is a few hundred lines | **INFERRED — not yet written** |
| The environment installs on Windows | **UNVERIFIED — the next thing to test** |
| torch==2.11.0 has a Windows CUDA build | **UNVERIFIED** |

**The blocker moved.** It is no longer "Phi is unsupported" in the abstract — it is
"can this stack be installed on Windows at all." That is a cheap question to answer and it
should be answered before writing a line of wrapper code.

## 7. Next step

Create an isolated venv, install the **subset** of dependencies, and run the 2-layer smoke
test on a supported model. If that passes, the Phi wrapper is worth writing. If the
environment will not build, the whole path needs a Linux box or a rented GPU — a costed
decision for Eddie, not a silent substitution.

## 8. Artifacts

```text
E:\Echo-DB\tools\GSQ    03fc164
E:\Echo-DB\tools\RCO    9a1e09c
E:\ternary-lab\models\base\phi4-mini-unsloth\   (param names read from index)
```
