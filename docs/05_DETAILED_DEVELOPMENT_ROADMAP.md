# 05 — Detailed Development Roadmap

**Status:** ACTIVE — canonical for what gets built and in what order
**Last updated:** 2026-09-05
**Controls:** phases, stages, work items, dependencies, acceptance criteria, evidence

---

## PROJECT POSITION

```text
PHASE:        Phase 0 — Repository & Platform Foundation
STAGE:        P0-S1 — Repository scaffold
CHECKPOINT:   CP-00 Platform Foundation
STATUS:       NOT_STARTED
DECOMPOSITION: P0 is at implementation-ready level (rolling-detail rule below)

COMPLETED:    P0-W1-T1  version control established — commit c4664f1 on origin/main
              Roadmap V2 correction — DEC-005..009, 13-checkpoint model,
              decision register, consistency audit clean

CURRENT:      P0-W1-T2  repository structure and module layout
              BLOCKED on DEC-002 (control-plane language)

NEXT:         P0-W1-T3 → P0-W1-T4 → P0-W2 (domain skeleton)

BLOCKED:      P2 framework packs      ← OD-3 (AUT stack unknown)
              Phase sequencing commit ← OD-4 (delivery window/team unknown)

OPEN DECISIONS:
              OD-1 exploration write policy      → blocks P4 detail
              OD-2 source egress to LLM          → blocks P0-W7 provider config
              OD-3 Phase 1 AUT + stack           → blocks P2 packs
              OD-4 delivery window + team        → blocks sequencing
              OD-5 Code Intelligence access      → blocks P3 evaluation
              OD-6 repo-resident artifacts as evidence → affects P2 depth
              OD-7 tenancy + hosting             → blocks infra planning only
              OD-8 AUT stable test attributes    → affects P8 locator strategy
              OD-9 vendor the evaluation harness → affects P2 calibration

PROVIDER RESEARCH:
              Code Intelligence → OPEN   (ADR-041 · contract FIXED, binding OPEN)
              Crawler           → OPEN   (ADR-040 · contract FIXED, binding OPEN)

DECISION CLASSIFICATION:  see 14_ARCHITECTURAL_DECISION_REGISTER.md
              All 15 numeric thresholds are PROVISIONAL, not acceptance requirements.
              THR-SAFETY-001 (destructive-action recall 1.00) awaits Product Owner
              confirmation as a FIXED safety requirement.
```

> **Update this block whenever the development stage changes.** It is the first thing a
> new session reads after document 00.

---

## Roadmap hierarchy and identifiers

```text
Phase (P0..P13) → Stage → Workstream (Wn) → Task (Tn) → Test → Evidence
```

**Work item ID format:** `P{phase}-W{workstream}-T{task}` — e.g. `P0-W3-T2`

**Status model** (used throughout; no vague states). Full definitions and the rules
they enforce: [`06_IMPLEMENTATION_PHASES.md`](06_IMPLEMENTATION_PHASES.md) §1.

| Status | Objective meaning |
|---|---|
| `NOT_STARTED` | No work begun |
| `PLANNED` | Scoped and sequenced, not started |
| `IN_PROGRESS` | Actively being implemented |
| `IMPLEMENTED` | Code complete and compiling; tests not yet run |
| `TESTING` | Tests are being executed; results not yet complete |
| `TESTED` | Tests completed with **recorded results** (commit hash + output) |
| `VALIDATED` | A checkpoint demonstrated the capability, both dimensions; evidence captured |
| `ACCEPTED` | Product or architecture acceptance recorded by the responsible party |
| `BLOCKED` | Cannot proceed — blocker recorded |
| `DEFERRED` | Consciously postponed — reason recorded |
| `REQUIRES_DECISION` | Needs an open decision resolved first |
| `COMPLETE` | Accepted **and** validated **and** no unresolved blocking issue remains |

---

## Phase summary and dependency graph

