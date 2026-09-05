# 01 — Product Vision and Scope

**Status:** ACTIVE — canonical for product scope
**Last updated:** 2026-09-05
**Controls:** what the product is, what Phase 1 includes, what "done" means for a capability

> This document is the authority on **what** we are building. It does not describe
> **how** — that is [`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md).

---

## 1. Vision

An enterprise QA platform that maintains an **evidence-backed, versioned understanding
of an application** and generates QA outcomes from it — measurable coverage, executable
test cases with realistic data, maintainable Playwright automation, real execution, and
findings a person can act on and challenge.

The platform is not fundamentally "an AI system that generates Playwright tests".
It is a knowledge system that happens to emit tests.

---

## 2. Product principles

| # | Principle | Consequence |
|---|---|---|
| PP-1 | The understanding is the durable asset | It is versioned, inspectable and outlives any test it produces |
| PP-2 | Nothing is asserted without support | Evidence, inference type, oracle status and confidence travel with every artifact |
| PP-3 | Coverage is computed, not claimed | Obligations are derived from the understanding; coverage is arithmetic over explicit links |
| PP-4 | Deterministic first, model last | Models are used where semantic judgement genuinely adds value, not as the default mechanism |
| PP-5 | The platform must be able to say "I don't know" | `Insufficient evidence` and `unknown oracle` are valid, first-class outcomes |
| PP-6 | Every component is replaceable | Providers, techniques, evaluators and whole components sit behind contracts |
| PP-7 | Humans stay in control by default | Autonomy is a policy at the same gate a person would use, never a separate path |

---

## 3. Scope — Application understanding

The platform ingests **one or more Git repositories** per workspace and builds a
durable model of the application.

**Repository handling**
- Frontend repository and backend repository in a single workspace (multi-repository is baseline, not an extension)
- Repository identity: URL, ref, resolved revision, credential reference, role (frontend/backend/other)
- The application version is a **version tuple** across repositories, not a single commit
- Cross-repository relationships are a first-class requirement

**What the understanding must represent**

| Category | Content |
|---|---|
| Structure | Entities, screens/pages, components, fields, APIs, backend services, data structures, configuration |
| Relationships | Navigation, component composition, frontend→backend call paths, service and data dependencies, cross-repository links |
| Behaviour | Validations, business rules, permissions and roles, states, transitions, success paths, failure paths, error handling |
| Grounding | Evidence, provenance, inference type, oracle status, reconciliation status, confidence, version |

**Frontend ↔ backend relationship chain** — the platform must attempt to establish,
with evidence per hop:

```text
UI Route → Page → Component → Event/Handler → Frontend Function
        → API Client → HTTP Request → Backend Endpoint → Controller
        → Service → Business Logic → Data Access → Persistence
```

Where a hop cannot be resolved statically it is recorded as **unresolved**, not guessed.

---

## 4. Scope — Runtime exploration

> **Runtime exploration is valuable but NOT mandatory for downstream activity.**

The platform supports three operating modes:

| Mode | Meaning |
|---|---|
| **Static-only** | No running instance available, reachable or permitted. Full pipeline still runs. Locators remain candidates, elements remain unverified, confidence is capped, coverage reports a larger insufficient-evidence share |
| **Static + exploration** | The intended default. Runtime confirms relationships and locators, raises confidence, and surfaces reconciliation differences |
| **Continuous** | Re-analysis on new revisions and periodic re-exploration; understanding diffs drive staleness and regeneration decisions |

**Requirement:** every downstream component must handle unverified input.
No component may treat runtime confirmation as a precondition. (INV-4)

**What exploration contributes:** actual pages, DOM, navigation, controls, workflows,
API traffic, responses, errors, state changes, validated locators, runtime-only behaviour,
dynamically generated content, and an explicit record of what it did *not* reach.

**Exploration must not fail silently.** Where it cannot proceed (a required identifier,
a one-time code, an upload, a prerequisite entity) it raises a **governed blocker** stating
what is required, why, what was attempted, and why each attempt was insufficient.

---

## 5. Scope — Reconciliation

Reconciliation relates runtime observation to static expectation and assigns a status:

`matched` · `runtime-only` · `static-only` · `different` · `conflict` · `unverified`

> **Mismatch is not defect.** (INV-5)

A difference has at least six possible explanations — incomplete exploration, stale static
context, environment or configuration difference, legitimate runtime variation, insufficient
evidence, or an actual defect. The platform must be able to express all six and must run
deterministic checks before any semantic judgement.

Reconciliation feeds: understanding, confidence, coverage, scenario generation, and — where
justified — findings.

---

## 6. Scope — Test design and coverage

> The platform systematically pursues **very high coverage** using multiple complementary
> test-design techniques. Coverage is measured against explicit obligations and reported
> with evidence, uncovered areas and insufficient-evidence gaps — rather than presented
> as an unqualified percentage.

**The LLM must never claim a coverage percentage.** (INV-7, PP-3)

**Required techniques** — all eight, as registered, extensible strategies:

| Technique | Requires from the understanding |
|---|---|
| Equivalence Partitioning | Field constraints, enums, type information |
| Boundary Value Analysis | Numeric/length/date constraints |
| Decision Tables | Business rules and guard conditions |
| Pairwise Testing | Multiple independent input dimensions |
| Permutation & Combination | Bounded state or option sets |
| Graph-Based Path Coverage | State and transition model |
| Metamorphic Testing | Relations that must hold across transformations |
| Model-Based Testing | Behavioural/state model |

A technique becomes **selectable only when the understanding supports it**, and the
selection rationale is recorded. This prevents applying a technique with no evidential basis.

**Coverage obligation states:** `covered` · `partially covered` · `uncovered` ·
`insufficient evidence` · `not applicable` (with recorded reason and actor).

Every coverage report states its denominator, the understanding version, and the share
of that denominator resting on unverified elements.

**Configurable generation limits** (per workspace): maximum scenarios, maximum test cases,
generation depth, coverage target, concurrency, execution scope, human/autonomous mode.

---

## 7. Scope — Test case and test data

The platform must understand application semantics: mandatory and optional fields,
radio buttons, dropdowns, conditional fields, invalid formats, min/max constraints,
relational dependencies. Generated cases and data must reflect those actual semantics,
not generic QA steps.

**Separation of concerns — required:**

```text
Test intent            what must be true
   ↓
Test case              steps, semantic element references, expected results, oracle status
   ↓
Data requirements      declared, not resolved
   ↓
Generated values       constraint-satisfying data
   ↓
State provisioning     creating application state
```

> **Generating a value is not equivalent to creating application state.**

Data resolution ladder, in order, each rung recording why the previous was insufficient:

1. Derive from the understanding (schema and constraint elements) — deterministic
2. Retrieve from an approved test-data source — read-only
3. Synthesise — constraint-satisfying, seeded, never implying real identities or payment instruments
4. **Provision state** — creates application state; risk-classified, policy-gated, environment-restricted, recorded for cleanup
5. Ask a human — a durable work item with full attempt history; supplied values classified on arrival

Test data may represent identity, credentials, an existing entity, application state,
prerequisite records, relationships or environment state — not merely values.

---

## 8. Scope — Automation

```text
Test Case
   ↓
Automation Specification      test intent — actions, semantic element references,
   ↓                          assertions, expected outcomes, data requirements
Automation Binding            concrete locators, environment, fixtures, resolved data
   ↓
Reusable Playwright Components   page and component objects projected from the understanding
   ↓
Executable Playwright Test
```

**Required qualities:** maintainability, reusability, page/component abstraction, locator
strategy, test-data separation, fixtures, environment configuration, assertions,
setup/teardown, diagnostics, traceability, scalability, self-healing.

**Healing constraint (INV-6):** healing may change concrete locator/binding resolution.
It may **never** change:

- test intent
- assertion semantics
- acceptance criteria
- business purpose
- test scope

Every healing action must be evidence-backed, auditable, observable, policy-controlled
and reviewable. Phase 1 default policy is **propose, not auto-apply**; auto-apply is a
per-workspace setting enabled on measured evidence.

Generated automation must be linked to its originating test case, scenario and
understanding version.

---

## 9. Scope — Execution

Real Playwright execution is required in Phase 1. Execution runs in **isolated workers**,
never inside the API process.

| Concern | Requirement |
|---|---|
| Environments | Configurable targets (e.g. DEV, QA, STAGING, UAT). Automation must not hard-code environment configuration |
| Secrets | Usernames, passwords, tokens, OAuth credentials must never appear in generated code, Git, LLM context, logs or reports. Secret references with runtime injection |
| Workers | Isolated processes/containers; no database credentials; results posted through a narrow callback |
| Concurrency | Configurable parallelism (1 = sequential, N = N parallel), bounded by global, per-environment and per-run ceilings |
| Artifacts | Execution logs, screenshots, traces, video where enabled, network logs, console logs |
| History | Run history, run-over-run comparison, change detection |
| Diagnostics | Enough to explain a failure, not merely record it |

---

## 10. Scope — Evaluation and findings

**Evaluator layers** — all three required, composed with deterministic first:

| Layer | Examples |
|---|---|
| Deterministic | Schema validation, required fields, reference validity, duplicate detection, traceability completeness, executable syntax, test-data rule validation, coverage arithmetic, secret scanning |
| Derived | Scenario↔test-case consistency, test-case↔automation consistency, field↔data compatibility, expected-result completeness, understanding↔artifact consistency |
| Semantic | Business behaviour plausibility, missing edge cases, logical consistency, requirement interpretation quality, diagnostic interpretation |

An evaluator layer must never be "ask another LLM whether the output looks good".
A semantic verdict may not overturn a deterministic one.

**Result states:** `pass` · `fail` · `attention` · `unknown`.
An item that was **never executed** carries an *assessment*, not a result, and is excluded
from pass-rate arithmetic and run comparison.

**Finding classification** — a failure is classified by cause before anything is called a
defect: infrastructure · environment · data/state · automation · insufficient evidence ·
product defect candidate.

**Confidence model** — every generated work item exposes:

- **Confidence band:** Mostly Confident · Somewhat Confident · Insufficient Evidence
- **Numeric confidence score**
- **Inference type:** Deterministic · Derived · Semantic
- **Oracle status:** Confirmed · Partially Confirmed · Unknown *(expectation-bearing items only)*
- **Reconciliation status**
- **Evidence:** traceable references to repository files, code locations, APIs, graph elements, previous work items, evaluator results or execution artifacts

These five concepts are **distinct and must not be collapsed**. Confidence is computed by
the platform; it is never self-reported by a model.

---

## 11. Scope — Governance

One mechanism, two deciders:

```text
Generate → Evaluate → Governance Gate
                          ├── Human      → Approve / Edit / Reject
                          └── Autonomous → Continue / Stop / Escalate
```

Autonomous mode must not bypass evaluator controls, safety policy, environment policy,
required approvals, or confidence/evidence constraints. (INV-9)

- Approved versions are immutable
- A human edit creates a new version, retains the machine original, and re-derives confidence
- Rejection is auditable with a recorded reason
- Escalation is one mechanism serving three cases: a stalled autonomous gate, an exploration blocker, and an unknown oracle

---

## 12. Scope — Traceability and export

**Traceability chain (INV-2, INV-8):**

```text
Repository version → Evidence → Understanding (versioned) → Coverage obligation
→ Scenario → Test Case → Test Data → Automation Spec → Automation Binding
→ Execution → Evaluation → Finding
```

**Excel export** — real downloadable `.xlsx` for test scenarios, test cases and test data.
Export logic is separated from domain logic so additional formats can be added later.

---

## 13. Phase 1 depth — what "delivered" means

> **All Phase 1 capabilities are real and functional within their bounded scope.**

A capability may have a bounded scope, a supported-stack limitation, an explicit default
policy, a measured quality limit, or a dependency on provider availability. Those bounds
must be **stated**, not hidden.

It is **not acceptable** to present as complete:

- placeholder code
- fake success
- simulated execution
- hardcoded results
- mock-only workflows

**The two intentional placeholders are provider-selection placeholders only:**

```text
CODE_INTELLIGENCE_PROVIDER
CRAWLER_PROVIDER
```

Their **contracts are real architecture**. They are not licence to create fake
functionality anywhere else in the platform. Any additional placeholder requires an
explicit Decision Log entry.

### Phase 1 capability list

All of the following are in Phase 1 scope and must be functionally delivered:

repository onboarding · multi-repository workspace · application/code understanding ·
application context graph · frontend/backend relationship discovery · runtime application
exploration · context reconciliation · functional application understanding · context
persistence and versioning · agentic test-scenario generation · coverage analysis ·
test-case generation · test-data generation and state handling · evidence · provenance ·
confidence · deterministic/derived/semantic inference classification · evaluators ·
human-in-the-loop workflow · autonomous workflow · Playwright automation generation ·
locator discovery and validation · self-healing (scope per research outcome) ·
real Playwright execution · environment handling · secret handling · worker execution ·
concurrency · artifact storage · result evaluation and classification · findings ·
run history · comparison · Excel export · configurable generation limits

**Architecturally extensible, not necessarily populated in Phase 1:**
multiple LLM providers · external agents · MCP servers · external tools ·
enterprise advisory agents · future context sources · future integrations

---

## 14. Explicit non-goals for Phase 1

| Non-goal | Note |
|---|---|
| Mobile or desktop native application testing | Web AUTs only (assumption A1 — requires validation) |
| Writing generated automation back into the AUT repository | No write-back path exists in Phase 1 |
| Defect-tracker integration (Jira/ADO) | Deferred; findings export/copy is sufficient |
| Multi-tenant SaaS operation | Single-tenant deployment with workspace scoping (OD-7) |
| Performance or security testing of the AUT | Functional QA only |
| Ingesting non-code context (requirements, wireframes, backlog items) | Architecture leaves the door open; not Phase 1 scope. See OD-6 for the repo-resident-artifact question |

---

## 15. UX reference

The Phase 1 UX prototype established the interaction model: workspace navigation,
staged workflow, evidence and confidence presentation, review/approval interactions,
findings, run history, and a capability/skill model.

Confirmed extension: the platform provides **durable artifact views** for Understanding,
Scenarios, Test Cases, Test Data, Automation, Execution/Runs, Findings and Capabilities.
The transcript remains the command and explanation surface — **not** the storage surface.

The UX must remain simple, workflow-oriented, evidence-driven, confidence-aware and
enterprise-appropriate, and must let a user see what the agent did, why, what evidence
supports it, how confident the system is, which evaluator checks ran, what needs
attention, and what happens next.
