# 07 — Checkpoint and Validation Plan

**Status:** ACTIVE — canonical for how completion is proven
**Last updated:** 2026-09-05 (Roadmap V2 — checkpoint restructure)
**Controls:** checkpoint definitions, the dependency model, validation experiments, evidence requirements

> Per-checkpoint working documents live in [`checkpoints/`](checkpoints/).
> Numeric thresholds live in [`14_ARCHITECTURAL_DECISION_REGISTER.md`](14_ARCHITECTURAL_DECISION_REGISTER.md) §3 — **all are PROVISIONAL**.

---

## 1. Why checkpoint-driven development

The platform must not be developed as one large implementation followed by one final test.
Every logical capability has a checkpoint that demonstrates it.

> **Never advance a major phase solely because implementation code exists.**

Results: `PASS` · `PASS WITH KNOWN LIMITATION` · `BLOCKED` · `FAIL`.
Semantics in [`06_IMPLEMENTATION_PHASES.md`](06_IMPLEMENTATION_PHASES.md) §4.

---

## 2. Every checkpoint has two dimensions

A checkpoint that only proves internal machinery is not a checkpoint.

| Dimension | Question | Failure mode it prevents |
|---|---|---|
| **A. Product validation** | What can the QA user actually do? | Working APIs and green tests while the product does nothing a user recognises |
| **B. Engineering validation** | What must the platform prove internally? | A demo that works once, on one path, with nothing underneath it |

**Both must pass.** A checkpoint may not pass on internal modules alone, and it may not
pass on a demonstration that the engineering cannot support.

### Checkpoint document structure

```text
Checkpoint ID · Objective
A. Product Validation      B. Engineering Validation
Dependencies (four kinds)  Preconditions
Required Components        Functional Test
Automated Tests            Required Test Data
Expected Outcome           Evidence Produced
Failure Conditions         Known Limitations
Exit Criteria              Regression Criteria
Result Record
```

---

## 3. The four-way dependency model

Introduced in V2 to stop optional enrichment being mistaken for a prerequisite.

| Kind | Meaning | Consequence if absent |
|---|---|---|
| **Development dependency** | Must exist before implementation can begin | Work cannot start |
| **Validation dependency** | Must exist before the capability can be fully validated | Work can proceed; the checkpoint cannot close |
| **Optional enrichment** | Improves the capability; not required for it to exist | Capability exists at lower quality or confidence |
| **Parallel work** | Can safely be built independently | No effect |

> **The correction this model exists to enforce:** reconciliation (CP-10) previously
> depended on the crawler phase. That contradicted ADR-002 / INV-4, which state that static
> and runtime are independent evidence sources and that runtime exploration is optional.
> Reconciliation now develops against **recorded, synthetic or fixture observations**, with
> live exploration as *optional enrichment*.

---

## 4. Checkpoint set

Thirteen checkpoints. The V2 restructure separated capabilities that were previously
validated together and could therefore mask each other.

| ID | Name | Phase(s) | Vertical slice | Development depends on | Status |
|---|---|---|---|---|---|
| CP-00 | Platform Foundation | P0 | Prerequisite for A | — | NOT_STARTED |
| CP-01 | Source Ingestion | P1 | A (part 1) | CP-00 | NOT_STARTED |
| CP-02 | Static Application Understanding | P2, P3 (contract) | A (part 2) — completes A | CP-01 | NOT_STARTED |
| CP-03 | Test Design & Coverage | P6 | B (part 1) | CP-02 | NOT_STARTED |
| CP-04 | Test Case & Test Data | P7 | B (part 2) — completes B | CP-03 | NOT_STARTED |
| CP-05 | Automation | P8 | C (part 1) | CP-04, CP-02 | NOT_STARTED |
| CP-06 | Real Execution | P9 | C (part 2) — completes C | CP-05, CP-00 | NOT_STARTED |
| CP-07 | Evaluation & Findings | P10 | D | CP-06 | NOT_STARTED |
| CP-08 | Governance | P11 | Cross-cutting | CP-00 *(foundations)* | NOT_STARTED |
| CP-09 | Runtime Exploration | P4 | Enriches A and C | CP-02, CP-00 | NOT_STARTED |
| CP-10 | Static/Runtime Reconciliation | P5 | Enriches A, B and D | **CP-02 only** | NOT_STARTED |
| CP-11 | Integrated Vertical Slices | P12 | E — full journey | CP-00…CP-08 | NOT_STARTED |
| CP-12 | Phase 1 Hardening & Release Readiness | P13 | All | CP-11 | NOT_STARTED |