| Phase | Name | Development depends on | Validation depends on | Parallel with | Checkpoint |
|---|---|---|---|---|---|
| **P0** | Repository & Platform Foundation *(incl. governance foundations)* | — | — | Provider research | CP-00 |
| **P1** | Application Source Ingestion | P0 | P0 | Provider research | CP-01 |
| **P2** | Static Application Understanding | P1 | P1 + reference corpus (OD-3) | P4 research | CP-02 |
| **P3** | Code Intelligence Provider Integration | P2 (contract only) | OD-5 + Validation B | P4, P5, P6 | CP-02 |
| **P4** | Runtime Exploration / Crawler | P0, P2 | **OD-1** + Validation C | P3, P5, P6…P11 | CP-09 |
| **P5** | Static/Runtime Reconciliation | **P2 only** — observations may be recorded, synthetic or fixture | P2 + observation dataset + Validation D | P3, P4, P6…P11 | CP-10 |
| **P6** | Coverage & Test Scenario Generation | P2 | P2 + Validation F | P4, P5 | CP-03 |
| **P7** | Test Case & Test Data | P6 | P6 + an environment | P4, P5 | CP-04 |
| **P8** | Playwright Automation | P7, P2 | P7 + Validation E | P4, P5 | CP-05 |
| **P9** | Real Execution | P8, P0 | P8 + AUT environment | P4, P5 | CP-06 |
| **P10** | Evaluation, Triage & Findings | P9 | P9 | P11 | CP-07 |
| **P11** | Governance (full capability) | **P0 foundations** | Any generating stage | P6…P10 | CP-08 |
| **P12** | End-to-End Integration | P1…P11 | All prior checkpoints | — | CP-11 |
| **P13** | Phase 1 Hardening | P12 | P12 + OD-4, OD-7 | — | CP-12 |

**Critical path:** P0 → P1 → P2 → P6 → P7 → P8 → P9 → P10 → P12 → P13
**Off critical path (parallelisable):** P3, P4, P5, P11

> **P4 and P5 are not on the critical path.** Static-only operation is fully supported
> (ADR-002 / INV-4): exploration adds validation and confidence, it does not gate delivery.
>
> **P5 does not depend on P4.** Reconciliation develops and is validated against
> **recorded, synthetic or fixture observations**. A live `CrawlerProvider` is *optional
> enrichment* that replaces fixtures with real observations — never a development
> prerequisite. Treating it as one would contradict INV-4.

```text
                Static Understanding (P2)
                          │
              ┌───────────┴────────────┐
              ▼                        ▼
   Recorded / synthetic /        Live CrawlerProvider (P4)
   fixture observations          (when available — OD-1)
              │                        │
              └───────────┬────────────┘
                          ▼
                 Reconciliation (P5)
```

---

# PHASE 0 — Repository & Platform Foundation

**Status:** `NOT_STARTED` · **Checkpoint:** CP-00 · **Depends on:** nothing · **Blocks:** everything

### Goal
A bootable, testable, version-controlled platform skeleton that can persist state, execute
controlled workflows, store artifacts and run its own automated test suite reliably.

### Scope
Repository structure · development environment · configuration · core domain foundations ·
persistence · artifact management · API foundation · workflow/job foundations · observability ·
testing foundation · security boundaries · **governance foundations**.

> **V2 correction — governance moved earlier.** Governance is cross-cutting and must not
> first appear as a late-stage feature. The gate contract, decision state model, approval
> states, human/autonomous decider abstraction, mandatory policy hook and audit record are
> built here (P0-W10). Rich approval UI, policy configuration, autonomous behaviour and
> escalation remain at P11 / CP-08. A gate retrofitted late is a gate that stages have
> already learned to work around.

### Non-goals
No understanding, no extraction, no browsers, no LLM calls, no test generation.
Phase 0 builds the frame, not the picture.

### Architecture references
[`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) §7 (deployment), §8 (security);
[`04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](04_DOMAIN_AND_COMPONENT_CONTRACTS.md) §1, §2, §10, §12.

### Work items

