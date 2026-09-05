# 08 — Traceability Matrix

**Status:** ACTIVE — canonical for requirement → implementation → evidence linkage
**Last updated:** 2026-09-05
**Controls:** requirement identifiers and their coverage across the lifecycle

> **Purpose: make omissions visible.** A requirement with no roadmap item, or a roadmap
> item with no test, is a gap this table is designed to expose — not hide.

---

## Trace chain

```text
Requirement → Architecture Component → Contract → Roadmap Work Item
           → Implementation → Test → Checkpoint → Evidence
```

**Current reality (2026-09-05):** every requirement traces as far as *Contract* and
*Roadmap Work Item*. **Nothing traces to Implementation, Test or Evidence, because no
implementation exists.** The `Impl`, `Test` and `Evidence` columns are therefore empty by
fact, not by omission.

---

## Requirement identifier scheme

| Prefix | Area |
|---|---|
| `REQ-SRC-nnn` | Source ingestion and repository handling |
| `REQ-UAU-nnn` | Unified Application Understanding |
| `REQ-STA-nnn` | Static understanding |
| `REQ-EXP-nnn` | Runtime exploration |
| `REQ-REC-nnn` | Reconciliation |
| `REQ-COV-nnn` | Coverage |
| `REQ-TEST-nnn` | Test design, cases and data |
| `REQ-AUTO-nnn` | Automation |
| `REQ-EXEC-nnn` | Execution |
| `REQ-EVAL-nnn` | Evaluation, triage and findings |
| `REQ-GOV-nnn` | Governance |
| `REQ-PROV-nnn` | Provider integration |
| `REQ-SEC-nnn` | Security |
| `REQ-PLAT-nnn` | Platform foundations |

Identifiers are **stable**. Do not renumber. Retired requirements are marked `RETIRED`
with a Decision Log reference, never deleted.

---

## Matrix

Legend — Impl/Test/Evidence: `—` none yet · `✓` complete · `~` partial

### Source ingestion

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-SRC-001 | Register a Git repository with URL, ref, credential reference and role | Understanding / Source Ingestion | `UnderstandingContribution` | P1-W1-T1 | — | — | CP-00 | — |
| REQ-SRC-002 | Multiple repositories per workspace (frontend + backend) | Understanding | — | P1-W1-T2 | — | — | CP-00 | — |
| REQ-SRC-003 | Application version is a tuple across all repositories | Understanding | `UnderstandingQuery` | P1-W1-T2 | — | — | CP-00 | — |
| REQ-SRC-004 | Resolve and pin a revision; immutable once referenced | Understanding | — | P1-W2-T2 | — | — | CP-00 | — |
| REQ-SRC-005 | Acquire source into an isolated sandbox with no persistent credential | Understanding | — | P1-W2-T1 | — | — | CP-00 | — |
| REQ-SRC-006 | Content hashing per file for evidence anchoring | Understanding | — | P1-W2-T3 | — | — | CP-00 | — |

