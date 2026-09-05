# 06 — Implementation Phases

**Status:** ACTIVE — canonical for phase semantics and completion definitions
**Last updated:** 2026-09-05
**Controls:** what a phase is, when it may start, when it is genuinely done

> Phase *content* lives in [`05_DETAILED_DEVELOPMENT_ROADMAP.md`](05_DETAILED_DEVELOPMENT_ROADMAP.md).
> This document defines the *rules* that govern phases. It deliberately does not repeat
> the roadmap.

---

## 1. The eight maturity states

These are **different things** and must never be used interchangeably. Most project
failures start by treating one as another.

| State | Means precisely | Evidence required | Not sufficient |
|---|---|---|---|
| **DEFINED** | The requirement or design intent is written down and agreed | A document section with an identifier | A conversation, an idea, a diagram |
| **DESIGNED** | An approach exists that satisfies the requirement | Architecture or contract section | A prototype |
| **IMPLEMENTED** | Code exists that attempts the behaviour | Source files that compile / type-check | Code that has never run |
| **TESTING** | Relevant automated or manual tests are being executed | Test run in progress | Tests merely written |
| **TESTED** | Tests completed **with recorded results** | Test output with a commit hash | "The tests pass" without a record |
| **VALIDATED** | A checkpoint demonstrated the intended **capability**, both product and engineering dimensions | Captured checkpoint evidence | Green unit tests |
| **ACCEPTED** | Product or architecture acceptance has been recorded by the responsible party | A recorded acceptance with actor and date | The implementer's own opinion |
| **COMPLETE** | Accepted, validated, and no unresolved blocking issue remains | All of the above plus a clean blocker list | Any of the above alone |

**The rules these states exist to enforce:**

- A **document existing** makes something `DEFINED` — never `IMPLEMENTED`
- A **prototype existing** makes something `DESIGNED` — never `IMPLEMENTED`
- **Code existing** makes something `IMPLEMENTED` — never `TESTED`
- A **passing unit test** makes something `TESTED` — never `VALIDATED`
- A **checkpoint** makes something `VALIDATED` — never `ACCEPTED`
- Only a **recorded acceptance** makes something `ACCEPTED`
- **`COMPLETE` requires all of them plus no open blockers**

> **Nothing in this repository is currently past `DESIGNED`**, except version control
> (P0-W1-T1), which is `COMPLETE`.

---

## 2. Phase entry criteria

A phase may start only when **all** of the following hold:

1. Its dependency phases have reached `VALIDATED` at their checkpoint, or the dependency is explicitly recorded as partial with a reason
2. Any `REQUIRES_DECISION` blocker on the phase is resolved, or the affected work items are explicitly deferred
3. The phase's contracts in [`04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](04_DOMAIN_AND_COMPONENT_CONTRACTS.md) are current
4. Its checkpoint document exists and its exit criteria are written **before** implementation starts
5. [`12_CURRENT_IMPLEMENTATION_STATE.md`](12_CURRENT_IMPLEMENTATION_STATE.md) records the transition

> Writing the checkpoint before the code is deliberate. A checkpoint written afterwards
> describes what was built, not what was needed.

---

## 3. Phase exit criteria

A phase is `COMPLETE` only when:

1. All non-deferred work items are `VALIDATED`
2. Its checkpoint result is `PASS` or `PASS WITH KNOWN LIMITATION`
3. Every known limitation is documented and visible in the product, not only in a document
4. Regression criteria from all prior checkpoints still hold
5. Evidence is captured and linked from [`08_TRACEABILITY_MATRIX.md`](08_TRACEABILITY_MATRIX.md)
6. [`12_CURRENT_IMPLEMENTATION_STATE.md`](12_CURRENT_IMPLEMENTATION_STATE.md) is updated

### The advancement rule

> **Never advance a major phase solely because implementation code exists.
> Advance only when the corresponding checkpoint demonstrates the intended capability.**

---

## 4. Checkpoint result semantics

| Result | Meaning | May the next phase start? |
|---|---|---|
| **PASS** | All exit criteria met | Yes |
| **PASS WITH KNOWN LIMITATION** | Capability demonstrated; a bounded, documented gap remains | Yes — the limitation must be recorded in the product and in document 12 |
| **BLOCKED** | Cannot be evaluated — a dependency or decision is missing | No |
| **FAIL** | Capability not demonstrated | No |

**A checkpoint must not be marked PASS if a capability the phase requires is mocked.**
A contract-conformance fake used in tests is legitimate; presenting it as working
functionality is not.

---

## 5. Production placeholders versus test doubles

Two different things, and conflating them is how fake capability gets presented as real.