| ID | Title | Purpose | Depends | Status |
|---|---|---|---|---|
| P0-W1-T1 | Initialise git repository | Version control | — | **COMPLETE** `c4664f1` |
| P0-W1-T2 | Repository structure and module layout | Enforce domain boundaries in the file system | T1 | NOT_STARTED |
| P0-W1-T3 | Development environment + dependency management | Reproducible local setup | T1 | NOT_STARTED |
| P0-W1-T4 | Configuration model (env, workspace, policy) | No hard-coded environment values anywhere | T3 | NOT_STARTED |
| P0-W2-T1 | Core domain primitives: workspace, artifact, version, lineage | The work-item envelope every artifact shares | T2 | NOT_STARTED |
| P0-W2-T2 | Evidence and provenance primitives | INV-8 foundation | W2-T1 | NOT_STARTED |
| P0-W2-T3 | Status/state machine primitives | Uniform status model | W2-T1 | NOT_STARTED |
| P0-W3-T1 | Canonical store schema + migrations | Transactional persistence | W2 | NOT_STARTED |
| P0-W3-T2 | Workspace scoping enforced at data-access layer | INV / OD-7 safety under either tenancy answer | W3-T1 | NOT_STARTED |
| P0-W3-T3 | Artifact versioning + immutability of approved versions | INV-2 groundwork | W3-T1 | NOT_STARTED |
| P0-W4-T1 | Object storage abstraction | Large payloads by reference (UAU-R9) | W2 | NOT_STARTED |
| P0-W4-T2 | Artifact manifest + retention tiers | Trace/video lifecycle | W4-T1 | NOT_STARTED |
| P0-W5-T1 | API foundation: routing, auth, error model | Frontend/backend separation | W3 | NOT_STARTED |
| P0-W5-T2 | Worker callback API (narrow, authenticated) | INV-10 — workers hold no DB credentials | W5-T1 | NOT_STARTED |
| P0-W5-T3 | Read-model pattern for artifact views | UI responsiveness | W5-T1 | NOT_STARTED |
| P0-W6-T1 | Run/stage state machine (durable, in product DB) | Decision 20 | W3 | NOT_STARTED |
| P0-W6-T2 | Job queue + dispatch port | Async foundation | W6-T1 | NOT_STARTED |
| P0-W6-T3 | Worker harness: lease, heartbeat, idempotency, cancellation | Resumability (U4) | W6-T2 | NOT_STARTED |
| P0-W7-T1 | Capability registry + Capability Invoker | INV-3, single egress (Decision 21) | W3 | NOT_STARTED |
| P0-W7-T2 | `LanguageModelProvider` port + **two** adapters | Contract with one impl is an assumption | W7-T1 | REQUIRES_DECISION (OD-2) |
| P0-W7-T3 | Secret provider port + reference resolution | U5, no secrets in artifacts | W7-T1 | NOT_STARTED |
| P0-W8-T1 | Test framework + unit/integration/contract layers | [`09_TEST_STRATEGY.md`](09_TEST_STRATEGY.md) | W2 | NOT_STARTED |
| P0-W8-T2 | CI pipeline: lint, type-check, test, boundary check | Enforce module boundaries mechanically | W8-T1 | NOT_STARTED |
| P0-W8-T3 | Architecture boundary static check | INV-3: zero provider imports in domain | W8-T2 | NOT_STARTED |
| P0-W9-T1 | Structured logging + correlation ids | Observability from the start | W5 | NOT_STARTED |
| P0-W9-T2 | Audit log (append-only) | INV-9 groundwork | W3 | NOT_STARTED |
| P0-W9-T3 | Secret-pattern scanner as a blocking check | Security control | W7-T3 | NOT_STARTED |
| **P0-W10-T1** | **Gate contract + single gate implementation** | Governance foundations — moved earlier in V2 (INV-9, ARCH-6) | W3 | NOT_STARTED |
| **P0-W10-T2** | **Decision state model + approval states** | Immutable approved versions (ADR-003) | W10-T1 | NOT_STARTED |
| **P0-W10-T3** | **Human/autonomous decider abstraction** | One gate, two deciders — not two code paths | W10-T1 | NOT_STARTED |
| **P0-W10-T4** | **Mandatory policy check hook + audit record** | Autonomy can never bypass a control | W10-T1, W9-T2 | NOT_STARTED |

### Detailed specification — P0-W1-T1 (the immediate next step)

| Field | Value |
|---|---|
| **Purpose** | Place the repository under version control so all subsequent work is recoverable and reviewable |
| **Why needed** | The repository is initialised but empty: 0 commits, 0 tracked files. Every later guarantee about traceability depends on a real history existing |
| **Preconditions** | `/docs` tree exists (satisfied by this bootstrap). `git init` already run — repository exists on `master` with 0 commits |
| **Inputs** | Current repository contents |
| **Outputs** | Initialised git repository; `.gitignore`; initial commit containing `/docs` |
| **Components touched** | None (infrastructure) |
| **Contract impact** | None |
| **Data model impact** | None |
| **Security impact** | `.gitignore` must exclude secrets, `.env`, credentials, and large generated artifacts from the outset |
| **Observability impact** | None |
| **Tests required** | None (verified by inspection) |
| **Acceptance criteria** | `git log` shows one commit; `git status` clean; `/docs` tracked; no secret-bearing file tracked |
| **Validation method** | `git rev-parse --is-inside-work-tree` returns true; `git ls-files` lists the docs tree |
| **Expected evidence** | Commit hash recorded in [`12_CURRENT_IMPLEMENTATION_STATE.md`](12_CURRENT_IMPLEMENTATION_STATE.md) |
| **Risks** | Accidentally committing credentials from a sibling directory — mitigate with `.gitignore` first |
| **Open decisions** | None |
| **Checkpoint** | CP-00 |
| **Status** | `COMPLETE` - commit `c4664f1` pushed to `origin/main`; 50 files tracked, secret scan clean |

