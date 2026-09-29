# AIK-07 — Owner decision brief for Kanban D-1–D-5

**Project:** `ai-box-kanban-foundation`
**Task:** `AIK-07-KANBAN-DECISION-BRIEF`
**Status:** `COMPLETE_WITH_LIMITATIONS` — recommendation brief delivered; D-1–D-5 remain Eddie's pending choices.
**Repository:** `eOnoes/echo-projects`, branch `codex/AIK-07-KANBAN-DECISION-BRIEF`
**Base:** `e2a419a9bf164707e7c7a31996219bfa49103a67`
**Executor:** Codex, `gpt-6-sol`, medium reasoning

## Goal

Turn the five existing Kanban decision gates into a concise, evidence-backed owner brief that makes the next product slice easy to authorize.

## Allowed inputs and outputs

Read only the existing `ai-box-kanban-foundation` project packet, especially `deliverables/KANBAN-MVP.md`, `reports/KANBAN-ACCEPTANCE-MATRIX.md`, `CHARTER.md`, `PLAN.md`, `TASKS.md`, `AUTHORITY.md`, `EVIDENCE.md`, and `HANDOFF.md`.

Write only these packet paths:
- `reports/KANBAN-DECISION-BRIEF.md`
- `reports/KANBAN-DECISION-BRIEF.html`
- `receipts/AIK-07-DECISION-BRIEF.md`
- task ledger/evidence/handoff entries directly required to record this report.

## Required content

For each D-1–D-5, provide: the exact owner choice, packet evidence, viable options supported by that evidence, a low-complexity recommendation with tradeoffs, unresolved information, and what becomes unblocked after the decision. Lead with a phone-readable TL;DR and give a minimal decision set that unlocks the first testable Kanban slice.

Keep all five decisions explicitly `PENDING` until Eddie answers. Recommendations are not decisions. D-4 must include idempotency-outcome retention; D-5 must cover snapshot/feed and priority/source-reference conventions.

## Stop rules

- No edits to the existing Kanban contract, acceptance matrix, policies, or owner decisions.
- Do not inspect or modify Onoes-Mind, Relay, Control, Inference Control, or hardware; use only packet evidence.
- Do not invent a backend, identity provider, numeric retention period, API protocol, URL, deployment, budget, or security claim. Mark unavailable facts as unknown.
- No implementation, service/network access, credentials, deployment, CI, or code changes.
- Stop and report if the packet does not support a recommendation; do not expand research.

## Acceptance

1. Each D-1–D-5 is accurately restated and remains pending.
2. Recommendations are tied to cited packet evidence and distinguish fact, option, recommendation, and owner decision.
3. The proposed first slice is testable and respects the single-authoritative-store and Control/Mind/Relay boundaries.
4. Markdown and HTML render, links resolve, no secrets/private paths appear, `git diff --check` passes.
5. Commit/push only `codex/AIK-07-KANBAN-DECISION-BRIEF`; report exact commit and changed-file list.
