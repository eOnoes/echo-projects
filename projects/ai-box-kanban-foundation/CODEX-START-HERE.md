# Codex Start Here

**Project:** `ai-box-kanban-foundation`
**Repository:** `eOnoes/echo-projects` (public, repository-content-only)
**Canonical goal:** `projects/ai-box-kanban-foundation/GOAL.md`
**Current status:** `READY_FOR_ROUND_3` — Round 2 independent DSH/Gemini report returned `PASS`; its process limitations are recorded in [`receipts/AIK-07B-ROUND-2.md`](receipts/AIK-07B-ROUND-2.md). Round 3 must be a distinct read-only audit of a fresh packet. No implementation is authorized.
Read `tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md` and both Round 1/2 audit reports and receipts. Do not implement source code. AIK-08 remains blocked until Round 3 and Eddie approves the final contract.

## Read order

1. `GOAL.md`
2. `CHARTER.md`
3. `AUTHORITY.md` and `PROJECT-OVERRIDES.md`
4. Current task in `TASKS.md`
5. `PLAN.md`, `HANDOFF.md`, and `EVIDENCE.md`

## Current assignment

Round 1 is complete with verdict `NEEDS_CHANGES`; R1-01–R1-05 are corrected and Echo-verified at commit `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`. Round 2 returned `PASS` via `google/gemini-3.5-flash-lite`; its verification limits are in `receipts/AIK-07B-ROUND-2.md`. Freeze a fresh packet for Round 3 using a distinct read-only auditor. Do not implement source or start a service; AIK-08 remains blocked until Round 3 and Eddie's final approval.

## Milestone and error rule

For project-packet tasks, update `TASKS.md`, append `EVIDENCE.md`, refresh `HANDOFF.md`, create a receipt, and provide Markdown plus HTML milestone reports. For a task assigned to a separate source repository, write only in the named branch/paths in its task handoff, return the exact commit and checks, and do not edit the packet from that source checkout. Echo updates the packet after independently verifying the remote diff. Never force-push or push directly to `main`. Stop on secrets, conflicting instructions, unrelated dirty files, live-service needs, or work outside the assigned paths.

**GitHub billing-safe mode is active:** no Actions, Codespaces, Packages, deployments, runners, releases, or paid features. Do not read or handle credentials. No source-code or live-model task is active; AIK-06 remains branch-only. AIK-07B is packet-only.