### Unified Application Understanding

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-UAU-001 | UAU is the canonical semantic model; projections are derived | Understanding / UAU | `UnderstandingQuery` | P2-W6-T1 | — | — | CP-01 | — |
| REQ-UAU-002 | No element without ≥1 resolvable evidence reference (INV-8) | Understanding | `UnderstandingContribution` | P2-W4-T1 | — | — | CP-01 | — |
| REQ-UAU-003 | Evidence is append-only and immutable | Platform / Evidence | — | P0-W2-T2 | — | — | CP-00 | — |
| REQ-UAU-004 | Element identity is content-derived and deterministic | Understanding | — | P2-W4-T1 | — | — | CP-01 | — |
| REQ-UAU-005 | Understanding version pins the full repository tuple | Understanding | — | P2-W5-T1 | — | — | CP-01 | — |
| REQ-UAU-006 | Every downstream artifact carries a non-nullable UAU version (INV-2) | Platform / Artifact | all artifact contracts | P0-W2-T1 | — | — | CP-00 | — |
| REQ-UAU-007 | Projections rebuildable from canonical model with no data loss | Understanding | — | P2-W6-T1 | — | — | CP-01 | — |
| REQ-UAU-008 | Confidence computed by platform; no model path can set it (INV-7) | Platform / Confidence | — | P2-W4-T3 | — | — | CP-01 | — |
| REQ-UAU-009 | Evidence, inference type, oracle status, reconciliation, confidence are five separate fields | Understanding | `UnderstandingQuery` | P0-W2-T2 | — | — | CP-01 | — |
| REQ-UAU-010 | Large evidence payloads stored by reference | Platform / Artifact | — | P0-W4-T1 | — | — | CP-00 | — |
| REQ-UAU-011 | Staleness computed and surfaced, never auto-resolved | Understanding | — | P2-W5-T2 | — | — | CP-01 | — |
| REQ-UAU-012 | Understanding diff across versions | Understanding | `UnderstandingQuery` | P2-W5-T3 | — | — | CP-01 | — |

### Static understanding

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-STA-001 | Discover screens, pages, components, fields | Static Understanding | `UnderstandingContribution` | P2-W3-T1 | — | — | CP-01 | — |
| REQ-STA-002 | Discover APIs, services, data structures | Static Understanding | — | P2-W3-T1 | — | — | CP-01 | — |
| REQ-STA-003 | Extract schemas, constraints, optionality, enums | Static Understanding | — | P2-W3-T2 | — | — | CP-01 | — |
| REQ-STA-004 | Extract validations, business rules, permissions | Static Understanding | — | P2-W3-T3 | — | — | CP-01 | — |
| REQ-STA-005 | Discover frontend↔backend relationships with per-hop evidence | Static Understanding | — | P2-W3-T4 | — | — | CP-01 | — |
| REQ-STA-006 | Unresolvable segments marked `unresolved`, never wildcard-matched (R1) | Static Understanding | — | P2-W3-T4 | — | — | CP-01 | — |
| REQ-STA-007 | Route matching via framework pack modelling real resolution (R2) | Static Understanding | — | P2-W2-T2 | — | — | CP-01 | — |
| REQ-STA-008 | Reachability alone never produces a call relation (R3) | Static Understanding | — | P2-W3-T4 | — | — | CP-01 | — |
| REQ-STA-009 | Statically unresolvable relations marked `unverified` (R4) | Static Understanding | — | P2-W3-T4 | — | — | CP-01 | — |
| REQ-STA-010 | Framework packs are the extensibility unit, independently evaluable | Static Understanding | — | P2-W2-T1 | — | — | CP-01 | — |
| REQ-STA-011 | Incremental rescan scoped by change | Static Understanding | — | P2-W5-T2 | — | — | CP-01 | — |
| REQ-STA-012 | Calibration harness with ground-truth corpus, per-extractor metrics never averaged | Platform / Confidence | — | P2-W7-T1 | — | — | CP-01 | — |
| REQ-STA-013 | Locator candidates extracted with provenance and strategy ranking | Static Understanding | — | P2-W3-T5 | — | — | CP-01 | — |

### Runtime exploration

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-EXP-001 | Exploration is optional; static-only mode fully supported (INV-4) | Understanding | — | P4 / P12 | — | — | CP-02, CP-08 | — |
| REQ-EXP-002 | Platform owns planning, state identity, safety, interpretation | Runtime Exploration | `CrawlerProvider` | P4-W1 | — | — | CP-02 | — |
| REQ-EXP-003 | State identity is composite, not URL alone | Runtime Exploration | — | P4-W2 | — | — | CP-02 | — |
| REQ-EXP-004 | Action risk classified before acting; unknown denied by default | Runtime Exploration | — | P4-W3 | — | — | CP-02 | — |
| REQ-EXP-005 | Blockers are durable, attributable, resumable governed work items | Runtime Exploration / Governance | `GateContract` | P4-W4 | — | — | CP-02 | — |
| REQ-EXP-006 | Session budgets enforced; partial exploration is a valid result | Runtime Exploration | — | P4-W5 | — | — | CP-02 | — |
| REQ-EXP-007 | Observations become runtime evidence; provider never writes understanding | Runtime Exploration | `CrawlerProvider` | P4-W1 | — | — | CP-02 | — |
| REQ-EXP-008 | Locator probing: exists, unique, visible, interactable | Runtime Exploration | `CrawlerProvider` | P4-W6 | — | — | CP-02 | — |

