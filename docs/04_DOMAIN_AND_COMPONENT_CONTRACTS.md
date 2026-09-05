# 04 — Domain and Component Contracts

**Status:** ACTIVE — canonical for interfaces and replaceability
**Last updated:** 2026-09-05
**Controls:** what each component promises, what may change behind it, how it is replaced

> Contracts here are **technology-neutral by design**. They define obligations, not
> transport, serialisation or language. Concrete signatures are an implementation
> concern and belong in code, generated from these definitions during P0.
>
> **Change rule:** a breaking change to any contract below requires a Decision Log entry
> **before** implementation. See [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md).

---

## Contract conventions

Every contract in this document specifies the same fields:

| Field | Meaning |
|---|---|
| Responsibility | What the contract exists to achieve |
| Request / Response | Logical inputs and outputs |
| Errors | Failure modes the caller must handle |
| Lifecycle | Synchronous, asynchronous, long-running, or batch |
| Versioning | How the contract evolves |
| Idempotency | Repeat-call behaviour |
| Evidence | What provenance must be produced |
| Confidence | Whether and how confidence is involved |
| Security | Trust assumptions and boundaries |
| Extension points | Where behaviour may be added without breaking callers |
| Replacement rules | What a replacement implementation must honour |

**Universal rules applying to every contract:**

- **U1** — No provider-specific type may cross a contract boundary into domain code (INV-3)
- **U2** — Any operation producing understanding must produce evidence (INV-8)
- **U3** — Any artifact-producing operation must record the UAU version it consumed (INV-2)
- **U4** — Long-running operations are resumable and expose partial results as valid results
- **U5** — Secrets cross contracts as references, never as values
- **U6** — Every external invocation is recorded as provenance

---

## 1. `UnderstandingQuery`

| | |
|---|---|
| **Responsibility** | Serve the Unified Application Understanding to any consumer, at a named version |
| **Request** | Understanding version id (required); element type filter; relationship traversal spec; projection selector; pagination |
| **Response** | Elements with grounding (evidence refs, inference type, oracle status, reconciliation status, confidence); traversal paths; projection payloads |
| **Errors** | `VersionNotFound`, `VersionSuperseded` (advisory, still readable), `ProjectionUnavailable`, `TraversalDepthExceeded` |
| **Lifecycle** | Synchronous, read-only |
| **Versioning** | Additive element/relationship types are non-breaking. Removing or re-typing an element type is breaking |
| **Idempotency** | Fully idempotent — a version is immutable once referenced |
| **Evidence** | Every returned element carries resolvable evidence references |
| **Confidence** | Returned per element; never computed by the caller |
| **Security** | Workspace-scoped; returns no secret values; evidence references resolve through a separate authorised endpoint |
| **Extension points** | New projections; new element types; new traversal predicates |
| **Replacement rules** | Any implementation must preserve version immutability, evidence resolvability and the five distinct grounding fields |

---

## 2. `UnderstandingContribution`

| | |
|---|---|
| **Responsibility** | Allow any extractor or observer to contribute to the understanding without owning it |
| **Request** | Workspace; repository tuple or exploration session; observation batch; contributor identity and version |
| **Response** | Accepted count; rejected entries with reasons; resulting understanding version (if a build completes) |
| **Errors** | `UnanchoredObservation` (no resolvable evidence — rejected), `UnknownContributor`, `RevisionMismatch`, `SchemaViolation` |
| **Lifecycle** | Asynchronous, batch |
| **Versioning** | Contributor declares its own version; the platform records it as provenance |
| **Idempotency** | **Required.** Re-submitting the same batch produces no duplicate assertions — content-derived identity makes this natural |
| **Evidence** | Mandatory. A contribution without a resolvable anchor is rejected, not stored with a warning |
| **Confidence** | The contributor supplies inference type; the **platform** computes confidence from calibrated priors. A contributor may not set confidence |
| **Security** | Contributors run in workers; contributions cross the callback boundary; content is untrusted |
| **Extension points** | New contributor types (static extractor, runtime observer, future context provider such as requirements or specifications) |
| **Replacement rules** | Contributors are freely replaceable. The platform never trusts a contributor's self-assessment |

