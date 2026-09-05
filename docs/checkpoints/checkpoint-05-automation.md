# CP-05 — Automation

**Phases:** P8
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

Generated automation is maintainable, executable and structurally reusable rather than a collection of copied scripts, and healing cannot alter intent.

## Preconditions

- CP-04 PASS
- Locator candidates available from the understanding; runtime-validated candidates if CP-02 passed
- Validation E executed

## Functional test

What a human should be able to do:

1. Generate an automation specification from an approved test case
2. Confirm the specification contains semantic element references and assertions, and **no** locators or environment values
3. Generate bindings for two environments from the one specification
4. Inspect the page and component objects; confirm they are projected from the understanding and shared across test cases
5. Introduce a markup change in the AUT; run the suite; observe locator failures and healing **proposals**
6. Confirm auto-apply is disabled by default and healing produced proposals, not silent changes
7. Accept a proposal; confirm only the binding changed and the specification version is untouched
8. Inspect the healing event record: before, after, evidence, score

## Automated tests

- Unit: locator ranking by strategy stability; healing candidate scoring
- Contract: `AutomationSpecification`/`Binding` conformance, including the structural test that **the healing path cannot write to specification storage**
- Integration: one specification against two environments; object model regeneration after an understanding change
- Architecture: ARCH-5 — healing components have no write access to specification storage
- Measurement: object reuse across the generated suite

## Test data

- Approved test cases
- Two configured environments
- A markup-churn branch of the AUT: renamed classes, restructured containers, a relocated control

## Evidence

Stored under `docs/checkpoints/evidence/CP-05/`:

- Generated specification and two bindings
- Page/component object model with measured reuse across the suite
- Suite pass rate before and after markup churn
- Healing proposals with precision measured by hand review (Validation E)
- Test output proving an attempted assertion modification through the healing path fails

## Expected result

Reuse is structural. Healing repairs how an element is found and is structurally incapable
of changing what the test means.

## Failure conditions

- **A test that modifies an assertion through the healing path succeeding**
- A specification containing locators or environment values
- Per-test object generation instead of a shared projected object model
- Auto-apply enabled by default
- A healing event applied without a recorded before, after, evidence and score

## Exit criteria

- [ ] All Phase 8 acceptance criteria met
- [ ] Validation E recorded
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-05/`
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
| RG-8 | The healing path cannot write to specification storage (INV-6) |

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