### Reconciliation

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-REC-001 | Six statuses: matched, runtime-only, static-only, different, conflict, unverified | Reconciliation | — | P5-W1 | — | — | CP-02 | — |
| REQ-REC-002 | Deterministic set algebra over element identity, not fuzzy matching | Reconciliation | — | P5-W1 | — | — | CP-02 | — |
| REQ-REC-003 | Mismatch is not defect; four deterministic checks precede any candidate (INV-5) | Reconciliation | — | P5-W2 | — | — | CP-02 | — |
| REQ-REC-004 | All six explanations for a difference are expressible | Reconciliation | — | P5-W2 | — | — | CP-02 | — |
| REQ-REC-005 | Confidence updates traceable to a reconciliation event | Reconciliation | — | P5-W3 | — | — | CP-02 | — |

### Coverage and test design

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-COV-001 | Obligations derived from UAU elements, traceably | Coverage | — | P6-W1 | — | — | CP-03 | — |
| REQ-COV-002 | Coverage computed from explicit satisfaction links (INV-7) | Coverage | — | P6-W1 | — | — | CP-03 | — |
| REQ-COV-003 | No code path allows a model to set a coverage value | Coverage | — | P6-W1 | — | — | CP-03 | — |
| REQ-COV-004 | Five obligation states incl. insufficient evidence and not applicable | Coverage | — | P6-W1 | — | — | CP-03 | — |
| REQ-COV-005 | Reports state denominator, understanding version and unverified share | Coverage | — | P6-W2 | — | — | CP-03 | — |
| REQ-TEST-001 | All eight test-design techniques registered as strategies | Test Design | `ScenarioContract` | P6-W3 | — | — | CP-03 | — |
| REQ-TEST-002 | Technique selectable only where the understanding supports it | Test Design | `ScenarioContract` | P6-W3 | — | — | CP-03 | — |
| REQ-TEST-003 | Technique selection rationale recorded per scenario | Test Design | `ScenarioContract` | P6-W3 | — | — | CP-03 | — |
| REQ-TEST-004 | New technique added without orchestration change | Test Design | `ScenarioContract` | P6-W3 | — | — | CP-03 | — |
| REQ-TEST-005 | Configurable generation limits per workspace | Test Design | — | P6-W4 | — | — | CP-03 | — |
| REQ-TEST-006 | Test cases carry semantic element references, never locators | Test Case | `TestCaseContract` | P7-W1 | — | — | CP-04 | — |
| REQ-TEST-007 | Data requirements declared, not resolved, at generation time | Test Data | `DataRequirementContract` | P7-W2 | — | — | CP-04 | — |
| REQ-TEST-008 | Five-rung resolution ladder, each recording prior failure | Test Data | — | P7-W2 | — | — | CP-04 | — |
| REQ-TEST-009 | State provisioning separate from value generation, policy-gated | Test Data | — | P7-W3 | — | — | CP-04 | — |
| REQ-TEST-010 | Field semantics from UAU, not generic patterns | Test Case | — | P7-W1 | — | — | CP-04 | — |

