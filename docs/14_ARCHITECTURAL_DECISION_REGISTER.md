# 14 — Architectural Decision Register

**Status:** ACTIVE — canonical for the *classification* of every decision
**Created:** 2026-09-05 (Roadmap V2 correction)
**Controls:** what is fixed, what is free, what is provisional, what is deliberately unresolved

> **Read this before implementing anything.** It exists to make it impossible to mistake
> a recommendation for a requirement.
>
> **Relationship to other documents:** [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md) records *what changed and
> why, over time* (the history). This register records *what the current rule is and how
> binding it is* (the classification). A decision appears in both: the log narrates it,
> the register classifies it. Where they disagree, the log is the record of intent and
> this register must be corrected to match.

---

## 1. Classification definitions

| Type | Meaning | May an implementer change it? |
|---|---|---|
| **FIXED** | An established architecture or product decision with traceable provenance | **No.** Changing it requires a Decision Log entry and architecture update *before* code |
| **FLEXIBLE** | Implementation may choose among valid approaches without altering architecture or product intent | **Yes**, freely. Note the choice in the commit; no decision entry needed |
| **PROVISIONAL** | An engineering hypothesis or working target adopted to make progress. Not a product obligation | **Yes**, on evidence. Update the register entry with the new value and its basis |
| **PLACEHOLDER** | A concrete technology or provider deliberately not selected. The *contract* is FIXED; the *binding* is open | **No** — research first, then a recorded decision |
| **OPEN** | Requires external input, research or validation. Not yet decided by anyone | **No.** Record the blocker and surface the question |

### The rule that motivated this register

> **A decision is not FIXED merely because it appears in the roadmap.**
> Provenance must be verifiable. Where a value or rule originated as an engineering
> recommendation and was never explicitly approved, it is `PROVISIONAL`, not `FIXED`.

Three corrections were made under this rule during the V2 pass — see §6.

---

## 2. Decision register

### FIXED — architecture and product

| ID | Decision / Topic | Current rule | Source | Implementation freedom | Review trigger |
|---|---|---|---|---|---|
| ADR-001 | UAU is the central product asset | Downstream generation consumes a versioned Unified Application Understanding | Product direction; doc 02 §2; INV-1 | Internal representation is free (see ADR-020) | Product direction change only |
| ADR-002 | Static and runtime are independent evidence sources | Neither is a precondition for the platform to function; static-only is fully supported | Product direction; INV-4 | How each contributes is free | Product direction change only |
| ADR-003 | No artifact without a named UAU version | Required, non-nullable field on every artifact type | Traceability requirement; INV-2 | Field naming and storage free | Never — foundational |
| ADR-004 | Evidence is first-class and append-only | No understanding element without ≥1 resolvable evidence reference; evidence is never overwritten | Product direction; INV-8 | Anchor format free (see ADR-021) | Never — foundational |
| ADR-005 | Mismatch is not defect | Reconciliation emits classified candidates, never findings directly | Product direction; INV-5 | Classification algorithm free | Never — foundational |
| ADR-006 | Healing cannot change intent | Intent lives in the Specification; the healing path has no write access to it | Product direction; INV-6; doc 04 §7 | Healing algorithm free | Never — foundational |
| ADR-007 | Coverage is obligation-based | Computed from explicit satisfaction links; no model output path may set a coverage value | Product direction; INV-7 | Obligation derivation free | Never — foundational |
| ADR-008 | One governance mechanism | Exactly one gate implementation; autonomy is a decider, not a code path | Product direction; INV-9 | Policy language free | Never — foundational |
| ADR-009 | Workers are isolated | Browser, analysis and long-running workloads run outside the control plane; workers hold no database credentials | Security requirement; INV-10 | Worker technology free (ADR-023) | Never — foundational |
| ADR-010 | Providers are replaceable behind contracts | Providers supply observations; only platform components build understanding. No provider type in domain code | Product direction; INV-3 | Adapter design free | Never — foundational |
| ADR-011 | `CrawlerProvider` **contract** | The platform owns exploration planning, state identity, safety, governance, evidence, interpretation and reconciliation. The provider is a browser actuator and sensor | Doc 04 §4 | Contract may be extended additively | A candidate provider demanding ownership of the state model |
| ADR-012 | `CodeIntelligenceProvider` **contract** | Advisory contributor, never authoritative. Platform functions fully without it | Doc 04 §3 | Contract may be extended additively | Evidence that advisory positioning is untenable |
| ADR-013 | `LanguageModelProvider` **abstraction** | Model access is separated into task definition, prompt assembly, routing, invocation and adapter. No vendor API in domain code | Product direction: provider-agnostic from day 1 | Adapter design free; **count is not fixed** (see ADR-030) | Never — foundational |
| ADR-014 | Single capability-invocation path | All external calls pass one invoker: auth, timeout, retry, circuit breaker, rate limit, budget, redaction, provenance | Doc 02 §9 #21 | Invoker internals free | Never — foundational |
| ADR-015 | Repository and page content are untrusted | Content reaches models as structured, provenance-tagged data. No content-derived text may authorise a tool invocation | Security requirement | Framing mechanism free | Never — foundational |
| ADR-016 | Specification / Binding separation | Test intent and environment-specific resolution are separate versioned artifacts | Doc 04 §7 | Both schemas free | Never — makes ADR-006 structural |
| ADR-017 | Relationship extraction under-reports | Unresolvable segments marked `unresolved`; reachability alone never produces a call relation | Derived from measured failure causes, doc 02 §6 | Extractor design free | Re-measurement showing the rules are counterproductive |
| ADR-018 | Modular monolith control plane + worker fleets | Split where resource profile, isolation need, scalability or blast radius genuinely differ — not per domain name | Doc 02 §7 | Module layout free | Measured need for service extraction |
| ADR-019 | Test doubles are permitted; production placeholders are not | See §4 | V2 correction | — | Never |

