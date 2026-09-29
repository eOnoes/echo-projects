# Goal: ai-box-kanban-foundation

**Project ID:** `ai-box-kanban-foundation`
**Full name:** AI Box and Shared Task Board Foundation
**Status:** `RUNNING`
**Owner:** Eddie
**Manager:** Echo
**Current executor:** Eddie — final owner review. Three audit/refinement rounds are recorded; Round 1 findings were corrected and Rounds 2–3 returned `PASS` with limitations documented. No implementation or runtime work has occurred.
**Created:** 2026-09-28

## Project keyword

```text
Goal: ai-box-kanban-foundation
```

## One-sentence goal

Progress the AI-box dashboard and shared task board from verified boundaries to a functioning, testable local product slice, then iterate through the Kanban/Control software work and hardware boundary in dependency order without bypassing owner decisions or live-operation gates.

## Why this exists

The audits found that Mind’s current `work_items` and `stage_executions` are internal execution records, not a qualified general-purpose shared board; Relay is a candidate UI, not yet a shared task service; and Control is intended for oversight/approval rather than execution authority. The separate model dashboard, now named **Onoes-Inference-Control**, has a loopback-only local-mock prototype for test/trial; its live security gate remains open. The project now advances through bounded product slices and explicit owner/hardware gates.

## Definition of done

1. The Control authority boundary is verified and the named documentation changes are recorded.
2. The Inference Control local-mock slice is usable for bounded UI/workflow testing; this does not qualify live mode.
3. The Kanban contract is reviewed and Eddie's D-1–D-5 policy directions are integrated before selecting implementation details; complete three distinct audit/refinement rounds and obtain final owner approval before building.
4. Relay and Control use one reviewed task service and do not create competing authority or execution paths.
5. The Control Chat navigation crash is repaired and regression-tested without guessing a Relay target.
6. The separate Mind effort supplies its own G0–G7 evidence; no duplicate inspection or integration is implied.
7. Hardware/topology is measured after delivery; the Proxmox boundary is chosen from evidence, with installation and host changes separately authorized.
8. Each milestone has verified tests, scope, limitations, and a recorded next action. A working prototype need not be 100% complete or production-ready.

## Explicit non-goals

- Do not build or deploy a shared Kanban service, Relay adapter, or Control task view until the revised contract passes three distinct audit/refinement rounds and Eddie approves it. The approved private-trial directions do not authorize public exposure or production retention.
- Do not repurpose Mind’s internal execution ledger as the user-facing task store.
- Do not authorize Control to execute work from Mind data.
- Do not enable real model/upstream operations or expose the dashboard. The owner-authorized loopback-only mock prototype is the sole current application-code exception; live-mode fixes require separate scope and review.
- Do not rotate, inspect, copy, print, or publish any credential.
- Do not inspect the dirty Mind workspace or alter unrelated working trees.
- Do not install Proxmox, change OS/BIOS/GPU settings, buy hardware, or start paid resources.
- Do not use GitHub Actions, Codespaces, Packages, deployments, runners, or other potentially billable features.

## Owner-estimated AI-box schedule (recorded 2026-09-28)

- All parts expected by **October 6, 2026**.
- Unit assembled by **October 11, 2026**.
- Boot/load testing targeted for **October 12, 2026**.

Eddie called these estimates, not commitments. They do not authorize purchases, OS/BIOS changes, service exposure, or live testing before the applicable gates pass.

## Current phase and task

**Current phase:** Item 4 — three review/refinement rounds recorded; Eddie's final contract approval is next.
**Current task:** `AIK-07B` — Eddie final contract review; no source/runtime work.
**Current status:** `READY_FOR_OWNER_REVIEW` — Round 1 findings corrected; Rounds 2–3 returned `PASS` with report/process limitations disclosed in their receipts. AIK-08 remains blocked until Eddie approves.

## Next safe action

Rounds 2–3 returned `PASS` with process/report limitations; both reports and receipts are linked in the packet map. The next safe action is Eddie's final contract review; no build is authorized.

## Decision log

- The project scope is expanded by Eddie through backlog items 4–10, staged by dependency; all D-1–D-5 policy directions are recorded. Contract audits/final approval, the separate Mind G0–G7 effort, and hardware evidence remain gates.
- The local-mock prototype is a bounded product slice only; live security, remote use, and real model operations remain gated.
- Mind remains the memory and internal execution-record system. Shared Kanban is a separate contract/store decision.
- AIK-06 is separately approved for Chat navigation only; route targets are evidence-based and source changes stay on its task branch.
- The existing shared agent credential will not be rotated by this project. No credential material is allowed in this public project packet.

