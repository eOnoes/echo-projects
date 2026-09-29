# Goal: ai-box-kanban-foundation

**Project ID:** `ai-box-kanban-foundation`
**Full name:** AI Box and Shared Task Board Foundation
**Status:** `RUNNING`
**Owner:** Eddie
**Manager:** Echo
**Current executor:** Codex (`gpt-6-sol`, medium) on AIK-07B Round 1 remediation; packet corrections await Echo verification. Round 1 Kimi K3 audit returned `NEEDS_CHANGES`; Echo verified the frozen artifacts remained unchanged.
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

**Current phase:** Item 4 — apply Round 1 contract corrections, then complete audits 2–3 and owner approval.
**Current task:** `AIK-07B-R1-REMEDIATION` — packet-only; no source/runtime work.
**Current status:** `RUNNING` — Kimi K3 Round 1 returned `NEEDS_CHANGES`; five scoped packet corrections are applied for Echo verification.

## Next safe action

Echo verifies Codex's R1 corrections, then launches Round 2 against a fresh frozen snapshot. Keep the three-round gate and AIK-08 block; request Eddie's final approval only after Round 3 passes. No source-code or runtime work is active.

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
- [`tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md`](tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md) — contract integration task, currently `NEEDS_CHANGES` after Round 1.
- [`tasks/AIK-07B-R1-REMEDIATION.md`](tasks/AIK-07B-R1-REMEDIATION.md) — current bounded corrective task.
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
