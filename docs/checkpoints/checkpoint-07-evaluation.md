# CP-07 — Evaluation and Governance

**Phases:** P10, P11
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

The platform explains execution outcomes and distinguishes observed evidence from evaluator interpretation; human and autonomous workflows use one governance model and autonomous mode bypasses no control.

## Preconditions

- CP-06 PASS
- An execution run with a mix of pass, fail and non-executed items

## Functional test

What a human should be able to do:

1. Inspect a failed test; confirm the triage ladder ran and the classification names a cause
2. Confirm a failure caused by a missing precondition is classified data/state, not a product defect
3. Confirm an assertion failure with unknown oracle status is classified insufficient evidence, not a defect
4. Inspect an item that was never executed; confirm it carries an assessment and is excluded from pass-rate arithmetic
5. Submit a generated artifact to a gate in human mode; approve, edit and reject in turn
6. Confirm an edit created a new version, retained the machine original and re-derived confidence
7. Switch to autonomous mode; confirm the gate package is identical to the human one
8. Present an artifact with an unresolved deterministic failure to the autonomous gate; confirm it cannot be approved
9. Trigger an escalation; confirm it appears in the same queue as an exploration blocker

## Automated tests

- Unit: triage ladder ordering; evaluator layer precedence; policy evaluation
- Contract: `EvaluatorContract` (adding an evaluator with no orchestration change), `GateContract`, `FindingContract`
- Integration: full evaluation over a real execution run; both gate modes over identical artifacts
- Architecture: ARCH-6 — exactly one gate implementation exists
- Negative: a semantic verdict cannot overturn a deterministic one; a failed assertion alone cannot produce a product defect finding

## Test data

- An execution run containing passes, failures of several causes, and non-executed items
- Policies for both permissive and restrictive autonomous configurations

## Evidence

Stored under `docs/checkpoints/evidence/CP-07/`:

- Triage output classifying each failure by cause
- Findings with evidence and lineage to test case, scenario and understanding version
- Gate packages from human and autonomous modes over the same artifact, shown identical
- Audit log excerpt with actor, policy version and rationale per decision
- Test output proving autonomous mode cannot approve an artifact with an unresolved deterministic failure

## Expected result

Outcomes are explained and classified. One governance mechanism serves both modes, and
autonomy is a decider rather than a shortcut.

## Failure conditions

- A failed assertion alone producing a product defect finding
- A semantic verdict overturning a deterministic one
- More than one gate implementation
- Autonomous mode approving an artifact with an unresolved deterministic failure
- Non-executed items counted in pass rates
- A decision not attributable to an actor and policy version

## Exit criteria

- [ ] All Phase 10 and Phase 11 acceptance criteria met
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-07/`
- [ ] State and roadmap position updated

## Regression criteria

Must continue working from previous checkpoints:

| ID | Criterion |
|---|---|
| RG-1 | No artifact exists without a UAU version reference (INV-2) |
| RG-2 | No understanding element exists without resolvable evidence (INV-8) |
| RG-3 | No provider type appears in a domain module (INV-3) |
| RG-4 | Workers hold no database credentials (INV-10) |
| RG-5 | No secret appears in any artifact, log or prompt |
| RG-10 | Full test suite green in CI |
| RG-9 | Exactly one gate implementation exists (INV-9) |

---

## Result record

| Field | Value |
|---|---|
| Attempted on | — |
| Result | — |
| Evidence location | — |
| Known limitations | — |
| Recorded by | — |

*Results: PASS · PASS WITH KNOWN LIMITATION · BLOCKED · FAIL*
