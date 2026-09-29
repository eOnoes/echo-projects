# Codex Start Here

**Project:** `ai-box-kanban-foundation`
**Repository:** `eOnoes/echo-projects` (public, repository-content-only)
**Canonical goal:** `projects/ai-box-kanban-foundation/GOAL.md`
**Current status:** `READY_FOR_OWNER_REVIEW` — Round 1 corrections are verified; Rounds 2–3 DSH/Gemini returned `PASS` with process/report limitations recorded in the [Round 2 receipt](receipts/AIK-07B-ROUND-2.md) and [Round 3 receipt](receipts/AIK-07B-ROUND-3.md). Eddie's final approval remains; AIK-08 is blocked.
Read `tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md` and the Round 1–3 reports/receipts. No implementation is authorized. AIK-08 remains blocked until Eddie approves the final contract and a separate implementation task is authorized.

## Read order

1. `GOAL.md`
2. `CHARTER.md`
3. `AUTHORITY.md` and `PROJECT-OVERRIDES.md`
4. Current task in `TASKS.md`
5. `PLAN.md`, `HANDOFF.md`, and `EVIDENCE.md`

## Current assignment

Round 1 is complete with verdict `NEEDS_CHANGES`; R1-01–R1-05 are corrected and Echo-verified at commit `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`. Rounds 2–3 returned `PASS`; process/report limitations are recorded in their receipts. Three audit/refinement rounds are recorded. Do not implement source or start a service; AIK-08 remains blocked pending Eddie's final approval and separate authorization.

## Milestone and error rule

For project-packet tasks, update `TASKS.md`, append `EVIDENCE.md`, refresh `HANDOFF.md`, create a receipt, and provide Markdown plus HTML milestone reports. For a task assigned to a separate source repository, write only in the named branch/paths in its task handoff, return the exact commit and checks, and do not edit the packet from that source checkout. Echo updates the packet after independently verifying the remote diff. Never force-push or push directly to `main`. Stop on secrets, conflicting instructions, unrelated dirty files, live-service needs, or work outside the assigned paths.

**GitHub billing-safe mode is active:** no Actions, Codespaces, Packages, deployments, runners, releases, or paid features. Do not read or handle credentials. No source-code or live-model task is active; AIK-06 remains branch-only. AIK-07B is packet-only.
