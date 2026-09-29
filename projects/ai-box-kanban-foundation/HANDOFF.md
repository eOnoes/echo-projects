# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `READY_FOR_OWNER_REVIEW` — Round 1 findings were corrected; Rounds 2–3 DSH/Gemini returned `PASS` with process/report limitations recorded in their receipts. Eddie's final contract approval remains before implementation.
**Current phase:** Item 4 review is complete pending Eddie's decision. Implementation remains blocked.
**Current task:** Eddie's final AIK-07B contract review; no source/runtime work.
**Manager:** Echo
**Executor:** Codex applied the five Round 1 packet corrections; Echo verified the remote branch, changed blobs, acceptance cases, links, JSON, and Queue table at `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`.

## TL;DR

Round 3 returned `PASS` via `google/gemini-3.1-flash-lite`; the report omits two explicitly open cursor choices and its completion wording does not replace Eddie's decision. See `receipts/AIK-07B-ROUND-3.md`. The three design review/refinement rounds are recorded, but the final contract approval and a separate implementation authorization remain outstanding.

Eddie's estimated AI-box schedule is: parts by **Oct 6**, assembly by **Oct 11**, boot/load testing on **Oct 12**. These are estimates, not commitments.

## Last completed action

AIK-07B's contract update at `13ebdac3691edb27fdebe1bbfc6c5961955afb02` integrated all five owner directions. Kimi K3 Round 1 returned `NEEDS_CHANGES`; Echo verified the frozen 16-file inputs remained hash-identical. The five corrections are now bounded in `AIK-07B-R1-REMEDIATION`; no implementation or runtime work occurred.

## Verified context

- Mind's `work_items` and `stage_executions` remain internal execution records, not a shared task backend.
- Relay remains a candidate UI, not an authenticated shared task service.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control is a sanitized private repo, but the current security audit says it is not safe to run or expose.
- The existing shared key was not rotated or copied; do not inspect, print, or publish any credential.

## Current constraint

Rounds 2–3 returned `PASS`, with process/report limitations recorded in their receipts. Eddie's final contract approval remains; implementation, source changes, service, runtime, deployment, and public exposure stay blocked until explicit approval and a separate implementation task. Technical details expressly left open remain unselected. AIK-06 remains branch-only; Inference Control stays mock-only.

## Next safe action

Round 3 returned an independent model verdict `PASS`/zero findings; Echo's hash checks and the report caveats are recorded in the Round 3 report/receipt. The three review/refinement rounds are recorded. Eddie's final contract approval is the remaining design gate before AIK-08; no implementation or runtime work.

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