### Production architectural placeholders — exactly two

```text
CODE_INTELLIGENCE_PROVIDER   (ADR-041)
CRAWLER_PROVIDER             (ADR-040)
```

These are **provider-selection** placeholders. Their **contracts are FIXED architecture**;
their **bindings are deliberately unselected**. Any additional production placeholder
requires an explicit entry in [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md). There are none today.

### Test doubles — permitted, and necessary

Mocks, stubs, fakes, contract-test providers, recorded fixtures, synthetic observations and
controlled observation datasets are **legitimate engineering**. They are how a
non-deterministic system becomes testable and how reconciliation develops without a live
crawler.

| Permitted | Not permitted |
|---|---|
| A test double inside a test environment | A double reachable in a production code path |
| A contract-conformance fake in a conformance suite | A fake presented as a working provider |
| Recorded or synthetic observations driving reconciliation development | Fixture output presented as live observation |
| Degraded-mode behaviour when a provider is absent, clearly recorded | Hardcoded results presented as generated |
| A checkpoint passing in a clearly recorded degraded mode | **A checkpoint passing because a mock returned success** |

**The rule:** a test double is legitimate. Representing one as production capability — in a
checkpoint result, a status claim, or the product interface — is not. Full definitions:
[`14_ARCHITECTURAL_DECISION_REGISTER.md`](14_ARCHITECTURAL_DECISION_REGISTER.md) §4.

---

## 6. Phase-to-checkpoint mapping

| Phase | Checkpoint | Proves |
|---|---|---|
| P0 | CP-00 Platform Foundation | Platform boots, persists, queues, executes workflow, **governance foundations exist**, tests green |
| P1 | CP-01 Source Ingestion | A repository set is registered, acquired, pinned and represented as a version tuple |
| P2, P3 | CP-02 Static Application Understanding | A real repository set yields a durable, evidence-backed, **readable** UAU |
| P6 | CP-03 Test Design & Coverage | Coverage is computed from obligations, not asserted |
| P7 | CP-04 Test Case & Test Data | Executable intent with declared data requirements |
| P8 | CP-05 Automation | Maintainable, reusable Playwright; healing cannot touch intent |
| P9 | CP-06 Real Execution | Real runs, real artifacts, isolated workers |
| P10 | CP-07 Evaluation & Findings | Outcomes explained and classified by cause |
| P11 | CP-08 Governance | One mechanism; autonomy bypasses no control |
| P4 | CP-09 Runtime Exploration | Controlled, safe exploration through a replaceable provider |
| P5 | CP-10 Static/Runtime Reconciliation | Static and runtime reconcile explainably — **validated with fixture observations; no live crawler required** |
| P12 | CP-11 Integrated Vertical Slices | Both end-to-end paths complete and deliver user value |
| P13 | CP-12 Hardening & Release Readiness | Functional within documented boundaries; gaps visible |

Checkpoint definitions: [`07_CHECKPOINT_AND_VALIDATION_PLAN.md`](07_CHECKPOINT_AND_VALIDATION_PLAN.md)
and the per-checkpoint documents in [`checkpoints/`](checkpoints/).

---

## 7. Parallelisation

Phases are ordered by **dependency**, not by user-interface order. These may proceed in parallel:

| Track | Phases | Rationale |
|---|---|---|
| Critical path | P0 → P1 → P2 → P6 → P7 → P8 → P9 → P10 → P12 → P13 | Each strictly needs the last |
| Provider research | P3, P4 research | Independent of code; gated on OD-1 / OD-5 |
| Exploration | P4 | Off critical path — static-only is supported (INV-4). Gated on OD-1 |
| Reconciliation | P5 | **Depends on P2 only.** Develops against recorded, synthetic or fixture observations. P4 is *optional enrichment*, not a prerequisite |
| Governance | P11 | Foundations in P0-W10; full capability alongside P6–P10 |

> **P5 is deliberately not shown as depending on P4.** A dependency there would contradict
> INV-4 and was corrected in the V2 pass.

**Do not serialise independent work.** The dependency table in
[`05_DETAILED_DEVELOPMENT_ROADMAP.md`](05_DETAILED_DEVELOPMENT_ROADMAP.md) is authoritative.

---

## 8. Deferral

Deferring a work item requires recording:

```text
Item ID · Reason · What is lost · Whether a checkpoint is affected ·
Whether it becomes a known limitation · When it will be reconsidered
```

A deferred item that affects a checkpoint changes that checkpoint's result to
`PASS WITH KNOWN LIMITATION` at best. Deferral is a decision and belongs in the
Decision Log.
