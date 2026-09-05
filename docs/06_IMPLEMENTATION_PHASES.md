# 06 — Implementation Phases

**Status:** ACTIVE — canonical for phase semantics and completion definitions
**Last updated:** 2026-09-05
**Controls:** what a phase is, when it may start, when it is genuinely done

> Phase *content* lives in [`05_DETAILED_DEVELOPMENT_ROADMAP.md`](05_DETAILED_DEVELOPMENT_ROADMAP.md).
> This document defines the *rules* that govern phases. It deliberately does not repeat
> the roadmap.

---

## 1. The six maturity states

These are **different things** and must never be conflated. Most project failures start
by treating one as another.

| State | Means | Evidence required |
|---|---|---|
| **DEFINED** | The requirement is written down and agreed | A document section |
| **DESIGNED** | An approach exists that satisfies the requirement | Architecture/contract section |
| **IMPLEMENTED** | Code exists that attempts the behaviour | Source files, compiles/type-checks |
| **TESTED** | Automated tests exercise it and pass | Test run output |
| **VALIDATED** | A checkpoint demonstrated the intended *capability* | Captured checkpoint evidence |
| **PRODUCTION-READY** | Validated **plus** hardened: reliability, security, observability, documentation | Phase 13 hardening evidence |

**Rules:**

- Documentation existing makes something `DEFINED`, never `IMPLEMENTED`
- A prototype existing makes something `DESIGNED`, never `IMPLEMENTED`
- Code existing makes something `IMPLEMENTED`, never `VALIDATED`
- Tests passing makes something `TESTED`; only a checkpoint makes it `VALIDATED`
- **Nothing in this repository is currently past `DESIGNED`.**

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

## 5. The two legitimate placeholders

Only two placeholder areas exist in this architecture:

```text
CODE_INTELLIGENCE_PROVIDER
CRAWLER_PROVIDER
```

Their **contracts are real architecture**; their **bindings are deliberately unselected**.

| Permitted | Not permitted |
|---|---|
| A defined port with a conformance suite | Fake success anywhere else |
| A test-only fake implementing the contract | Simulated execution presented as execution |
| Degraded-mode behaviour when the provider is absent | Hardcoded results presented as generated |
| A checkpoint passing in degraded mode, clearly recorded | A checkpoint passing because a mock returned success |

Any **additional** placeholder requires an explicit entry in
[`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md). There are none today.

---

## 6. Phase-to-checkpoint mapping

| Phase | Checkpoint | Proves |
|---|---|---|
| P0, P1 | CP-00 Foundation | Platform boots, persists, queues, executes workflow, tests green |
| P2, P3 | CP-01 Understanding | A real repository set yields a durable, evidence-backed UAU |
| P4, P5 | CP-02 Exploration & Reconciliation | Controlled exploration runs; static and runtime reconcile explainably |
| P6 | CP-03 Test Design | Coverage is computed from obligations, not asserted |
| P7 | CP-04 Test Case & Data | Executable intent with declared data requirements |
| P8 | CP-05 Automation | Maintainable, reusable Playwright; healing cannot touch intent |
| P9 | CP-06 Execution | Real runs, real artifacts, isolated workers |
| P10, P11 | CP-07 Evaluation & Governance | Outcomes explained; one governance mechanism |
| P12, P13 | CP-08 Phase 1 Integrated | Both end-to-end paths complete; hardened |

Checkpoint definitions: [`07_CHECKPOINT_AND_VALIDATION_PLAN.md`](07_CHECKPOINT_AND_VALIDATION_PLAN.md)
and the per-checkpoint documents in [`checkpoints/`](checkpoints/).

---

## 7. Parallelisation

Phases are ordered by **dependency**, not by user-interface order. These may proceed in parallel:

| Track | Phases | Rationale |
|---|---|---|
| Critical path | P0 → P1 → P2 → P6 → P7 → P8 → P9 → P10 → P12 → P13 | Each strictly needs the last |
| Provider research | P3, P4 research | Independent of critical path; gated on OD-1/OD-5, not on code |
| Runtime track | P4 → P5 | Off critical path because static-only is supported (INV-4) |
| Governance | P11 | Depends only on P0; can be built alongside P6–P10 |

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
