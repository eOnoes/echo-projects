# AIK-04-PLAN — security planning handoff

## TL;DR

**Status: READY_FOR_REVIEW.** The packet now has a prioritized, fail-closed proposal for all seven sanitized Inference Control findings and nine mock/fixture acceptance cases. These are planning artifacts, not fixes or executed tests. The dashboard remains blocked from live model or network use. Echo must review the branch and remote readback before marking the task complete.

## Deliverables and evidence

- [Security readiness plan](../deliverables/INFERENCE-CONTROL-SECURITY-PLAN.md) — F1–F7, required behavior, and dependencies.
- [Acceptance matrix](INFERENCE-CONTROL-SECURITY-ACCEPTANCE.md) — S-01–S-09, denial cases, permitted controls, and zero-side-effect expectations.
- [Task receipt](../receipts/AIK-04-PLAN.md) — exact files and local document checks.
- [Task handoff](../tasks/AIK-04-PLAN-CODEX-HANDOFF.md) — sole source of the seven sanitized audit findings.

## Drill-down

The sequence closes hub and proxy authorization, including the unset agent-key case, before adding destructive-action request proof, finite body handling, approved remote destinations, and model/context/unload-all validation. A later implementation task must specify policy values and map all relevant routes and code paths. That mapping and the policy values are **UNRESOLVED** in this read-only packet; missing values must fail closed.

The existing credential non-rotation decision is preserved. No credential material was sought or included. Eddie's October 6/11/12 AI-box dates remain estimates and do not override the security gate.

## Authorization boundary

This handoff authorizes no code or test changes, source inspection, credential work, services, upstream calls, live model testing, exposure, or deployment. Before any code work, Eddie and Echo must open a separately approved task naming exact files and policy decisions. After code changes, local mock/fixture acceptance and independent security review must pass before any separately authorized live test.