> This is the contract by which future evidence sources (requirements documents, backlog
> items, wireframes, existing test suites) are admitted **without schema change**.

---

## 3. `CodeIntelligenceProvider`

> **Same separation as the crawler, and for the same reason.**
>
> | Aspect | Classification | Meaning |
> |---|---|---|
> | The **contract** below | **FIXED** (ADR-012) | The integration boundary is decided |
> | Advisory positioning — never authoritative; platform fully functional without it | **FIXED** | Not renegotiable by a capable provider |
> | The **concrete provider** | **OPEN / PLACEHOLDER** (ADR-041) | Deliberately unselected |
> | Candidate evaluation | **RESEARCH** | [`research/code-intelligence/`](research/code-intelligence/) |
> | Final binding | **PENDING DECISION** | Gate: OD-5 + Validation B |
>
> Until a provider is bound, development proceeds on **native static analysis**,
> provider-neutral fixtures and contract test doubles — without any of those being
> represented as a production provider (register §4).

| | |
|---|---|
| **Responsibility** | Contribute code understanding as **one advisory extractor among several**, plus optional retrieval |
| **Operations** | `analyzeRepository` · `listSymbols`/`listEntities` · `getRelationships` · `semanticSearch` · `getContext` · `describeCapabilities` |
| **Request** | Repository at a pinned revision; target symbol/feature; query text |
| **Response** | Symbols, entities, typed relationships — each with **file/symbol/span anchors**, a resolution status, and provider version |
| **Errors** | `Unavailable`, `Timeout`, `PartialResult` (with a completeness qualifier), `RevisionNotIndexed`, `Unsupported` |
| **Lifecycle** | Asynchronous for analysis; synchronous for retrieval |
| **Versioning** | `describeCapabilities` declares supported languages, relationship types, anchor granularity, version. Registration **fails closed** if absent |
| **Idempotency** | Analysis is idempotent per revision |
| **Evidence** | Results must carry symbol- or span-level anchors, or they **cannot become understanding** |
| **Confidence** | Provider results receive platform-calibrated confidence like any extractor. Not authoritative |
| **Security** | Sending source to this provider is an egress event subject to workspace policy (OD-2) |
| **Extension points** | Additional operations may be added; the platform must degrade gracefully if unimplemented |
| **Replacement rules** | **The platform must be fully functional with this provider absent.** Removing the adapter reduces understanding breadth and changes no domain code. Must be addressable per repository revision — a service that re-indexes and re-points identifiers under a pinned version cannot participate in a reproducible model |

**Degraded-mode behaviour is part of the contract:**

| Operation unavailable | Platform behaviour |
|---|---|
| `analyzeRepository` | Native extractors run alone; understanding proceeds at reduced breadth |
| `getRelationships` | Native extractors only, with the measured accuracy penalty made visible |
| `semanticSearch` | Falls back to structured queries; free-text code search unavailable |

---

## 4. `CrawlerProvider`

> **Two things are being separated here, and conflating them is the failure this section prevents.**
>
> | Aspect | Classification | Meaning |
> |---|---|---|
> | The **contract** below | **FIXED** (ADR-011) | The architectural boundary is decided and may not be renegotiated by a provider |
> | Platform responsibilities: exploration planning, state identity, safety, governance, evidence, interpretation, reconciliation | **FIXED** | These never move to a provider |
> | The **concrete provider/browser implementation** | **OPEN / PLACEHOLDER** (ADR-040) | Deliberately unselected |
> | Candidate evaluation | **RESEARCH** | [`research/crawler/`](research/crawler/) |
> | Final binding | **PENDING DECISION** | Gate: OD-1 + Validation C |

The provider is a **browser actuator and sensor**. Everything constituting knowledge or
judgement stays on the platform side.

