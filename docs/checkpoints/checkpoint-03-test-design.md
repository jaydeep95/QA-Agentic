# CP-03 — Test Design

**Phases:** P6
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

Scenarios are generated from explicit application and coverage obligations, and coverage is computed rather than asserted.

## Preconditions

- CP-01 PASS (CP-02 is not required — static-only design is supported)
- A stabilised understanding version for the reference AUT

## Functional test

What a human should be able to do:

1. Open the obligation ledger; trace an obligation back to the understanding element that produced it
2. Generate scenarios for a feature area
3. Inspect a scenario: confirm the technique(s) selected, the recorded rationale and the obligation links
4. Find a feature with no rule elements; confirm decision-table generation was **not** selected for it
5. View the coverage report; confirm it states denominator, understanding version and unverified share
6. Confirm obligations resting on unverified elements appear as `insufficient evidence`, counted as neither covered nor uncovered
7. Exclude an obligation; confirm a reason and actor are recorded
8. Change a generation limit and confirm it is enforced

## Automated tests

- Unit: obligation derivation; coverage arithmetic across all five states; deduplication by content identity
- Contract: `ScenarioContract`; registering a ninth technique with **no orchestration change**
- Integration: end-to-end generation against a stabilised understanding; limit enforcement
- Architecture: ARCH-4 — no public path allows setting a coverage value
- Property: every generated scenario links to at least one obligation

## Test data

- A stabilised understanding version
- An independently authored manual test inventory for one feature area (for Validation F)

## Evidence

Stored under `docs/checkpoints/evidence/CP-03/`:

- Obligation ledger export with derivation traceability
- Generated scenario set with technique rationale
- Coverage report with denominator, version and unverified share
- Validation F comparison against the human inventory
- Test output proving no code path can set a coverage value

## Expected result

Coverage is bookkeeping over explicit links. No model is asked what the coverage is.

## Failure conditions

- Any code path permitting a model to set a coverage value
- A coverage figure reported without denominator, version and unverified share
- A technique applied where the understanding does not support it
- A scenario with no obligation link
- Adding a technique requiring an orchestration change

## Exit criteria

- [ ] All Phase 6 acceptance criteria met
- [ ] Validation F recorded
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-03/`
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
| RG-7 | No code path allows a model to set a coverage value (INV-7) |

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
