# Goal: ai-box-kanban-foundation

**Project ID:** `ai-box-kanban-foundation`
**Full name:** AI Box and Shared Task Board Foundation
**Status:** `READY_FOR_REVIEW`
**Owner:** Eddie
**Manager:** Echo
**Conditional executor:** Codex, task-by-task after approval
**Created:** 2026-09-28

## Project keyword

```text
Goal: ai-box-kanban-foundation
```

## One-sentence goal

Complete the first three planning items for the AI-box dashboard and shared task board: reconcile Control’s authority boundaries, record the located and sanitized Inference Control project, and define a reviewable Kanban MVP contract before implementation.

## Why this exists

The audits found that Mind’s current `work_items` and `stage_executions` are internal execution records, not a qualified general-purpose shared board; Relay is a candidate UI, not yet a shared task service; and Control is intended for oversight/approval rather than execution authority. The separate model dashboard, now named **Onoes-Inference-Control**, has been located and imported to its own sanitized private repository. The project needs a durable, evidence-backed sequence before anyone starts changing source code.

## Definition of done

1. The relevant Control documents have a cited contradiction matrix and proposed wording that Eddie approves; any approved source-document changes are minimal and verified.
2. The dashboard discovery/import status is recorded accurately. Its security gate is explicit, and no claim suggests it is ready to run or expose.
3. A compact Kanban MVP specification defines tasks, actors, permissions, state transitions, mutation attribution, removal approval, idempotency, and event-feed recovery.
4. An independent review identifies unresolved issues; the packet records decisions, evidence, and the next safe action.
5. No live app/model operations, Mind integration, or backend implementation occurs under this packet.

## Explicit non-goals

- Do not build or deploy the shared Kanban service or Relay adapter in this project.
- Do not repurpose Mind’s internal execution ledger as the user-facing task store.
- Do not authorize Control to execute work from Mind data.
- Do not modify application code or run Onoes-Inference-Control against live runtimes.
- Do not rotate, inspect, copy, print, or publish any credential.
- Do not inspect the dirty Mind workspace or alter unrelated working trees.
- Do not install Proxmox, change OS/BIOS/GPU settings, buy hardware, or start paid resources.
- Do not use GitHub Actions, Codespaces, Packages, deployments, runners, or other potentially billable features.

## Current phase and task

**Phase:** 0 — packet prepared; awaiting Eddie’s review.
**Current task:** None in progress. `AIK-01` awaits explicit start approval.
**Current status:** `READY_FOR_REVIEW` — the packet is ready for human judgement; no executor has been dispatched.

## Next safe action

Eddie reviews this packet and, if its scope and Codex permissions are right, authorizes `AIK-01` (read-only Control-document audit). Do not start work from the repository merely because it has been published.

## Decision log

- The first three planning items remain: Control boundary reconciliation; dashboard discovery/import; Kanban MVP requirements. Item 2 is complete; its security remediation remains a separate prerequisite before runtime use.
- Mind remains the memory and internal execution-record system. Shared Kanban is a separate contract/store decision.
- Codex’s initial assignment is read-only against source repositories and may write only project-scoped deliverables; source edits require a later explicit gate.
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
- [`reports/`](reports/) — Markdown and HTML handoffs.
