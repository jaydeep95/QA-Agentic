# CP-04 — Test Case and Data

**Phases:** P7
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

Real test cases can be created with explainable data requirements and an executable data-preparation strategy.

## Preconditions

- CP-03 PASS
- An approved scenario set

## Functional test

What a human should be able to do:

1. Generate test cases from approved scenarios
2. Inspect a case: confirm steps reference understanding elements, never locators
3. Confirm field semantics — mandatory, optional, enum, conditional, min/max — come from the understanding and match the real application
4. Inspect declared data requirements; confirm none are resolved at generation time
5. Resolve requirements against an environment; observe each ladder rung and the recorded reason the previous rung was insufficient
6. Trigger a requirement needing an entity that must already exist; confirm it routes to provisioning, not value generation, and that provisioning is policy-gated
7. Resolve the same case against a second environment without regenerating it

## Automated tests

- Unit: requirement classification by kind; ladder ordering; constraint-satisfying generation
- Contract: `TestCaseContract`, `DataRequirementContract`
- Integration: resolution against two environments; provisioning policy gate; cleanup recording
- Property: no test case contains a literal locator, environment value or resolved secret

## Test data

- Approved scenarios
- Two configured environments
- An approved test-data source, if one exists (OD-1 dependency)

## Evidence

Stored under `docs/checkpoints/evidence/CP-04/`:

- Generated test cases with declared requirements
- Resolution log showing each ladder rung and its failure reason
- The same case executed against two environments via different resolutions
- Provisioning record with policy decision and cleanup entry

## Expected result

Test intent is environment-independent; data resolution is explainable and policy-controlled.

## Failure conditions

- A test case containing a concrete locator or environment value
- Data resolved at generation time rather than bind time
- State provisioning treated as value generation
- Provisioning proceeding without a policy decision
- A resolved secret appearing in a stored test case

## Exit criteria

- [ ] All Phase 7 acceptance criteria met
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-04/`
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
| RG-7 | Coverage remains computed, not asserted |

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