### Phase 0 acceptance criteria
- [ ] Repository under version control with CI running on every commit
- [ ] Platform boots locally from documented steps
- [ ] A workspace can be created and persisted
- [ ] An artifact can be created, versioned, and its lineage queried
- [ ] Evidence can be attached to an artifact and resolved back
- [ ] A job can be enqueued, picked up by a worker, and its result recorded via callback
- [ ] Worker cannot reach the database directly (verified by test)
- [ ] A capability invocation is recorded as provenance
- [ ] Secrets resolve from references and never appear in logs or stored artifacts
- [ ] Boundary check fails the build if a domain module imports a provider SDK
- [ ] Full test suite runs green in CI

### Exit criteria → CP-00
See [`checkpoints/checkpoint-00-platform-foundation.md`](checkpoints/checkpoint-00-platform-foundation.md).

---

# PHASE 1 — Application Source Ingestion

**Status:** `NOT_STARTED` · **Checkpoint:** CP-01 · **Depends on:** P0

### Goal
A real frontend/backend repository set can be registered, acquired, versioned and
represented durably.

### Scope
Repository registration (URL, ref, credential reference, role) · Git acquisition into an
analysis sandbox · repository metadata · multi-repository workspace representation ·
revision resolution and pinning · **version tuple** formation · source snapshots ·
source/version lineage.

### Non-goals
No parsing, no understanding. Acquisition and identity only.

### Key work items

| ID | Title | Notes |
|---|---|---|
| P1-W1-T1 | Repository registration API + model | Must capture ref and credential reference — the UX prototype omitted both |
| P1-W1-T2 | Multi-repository workspace model | Version tuple across N repositories |
| P1-W2-T1 | Git acquisition into ephemeral sandbox | Read-only credentials, no write-back path (Phase 1 non-goal) |
| P1-W2-T2 | Revision resolution and pinning | Immutable once referenced |
| P1-W2-T3 | Source snapshot + content hashing | Foundation for evidence anchoring and incremental rescan |
| P1-W3-T1 | Repository/version lineage records | Traceability root |

### Acceptance criteria
- [ ] Two repositories (frontend + backend) register into one workspace
- [ ] Each resolves to a pinned revision; the workspace exposes a version tuple
- [ ] Source is acquired into an isolated sandbox with no persistent credential
- [ ] Content hashes recorded per file for later evidence anchoring
- [ ] Re-registering the same revision is idempotent

---

# PHASE 2 — Static Application Understanding

**Status:** `NOT_STARTED` · **Checkpoint:** CP-02 · **Depends on:** P1 · **Partially blocked by:** OD-3

### Goal
Given a supported real application repository set, the platform creates a durable,
inspectable, evidence-backed Unified Application Understanding.

> **This phase must not produce mock context.** A mock UAU fails the checkpoint.

### Scope
Stack detection · framework pack selection · extractor pipeline · entity discovery (screens,
pages, components, fields, APIs, services, data structures) · schema and constraint extraction ·
rule extraction · relationship extraction including frontend↔backend · locator candidate
extraction · normalisation to assertions with content-derived identity · corroboration ·
inference typing · confidence priors · understanding versioning · incremental rescan ·
understanding diff · document projection · graph projection.

