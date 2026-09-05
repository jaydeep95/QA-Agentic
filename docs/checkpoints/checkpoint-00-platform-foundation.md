# CP-00 — Platform Foundation

**Phase(s):** P0
**Vertical slice:** Prerequisite for Slice A
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

The platform boots, persists state, executes controlled workflows, stores artifacts, invokes external capabilities through one path, and runs its own test suite reliably. Governance *foundations* exist from the start rather than arriving late.

## A. Product validation — what the QA user can do

A QA user can:
1. Reach a running platform and authenticate
2. Create a workspace and see it persist across a restart

This is deliberately thin — CP-00 is infrastructure. The product surface is proven from CP-01 onward.

## B. Engineering validation — what the platform must prove internally

- Domain primitives: workspace, artifact, version, lineage, evidence, status
- Canonical store with migrations and workspace scoping that cannot be bypassed
- Object storage abstraction with payloads held by reference
- API foundation plus a narrow authenticated worker callback
- Durable run/stage state machine and job queue
- Worker harness: lease, heartbeat, idempotency, cancellation, resumption
- Capability registry and single Capability Invoker
- Secret provider port with reference resolution
- **Governance foundations** (moved earlier in V2): gate contract, decision state model, approval state, audit record, human/autonomous decider abstraction, mandatory policy check hook
- Test framework, CI, and the architecture boundary check

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | None — this is the root |
| **Validation dependency** *(must exist before this can be fully validated)* | None |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | None |
| **Parallel work** *(can safely proceed independently)* | Provider research (ADR-040, ADR-041); contract refinement in doc 04 |

## Preconditions

- DEC-002 / ADR-059 resolved: control-plane language and runtime chosen and recorded
- `.gitignore` and version control in place (P0-W1-T1, COMPLETE)

## Required components

Domain core · persistence · artifact store · API · workflow · queue · worker harness · capability invoker · secret port · governance foundations · test framework

## Functional test

1. Follow the documented setup and start the platform locally
2. Authenticate and create a workspace through the API
3. Restart the platform; confirm the workspace persists
4. Create an artifact, attach evidence, query its lineage
5. Enqueue a job; observe a worker execute it and post the result via callback
6. Attempt a direct database connection from a worker; observe it fail
7. Invoke a registered capability; observe the invocation recorded as provenance
8. Submit an artifact to a governance gate; record a decision; observe it in the audit log
9. Search all logs and stored artifacts for a known secret; find none

## Automated tests

- Unit: domain primitives, artifact versioning, lineage, evidence attachment, status transitions, gate decision states
- Contract: `Capability`, `LanguageModelProvider` (**at least one real adapter** — see ADR-030), `GateContract`
- Integration: migrations; workspace scoping cannot be bypassed; queue lease/heartbeat/idempotency/cancellation; worker resumption after induced kill; artifact round-trip; secret resolution in-worker
- Architecture: ARCH-1 (no provider imports in domain), ARCH-2 (module dependency graph), ARCH-3 (no nullable UAU version), ARCH-6 (one gate implementation), ARCH-7 (no element without evidence)

## Required test data

A test secret registered in the secret provider. No repository and no AUT environment required.

## Expected outcome

The platform is a working skeleton with governance and provider boundaries already enforced, not retrofitted.

## Evidence produced

Stored under `evidence/CP-00/`:

- CI run URL and commit hash, full suite green
- Persisted workspace surviving a restart
- Artifact with resolvable evidence and queryable lineage
- Queue trace: enqueue → lease → execute → callback → result
- Negative-test output: worker database connection refused
- Gate decision record in the audit log
- Secret-scan report over all artifacts and logs: zero findings

## Failure conditions

- Any architecture test failing
- A worker able to reach the database
- A secret discoverable in any artifact or log
- An artifact creatable without a UAU version field
- More than one gate implementation
- **Any P0 capability demonstrated only through a test double**

## Known limitations

Governance is foundations only — rich approval UI, policy configuration, autonomous behaviour and escalation arrive at CP-08.

## Exit criteria

- [ ] All Phase 0 acceptance criteria met
- [ ] Both product-validation steps demonstrated
- [ ] Evidence captured under `evidence/CP-00/`
- [ ] `12_CURRENT_IMPLEMENTATION_STATE.md` and the roadmap position updated

## Regression criteria

None — CP-00 establishes the regression baseline RG-1 … RG-10.

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
