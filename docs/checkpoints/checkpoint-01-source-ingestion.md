# CP-01 — Source Ingestion

**Phase(s):** P1
**Vertical slice:** Slice A (part 1)
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

A real frontend/backend repository set can be registered, acquired, pinned and represented durably as a workspace version tuple.

## A. Product validation — what the QA user can do

A QA user can:
1. Register a frontend repository and a backend repository into one workspace, supplying URL, branch/ref and a credential reference
2. See each resolve to a pinned revision, and see the workspace's combined version
3. Re-register the same revision and observe no duplication

## B. Engineering validation — what the platform must prove internally

- Repository registration model capturing ref and credential reference
- Multi-repository workspace with a version tuple across all repositories
- Git acquisition into an ephemeral, credential-free sandbox
- Revision resolution and pinning, immutable once referenced
- Per-file content hashing as the foundation for evidence anchoring
- Repository/version lineage records

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-00 |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-00 |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | None |
| **Parallel work** *(can safely proceed independently)* | CP-03 obligation modelling design; provider research |

## Preconditions

- CP-00 PASS

## Required components

Source ingestion · repository model · sandbox acquisition · lineage

## Functional test

1. Register two repositories into one workspace with ref and credential reference
2. Observe pinned revisions and the workspace version tuple
3. Trigger acquisition; confirm source lands in an isolated sandbox
4. Confirm no credential persists after acquisition
5. Re-register the same revision; confirm idempotency
6. Register a second revision; confirm both are addressable

## Automated tests

- Unit: version tuple formation; revision pinning immutability
- Integration: clone into sandbox; credential lifecycle; content hashing; idempotent re-registration
- Negative: registration rejected when ref or credential reference is missing

## Required test data

Two real, public Git repositories (a frontend and a backend) pinned at fixed revisions. The Phase 1 AUT is **OPEN (OD-3)** — public stand-ins are acceptable for this checkpoint.

## Expected outcome

A workspace durably represents a multi-repository application at an exact, reproducible version.

## Evidence produced

Stored under `evidence/CP-01/`:

- Persisted workspace with a two-repository version tuple
- Acquisition log showing sandbox isolation and credential lifecycle
- Content-hash manifest
- Idempotency test output

## Failure conditions

- Registration accepting a repository without a ref or credential reference
- A credential persisting after acquisition
- Re-registration creating duplicate records
- Version tuple representing only one repository

## Known limitations

Acquisition only — no parsing or understanding. Write-back to the AUT repository is out of Phase 1 scope.

## Exit criteria

- [ ] All Phase 1 acceptance criteria met
- [ ] All three product-validation steps demonstrated
- [ ] Evidence captured under `evidence/CP-01/`
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