### Critical constraints from measured evidence
See [`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) §6. Four rules are **binding**:

| Rule | Requirement |
|---|---|
| R1 | An unresolvable dynamic path segment yields an `unresolved` element and participates in **no** match |
| R2 | Route matching is delegated to a framework pack modelling the real resolution algorithm (registration order, first-match-wins) |
| R3 | Direct calls and transitive reachability are different relations; reachability alone never produces a call relation |
| R4 | Statically unresolvable relations (DI, type-only imports) are marked `unverified` and handed to exploration — never guessed |

### Key work items

| ID | Title | Notes |
|---|---|---|
| P2-W1-T1 | Analysis worker + sandbox execution | Untrusted content boundary |
| P2-W1-T2 | Stack detection | Determines pack selection |
| P2-W2-T1 | Framework pack interface | The extensibility unit (Decision 13) |
| P2-W2-T2 | First framework pack | **REQUIRES_DECISION — OD-3** |
| P2-W3-T1 | Inventory extractors | Measured P 1.000 / R 0.982 — the reliable class |
| P2-W3-T2 | Schema/constraint extractors | Measured 1.000/1.000 |
| P2-W3-T3 | Rule extractors | Measured recall ~0.500 — report as known-partial |
| P2-W3-T4 | Relationship extractors under rules R1–R4 | The weak class — Validation A gates this |
| P2-W3-T5 | Locator candidate extractors | Feeds P8 |
| P2-W4-T1 | Normalisation + content-derived identity | UAU-R3 |
| P2-W4-T2 | Corroboration across extractors | Independent evidence signal |
| P2-W4-T3 | Confidence prior assignment from calibration | UAU-R7 |
| P2-W5-T1 | Understanding version assembly | UAU-R4 |
| P2-W5-T2 | Incremental rescan + staleness cascade | UAU-R10 |
| P2-W5-T3 | Understanding diff | Set difference on identity |
| P2-W6-T1 | Graph + document projections | UAU-R6 |
| P2-W7-T1 | **Calibration harness + ground-truth corpus** | First-class component, not a script |

### Acceptance criteria
- [ ] A real repository set produces a UAU with ≥1 evidence reference per element
- [ ] Every element carries inference type, oracle status where applicable, and computed confidence
- [ ] Unresolved relations are marked, not guessed (R1–R4 verified by test)
- [ ] Understanding is versioned and pins the full repository tuple
- [ ] A second scan of the same revision is idempotent
- [ ] A changed revision produces a diff and cascades staleness
- [ ] Document projection is human-readable and inspectable in the UI
- [ ] Calibration harness reports per-extractor precision/recall, never averaged

---

# PHASE 3 — Code Intelligence Provider Integration

**Status:** `REQUIRES_DECISION` · **Checkpoint:** CP-02 · **Depends on:** P2 contract, OD-5

> **This is a controlled placeholder phase.** Do not invent a concrete provider as a final
> architectural decision.

### Scope
| Item | State |
|---|---|
| Current abstraction | `CodeIntelligenceProvider` — defined in [`04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](04_DOMAIN_AND_COMPONENT_CONTRACTS.md) §3 |
| Expected responsibilities | Repository analysis, symbol/entity discovery, relationships, semantic retrieval, contextual understanding |
| Integration point | One advisory contributor in the P2 extractor pipeline |
| Test provider strategy | A **contract-conformance fake** for tests only — never presented as a working provider, never used in a checkpoint that requires functional understanding |
| Intentionally deferred | Provider selection, API binding, performance characteristics |
| Research required | [`research/code-intelligence/`](research/code-intelligence/) |
| Evaluation criteria | Validation B — see [`07_CHECKPOINT_AND_VALIDATION_PLAN.md`](07_CHECKPOINT_AND_VALIDATION_PLAN.md) |
| Decision condition | Provider access obtained (OD-5) **and** Validation B executed |
| Migration path | Bind adapter → run conformance suite → re-run calibration → update Decision Log → update this phase |

### Acceptance criteria
- [ ] The port exists with a conformance suite
- [ ] The platform builds understanding **with the adapter absent** (degraded-mode test passes)
- [ ] No provider type appears in any domain module (boundary check)
- [ ] Research document records candidates, criteria and current recommendation

---

# PHASE 4 — Runtime Exploration / Crawler

**Status:** `REQUIRES_DECISION` · **Checkpoint:** CP-09 · **Depends on:** P0, P2 · **Validation blocked by OD-1**

> Second intentional placeholder. The architecture supports `CrawlerProvider`;
> provider selection remains open.

### Scope
Runtime environment configuration · browser/session lifecycle · authentication strategy ·
**state identity** · page discovery · action discovery · **action-risk classification** ·
safe action policy · state-changing action control · human checkpoint (blockers) ·
navigation · DOM observation · network observation · screenshot capture · locator probing ·
runtime evidence · exploration planning from the UAU agenda · exploration limits and budgets ·
failure recovery · blocked states · exploration history.

### Critical design elements

| Element | Requirement |
|---|---|
| **State identity** | Composite: route template + structural fingerprint + affordance set + actor context. **URL alone is insufficient** (Decision 11) |
| **Action risk** | Classified **before** acting using the UAU: read · benign write · destructive · external effect · unknown. **Unknown defaults to not permitted** (Decision 12) |
| **Blockers** | Durable, attributable, resumable work items stating requirement, reason, attempts made, why each failed |
| **Budgets** | Actions, wall-clock, artifact volume — hard limits per session |
| **Ownership** | Platform owns planning, state identity, safety, reconciliation, evidence and interpretation. Provider owns browser control and observation capture only |

