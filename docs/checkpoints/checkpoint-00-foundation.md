# CP-00 — Foundation

**Phases:** P0, P1
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

The platform boots, persists state, executes controlled workflows, stores artifacts and runs its own test suite reliably. A real frontend/backend repository set can be registered, acquired, versioned and represented durably.

## Preconditions

- `/docs` source-of-truth tree exists (satisfied by the governance bootstrap)
- DEC-002 resolved: control-plane language and runtime chosen
- No prior checkpoint — this is the first

## Functional test

What a human should be able to do:

1. Clone the repository and follow the documented setup steps; the platform starts locally
2. Create a workspace through the API
3. Register two repositories (frontend and backend) with URL, ref and credential reference
4. Observe both resolve to pinned revisions and the workspace expose a version tuple
5. Trigger source acquisition; observe source land in an isolated sandbox
6. Create an artifact, attach evidence, and query its lineage back to the repository revision
7. Enqueue a job; observe a worker pick it up and post its result through the callback API
8. Attempt a direct database connection from a worker; observe it fail
9. Invoke a registered capability; observe the invocation recorded as provenance
10. Search all logs and stored artifacts for a known secret value; find none

## Automated tests

- Unit: domain primitives, artifact versioning, lineage, evidence attachment, status transitions
- Contract: `Capability`, `LanguageModelProvider` (both adapters), `UnderstandingContribution` skeleton
- Integration: migrations, workspace scoping cannot be bypassed, queue lease/heartbeat/idempotency/cancellation, worker resumption after induced kill, artifact storage round-trip and reference resolution, secret resolution in-worker
- Architecture: ARCH-1 (no provider imports in domain), ARCH-2 (module dependency graph), ARCH-3 (no nullable UAU version on artifact types), ARCH-7 (no element without evidence)

## Test data

- Two real, public Git repositories (a frontend and a backend) pinned at fixed revisions
- A test secret registered in the secret provider, used to prove absence from logs and artifacts
- No AUT environment required — Phase 0/1 do not run browsers

## Evidence

Stored under `docs/checkpoints/evidence/CP-00/`:

- CI run URL and commit hash with the full suite green
- Persisted workspace record showing a two-repository version tuple
- Persisted artifact with a resolvable evidence reference and queryable lineage
- Queue trace: enqueue → lease → execute → callback → recorded result
- Negative-test output: worker database connection refused
- Secret-scan report over all artifacts and logs showing zero findings

## Expected result

All ten functional steps complete. The full automated suite passes. Worker isolation and secret absence are demonstrated by test, not assertion.

## Failure conditions

- Any architecture test failing
- A worker able to reach the database
- A secret discoverable in any artifact or log
- An artifact creatable without a UAU version field on its type
- Repository registration lacking ref or credential reference
- **Any capability required by P0 or P1 demonstrated only through a mock**

## Exit criteria

- [ ] All Phase 0 acceptance criteria in the roadmap met
- [ ] All Phase 1 acceptance criteria in the roadmap met
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-00/`
- [ ] `12_CURRENT_IMPLEMENTATION_STATE.md` updated
- [ ] PROJECT POSITION block in the roadmap updated

## Regression criteria

Must continue working from previous checkpoints:

| ID | Criterion |
|---|---|
No prior checkpoints. This checkpoint **establishes** the regression baseline
RG-1 through RG-5 and RG-10 for everything that follows.

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
