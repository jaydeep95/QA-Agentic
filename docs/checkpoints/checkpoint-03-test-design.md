# CP-03 — Test Design & Coverage

**Phase(s):** P6
**Vertical slice:** Slice B (part 1)
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Scenarios are generated from explicit coverage obligations derived from the understanding, and coverage is computed rather than asserted.

## A. Product validation — what the QA user can do

A QA user can:
1. See the coverage obligations derived from the application understanding, and trace any obligation back to the element that produced it
2. Generate scenarios for a feature area within configured limits
3. See, per scenario, which technique(s) were selected and **why**
4. See a coverage report stating its denominator, understanding version and unverified share
5. Exclude an obligation with a recorded reason
6. See obligations resting on unverified elements reported as `insufficient evidence` — counted as neither covered nor uncovered

## B. Engineering validation — what the platform must prove internally

- Obligation model and derivation from understanding elements
- Five obligation states including `insufficient evidence` and `not applicable`
- Technique registry: all eight techniques as strategies, each declaring the element types it consumes
- Evidence-gated technique selection with recorded rationale
- Scenario generation workflow with deduplication by content identity, gap discovery and coverage analysis
- Configurable generation limits
- **No code path by which a model can set a coverage value** (ARCH-4)

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-02 (a stabilised understanding version) |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-02; an independently authored manual inventory for Validation F |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | CP-10 reconciliation — raises confidence and reduces the insufficient-evidence share; **not required** |
| **Parallel work** *(can safely proceed independently)* | CP-09, CP-10 |

## Preconditions

- CP-02 PASS (CP-09 and CP-10 are **not** required — static-only design is fully supported, ADR-002)

## Required components

Coverage · obligation ledger · technique registry · scenario generation

## Functional test

1. Open the obligation ledger; trace an obligation to its source element
2. Generate scenarios for a feature area
3. Inspect a scenario: technique(s), rationale, obligation links, evidence
4. Find a feature with no rule elements; confirm decision-table generation was not selected
5. View the coverage report; confirm denominator, version and unverified share are stated
6. Exclude an obligation; confirm reason and actor recorded
7. Change a generation limit; confirm enforcement

## Automated tests

- Unit: obligation derivation; coverage arithmetic across five states; deduplication
- Contract: `ScenarioContract`; registering a ninth technique with **no orchestration change**
- Integration: generation against a stabilised understanding; limit enforcement
- Architecture: ARCH-4 — no public path sets a coverage value
- Property: every generated scenario links to ≥1 obligation

## Required test data

A stabilised understanding version · an independently authored manual test inventory for one feature area (Validation F).

## Expected outcome

Coverage is bookkeeping over explicit links. No model is asked what the coverage is.

## Evidence produced

Stored under `evidence/CP-03/`:

- Obligation ledger export with derivation traceability
- Generated scenario set with technique rationale
- Coverage report with all qualifiers
- Validation F comparison against the human inventory (THR-COV-001/002)
- Test output proving no code path can set a coverage value

## Failure conditions

- Any code path permitting a model to set a coverage value
- A coverage figure reported without denominator, version and unverified share
- A technique applied where the understanding does not support it
- A scenario with no obligation link
- Adding a technique requiring an orchestration change

## Known limitations

Coverage is coverage **of the understanding model**, which is itself incomplete. Reports must carry that qualifier. Rule-derived obligations inherit the known-partial rule recall from CP-02.

## Exit criteria

- [ ] All Phase 6 acceptance criteria met
- [ ] Validation F recorded against THR-COV-001/002
- [ ] All six product-validation steps demonstrated
- [ ] Evidence captured under `evidence/CP-03/`
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
