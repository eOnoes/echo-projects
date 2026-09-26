# Reporting Standard

**Status:** HARD-LOCK DRAFT
**Version:** 1.0-draft

## Required formats

Every milestone or checkpoint report must be generated as:

- Human-readable Markdown.
- Progressive-disclosure HTML.

## HTML layout

The first screen must contain:

1. Project name and ID.
2. Status.
3. One-sentence result.
4. TL;DR bullets.
5. Current blocker or next action.

The remaining sections provide drill-down detail:

- What changed.
- What was verified.
- Evidence and receipts.
- Decisions.
- Scope check.
- Risks and limitations.
- Agent handoff.
- Technical logs and commands.

## Required status values

`DRAFT`, `READY`, `READY_FOR_REVIEW`, `RUNNING`, `PAUSED`, `BLOCKED`, `NEEDS_APPROVAL`, `FAILED_BOUNDED`, `COMPLETE`, `COMPLETE_WITH_LIMITATIONS`, `CANCELLED`.

`READY_FOR_REVIEW` means the artifact or packet is complete and awaiting human judgement.
`NEEDS_APPROVAL` means work is blocked on an explicit go/no-go decision.

## Required checkpoint fields

```text
Project ID
Goal file
Phase
Task ID
Manager
Executor
Status
TL;DR
What changed
What was verified
Evidence
Remaining work
Next safe action
Scope check
```

## Truthfulness rule

Reports must distinguish proven results from inference, unresolved questions, and bounded failures. A successful command is not by itself proof of a successful project outcome.

## Override rule

A project may add reporting fields with `*` in `PROJECT-OVERRIDES.md`. The TL;DR, status, evidence, and scope sections remain mandatory.
