# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `RUNNING` — AIK-01 and AIK-01-DOCS complete; AIK-03 complete with limitations; AIK-04-PLAN ready for Echo review.
**Current phase:** Phase 4 — read-only Inference Control security-remediation planning.
**Current task:** `AIK-04-PLAN`, `READY_FOR_REVIEW`; Codex (`gpt-6-sol`, medium) drafted, Echo independently verifies.
**Manager:** Echo
**Executor:** Codex; no dashboard source edits, credentials, services, or runtimes.

## TL;DR

The Control boundary wording is corrected and verified. The Kanban MVP contract and acceptance matrix are complete with limitations: an independent reviewer passed them, but Eddie's D-1–D-5 decisions remain open; D-4 should explicitly cover idempotency-outcome retention before implementation. Codex has drafted a **read-only** security plan for all seven supplied Inference Control findings and nine mock/fixture acceptance cases. It awaits Echo review; no fix or test has been performed.

Eddie's estimated AI-box schedule is: parts by **Oct 6**, assembly by **Oct 11**, boot/load testing on **Oct 12**. These are estimates, not commitments.

## Current packet output

- [Security plan](deliverables/INFERENCE-CONTROL-SECURITY-PLAN.md): prioritized fail-closed sequence and dependencies for F1–F7.
- [Acceptance matrix](reports/INFERENCE-CONTROL-SECURITY-ACCEPTANCE.md): S-01–S-09 proposed mock/fixture cases.
- [Milestone handoff](reports/AIK-04-PLAN-HANDOFF.md) and [HTML](reports/AIK-04-PLAN-HANDOFF.html): concise review entrypoint.
- Exact code paths and implementation policy values remain `UNRESOLVED` for a separately approved task.

## Last accepted action

AIK-03 was pushed on `codex/AIK-03`; Echo verified the eight-file project-only scope, 27 remote packet blobs, links, JSON example, HTML, diff, and safety scans. Independent `mimo-v2.5` review returned **PASS — ZERO GAPS**.

## Verified context

- Mind's `work_items` and `stage_executions` remain internal execution records, not a shared task backend.
- Relay remains a candidate UI, not an authenticated shared task service.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control is a sanitized private repo, but the current security audit says it is not safe to run or expose.
- The existing shared key was not rotated or copied; do not inspect, print, or publish any credential.

## Current constraint

AIK-04-PLAN may write only project-packet documents from the supplied sanitized findings. It must not inspect dashboard source or history, access keys, run services, connect live models, or make code changes. Actual security remediation requires a separate exact-file task and review.

## Next safe action

Echo verifies the AIK-04-PLAN exact diff, local checks, and remote readback, then presents the plan and acceptance criteria for owner review. Any source remediation needs a separate exact-file task. No live boot/load test may bypass the security gate.

## Owner decisions still open

- Kanban D-1–D-5: authoritative store/operator, identity and board grants, removal policy, retention (including idempotency outcomes), and snapshot/feed interface.
- Exact implementation scope for Inference Control security remediation after its plan is reviewed.

## Do not do

- Do not rotate, inspect, copy, print, or publish credentials.
- Do not inspect the legacy credential-bearing dashboard tree or dirty Mind workspace.
- Do not modify dashboard, Control, Mind, or Relay source code in the current task.
- Do not install Proxmox, alter BIOS/OS/GPU settings, expose services, or start live model operations.
- Do not use paid GitHub features, workflows, deployments, or CI.
