# CP-06 — Execution

**Phases:** P9
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

The platform executes real Playwright tests against a controlled environment and persists complete results and artifacts.

## Preconditions

- CP-05 PASS
- A reachable AUT environment with test identities
- Environment definitions with allowlists and data-safety class

## Functional test

What a human should be able to do:

1. Request an execution run with concurrency set to 1; observe sequential execution
2. Repeat with concurrency 4; observe four parallel tests and confirm the effective limit is the minimum of the three ceilings
3. Inspect a completed attempt: trace, video, screenshots, network log, console log all present and stored by reference
4. Force an infrastructure failure; confirm automatic retry, recorded as a distinct attempt
5. Force an assertion failure; confirm it is **not** retried
6. Cancel a running execution; confirm partial artifacts are retained and the run is marked partially complete
7. Search all logs and artifacts for a known secret; find none
8. Run the same suite twice; compare runs on stable identity

## Automated tests

- Unit: concurrency ceiling composition; retry classification
- Integration: real Playwright execution in isolated workers; environment lease acquisition and release; secret resolution in-worker; artifact capture and storage; cancellation with partial retention; resumption after induced worker kill
- Negative: worker cannot open a database connection
- Security: secret scan across all produced artifacts and logs

## Test data

- A reachable AUT environment
- Test identities per role, stored as secret references
- A generated suite from CP-05

## Evidence

Stored under `docs/checkpoints/evidence/CP-06/`:

- Execution run records with attempts, timings and artifact references
- Trace, video and screenshot artifacts for at least one passing and one failing test
- Concurrency demonstration at 1 and 4
- Retry classification log
- Secret-scan report showing zero findings
- Run-over-run comparison output

## Expected result

Real tests execute in isolation, produce complete evidence, and honour every configured ceiling.

## Failure conditions

- Execution running inside the API process
- A worker holding database credentials
- A secret discoverable in an artifact or log
- An assertion failure retried automatically
- Retries overwriting attempts rather than creating distinct ones
- Simulated execution presented as real execution

## Exit criteria

- [ ] All Phase 9 acceptance criteria met
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-06/`
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
