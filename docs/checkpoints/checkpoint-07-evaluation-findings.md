# CP-07 — Evaluation & Findings

**Phase(s):** P10
**Vertical slice:** Slice D
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

The platform explains execution outcomes, classifies failures by cause, and distinguishes observed evidence from evaluator interpretation.

## A. Product validation — what the QA user can do

A QA user can:
1. Open a failed test and see **why** it failed, classified by cause
2. See that a missing precondition is reported as a data/state problem, not a product defect
3. See that a failure with unknown expected behaviour is reported as insufficient evidence, not a defect
4. See items that never executed carried as assessments, excluded from pass rates
5. Read a finding with its evidence and its lineage back to test case, scenario and understanding version

**This completes Vertical Slice D:** *QA can understand what happened and why.*

## B. Engineering validation — what the platform must prove internally

- Three evaluator layers with fixed ordering; deterministic can fail outright
- Evaluator registry and independent versioning; version recorded on every verdict
- The seven-rung triage ladder, deterministic before semantic
- Finding classification across six causes
- Findings with mandatory evidence and lineage
- Run comparison on content-derived identity
- **A semantic verdict may never overturn a deterministic one**

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-06 |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-06 (an execution run with mixed outcomes) |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | CP-10 reconciliation — supplies the highest-value oracle-free defect candidates |
| **Parallel work** *(can safely proceed independently)* | CP-08 governance |

## Preconditions

- CP-06 PASS, with a run containing passes, failures of several causes, and non-executed items

## Required components

Evaluators · triage ladder · findings · run comparison

## Functional test

1. Inspect a failed test; confirm the triage ladder ran and a cause is named
2. Confirm a missing-precondition failure is classified data/state
3. Confirm an unknown-oracle assertion failure is classified insufficient evidence
4. Confirm never-executed items carry assessments and are excluded from pass rates
5. Open a finding; follow its lineage to test case, scenario and understanding version

## Automated tests

- Unit: triage ladder ordering; evaluator layer precedence
- Contract: `EvaluatorContract` (adding an evaluator with no orchestration change), `FindingContract`
- Integration: full evaluation over a real execution run
- Negative: a semantic verdict cannot overturn a deterministic one; **a failed assertion alone cannot produce a product defect finding**

## Required test data

An execution run containing passes, failures of several causes, and non-executed items.

## Expected outcome

Outcomes are explained and classified; observation is distinguished from interpretation.

## Evidence produced

Stored under `evidence/CP-07/`:

- Triage output classifying each failure by cause
- Findings with evidence and full lineage
- Test output proving a failed assertion alone yields no product defect finding
- Run comparison across two runs

## Failure conditions

- A failed assertion alone producing a product defect finding
- A semantic verdict overturning a deterministic one
- Non-executed items counted in pass rates
- A finding without evidence or lineage

## Known limitations

Defect candidates require a confirmed or coded-rule oracle. Where the oracle is unknown, the platform reports insufficient evidence — a correct outcome, not a gap.

## Exit criteria

- [ ] All Phase 10 acceptance criteria met
- [ ] **Vertical Slice D demonstrated end to end**
- [ ] Evidence captured under `evidence/CP-07/`
- [ ] State and roadmap position updated

## Regression criteria

All previously established regression criteria (RG-1 … RG-10) in
[`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md) §4, plus the functional test of every
prior checkpoint re-executed automatically.

---

## Result record

| Field | Value |
|---|---|
| Attempted on | — |
| Result | — |
| Evidence location | — |
| Known limitations accepted | — |
| Recorded by | — |

*Results: PASS · PASS WITH KNOWN LIMITATION · BLOCKED · FAIL*
