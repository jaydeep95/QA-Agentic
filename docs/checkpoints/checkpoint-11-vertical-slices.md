# CP-11 — Integrated Vertical Slices

**Phase(s):** P12
**Vertical slice:** Slice E — the full journey
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

The complete Phase 1 journey runs end to end and delivers user value, on both the static-only path and the static-plus-exploration path.

## A. Product validation — what the QA user can do

A QA user can, from an empty workspace:
1. Register a repository set
2. Review the generated application understanding
3. Review coverage obligations and generated scenarios
4. Review test cases with their data requirements
5. Generate automation and bind it to an environment
6. Execute it for real
7. Read findings explaining what happened and why
8. See the understanding updated by what execution revealed

**This is Vertical Slice E**, and it must pass **twice**: once with exploration enabled and once with it disabled.

## B. Engineering validation — what the platform must prove internally

- The full chain wired: Source → Understanding → (Exploration) → Reconciliation → Coverage → Scenarios → Test Cases/Data → Automation → Execution → Evaluation → Findings → Knowledge Update
- Gates operating at every stage transition in both modes
- Every artifact carrying its UAU version
- The knowledge-update loop closing
- **The static-only path completing without exploration** (INV-4)

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-00 … CP-08 (CP-09 and CP-10 optional for the static-only path) |
| **Validation dependency** *(must exist before this can be fully validated)* | All prior checkpoints; a reference AUT and environment |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | CP-09 and CP-10 — required for the exploration path, not for the static-only path |
| **Parallel work** *(can safely proceed independently)* | None — this integrates everything |

## Preconditions

- CP-00 through CP-08 PASS. CP-09 and CP-10 PASS required only for the exploration path.

## Required components

All

## Functional test

1. Run the full journey with exploration **enabled**
2. Run the full journey with exploration **disabled**; confirm it completes with lower confidence and a larger insufficient-evidence share — not with failure
3. Trace a finding back to a repository revision, file and symbol in one query
4. Advance the AUT by one revision; rebuild; observe staleness surfaced and nothing auto-regenerated

## Automated tests

- Both end-to-end paths as automated tests
- Full regression suite across all prior checkpoints
- Resilience: induced worker termination at every stage boundary

## Required test data

A complete reference AUT repository set and environment · a second revision for the change scenario.

## Expected outcome

The architecture produces actual user value end to end, and does so without runtime exploration when it is unavailable.

## Evidence produced

Stored under `evidence/CP-11/`:

- Two end-to-end run records, one per path, with artifacts at every stage
- A finding traced to source in a single query
- Resumption evidence at every stage boundary
- Full regression suite result with commit hash

## Failure conditions

- **The static-only path failing to complete** — this is an architectural failure, not a limitation
- Any stage bypassing its gate
- An artifact without a UAU version
- The knowledge-update loop not closing

## Known limitations

Quality of each stage is bounded by its own checkpoint's limitations; those are carried forward and restated here rather than re-litigated.

## Exit criteria

- [ ] Both paths complete end to end
- [ ] All five vertical slices demonstrated
- [ ] Evidence captured under `evidence/CP-11/`
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