### What changed from V1, and why

| V1 | V2 | Reason |
|---|---|---|
| CP-00 covered P0 **and** P1 | CP-00 (P0) and CP-01 (P1) | Platform foundation and source ingestion are different capabilities; combining them let one mask the other |
| CP-02 covered exploration **and** reconciliation | CP-09 and CP-10 | They have different dependencies. Combining them made reconciliation appear to require a live crawler |
| CP-07 covered evaluation **and** governance | CP-07 and CP-08 | Governance is cross-cutting and its foundations now start at CP-00 |
| CP-08 covered integration **and** hardening | CP-11 and CP-12 | Proving the journey works is not the same as making it robust |
| Governance validated only at the end | Foundations at CP-00, full capability at CP-08 | Governance is cross-cutting; a late-arriving gate is a retrofit |
| No vertical slices | Five slices mapped across checkpoints | Validates user value before the whole platform exists |

---

## 5. Vertical product slices

Component-by-component progress can look healthy while delivering nothing usable. Slices
validate that the architecture produces user value at each stage. Full definitions:
[`16_VERTICAL_SLICES.md`](16_VERTICAL_SLICES.md).

| Slice | Chain | User outcome | Completed at |
|---|---|---|---|
| **A** | Repository → Static Understanding → UAU → Reviewable understanding | QA can provide a real repository and inspect the generated understanding | CP-02 |
| **B** | UAU → Coverage obligations → Scenario → Test Case | QA can generate and inspect evidence-backed scenarios and cases | CP-04 |
| **C** | Test Case → Automation Spec → Binding → Playwright → Real execution | QA can run a generated test against a real environment | CP-06 |
| **D** | Execution → Evaluation → Classification → Finding | QA can understand what happened and why | CP-07 |
| **E** | The full Phase 1 journey including knowledge update | The complete product loop, on both the static-only and exploration paths | CP-11 |

---

## 6. Universal regression criteria

Every checkpoint from CP-01 onward re-verifies:

| # | Criterion | Origin | Applies from |
|---|---|---|---|
| RG-1 | No artifact exists without a UAU version reference | INV-2 | CP-02 |
| RG-2 | No understanding element exists without resolvable evidence | INV-8 | CP-02 |
| RG-3 | No provider type appears in a domain module | INV-3 | CP-00 |
| RG-4 | Workers hold no database credentials | INV-10 | CP-00 |
| RG-5 | No secret appears in any artifact, log or prompt | Security | CP-00 |
| RG-6 | Static-only mode completes the pipeline available at that phase | INV-4 | CP-03 |
| RG-7 | No code path allows a model to set a coverage value | INV-7 | CP-03 |
| RG-8 | The healing path cannot write to specification storage | INV-6 | CP-05 |
| RG-9 | Exactly one gate implementation exists | INV-9 | CP-00 |
| RG-10 | Full test suite green in CI | Discipline | CP-00 |

---

## 7. Empirical validations

Six experiments de-risk the **architecture**, distinct from checkpoints which validate
**implementation**.

> **Every threshold below is `PROVISIONAL`** and carries a full provenance record in
> [`14_ARCHITECTURAL_DECISION_REGISTER.md`](14_ARCHITECTURAL_DECISION_REGISTER.md) §3.
> They are decision rules for experiments, **not product obligations**. Do not trace a
> product requirement to one without a recorded approval.

