# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `COMPLETE_WITH_LIMITATIONS` — AIK-07B Round 1 returned `NEEDS_CHANGES`; the five bounded corrections are Echo-verified. Rounds 2–3 and owner approval remain before implementation.
**Current phase:** Item 4 contract audits; Round 1 corrections are verified, followed by Rounds 2–3 and owner approval. Implementation remains blocked.
**Current task:** AIK-07B Round 2 audit preparation — packet freeze/hash followed by a separate read-only auditor; no code or runtime work.
**Manager:** Echo
**Executor:** Codex applied the five Round 1 packet corrections; Echo verified the remote branch, changed blobs, acceptance cases, links, JSON, and Queue table at `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`.

## TL;DR

Eddie has recorded D-1–D-5 in [`receipts/AIK-07B-OWNER-DECISIONS.md`](receipts/AIK-07B-OWNER-DECISIONS.md). Round 1 audited ancestor commit `13ebdac3691edb27fdebe1bbfc6c5961955afb02` and returned `NEEDS_CHANGES` with five findings. Echo verified the frozen files remained unchanged. Codex commit `7020c77469585062ed6fe9136a0dbf4c8e7f2e53` contains the verified Round 1 corrections. Echo confirmed remote ref and all changed blobs match; five corrections resolve the contract/matrix findings. No implementation or runtime work occurred. Two further distinct reviews and final owner approval must pass before implementation.

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

Codex may update only packet paths for its bounded assignment. Round 1 remediation is verified. Round 2 may proceed only as a separate read-only audit of a fresh frozen packet; implementation, source changes, service, runtime, deployment, external data, and public exposure remain blocked until all audit/owner gates pass. Technical details expressly left open by Eddie remain unselected. AIK-06 remains branch-only; Inference Control stays mock-only.

## Next safe action

Echo has verified the remediation. The next safe action is to freeze/hash the revised packet and run Round 2 as a separate read-only audit. Preserve the three-round gate and request Eddie's final contract approval only after Round 3; keep AIK-08 blocked. Do not start implementation or duplicate the separate Mind effort.

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