### Acceptance criteria
- [ ] Controlled exploration runs against an approved AUT environment
- [ ] The provider implementation is swappable with no change to state model or planning
- [ ] Destructive-action classification recall is **1.00** on a hand-labelled control set before any write-enabled session
- [ ] Blocked exploration produces a governed work item, never a silent failure
- [ ] Exploration produces runtime evidence and a state graph, not understanding directly
- [ ] Session budgets are enforced and partial exploration is a valid result

---

# PHASE 5 — Static/Runtime Reconciliation

**Status:** `NOT_STARTED` · **Checkpoint:** CP-10
**Development depends on:** **P2 only** · **Validation depends on:** an observation dataset (any admissible source) + Validation D
**Optional enrichment:** P4 live exploration

### Goal
Static and runtime evidence can be compared and the resulting reconciliation is explainable
and traceable.

### Scope
Comparison by assertion identity · status assignment (`matched`, `runtime-only`,
`static-only`, `different`, `conflict`, `unverified`) · deterministic mismatch classification ·
confidence updates · understanding updates · mismatch candidates.

> **INV-5 — Mismatch does not automatically mean defect.**
> Four deterministic checks run before any semantic judgement: was the area within exploration
> coverage; are both observations from the same revision and environment; is the difference
> within a declared variation allowance; is there sufficient evidence on both sides.
> The six possible explanations — incomplete exploration, stale static context, environment
> difference, legitimate runtime variation, insufficient evidence, actual defect — must all
> be expressible.

### Acceptance criteria
- [ ] Reconciliation is deterministic set algebra over assertion identity, not fuzzy matching
- [ ] All six statuses are produced and persisted
- [ ] Confidence changes are traceable to a reconciliation event
- [ ] A defect candidate is produced only after all four deterministic checks pass
- [ ] Every mismatch carries evidence from both sides

---

# PHASE 6 — Coverage & Test Scenario Generation

**Status:** `NOT_STARTED` · **Checkpoint:** CP-03 · **Depends on:** P2

### Goal
Scenarios are generated from explicit application/coverage obligations, and coverage is
**computed rather than asserted**.

### Scope
Obligation model and derivation · obligation states (`covered`, `partially covered`,
`uncovered`, `insufficient evidence`, `not applicable`) · technique registry and selection
with recorded rationale · scenario generation workflow · deduplication by content identity ·
gap discovery · cross-feature and state-path analysis · completeness evaluation ·
coverage calculation · configurable generation limits.

### Required techniques (all eight, as registered strategies)
Equivalence Partitioning · Boundary Value Analysis · Decision Tables · Pairwise ·
Permutation & Combination · Graph-Based Path Coverage · Metamorphic Testing · Model-Based Testing

A technique is **selectable only when the understanding supports it**.

### Acceptance criteria
- [ ] Obligations derive from UAU elements, with the derivation traceable
- [ ] Coverage is computed from explicit satisfaction links — **no code path allows a model to set a coverage value** (verified by test)
- [ ] Every coverage report states denominator, understanding version and unverified share
- [ ] All eight techniques are registered; selection rationale is recorded per scenario
- [ ] Generation limits are enforced
- [ ] Adding a ninth technique requires no orchestration change (verified by test)

---

# PHASE 7 — Test Case & Test Data

**Status:** `NOT_STARTED` · **Checkpoint:** CP-04 · **Depends on:** P6

### Goal
Real test cases with explainable data requirements and an executable data-preparation strategy.

### Scope
Test case generation with semantic element references · mandatory/optional fields · forms ·
radio buttons · selections · conditional fields · validations · dependencies · negative data ·
boundary data · relational data · data requirement declaration · synthetic generation ·
approved data retrieval · **state provisioning** · data lifecycle and cleanup.

> **Generating a value is not creating application state.** Rungs 1–3 generate; rung 4
> provisions and is a state-changing action — risk-classified, policy-gated,
> environment-restricted, recorded for cleanup.

### Acceptance criteria
- [ ] Test cases contain semantic element references, **never** concrete locators (verified by test)
- [ ] Data requirements are declared, not resolved, at generation time
- [ ] All five resolution rungs are implemented, each recording why the previous failed
- [ ] State provisioning is a separate, policy-gated capability
- [ ] A test case is executable against two environments without regeneration
- [ ] Field semantics come from the UAU, not from generic patterns

---

# PHASE 8 — Playwright Automation

**Status:** `NOT_STARTED` · **Checkpoint:** CP-05 · **Depends on:** P7, P2

### Goal
Generated automation is maintainable, executable and structurally reusable — not a
collection of copied scripts.

### Scope
Automation specification · automation binding · page/component abstraction projected from
the UAU · locator discovery · locator validation · locator lifecycle · fixtures ·
environment binding · data binding · assertion generation · safe healing.

