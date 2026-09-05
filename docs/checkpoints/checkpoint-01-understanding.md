# CP-01 — Understanding

**Phases:** P2, P3
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

Given a supported real application repository set, the platform creates a durable, inspectable, evidence-backed Unified Application Understanding.

## Preconditions

- CP-00 PASS
- OD-3 resolved: the Phase 1 AUT and its technology stack are known
- Validation A executed (relationship extraction quality measured)
- A ground-truth corpus exists for the reference AUT

## Functional test

What a human should be able to do:

1. Trigger an understanding build for a registered repository set
2. Open the understanding document projection; read it as a human would
3. Inspect any element and reach its supporting evidence at source location in two interactions
4. Confirm every element shows inference type, oracle status where applicable, reconciliation status and a computed confidence
5. Find an unresolved relationship; confirm it is marked `unresolved`, not guessed at
6. Re-run the build on the same revision; confirm idempotency
7. Advance one repository to a new revision, rebuild, and view the understanding diff
8. Confirm artifacts derived from the prior version are marked stale, and that nothing auto-regenerated

## Automated tests

- Unit: normalisation and content-derived identity determinism; corroboration; confidence prior assignment
- Contract: `UnderstandingQuery`, `UnderstandingContribution`; `CodeIntelligenceProvider` conformance including the **adapter-absent** degraded-mode test
- Integration: full extraction pipeline against the reference AUT; incremental rescan; staleness cascade; projection rebuild with no data loss
- Architecture: ARCH-3, ARCH-7 re-verified
- Calibration: per-extractor precision and recall reported by element type, never averaged

## Test data

- The reference AUT repository set pinned at a fixed revision
- A hand-written ground-truth corpus covering entities, schema, rules, workflows and relationships
- A second revision of the same repositories for diff and staleness testing

## Evidence

Stored under `docs/checkpoints/evidence/CP-01/`:

- A persisted understanding version pinning the full repository tuple
- Calibration report: per-extractor precision/recall by element type
- Understanding document projection export
- Understanding diff output between two versions
- Staleness cascade record showing affected downstream artifacts
- Degraded-mode test output proving the platform builds understanding with the Code Intelligence adapter removed

## Expected result

A real repository set yields a versioned, evidence-backed understanding whose quality is
measured rather than asserted, and whose weak areas are visibly marked rather than hidden.

## Failure conditions

- Any element without a resolvable evidence reference
- A relationship produced from transitive reachability alone (rule R3)
- An unresolvable path segment wildcard-matched to an endpoint (rule R1)
- Confidence set anywhere by a model rather than computed
- A mock or fixture understanding presented as an extraction result
- Calibration reporting a single averaged accuracy figure

## Exit criteria

- [ ] All Phase 2 acceptance criteria met
- [ ] Phase 3 acceptance criteria met (port defined, conformance suite passing, degraded mode proven)
- [ ] Validation A result recorded, with consequences applied if below threshold
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-01/`
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
