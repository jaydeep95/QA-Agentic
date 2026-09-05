# CP-04 — Test Case & Test Data

**Phase(s):** P7
**Vertical slice:** Slice B (part 2) — completes Slice B
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Executable test intent is generated with explainable, declared data requirements and a working preparation strategy — without resolving data at generation time.

## A. Product validation — what the QA user can do

A QA user can:
1. Generate test cases from approved scenarios and read them as steps a tester would follow
2. See field semantics that match the real application — mandatory, optional, enum, conditional, min/max
3. See what data each case *requires*, expressed in business terms, with nothing resolved yet
4. Resolve requirements against an environment and see how each value was obtained, including why earlier strategies were insufficient
5. Run the same test case against a second environment without regenerating it

**This completes Vertical Slice B:** *QA can generate and inspect evidence-backed test scenarios and cases from the application understanding.*

## B. Engineering validation — what the platform must prove internally

- Test case generation with semantic element references — never locators
- Data requirement declaration by kind: value, identity, entity, relationship, environment precondition
- The five-rung resolution ladder, each rung recording why the previous was insufficient
- **State provisioning as a separate, policy-gated capability** distinct from value generation
- Cleanup recording for provisioned state
- Oracle status carried per expectation

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-03 |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-03; a configured environment for resolution |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | An approved test-data source (rung 2) — without it the ladder falls through to synthesis or provisioning |
| **Parallel work** *(can safely proceed independently)* | CP-05 automation specification design; CP-09; CP-10 |

## Preconditions

- CP-03 PASS
- An approved scenario set
- At least one configured environment; a second for the portability test

## Required components

Test case generation · data requirement model · resolution ladder · state provisioning

## Functional test

1. Generate test cases from approved scenarios
2. Confirm steps reference understanding elements, never locators
3. Confirm field semantics match the real application
4. Inspect declared data requirements; confirm nothing is resolved
5. Resolve against an environment; observe each ladder rung and its recorded reason
6. Trigger a requirement needing a pre-existing entity; confirm it routes to provisioning, is policy-gated, and is recorded for cleanup
7. Resolve the same case against a second environment without regeneration

## Automated tests

- Unit: requirement classification by kind; ladder ordering; constraint-satisfying generation
- Contract: `TestCaseContract`, `DataRequirementContract`
- Integration: resolution against two environments; provisioning policy gate; cleanup recording
- Property: no test case contains a literal locator, environment value or resolved secret

## Required test data

Approved scenarios · two configured environments · an approved test-data source if one exists (**OPEN — OD-1**).

## Expected outcome

Test intent is environment-independent; data resolution is explainable and policy-controlled.

## Evidence produced

Stored under `evidence/CP-04/`:

- Generated test cases with declared requirements
- Resolution log showing each rung and its failure reason
- The same case resolved against two environments
- Provisioning record with policy decision and cleanup entry

## Failure conditions

- A test case containing a concrete locator or environment value
- Data resolved at generation time
- State provisioning treated as value generation
- Provisioning proceeding without a policy decision
- A resolved secret stored in a test case

## Known limitations

Rung 4 provisioning is available only through approved paths and only where the environment write policy permits it (**OD-1**). Where it does not, affected cases carry an unresolved data requirement rather than failing silently.

## Exit criteria

- [ ] All Phase 7 acceptance criteria met
- [ ] **Vertical Slice B demonstrated end to end**
- [ ] Evidence captured under `evidence/CP-04/`
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
