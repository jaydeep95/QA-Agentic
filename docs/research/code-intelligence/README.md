# Research — CODE_INTELLIGENCE_PROVIDER

**Status:** `OPEN` — provider not selected
**Final decision:** `UNDECIDED`
**Decision gate:** OD-5 (access) + Validation B (measured contribution)
**Last updated:** 2026-09-05

> **Do not silently finalise this provider.** The research-first sequence in
> [`../../11_RESEARCH_AND_OPEN_DECISIONS.md`](../../11_RESEARCH_AND_OPEN_DECISIONS.md) §3 is mandatory before any binding.

---

## 1. Problem statement

Static code understanding is the foundation of the Unified Application Understanding.
Measured native extraction quality is uneven: entity inventory and schema semantics are
near-perfect, while **frontend-to-backend relationship mapping measured P 0.414 / R 0.632**
— the weakest and most valuable class.

The question this research answers: **can an external code-intelligence capability raise
the measured floor on the weak classes, at acceptable cost and without compromising
reproducibility?**

---

## 2. Current architectural boundary

| Platform owns | Provider may supply |
|---|---|
| The Unified Application Understanding | Repository analysis |
| Normalisation into understanding elements | Symbol and entity discovery |
| Element identity and versioning | Typed relationships with evidence |
| Confidence computation and calibration | Semantic code retrieval |
| Evidence anchoring and provenance | Contextual code understanding |
| Reconciliation | — |

**Invariant:** providers supply observations; only the platform builds understanding (INV-3).
A provider result never reaches the canonical model unmediated.

Contract: [`../../04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](../../04_DOMAIN_AND_COMPONENT_CONTRACTS.md) §3.

---

## 3. Positioning options

| Option | Description | Assessment |
|---|---|---|
| **A — Authoritative** | The provider owns canonical code context | **Rejected.** Breaks revision pinning and reproducibility; cannot carry runtime evidence; makes the knowledge model theirs |
| **B — Advisory contributor + retrieval** | One extractor among several, corroborating native extraction; optional semantic retrieval | **Current recommendation** |
| **C — Not used** | Native extraction only | Viable fallback. Forfeits a potential accelerator on the weakest class |

**Current recommendation: Option B.** Reasoning: no single extractor measured uniformly
reliable, so corroboration beats a single source; external canonical ownership breaks
pinning; and an external model cannot represent runtime evidence, which is half of this
platform.

---

## 4. Capabilities required

Non-negotiable contract obligations for any candidate:

| # | Requirement | Why |
|---|---|---|
| C1 | Results carry **symbol- or span-level anchors** | Without them, results cannot become evidence-backed understanding (INV-8) |
| C2 | Results attributable to a **provider version** | Provenance and calibration |
| C3 | **Partial results expressible** with a completeness qualifier | Silent truncation corrupts coverage denominators |
| C4 | **Addressable per repository revision** | A service that re-indexes and re-points identifiers under a pinned understanding version cannot participate in a reproducible model |
| C5 | Declares its own capabilities | Registration fails closed rather than assuming capability |
| C6 | Degrades gracefully | The platform must be fully functional with the provider absent |

---

## 5. Evaluation criteria

| Criterion | How assessed | Threshold |
|---|---|---|
| Relationship quality | Precision/recall vs ground truth, native alone vs provider alone vs corroborated | ≥ 0.10 improvement on a class without degrading others |
| Anchor granularity | Inspect returned anchors | Symbol or span level required |
| Revision addressability | Query a pinned revision after a re-index | Identifiers must remain stable or be explicitly versioned |
| Latency | Ingestion and query time at representative repository size | Must not dominate the understanding build |
| Cross-repository support | Multi-repository index and cross-repo relations | Desirable, not mandatory |
| Deployment options | Hosted only vs private/on-premises | Interacts with OD-2 egress policy |
| Licensing | Terms for a product deliverable | Must permit commercial use |

---

## 6. Inputs required to proceed — OD-5

None of the following are currently available:

1. API documentation or specification
2. The entity and relationship schema exposed
3. A sandbox instance or credentials
4. Versioning and re-indexing semantics
5. Multi-repository and cross-repository support
6. Anchor granularity
7. Ingestion and query latency at representative repository size
8. Deployment options

> **Without these, Validation B cannot run and any evaluation would be speculation.**

---

## 7. Candidate approaches

| Candidate | Notes | Status |
|---|---|---|
| The named external Code Intelligence service | Referenced in product direction. Capabilities, API and schema **UNKNOWN** pending OD-5 | UNEVALUATED |
| Native extraction only (tree-sitter-class parsing + framework packs) | The baseline; measured figures exist | BASELINE |
| Code-property-graph analysis as an optional worker-side extractor | Directly targets the ~0.500 rule-recall gap; heavy runtime footprint, acceptable in a worker | CANDIDATE — complementary, not competing |

> No candidate has been evaluated. Recording them is not endorsing them.

---

## 8. Implications

| Dimension | Assessment |
|---|---|
| **Security** | Sending source to an external provider is an egress event governed by OD-2. If egress is prohibited, only a private or on-premises deployment is viable |
| **Performance** | Must not dominate the understanding build; measured in Validation B |
| **Integration** | One adapter behind the existing port; degraded-mode behaviour already specified |
| **Coupling risk** | Low by construction — the port exists precisely to contain it |
| **Licensing/deployment** | UNKNOWN pending OD-5 |

---

## 9. Empirical validation

**Validation B** — see [`../../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../../07_CHECKPOINT_AND_VALIDATION_PLAN.md) §5.

Result: **NOT RUN** — blocked on OD-5 and OD-3.

> Whatever the outcome, **no architectural change follows.** That is the point of advisory
> positioning: a pass adds an extractor, a fail leaves the port defined and unbound.

---

## 10. Decision record

| Field | Value |
|---|---|
| Recommendation | Option B — advisory contributor plus optional retrieval |
| Final decision | **UNDECIDED** |
| Decided by | — |
| Date | — |
| Migration impact | Bind adapter → run conformance suite → re-run calibration → update Decision Log → update roadmap Phase 3 |

When decided, follow the seven-step sequence in
[`../../11_RESEARCH_AND_OPEN_DECISIONS.md`](../../11_RESEARCH_AND_OPEN_DECISIONS.md) §3 before implementing.