### Automation

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-AUTO-001 | Specification and binding are separate persisted artifacts | Automation | `AutomationSpecification`/`Binding` | P8-W1 | — | — | CP-05 | — |
| REQ-AUTO-002 | Same specification runs against two environments via different bindings | Automation | — | P8-W2 | — | — | CP-05 | — |
| REQ-AUTO-003 | Page/component objects projected from UAU and shared | Automation | — | P8-W3 | — | — | CP-05 | — |
| REQ-AUTO-004 | Locator lifecycle persisted with provenance | Automation | — | P8-W4 | — | — | CP-05 | — |
| REQ-AUTO-005 | Healing cannot write to specification storage (INV-6, structural) | Automation | — | P8-W5 | — | — | CP-05 | — |
| REQ-AUTO-006 | Healing default is propose-only; auto-apply disabled at ship | Automation | — | P8-W5 | — | — | CP-05 | — |
| REQ-AUTO-007 | Every healing event recorded with before, after, evidence, score | Automation | — | P8-W5 | — | — | CP-05 | — |

### Execution

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-EXEC-001 | Real Playwright execution in isolated workers | Execution | `ExecutionRequest` | P9-W1 | — | — | CP-06 | — |
| REQ-EXEC-002 | Workers hold no database credentials (INV-10) | Execution | — | P0-W5-T2 | — | — | CP-00 | — |
| REQ-EXEC-003 | Configurable environments; no environment values in automation | Execution | — | P9-W2 | — | — | CP-06 | — |
| REQ-EXEC-004 | Secrets as references, resolved in-worker, never in artifacts or logs | Execution / Platform | — | P0-W7-T3 | — | — | CP-00 | — |
| REQ-EXEC-005 | Concurrency = min(global, per-environment, per-run) | Execution | — | P9-W3 | — | — | CP-06 | — |
| REQ-EXEC-006 | Retries classified before attempted; each is a distinct attempt | Execution | — | P9-W4 | — | — | CP-06 | — |
| REQ-EXEC-007 | Complete artifacts captured: trace, video, screenshots, network, console | Execution | — | P9-W5 | — | — | CP-06 | — |
| REQ-EXEC-008 | Execution history and run-over-run comparison on stable identity | Execution | — | P9-W6 | — | — | CP-06 | — |

### Evaluation, triage, findings

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-EVAL-001 | Three evaluator layers; deterministic first, can fail outright | Evaluation | `EvaluatorContract` | P10-W1 | — | — | CP-07 | — |
| REQ-EVAL-002 | Semantic verdict cannot overturn a deterministic one | Evaluation | `EvaluatorContract` | P10-W1 | — | — | CP-07 | — |
| REQ-EVAL-003 | Evaluators independently versioned; version recorded on verdicts | Evaluation | `EvaluatorContract` | P10-W1 | — | — | CP-07 | — |
| REQ-EVAL-004 | Triage ladder runs before any defect classification | Triage | `FindingContract` | P10-W2 | — | — | CP-07 | — |
| REQ-EVAL-005 | A failed assertion alone never yields a product defect finding | Triage | `FindingContract` | P10-W2 | — | — | CP-07 | — |
| REQ-EVAL-006 | Never-executed items carry assessments, excluded from pass rates | Triage | — | P10-W2 | — | — | CP-07 | — |
| REQ-EVAL-007 | Findings carry evidence and lineage to case, scenario, UAU version | Findings | `FindingContract` | P10-W3 | — | — | CP-07 | — |

### Governance

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-GOV-001 | Exactly one gate implementation for both modes (INV-9) | Governance | `GateContract` | P11-W1 | — | — | CP-07 | — |
| REQ-GOV-002 | Autonomous mode cannot bypass evaluators, safety or environment policy | Governance | `GateContract` | P11-W2 | — | — | CP-07 | — |
| REQ-GOV-003 | Approved versions immutable | Platform / Artifact | — | P0-W3-T3 | — | — | CP-00 | — |
| REQ-GOV-004 | Human edit creates new version, retains machine original, re-derives confidence | Governance | — | P11-W3 | — | — | CP-07 | — |
| REQ-GOV-005 | Rejection auditable with recorded reason | Governance | — | P11-W3 | — | — | CP-07 | — |
| REQ-GOV-006 | One escalation mechanism for stalled gate, blocker and unknown oracle | Governance | `GateContract` | P11-W4 | — | — | CP-07 | — |
| REQ-GOV-007 | Append-only audit log; every decision attributable to actor and policy version | Governance | — | P0-W9-T2 | — | — | CP-00 | — |

