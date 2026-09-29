# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `COMPLETE_WITH_LIMITATIONS` — Round 1 corrections are verified; Round 2 DSH/Gemini returned `PASS`, with process limitations recorded in [`receipts/AIK-07B-ROUND-2.md`](receipts/AIK-07B-ROUND-2.md). Round 3 and owner approval remain before implementation.
**Current phase:** Item 4 contract audits; Round 1 corrections are verified and Round 2 returned `PASS` with process limitations recorded. Round 3 and owner approval remain. Implementation remains blocked.
**Current task:** AIK-07B Round 3 — freeze/hash a fresh packet including both prior audit reports/receipts; dispatch a distinct read-only auditor only. No source/runtime work.
**Manager:** Echo
**Executor:** Codex applied the five Round 1 packet corrections; Echo verified the remote branch, changed blobs, acceptance cases, links, JSON, and Queue table at `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`.

## TL;DR

Round 1 audit returned `NEEDS_CHANGES`; its five scoped corrections are Echo-verified at `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`. Round 2 returned `PASS` via `google/gemini-3.5-flash-lite`; its report and process limitations are in the Round 2 receipt. Prepare and freeze a distinct Round 3 audit packet. One further review and final owner approval must pass before implementation.

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

Round 1 remediation is verified; Round 2 DSH/Gemini returned `PASS`, with process limitations recorded. Round 3 may proceed as a separate read-only audit of a fresh frozen packet. Implementation, source changes, service, runtime, deployment, and public exposure remain blocked until Round 3 and owner approval pass. Technical details expressly left open by Eddie remain unselected. AIK-06 remains branch-only; Inference Control stays mock-only.

## Next safe action

Round 2's model verdict was `PASS` with zero findings; Echo's verification and process limitations are recorded in its report/receipt. The current next step is a fresh, hash-pinned Round 3 review with a distinct auditor. After it passes, Eddie must approve the final contract before AIK-08. Do not start implementation or duplicate the separate Mind effort.

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
