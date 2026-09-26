# Quantization Pitfalls and Lessons

**Status:** LIVING DOCUMENT — append-only
**Scope:** all model-compression projects, not just phi-q
**Rule:** every new campaign appends findings here. Never overwrite an entry.

This file exists so the next model does not repeat the last model's mistakes.

---

## L-001 — Storage format is not a compression method

**Lesson:** `TQ1_0` (1.6875 bpw) and `TQ2_0` (2.0625 bpw) in llama.cpp are packing formats for
models that are **already ternary**. They exist to store TriLM/BitNet-style models efficiently.

**Pitfall:** applying them to a normally-trained FP16 model yields a correctly-sized file full
of garbage. The numbers look plausible until you evaluate.

**Check before use:** what was this format designed to hold? If the answer is "weights that are
already in this format," it is not a compression method.

**Found in:** phi-q, Phase 0.

---

## L-002 — Post-training quantization has a floor

**Lesson:** scalar PTQ plateaus at 3–4 bits per parameter and degrades sharply at or below 2 bits.
Strict ternary (~1.58 bit) is not reachable by any post-training method.

**Why:** the model was never given a chance to adapt to the constraint.

**Do instead:** choose the method from the target bit-width.

| Target | Method |
|---|---|
| 4-bit and above | Fine-tune, then PTQ |
| 2–3 bit | Fine-tune, then learned/gradient-guided quantizer |
| ~1.58 bit | Quantization inside training (QAT), or distillation from an FP16 teacher |

**Found in:** phi-q research audit.

---

## L-003 — Never compare numbers from two different harnesses

**Lesson:** the same checkpoint measured under two harnesses can produce very different absolute
perplexities. A real case from this project:

| Harness | FP16 PPL |
|---|---|
| llama.cpp `perplexity_v2` | 5.0863 |
| PyTorch / Transformers bf16 | 12.0361 |

Presented together without labels, that implied an 8,700× ternary collapse. The real figure
against the correct baseline was 3,670×.

**Pitfall:** absolute perplexity is harness-specific. Mixing harnesses silently invents a disaster
that did not happen — or hides one that did.

**Rule:** every number carries its harness. Never compare across harnesses. If both must appear,
show each as a ratio against its own baseline.

**Found in:** phi-q, Phase 0.

---

## L-004 — Reproduce the baseline before doing anything else

**Lesson:** recompute the recorded baseline from raw data if raw data exists. If it does not
reproduce, every downstream comparison is meaningless.

**Practice that worked:** recomputing the geometric mean from per-chunk perplexity values
recovered the recorded figures to four decimal places, confirming the harness was understood.

**Gate:** ±0.5% is a workable tolerance. Investigate 0.5–2%. Stop above 2%.

**Found in:** phi-q, Phase 0.

---

## L-005 — Hash against prior receipts to prove nothing drifted

**Lesson:** hash every artifact and compare against any hash recorded in earlier work. Three
independent matches proved the frozen baseline was untouched.

**Why it matters:** an unverified baseline invalidates every comparison built on it.

**Found in:** phi-q, Phase 0.

---

## L-006 — Search for uncatalogued prior work before re-deriving

**Lesson:** projects accumulate evidence folders that never make it into the index. In phi-q,
roughly 90 receipts existed in a `parity-harness/` folder — including a corrected KL handoff and
layer-0 through layer-31 divergence checkpoints — none of which were logged.

**Pitfall:** re-deriving solved work is the most expensive mistake available.

**Practice:** before starting a phase, sweep the evidence tree for prior art on that exact question.

**Found in:** phi-q, Phase 0.

---

## L-007 — Fine-tuning does not automatically make a model easier to quantize

**Lesson:** fine-tuning sharpens weights and enlarges activation outliers in a few channels.
Outliers are exactly what quantization destroys. Base models often quantize more gracefully than
heavily fine-tuned ones.

But a better starting model also produces a better endpoint. **The two effects oppose each other
and the net direction is not predictable from theory.**

**Measure it:**

```text
quantize base weights at fixed bits        -> delta_base
quantize fine-tuned weights at same bits   -> delta_tuned
compare the deltas
```

**Do not assume the answer in either direction.**

**Found in:** phi-q, from Eddie's question about train-first ordering.

---

## L-008 — Sensitivity maps may not transfer after fine-tuning

**Lesson:** sensitivity depends on the actual weight distribution and activation outliers, so a
map measured on base weights may not hold for fine-tuned weights.

