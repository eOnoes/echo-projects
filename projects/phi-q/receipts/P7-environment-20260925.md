# Phase 7 Receipt — GSQ Environment Build

**Project:** phi-q
**Phase:** 7 — Environment build
**Date:** 2026-09-25
**Result:** ✅ **PASS** — the GSQ stack installs and runs on Windows/Ada. No escalation needed.

---

## 1. Objective

Prove the GSQ stack installs and runs on this machine. This was the re-scoped blocker from
Phase 6b: "Phi-4 is unsupported in the abstract" became "can this stack be installed on Windows
at all."

## 2. Method

Isolated venv via `uv`, Python 3.12.13, with a **dependency subset** excluding
`vllm` / `ray` / `lm-eval` / `lighteval` / `humming-kernels`.

**Rationale for the exclusion:** those are the serving and evaluation path. The GSQ README frames
vLLM as the serving step, and its Ada limitation is documented as "a vLLM-upstream gap, not a GSQ
one." Core quantization does not need it — and vLLM has no Windows support.

## 3. Result — all gates passed

```text
venv created        E:/ternary-lab/gsq-env   (Python 3.12.13, isolated)

torch               2.11.0+cu126   |  cuda True
device              NVIDIA GeForce RTX 4070
capability          (8, 9)  Ada / sm_89
vram                12282 MiB

transformers        5.17.0
accelerate          1.15.0
datasets            5.0.1
safetensors         0.8.0
compressed-tensors  0.19.0
lion-pytorch        ok
numpy               2.5.2
```

**GSQ's own source imports — the real test:**

```text
from src.models.base import BaseModelWrapper        OK
from src.models.llama import LLaMAWrapper          OK
from src.quantization import gumbel_quantizer_2bit OK
```

**Architecture availability:**

```text
Phi3ForCausalLM available in transformers 5.17.0:  True
```

## 4. The critical unknown, resolved

GSQ pins `torch==2.11.0` **exactly** in `pyproject.toml`. On Windows, PyPI's `torch` is CPU-only —
the same trap that produced `NotImplementedError: Unsloth cannot find any torch accelerator?` in
Phase 5, which reads like a missing GPU rather than a wrong wheel.

**A Windows CUDA build of exactly this version exists on the PyTorch index:**

```text
torch==2.11.0+cu126        RESOLVED
torchvision==0.26.0+cu126  RESOLVED
```

This was the single biggest risk in the entire GSQ path. It is now closed.

## 5. Isolation

Two separate environments now exist, neither touching the other or the system Python:

```text
E:/ternary-lab/qad-env    phase 5   torch 2.12.1+cu126, unsloth 2026.9.11
E:/ternary-lab/gsq-env    phase 7   torch 2.11.0+cu126, transformers 5.17.0
```

The machine's third CUDA torch (Python 3.14, `2.12.1+cu126`) is untouched. Nothing was
uninstalled, downgraded, or shadowed in a shared location.

## 6. What this unlocks

The escalation route is **not triggered**. GSQ is locally runnable, free, and needs no rented
GPU for the quantization work. Per the doctrine, a paid resource is only justified by a
demonstrated local failure — there isn't one.

## 7. Not verified by this phase

| Claim | State |
|---|---|
| GSQ can quantize a model on this card | **UNVERIFIED — Phase 8** |
| Peak VRAM for a real run | **UNVERIFIED — Phase 8** |
| Runtime for a full Phi-4-mini run | **UNVERIFIED — Phase 10** |
| A Phi wrapper works | **UNVERIFIED — Phase 9** |
| The env supports the serving/eval path | **NO — vLLM excluded by design** |

Installing is not running. Phase 8 must prove GSQ actually quantizes something on sm_89 before
any of this counts.

## 8. Artifacts

```text
E:/ternary-lab/gsq-env/                   isolated venv
E:/Echo-DB/tools/GSQ/                     03fc164
E:/Echo-DB/tools/RCO/                     9a1e09c
```

## 9. Next step

**Phase 8 — toolchain smoke test.** Run GSQ's 2-layer dry run
(`SMOKE_TEST=1 bash scripts/run.sh` or `--max-layers 2`) against a **supported** architecture
(small model, e.g. Qwen3-0.6B) to confirm the quantizer actually executes on this Ada card.
Record peak VRAM and runtime; project the full Phi-4-mini run from the measured rate.
