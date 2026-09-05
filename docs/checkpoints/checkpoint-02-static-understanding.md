# CP-02 — Static Application Understanding

**Phase(s):** P2, P3 (contract only)
**Vertical slice:** Slice A (part 2) — completes Slice A
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Given a supported real repository set, the platform produces a durable, inspectable, evidence-backed Unified Application Understanding — and a QA user can read it.

## A. Product validation — what the QA user can do

A QA user can:
1. Trigger an understanding build and watch its progress
2. **Read the understanding as a document** — screens, components, fields, APIs, rules, relationships in plain language
3. Click any element and reach its supporting evidence at a source location
4. See inference type, oracle status where applicable, reconciliation status and confidence on every element
5. See clearly which relationships are `unresolved` rather than guessed
6. Rebuild after a code change and view a diff of what moved
7. See which downstream artifacts became stale — with nothing regenerated automatically

**This completes Vertical Slice A:** *QA can provide a real repository and inspect the generated application understanding.*

## B. Engineering validation — what the platform must prove internally

- Analysis worker with untrusted-content boundary
- Stack detection and framework pack selection
- Extractor pipeline: inventory, schema/constraints, rules, relationships, locator candidates
- The four extractor rules (ADR-017) enforced by test
- Normalisation to content-derived identity; corroboration across extractors
- Confidence priors from the calibration harness
- Understanding version assembly pinning the repository tuple
- Incremental rescan, staleness cascade, understanding diff
- Graph and document projections, rebuildable with no data loss
- `CodeIntelligenceProvider` port with a conformance suite and a proven **adapter-absent degraded mode**

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-01 |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-01; a reference corpus with ground truth (**OPEN — OD-3**) |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | A bound Code Intelligence provider (ADR-041) — improves breadth; **not required** |
| **Parallel work** *(can safely proceed independently)* | CP-09 runtime exploration; CP-03 coverage modelling |

## Preconditions

- CP-01 PASS
- ADR-052 / OD-3 resolved: the Phase 1 AUT and stack are known
- A ground-truth corpus exists (see [`../test-assets/`](../test-assets/))
- Validation A executed and its result recorded

## Required components

Static understanding · extractors · framework packs · UAU store · projections · calibration harness

## Functional test

1. Trigger an understanding build for the registered repository set
2. Open the understanding document projection and read it
3. Inspect an element; reach its source evidence in two interactions
4. Confirm the five grounding fields are present and distinct
5. Locate an `unresolved` relationship; confirm it was not guessed
6. Re-run on the same revision; confirm idempotency
7. Advance a revision, rebuild, view the diff, confirm staleness is surfaced and nothing auto-regenerated

## Automated tests

- Unit: normalisation determinism; corroboration; confidence prior assignment
- Contract: `UnderstandingQuery`, `UnderstandingContribution`, `CodeIntelligenceProvider` including the adapter-absent test
- Integration: full pipeline against the reference corpus; incremental rescan; staleness cascade; projection rebuild
- Rules: R1–R4 (ADR-017) each verified by a dedicated negative test
- Calibration: per-extractor precision and recall by element type, **never averaged**

## Required test data

Reference AUT repository set at a fixed revision · hand-written ground-truth corpus · a second revision for diff and staleness. **Reference AUT is OPEN (OD-3).**

## Expected outcome

A versioned, evidence-backed understanding whose quality is measured rather than asserted, and whose weak areas are visibly marked.

## Evidence produced

Stored under `evidence/CP-02/`:

- A persisted understanding version pinning the full repository tuple
- Calibration report: per-extractor precision/recall by element type
- Understanding document projection export
- Understanding diff between two versions
- Staleness cascade record
- Degraded-mode test output (Code Intelligence adapter removed)
- Validation A result recorded against THR-EXTRACT-001/002

## Failure conditions

- Any element without a resolvable evidence reference
- A relationship produced from transitive reachability alone (R3)
- An unresolvable path segment wildcard-matched (R1)
- Confidence set by a model rather than computed
- **A fixture or mock understanding presented as an extraction result**
- Calibration reporting a single averaged accuracy figure

## Known limitations

- Framework packs cover the confirmed stack only; other stacks are unsupported, not broken
- Business-rule recall is known-partial (measured ~0.500 on a different application)
- Rename detection is heuristic and limited to routes and endpoints (ADR-033)
- If Validation A falls below THR-EXTRACT-003, static relationships gate nothing and this becomes a PASS WITH KNOWN LIMITATION

## Exit criteria

- [ ] All Phase 2 acceptance criteria met
- [ ] `CodeIntelligenceProvider` port defined, conformance passing, degraded mode proven
- [ ] Validation A result recorded with consequences applied
- [ ] **Vertical Slice A demonstrated end to end**
- [ ] Evidence captured under `evidence/CP-02/`
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