| Provider responsibility | Platform responsibility |
|---|---|
| Establish a session; apply an authentication strategy given secret references | Decide which environment, which identity, whether the session is permitted at all |
| Navigate; enumerate interactive affordances; execute a requested action | Decide **which** action, in what order, and whether it is allowed |
| Capture DOM, accessibility tree, screenshot, network exchanges, console output, validation messages, resulting URL | Compute state identity, maintain the state graph, derive understanding, assign confidence |
| Report action outcome including failure and unexpected navigation | Interpret outcome, detect blockers, classify differences, decide continuation |
| Probe a candidate locator; report existence, uniqueness, visibility, interactability | Own the locator lifecycle, validation history and healing decisions |

| | |
|---|---|
| **Request** | Session config (environment, identity refs, allowlists); action request; observation request; locator probe |
| **Response** | Observation bundle; action outcome; probe result |
| **Errors** | `SessionFailed`, `AuthenticationFailed`, `ActionFailed`, `NavigationUnexpected`, `Timeout`, `Blocked` |
| **Lifecycle** | Long-running session; step-wise request/response |
| **Versioning** | Provider declares supported capabilities; unsupported operations degrade, not crash |
| **Idempotency** | **Not idempotent** — actions change the world. The platform owns replay semantics, not the provider |
| **Evidence** | Every observation becomes `RuntimeEvidence` with session, step and artifact references |
| **Confidence** | Provider asserts nothing. The platform derives assertions from observations |
| **Security** | Highest boundary. Holds AUT credentials as references; bound by environment and domain allowlists; page content is untrusted |
| **Extension points** | Additional observation types; richer probe results |
| **Replacement rules** | A replacement must not require the platform to surrender planning, state identity, safety classification, reconciliation or interpretation. **If a candidate provider demands ownership of the state model, it fails the contract.** |

---

## 5. `ScenarioContract`

| | |
|---|---|
| **Responsibility** | Carry approved test scenarios from Quality Design to Test Case generation |
| **Request** | UAU version; obligation scope; generation limits; prior scenarios (for incremental generation) |
| **Response** | Scenario versions: description, technique(s) with recorded rationale, obligation links, evidence refs, oracle status, confidence, lineage |
| **Errors** | `UnderstandingInsufficient`, `LimitExceeded`, `NoApplicableTechnique` |
| **Lifecycle** | Asynchronous, long-running, resumable, fan-out per feature |
| **Versioning** | Scenario versions are immutable once approved |
| **Idempotency** | Re-running produces a new version; content-derived identity makes duplicates detectable |
| **Evidence** | Each scenario links to the understanding elements that justify it |
| **Confidence** | Computed; scenario confidence is bounded by the confidence of the elements it rests on |
| **Security** | Consumes structured understanding only, never raw source |
| **Extension points** | **Technique registry** — a new technique is a registry entry declaring which element types it consumes and which obligation types it satisfies |
| **Replacement rules** | A replacement generator must still emit obligation links and technique rationale; a scenario without obligation links is invalid |

> A technique is **selectable only when the understanding supports it**. Decision-table
> generation requires rule elements; boundary analysis requires constraint elements.
> A scenario may legitimately carry more than one technique.

---

## 6. `TestCaseContract` and `DataRequirementContract`

| | |
|---|---|
| **Responsibility** | Express executable test intent and declare — not resolve — its data needs |
| **Request** | Approved scenario version; UAU version; generation limits |
| **Response** | Test case: ordered steps, **semantic element references** (understanding element ids, not locators), expected results, oracle status, declared data requirements, lineage |
| **Errors** | `ScenarioNotApproved`, `ElementUnresolvable`, `OracleUnknown` (not an error — a recorded state) |
| **Lifecycle** | Asynchronous |
| **Versioning** | Immutable once approved |
| **Idempotency** | New version per run |
| **Evidence** | Steps reference understanding elements; expected results reference the rule or observation that justifies them |
| **Confidence** | Computed; bounded by scenario and element confidence |
| **Security** | Data requirements may reference secrets **by reference only** |
| **Extension points** | New requirement kinds (value, identity, entity, state, relationship, environment precondition) |
| **Replacement rules** | A test case containing a concrete locator, environment value or resolved secret **violates this contract** |

