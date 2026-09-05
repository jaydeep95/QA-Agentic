# 02 — Architecture Baseline

**Status:** ACTIVE — canonical for architecture
**Version:** v2.1
**Last updated:** 2026-09-05
**Rendered companion:** [`architecture/architecture-overview.html`](architecture/architecture-overview.html) — 15 diagrams, two-level document
**Superseded:** [`architecture/architecture-baseline-v1-SUPERSEDED.html`](architecture/architecture-baseline-v1-SUPERSEDED.html) — retained for history, see DEC-001

> This document is the Markdown canonical statement of the architecture. The HTML
> companion is the same architecture with rendered diagrams and expanded detail;
> where they differ, **this document wins** and the HTML is a defect to be corrected.

---

## 1. Architecture in one statement

A modular QA platform built around a **versioned Unified Application Understanding**,
combining static source understanding with optional runtime exploration and reconciliation.
Independent test-design, test-case, automation, execution and evaluation components consume
that understanding through explicit contracts. External capabilities such as Code Intelligence
and the Crawler remain replaceable providers. Evidence, confidence, governance and traceability
are cross-cutting. Heavy browser and analysis workloads run in isolated workers, while the core
platform remains modular and independently evolvable.

---

## 2. The central concept

> **Unified Application Understanding (UAU) is the central product asset.**

The architecture is **not** a "claims system". Claims, evidence, graph, state, document and
retrieval structures are **supporting representations and projections** of the UAU.

```text
                    UNIFIED APPLICATION UNDERSTANDING     ← canonical semantic model
                                  │
              ┌───────────────────┴───────────────────┐
              │                                       │
      Static Understanding                   Runtime Exploration
              │                                       │
              └───────────────────┬───────────────────┘
                                  ▼
                           Reconciliation
                                  ▼
                            Test Design
                                  ▼
                         Test Case + Data
                                  ▼
                    Automation Specification
                                  ▼
                        Automation Binding
                                  ▼
                            Execution
                                  ▼
                     Evaluation + Findings
                                  │
                                  └──────► Knowledge Update
```

### Canonical model vs supporting representations

| Layer | Content | Authority |
|---|---|---|
| **Canonical semantic model** | Unified Application Understanding — entities, screens, components, fields, APIs, backend services, data structures, relationships, validations, business rules, permissions, states, transitions, runtime observations, evidence, provenance, confidence, reconciliation | **Source of truth** |
| **Supporting representations** | Claims/assertions · evidence records · graph projection · state projection · domain projection · coverage/obligation projection · document projection · retrieval/indexing projection | Derived, rebuildable, replaceable |

Projections may be added, changed or removed without altering the canonical model.
Detail: [`03_UNIFIED_APPLICATION_UNDERSTANDING.md`](03_UNIFIED_APPLICATION_UNDERSTANDING.md).

---

## 3. Architectural invariants

Violating any of these requires a Decision Log entry and an architecture update **before**
implementation. They are repeated in [`00_PROJECT_SOURCE_OF_TRUTH.md`](00_PROJECT_SOURCE_OF_TRUTH.md) §7 for discoverability.

| # | Invariant | Enforcement point |
|---|---|---|
| INV-1 | UAU is central; downstream generation consumes a versioned UAU | Test Design, Case, Automation contracts all require a UAU version reference |
| INV-2 | No downstream artifact against implicit/unversioned/unknown context | Artifact schema — UAU version is a required, non-nullable field |
| INV-3 | Provider independence — Code Intelligence and Crawler replaceable behind contracts | Static check: zero provider imports in domain modules |
| INV-4 | Static and runtime are independent evidence sources; neither is a precondition | Static-only mode must pass its own end-to-end test |
| INV-5 | Mismatch is not defect | Reconciliation emits classified candidates, never findings directly |
| INV-6 | Healing cannot change intent | Intent lives in the Specification; the healing path has no write access to it |
| INV-7 | Coverage is obligation-based | Coverage API computes from links; no model output path can set a coverage value |
| INV-8 | Evidence is first-class | No understanding element may exist without at least one evidence reference |
| INV-9 | Governance is unified | Exactly one gate implementation; autonomy is a decider, not a code path |
| INV-10 | Workers are isolated | Workers hold no database credentials; results arrive through a narrow callback |

