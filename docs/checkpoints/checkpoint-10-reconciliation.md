# CP-10 — Static/Runtime Reconciliation

**Phase(s):** P5
**Vertical slice:** Enrichment of Slices A, B and D
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Static and runtime evidence are compared and the result is explainable and traceable — **developed and validated without requiring a live crawler**.

## A. Product validation — what the QA user can do

A QA user can:
1. See, per understanding element, whether static and runtime agree
2. See all six statuses and what each means
3. Open a difference and see evidence from **both** sides
4. See that a mismatch is presented as a classified candidate with a stated explanation — never auto-labelled a defect
5. See confidence change traceably as a result of reconciliation

## B. Engineering validation — what the platform must prove internally

- Deterministic set algebra over element identity — **not fuzzy matching**
- Six statuses: matched, runtime-only, static-only, different, conflict, unverified
- Four deterministic checks before any defect candidate is raised
- All six explanations for a difference expressible
- Confidence updates traceable to a reconciliation event
- Mismatch candidates surfaced to CP-07 findings without becoming findings themselves

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | **CP-02 only.** Runtime observations may come from recorded sessions, synthetic
observations, replay fixtures or controlled observation datasets. **A live CrawlerProvider is NOT a
development dependency** — see [`../05_DETAILED_DEVELOPMENT_ROADMAP.md`](../05_DETAILED_DEVELOPMENT_ROADMAP.md) and ADR-002 / INV-4 |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-02; a seeded-discrepancy dataset; Validation D executed |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | **CP-09 live exploration** — replaces fixtures with real observations and enables live end-to-end validation. Enrichment, not prerequisite |
| **Parallel work** *(can safely proceed independently)* | CP-03 through CP-08 |

## Preconditions

- CP-02 PASS
- A runtime observation dataset from **any** admissible source: recorded session, synthetic
  generation, replay fixture, or live exploration if CP-09 is available
- A seeded-discrepancy dataset for Validation D

## Required components

Reconciliation engine · status model · mismatch classifier · confidence updater

## Functional test

1. Run reconciliation over a static understanding and an observation dataset
2. Inspect the result; confirm all six statuses appear
3. Open a `different`; confirm evidence from both sides
4. Open a `conflict`; confirm it is a classified candidate, **not** labelled a defect
5. Confirm an `unverified` element is treated as not-yet-observed, never as absent
6. Trace a confidence change to its reconciliation event

## Automated tests

- Unit: set algebra producing all six statuses from constructed populations
- Integration: reconciliation against a seeded-discrepancy dataset using **fixture observations**
- Integration (enrichment): the same run using live observations once CP-09 is available
- Negative: no path by which reconciliation emits a finding directly

## Required test data

A static understanding version · an observation dataset (**recorded, synthetic or fixture — live not required**) ·
a seeded-discrepancy branch with a removed confirmation step, a renamed response property and an endpoint with no UI path.

## Expected outcome

Differences between expectation and observation are explained, classified and traceable, with the highest-value oracle-free defect candidates surfaced for triage.

## Evidence produced

Stored under `evidence/CP-10/`:

- Reconciliation report with all six statuses
- Classified mismatches with both-sided evidence
- Validation D result (THR-RECON-001/002/003)
- Confidence-change audit trail
- **Explicit note of which observation source was used** (fixture / recorded / synthetic / live)

## Failure conditions

- A mismatch auto-labelled as a defect
- Fuzzy matching instead of identity-based set algebra
- A status assigned without evidence from the relevant side
- **A fixture-based run presented as live reconciliation**
- Reconciliation blocked solely because no live crawler exists — this would violate ADR-002

## Known limitations

Fixture-based validation proves the engine, not the ecosystem. Live reconciliation quality is validated once CP-09 is available and is recorded separately.

## Exit criteria

- [ ] All Phase 5 acceptance criteria met
- [ ] Validation D recorded
- [ ] Observation source explicitly stated in the result record
- [ ] Evidence captured under `evidence/CP-10/`
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