**Rule:** map, train, re-map. Compare the two maps. Agreement builds confidence; divergence is a
finding worth recording.

**Found in:** phi-q.

---

## L-009 — Check hardware feasibility before planning training

**Lesson:** a 3.8B model at FP16 is ~7.6 GB of weights. 16-bit LoRA training needs well over
12 GB once activations and optimizer state are counted.

**Practice:** QLoRA — 4-bit base, adapters trained, merged back to FP16. Merging restores FP16
weights, so the quantization phases are unaffected.

**Found in:** phi-q pre-flight.

---

## L-010 — Quantization damage can detonate, not accumulate

**Lesson:** ternarizing one layer of a dense model cost ~3,670×. Eight layers cost ~1.7 × 10^9×.
Thirty-two layers cost ~3.8 × 10^10×.

**Pitfall:** a small-scale result does not predict the full-model result, and the error grows
much faster than linearly.

**Practice:** test at small scale first, and never extrapolate a single-layer result to a
full-model claim.

**Found in:** phi-q, recovered layer sweeps.

---

## L-011 — Never rent compute preemptively

**Lesson:** a local GPU that might work is worth trying before renting one that definitely will.

**Practice:** escalate only when a required run demonstrably cannot complete locally, and bring a
bounded proposal — card and VRAM, hourly rate, bounded hours, hard cost ceiling, exactly which
runs it unblocks, evidence plan, and stop trigger.

**Found in:** phi-q.

---

## L-012 — Perplexity alone is not a quality claim

**Lesson:** perplexity is a proxy. An artifact is not usable until it generates coherent text,
handles repetition, and terminates correctly.

**Practice:** pair every perplexity result with generation, repetition, and EOS checks before
calling a model deployable.

**Found in:** phi-q, adopted as a standing rule.

## L-016 — A model that needs a chat template will produce garbage without one

**Lesson:** evaluating an instruct model with raw completion — no system message, no chat
template, no single-turn framing — measures the prompt path, not the model. Both the quantized
*and* unquantized models fail, and the failure gets misattributed to quantization.

**Symptom:** repetitive fragments (`Mar`, `pr`, `Michael`, `rerere`, `510510510510`,
`samples samples ... blocks blocks`) instead of answers.

**Practice:** before any quantization comparison, validate that the **unquantized** baseline
answers correctly under a documented, explicit protocol. If the baseline cannot answer, the
comparison is void. Record the protocol alongside the numbers.

**Cost of missing it:** an entire prior campaign concluded that ternarization destroyed the
model, when the benchmark was defeating the unquantized model too.

**Found in:** phi-q, Phase 2; confirmed against the `v25` receipt from 2026-09-16.

## L-017 — A control that crosses runtimes proves nothing

**Lesson:** if the baseline runs on runtime A and the variant runs on runtime B, a difference
between them is confounded by the runtime. Either run both through the same binary, or do not
report the difference as a model result.

**Practice:** when a control cannot be obtained (e.g. the second runtime cannot load the
baseline model), report the comparison as **unresolved** rather than reporting the variant's
result as a finding.

**Found in:** phi-q, Phase 2 — Prism runtime produced repetition on both ternary artifacts, but
could not load the stock F16 baseline, so the comparison was left unresolved instead of claimed.

## L-018 — A well-tuned default is a real competitor, not a baseline

**Lesson:** before designing a clever allocation, measure what the existing default already
achieves. Hand-tuned heuristics encode a lot of empirical work, and a data-driven design that
loses to the default has not earned its complexity.

**Found in:** phi-q, Phase 4b — a layer-graded allocation derived from a real sensitivity map lost
to llama.cpp's own `Q4_K_M` mix (`5.3961` vs `5.2206`), and also lost to plain uniform `Q4_K_S`
at matched size. The default won for free.

**Practice:** "our method beat the baseline" must mean a *tuned* baseline, not a naive one.

---

# Environment lessons (Windows builds)

These cost real time and are not specific to quantization.

## L-013 — Two Visual Studio installs will silently break a link

**Lesson:** a CMake cache pins an exact compiler path. If the machine has more than one Visual
Studio install and you source the *other* one's `vcvars64.bat`, compilation succeeds and linking
fails with unresolved `__std_*` symbols — the header STL is newer than the lib STL being linked.

**Symptom:**

