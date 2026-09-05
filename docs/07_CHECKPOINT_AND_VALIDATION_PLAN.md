# 07 — Checkpoint and Validation Plan

**Status:** ACTIVE — canonical for how completion is proven
**Last updated:** 2026-09-05
**Controls:** checkpoint definitions, validation experiments, evidence requirements

> Per-checkpoint working documents live in [`checkpoints/`](checkpoints/). This document
> defines the checkpoint template, the checkpoint set, and the six empirical validations
> that de-risk the architecture itself.

---

## 1. Why checkpoint-driven development

The platform must **not** be developed as one large implementation followed by one final
test. Every logical phase has a checkpoint that demonstrates a *capability*, not the
existence of code.

> **Never advance a major phase solely because implementation code exists.**

Results: `PASS` · `PASS WITH KNOWN LIMITATION` · `BLOCKED` · `FAIL`.
Semantics in [`06_IMPLEMENTATION_PHASES.md`](06_IMPLEMENTATION_PHASES.md) §4.

---

## 2. Checkpoint template

Every checkpoint document contains exactly these sections:

| Section | Content |
|---|---|
| **Objective** | What this checkpoint proves — as a capability statement |
| **Preconditions** | What must already exist and be validated |
| **Functional test** | What a human should be able to do, step by step |
| **Automated tests** | What must be covered automatically |
| **Test data** | What data and environment are required |
| **Evidence** | What artifacts prove success, and where they are stored |
| **Expected result** | What should happen |
| **Failure conditions** | What constitutes failure — including mocked-capability failure |
| **Exit criteria** | What must be true before proceeding |
| **Regression criteria** | What must continue working from previous checkpoints |
| **Result** | PASS / PASS WITH KNOWN LIMITATION / BLOCKED / FAIL, with date and evidence links |

---

## 3. Checkpoint set

| ID | Name | Phases | Capability proven | Status |
|---|---|---|---|---|
| CP-00 | Foundation | P0, P1 | Platform boots, persists state, queues and executes controlled workflows, stores artifacts, runs its test suite; a repository set can be registered, acquired and versioned | NOT_STARTED |
| CP-01 | Understanding | P2, P3 | A real repository set produces a durable, inspectable, evidence-backed UAU | NOT_STARTED |
| CP-02 | Exploration & Reconciliation | P4, P5 | Controlled exploration runs safely against an approved environment; static and runtime evidence reconcile explainably | NOT_STARTED |
| CP-03 | Test Design | P6 | Scenarios derive from explicit obligations; coverage is computed, not asserted | NOT_STARTED |
| CP-04 | Test Case & Data | P7 | Executable test intent with explainable data requirements and a working preparation strategy | NOT_STARTED |
| CP-05 | Automation | P8 | Maintainable, reusable Playwright; healing structurally cannot alter intent | NOT_STARTED |
| CP-06 | Execution | P9 | Real Playwright execution against a controlled environment with complete artifacts | NOT_STARTED |
| CP-07 | Evaluation & Governance | P10, P11 | Outcomes explained and classified; one governance mechanism for both modes | NOT_STARTED |
| CP-08 | Phase 1 Integrated | P12, P13 | Both end-to-end paths complete with durable evidence at every stage | NOT_STARTED |

---

## 4. Universal regression criteria

Every checkpoint from CP-01 onward must additionally re-verify:

| # | Criterion | Origin |
|---|---|---|
| RG-1 | No artifact exists without a UAU version reference | INV-2 |
| RG-2 | No understanding element exists without resolvable evidence | INV-8 |
| RG-3 | No provider type appears in a domain module | INV-3 |
| RG-4 | Workers hold no database credentials | INV-10 |
| RG-5 | No secret appears in any artifact, log or prompt | Security |
| RG-6 | Static-only mode completes the pipeline available at that phase | INV-4 |
| RG-7 | No code path allows a model to set a coverage value | INV-7 |
| RG-8 | The healing path cannot write to specification storage | INV-6 |
| RG-9 | Exactly one gate implementation exists | INV-9 |
| RG-10 | Full test suite green in CI | Discipline |

RG-6 through RG-9 apply from the checkpoint at which the relevant capability first exists.

---

## 5. Empirical validations

Six experiments de-risk the **architecture**, distinct from checkpoints which validate
**implementation**. Each has a hypothesis, a threshold, and a stated architectural
consequence if it fails.

> These are transcribed from the architecture baseline so they survive independently.
> Full detail: [`architecture/architecture-overview.html`](architecture/architecture-overview.html) section D13.

