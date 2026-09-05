# 00 — Project Source of Truth

> **Read this document first.** It is the entry point for any engineer or AI agent
> joining this project. It tells you what this is, where development stands, and
> what to read next.

**Last updated:** 2026-09-05
**Document status:** ACTIVE — canonical
**Maintained by:** whoever performs the most recent implementation task

---

## 1. Project identity

| Field | Value |
|---|---|
| **Project name** | Agentic QA Automation Platform |
| **Repository** | `c:\waynautic\QA-Agentic` |
| **Phase** | Phase 1 |
| **Purpose** | Build and maintain a durable, evidence-backed understanding of an application under test (AUT), and generate measurable test coverage, maintainable Playwright automation, real execution and explainable findings from it |
| **Target users** | QA engineers and test leads; secondarily engineering leads consuming findings |
| **Problem solved** | Test generation today is either manual and slow, or LLM-driven and unverifiable. Neither produces a reusable model of the application, measurable coverage, or evidence for its claims |
| **Phase 1 objective** | A functional end-to-end platform: register repositories → build understanding → optionally explore the running app → reconcile → design tests → generate cases and data → generate Playwright → execute for real → evaluate → report findings, with evidence and traceability throughout |

### What makes this platform different

Three properties distinguish it from "an LLM that writes tests". They are architectural
commitments, not features:

1. **The durable asset is the understanding, not the tests.** Tests are regenerable;
   the Unified Application Understanding is not.
2. **Nothing is asserted without support.** Evidence, provenance, inference type,
   oracle status and confidence are modelled data. The platform can say
   *insufficient evidence* instead of inventing certainty.
3. **Coverage is computed, never claimed.** Coverage is arithmetic over explicit
   obligations derived from the understanding. No model is asked what the coverage is.

---

## 2. Architecture summary

```text
Understand AUT  (source repositories)
      ↓
Unified Application Understanding   ← THE CENTRAL PRODUCT ASSET
      ↓
Optional Runtime Exploration  (running AUT — valuable, not mandatory)
      ↓
Reconciliation  (relate observation to expectation)
      ↓
Test Design  (coverage obligations + scenarios)
      ↓
Test Case + Test Data
      ↓
Automation  (specification → binding → Playwright)
      ↓
Execution  (isolated workers, real browsers)
      ↓
Evaluation + Findings
      ↓
Knowledge Update  → feeds back into Understanding
```

**Deployment shape:** a modular monolith control plane plus three isolated worker
fleets (analysis, exploration, execution), connected by a job queue.

