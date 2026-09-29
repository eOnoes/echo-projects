# Goal: ai-box-kanban-foundation

**Project ID:** `ai-box-kanban-foundation`
**Full name:** AI Box and Shared Task Board Foundation
**Status:** `RUNNING`
**Owner:** Eddie
**Manager:** Echo
**Current executor:** Codex (`gpt-6-sol`, medium) on AIK-04-PLAN; Echo verifies. AIK-03 is complete with limitations.
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

1. The relevant Control documents have a cited contradiction matrix and wording approved or delegated by Eddie; any delegated source-document changes are minimal and verified.
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

## Owner-estimated AI-box schedule (recorded 2026-09-28)

- All parts expected by **October 6, 2026**.
- Unit assembled by **October 11, 2026**.
- Boot/load testing targeted for **October 12, 2026**.

Eddie called these estimates, not commitments. They do not authorize purchases, OS/BIOS changes, service exposure, or live testing before the applicable gates pass.

## Current phase and task

**Current phase:** Phase 4 — Inference Control security remediation plan (read-only).
**Current task:** `AIK-04-PLAN` — derive a reviewed remediation plan and acceptance matrix from the sanitized audit findings.
**Current status:** `RUNNING` — Codex assigned; no dashboard source, credentials, runtime, or services may be touched.

## Next safe action

AIK-03 is complete with limitations and independently reviewed; the next task is a read-only security remediation plan from the sanitized dashboard findings. Eddie's estimated schedule is parts by Oct 6, assembly by Oct 11, boot/load testing Oct 12. The dates are estimates; no live testing starts before security approval.

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
- [`tasks/AIK-01-CODEX-HANDOFF.md`](tasks/AIK-01-CODEX-HANDOFF.md) — completed bounded audit assignment.
- [`tasks/AIK-01-DOCS-CHANGE.md`](tasks/AIK-01-DOCS-CHANGE.md) — completed two-file documentation task.
- [`tasks/AIK-03-CODEX-HANDOFF.md`](tasks/AIK-03-CODEX-HANDOFF.md) — completed design-only assignment.
- [`tasks/AIK-04-PLAN-CODEX-HANDOFF.md`](tasks/AIK-04-PLAN-CODEX-HANDOFF.md) — active read-only security-plan assignment.
- [`reports/AIK-03-ECHO-REVIEW.md`](reports/AIK-03-ECHO-REVIEW.md) — Echo and independent review record.
- [`reports/AIK-03-HANDOFF.html`](reports/AIK-03-HANDOFF.html) — AIK-03 completion handoff.
- [`receipts/AIK-01-DOCS.md`](receipts/AIK-01-DOCS.md) — Control wording verification receipt.