**`DataRequirement` kinds and resolution ladder** (resolution happens at bind time, not here):

| Kind | Example | Resolution rung |
|---|---|---|
| Value | A string satisfying a length constraint | 1 — derive from understanding |
| Identity | A user holding a role | 2 — approved source, or 5 — human |
| Entity | An application record in a given state | 4 — provisioning |
| Relationship | Two entities linked | 4 — provisioning |
| Environment precondition | Feature flag enabled | 5 — human |

> **Generating a value is not creating application state.** Rungs 1–3 generate; rung 4
> provisions and is a state-changing action — risk-classified, policy-gated,
> environment-restricted and recorded for cleanup.

---

## 7. `AutomationSpecificationContract` and `AutomationBindingContract`

The separation is architectural, not stylistic. It is what makes INV-6 structural.

| | Specification — intent | Binding — resolution |
|---|---|---|
| **Holds** | Actions, semantic element references, assertions, expected outcomes, declared data requirements, oracle status | Concrete locators with provenance, page/component object refs, fixtures, data bindings, environment ref, secret refs, timeouts |
| **Scope** | One per test case, environment-independent | One per specification per environment |
| **Changes when** | Test intent changes — requires regeneration and a gate | Markup, environment or data changes — a routine audited event |
| **Healing may modify** | **Never** | Locator resolution only, under policy |
| **Versioning** | Immutable once approved | Versioned with an event history, no approval gate |
| **Idempotency** | New version per generation | Re-binding is idempotent per (spec, environment) |
| **Evidence** | Links to test case, scenario and UAU version | Locator provenance: candidate source, validation result, healing events |
| **Security** | Contains no secrets or environment values | Secret **references** only; resolved in-worker |
| **Replacement rules** | Any code generator may be substituted provided it consumes the specification and never writes to it |

### The healing invariant, at three levels

| Level | What it holds | Mutability |
|---|---|---|
| **Specification** | Business intent, action-sequence semantics, assertion semantics, expected results, acceptance criteria | **Immutable** for a generated version. Changing it means regenerating and passing a gate — never an in-place edit by an automated process |
| **Binding** | Concrete locator, environment, data and fixture resolution | Mutable. Versioned with an event history rather than an approval gate |
| **Healing** | Repairs binding resolution when the application changes | **May modify the binding only** |

Healing **must not** silently modify: business intent · action-sequence semantics ·
assertion semantics · expected result · acceptance criteria.

**Where healing would require such a change, it must not continue.** It emits one of:

| Outcome | Meaning |
|---|---|
| `UNRESOLVED` | No binding-level repair exists. The test cannot run as specified |
| `HUMAN_REVIEW_REQUIRED` | A repair exists but would alter meaning. A person must decide |

Silent continuation past either state is a defect, not a degraded mode.

**Enforcement is structural, not procedural:** the healing component has **no write path**
to specification storage. Verified by test (ARCH-5), not by policy configuration — because
a policy can be misconfigured and a missing code path cannot. (INV-6, ADR-006, ADR-016)

**Page/component object model:** projected from the UAU, not invented per test case.
Two test cases touching the same form reference the same object automatically —
reuse is structural, not emergent.

---

## 8. `ExecutionRequestContract` and `ExecutionResultContract`

