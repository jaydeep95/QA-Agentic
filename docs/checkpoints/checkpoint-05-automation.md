# CP-05 — Automation

**Phase(s):** P8
**Vertical slice:** Slice C (part 1)
**Status:** `NOT_STARTED` · **Result:** — *(not yet attempted)*
**Last updated:** 2026-09-05 (Roadmap V2)

> Template and rules: [`../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../07_CHECKPOINT_AND_VALIDATION_PLAN.md)
> **A checkpoint requires BOTH dimensions.** Internal modules working is not sufficient —
> the user-visible capability must also be demonstrated.
> **A checkpoint requiring functional behaviour may not pass on a test double.**

---

## Objective

Generated automation is maintainable, reusable and executable, and healing is structurally incapable of altering test intent.

## A. Product validation — what the QA user can do

A QA user can:
1. Generate automation from an approved test case and see the intent separately from the environment-specific detail
2. Bind the same automation to two different environments
3. See the shared page and component objects the tests reuse
4. After an application markup change, see healing **proposals** with evidence — not silent rewrites
5. Accept a proposal and confirm only the binding changed
6. See an audit record of every healing event

## B. Engineering validation — what the platform must prove internally

- Automation specification: actions, semantic element references, assertions, expected outcomes, data requirements
- Automation binding per environment: locators with provenance, fixtures, data bindings, secret references
- Page/component object model **projected from the UAU**, shared across cases
- Locator lifecycle: candidate → validated → bound → verified → healed, each with evidence
- Healing: capture, candidate search, probe, score, policy-gated application, audit event
- Healing emits `UNRESOLVED` or `HUMAN_REVIEW_REQUIRED` where a change would touch intent — **never silent continuation**
- **Structural** proof that the healing path cannot write to specification storage (ARCH-5)

## Dependencies

| Kind | Items |
|---|---|
| **Development dependency** *(must exist before implementation can begin)* | CP-04; CP-02 (locator candidates) |
| **Validation dependency** *(must exist before this can be fully validated)* | CP-04; a markup-churn branch for Validation E |
| **Optional enrichment** *(improves the capability; not required for it to exist)* | CP-09 runtime-validated locators — materially improves reliability; **not required** (unvalidated candidates are usable at lower confidence) |
| **Parallel work** *(can safely proceed independently)* | CP-09, CP-10 |

## Preconditions

- CP-04 PASS
- Locator candidates available from the understanding
- Validation E executed and recorded

## Required components

Automation specification · binding · object model projection · locator lifecycle · healing

## Functional test

1. Generate a specification from an approved test case
2. Confirm it contains no locators or environment values
3. Generate bindings for two environments from that one specification
4. Inspect the page/component objects; confirm projection from the UAU and reuse across cases
5. Introduce markup change; run; observe locator failures and healing proposals
6. Confirm auto-apply is disabled by default
7. Accept a proposal; confirm only the binding changed and the specification version is untouched
8. Trigger a case where healing would require changing an assertion; confirm `HUMAN_REVIEW_REQUIRED`, not silent continuation

## Automated tests

- Unit: locator ranking; healing candidate scoring; the intent-change refusal path
- Contract: `AutomationSpecification`/`Binding`, including the structural test that healing cannot write to specification storage
- Integration: one specification against two environments; object model regeneration after an understanding change
- Architecture: ARCH-5
- Measurement: object reuse across the generated suite

## Required test data

Approved test cases · two configured environments · a markup-churn branch (renamed classes, restructured containers, relocated control).

## Expected outcome

Reuse is structural rather than emergent. Healing repairs how an element is found and cannot change what the test means.

## Evidence produced

Stored under `evidence/CP-05/`:

- Generated specification and two bindings
- Object model with measured reuse
- Suite pass rate before and after markup churn
- Healing proposals with precision measured by review (Validation E, THR-HEAL-001)
- Test output proving an attempted assertion change through healing fails
- An example `HUMAN_REVIEW_REQUIRED` outcome

## Failure conditions

- **A test modifying an assertion through the healing path succeeding**
- A specification containing locators or environment values
- Per-test object generation instead of a shared projected model
- Auto-apply enabled by default
- Healing silently continuing where intent would change
- A healing event applied without before, after, evidence and score

## Known limitations

Locators are static candidates unless CP-09 has validated them; tests built on unvalidated locators carry capped confidence. Healing ships propose-only (ADR-027) until THR-HEAL-001 is met.

## Exit criteria

- [ ] All Phase 8 acceptance criteria met
- [ ] Validation E recorded against THR-LOC-001 and THR-HEAL-001
- [ ] Evidence captured under `evidence/CP-05/`
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