### The healing invariant — must be tested explicitly

> **INV-6 — Healing may change binding/locator resolution but never silently changes test
> intent or assertion semantics.**

Enforcement is **structural**: the healing component has no write path to specification
storage. This is verified by test, not by policy configuration.

### Acceptance criteria
- [ ] Specification and binding are separate persisted artifacts
- [ ] The same specification executes against two environments via different bindings
- [ ] Page/component objects are projected from the UAU and shared across test cases (reuse measured)
- [ ] Locator lifecycle states are persisted with provenance
- [ ] **A test attempting to modify an assertion through the healing path fails** (structural, not policy)
- [ ] Healing default policy is propose-only; auto-apply is a per-workspace setting shipped disabled
- [ ] Every healing event is recorded with before, after, evidence and score

---

# PHASE 9 — Real Execution

**Status:** `NOT_STARTED` · **Checkpoint:** CP-06 · **Depends on:** P8, P0

### Goal
Real Playwright tests execute against a controlled environment with complete results and
artifacts persisted.

### Scope
Execution manager · policy gate · environment lease · job queue · concurrency controller
(global / per-environment / per-run) · worker management · browser lifecycle · secret
resolution in-worker · test data setup · artifact capture (screenshots, traces, video,
network, console) · result storage · execution history · retry classification · cancellation.

### Acceptance criteria
- [ ] Real Playwright runs execute in isolated workers against a configured environment
- [ ] Workers hold no database credentials (verified by test)
- [ ] Secrets resolve in-worker from references; a scan of all artifacts and logs finds none
- [ ] Concurrency honours the minimum of three ceilings
- [ ] Retries are classified before attempted; each retry is a distinct attempt
- [ ] Complete artifacts are captured and stored by reference
- [ ] Execution history supports run-over-run comparison on stable identity

---

# PHASE 10 — Evaluation, Triage & Findings

**Status:** `NOT_STARTED` · **Checkpoint:** CP-07 · **Depends on:** P9

### Goal
The platform can explain execution outcomes and distinguish observed evidence from
evaluator interpretation.

### Scope
Deterministic / derived / semantic evaluators · evaluator registry and versioning ·
failure triage ladder · classification (infrastructure, environment, data/state, automation,
insufficient evidence, product defect candidate) · evaluator confidence contribution ·
findings · evidence assembly · run comparison · history.

### The triage ladder — deterministic before semantic

| Order | Check | Classification |
|---|---|---|
| 1 | Worker/browser fault, timeout, resource exhaustion | Infrastructure |
| 2 | Environment unreachable, wrong version, dependency down | Environment |
| 3 | Precondition unmet: identity, data or state missing | Data / state |
| 4 | Locator failed to resolve or resolved ambiguously | Automation |
| 5 | Assertion failed, oracle status unknown | Insufficient evidence |
| 6 | Assertion failed against a confirmed or coded-rule oracle | Product defect candidate |
| 7 | Reconciliation conflict corroborated by execution | Product defect candidate — high value |

### Acceptance criteria
- [ ] All three evaluator layers exist; deterministic runs first and can fail outright
- [ ] A semantic verdict cannot overturn a deterministic one (verified by test)
- [ ] **A failed assertion alone never produces a product defect finding** (verified by test)
- [ ] Items never executed carry an assessment, excluded from pass-rate arithmetic
- [ ] Every finding carries evidence and lineage to test case, scenario and UAU version
- [ ] Run comparison works on content-derived identity across regenerations

---

# PHASE 11 — Governance: Full Capability

> **Foundations were built in P0-W10** (gate contract, decision states, decider abstraction,
> policy hook, audit). This phase completes the capability; it does not introduce governance.

**Status:** `NOT_STARTED` · **Checkpoint:** CP-08 · **Depends on:** P0 governance foundations · **Parallel with:** P6–P10

### Goal
Human and autonomous workflows use the same governance model, and autonomous mode cannot
bypass mandatory controls.

### Scope
Gate instances and packages · approval states · human decisions (approve/edit/reject) ·
policy evaluation and versioning · autonomous decisions (continue/stop/escalate) ·
escalation work items · rejection with reason · edit with machine-original retention ·
retry · immutable approved versions · append-only audit trail.

### Acceptance criteria
- [ ] Exactly one gate implementation exists (verified by inspection and test)
- [ ] Human and autonomous decisions on the same artifact produce identical gate packages
- [ ] Autonomous mode cannot approve an artifact with an unresolved deterministic failure (verified by test)
- [ ] A human edit creates a new version, retains the machine original and re-derives confidence
- [ ] Rejection records a reason and is auditable
- [ ] Escalation serves all three cases: stalled gate, exploration blocker, unknown oracle
- [ ] Every decision is attributable to an actor and a policy version

