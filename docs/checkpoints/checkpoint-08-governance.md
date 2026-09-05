# CP-08 — Governance

**Phase(s):** P11
**Vertical slice:** Cross-cutting
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Human and autonomous workflows use one governance mechanism, and autonomous mode bypasses no control. Foundations were built at CP-00; this validates the complete capability.

## A. Product validation — what the QA user can do

A QA user can:
1. Review a generated artifact at a gate and approve, edit or reject it
2. See that an edit preserved the machine original and re-derived confidence
3. Record a rejection reason and find it later in the audit trail
4. Switch a stage to autonomous mode and see the identical gate package used
5. See an escalation raised when policy cannot proceed — in the same queue as an exploration blocker
6. Review the full audit trail of who decided what, when and under which policy version

## B. Engineering validation — what the platform must prove internally

- Exactly one gate implementation (ARCH-6) serving both deciders
- Gate package: artifact, verdicts, confidence distribution, oracle coverage, unresolved conflicts, budget
- Approval states; immutable approved versions
- Human edit creating a new version retaining the machine original
- Policy evaluation with versioning; version recorded on every autonomous decision
- Escalation as one mechanism for three cases
- Append-only audit; every decision attributable to actor and policy version
- **Autonomous mode cannot approve an artifact with an unresolved deterministic failure**

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-00 (governance foundations) |
| **Validation dependency** *(must exist before this can be fully validated)* | At least one generating stage — CP-03 or later — to gate |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | CP-09 exploration blockers exercise the third escalation path |
| **Parallel work** *(can safely proceed independently)* | CP-03 through CP-07 — governance is built alongside, not after |

## Preconditions

- CP-00 PASS; at least one generating capability available to gate

## Required components

Gate · policy engine · approval states · escalation · audit

## Functional test

1. Submit an artifact to a gate in human mode; approve, edit and reject in turn
2. Confirm the edit created a new version, retained the machine original, re-derived confidence
3. Switch to autonomous mode; confirm the gate package is identical
4. Present an artifact with an unresolved deterministic failure; confirm autonomous mode cannot approve it
5. Trigger an escalation; confirm it appears in the shared queue
6. Review the audit trail

## Automated tests

- Unit: policy evaluation; approval state transitions
- Contract: `GateContract`
- Integration: both modes over identical artifacts
- Architecture: ARCH-6 — exactly one gate implementation
- Negative: autonomous approval blocked on unresolved deterministic failure

## Required test data

Generated artifacts from any producing stage · permissive and restrictive policy configurations.

## Expected outcome

One governance mechanism; autonomy is a decider, never a shortcut.

## Evidence produced

Stored under `evidence/CP-08/`:

- Gate packages from both modes over the same artifact, shown identical
- Audit log excerpt with actor, policy version and rationale
- Test output proving autonomous mode cannot bypass a deterministic failure
- An escalation work item

## Failure conditions

- More than one gate implementation
- Autonomous mode approving an artifact with an unresolved deterministic failure
- A decision not attributable to an actor and a policy version
- An approved version subsequently mutated

## Known limitations

Rich policy configuration UI may lag the engine; where so, policies are configured as data and this is recorded as a known limitation.

## Exit criteria

- [ ] All Phase 11 acceptance criteria met
- [ ] All six product-validation steps demonstrated
- [ ] Evidence captured under `evidence/CP-08/`
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
