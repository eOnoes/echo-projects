# Phase 10 Receipt — GSQ on Phi-4-mini: the experiment

**Project:** phi-q
**Phase:** 10 — GSQ on Phi-4-mini (the experiment)
**Date:** 2026-09-25 / 2026-09-26
**Result:** ✅ **THE QUESTION IS ANSWERED** — and the answer is **NEGATIVE**.

GSQ, as configured and run here, does **not** beat llama.cpp's K-quants on Phi-4-mini.
The export is faithful; the degradation is real.

---

## 1. Objective

Answer the question the project exists for: **can GSQ+RCO recover usable quality from
Phi-4-mini at 2–3 bits per weight, better than the uniform K-quant curve?**

Context: Phase 4b had established that naive heterogeneous allocation loses to llama.cpp's
hand-tuned Q4_K_M on this model. The Phase 6 method revision replaced that approach with
ISTA-DASLab's GSQ (learned grid via Gumbel-Softmax) + RCO (budgeted per-tensor allocation).

## 2. The headline numbers

```text
                        PPL       tokens    harness
FP16 baseline          6.9705      3584     fixed (this project)
GSQ 2-bit, corrected  14.6434      3584     fixed (this project)
                      ------
                      +110.1% perplexity vs FP16
```

**For scale:** on the project's *other* ruler (the Phase 1 llama.cpp rig, README corpus,
`-c 768 --ppl-stride 1024`), llama.cpp's own numbers were:

```text
FP16      5.0553
Q2_K      7.1262   (+40.96%)
Q3_K_M    5.5222   (+9.24%)
Q4_K_M    5.2206   (+3.27%)
```

**These two rulers are not comparable to each other** (different corpus, different windowing,
different binary — see W-002, the cross-ruler error this project already hit once). The
honest statement is therefore:

> GSQ 2-bit costs **+110%** on the fixed harness where FP16 costs 0%.
> llama.cpp's Q2_K costs **+41%** on a different ruler where FP16 costs 0%.
> Both are ratios against their own FP16 baseline, so the *ratios* are comparable in
> character even though the absolute perplexities are not. **GSQ is substantially worse.**

## 3. Why this is a real result and not an artifact

This is the part that took the longest to establish, and it matters.

### 3a. The quantization is genuine 2-bit

Unpacked the stored codes directly from the shards:

```text
model.layers.0.mlp.down_proj     DISTINCT CODES: 4   range 6..9
model.layers.5.mlp.gate_up_proj  DISTINCT CODES: 4   range 6..9
model.layers.31.mlp.down_proj    DISTINCT CODES: 4   range 6..9
```

Codes `{6,7,8,9}` with zero-point 8 = signed `{-2,-1,0,+1}` — exactly the 2-bit codebook GSQ
documents. Four levels, confirmed across three layers by two independent processes.

### 3b. The export is faithful

The decisive test — dequantized GSQ weights vs the original FP16 weights:

```text
                                    cosine     correlation
model.layers.0.mlp.down_proj        0.88411    0.88434
model.layers.0.mlp.gate_up_proj     0.88977    0.88976
model.layers.20.mlp.down_proj       0.90361    0.90369

std   orig 0.0328  vs  dequant 0.0331
mean|w| ratio (deq/orig)  0.88 - 0.93
```

**~0.89 correlation is exactly what a working 2-bit quantization produces** — four discrete
levels cannot correlate more strongly with a continuous distribution. Standard deviations
match; magnitudes shrink slightly as expected. Nothing is scrambled.

### 3c. Rejected hypotheses (each tested, not assumed)

| Hypothesis | Test | Verdict |
|---|---|---|
| Export corrupted (weights scrambled) | cosine/correlation vs original | **REFUTED** — 0.89 |
| Negative scales = corruption | sign audit across 5 layers | **REFUTED** — clean 50/50, a sign convention |
| Container bit-width wrong | `weight_packed` column count | CONFIRMED as an issue, but *space* only |
| PPL harness broken | FP16 measured same harness | **REFUTED** — FP16 reproduced 6.9705 |

