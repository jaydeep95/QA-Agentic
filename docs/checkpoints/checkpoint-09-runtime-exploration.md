# CP-09 — Runtime Exploration

**Phase(s):** P4
**Vertical slice:** Enrichment of Slices A and C
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Controlled exploration runs safely against an approved AUT environment through a replaceable provider, without contaminating the core with provider-specific logic.

## A. Product validation — what the QA user can do

A QA user can:
1. Start an exploration session against an approved environment and watch its progress
2. See which pages, actions and states were discovered — and **what was not reached**
3. See an exploration blocked on a required value, with a clear statement of what is needed, why, what was attempted and why each attempt failed
4. Supply the value and see the session resume
5. See that destructive actions were identified and not performed

## B. Engineering validation — what the platform must prove internally

- Exploration planner building an agenda from unverified and unresolved elements
- Composite state identity (route template, structural fingerprint, affordance set, actor context) — **not URL alone**
- Action-risk classification **before acting**, derived from the understanding; unknown denied by default
- Safety policy: environment and domain allowlists, permitted action classes, action and session budgets
- Blockers as durable, attributable, resumable governed work items
- Observations become runtime evidence; **the provider never writes understanding**
- Locator probing: exists, unique, visible, interactable
- `CrawlerProvider` conformance, including binding a **second implementation** with no change to state model or planning

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-02 (an understanding to plan against); CP-00 (worker harness) |
| **Validation dependency** *(must exist before this can be fully validated)* | **ADR-050 / OD-1 resolved**; a reachable AUT environment; Validation C executed |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | CP-08 governance — enriches blocker escalation; foundations from CP-00 suffice |
| **Parallel work** *(can safely proceed independently)* | CP-03 through CP-07 all proceed without this |

## Preconditions

- CP-02 PASS
- OD-1 resolved: exploration write policy and target environment agreed
- Validation C executed, including destructive-action classification (THR-SAFETY-001)
- Test identities per role

## Required components

Exploration planner · state model · risk classifier · safety policy · blocker work items · CrawlerProvider adapter

## Functional test

1. Plan a session; observe the agenda derived from unverified elements
2. Run read-only; observe pages, actions, DOM and network captured
3. Inspect the state graph; confirm parameterised variants collapse and a modal does not
4. Trigger a control whose target is a delete; confirm policy blocks it **before** the click
5. Trigger a flow needing an unknown value; confirm a governed blocker with full attempt history
6. Supply the value; confirm resumption and correct classification of the value
7. Confirm the session honoured its budget and reported what it did not reach

## Automated tests

- Unit: state identity fingerprint determinism; action risk classification
- Contract: `CrawlerProvider` conformance, including a **second implementation** binding
- Integration: full session against a controlled environment; budget enforcement; blocker persistence and resumption
- Safety: destructive-action classification against a hand-labelled control set

## Required test data

A reachable, reset-capable AUT environment · non-interactive test identities per role · a hand-labelled control set of actions with known risk classes.

## Expected outcome

Exploration validates and enriches the understanding within budget and safety policy, through a provider that can be replaced.

## Evidence produced

Stored under `evidence/CP-09/`:

- Session record: agenda, states, actions, budget consumed, areas not reached
- State graph export
- Risk-classification report against the control set (THR-SAFETY-001)
- A blocker work item with full attempt history
- Second-provider conformance run

## Failure conditions

- **Any missed destructive action** — blocks write-enabled exploration entirely
- The crawler provider writing to the canonical model
- State identity based on URL alone
- Exploration failing silently instead of raising a blocker
- A supplied secret entering an artifact
- **A recorded fixture presented as a live exploration session**

## Known limitations

- Provider selection is a PLACEHOLDER (ADR-040); this checkpoint validates the *boundary*, and a bound provider is validated per-provider
- Read-only mode cannot observe state transitions — reconciliation's strongest evidence class is unavailable until write-enabled exploration is permitted (OD-1)
- Coverage of the agenda is bounded by budget; partial exploration is a valid result

## Exit criteria

- [ ] All Phase 4 acceptance criteria met
- [ ] Validation C recorded, THR-SAFETY-001 satisfied
- [ ] All five product-validation steps demonstrated
- [ ] Evidence captured under `evidence/CP-09/`
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
