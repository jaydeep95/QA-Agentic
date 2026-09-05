# 16 — Vertical Product Slices

**Status:** ACTIVE — canonical for user-value validation
**Created:** 2026-09-05 (Roadmap V2)
**Controls:** how we prove the architecture produces usable product before it is finished

> **Authority:** this document complements [`05_DETAILED_DEVELOPMENT_ROADMAP.md`](05_DETAILED_DEVELOPMENT_ROADMAP.md), which remains
> canonical for *what gets built and in what order*. Slices are a **validation lens**, not a
> second roadmap. Where they disagree on sequencing, the roadmap wins.

---

## 1. Why slices exist

The roadmap is organised by component and dependency — correct for engineering, but it has
a known failure mode: every component can be progressing while nothing a QA user recognises
exists yet. A slice answers a different question.

| Roadmap asks | A slice asks |
|---|---|
| Is the component built and tested? | Can a QA user do something useful end to end? |
| Are the contracts honoured? | Does the chain deliver a recognisable outcome? |
| Is the phase complete? | Has the architecture produced value yet? |

Both are required. A phase can be complete with no slice delivered; a slice cannot be
delivered without the components beneath it working.

---

## 2. Slice A — Source to Understanding

```text
Repository set → Static Understanding → UAU → Reviewable understanding
```

> **User outcome:** QA can provide a real repository and inspect the generated application understanding.

| | |
|---|---|
| **Prerequisite components** | Source ingestion · analysis worker · extractor pipeline · framework pack · UAU store · document and graph projections · evidence resolution · review surface |
| **Checkpoints** | CP-00 (foundation) → CP-01 (ingestion) → **CP-02 (completes the slice)** |
| **Functional user test** | Register a frontend and backend repository; trigger a build; read the understanding document; click an element and reach its source evidence; see confidence and inference type; find an `unresolved` relationship; rebuild after a change and read the diff |
| **Automated tests** | Full extraction pipeline against the reference corpus; evidence resolvability; projection rebuild; incremental rescan; staleness cascade; the four extractor rules |
| **Evidence** | A persisted understanding version; calibration report; document projection export; understanding diff |
| **Acceptance criteria** | A QA user unfamiliar with the codebase can read the understanding and correctly describe what the application does, and can reach source evidence for any statement in ≤2 interactions |
| **Regression criteria** | RG-1 … RG-5, RG-9, RG-10 |
| **Enrichment** | CP-09 exploration and CP-10 reconciliation raise confidence and reduce the unverified share — **neither is required for the slice** |

---

## 3. Slice B — Understanding to Test Case

```text
UAU → Coverage obligations → Scenario → Test Case
```

> **User outcome:** QA can generate and inspect evidence-backed test scenarios and cases from the application understanding.

| | |
|---|---|
| **Prerequisite components** | Obligation model · technique registry · scenario generation · test case generation · data requirement model · evaluators · gate |
| **Checkpoints** | CP-03 (design and coverage) → **CP-04 (completes the slice)** |
| **Functional user test** | Review obligations and trace one to its source element; generate scenarios within limits; read the technique rationale; view a coverage report with its denominator and unverified share; generate test cases; read declared data requirements; approve at a gate |
| **Automated tests** | Obligation derivation; coverage arithmetic across five states; technique evidence-gating; ARCH-4 (no model may set coverage); no test case contains a locator |
| **Evidence** | Obligation ledger; generated scenario and case sets; coverage report; Validation F comparison |
| **Acceptance criteria** | A QA user can explain, for any generated scenario, which obligation it satisfies, which technique produced it and why, and what evidence supports it |
| **Regression criteria** | Slice A criteria plus RG-6, RG-7 |
| **Enrichment** | Reconciliation-confirmed elements reduce the insufficient-evidence share |

---

## 4. Slice C — Test Case to Execution

```text
Test Case → Automation Specification → Binding → Playwright → Real execution
```

> **User outcome:** QA can run a generated automated test against a real environment.

