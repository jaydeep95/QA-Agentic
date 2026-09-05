# CP-08 — Phase 1 Integrated

**Phases:** P12, P13
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

A realistic end-to-end workflow completes against a supported AUT with durable evidence at every major stage, on both the static+exploration path and the static-only path, and Phase 1 capabilities are functional within their documented boundaries.

## Preconditions

- CP-00 through CP-07 all PASS
- All Phase 13 hardening work items complete
- Quality-requirement targets defined (OD-4, OD-7 resolved)

## Functional test

What a human should be able to do:

1. From an empty workspace, register a repository set and run the complete chain to findings
2. Repeat with exploration disabled; confirm the static-only path completes and produces scenarios, cases and automation at reduced confidence
3. Trace a finding all the way back to a repository revision, file, symbol and runtime artifact in one query
4. Export scenarios, test cases and test data to `.xlsx` and open the files
5. Advance the AUT by one revision; rebuild understanding; observe staleness surfaced and nothing auto-regenerated
6. Kill a worker mid-run; confirm the run resumes correctly
7. Review the product's own display of known limitations

## Automated tests

- Full suite: unit, contract, integration, end-to-end, architecture
- Both end-to-end paths as automated tests
- Regression suite covering every prior checkpoint
- Security: secret scan, egress confinement, workspace scoping cannot be bypassed
- Resilience: induced worker termination at every stage boundary

## Test data

- A complete reference AUT repository set and environment
- A second revision for the change-and-staleness scenario

## Evidence

Stored under `docs/checkpoints/evidence/CP-08/`:

- Two end-to-end run records, one per path, with artifacts at every stage
- A finding traced to source in a single query, captured
- Exported `.xlsx` files for scenarios, cases and data
- Resumption evidence for every stage boundary
- Security review report
- Full regression suite result with commit hash

## Expected result

Phase 1 capabilities are functional within their explicitly documented boundaries and all
known gaps are visible.

## Failure conditions

- The static-only path failing to complete
- Any capability presented as complete while mocked
- A known limitation absent from the product's own display
- Any prior checkpoint regressing
- A quality attribute claimed as hardened while having no defined target

## Exit criteria

- [ ] All Phase 12 and Phase 13 acceptance criteria met
- [ ] Every known limitation documented and visible in the product
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-08/`
- [ ] State and roadmap position updated
- [ ] Phase 1 declared complete in `12_CURRENT_IMPLEMENTATION_STATE.md`

## Regression criteria

Must continue working from previous checkpoints:

| ID | Criterion |
|---|---|
All of RG-1 through RG-10, plus every functional test from CP-00 through CP-07
re-executed as an automated regression suite.

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