### FLEXIBLE — implementation choices

| ID | Decision / Topic | Current rule | Source | Implementation freedom | Review trigger |
|---|---|---|---|---|---|
| ADR-020 | UAU internal representation | Versioned, evidence-backed assertions with content-derived identity | Doc 03 §4, recommended | **Free** — any representation satisfying doc 03 §10 requirements | A representation that better supports reconciliation |
| ADR-021 | Evidence anchor format | Commit + path + symbol + span + content hash for static; session + step + artifact for runtime | Doc 03 §4.2 | Free, provided anchors resolve and survive refactor | — |
| ADR-022 | Graph implementation technology | Graph is a **projection**, not the model. In-process at current scale | Doc 02 §10 | **Free** — in-process, embedded or external graph engine, or none | A measured query need the projection cannot serve |
| ADR-023 | Worker runtime technology | Isolated processes or containers | Doc 02 §7 | Free | — |
| ADR-024 | Queue technology | Behind a dispatch port | Doc 02 §10 | Free | Measured throughput need |
| ADR-025 | Canonical store product | Must provide transactional guarantees and versioned queries | Doc 02 §10 | Free among products meeting the guarantees | — |
| ADR-026 | Retrieval and indexing technology | Structured queries suffice for known Phase 1 needs | Doc 02 §10 | Free; adding a lexical index is a free choice | A demonstrated retrieval failure |
| ADR-027 | Self-healing default policy | Propose, do not auto-apply | Doc 02 §9 #18 | Per-workspace policy setting | Measured healing precision (see THR-HEAL-001) |
| ADR-028 | Workflow state location | In the product database, not a separate engine | Doc 02 §9 #20 | Free | Cross-service saga complexity appearing |
| ADR-029 | Export format adapters | Behind an export contract; `.xlsx` for Phase 1 | Doc 01 §12 | Free | — |

### PROVISIONAL — engineering targets, not product obligations

