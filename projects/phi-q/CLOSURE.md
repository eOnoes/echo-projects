# Phi-4-mini Quant Closure Report

## Final status

UNOPENED

Valid final statuses:

- COMPLETE
- COMPLETE_WITH_LIMITATIONS
- PAUSED
- BLOCKED
- CANCELLED

## Acceptance checklist

- [ ] Baselines reproduced within tolerance
- [ ] Ternary failure reproduced and classified
- [ ] Learned quantization measured at 3-bit and 2-bit
- [ ] Mixed-precision allocation beats uniform at equal average bits
- [ ] Fine-tune-then-quantize ordering measured
- [ ] GGUF artifact exported
- [ ] Artifact loads and generates in a local runtime
- [ ] Evidence register complete
- [ ] Limitations documented

## Known limitations

Not yet recorded. To be completed during Phase 8.

## Retrospective (required)

Every closure must answer:

- Which decisions were made, and what were they based on?
- Which turned out `RIGHT`, `WRONG`, `MIXED`, or `LUCKY`?
- What did the wrong calls cost in time, compute, or credibility?
- What would we do differently on the next model?
- Which new entries were appended to `doctrine/PITFALLS-AND-LESSONS.md`?
- Which new entries were appended to `doctrine/DECISION-JOURNAL.md`?

A project is not closed until its retrospective is recorded.

## Final evidence

To be completed only after Phase 7 runtime validation.

## Release decision

Not ready. No artifact may be described as usable until it generates coherently.
