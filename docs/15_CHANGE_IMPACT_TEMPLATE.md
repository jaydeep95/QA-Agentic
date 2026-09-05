# 15 — Change Impact Template

**Status:** ACTIVE — the required form for changes affecting architecture or product behaviour
**Created:** 2026-09-05 (Roadmap V2 correction)

> **Relationship to other documents:** this is the *form*. Completed analyses become entries
> in [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md); the resulting
> classification lands in [`14_ARCHITECTURAL_DECISION_REGISTER.md`](14_ARCHITECTURAL_DECISION_REGISTER.md).

---

## 1. When this is required

Complete this template **before implementing** any change that can affect:

product scope · UAU · contracts · data model · API · workflow · security ·
provider boundary · coverage model · test design · test data · automation semantics ·
execution semantics · evaluation · governance · UI/UX behaviour · traceability ·
phase scope · a FIXED decision in the register

## 2. When this is NOT required

Trivial implementation changes that do not affect product behaviour, architecture or a
contract: refactoring within a module, renaming a private symbol, adding a test, improving
an error message, performance work behind an unchanged interface, or exercising an explicit
`FLEXIBLE` freedom already recorded in the register.

> Requiring this form for routine work would make it ceremony, and ceremony gets skipped.
> Requiring it for the list in §1 is what keeps the architecture honest.

---

## 3. Template

```text
CHANGE ID:                    CHG-nnn
Date:
Raised by:
Requested Change:
Reason:

─────────────────────────────────────────────────────────────
CHANGE CLASSIFICATION   (select exactly one)
─────────────────────────────────────────────────────────────
[ ] FIXED DECISION CHANGE     — alters an ADR classified FIXED
[ ] FLEXIBLE IMPLEMENTATION   — exercises a recorded freedom
[ ] PROVISIONAL UPDATE        — revises a target on new evidence
[ ] PLACEHOLDER UPDATE        — advances a provider decision
[ ] OPEN DECISION             — resolves an OPEN item
[ ] PRODUCT CHANGE            — alters scope or user-visible behaviour
[ ] ARCHITECTURE CHANGE       — alters structure, boundary or contract

Register entry affected:      ADR-___ / THR-___ / OD-___  (or "none")
Invariant affected:           INV-__  (or "none")

─────────────────────────────────────────────────────────────
IMPACT   (tick all that apply)
─────────────────────────────────────────────────────────────
[ ] Product Scope       [ ] UAU              [ ] Contracts
[ ] Data Model          [ ] API              [ ] Workflow
[ ] Security            [ ] Provider Boundary[ ] Coverage
[ ] Test Design         [ ] Test Data        [ ] Automation
[ ] Execution           [ ] Evaluation       [ ] Governance
[ ] UI/UX               [ ] Tests            [ ] Checkpoints
[ ] Traceability        [ ] Documentation

─────────────────────────────────────────────────────────────
ANALYSIS
─────────────────────────────────────────────────────────────
Documents Requiring Update:
Affected Components:
Affected Contracts:
Affected Roadmap Items:
Affected Requirements (REQ-*):
Affected Tests:
Affected Checkpoints:
Affected Vertical Slices:

Migration Required:            yes / no  — describe
Rollback Considerations:
Risk if not done:
Risk of doing it:

─────────────────────────────────────────────────────────────
DECISION
─────────────────────────────────────────────────────────────
Decision:                      approved / rejected / deferred
Approved by:
Date:
Recorded as:                   DEC-nnn in 10_DECISION_AND_CHANGE_LOG.md
Register updated:              yes / no

─────────────────────────────────────────────────────────────
EXECUTION
─────────────────────────────────────────────────────────────
Implementation Status:
Tests Updated:
Checkpoint Revalidated:
Evidence:
Current State Updated:
```

---

## 4. The order that must not be reversed

```text
Change Request → Impact Analysis → Decision Classification
   → Documentation Update → Roadmap Update → Implementation
   → Testing → Evidence → Checkpoint Revalidation → Current State Update
```

**Never implement first and document afterwards** for anything in §1.

---

## 5. The DO NOT PROCEED rule

Stop and record before implementing when any of these is true:

| Trigger | Why it stops work |
|---|---|
| The change conflicts with a FIXED decision | The register is the authority; working around it silently corrupts the architecture |
| The requirement is not represented in the roadmap | Undocumented work cannot be traced, validated or recovered |
| A component boundary is unclear | Guessing a boundary is how responsibilities leak |
| A provider decision is still open | Binding a provider silently defeats replaceability |
| A numeric threshold has no provenance | It would become a fake obligation |
| A data model change is required | It propagates to lineage, staleness and traceability |
| A checkpoint would no longer prove its intended capability | The validation system stops meaning anything |
| Implementation requires changing a product invariant | INV-1 … INV-10 are not implementation details |

**Do not resolve any of these by picking an interpretation.** Record the conflict,
complete this template, obtain the decision, then implement.

## 6. Completed change analyses

| CHG ID | Change | Classification | Decision | Recorded as |
|---|---|---|---|---|
| CHG-001 | Roadmap V2 correction: checkpoint restructure, dependency corrections, decision classification | ARCHITECTURE CHANGE (documentation only — no code exists) | Approved — requested by the Solution Architect | DEC-005 … DEC-009 |