| ID | Hypothesis | Threshold refs | Consequence if it fails |
|---|---|---|---|
| **A** | Extractor rules plus a framework pack lift relationship precision without losing recall | THR-EXTRACT-001/002/003 | Below THR-EXTRACT-003, static relationships become hypotheses that gate nothing; exploration becomes the primary relationship source. Pipeline reorders; architecture holds |
| **B** | Code Intelligence materially improves relationship or symbol quality with usable anchors | THR-CI-001 | The port stays defined and unbound; native extraction carries Phase 1. **No architectural change either way** |
| **C** | Composite state identity is stable across sessions; exploration reaches known screens safely | THR-STATE-001/002, **THR-SAFETY-001** | High variance forces a coarser identity model. **Any missed destructive action blocks write-enabled exploration entirely** |
| **D** | Reconciliation finds seeded discrepancies without unusable false-positive volume | THR-RECON-001/002/003 | Reconciliation becomes a diagnostic view rather than a finding source |
| **E** | Static locator candidates validate usefully; healing proposals are correct enough to justify automation | THR-LOC-001, THR-HEAL-001 | Runtime-observed locators become mandatory, making exploration a prerequisite for automation rather than an enrichment |
| **F** | Derived obligations match what an experienced tester independently identifies | THR-COV-001/002 | Coverage is presented strictly as coverage of a known-partial model with the shortfall quantified |

### Sequencing and gating

| Validation | Runs at | Gates | Blocked by |
|---|---|---|---|
| A | Before/within P2 | CP-02 relationship claims | OD-3 |
| B | Alongside A | CP-02 provider binding | OD-5 |
| C | Before CP-09 write-enabled work | CP-09 entirely | OD-1, OD-3 |
| D | After A; observations may be fixtures | CP-10 findings path | A |
| E | Parallel with D | CP-05 healing auto-apply | OD-8 |
| F | After a stabilised UAU | CP-03 coverage claims | CP-02 |

**Smallest sufficient set before implementation begins:** **A** and **C**.
Neither blocks Phase 0 — CP-00 builds foundations that are correct regardless of outcome.

> **Note on Validation D:** it requires an observation dataset, **not a live crawler**.
> Recorded, synthetic or fixture observations are admissible.

---

## 8. Evidence requirements

Evidence is stored under `docs/checkpoints/evidence/<checkpoint-id>/` and linked from the
checkpoint result record and from [`08_TRACEABILITY_MATRIX.md`](08_TRACEABILITY_MATRIX.md).

| Acceptable | Not acceptable |
|---|---|
| Automated test result with CI run and commit hash | A claim in a document |
| Integration test result against a real repository or environment | A screenshot of code |
| A generated artifact — an actual UAU version, scenario set, Playwright file | A passing test exercising a mock where the phase requires functional behaviour |
| Execution trace, screenshot, video | A manual assertion that something works |
| API request/response records; persisted records with lineage | An LLM-generated summary of behaviour |
| Coverage report with denominator and unverified share | A coverage percentage without qualifiers |
| Benchmark or timing measurement | — |

---

## 9. Checkpoint execution procedure

```text
1. Confirm preconditions and prior regression criteria still hold
2. Run the automated suite -> record result
3. Perform BOTH validations: product functional test AND engineering checks
4. Capture evidence into docs/checkpoints/evidence/<id>/
5. Evaluate each exit criterion explicitly (pass/fail, not impression)
6. Record the result with date, evidence links and any known limitation
7. Update 12_CURRENT_IMPLEMENTATION_STATE.md
8. Update the PROJECT POSITION block in 05_DETAILED_DEVELOPMENT_ROADMAP.md
9. Only then begin the next phase
```

Step 9 is not optional. A phase begun before its predecessor's checkpoint is recorded
leaves the repository unable to answer "where are we?".