| | |
|---|---|
| **Responsibility** | Run generated automation safely and report what happened — without interpreting it |
| **Request** | Specification + binding refs; environment; concurrency; policy context; run scope |
| **Response** | Attempts with status, timings, artifact references (trace, video, screenshots, network, console), locator outcomes, resolution logs |
| **Errors** | `EnvironmentUnavailable`, `PolicyDenied`, `LeaseUnavailable`, `ConcurrencyExceeded`, `WorkerFailed`, `Cancelled` |
| **Lifecycle** | Asynchronous; queue-dispatched; one job per test case |
| **Versioning** | Attempts are append-only |
| **Idempotency** | A job is idempotent on its job key; retries create **distinct attempts**, never overwriting — so flakiness stays visible |
| **Evidence** | Every attempt produces `ExecutionEvidence` with artifact references |
| **Confidence** | Execution asserts no confidence; it reports observations |
| **Security** | Workers hold no database credentials; secrets resolved in-worker from references with a job-scoped token; output redaction filter on all logs (INV-10) |
| **Extension points** | Additional artifact types; additional environment classes |
| **Replacement rules** | Worker technology and scheduler are freely replaceable. **A result that includes a defect judgement violates this contract** — classification belongs to Triage |

---

## 9. `EvaluatorContract`

| | |
|---|---|
| **Responsibility** | Assess a generated artifact and produce an actionable verdict |
| **Request** | Artifact version; its context (UAU version, lineage, prior verdicts) |
| **Response** | Verdict: decision, severity, assertions performed, evidence consulted, confidence contribution, remediation output |
| **Errors** | `InputUnavailable`, `EvaluatorTimeout` (a timeout is a recorded non-verdict, not a pass) |
| **Lifecycle** | Deterministic evaluators inline and synchronous; semantic evaluators asynchronous |
| **Versioning** | Each evaluator is independently versioned; the version is recorded on every verdict |
| **Idempotency** | Deterministic evaluators are fully idempotent; semantic evaluators record the model and prompt version used |
| **Evidence** | A verdict must state what it examined |
| **Confidence** | Verdicts are an input to confidence, never the output |
| **Security** | Evaluators consuming generated content treat it as untrusted |
| **Extension points** | **Evaluator registry** — new evaluators are added without orchestration change |
| **Replacement rules** | Layer ordering is fixed: deterministic → derived → semantic. **A semantic verdict may never overturn a deterministic one.** A deterministic failure short-circuits — semantic evaluation is never spent on structurally invalid output |

---

## 10. `GateContract`

> **Exactly one implementation exists.** Human and autonomous modes are *deciders*, not
> separate code paths. (INV-9)

| | |
|---|---|
| **Responsibility** | Decide whether work advances, by human review or by policy |
| **Request** | Gate package: artifact version, evaluator verdicts, confidence distribution, oracle coverage, unresolved reconciliation conflicts, budget state |
| **Response** | Decision: approve / edit / reject (human) or continue / stop / escalate (policy); decision record with actor, rationale, policy version, timestamp |
| **Errors** | `PolicyUnevaluable` → mandatory escalation, never an implicit pass |
| **Lifecycle** | Synchronous decision; the gate instance may wait indefinitely for a human |
| **Versioning** | Policies are versioned; the version is recorded on every autonomous decision |
| **Idempotency** | A decided gate is immutable |
| **Evidence** | The gate package is retained as the evidence for the decision |
| **Confidence** | An input to the decision; a policy may require a minimum band |
| **Security** | Decisions are attributable to an authenticated actor; the audit log is append-only |
| **Extension points** | New policy predicates; new escalation routes |
| **Replacement rules** | Autonomous mode may **not** skip evaluation, lower a confidence threshold, bypass safety or environment policy, or approve an artifact carrying an unresolved deterministic failure |

**Escalation is one mechanism serving three cases:** a stalled autonomous gate, an
exploration blocker requiring a value, and a test case with an unknown oracle. All three
produce the same work-item shape — what is needed, why, what was attempted, why it failed.

---

## 11. `LanguageModelProvider`

Five concerns separated deliberately, because merging them is the common and damaging failure:

| Concern | Owner | Note |
|---|---|---|
| Task definition | Domain | Reasoning required, in domain terms, with a typed output contract |
| Prompt assembly | Platform | Versioned templates + context bundle from UAU. **Prompt version recorded on every artifact produced** |
| Model routing | Platform | Task→model policy with declared fallbacks |
| Invocation | Capability Invoker | Retries, timeouts, structured-output enforcement, token accounting, redaction, provenance |
| Provider adapter | Adapter | **The only component aware of a vendor API** |

