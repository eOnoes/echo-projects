# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `RUNNING` — AIK-01/01-DOCS/03 and AIK-05/06 complete with limitations; AIK-07 brief ready for Echo review.
**Current phase:** Software/product chunk, item 4 decision gate — D-1–D-5 recommendation brief.
**Current task:** `AIK-07-KANBAN-DECISION-BRIEF`, Codex (`gpt-6-sol`, medium); packet-only brief drafted for Echo review, with D-1–D-5 `PENDING`.
**Manager:** Echo
**Executor:** Codex; no dashboard source edits, credentials, services, or runtimes.

## TL;DR

The Control boundary wording is corrected and verified. AIK-06 fixed the Control Chat render crash on a branch; its focused test and production build pass, while six baseline full-suite failures remain. The Kanban MVP contract passed independent review, but D-1–D-5 remain open. AIK-07 drafted the [owner decision brief](reports/KANBAN-DECISION-BRIEF.md) and [HTML companion](reports/KANBAN-DECISION-BRIEF.html); all five choices remain Eddie's. Item 8 stays with the separate Mind effort, and items 9–10 wait for physical hardware inspection.

Eddie's estimated AI-box schedule is: parts by **Oct 6**, assembly by **Oct 11**, boot/load testing on **Oct 12**. These are estimates, not commitments.

## Last completed action

AIK-05 local mock was pushed on private `codex/IC-01-LOCAL-MOCK` at `e480d8a6f92cccc20d3fdba7397145e97b537943`; Echo verified the five-file scope, remote readback, syntax checks, and 3 passing tests. AIK-06 is on `codex/AIK-06-CONTROL-CHAT-NAV` at `6c2a86551a49412ad7c3c4c9a65b1ff9fab2c66c`; Echo reran its focused test and build, compared the six failing full-suite cases against base, and verified remote blobs. It is not merged/deployed. AIK-03's independent review returned **PASS — ZERO GAPS**.

## Verified context

- Mind's `work_items` and `stage_executions` remain internal execution records, not a shared task backend.
- Relay remains a candidate UI, not an authenticated shared task service.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control is a sanitized private repo, but the current security audit says it is not safe to run or expose.
- The existing shared key was not rotated or copied; do not inspect, print, or publish any credential.

## Current constraint

AIK-07 may write only its decision brief, HTML, receipt, and packet tracking entries. Do not change the Kanban contract or resolve owner decisions. AIK-06's Control fix remains branch-only; do not guess a Relay URL or modify boundary/security surfaces. Inference Control stays mock-only; live auth/security and remote exposure remain blocked.

## Next safe action

Echo verifies AIK-07 citations, option recommendations, links, and remote packet branch using the [receipt](receipts/AIK-07-DECISION-BRIEF.md). Then give Eddie the brief's compact D-1–D-5 decision prompt; do not start shared-board implementation until he answers and a separate implementation task is scoped. Keep item 8 with the separate Mind task; start items 9–10 only after physical parts/topology can be inspected.

## Owner decisions still open

- Kanban D-1–D-5: authoritative store/operator, identity/grants, removal policy, retention including idempotency outcomes, and snapshot/feed interface.
- Relay destination URL only if it is not verifiable in the existing Control configuration.
- Physical hardware and topology confirmation; dates are estimates only.

## Do not do

- Do not rotate, inspect, copy, print, or publish credentials.
- Do not modify any dashboard source, unrelated Control files, Mind/Relay source, or the legacy credential-bearing tree; AIK-06 is limited to Control Chat navigation and its direct regression test.
- Do not inspect the dirty Mind workspace or touch any credential.
- Do not install Proxmox, alter BIOS/OS/GPU settings, expose services, or start live model operations.
- Do not use paid GitHub features, workflows, deployments, or CI.
