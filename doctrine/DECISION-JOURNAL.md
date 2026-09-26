# Decision Journal

**Status:** LIVING DOCUMENT — append-only
**Scope:** all projects
**Purpose:** record the decisions that materially shaped a project, why we made them, and
whether they turned out right. So the next project makes the same good calls faster and
does not repeat the bad ones.

---

## How to use this

1. **Record a decision when it is made** — before the outcome is known. A decision logged only
   after it succeeded teaches nothing about judgement.
2. **State the basis** — evidence, research, intuition, or the user's call. Different bases
   deserve different confidence.
3. **Come back and set the verdict** when the outcome is knowable.
4. **Never rewrite an entry to look smarter.** A wrong call recorded honestly is worth more
   than a right call recorded retroactively.

### Verdict vocabulary

| Verdict | Meaning |
|---|---|
| `RIGHT` | The decision produced the intended result |
| `WRONG` | The decision produced a worse result than the alternative |
| `MIXED` | Partially right — some consequences good, some not |
| `PENDING` | Not yet knowable |
| `LUCKY` | Right outcome, but the reasoning was not sound. Repeat it and you may lose. |

`LUCKY` matters most. A right answer from bad reasoning is a trap, not a lesson.

---

## Decision index

| ID | Date | Project | Decision | Basis | Verdict |
|---|---|---|---|---|---|
| D-001 | 2026-09-25 | doctrine | Build an agent-agnostic project doctrine with per-project goal packets | Eddie's direction | PENDING |
| D-002 | 2026-09-25 | phi-q | Reopen the Phi-4-mini work as a mixed-precision project | Prior failure + research | RIGHT |
| D-003 | 2026-09-25 | phi-q | Exclude strict ternary from scope | Research audit | RIGHT |
| D-004 | 2026-09-25 | phi-q | Correct Phase 5 from 16-bit LoRA to QLoRA | Hardware measurement | RIGHT |
| D-005 | 2026-09-25 | phi-q | Adopt map → train → re-map instead of a single ordering | Eddie's challenge | PENDING |
| D-006 | 2026-09-25 | phi-q | Do not rent GPU capacity preemptively | Cost discipline | PENDING |
| D-007 | 2026-09-25 | phi-q | Retract the cross-harness ternary comparison | Phase 0 evidence | RIGHT |
| D-008 | 2026-09-25 | phi-q | Split harnesses: llama.cpp for GGUF, PyTorch for weights | Harness incompatibility | PENDING |
| D-009 | 2026-09-25 | doctrine | Make the lessons record append-only rather than editable | Eddie's request | PENDING |
| D-010 | 2026-09-25 | phi-q | Assume the "README corpus" is the model README and verify by chunk math | Milestone wording + arithmetic check | RIGHT |
| D-011 | 2026-09-25 | phi-q | Build llama-perplexity from local source rather than accept the available binary's behavior | Available binary overrides n_ctx from stride | RIGHT |
| D-012 | 2026-09-25 | phi-q | Adopt our own hashed baseline instead of treating the historical 5.0863 as the gate | Original binary unavailable; gate unachievable as written | PENDING |
| D-013 | 2026-09-25 | phi-q | Rebuild with VS 18 vcvars after two failed build attempts | Cache pinned VS 18; VS 2022 STL was linked | RIGHT |

---

## Wrong calls and recoveries

Recorded so they are visible, not buried. Each entry states what happened, what it cost, and
how it was caught.

| ID | Decision | What went wrong | How it was caught | Cost |
|---|---|---|---|---|
| W-001 | Quantize FP16 Phi-4-mini directly to `TQ1_0`/`TQ2_0` | Those are packing formats for already-ternary models. The result was a correctly-sized, unusable model. | Later research identified what the formats were designed for | Prior campaign produced no usable model |
| W-002 | Present GGUF and PyTorch perplexities side by side | Invented an 8,700× collapse that did not exist. Undermined confidence in real findings. | Phase 0 recomputed both baselines and spotted two different FP16 values | Reporting credibility; required a retraction |
| W-003 | Conclude the GitHub keyring was unreachable without re-testing | Stated a blocker that did not exist and asked Eddie for access he had already granted | Re-ran the check after his pushback | Wasted his time; avoidable |
| W-004 | Flag 1.4 GB of VRAM use as a stuck process | It was the Windows desktop compositor and open apps. Normal overhead. | Checked the process list | Small — corrected in the same turn |

**Pattern across W-001 and W-002:** both came from *not verifying what a thing actually was*
before building on it. Both were caught by going back to primary evidence.

---

## Detail entries

### D-002 — Reopen Phi-4-mini as a mixed-precision project

**Date:** 2026-09-25
**Decision:** Rather than abandoning the ternary work or repeating it, reframe it as a
mixed-precision recovery project.
**Basis:** Research audit showing PTQ has a 3–4 bit floor and that salience-driven mixed precision
is a shipped technique.
**Alternatives rejected:** Repeat the ternary attempt (research says it cannot work post hoc);
abandon the model entirely (the failure evidence was still valuable).
**Verdict:** RIGHT — the approach is supported by published work and the prior failure is now
explained rather than mysterious.

### D-005 — Map, train, re-map

**Date:** 2026-09-25
**Decision:** Measure sensitivity on base weights, fine-tune, then re-measure, instead of picking
one ordering.
**Basis:** Eddie's challenge, plus the observation that fine-tuning both improves the starting
point and worsens outlier structure. Two opposing effects.
**Alternatives rejected:** Map first only (map may not transfer); train first only (spends the
training budget before knowing whether it helps).
**Verdict:** PENDING — Phase 5b settles it.

### D-007 — Retract the cross-harness comparison

**Date:** 2026-09-25
**Decision:** Record the prior milestone's headline comparison as retracted rather than quietly
re-scoping it.
**Basis:** Two harnesses with FP16 baselines of 5.0863 and 12.0361 cannot be compared.
**Alternatives rejected:** Silently restate the numbers (leaves a known-false claim in the record).
**Verdict:** RIGHT — an honest retraction is cheaper than a comparison built on a bad number.

### W-003 — Concluding a blocker without re-testing

**Date:** 2026-09-25
**What happened:** Reported that the GitHub CLI credential was unavailable to this session and
asked Eddie to supply access. It was available. The check had been run before he finished logging
in, and it was never repeated.
**Fix applied:** Re-run a state check immediately before reporting it as a blocker. A blocker
claim is a claim and needs the same evidence standard as any other.
**Verdict:** WRONG — avoidable, and it cost the user's time.

---

## Retrospective index

Every project closure produces a retrospective that feeds this file.

| Project | Closure date | Retrospective | Decisions reviewed |
|---|---|---|---|
| phi-q | not closed | — | — |

---

## Standing rules derived from this journal

These were promoted from individual entries after the pattern repeated.

1. **Verify what a thing is before building on it.** W-001 and W-002 both came from skipping this.
2. **Re-test a state immediately before reporting it as a blocker.** From W-003.
3. **Log the decision before the outcome is known.** Otherwise the journal becomes marketing.
4. **Label `LUCKY` when the outcome was good but the reasoning was not.** Otherwise it gets copied.
