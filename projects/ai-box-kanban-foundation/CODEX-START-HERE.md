# Codex Start Here

**Project:** `ai-box-kanban-foundation`
**Repository:** `eOnoes/echo-projects` (public, repository-content-only)
**Canonical goal:** `projects/ai-box-kanban-foundation/GOAL.md`
**Current status:** `READY` — Round 1 Kimi K3 audit returned `NEEDS_CHANGES`; Codex remediation is prepared. Two distinct audit/refinement rounds and owner review remain.
Read `tasks/AIK-07B-R1-REMEDIATION.md`. Do not implement source code. AIK-08 remains blocked until all three rounds pass and Eddie approves the final contract.

## Read order

1. `GOAL.md`
2. `CHARTER.md`
3. `AUTHORITY.md` and `PROJECT-OVERRIDES.md`
4. Current task in `TASKS.md`
5. `PLAN.md`, `HANDOFF.md`, and `EVIDENCE.md`

## Current assignment

Round 1 is complete with verdict `NEEDS_CHANGES`. Read `tasks/AIK-07B-R1-REMEDIATION.md`, its linked Kimi audit report, and the provenance receipt. This is a packet-only correction task. Do not start Round 2, modify source, or implement a service; Echo will verify before the next audit.

## Milestone and error rule

For project-packet tasks, update `TASKS.md`, append `EVIDENCE.md`, refresh `HANDOFF.md`, create a receipt, and provide Markdown plus HTML milestone reports. For a task assigned to a separate source repository, write only in the named branch/paths in its task handoff, return the exact commit and checks, and do not edit the packet from that source checkout. Echo updates the packet after independently verifying the remote diff. Never force-push or push directly to `main`. Stop on secrets, conflicting instructions, unrelated dirty files, live-service needs, or work outside the assigned paths.

**GitHub billing-safe mode is active:** no Actions, Codespaces, Packages, deployments, runners, releases, or paid features. Do not read or handle credentials. No source-code or live-model task is active; AIK-06 remains branch-only. AIK-07B is packet-only.
