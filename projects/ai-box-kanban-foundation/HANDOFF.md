# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `WAITING_FOR_OWNER_DECISIONS` — D-1 is recorded; D-2–D-5 remain pending.
**Current phase:** Item 4 owner decision gate; no implementation task is active.
**Current task:** Eddie to review the decision brief and answer D-2–D-5; Codex is idle until a new bounded task is authorized.
**Manager:** Echo
**Executor:** Echo coordinates; no active Codex task. No credentials, services, or runtimes.

## TL;DR

The Control boundary wording is corrected and verified. AIK-06 fixed the Control Chat render crash on a branch; its focused test and production build pass, while six baseline full-suite failures remain. AIK-07's [owner decision brief](reports/KANBAN-DECISION-BRIEF.md) and [HTML companion](reports/KANBAN-DECISION-BRIEF.html) are independently verified. Eddie decided D-1; D-2–D-5 remain his choices. Item 8 stays with the separate Mind effort, and items 9–10 wait for physical hardware inspection.

Eddie's estimated AI-box schedule is: parts by **Oct 6**, assembly by **Oct 11**, boot/load testing on **Oct 12**. These are estimates, not commitments.

## Last completed action

AIK-07 is on `codex/AIK-07-KANBAN-DECISION-BRIEF` at `fa8fc4bedb1eb94006e230a1d85e75b7f253dc64`; Echo independently verified the six allowed task-output paths against remote blobs, pending markers, links, HTML, and public-content scan. All five owner choices remain pending. AIK-05 local mock was pushed on private `codex/IC-01-LOCAL-MOCK` at `e480d8a6f92cccc20d3fdba7397145e97b537943`; Echo verified the five-file scope, remote readback, syntax checks, and 3 passing tests. AIK-06 is on `codex/AIK-06-CONTROL-CHAT-NAV` at `6c2a86551a49412ad7c3c4c9a65b1ff9fab2c66c`; Echo reran its focused test and build, compared the six failing full-suite cases against base, and verified remote blobs. It is not merged/deployed. AIK-03's independent review returned **PASS — ZERO GAPS**.

## Verified context

- Mind's `work_items` and `stage_executions` remain internal execution records, not a shared task backend.
- Relay remains a candidate UI, not an authenticated shared task service.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control is a sanitized private repo, but the current security audit says it is not safe to run or expose.
- The existing shared key was not rotated or copied; do not inspect, print, or publish any credential.

## Current constraint

No new Codex scope is active until Eddie answers D-2–D-5. D-1 selects a dedicated logical authority operated by Echo; technology/hosting remain open. Do not change the Kanban contract or resolve other owner decisions. AIK-06's Control fix remains branch-only; do not guess a Relay URL or modify boundary/security surfaces. Inference Control stays mock-only; live auth/security and remote exposure remain blocked.

## Next safe action

Eddie reviews the [D-1–D-5 decision brief](reports/KANBAN-DECISION-BRIEF.md) and supplies D-2–D-5. Do not start shared-board implementation until those answers are recorded and the contract is updated/reviewed in a separate scoped task. Keep item 8 with the separate Mind task; start items 9–10 only after physical parts/topology can be inspected.

## Owner decisions still open

- D-1 decided: dedicated logical service/store; Echo operator; Eddie owner/policy authority. Technology/hosting remain open. D-2–D-5: identity/grants, removal policy, retention including idempotency outcomes, and snapshot/feed interface.
- Relay destination URL only if it is not verifiable in the existing Control configuration.
- Physical hardware and topology confirmation; dates are estimates only.

## Do not do

- Do not rotate, inspect, copy, print, or publish credentials.
- Do not modify any dashboard source, unrelated Control files, Mind/Relay source, or the legacy credential-bearing tree; AIK-06 is limited to Control Chat navigation and its direct regression test.
- Do not inspect the dirty Mind workspace or touch any credential.
- Do not install Proxmox, alter BIOS/OS/GPU settings, expose services, or start live model operations.
- Do not use paid GitHub features, workflows, deployments, or CI.