| ID | Decision / Topic | Current rule | Source | Review trigger |
|---|---|---|---|---|
| ADR-030 | Number of concrete LLM adapters | **At least one real adapter** before LLM-dependent functionality can be validated. Additional adapters are independently extensible | **V2 correction** — previously stated as "two required from the start" without approved provenance | Product Owner decision, or a second provider becoming necessary |
| ADR-031 | All numeric quality thresholds | See the threshold register in §3. **Every one is PROVISIONAL** | Engineering hypotheses in the validation plan | First measurement against a representative corpus |
| ADR-032 | Framework pack granularity | One pack per framework family | Doc 02 §6 | Experience with a second stack |
| ADR-033 | Rename detection scope | Heuristic, routes and endpoints only in Phase 1 | Doc 03 §4.3 | Diff quality complaints |
| ADR-034 | Control-plane language and runtime | Recorded OPEN — see ADR-041 | DEC-002 | — |

### PLACEHOLDER — production provider selection deliberately unresolved

| ID | Decision / Topic | Current rule | Source | What is fixed vs open | Review trigger |
|---|---|---|---|---|---|
| ADR-040 | Crawler provider selection | **UNDECIDED** | [`research/crawler/`](research/crawler/) | Contract FIXED (ADR-011); platform responsibilities FIXED; **concrete provider OPEN** | OD-1 resolved + Validation C executed |
| ADR-041 | Code Intelligence provider selection | **UNDECIDED** | [`research/code-intelligence/`](research/code-intelligence/) | Contract FIXED (ADR-012); integration boundary FIXED; **concrete provider OPEN** | OD-5 resolved + Validation B executed |

### OPEN — requires external input

| ID | Topic | Blocks | Who decides | Tracked as |
|---|---|---|---|---|
| ADR-050 | Exploration write policy and target environment | CP-09, CP-10 live validation; test-data provisioning | Product Owner / environment owner | OD-1 |
| ADR-051 | Source and context egress to external LLM providers | Provider strategy; hosting; quality ceiling | Client security | OD-2 |
| ADR-052 | Phase 1 AUT and technology stack | Framework packs; reference corpus; Validations A and C | Product Owner | OD-3 |
| ADR-053 | Delivery window and team size | Phase sequencing; ADR-034 | Delivery management | OD-4 |
| ADR-054 | Code Intelligence access (docs, schema, sandbox) | Validation B | Provider owner | OD-5 |
| ADR-055 | Repo-resident specs/tests as admissible evidence | Rule-extraction depth | Product Owner | OD-6 |
| ADR-056 | Tenancy model and hosting target | Infrastructure planning | Product Owner / infrastructure | OD-7 |
| ADR-057 | AUT stable test attributes | Locator strategy quality | AUT engineering team | OD-8 |
| ADR-058 | Vendoring the evaluation harness | Calibration capability | Engineering lead | OD-9 |
| ADR-059 | Control-plane language and runtime | P0-W1-T2 module layout | Engineering lead + Solution Architect | DEC-002 |

---

## 3. Threshold register

> **Every threshold below is `PROVISIONAL`.** None has recorded Product Owner approval.
> They exist so that a validation experiment has a decision rule, not because the product
> promises them. **Do not convert one into a traceable product obligation without an
> explicit approval recorded in [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md).**

