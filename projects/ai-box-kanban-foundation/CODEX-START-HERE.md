# Codex Start Here

**Project:** `ai-box-kanban-foundation`
**Repository:** `eOnoes/echo-projects` (public, repository-content-only)
**Canonical goal:** `projects/ai-box-kanban-foundation/GOAL.md`
**Current status:** `READY_FOR_REVIEW` — AIK-07B integrates D-1–D-5 in the packet; Echo review and three separate audit rounds remain.
Read `tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md`. Do not implement source code. AIK-08 remains blocked until three distinct audit/refinement rounds pass and Eddie approves the final contract.

## Read order

1. `GOAL.md`
2. `CHARTER.md`
3. `AUTHORITY.md` and `PROJECT-OVERRIDES.md`
4. Current task in `TASKS.md`
5. `PLAN.md`, `HANDOFF.md`, and `EVIDENCE.md`

## Current assignment

AIK-07B contract integration is ready for Echo review. All five owner directions are in `receipts/AIK-07B-OWNER-DECISIONS.md`; open technical choices remain gated. The manager launches each audit round separately. No audit or implementation occurred in this task.

## Milestone and error rule

For project-packet tasks, update `TASKS.md`, append `EVIDENCE.md`, refresh `HANDOFF.md`, create a receipt, and provide Markdown plus HTML milestone reports. For a task assigned to a separate source repository, write only in the named branch/paths in its task handoff, return the exact commit and checks, and do not edit the packet from that source checkout. Echo updates the packet after independently verifying the remote diff. Never force-push or push directly to `main`. Stop on secrets, conflicting instructions, unrelated dirty files, live-service needs, or work outside the assigned paths.

**GitHub billing-safe mode is active:** no Actions, Codespaces, Packages, deployments, runners, releases, or paid features. Do not read or handle credentials. No source-code or live-model task is active; AIK-06 remains branch-only. AIK-07B is packet-only.
