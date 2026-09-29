# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `READY` — D-1–D-5 owner directions are recorded; AIK-07B packet task is prepared, Codex dispatch pending.
**Current phase:** Item 4 contract integration and three audit/refinement rounds; no implementation task is active.
**Current task:** `AIK-07B-KANBAN-CONTRACT-UPDATE` — project-packet-only; Codex receives a separate handoff. AIK-08 remains blocked pending contract audits and owner review.
**Manager:** Echo
**Executor:** Echo coordinates; no active Codex task. No credentials, services, or runtimes.

## TL;DR

The Control boundary wording is corrected and verified. AIK-06 fixed the Control Chat render crash on a branch; its focused test and production build pass, while six baseline full-suite failures remain. Eddie has recorded D-1–D-5 in [`receipts/AIK-07B-OWNER-DECISIONS.md`](receipts/AIK-07B-OWNER-DECISIONS.md). Codex may now update the design contract and acceptance matrix only. Three distinct audit/refinement rounds and final owner review must pass before any implementation. Item 8 stays with the separate Mind effort; items 9–10 wait for physical inspection.

Eddie's estimated AI-box schedule is: parts by **Oct 6**, assembly by **Oct 11**, boot/load testing on **Oct 12**. These are estimates, not commitments.

## Last completed action

AIK-07 is on `codex/AIK-07-KANBAN-DECISION-BRIEF` at `fa8fc4bedb1eb94006e230a1d85e75b7f253dc64`; Echo verified the brief. Eddie then recorded D-1–D-5 in the owner-decision receipt. AIK-05 local mock remains mock-only at `e480d8a6f92cccc20d3fdba7397145e97b537943`. AIK-06 Chat fix remains branch-only at `6c2a86551a49412ad7c3c4c9a65b1ff9fab2c66c`; focused test/build pass, six baseline full-suite failures remain, and Relay routing is unresolved. AIK-03's independent review returned **PASS — ZERO GAPS**.

## Verified context

- Mind's `work_items` and `stage_executions` remain internal execution records, not a shared task backend.
- Relay remains a candidate UI, not an authenticated shared task service.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control is a sanitized private repo, but the current security audit says it is not safe to run or expose.
- The existing shared key was not rotated or copied; do not inspect, print, or publish any credential.

## Current constraint

Codex may update only the packet paths listed in `tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md`. No source code, database, runtime, service, deployment, external data, or public exposure. Technical details expressly left open by Eddie remain unselected. Three independent audit/refinement rounds and final owner review precede AIK-08. AIK-06 remains branch-only; do not guess a Relay URL. Inference Control stays mock-only; live auth/security remain blocked.

## Next safe action

Dispatch AIK-07B on the named branch; Echo reviews its contract update, then runs audit rounds 1–3 sequentially with remediation and fresh rechecks. Ask Eddie to approve the final audited contract before AIK-08. Keep item 8 with the separate Mind task; start items 9–10 only after physical parts/topology can be inspected.

## Owner decisions still open

- Owner directions D-1–D-5 are recorded. Open technical choices: database/runtime/hosting, concrete verified identity source/lifecycle, numeric production retention, and event transport. Three design audits and final owner approval remain.
- Relay destination URL only if it is not verifiable in the existing Control configuration.
- Physical hardware and topology confirmation; dates are estimates only.

## Do not do

- Do not rotate, inspect, copy, print, or publish credentials.
- Do not modify any dashboard source, unrelated Control files, Mind/Relay source, or the legacy credential-bearing tree; AIK-06 is limited to Control Chat navigation and its direct regression test.
- Do not inspect the dirty Mind workspace or touch any credential.
- Do not install Proxmox, alter BIOS/OS/GPU settings, expose services, or start live model operations.
- Do not use paid GitHub features, workflows, deployments, or CI.