| | |
|---|---|
| **Errors** | `Unavailable`, `RateLimited`, `BudgetExceeded`, `StructuredOutputViolation`, `ContentFiltered` |
| **Idempotency** | Not idempotent. Reproducibility is of *inputs and lineage*, not output |
| **Security** | Sending content is an egress event under workspace policy (OD-2); secrets never enter a prompt |
| **Replacement rules** | The **abstraction is FIXED** (ADR-013). The **adapter count is not** (ADR-030): at least one real adapter is required before LLM-dependent functionality can be validated; additional adapters are independently extensible. *Previously stated as "two adapters required from the start" — that was an engineering argument, never an approved requirement. See register §6.* |

---

## 12. `Capability` (tools, MCP servers, external agents)

| | |
|---|---|
| **Responsibility** | One uniform path for every external capability |
| **Request** | Capability id + version; typed input; invocation context (workspace, budget, scopes) |
| **Response** | Typed output; invocation record |
| **Errors** | `NotRegistered`, `Unauthorised`, `Timeout`, `CircuitOpen`, `RateLimited`, `BudgetExceeded` |
| **Lifecycle** | Synchronous with async fan-out |
| **Versioning** | Registry holds capability metadata, version, config, health, trust level, scopes |
| **Idempotency** | Declared per capability |
| **Evidence** | Every invocation is recorded and becomes `ProviderEvidence` |
| **Security** | **The sole egress point.** Declared scopes; a tool result is **data, never an instruction** — tool output cannot cause further tool invocation without a domain decision |
| **Extension points** | Registration of new capabilities without stage-level code |
| **Replacement rules** | No stage may bypass the invoker. Static check: zero provider SDK imports in domain modules |

---

## 13. `FindingContract`

| | |
|---|---|
| **Responsibility** | Represent an interpreted, classified, evidence-backed observation about the AUT |
| **Request** | Triaged execution attempt, or a corroborated reconciliation conflict |
| **Response** | Finding: classification, severity, description, evidence from all contributing sources, lineage to test case/scenario/UAU version, suggested remediation, confidence |
| **Classification** | `infrastructure` · `environment` · `data/state` · `automation` · `insufficient evidence` · `product defect candidate` |
| **Errors** | `TriageIncomplete` — a finding may not be created before triage completes |
| **Lifecycle** | Created after triage; versioned across runs |
| **Evidence** | Mandatory from every contributing source |
| **Confidence** | Computed; a finding with unknown oracle status may not be classified as a product defect |
| **Replacement rules** | **A failed assertion alone never produces a product defect finding.** The deterministic triage ladder runs first |

---

## 14. Contract conformance testing

Every contract above has a **conformance suite** — the mechanism by which replaceability
stops being a claim and becomes a property.

| Contract | Conformance suite must prove |
|---|---|
| `CodeIntelligenceProvider` | Platform builds understanding with the adapter removed |
| `CrawlerProvider` | A second implementation drives exploration with no change to state model or planning |
| `LanguageModelProvider` | A task runs against a real adapter with typed output honoured; provenance records which adapter was used. **When a second adapter exists**, both produce contract-conformant typed output for the same task |
| `Capability` | A new tool is registered and invoked with no stage-level code |
| `EvaluatorContract` | A new evaluator is added with no orchestration change |
| `AutomationSpecification`/`Binding` | The same specification executes against two environments via different bindings; the healing path cannot reach the specification |
| `UnderstandingContribution` | A new evidence source contributes and joins reconciliation with no schema change |

Adapter acceptance is gated on its conformance suite passing. These are the same criteria
listed as architecture acceptance criteria in [`07_CHECKPOINT_AND_VALIDATION_PLAN.md`](07_CHECKPOINT_AND_VALIDATION_PLAN.md).