| Threshold ID | Value | Unit | Purpose | Type | Source / provenance | Why selected | Evaluation dataset | Review condition | Owner | Status |
|---|---|---|---|---|---|---|---|---|---|---|
| THR-EXTRACT-001 | ≥ 0.85 | precision | Validation A pass rule for relationship extraction | PROVISIONAL | Engineering hypothesis, architecture validation plan | Measured baseline was 0.414; a large improvement is needed for relationships to gate downstream work | Reference corpus (OPEN — OD-3) | First measurement on the real AUT | Engineering | REQUIRES_VALIDATION |
| THR-EXTRACT-002 | ≥ 0.60 | recall | Validation A guard against precision gained by silence | PROVISIONAL | Engineering hypothesis | Measured baseline 0.632; must not regress materially | Reference corpus | With THR-EXTRACT-001 | Engineering | REQUIRES_VALIDATION |
| THR-EXTRACT-003 | < 0.70 | precision | Consequence trigger: below this, static relationships gate nothing | PROVISIONAL | Engineering hypothesis | A judgement about when static output stops being trustworthy | Reference corpus | With THR-EXTRACT-001 | Engineering | REQUIRES_VALIDATION |
| THR-CI-001 | ≥ 0.10 | absolute improvement | Validation B adoption rule for Code Intelligence | PROVISIONAL | Engineering hypothesis | Below this the integration cost is not repaid | Same corpus, three-way comparison | Provider access obtained (OD-5) | Engineering | REQUIRES_VALIDATION |
| THR-STATE-001 | < 10 | % variance | Validation C: state-count stability across identical sessions | PROVISIONAL | Engineering hypothesis | Instability above this makes reconciliation unreliable | Live AUT environment (OPEN — OD-1, OD-3) | First exploration sessions | Engineering | REQUIRES_VALIDATION |
| THR-STATE-002 | ≥ 60 | % of known screens | Validation C: agenda coverage | PROVISIONAL | Engineering hypothesis | A working figure for "exploration is useful" | Live AUT environment | First exploration sessions | Engineering | REQUIRES_VALIDATION |
| **THR-SAFETY-001** | **1.00** | recall | Validation C: destructive-action classification | **PROVISIONAL — recommended FIXED** | Engineering judgement | A missed destructive action can damage a real environment. **This is the one threshold that should arguably be a product safety requirement** | Hand-labelled control set | **Recommend Product Owner confirm as FIXED** | Product Owner | REQUIRES_DECISION |
| THR-RECON-001 | ≥ 80 | % seeded detection | Validation D | PROVISIONAL | Engineering hypothesis | Working figure | Seeded-discrepancy branch | First reconciliation run | Engineering | REQUIRES_VALIDATION |
| THR-RECON-002 | < 20 | % false defect candidates | Validation D | PROVISIONAL | Engineering hypothesis | Above this, output is noise rather than signal | Seeded-discrepancy branch | First reconciliation run | Engineering | REQUIRES_VALIDATION |
| THR-RECON-003 | < 1 | hour to review residual | Validation D usability guard | PROVISIONAL | Engineering hypothesis | Proxy for reviewability | Seeded-discrepancy branch | First reconciliation run | Engineering | REQUIRES_VALIDATION |
| THR-LOC-001 | ≥ 70 | % candidate validation | Validation E | PROVISIONAL | Engineering hypothesis | Working figure | Live AUT environment | First locator validation | Engineering | REQUIRES_VALIDATION |
| THR-HEAL-001 | ≥ 0.90 | precision | Validation E: gate before healing auto-apply is *considered* | PROVISIONAL | Engineering hypothesis | Healing that is often wrong is worse than none | Markup-churn branch | Before enabling auto-apply | Engineering | REQUIRES_VALIDATION |
| THR-COV-001 | ≥ 70 | % recall vs human inventory | Validation F | PROVISIONAL | Engineering hypothesis | Working figure | Independently authored inventory | First coverage comparison | Engineering | REQUIRES_VALIDATION |
| THR-COV-002 | < 20 | % judged not worth testing | Validation F precision guard | PROVISIONAL | Engineering hypothesis | Working figure | Independently authored inventory | First coverage comparison | Engineering | REQUIRES_VALIDATION |
| THR-UX-001 | ≤ 2 | interactions to reach evidence | Explainability quality attribute | PROVISIONAL | Engineering hypothesis | Proxy for "evidence is genuinely reachable" | Review surfaces | First UI review | Product Owner | REQUIRES_VALIDATION |

### Measured baselines — facts, not targets

