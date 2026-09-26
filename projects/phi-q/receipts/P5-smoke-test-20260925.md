# Phase 5 Receipt — Stage 1 Smoke Test

**Project:** phi-q
**Phase:** 5 — QAD pilot, stage 1
**Date:** 2026-09-25
**Result:** TOOLCHAIN PASSES. Single-pass design does NOT fit local VRAM. Two-pass design does.

---

## 1. Purpose

Answer four questions before writing a training loop:

1. Does Phi-4-mini load in FP16 on the 4070?
2. Does group-wise fake-quant run and stay finite?
3. Does the KL divergence between exact and quantized forward passes compute?
4. **What is the real peak VRAM**, so local-vs-rented is decided on a number?

## 2. Answers

```text
1. loads              3.836 B params, 7316.6 MiB on GPU, 4.5s from disk
2. fake-quant         finite=True, 129 Linear layers targeted, group=32 bits=4
3. KL computes        KL(exact || fake-quant) = 205.068
4. peak VRAM          7716.1 MiB forward peak
   free after load    3786.0 MiB of 12282 MiB
   est. training need 12574 MiB
   FITS LOCALLY       NO
```

## 3. The gate result, stated plainly

**The single-pass design does not fit.** Loading the FP16 weights consumes 7,316.6 MiB of a
12,282 MiB card, leaving 3,786 MiB. Holding teacher and student forward passes plus gradients
needs an estimated 12,574 MiB.

This is a real answer rather than a failure. The design assumed both passes could coexist. They
cannot, on this card.

## 4. The local path that does fit

Both halves of the operation fit **individually** — we just proved the teacher pass fits. So
split them in time rather than running them together:

```text
Pass A  teacher only, no gradients, FP16            ~7,716 MiB peak   FITS
        run the frozen FP16 model over the corpus,
        save soft targets (top-k logits) to disk

Pass B  student only, 4-bit base + LoRA             est. ~5-6 GB      FITS
        train against the saved targets
```

Disk cost is bounded: top-k logits over a bounded corpus. This removes the memory conflict
entirely and keeps the whole pilot local and free — no rental.

It is also closer to the published QAD recipe than a simultaneous two-model forward, and it has a
practical bonus: the saved targets are **reusable**. Run the teacher once, then train as many
student configurations against it as we like without paying for the teacher again.

## 5. Honest caveat on the KL number

`KL = 205.068` was measured on **random token input**, not real text. It proves the machinery
computes a finite, non-trivial divergence between the exact and quantized forward passes. It is
**not** a quality metric and must not be reported as one. The real measurement will use the
corpus.

## 6. Bugs hit and fixed

**My own bug, worth recording.** The first attempt hooked the wrong tensor:

```python
def hook(mod, inp):
    return (wrap(mod.weight),) + inp[1:]   # WRONG: inp[0] is the activation
```

This replaced each Linear layer's *input activation* with its *weight*, producing wrong-shaped
tensors downstream. The error surfaced far from the cause, as an attention stride complaint:

```text
RuntimeError: view size is not compatible with input tensor's size and stride
  at modeling_phi3.py:243  query_states.view(hidden_shape)
```

Fix: swap the **weight** in place via a pre-hook, restore it in the post-hook, so peak extra
memory is one layer rather than a second full copy of the model.

Environment note: transformers 5.x moved to `loading weights` progress bars and the `phi3`
modeling file is at `transformers/models/phi3/modeling_phi3.py`.

## 7. Environment (verified working)

```text
E:/ternary-lab/qad-env           isolated uv venv, Python 3.12.13
torch         2.12.1+cu126       cuda True
unsloth       2026.9.11
transformers  5.5.0
peft          0.21.0
trl           0.24.0
bitsandbytes  0.50.2
device        NVIDIA GeForce RTX 4070, 12282 MiB
```

**Install trap worth remembering:** `pip install unsloth` pulls `torch==2.12.1+cpu` from PyPI,
because Windows PyPI torch is CPU-only by default. CUDA builds live on PyTorch's own index.
Unsloth then fails at import with `NotImplementedError: Unsloth cannot find any torch
accelerator?` — which reads like a missing GPU, not a wrong wheel. Fix: reinstall torch from
`https://download.pytorch.org/whl/cu126`.

The venv is fully isolated; the machine's other CUDA torch (Python 3.14, `2.12.1+cu126`) was
never touched.

## 8. Artifacts

```text
staging/qad_smoke_test.py            the test
staging/qad-smoke-test.json          machine-readable receipt
staging/qad-smoke2.log               passing run
staging/phi-q-corpus/instruction.jsonl   8,000 examples  47.60 MB
staging/phi-q-corpus/general.jsonl      20,000 examples  14.87 MB
```

## 9. Next step

Build the two-pass pipeline: teacher pass writes soft targets, student pass trains against them.
Both fit locally. No paid resource required.