| ID | Hypothesis | Pass threshold | Consequence if it fails |
|---|---|---|---|
| **A** | Extractor rules R1–R4 plus a framework pack lift relationship precision materially without losing recall | Precision ≥ 0.85 with recall ≥ 0.60 | Below 0.70: static relationships become hypotheses that gate nothing; exploration becomes the primary source. The pipeline reorders; the architecture holds |
| **B** | Code Intelligence materially improves relationship or symbol quality at acceptable latency with usable anchors | ≥ 0.10 improvement on a claim type without degrading others, with symbol or span anchors | The port stays defined and unbound; native extraction carries Phase 1. **No architectural change either way** — the point of advisory positioning |
| **C** | Composite state identity is stable across repeated sessions; agenda-driven exploration reaches most known screens safely | State-count variance < 10%; ≥ 60% of known screens reached; **destructive-action classification recall 1.00** | High variance forces a coarser identity model and reduced coverage claims. **Any missed destructive action blocks write-enabled exploration entirely** |
| **D** | Reconciliation finds seeded discrepancies and classifies them without unusable false-positive volume | ≥ 80% seeded detection; < 20% false defect candidates; residual reviewable in under an hour | Reconciliation output becomes a diagnostic view rather than a finding source — a product reduction worth knowing before build |
| **E** | Static locator candidates validate at a useful rate; healing proposals are correct often enough to justify automation | ≥ 70% candidate validation; healing precision ≥ 0.90 before auto-apply is considered | Runtime-observed locators become mandatory, making exploration a prerequisite for automation rather than an enrichment |
| **F** | Derived obligations correspond to what an experienced tester independently identifies as worth testing | ≥ 70% recall against a human inventory; < 20% judged not worth testing | Coverage is presented strictly as coverage of a known-partial model with the shortfall quantified — reframes the product claim, not the architecture |

### Validation sequencing and gating

| Validation | Runs at | Gates | Blocked by |
|---|---|---|---|
| A | Before/within P2 | P2 relationship extractors | OD-3 (AUT stack) |
| B | Alongside A | P3 provider binding | OD-5 (provider access) |
| C | Before P4 write-enabled work | P4 entirely | OD-1 (write policy), OD-3 |
| D | After A and C | P5 findings path | A, C |
| E | Parallel with D | P8 healing auto-apply | OD-8 (test attributes) |
| F | After a stabilised UAU | P6 coverage claims | P2 complete |

**Smallest sufficient set before implementation begins:** **A** and **C**.
A determines whether static understanding can carry the weight the pipeline places on it;
C determines whether exploration is safe to enable and stable enough to reconcile against.

> **Neither A nor C blocks Phase 0.** Phase 0 builds foundations that are correct
> regardless of their outcomes.

---

## 6. Evidence requirements

Every checkpoint and every major work item must produce evidence. Evidence is stored under
`docs/checkpoints/evidence/<checkpoint-id>/` and linked from the checkpoint document and
from [`08_TRACEABILITY_MATRIX.md`](08_TRACEABILITY_MATRIX.md).

Acceptable evidence:

| Type | Example |
|---|---|
| Automated test result | CI run output with commit hash |
| Integration test result | Recorded run against a real repository or environment |
| Generated artifact | An actual UAU version, scenario set, or generated Playwright file |
| Execution trace | Playwright trace, screenshot, video |
| API result | Recorded request/response |
| Database record | A persisted artifact with lineage |
| Coverage report | Obligation ledger output with denominator and unverified share |
| Benchmark | Timing or throughput measurement |
| Checkpoint result | The completed checkpoint document with date and outcome |

**Not acceptable as evidence:**
a claim in a document · a screenshot of code · a passing test that exercises a mock where
the phase requires functional behaviour · a manual assertion that something works.

---

## 7. Checkpoint execution procedure

```text
1. Confirm preconditions and prior regression criteria still hold
2. Run the automated test suite → record result
3. Perform the functional test manually → record observations
4. Capture evidence into docs/checkpoints/evidence/<id>/
5. Evaluate each exit criterion explicitly (pass/fail, not impression)
6. Record the result with date, evidence links and any known limitation
7. Update 12_CURRENT_IMPLEMENTATION_STATE.md
8. Update the PROJECT POSITION block in 05_DETAILED_DEVELOPMENT_ROADMAP.md
9. Only then begin the next phase
```

Step 9 is not optional. A phase begun before its predecessor's checkpoint is recorded
leaves the repository unable to answer "where are we?" — which is the failure this entire
documentation system exists to prevent.
