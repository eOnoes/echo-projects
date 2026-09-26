# Phi-4-mini Quant Task Ledger

## Current task

**Phase 8 — Toolchain smoke test (GSQ).**

**Status:** IN_PROGRESS
**Owner:** Echo
**Approval required:** No (local, free)
**Evidence required:** a 2-layer quantization run completes on sm_89; output shards written;
peak VRAM and runtime recorded.

**Why this is the current task:** Phase 7 proved the stack *installs*. Installing is not running.
This phase proves GSQ actually quantizes something on this Ada card — the gate that must pass
before writing a Phi wrapper.

---

## Completed

### Phase 0 — COMPLETE 2026-09-25
- [x] Pin and record the model revision
- [x] Hash all existing quantization artifacts
- [x] Transcribe prior baseline numbers into `EVIDENCE.md`
- [x] Receipt: `receipts/P0-freeze-20260925.md`

### Phase 1 — COMPLETE 2026-09-25
- [x] Corpus identified and hash-recorded (README, 7,994 tokens)
- [x] Harness algorithm read from source
- [x] Binary built from local source, hashed, versioned
- [x] FP16 baseline measured: 5.0553
- [x] Q4_K_M baseline measured: 5.2206
- [x] Deviation from historical explained (−0.61% / −1.18%)
- [x] Receipt: `receipts/P1-measurement-rig-20260925.md`

### Phase 2 — CLOSED WITH UNRESOLVED ITEM 2026-09-25
- [x] Review the uncatalogued parity-harness evidence (851 files)
- [x] Two ternary efforts identified (Line A / Line B)
- [x] Line B's failure traced to a broken benchmark, not the model
- [x] Prompt protocol fixed: F16 answers all 4 fixtures correctly under a chat template
- [x] Cross-ruler error caught before reporting (W-002 class)
- [x] Capped: no clean control available; Prism fork cannot load stock F16
- [x] Receipt: `receipts/P2-evidence-reconciliation-20260925.md`

### Phase 3 — COMPLETE 2026-09-25
- [x] Sensitivity map over all 128 quantizable tensors
- [x] Group-size sweep (H-002 test) — H-002 refuted on weight-space error
- [x] Receipt: `receipts/P3-sensitivity-map-20260925.md`
- *Note: the "learned quantizer" objective was NOT met — fixed llama.cpp formats only.*

### Phase 4 — COMPLETE 2026-09-25
- [x] Uniform bit-budget curve (Q2_K → Q8_0), 6 points + FP16 reference
- [x] Receipt: `receipts/P4-uniform-curve-20260925.md`

### Phase 4b — COMPLETE (NEGATIVE) 2026-09-25
- [x] Heterogeneous allocation built from the sensitivity map, size-matched
- [x] Measured: 5.3961 vs control 5.3568 — **LOSES by 0.73%**
- [x] llama.cpp's own Q4_K_M beats both
- [x] Parked with a measured reason (D-020)
- [x] Receipt: `receipts/P4b-heterogeneous-test-20260925.md`

### Phase 5 stage 1 — COMPLETE 2026-09-25
- [x] Isolated QAD venv built; CUDA torch fixed (PyPI gives a CPU-only build on Windows)
- [x] Toolchain verified: model loads, fake-quant runs, KL computes
- [x] **Single-pass teacher+student does NOT fit** (needs est. 12,574 MiB; 3,786 MiB free)
- [x] Two-pass design identified that does fit
- [x] Receipt: `receipts/P5-smoke-test-20260925.md`

### Phase 6 — COMPLETE 2026-09-25
- [x] GSQ + RCO investigated; adopted as the method (D-022)
- [x] Phase 4b failure root-caused as instrument-limited, not idea-limited
- [x] Receipt: `receipts/P6-gsq-rco-investigation-20260925.md`

### Phase 6b — COMPLETE 2026-09-25
- [x] GSQ (`03fc164`) and RCO (`9a1e09c`) cloned to `E:\Echo-DB\tools\`
- [x] Wrapper contract read: 7 abstract methods + 2 attributes
- [x] Phi-4-mini checkpoint index read; 8 of 10 module paths match the LLaMA template
- [x] Three real deltas identified (fused qkv, fused gate_up, tied lm_head)
- [x] Blocker re-scoped to the Windows environment question
- [x] Receipt: `receipts/P6b-wrapper-feasibility-20260925.md`

### Phase 7 — COMPLETE 2026-09-25
- [x] Isolated venv created (`E:/ternary-lab/gsq-env`, Python 3.12.13)
- [x] Dependency subset installed, excluding vllm / ray / lm-eval / lighteval / humming-kernels
- [x] torch 2.11.0+cu126 reports CUDA True on Windows
- [x] Core imports resolve; GSQ's own source imports cleanly
- [x] Phi3ForCausalLM confirmed available in transformers 5.17.0
- [x] **No escalation triggered — GSQ runs locally and free**
- [x] Receipt: `receipts/P7-environment-20260925.md`

---

## Queue

### Phase 8 — Toolchain smoke test  *(CURRENT)*
- [ ] Run GSQ's 2-layer dry run on a supported architecture (e.g. Qwen3-0.6B)
- [ ] Confirm output shards are written and loadable
- [ ] Record peak VRAM and runtime
- [ ] Project the full Phi-4-mini run from the measured rate

### Phase 9 — Phi-4-mini wrapper
- [ ] Implement `PhiWrapper` (7 abstract methods)
- [ ] Decide and document how fused `qkv_proj` is treated
- [ ] Guard the tied `lm_head`
- [ ] Register a `'phi'` branch in `get_model_wrapper()`
- [ ] Verify it loads and reports layer/parameter counts matching the checkpoint

### Phase 10 — GSQ on Phi-4-mini  *(the experiment)*
- [ ] Run at 3-bit
- [ ] Run at 2-bit
- [ ] Measure perplexity on the Phase 1 rig, same corpus, same binary
- [ ] Compare against FP16 5.0553 and against the uniform curve at matched size
- [ ] Label PROVEN / INFERRED / UNRESOLVED — a negative result is a valid result

### Phase 11 — RCO allocation  *(conditional on Phase 10)*
- [ ] Run RCO at a stated size budget
- [ ] Inspect the allocation dump against the Phase 3 sensitivity map
- [ ] Measure against uniform GSQ at matched size

### Phase 12 — Export and runtime validation
- [ ] Export GGUF in a standard format
- [ ] Load in the hashed llama.cpp from Phase 1
- [ ] Bounded generation; record repetition and EOS behavior

### Phase 13 — Closure
- [ ] Evidence register complete
- [ ] Retrospective written (required by `CLOSURE.md`)
- [ ] Release decision stated

---

## Blocked / deferred

| Item | Blocked on |
|---|---|
| `qwen36-ternary` doctrine packet | Not created — deprioritized behind phi-q |
| `qwen-runtime` CUDA 13 D3 continuation | H200 capacity unavailable |
| `templates/project-packet/` | Still empty; blocks clean project #2 creation |

---

## Rules

- Only one task may be `IN_PROGRESS`.
- Every completed task requires evidence.
- A blocked task must state exactly what is needed.
- A successful command is not proof of a successful outcome.
- **No silent substitution.** If a tool cannot run, say so and escalate — never swap in an
  approximation and present it under the original method's name.
