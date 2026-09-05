# 11 — Research and Open Decisions

**Status:** ACTIVE — canonical for what is not yet decided
**Last updated:** 2026-09-05

> **Rule: do not convert uncertainty into false certainty.** Where information is
> unavailable, record `UNKNOWN`, `REQUIRES_VALIDATION` or `OPEN_DECISION`. Do not
> fabricate requirements, architecture, provider capabilities, test results,
> performance figures, compatibility or implementation status.

---

## 1. Open decisions register

| ID | Question | Class | Blocks | Who can answer | Status |
|---|---|---|---|---|---|
| **OD-1** | May exploration perform state-changing actions, against which environment, with what reset capability? | OPEN | P4 entirely; Validations C, D, E; test-data provisioning strategy | Product Owner / environment owner | OPEN |
| **OD-2** | May source code and derived context reach an external LLM provider? If not, what inference options exist? | OPEN | P0-W7-T2 provider config; hosting model; quality ceiling | Client security / Product Owner | OPEN |
| **OD-3** | What is the Phase 1 AUT and its technology stack? | OPEN | P2 framework packs; Validations A, C; reference AUT for tests | Product Owner | OPEN |
| **OD-4** | Delivery window and team size/composition | OPEN | Phase sequencing commitment; DEC-002 | Delivery management | OPEN |
| **OD-5** | Code Intelligence API documentation, schema and a sandbox instance | OPEN | P3 evaluation; Validation B | Provider owner | OPEN |
| **OD-6** | Do repository-resident specifications, schemas and existing tests count as admissible evidence? | OPEN | P2 rule-extraction depth | Product Owner | OPEN |
| **OD-7** | Tenancy model and hosting target | OPEN | Infrastructure planning only — the data model is already safe either way | Product Owner / infrastructure | OPEN |
| **OD-8** | Does the AUT carry stable test attributes, and can they be added? | OPEN | P8 locator strategy quality; Validation E | AUT engineering team | OPEN |
| **OD-9** | Should the external evaluation harness be vendored into this repository? | OPEN | P2-W7-T1 calibration | Engineering lead | OPEN |

### Why OD-9 exists