---

## 4. Domain decomposition

Five architectural domains. The finer module decomposition inside each is real and lives in
[`04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](04_DOMAIN_AND_COMPONENT_CONTRACTS.md); this section
defines ownership boundaries.

### 4.1 Understanding Domain

Components: Source Ingestion · Static Understanding · Runtime Exploration · Reconciliation · Unified Application Understanding

| Aspect | Definition |
|---|---|
| **Purpose** | Build, version and serve the platform's model of the AUT |
| **Owns** | Repository identity and revisions; extraction; exploration planning and state identity; reconciliation status; the versioned UAU and all its projections; evidence records |
| **Does NOT own** | What any of it means for testing. It has no notion of a scenario, a test case or coverage |
| **Inputs** | Repository references and credentials; AUT environment configuration; provider results |
| **Outputs** | Understanding versions; understanding queries and traversals; reconciliation statuses; exploration observations and blockers |
| **Contract** | `UnderstandingQuery`, `UnderstandingContribution`, `ObservationContract` |
| **Dependencies** | `CodeIntelligenceProvider` (optional), `CrawlerProvider` (optional), object storage, canonical store |
| **Replaceability** | Extractors, framework packs and providers all individually replaceable |
| **Scaling** | Analysis and exploration are worker workloads; the query surface is control-plane |
| **Security boundary** | Processes untrusted repository and page content; the structured-extraction boundary sits here |
| **Evolution boundary** | New extractors, packs and evidence sources are additive and require no downstream change |

### 4.2 Quality Design Domain

Components: Coverage · Scenario Generation · Test Case · Test Data

| Aspect | Definition |
|---|---|
| **Purpose** | Turn understanding into measurable coverage obligations and executable test intent |
| **Owns** | Coverage obligations and the satisfaction ledger; technique selection and rationale; scenarios; test cases; declared data requirements |
| **Does NOT own** | How anything executes. It produces no locators, no environment detail, no resolved data |
| **Inputs** | A named UAU version; obligation set; generation limits; approval decisions |
| **Outputs** | Scenario versions; test case versions; data requirement declarations; coverage reports |
| **Contract** | `ScenarioContract`, `TestCaseContract`, `DataRequirementContract` |
| **Dependencies** | Understanding Domain; `LanguageModelProvider`; Evaluators |
| **Replaceability** | Techniques are registered strategies; generation approach is internal |
| **Scaling** | Long-running, model-heavy, fan-out per feature; resumable |
| **Security boundary** | Consumes only structured understanding, never raw source |
| **Evolution boundary** | A new technique is a registry entry, not an orchestration change |

### 4.3 Automation Domain

Components: Automation Specification · Automation Binding · Page/Component Abstraction · Locator Discovery · Locator Validation · Healing

| Aspect | Definition |
|---|---|
| **Purpose** | Convert test intent into maintainable, reusable, executable Playwright |
| **Owns** | Automation specifications; page and component object model; locator lifecycle; environment-specific bindings; healing proposals; generated code artifacts |
| **Does NOT own** | Test intent. It may not add, remove or reinterpret an assertion or expected outcome |
| **Inputs** | Approved test cases with data requirements; UAU (for the object model and locator candidates); environment definitions |
| **Outputs** | Automation specification versions; bindings per environment; generated Playwright artifacts; healing events |
| **Contract** | `AutomationSpecificationContract`, `AutomationBindingContract` |
| **Dependencies** | Quality Design Domain; Understanding Domain; Secret provider |
| **Replaceability** | Code generation, object model strategy, locator ranking and healing algorithm all internal |
| **Scaling** | Deterministic generation; cheap relative to design and execution |
| **Security boundary** | Generated artifacts are secret-scanned before storage |
| **Evolution boundary** | Binding changes never invalidate a specification version (INV-6) |

### 4.4 Execution & Diagnostics Domain

Components: Execution · Workers · Environment · Results · Triage · Findings

| Aspect | Definition |
|---|---|
| **Purpose** | Run tests safely and scalably; classify what happened |
| **Owns** | Execution requests, jobs and attempts; environment definitions and leases; worker lifecycle; artifact collection; triage classification; findings of record; run history |
| **Does NOT own** | Test content. It never edits a specification or decides what *should* have happened |
| **Inputs** | Specification + binding; environment; concurrency and policy settings |
| **Outputs** | Attempts, results, artifacts, triage classifications, findings, run comparisons |
| **Contract** | `ExecutionRequestContract`, `ExecutionResultContract`, `FindingContract` |
| **Dependencies** | Automation Domain; secret provider; object storage; job queue |
| **Replaceability** | Worker technology, scheduling and isolation model are internal |
| **Scaling** | The primary scaling axis of the platform |
| **Security boundary** | Highest — holds AUT credentials, drives real browsers. Workers hold no DB credentials (INV-10) |
| **Evolution boundary** | Worker fleet scales and changes without control-plane change |

### 4.5 Platform & Governance Domain

Components: API · Workflow · Evaluators · Confidence · Evidence · Provenance · Governance · Provider Integration · Artifact Management

| Aspect | Definition |
|---|---|
| **Purpose** | Cross-cutting platform capability: orchestration, quality adjudication, decisions, integration, persistence of artifacts |
| **Owns** | Run/stage orchestration; evaluator registry; confidence computation and calibration; evidence and lineage; gates, policy, escalation and audit; the single provider integration path; artifact versioning and export |
| **Does NOT own** | Domain semantics. The workflow engine cannot tell a scenario from an exploration session; the gate does not know what it is gating |
| **Inputs** | Stage outputs; policies; provider configurations; human decisions |
| **Outputs** | Gate packages and decisions; evaluator verdicts; confidence scores; audit records; exports |
| **Contract** | `StageContract`, `EvaluatorContract`, `GateContract`, `Capability`/`Provider` contracts |
| **Dependencies** | Canonical store; secret provider; identity provider; external providers |
| **Replaceability** | Individual evaluators, policies and provider adapters are independently versioned |
| **Scaling** | Control-plane; small and transactional |
| **Security boundary** | The single egress point for all external calls |
| **Evolution boundary** | New evaluators, policies, capabilities and providers are additive |

---

## 5. Independent Evolution by Contract

> **Every major component must have a clear responsibility boundary so it can be
> independently improved, tuned, replaced, scaled or extended without significant
> impact on the rest of the platform.**

| Component | Independent evolution | Stable contract | What may change behind it |
|---|---|---|---|
| Static Understanding | Yes | `UnderstandingContribution` | Parsers, framework packs, analysis depth, supported stacks |
| Code Intelligence | Yes | `CodeIntelligenceProvider` **(PLACEHOLDER)** | Which service, or none — platform functions without it |
| Runtime Exploration | Yes | `CrawlerProvider` **(PLACEHOLDER)** | Crawler implementation, browser driver, exploration heuristics |
| Reconciliation | Yes | `Observation`/`Understanding` contract | Matching strategy, classification rules, heuristics |
| Test Design | Yes | `ScenarioContract` | Techniques, decomposition, generation approach |
| Test Case / Data | Yes | `TestCaseContract` / `DataRequirementContract` | Step generation, data strategies, provisioning paths |
| Automation | Yes | `AutomationSpecification` / `AutomationBinding` | Code generation, object model, locator strategy, healing |
| Execution | Yes | `ExecutionRequest` / `ExecutionResult` | Worker technology, scheduling, isolation, scaling |
| Evaluators | Yes | `EvaluatorContract` | Checks added, versioned or retired independently |
| LLM Provider | Yes | `LanguageModelProvider` | Vendor, model, routing policy, prompt versions |
| MCP / Tools / Agents | Yes | `Capability` contract | Any external tool or advisory agent |
| Storage & projections | Yes | `UnderstandingQuery` | Graph engine, index technology, projection set |

**The invariant that makes this work:** providers supply observations, candidates and raw
results. **Only platform-owned components turn those into understanding.** No external
service writes to the canonical model and no provider type appears in domain code. (INV-3)

---

## 6. Empirical evidence behind key decisions

These figures are **measured**, not estimated. Source: evaluation harness at
`c:\waynautic\skill-test\` (`evaluation_results.md`, `evaluation_scores.json`), run against
a hand-written ground truth on `cypress-io/cypress-realworld-app` — a representative
full-stack TypeScript application.

> **Recoverability note:** that harness is *outside this repository*. The figures are
> transcribed here so they survive its loss. See OD-9 about vendoring it.

| Extraction class | Precision | Recall | Architectural consequence |
|---|---:|---:|---|
| Entity inventory (endpoints, screens, tables) | 1.000 | 0.982 | Trusted as the coverage denominator and exploration agenda |
| Schema semantics (fields, types, optionality, enums) | 1.000 | 1.000 | Basis for field-level test data and validation obligations |
| Business rule extraction | 1.000 | 0.500 | Precise but incomplete — rule coverage reported as known-partial |
| Workflow / state discovery | 0.750 | 1.000 | Over-generous — enters as hypotheses for runtime confirmation |
| **Route→endpoint mapping** | **0.414** | **0.632** | The weak link, and the empirical case for runtime reconciliation |

### The four measured failure causes → four extractor rules

Every relationship error traced to one of four causes. Each becomes a binding rule:

| Cause | Rule |
|---|---|
| Unresolved dynamic path segments collapsed to wildcards that over-matched (one call site produced six wrong relationships) | An unresolvable segment yields an element marked `unresolved` and participates in **no** match |
| Framework routing semantics not modelled (registration order, first-match-wins) | Route matching is delegated to a **framework pack** modelling the real resolution algorithm |
| Over-broad transitive module reachability attributed endpoints to screens that never call them | Direct calls and transitive reachability are **different relations** with different confidence; reachability alone never produces a call relation |
| Dependency-injected services passed as props through type-only imports, erased at runtime | Statically unresolvable by construction — marked `unverified` and handed to exploration |

### Why confidence must be calibrated, not declared

The same evaluation recorded a skill banded **high** on the strength of a stated rule
("unresolvable dynamic paths are dropped rather than guessed") while the implementation did
the opposite and mis-reported more than it under-reported (17 false positives vs 7 false
negatives). **A declared confidence band is a claim about code that may simply be false.**
This is why the calibration harness is a first-class platform component, not a dev script.

---

## 7. Deployment model

```text
                Platform Control Plane
         ┌──────────────────────────────┐
         │ API                          │
         │ Domain modules               │
         │ Workflow                     │
         │ Governance                   │
         │ Evidence / lineage           │
         └──────────────┬───────────────┘
                        │
                     Job Queue
          ┌─────────────┼─────────────┐
          │             │             │
     Analysis       Exploration    Execution
      Workers          Workers       Workers
```

**Modular monolith control plane, three isolated worker fleets.** A module becomes a worker
only when it has a genuinely different resource profile, blast radius, runtime isolation need
or execution duration — not because it has a domain name.

| Fleet | Workload | Trust / blast radius | Concurrency governed by |
|---|---|---|---|
| Analysis | Clone, parse, extract | Processes untrusted source; no AUT credentials, no browser | Global pool; one build per workspace |
| Exploration | Drive a real browser through the AUT | Highest — holds AUT credentials, performs state-changing actions | Per-environment lease, action budget, policy allowlists |
| Execution | Run generated Playwright | High — holds AUT credentials and resolved secrets for the run | Global, per-environment and per-run limits |

**Not microservices.** Domain boundaries are enforced by module structure, dependency rules
and a static check. Extraction of a context into a service is possible later on measured need;
Static Understanding and Execution are the plausible candidates.

**Tenancy:** single-tenant deployment with workspace isolation enforced in the data model.
Correct under either answer to OD-7; scoping now is cheap and irreversible if omitted.

---

## 8. Security architecture

```text
Untrusted Inputs
      ↓
Security / Policy Boundary
      ↓
Trusted Platform Core
      ↓
Isolated Workers
```

| Control | Mechanism |
|---|---|
| Untrusted content | Repository and page content reach models only as structured, delimited, provenance-tagged **data**, explicitly framed as data. No content-derived text can authorise a tool invocation |
| Source confidentiality / egress | All external traffic passes one invocation gateway enforcing workspace egress policy, redaction, recording and cost metering. A prohibition on external inference is enforceable at that single point |
| Secrets | References everywhere; resolution only inside a worker at job start with a short-lived scoped token; log redaction; blocking secret scan on every generated artifact |
| Repository access | Read-only credentials as references; ephemeral per-job sandboxes; no write-back path in Phase 1 |
| Browser / session | Fresh context per test; per-environment domain allowlist enforced at the network layer; no persistent profile |
| Worker isolation | No database credentials; narrow authenticated callback only (INV-10) |
| Workspace scoping | Workspace identity on every record, enforced at the data-access layer |
| Generated content | All model-generated strings render as text, never as markup — they originate from client source code and are never assumed safe |
| Tool authorization | Declared scopes and trust level per capability; a tool result is data, never an instruction |
| Audit | Append-only log of every gate decision, policy evaluation, healing event, secret resolution, provider invocation and human edit |

---

## 9. Key architecture decisions

Summarised here; full records with alternatives and consequences in the HTML companion
(section D11) and in [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md).

| # | Decision | Class |
|---|---|---|
| 1 | UAU is the canonical asset; everything downstream is generated from it | FIXED |
| 2 | No artifact without a named understanding version | FIXED |
| 3 | Understanding recorded as versioned, evidence-backed assertions with content-derived identity | FLEXIBLE |
| 4 | Evidence is append-only; elements superseded, never silently overwritten | FIXED |
| 5 | Projections derived and rebuildable; the graph is a view, not the model | FIXED |
| 6 | Runtime exploration optional; static-only fully supported | FIXED |
| 7 | Providers produce observations; only the platform builds understanding | FIXED |
| 8 | Code Intelligence is an advisory contributor, not an authority | PLACEHOLDER |
| 9 | Crawler is actuator and sensor; planning, state identity, safety and interpretation stay on the platform | PLACEHOLDER |
| 10 | Relationship extraction under-reports rather than mis-reports | FIXED |
| 11 | Runtime state identity is a composite fingerprint, not a URL | FLEXIBLE |
| 12 | Exploration action risk is classified before acting, using the understanding | FIXED |
| 13 | Mismatch is not defect | FIXED |
| 14 | Coverage computed from explicit obligations | FIXED |
| 15 | Confidence computed by the platform from calibrated inputs | FIXED |
| 16 | Specification and binding are separate artifacts | FIXED |
| 17 | Page/component objects projected from the understanding | FIXED |
| 18 | Self-healing default policy is propose, not apply | FLEXIBLE |
| 19 | One gate implementation for human and autonomous modes | FIXED |
| 20 | Workflow state in the product database, not a separate engine | FLEXIBLE |
| 21 | One capability-invocation path for every external call | FIXED |
| 22 | Workers hold no database credentials | FIXED |
| 23 | Repository and page content are untrusted data | FIXED |
| 24 | Single-tenant with workspace scoping in the data model | OPEN (safe either way) |
| 25 | Modular monolith control plane with three worker fleets | FIXED |

---

## 10. What is deliberately not decided

| Area | Status | Where tracked |
|---|---|---|
| Code Intelligence provider | PLACEHOLDER | [`research/code-intelligence/`](research/code-intelligence/) |
| Crawler provider | PLACEHOLDER | [`research/crawler/`](research/crawler/) |
| Graph projection engine (in-process vs external) | FLEXIBLE | Decide on measured query need |
| Retrieval/index technology | FLEXIBLE | No vector store in Phase 1 — no demonstrated need |
| Control-plane language/runtime | OPEN | DEC-002 — pending team confirmation |
| Canonical store product | FLEXIBLE | Requires transactional guarantees; product choice open |
| Queue technology | FLEXIBLE | Database-backed initially, behind a dispatch port |
| LLM vendor and adapter count | FLEXIBLE | Abstraction FIXED (ADR-013); vendor not fixed; **adapter count PROVISIONAL** (ADR-030) — at least one real adapter before LLM-dependent validation |
| Hosting target | OPEN | OD-7 |

**Technology is subordinate to architecture.** Every technology name can be removed from
this document and the architecture still stands. That is the test it is designed to pass.