## 4. What GSQ actually produced

```text
quantized Linears      64         (32 layers x 2 MLP projections)
logical weights        2,415,919,104
packed int32 elements  301,989,888
EFFECTIVE bits/weight  4.000      <- in a 4-bit container
distinct VALUES        4          <- but only 2 bits of information
assembled size         3.81 GB
attention              bf16       (self_attn: false -> NOT quantized)
```

**Two defects in what I produced, both worth recording:**

**Defect 1 — the serializer pads 2-bit weights into 4-bit containers.** Four distinct values
stored at 4.000 bits/weight. A true 2-bit container would be ~1.9 GB, not 3.81 GB. This is a
`save_model.py` / compressed-tensors packer artifact, not a GSQ method flaw — but it is what
is on disk, and it doubles the footprint for zero quality benefit.

**Defect 2 — attention was never quantized.** `self_attn: false` means the MLP alone was
quantized (2.42B of 3.84B parameters). This makes GSQ's output *starved* of precision
reduction relative to a K-quant that quantizes everything.

**The comparison is therefore damning in a specific way:**

```text
GSQ (mine)         3.81 GB    MLP only, 2-bit values in 4-bit containers   +110%
Q2_K (llama.cpp)   1.73 GB    everything quantized                          ~+41%
Q4_K_M             2.49 GB    everything quantized                          ~+3.3%
```

**GSQ uses 2.2x the space of Q2_K while quantizing less of the model, and scores worse.**

## 5. Bounding the claim — what this does and does not say

### What it says

> On Phi-4-mini, with this configuration and this export path, GSQ's 2-bit output is
> materially worse than llama.cpp's K-quants, at larger size. The method's premise —
> learned grids beating hand-tuned uniform quant — **did not hold here.**

### What bounds it — stated so the claim is not overread

1. **Calibration was 1/16th of GSQ's recipe.** 1024 x 1024 = 1.05M tokens vs the shipped
   4096 x 4096 = 16.8M. This is a *real* calibration (16x the smoke run) but not a faithful
   reproduction. **This is the single largest caveat.**
2. **phi-q's Phase 8 estimate of the shipped recipe's cost was ~35 hours on this card**, which
   is why the reduced calibration was used. A full-recipe run is a legitimate future experiment.
3. **Phi-4-mini is 3.8B and dense.** GSQ's published results are on Llama-3.1-8B/70B,
   Kimi-K2.5 and Qwen3.5-MoE — 8B to 1T parameters. Small dense models have far less
   redundancy to absorb aggressive quantization. **The method was demonstrated outside this
   regime, and the negative result may simply reflect that.**
4. **Cross-ruler caveat for the K-quant comparison** (see §2). Ratios against own-baseline
   are comparable in character; absolute perplexities are not.
5. **`gsq_bits: 3` is an untested mode.** GSQ ships no example config using it (`GumbelQuantizerInt`),
   and the first Phase 10 attempt using it is invalidated (see §6).

## 6. The invalidated first attempt — recorded, not hidden

The first Phase 10 run used a config built from GSQ's **smoke** config rather than a production
one:

```text
                 shipped (real)    first attempt    corrected run
num_samples      4096              128              1024
max_length       4096              512              1024
batch_size       64                4                8
nsamples (GPTQ)  512               128              512
dataset          open_thoughts     c4               open_thoughts
gsq_bits         2                 3 (UNTESTED)     2
```

It produced PPL 12.2908 (+76%) and was **not** reported as a finding — it was flagged as
anomalous, the anomaly was traced, and the run was redone. The corrected run is **worse**
(14.64, +110%), which is consistent: 2-bit is more aggressive than 4-level-at-4-bit-container,
and the corrected run is a genuine 2-bit quantization.

**The lesson is in L-022.** The shipped configs' data settings were visible in the repository
the whole time; a thirty-second comparison would have prevented a 19-minute run and a round
of reasoning about a meaningless number.