---

# PHASE 12 — End-to-End Integration

**Status:** `NOT_STARTED` · **Checkpoint:** CP-11 · **Depends on:** P1–P11

### Goal
A realistic end-to-end workflow completes against a supported AUT with durable evidence at
every major stage.

### Scope
Wire the full chain: Source → Understanding → Exploration → Reconciliation → Coverage →
Scenarios → Test Cases/Data → Automation → Execution → Evaluation → Findings → Knowledge Update.

**Two end-to-end paths must both pass:**
1. **Static + exploration** — the default path
2. **Static-only** — exploration disabled; the full pipeline still produces scenarios, cases and automation at reduced confidence (INV-4)

### Acceptance criteria
- [ ] Both paths complete end to end on a real AUT
- [ ] Every artifact traces to a UAU version and to source evidence
- [ ] The knowledge-update loop closes: findings and execution evidence re-enter the understanding
- [ ] Gates operate at every stage transition in both human and autonomous modes

---

# PHASE 13 — Phase 1 Hardening

**Status:** `NOT_STARTED` · **Checkpoint:** CP-12 · **Depends on:** P12

### Scope
Reliability · concurrency · failure recovery · security review · artifact integrity ·
provenance completeness · performance · usability · observability · regression suite ·
history · **Excel export** · configuration · deployment · documentation.

### Acceptance criteria
- [ ] Excel export produces real `.xlsx` for scenarios, test cases and test data via an export adapter with no domain coupling
- [ ] Run resumes correctly after induced worker termination at every stage boundary
- [ ] Security review passes: no secrets in artifacts/logs/prompts; egress confined to the invoker; workspace scoping unbypassable
- [ ] Regression suite covers all prior checkpoints
- [ ] Deployment is documented and reproducible
- [ ] All known limitations are documented and visible in the product

### Final checkpoint
> Phase 1 capabilities are functional within their explicitly documented boundaries, and
> all known gaps are visible.

---

## Rolling-detail rule

> **The active phase must always be decomposed to implementation-ready task level before
> coding begins for that phase. Future phases stay at capability level.**

| Phase state | Required decomposition |
|---|---|
| **Active** (implementation about to begin or under way) | Work Item → Components → Contract → Data → APIs → Implementation Tasks → Tests → Evidence → Checkpoint |
| **Next** (predecessor checkpoint not yet validated) | Phase → Workstream → Capability → Acceptance Criteria → Dependencies |
| **Later** | Phase → Goal → Scope → Acceptance Criteria → Dependencies |

**Before entering a phase**, expand it to the active level. This keeps the roadmap detailed
where detail is actionable without prematurely freezing choices that evidence has not yet
informed.

**Current decomposition state:**

| Phase | Level | Note |
|---|---|---|
| P0 | **Active — implementation-ready** | 31 work items incl. governance foundations; P0-W1-T1 COMPLETE |
| P1 | Next — capability level | Expand before entering |
| P2…P13 | Later — goal, scope and acceptance criteria | Expand on approach |

Expanding a phase is not a roadmap change and needs no Decision Log entry — unless the
expansion reveals scope that was not previously represented, which is a CHANGE PROPOSAL.

---

## Vertical product slices

The roadmap is organised by component and dependency. That is correct for engineering and
has a known failure mode: every component progressing while nothing a QA user recognises
exists. Five vertical slices validate user value alongside phase completion.

| Slice | Delivers | Completed at | Phases involved |
|---|---|---|---|
| **A** | QA can inspect the understanding generated from a real repository | CP-02 | P0, P1, P2 |
| **B** | QA can generate and inspect evidence-backed scenarios and test cases | CP-04 | P6, P7 |
| **C** | QA can run a generated test against a real environment | CP-06 | P8, P9 |
| **D** | QA can understand what happened and why | CP-07 | P10 |
| **E** | The full Phase 1 journey, on both static-only and exploration paths | CP-11 | P12 |

Full definitions: [`16_VERTICAL_SLICES.md`](16_VERTICAL_SLICES.md).
**Slices validate; they do not reorder.** This roadmap remains canonical for sequencing.

---

## Roadmap change rule

Adding, removing or re-scoping a phase or work item requires an entry in
[`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md) **before** implementation.
Marking a status requires the objective condition in the status model to be met —
particularly `VALIDATED`, which requires captured checkpoint evidence.