```text
unicode.cpp.obj : error LNK2019: unresolved external symbol __std_regex_transform_primary_char
bin\llama.dll : fatal error LNK1120: 2 unresolved externals
```

**Fix:** read `CMAKE_CXX_COMPILER` from `CMakeCache.txt`, then source the `vcvars64.bat` from
*that same* Visual Studio install.

**Pitfall:** blaming the compiler cache. `ccache` was a plausible story and the wrong answer —
disabling it changed nothing. Two attempts were spent on that theory.

**Found in:** phi-q, Phase 1.

## L-014 — Missing SDK headers mean vcvars was never sourced

**Symptom:**

```text
fatal error C1083: Cannot open include file: 'stdbool.h'
fatal error C1083: Cannot open include file: 'windows.h'
```

**Cause:** `cl.exe` was invoked without the Visual Studio developer environment. The compiler is
on `PATH` but the include and lib paths are not.

**Found in:** phi-q, Phase 1.

## L-015 — In git-bash, `cmd //c` opens an interactive shell instead of running the command

**Lesson:** under MSYS/git-bash on this host, `cmd //c "script.bat"` prints the cmd banner and
drops to a prompt — the batch file never executes. Use:

```bash
MSYS_NO_PATHCONV=1 cmd /c "C:\path\to\script.bat"
```

**Pitfall:** the failure looks like the script exited immediately with no output, which reads as a
script bug rather than an invocation bug.

**Found in:** phi-q, Phase 1.

---

## L-019 — A substring test against a FUSED tensor name silently no-ops

**Phase:** phi-q Phase 9.

GSQ's trainer decided whether to write an MLP shard with:

```python
if "gate_proj" in tensor_name:
```

Phi-4-mini's MLP is a fused `gate_up_proj`. **`"gate_proj"` is not a substring of
`"gate_up_proj"`** — after `gate_` comes `u`, not `p`. The branch never fired, no shard was
written, and the run died later on `FileNotFoundError` for a file that was never created.

Two properties made this expensive to diagnose:

1. **It failed silently.** The pipeline logged `Finished writing layer checkpoint` anyway,
   because that message is emitted unconditionally.
2. **The error surfaced at the wrong place.** The failure appeared as a missing file at
   *load* time, pointing at the filesystem rather than at the naming assumption that caused it.

**Rule:** when matching tensor/module names by substring, always ask what the *fused* form of that
name looks like. `q_proj`/`k_proj` → `qkv_proj`. `gate_proj`/`up_proj` → `gate_up_proj`. A
substring test against a fused name matches nothing and raises nothing.

**Rule:** prefer structural grouping (walk up to the parent module) over matching literal leaf
names. Grouping by parent is equivalent for the architectures the code already supports and
correct for ones it does not.

## L-020 — Verify a "no change needed" claim by searching EVERY file, not the ones you remember

**Phase:** phi-q Phase 9.

Before starting the port, I searched `base.py` and `main.py` for hard-coded LLaMA projection names,
found two sites, and concluded both were unreachable — so the port would be purely additive. **The
conclusion about those two sites was correct. The conclusion about the port was wrong**, because
the third and only-reachable site was in `trainer.py`, which I never searched.

**Rule:** a claim of the form "X requires no change to file Y" is only as good as the search that
supported it. Search the whole tree (`grep -rn <pattern> --include=*.py .`), not the files you
happen to associate with the problem.

## L-021 — `trust_remote_code=True` lets a model folder silently override transformers' own implementation

**Phase:** phi-q Phase 9.

Loading Phi-4-mini failed with `ImportError: cannot import name 'LossKwargs' from
'transformers.utils'` — raised from a `modeling_phi3.py` inside the *model directory*.

The model folder shipped `modeling_phi3.py` + `configuration_phi3.py` and declared them in
`config.json`'s `auto_map`. Combined with `trust_remote_code=True` in the loader, transformers
resolved to **the vendored copy** rather than its own built-in `Phi3ForCausalLM`. The vendored file
targeted an older transformers.

**Rule:** when a model fails to import with a symbol-version error, the failing file's *path* is the
diagnosis. If it points into `transformers_modules/` or the model folder, the model is supplying
its own code and you are not running transformers' implementation at all.

**Fix pattern:** derive a sibling model directory with `auto_map` stripped and the vendored code
omitted. Prefer that over setting `trust_remote_code=False` in shared loader code, which would
break any architecture that genuinely needs remote code.
