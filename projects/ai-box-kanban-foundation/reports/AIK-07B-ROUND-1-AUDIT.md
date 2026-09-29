# AIK-07B Round 1 Contract Audit

**Verdict:** `NEEDS_CHANGES` — **Confidence:** high
**Scope confirmed:** Branch `docs/AIK-07B-KANBAN-CONTRACT-UPDATE`, commit `13ebdac3691edb27fdebe1bbfc6c5961955afb02` (HEAD short SHA `13ebdac` matches). All **16 frozen files** are present at the listed paths; none missing. Per instructions I did not independently compute SHA-256 values; hash verification is left to the supervisor. Review was read-only over the packet snapshot; no code, runtime, or service was executed or inspected.

## Answers to numbered questions

**1. D-1–D-5 integration:** Yes. The contract's "Threat notes and decisions" section and the brief reproduce the receipt faithfully: dedicated logical store with Echo operating/Eddie owning policy (D-1); verified per-actor identity, Eddie-directed grants, shared-key prohibition (D-2); distinct approver, no self-approval, reason + current revision (D-3); trial-scoped no-expiry with restart durability and production-retention gate (D-4); JSON snapshot, opaque cursor, ordered durable replay, `low|normal|high`, optional opaque `source_ref` (D-5). No database, runtime, host, identity provider, retention number, or transport is chosen anywhere.

**2. Identity/grant policy vs D-2:** Enforceable as designed. Contract requires actor ID derived "from a verified authentication context," denies "a shared service key, browser field, request-supplied actor name," requires per-operation grant checks "on every operation, including replay," and states "Eddie owns board-grant policy; Echo administers grants only under Eddie's direction." A-01/A-02/A-03/A-14 give observable pass/fail for forgery, cross-board denial, revocation, and key impersonation. The identity-source/lifecycle selection is explicitly gated, not a defect.

**3. Removal lifecycle vs D-3:** Substantially matches (A-08/A-15 cover request/deny/re-request/approve, bypass, self-approval, stale approval), with two gaps in findings R1-02/R1-03 below.

**4. D-4 scoping:** Correct. No-expiry is confined to "a private, bounded trial" with the explicit sentence "This trial rule does not authorize production, public exposure, or broader multi-user use," plus A-17 blocking broader use until numeric retention is evidence-backed and Eddie-approved.

**5. D-5:** Fully specified as directed; transport and snapshot/cursor handoff mechanics are named as open, evidence-gated choices in contract, brief, and receipt.

**6. Contract ↔ A-01–A-18 agreement:** Largely consistent with observable criteria, except R1-01 (A-13 lacks an expected outcome for Control-originated writes) and R1-02 (stale-approval denial exists only in the matrix, not the contract text).

**7. Relay/Control/Mind boundaries:** Consistent everywhere except the R1-01 ambiguity — see findings.

**8. Open-choice/no-implementation gates:** Explicit and aligned across brief (MD + HTML "Technical choices and gates" tables), task ledger, plan Phase 7B, handoff "Owner decisions still open," and both receipts. AIK-08 is uniformly blocked pending three audits + owner approval.

**9. Other issues:** See R1-03, R1-04, R1-05.

## Findings

**AIK07B-R1-01 — MAJOR — `reports/KANBAN-ACCEPTANCE-MATRIX.md` A-13 vs. Control oversight-only boundary.**
Evidence: A-13 scenario: "exercise task reads/writes through Relay and Control clients," but its pass criterion states only "One dedicated logical service/store is authoritative; Echo operates under Eddie's policy authority; clients create no competing task authority." Meanwhile the task constraint and contract state "Control is oversight/approval only" / "Control owns oversight, findings, and bounded approvals," and CHARTER item 6 limits Control views to "show task history/approvals."
Why it matters: The matrix header promises "A pass requires the stated observable result," yet A-13 never states whether a Control-originated board *write* must succeed (as an ordinary granted client) or be denied (to preserve oversight-only). Two implementers can build opposite behavior and both claim a pass on the exact boundary the audit was asked to scrutinize.
Correction: State in the contract whether actors operating through the Control surface may hold board write grants or are restricted to read + approve-removal, and add the corresponding explicit expected outcome to A-13.

**AIK07B-R1-02 — MINOR — `deliverables/KANBAN-MVP.md` "Lifecycle and writes" vs. matrix A-15.**
Evidence: The contract says approval occurs "with a recorded reason and the current task revision" but never states that an approval presented at a non-current (stale) revision is denied; only A-15 carries "stale revision deny without mutation." The contract's stale-revision conflict rule is written for edits ("Stale revisions fail with a conflict… must not silently overwrite another actor's edit").
Why it matters: D-3's "current task revision" requirement is enforceable for approvals only via the matrix; the normative contract text is silent, so a contract-only reader could implement approval without a revision check.
Correction: Add one sentence to the contract: an approval decision presented against a revision that is no longer current fails with a conflict and requires re-review.

**AIK07B-R1-03 — MINOR — `deliverables/KANBAN-MVP.md`, removal-request edge cases.**
Evidence: "A removal request can be made against an active task and becomes `pending`" and "removed tasks cannot be edited, completed, reopened, or removed again." Unspecified: (a) a removal *request* against an already-removed task; (b) a second request while one is already `pending` on the same task.
Why it matters: Both are reachable states with no specified behavior and no acceptance case; implementations can diverge.
Correction: Define both outcomes (e.g., reject both with a named result) and extend A-08's pass/fail line.

**AIK07B-R1-04 — NOTE — `TASKS.md` Queue table, lines for AIK-03, AIK-07B, AIK-08.**
Evidence: rows begin with `||` (e.g., "|| `AIK-07B-KANBAN-CONTRACT-UPDATE` | Integrate D-1–D-5…"), producing malformed Markdown table rows.
Why it matters: Cosmetic, but this is a public, hash-frozen governance packet; broken rows degrade the rendered ledger.
Correction: Replace leading `||` with `|` on the three affected rows.

**AIK07B-R1-05 — NOTE — `deliverables/KANBAN-MVP.md` "Event feed and recovery" / A-11/A-16.**
Evidence: Trial rule is "no automatic expiry," yet "A cursor outside retention produces an explicit expired-cursor result" and A-16 tests an expired cursor inside the private bounded trial. What makes a cursor "expired" when nothing expires automatically is undefined.
Why it matters: Mirrors the owner's own D-4 wording, so it is faithful integration, not a deviation — but the trigger condition for expiry-in-trial (e.g., manual compaction) is unspecified.
Correction: One clause naming what can expire a cursor during the trial, or explicitly deferring the trigger definition to the production-retention gate.

## Intentionally gated technical unknowns (not defects)

Database/runtime/hosting/deployment; concrete verified identity source and lifecycle (enrollment, revocation, service-actor handling); numeric production retention and capacity/deletion behavior; event transport; snapshot/cursor handoff mechanics. Each is named with its evidence/approval gate in the receipt, contract, brief, and receipt-of-work.

## Alignment and scope statements

Markdown brief, HTML brief, and acceptance matrix align in decision content, acceptance-case mapping, and gating language; the HTML faithfully mirrors the Markdown. No implementation, runtime, service, or test was inspected or executed; this audit covered document consistency only.

NEEDS_CHANGES — AIK07B-R1-01, AIK07B-R1-02, AIK07B-R1-03