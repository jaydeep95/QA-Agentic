# CP-02 — Exploration and Reconciliation

**Phases:** P4, P5
**Status:** `NOT_STARTED`
**Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint must not be marked PASS if a capability the phase requires is mocked.**

---

## Objective

Controlled exploration runs safely against an approved AUT environment using a provider implementation without contaminating the core with provider-specific logic; static and runtime evidence reconcile explainably.

## Preconditions

- CP-01 PASS
- OD-1 resolved: exploration write policy and target environment agreed
- Validation C executed, including destructive-action classification at recall 1.00
- A reachable AUT environment with test identities per role

## Functional test

What a human should be able to do:

1. Plan an exploration session; observe the agenda derived from unverified and unresolved elements
2. Run a read-only session; observe pages, actions, DOM and network captured as runtime evidence
3. Inspect the state graph; confirm two records under one parameterised route collapse to one state, and a modal opening does not
4. Trigger a control whose target endpoint is a delete; confirm policy blocks it before the click
5. Trigger a flow requiring an unknown value; confirm a governed blocker appears stating requirement, reason, attempts and why each failed
6. Supply the value; confirm the session resumes and the value is classified on arrival
7. Run reconciliation; inspect all six statuses in the result
8. Find a `conflict`; confirm it is presented as a classified candidate with evidence from both sides, and is **not** labelled a defect

## Automated tests

- Unit: state identity fingerprint determinism; action risk classification; reconciliation set algebra producing all six statuses
- Contract: `CrawlerProvider` conformance, including binding a **second implementation** with no change to state model or planning
- Integration: full exploration session against a controlled environment; budget enforcement; blocker persistence and resumption; reconciliation against a seeded-discrepancy branch
- Safety: destructive-action classification against a hand-labelled control set

## Test data

- A reachable AUT environment, reset-capable
- Non-interactive test identities for each role
- A hand-labelled control set of actions with known risk classes
- A seeded-discrepancy branch: removed confirmation step, renamed response property, endpoint with no UI path

## Evidence

Stored under `docs/checkpoints/evidence/CP-02/`:

- Exploration session record: agenda, states discovered, actions taken, budget consumed, areas not reached
- State graph export
- Risk-classification report against the control set showing destructive recall
- Blocker work item with full attempt history
- Reconciliation report with all six statuses and classified mismatches
- Second-provider conformance run

## Expected result

Exploration validates and enriches the understanding within its budget and safety policy;
reconciliation explains differences without asserting defects.

## Failure conditions

- **Any missed destructive action** — blocks write-enabled exploration entirely
- The crawler provider writing to the canonical model
- A mismatch auto-labelled as a defect
- State identity based on URL alone
- Exploration failing silently instead of raising a blocker
- A secret value entering an artifact after being supplied through a blocker

## Exit criteria

- [ ] All Phase 4 and Phase 5 acceptance criteria met
- [ ] Validation C recorded with destructive-action recall of 1.00
- [ ] Validation D recorded
- [ ] Evidence captured under `docs/checkpoints/evidence/CP-02/`
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
| RG-6 | Static-only mode still completes the pipeline available at this phase (INV-4) |

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