| | |
|---|---|
| **Prerequisite components** | Automation specification · binding · object model projection · locator lifecycle · execution manager · queue · workers · environment model · secret resolution · artifact capture |
| **Checkpoints** | CP-05 (automation) → **CP-06 (completes the slice)** |
| **Functional user test** | Generate automation from an approved case; bind it to an environment; run it; watch progress; open the trace, video and screenshots; run against a second environment without regenerating |
| **Automated tests** | One specification against two environments; object reuse; ARCH-5 (healing cannot reach intent); real execution in isolated workers; worker cannot reach the database; secret scan |
| **Evidence** | Specification and two bindings; execution records with artifacts; secret-scan report |
| **Acceptance criteria** | A QA user can run a generated test against a real environment and inspect complete diagnostic artifacts for the result |
| **Regression criteria** | Slice A and B criteria plus RG-8 |
| **Enrichment** | CP-09 runtime-validated locators materially raise pass rate; without them, locators remain candidates and failures may reflect locator resolution rather than the AUT |

---

## 5. Slice D — Execution to Finding

```text
Execution → Evaluation → Classification → Finding
```

> **User outcome:** QA can understand what happened and why.

| | |
|---|---|
| **Prerequisite components** | Evaluator registry · triage ladder · finding model · evidence assembly · run comparison |
| **Checkpoints** | **CP-07 (completes the slice)** |
| **Functional user test** | Open a failed test and see its cause; confirm a missing precondition is reported as data/state, not a defect; confirm an unknown-oracle failure is reported as insufficient evidence; follow a finding back to its test case, scenario and understanding version |
| **Automated tests** | Triage ladder ordering; evaluator layer precedence; a failed assertion alone yields no product defect finding; never-executed items excluded from pass rates |
| **Evidence** | Triage output; findings with evidence and lineage; run comparison |
| **Acceptance criteria** | For any failure, a QA user can state the cause category and see the evidence for that classification |
| **Regression criteria** | Slices A–C criteria |
| **Enrichment** | CP-10 reconciliation supplies the highest-value oracle-free defect candidates |

---

## 6. Slice E — The full Phase 1 journey

```text
Repository → Understanding → Test Design → Test Case/Data → Automation
    → Execution → Evaluation → Finding → Knowledge Update
```

> **User outcome:** the complete product loop, delivering evidence at every stage.

| | |
|---|---|
| **Prerequisite components** | All |
| **Checkpoints** | **CP-11 (completes the slice)**, hardened at CP-12 |
| **Functional user test** | From an empty workspace, complete the entire journey — **twice**: once with exploration enabled, once with it disabled |
| **Automated tests** | Both paths as end-to-end tests; full regression across all checkpoints; resumption at every stage boundary |
| **Evidence** | Two end-to-end run records with artifacts at every stage; a finding traced to source in one query |
| **Acceptance criteria** | Both paths complete. The static-only path produces usable output at lower confidence — **not failure** |
| **Regression criteria** | All of RG-1 … RG-10 and every prior slice |
| **Critical rule** | The static-only path failing is an **architectural failure**, not a known limitation (INV-4, ADR-002) |

---

## 7. Slice-to-checkpoint map

```text
CP-00  foundation ─────────────────────► prerequisite
CP-01  ingestion  ──┐
CP-02  understanding┴──────────────────► SLICE A complete
CP-03  test design ─┐
CP-04  case & data ─┴──────────────────► SLICE B complete
CP-05  automation ──┐
CP-06  execution ───┴──────────────────► SLICE C complete
CP-07  evaluation ─────────────────────► SLICE D complete
CP-08  governance ─────────────────────► cross-cutting
CP-09  exploration ────────────────────► enriches A, C
CP-10  reconciliation ─────────────────► enriches A, B, D
CP-11  integration ────────────────────► SLICE E complete
CP-12  hardening ──────────────────────► release readiness
```

**CP-09 and CP-10 enrich slices; they do not gate them.** A slice that cannot be delivered
without runtime exploration would contradict INV-4.

---

## 8. Using slices in planning

| Question | Answer |
|---|---|
| When is there something to demo? | At CP-02 (Slice A) — the first point where a QA user gets recognisable value |
| Can a slice be delivered early? | Only if every prerequisite component's checkpoint has passed. Slices do not bypass checkpoints |
| What if a slice passes but a checkpoint fails? | The checkpoint governs. A slice demonstration over an unvalidated component is a demo, not a delivery |
| Can slices reorder the roadmap? | No. They validate the roadmap's output; the roadmap remains canonical for sequencing |