The measured extraction figures that justify several architecture decisions
([`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) §6) were produced by a harness at
`c:\waynautic\skill-test\` — **outside this repository and outside its version control**.
The figures are transcribed into this repository so they survive, but the *ability to
re-measure* does not. Calibration (P2-W7-T1) needs a harness in-repo. Deciding whether to
vendor, rewrite or reference it is an open engineering decision.

---

## 2. What is blocked, and what is not

| Blocked now | Not blocked |
|---|---|
| P2 framework pack implementation (OD-3) | **All of Phase 0** |
| P3 provider evaluation (OD-5) | Phase 1 source ingestion |
| P4 write-enabled exploration (OD-1) | Contract definitions |
| Validations A, B, C (OD-1, OD-3, OD-5) | Domain primitives, persistence, queue, API, workflow |
| Infrastructure sizing (OD-4, OD-7) | Test framework and CI |
| Provider egress configuration (OD-2) | Governance and evaluator scaffolding |

> **Phase 0 is fully unblocked.** Nothing in the open register prevents starting work.
> Do not use an open decision as a reason to stall foundation work.

---

## 3. Provider placeholders

Two areas are intentionally unresolved. Their **contracts are real architecture**; their
**bindings are deliberately unselected**.

```text
CODE_INTELLIGENCE_PROVIDER    → docs/research/code-intelligence/
CRAWLER_PROVIDER              → docs/research/crawler/
```

### The research-first rule

Neither may be silently finalised. When research completes, this sequence is mandatory:

```text
1. Update the research document with findings and recommendation
2. Update 02_ARCHITECTURE_BASELINE.md
3. Update the relevant contract in 04_DOMAIN_AND_COMPONENT_CONTRACTS.md
4. Update 05_DETAILED_DEVELOPMENT_ROADMAP.md
5. Add a decision entry in 10_DECISION_AND_CHANGE_LOG.md
6. Update 12_CURRENT_IMPLEMENTATION_STATE.md
7. ONLY THEN implement the finalised provider integration
```

### What each research area must maintain

Both research directories carry the same structure:

| Section | Purpose |
|---|---|
| Problem statement | What the provider is for |
| Current architectural boundary | What the platform owns vs what the provider owns |
| Candidate approaches | Options under consideration, with sources |
| Evaluation criteria | How candidates will be judged |
| Capabilities required | Non-negotiable contract obligations |
| Limitations | Known constraints of each candidate |
| Security implications | Egress, credentials, trust |
| Performance implications | Latency, throughput, cost |
| Integration implications | Effort, coupling risk |
| Licensing / deployment | Where relevant to a product deliverable |
| Empirical validation | Which validation experiment applies and its result |
| Recommendation | Current position, may change |
| Final decision | `UNDECIDED` until made |
| Migration impact | What changes when the decision lands |

---

## 4. Assumptions requiring validation

These are **assumptions**, not requirements. Each must be confirmed or corrected.

| ID | Assumption | Impact if wrong | Status |
|---|---|---|---|
| A1 | Phase 1 targets web applications; browser-based execution | Playwright and the whole automation domain would need rethinking | REQUIRES_VALIDATION |
| A2 | The AUT stack is broadly comparable to the application used for prior measurements (TypeScript SPA + Node API) | Measured extraction figures do not transfer; Validation A must be re-run | REQUIRES_VALIDATION |
| A3 | A deployed AUT instance exists and is reachable for exploration and execution | Static-only becomes the only mode; P4, P9 blocked | REQUIRES_VALIDATION |
| A4 | Non-production test identities exist per role, obtainable non-interactively | Exploration and execution both stall at login | REQUIRES_VALIDATION |
| A5 | Generated automation is not committed back to the AUT repository | Would require write credentials and a review workflow | REQUIRES_VALIDATION |
| A6 | Reproducibility means inputs and lineage, not identical model output | Would require deterministic model settings and change cost assumptions | REQUIRES_VALIDATION |
| A7 | Repository history is available (full clone, not shallow) | Change detection and understanding-diff degrade | REQUIRES_VALIDATION |
| A8 | Phase 1 serves a small number of workspaces and concurrent users | Affects sizing only, not architecture | REQUIRES_VALIDATION |

---

## 5. Known unknowns

Recorded explicitly rather than assumed away:

| Area | What is unknown |
|---|---|
| Performance | No throughput, latency or scale targets exist. **UNKNOWN** |
| Cost | No LLM spend ceiling per run or per month. **UNKNOWN** |
| Accessibility | No accessibility requirements have been discussed. **UNKNOWN** |
| Data retention | Retention obligations for artifacts, evidence and model interaction logs. **UNKNOWN** |
| Operations | Who operates Phase 1; availability expectation. **UNKNOWN** |
| Defect tracker | Whether Jira/ADO integration is needed later. **UNKNOWN** |
| Existing test assets | Whether the AUT has manual test cases or automation to ingest. **UNKNOWN** |
| AUT authentication | Login mechanism (form, SSO, MFA, captcha). **UNKNOWN** — could block P4 and P9 entirely |

---

## 6. Decision-making authority

| Decision type | Who decides |
|---|---|
| Product scope and capability | Product Owner |
| Architecture and invariants | Solution Architect, recorded in the Decision Log |
| Implementation approach within a contract | Engineering — no decision entry needed (FLEXIBLE) |
| Technology selection with high migration cost | Engineering lead + Solution Architect, recorded |
| Provider selection | Research outcome + Solution Architect, recorded |
| Security policy (egress, environment write access) | Client security / Product Owner |

**An implementing agent may not resolve an OPEN decision unilaterally.** If work cannot
proceed without one, record the blocker, do the unblocked work, and surface the question.
