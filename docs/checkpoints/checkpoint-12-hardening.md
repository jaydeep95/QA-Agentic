# CP-12 — Phase 1 Hardening & Release Readiness

**Phase(s):** P13
**Vertical slice:** All
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Phase 1 capabilities are functional within explicitly documented boundaries, all known gaps are visible, and the platform is fit to be handed over.

## A. Product validation — what the QA user can do

A QA user can:
1. Export scenarios, test cases and test data to a real `.xlsx` file and open it
2. See the platform's own known limitations stated in the product, not only in a document
3. Configure generation limits, concurrency and mode per workspace
4. Rely on runs surviving infrastructure interruption

## B. Engineering validation — what the platform must prove internally

- Reliability, concurrency and failure recovery under load
- Security review: no secrets in artifacts, logs or prompts; egress confined to the invoker; workspace scoping unbypassable
- Artifact integrity and provenance completeness
- Performance and observability against defined targets
- Full regression suite
- Excel export via an adapter with no domain coupling
- Deployment documented and reproducible

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-11 |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-11; quality-attribute targets defined (**OD-4, OD-7**) |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | None |
| **Parallel work** *(can safely proceed independently)* | None |

## Preconditions

- CP-11 PASS
- Quality-requirement targets defined — several are currently `UNKNOWN` pending OD-4 and OD-7

## Required components

All, plus export and deployment

## Functional test

1. Export all three artifact types to `.xlsx` and open the files
2. Review the product's own display of known limitations
3. Change generation limits and concurrency; confirm enforcement
4. Kill a worker mid-run; confirm correct resumption

## Automated tests

- Full suite: unit, contract, integration, end-to-end, architecture
- Security scans
- Resilience at every stage boundary
- Performance measurement against defined targets

## Required test data

Complete reference AUT and environment.

## Expected outcome

Phase 1 is functional within its documented boundaries and every known gap is visible.

## Evidence produced

Stored under `evidence/CP-12/`:

- Exported `.xlsx` files
- Security review report
- Performance measurements
- Full regression result with commit hash
- The product's own limitations display

## Failure conditions

- Any capability presented as complete while mocked
- A known limitation absent from the product display
- Any prior checkpoint regressing
- **A quality attribute claimed as hardened while having no defined target**

## Known limitations

Any quality attribute still `UNKNOWN` at this point must be recorded as an explicit gap, and CP-12 cannot claim to have hardened it.

## Exit criteria

- [ ] All Phase 13 acceptance criteria met
- [ ] Every known limitation documented and visible in the product
- [ ] Evidence captured under `evidence/CP-12/`
- [ ] Phase 1 declared complete in `12_CURRENT_IMPLEMENTATION_STATE.md`

## Regression criteria

All of RG-1 … RG-10, plus every functional test from CP-00 through CP-11 re-executed automatically.

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