## Packet map

- [`CHARTER.md`](CHARTER.md) — scope and acceptance.
- [`AUTHORITY.md`](AUTHORITY.md) — Echo/Codex permissions and error handling.
- [`PLAN.md`](PLAN.md) — phases, gates, and escalation route.
- [`TASKS.md`](TASKS.md) — current task and queue.
- [`EVIDENCE.md`](EVIDENCE.md) — claims, labels, and pointers.
- [`HANDOFF.md`](HANDOFF.md) — current resume point.
- [`CLOSURE.md`](CLOSURE.md) — closure gate.
- [`PROJECT-OVERRIDES.md`](PROJECT-OVERRIDES.md) — additive restrictions.
- [`CODEX-START-HERE.md`](CODEX-START-HERE.md) — short entrypoint for Codex.
- [`tasks/AIK-01-CODEX-HANDOFF.md`](tasks/AIK-01-CODEX-HANDOFF.md) — completed bounded audit assignment.
- [`tasks/AIK-01-DOCS-CHANGE.md`](tasks/AIK-01-DOCS-CHANGE.md) — completed two-file documentation task.
- [`tasks/AIK-03-CODEX-HANDOFF.md`](tasks/AIK-03-CODEX-HANDOFF.md) — completed design-only assignment.
- [`tasks/AIK-06-CONTROL-CHAT-NAV-HANDOFF.md`](tasks/AIK-06-CONTROL-CHAT-NAV-HANDOFF.md) — bounded Control Chat source task, now complete with limitations.
- [Round 2 independent audit report](reports/AIK-07B-ROUND-2-AUDIT.md) and [receipt](receipts/AIK-07B-ROUND-2.md) — model verdict `PASS`; process limitations recorded.
- [Round 3 independent audit report](reports/AIK-07B-ROUND-3-AUDIT.md) and [receipt](receipts/AIK-07B-ROUND-3.md) — model verdict `PASS`; report limitations recorded; Eddie's review remains.
- [Round 1 remediation task](tasks/AIK-07B-R1-REMEDIATION.md) — complete with limitations; verified commit `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`.
- [Kanban contract-update task](tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md) — ready for Eddie's final owner review; implementation remains blocked.

- [`reports/AIK-07B-ROUND-1-AUDIT.md`](reports/AIK-07B-ROUND-1-AUDIT.md) and [`receipts/AIK-07B-ROUND-1.md`](receipts/AIK-07B-ROUND-1.md) — first audit and provenance receipt.
- [`receipts/AIK-07B-OWNER-DECISIONS.md`](receipts/AIK-07B-OWNER-DECISIONS.md) — exact D-1–D-5 owner directions and remaining technical gates.
- [`reports/KANBAN-DECISION-BRIEF.md`](reports/KANBAN-DECISION-BRIEF.md) — owner decision brief.
- [`reports/KANBAN-DECISION-BRIEF.html`](reports/KANBAN-DECISION-BRIEF.html) — phone-friendly brief.
- [`receipts/AIK-07-D1-DECISION.md`](receipts/AIK-07-D1-DECISION.md) — earlier D-1-only decision receipt; superseded for current status by `AIK-07B-OWNER-DECISIONS.md`.
- [`receipts/AIK-05-LOCAL-MOCK.md`](receipts/AIK-05-LOCAL-MOCK.md) — verified local-mock prototype receipt.
- [`receipts/AIK-06-CONTROL-CHAT-NAV.md`](receipts/AIK-06-CONTROL-CHAT-NAV.md) — verified branch-only Chat fix receipt.
- [`tasks/AIK-04-PLAN-CODEX-HANDOFF.md`](tasks/AIK-04-PLAN-CODEX-HANDOFF.md) — paused read-only security-plan task.
- [`reports/AIK-03-ECHO-REVIEW.md`](reports/AIK-03-ECHO-REVIEW.md) — Echo and independent review record.
- [`reports/AIK-03-HANDOFF.html`](reports/AIK-03-HANDOFF.html) — AIK-03 completion handoff.
- [`receipts/AIK-01-DOCS.md`](receipts/AIK-01-DOCS.md) — Control wording verification receipt.
