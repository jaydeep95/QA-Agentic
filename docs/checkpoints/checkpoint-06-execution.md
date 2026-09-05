# CP-06 — Real Execution

**Phase(s):** P9
**Vertical slice:** Slice C (part 2) — completes Slice C
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Real Playwright tests execute against a controlled environment in isolated workers, with complete results and artifacts persisted.

## A. Product validation — what the QA user can do

A QA user can:
1. Choose an environment and a concurrency level, and run a generated suite
2. Watch progress and cancel a run
3. Open a completed test and see its trace, video, screenshots, network and console logs
4. Compare this run against a previous one

**This completes Vertical Slice C:** *QA can run a generated automated test against a real environment.*

## B. Engineering validation — what the platform must prove internally

- Execution manager, policy gate, environment lease
- Job queue with one job per test case
- Three composing concurrency ceilings (global, per-environment, per-run)
- Isolated workers with in-worker secret resolution and log redaction
- Browser lifecycle; artifact capture and storage by reference
- Retry classification before attempt; each retry a distinct attempt
- Cancellation with partial artifact retention
- Run history and comparison on content-derived identity

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-05; CP-00 (queue and worker harness) |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-05; a reachable AUT environment with test identities |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | CP-09 validated locators — materially raises pass rate |
| **Parallel work** *(can safely proceed independently)* | CP-08 governance; CP-10 |

## Preconditions

- CP-05 PASS
- A reachable AUT environment with non-interactive test identities per role
- Environment definitions with domain allowlists and data-safety class

## Required components

Execution manager · queue · concurrency controller · workers · artifact capture · run history

## Functional test

1. Run with concurrency 1; observe sequential execution
2. Run with concurrency 4; confirm the effective limit is the minimum of three ceilings
3. Open an attempt; confirm trace, video, screenshots, network and console logs present
4. Force an infrastructure failure; confirm automatic retry as a distinct attempt
5. Force an assertion failure; confirm it is **not** retried
6. Cancel a run; confirm partial artifacts retained and the run marked partially complete
7. Search all logs and artifacts for a known secret; find none
8. Run twice; compare on stable identity

## Automated tests

- Unit: concurrency ceiling composition; retry classification
- Integration: real Playwright execution in isolated workers; lease acquisition/release; in-worker secret resolution; artifact capture; cancellation; resumption after induced worker kill
- Negative: worker cannot open a database connection
- Security: secret scan across all produced artifacts and logs

## Required test data

A reachable AUT environment · test identities as secret references · a generated suite from CP-05.

## Expected outcome

Real tests execute in isolation, produce complete evidence, and honour every configured ceiling.

## Evidence produced

Stored under `evidence/CP-06/`:

- Execution records with attempts, timings and artifact references
- Trace, video and screenshots for at least one passing and one failing test
- Concurrency demonstration at 1 and 4
- Retry classification log
- Secret-scan report: zero findings
- Run-over-run comparison output

## Failure conditions

- Execution running inside the API process
- A worker holding database credentials
- A secret discoverable in an artifact or log
- An assertion failure retried automatically
- Retries overwriting attempts
- **Simulated execution presented as real execution**

## Known limitations

Pass rate is bounded by locator quality; without CP-09 validation, failures may reflect locator resolution rather than the AUT. Triage (CP-07) is what distinguishes them.

## Exit criteria

- [ ] All Phase 9 acceptance criteria met
- [ ] **Vertical Slice C demonstrated end to end**
- [ ] Evidence captured under `evidence/CP-06/`
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
