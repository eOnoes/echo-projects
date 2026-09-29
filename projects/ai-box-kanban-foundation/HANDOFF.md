# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `READY` — AIK-07B Round 1 returned `NEEDS_CHANGES`; the scoped remediation handoff is ready for Codex.
**Current phase:** Item 4 contract remediation, then Rounds 2–3 and owner approval; implementation remains blocked.
**Current task:** `AIK-07B-R1-REMEDIATION` — packet-only corrections; no code or runtime work.
**Manager:** Echo
**Executor:** Codex is assigned the five Round 1 corrections; Echo independently verifies each result.

## TL;DR

Eddie has recorded D-1–D-5 in [`receipts/AIK-07B-OWNER-DECISIONS.md`](receipts/AIK-07B-OWNER-DECISIONS.md). AIK-07B contract work is at commit `13ebdac3691edb27fdebe1bbfc6c5961955afb02`; Kimi K3 Round 1 returned `NEEDS_CHANGES` with five findings. Echo verified the frozen files remained unchanged. The bounded Codex correction task is ready; two further distinct reviews and final owner approval must pass before implementation.

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

Codex may update only the packet paths listed in `tasks/AIK-07B-R1-REMEDIATION.md`. No source code, database, runtime, service, deployment, external data, or public exposure. Technical details expressly left open by Eddie remain unselected. Round 2 and implementation stay blocked until Echo verifies the remediation and later audit/owner gates pass. AIK-06 remains branch-only; do not guess a Relay URL. Inference Control stays mock-only; live auth/security remain blocked.

## Next safe action

Echo verifies Codex's R1 corrections, then runs Round 2 against a fresh frozen snapshot. Preserve the three-round gate and request Eddie's final contract approval only after Round 3; keep AIK-08 blocked. Do not start implementation or duplicate the separate Mind effort.

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