### Providers, security, platform

| ID | Requirement | Component | Contract | Roadmap | Impl | Test | Checkpoint | Evidence |
|---|---|---|---|---|---|---|---|---|
| REQ-PROV-001 | Code Intelligence replaceable; platform functional without it | Provider Integration | `CodeIntelligenceProvider` | P3 | — | — | CP-01 | — |
| REQ-PROV-002 | Crawler replaceable without changing state model or planning | Provider Integration | `CrawlerProvider` | P4 | — | — | CP-02 | — |
| REQ-PROV-003 | Two LLM adapters exist; identical typed output; provenance records which | Provider Integration | `LanguageModelProvider` | P0-W7-T2 | — | — | CP-00 | — |
| REQ-PROV-004 | New tool/MCP registered and invoked with no stage-level code | Provider Integration | `Capability` | P0-W7-T1 | — | — | CP-00 | — |
| REQ-PROV-005 | Single capability-invocation path; sole egress point | Provider Integration | `Capability` | P0-W7-T1 | — | — | CP-00 | — |
| REQ-SEC-001 | Zero provider imports in domain modules (static check) | Platform | — | P0-W8-T3 | — | — | CP-00 | — |
| REQ-SEC-002 | Repository and page content treated as data, never instructions | Platform / Security | — | P2-W1-T1 | — | — | CP-01 | — |
| REQ-SEC-003 | Generated content rendered as text, never markup | Platform / API | — | P0-W5-T3 | — | — | CP-00 | — |
| REQ-SEC-004 | Workspace scoping enforced at the data-access layer | Platform | — | P0-W3-T2 | — | — | CP-00 | — |
| REQ-SEC-005 | Secret-pattern scan blocks artifact storage | Platform / Security | — | P0-W9-T3 | — | — | CP-00 | — |
| REQ-SEC-006 | Domain allowlist enforced for browser sessions | Execution / Exploration | — | P4-W1 / P9-W2 | — | — | CP-02, CP-06 | — |
| REQ-PLAT-001 | Durable run/stage state in the product database | Platform / Workflow | `StageContract` | P0-W6-T1 | — | — | CP-00 | — |
| REQ-PLAT-002 | Stages idempotent and resumable; partial results valid | Platform / Workflow | `StageContract` | P0-W6-T3 | — | — | CP-00 | — |
| REQ-PLAT-003 | Excel export via adapter with no domain coupling | Platform / Export | — | P13-W1 | — | — | CP-08 | — |
| REQ-PLAT-004 | Durable artifact views for all artifact types | API / UI | — | P0-W5-T3 | — | — | CP-00 | — |

---

## Gap analysis

**Requirements defined:** 103
**Traced to a contract or component:** 103
**Traced to a roadmap item:** 103
**Implemented:** 0
**Tested:** 0
**Validated at a checkpoint:** 0

Per group: SRC 6 · UAU 12 · STA 13 · EXP 8 · REC 5 · COV 5 · TEST 10 · AUTO 7 ·
EXEC 8 · EVAL 7 · GOV 7 · PROV 5 · SEC 6 · PLAT 4

### Known coverage gaps in this matrix

| Gap | Note |
|---|---|
| No UX/UI requirements are itemised | The prototype defines the interaction model; artifact-view requirements are captured only as REQ-PLAT-004. **Expand before P0-W5-T3.** |
| Performance and scale requirements absent | Blocked on OD-4 and unknown execution scale. **Must be added before P13.** |
| Accessibility requirements absent | Not yet discussed with the Product Owner. `OPEN` |
| Deployment/operability requirements thin | Blocked on OD-7. **Must be added before P13.** |

These gaps are recorded rather than silently omitted, per the purpose of this document.