These are **measurements**, a different category from thresholds. They are evidence, not
obligations. Source: evaluation harness described in [`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) §6.

| Measurement | Value | Status |
|---|---|---|
| Entity inventory precision / recall | 1.000 / 0.982 | MEASURED — on one application, not the Phase 1 AUT |
| Schema semantics precision / recall | 1.000 / 1.000 | MEASURED — same caveat |
| Business rule extraction precision / recall | 1.000 / 0.500 | MEASURED — same caveat |
| Workflow discovery precision / recall | 0.750 / 1.000 | MEASURED — same caveat |
| Route→endpoint mapping precision / recall | 0.414 / 0.632 | MEASURED — same caveat |

> **These figures do not transfer to a different technology stack.** They are the design
> baseline until re-measured on the confirmed Phase 1 AUT (OD-3).

---

## 4. Production placeholders versus test doubles

A distinction the V2 pass made explicit, because conflating them is how fake capability
gets presented as real.

### Production architectural placeholders — exactly two

```text
CODE_INTELLIGENCE_PROVIDER   (ADR-041)
CRAWLER_PROVIDER             (ADR-040)
```

These are **provider-selection** placeholders. Their contracts are real, FIXED architecture.
No third production placeholder exists. Creating one requires a Decision Log entry.

### Test doubles — permitted, and necessary

| Kind | Permitted where | Never |
|---|---|---|
| Mocks, stubs, fakes | Unit and integration tests | In a code path reachable in production |
| Contract-test providers | Conformance suites | Presented as a working provider |
| Recorded fixtures | Replay-based testing of agentic workflows | Presented as live behaviour |
| Synthetic observations | Reconciliation development and testing | Presented as real runtime evidence |
| Controlled observation datasets | Reconciliation calibration | Counted as exploration coverage |

**The rule:** a test double is legitimate engineering. Representing one as production
capability — in a checkpoint result, a status claim, or the product interface — is not.
A checkpoint that requires functional behaviour may not pass on a double
([`06_IMPLEMENTATION_PHASES.md`](06_IMPLEMENTATION_PHASES.md) §4).

---

## 5. How to use this register

| Situation | Action |
|---|---|
| About to implement something | Check whether the topic is FIXED. If so, follow it |
| Tempted to choose a technology | Check for a FLEXIBLE entry. If present, choose freely and note it |
| About to rely on a number | Check the threshold register. If PROVISIONAL, do not treat it as an acceptance requirement |
| A provider is needed | Check for PLACEHOLDER. Research first; never bind silently |
| Something is not listed | It is probably a FLEXIBLE implementation choice — but if it affects architecture, product behaviour or a contract, treat it as a CHANGE PROPOSAL and record it |
| A FIXED entry seems wrong | Do not work around it. Raise a change proposal per [`15_CHANGE_IMPACT_TEMPLATE.md`](15_CHANGE_IMPACT_TEMPLATE.md) |

---

## 6. Corrections made in the V2 pass

Three cases where an engineering recommendation had hardened into an apparent requirement.
All are recorded as decisions in [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md) (DEC-005 … DEC-007).

| # | Was stated as | Actual provenance | Now classified |
|---|---|---|---|
| 1 | "Two LLM adapters must exist from the start" — in docs 02, 04 (twice), 08, 09 and `requirements/` (twice) — **seven places** | An engineering argument in the v1 architecture text: *"a contract with a single implementation is an assumption"*. Never an approved product requirement | **ADR-030 PROVISIONAL** — abstraction FIXED, adapter count flexible, at least one real adapter needed before LLM-dependent validation |
| 2 | Fourteen numeric thresholds presented as validation pass criteria | Engineering hypotheses written into the validation plan | **All PROVISIONAL** (§3), with THR-SAFETY-001 flagged for Product Owner confirmation as FIXED |
| 3 | Reconciliation (P5) depending on the crawler phase (P4) | A sequencing convenience, not an architectural necessity — it contradicted ADR-002 / INV-4 | **Corrected** — reconciliation develops against recorded, synthetic and fixture observations; live crawler integration is a separate validation dependency |