## 7. Two upstream GSQ facts confirmed

- **No GGUF code exists in the repository.** `grep -rn 'gguf' --include=*.py .` returns zero
  hits in source and config schema, despite the README claiming GSQ "can also refine publicly
  released GGUF K-Quant checkpoints in-format." That capability is not in commit `03fc164`.
  It affected planning materially: the Phase 1 rig was expected to be the measurement
  instrument, and is not usable for GSQ output.
- **The intended eval path is vLLM + lm-eval**, which has no Windows support. Worked around
  by loading the assembled checkpoint with stock transformers + compressed-tensors, which
  does work locally and free (see §8).

## 8. What DID work — the durable parts

These survive the negative result and are the project's real assets:

```text
1. GSQ runs on a 12 GB Ada card        CUDA-only, Hopper-tested toolchain, working locally
2. Phi-4-mini is a supported arch      PhiWrapper implemented; 7 abstract methods
3. A silent upstream bug found+fixed   trainer.py shard-grouping (D-022)
4. GSQ output is measurable on Windows transforms + compressed-tensors, no vLLM, no GPU rental
5. No paid compute was ever required   the entire phase ran on the local 4070, free
6. Measurement rig is reusable         fixed harness, same corpus/windowing for every model
```

Point 4 is the one with lasting value: when this phase began, the eval path appeared to
require a rented Linux GPU. It did not.

## 9. Verdict

**NEGATIVE.** The project's thesis — that a learned-grid quantizer beats llama.cpp's hand-tuned
uniform K-quants on Phi-4-mini at matched size — **is not supported by this experiment.**

State of the individual claims:

| Claim | State |
|---|---|
| GSQ's quantizer runs on sm_89 | **PROVEN** |
| A Phi wrapper can drive GSQ over real Phi-4-mini weights | **PROVEN** |
| GSQ output is loadable and measurable on Windows | **PROVEN** |
| The export is faithful (weights correlate 0.89 with original) | **PROVEN** |
| GSQ produces genuine 2-bit weights | **PROVEN** |
| The serializer wastes 2x space on 4-bit containers | **PROVEN** |
| **GSQ 2-bit beats llama.cpp K-quants on Phi-4-mini** | **REFUTED** |
| A full-recipe (16x calibration) run would change the verdict | **UNKNOWN** |
| The method works on larger models as published | **NOT TESTED HERE** |

## 10. Recommended next steps, in order of value

1. **Report the `trainer.py` bug upstream** — the shard-write trigger is LLaMA-specific and
   fails silently. That is a genuine contribution regardless of this result.
2. **Test on a LLaMA-family model first** — GSQ has a shipped, exercised wrapper for LLaMA.
   If GSQ beats Q4_K_M there at matched size, the method is vindicated and Phi-4-mini is the
   wrong test bed. **This is the highest-value next experiment and it is nearly free.**
3. **Run the full calibration recipe** (~35 h local, or a bounded rental) before any final
   claim about the method.
4. **Fix the export**: quantize attention too, and emit true 2-bit containers.
5. **Only then** consider RCO allocation (Phase 11) — allocation tuning on top of a
   configuration that is not competitive would be measuring noise.

## 11. Artifacts

```text
E:/ternary-lab/gsq-run/config_phi4mini_p10b_2bit.yaml     corrected config
E:/ternary-lab/gsq-run/p10b-full.log                      32-layer run, exit 0
E:/ternary-lab/gsq-run/p10b-assembled-2bit/               assembled checkpoint (3.81 GB)
E:/ternary-lab/staging/unpack_check.py                    distinct-code test
E:/ternary-lab/staging/scale_audit.py                     scale sign audit
E:/ternary-lab/staging/fidelity_check.py                  dequant vs original
E:/ternary-lab/staging/measure_gsq.py                     fixed measurement harness
E:/Echo-DB/tools/GSQ/src/models/phi.py                    the wrapper
```