**Canonical architecture documents:** [`architecture/architecture-overview.html`](architecture/architecture-overview.html)
(open in a browser — 15 rendered diagrams) and [`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md)
(the Markdown canonical summary).

---

## 3. Current implementation status

> **HONEST STATEMENT OF FACT (2026-09-05):**
> **No platform code exists.** The repository contains architecture and planning
> documentation only. Nothing has been implemented, tested or validated.
> Statuses below read `NOT_STARTED` because that is true, not because it is unrecorded.
> The single exception is version control (P0-W1-T1), which is complete.

| Area | Status | Version | Evidence | Next action |
|---|---|---|---|---|
| Product vision | DEFINED | v2 | [`01_PRODUCT_VISION_AND_SCOPE.md`](01_PRODUCT_VISION_AND_SCOPE.md) | None — stable |
| Architecture baseline | DESIGNED | v2.1 | [`architecture/architecture-overview.html`](architecture/architecture-overview.html) | None — stable pending validation A/C |
| Component contracts | DESIGNED | v1 | [`04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](04_DOMAIN_AND_COMPONENT_CONTRACTS.md) | Refine during P0 |
| Development roadmap | DEFINED | v1 | [`05_DETAILED_DEVELOPMENT_ROADMAP.md`](05_DETAILED_DEVELOPMENT_ROADMAP.md) | Begin P0 |
| Repository scaffold | NOT_STARTED | — | — | P0-W1 |
| Persistence layer | NOT_STARTED | — | — | P0-W3 |
| API foundation | NOT_STARTED | — | — | P0-W5 |
| Workflow / job engine | NOT_STARTED | — | — | P0-W6 |
| Source ingestion | NOT_STARTED | — | — | P1 |
| Static understanding | NOT_STARTED | — | — | P2 |
| Code Intelligence provider | REQUIRES_DECISION | — | [`research/code-intelligence/`](research/code-intelligence/) | Research + OD-5 |
| Runtime exploration / crawler | REQUIRES_DECISION | — | [`research/crawler/`](research/crawler/) | Research + OD-1 |
| Reconciliation | NOT_STARTED | — | — | P5 |
| Coverage & scenario design | NOT_STARTED | — | — | P6 |
| Test case & data | NOT_STARTED | — | — | P7 |
| Playwright automation | NOT_STARTED | — | — | P8 |
| Execution | NOT_STARTED | — | — | P9 |
| Evaluation & findings | NOT_STARTED | — | — | P10 |
| Governance | NOT_STARTED | — | — | P11 |
| Excel export | NOT_STARTED | — | — | P13 |
| Test suite (platform's own) | NOT_STARTED | — | — | P0-W8 |
| CI/CD | NOT_STARTED | — | — | P0-W8 |
| Version control | **COMPLETE** | c4664f1 | 50 files on `origin/main`; secret scan clean | None — P0-W1-T1 done |

Full detail: [`12_CURRENT_IMPLEMENTATION_STATE.md`](12_CURRENT_IMPLEMENTATION_STATE.md)

---

## 4. Current position

```text
CURRENT DEVELOPMENT PHASE:   Phase 0 — Repository & Platform Foundation
CURRENT CHECKPOINT:          CP-00 Foundation (NOT_STARTED)
CURRENT WORKSTREAM:          P0-W1 — Repository scaffold and version control
CURRENT OBJECTIVE:           Establish a bootable, testable, version-controlled
                             platform skeleton with persistence, API, workflow and
                             artifact foundations

CURRENT BLOCKERS:
  - OD-3  Phase 1 AUT and technology stack unknown  → blocks P2 framework packs
  - OD-4  Delivery window and team size unknown     → blocks phase sequencing commitment
  - Language/runtime choice for control plane pending team confirmation (see DEC-002)

CURRENT OPEN DECISIONS:      OD-1 … OD-9 — see 11_RESEARCH_AND_OPEN_DECISIONS.md
                             None of OD-1..OD-9 blocks Phase 0.

PROVIDER RESEARCH:
  CODE_INTELLIGENCE_PROVIDER → OPEN   (research/code-intelligence/)
  CRAWLER_PROVIDER           → OPEN   (research/crawler/)

NEXT APPROVED STEP:          P0-W1-T2 — repository structure and module layout.
                             BLOCKED on DEC-002 (control-plane language); resolve
                             and record it before writing the first source file.
```

---

## 5. Source-of-truth hierarchy

Each kind of decision has exactly one controlling document. If two documents
disagree, the controlling document wins and the other is a defect to be fixed.

| Decision type | Controlling document |
|---|---|
| Product scope, capabilities, what Phase 1 means | [`01_PRODUCT_VISION_AND_SCOPE.md`](01_PRODUCT_VISION_AND_SCOPE.md) |
| Architecture, invariants, component boundaries | [`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) |
| The central data asset and its versioning rules | [`03_UNIFIED_APPLICATION_UNDERSTANDING.md`](03_UNIFIED_APPLICATION_UNDERSTANDING.md) |
| Component contracts, interfaces, replaceability | [`04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](04_DOMAIN_AND_COMPONENT_CONTRACTS.md) |
| What gets built, in what order, with what acceptance | [`05_DETAILED_DEVELOPMENT_ROADMAP.md`](05_DETAILED_DEVELOPMENT_ROADMAP.md) |
| Phase definitions and exit criteria | [`06_IMPLEMENTATION_PHASES.md`](06_IMPLEMENTATION_PHASES.md) |
| How a phase is proven complete | [`07_CHECKPOINT_AND_VALIDATION_PLAN.md`](07_CHECKPOINT_AND_VALIDATION_PLAN.md) |
| Requirement → code → test → evidence linkage | [`08_TRACEABILITY_MATRIX.md`](08_TRACEABILITY_MATRIX.md) |
| How the platform itself is tested | [`09_TEST_STRATEGY.md`](09_TEST_STRATEGY.md) |
| Any change to a previously recorded decision | [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md) |
| Unresolved questions and provider research | [`11_RESEARCH_AND_OPEN_DECISIONS.md`](11_RESEARCH_AND_OPEN_DECISIONS.md) |
| What is actually built right now | [`12_CURRENT_IMPLEMENTATION_STATE.md`](12_CURRENT_IMPLEMENTATION_STATE.md) |
| How an agent must work in this repository | [`13_CLAUDE_ENGINEERING_WORKING_PROTOCOL.md`](13_CLAUDE_ENGINEERING_WORKING_PROTOCOL.md) |

**The conversation that produced these documents is not source of truth.**
It may be gone. These documents are.

---

## 6. How a new session recovers full context

Follow this order. Do not skip steps, and do not begin work before step 7.

```text
1. Read this document (00)                     → what and where we are
2. Read 12_CURRENT_IMPLEMENTATION_STATE.md     → what actually exists today
3. Read 02_ARCHITECTURE_BASELINE.md            → the design you must not violate
4. Open architecture/architecture-overview.html → the diagrams (browser)
5. Read the active phase in 05_DETAILED_DEVELOPMENT_ROADMAP.md
6. Read the active checkpoint in checkpoints/
7. Read 11_RESEARCH_AND_OPEN_DECISIONS.md      → what you must NOT decide alone
8. Read 13_CLAUDE_ENGINEERING_WORKING_PROTOCOL.md → how to work here
9. Inspect the actual source tree and tests    → trust code over documents for STATE
10. Continue ONLY from "NEXT APPROVED STEP" in section 4 above
```

**Trust rule:** for questions of *intent and design*, documents win.
For questions of *what exists*, the code and test results win — and if they
disagree with document 12, document 12 is stale and fixing it is your first task.

---

## 7. The ten architectural invariants

These may not be violated by any implementation. Changing one requires a Decision
Log entry and an architecture update **before** any code changes.

| # | Invariant |
|---|---|
| INV-1 | Unified Application Understanding is the central asset; downstream generation consumes a versioned UAU |
| INV-2 | No downstream artifact is generated against an implicit, unversioned or unknown application context |
| INV-3 | Code Intelligence and Crawler are replaceable behind contracts; no provider type appears in domain code |
| INV-4 | Static understanding and runtime exploration are independent evidence sources; neither is a precondition for the platform to function |
| INV-5 | A reconciliation mismatch is not automatically a defect |
| INV-6 | Automation healing may change locator/binding resolution; it may never change test intent, assertion semantics, acceptance criteria or business purpose |
| INV-7 | Coverage is measured against explicit obligations, never claimed by a model |
| INV-8 | Evidence is first-class; important generated outcomes are evidence-backed and traceable |
| INV-9 | Human and autonomous workflows use one governance mechanism; autonomous mode bypasses no control |
| INV-10 | Browser, analysis and long-running workloads execute in isolated workers outside the trusted control plane |

---

## 8. Known repository risks

| Risk | Detail | Mitigation |
|---|---|---|
| **Prior art lives outside the repository** | Empirical measurements that justify several architecture decisions come from `c:\waynautic\skill-test\` (not in this repo, not under this repo's version control) | Key figures are transcribed into [`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) §6 and [`research/code-intelligence/`](research/code-intelligence/). If that directory is lost, the numbers survive here. Consider vendoring the harness during P2 — see OD-9 |
| **Empty version control** | Git is initialised but holds 0 commits and 0 tracked files. Nothing is yet recoverable through history | Finish P0-W1-T1: write `.gitignore` first, then commit |
| **Single-machine documents** | Architecture HTML previously existed only in temporary storage | Resolved: consolidated into `docs/architecture/` and diagram sources extracted to `.mmd` |
