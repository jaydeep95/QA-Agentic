# 10 — Decision and Change Log

**Status:** ACTIVE — canonical for every change to a previously recorded decision
**Last updated:** 2026-09-05

---

## 1. The change-control rule

Before implementing any change that affects **product behaviour · architecture · component
responsibility · API contract · domain model · workflow · data model · persistence ·
provider boundary · test semantics · coverage model · automation semantics · execution
semantics · security boundary · governance · traceability · phase scope**, you must first
update the relevant documentation.

```text
Requested Change
       ↓
Assess Impact
       ↓
Update Roadmap / Architecture / Decision Log
       ↓
Update Current State if required
       ↓
Determine affected tests and checkpoints
       ↓
Implement
       ↓
Test
       ↓
Update Status / Evidence
```

> **Do NOT implement first and document later.** This order is never reversed for
> architectural or product-impacting changes.

---

## 2. Change classification

Every meaningful change is classified as exactly one of:

| Class | Meaning | Action required |
|---|---|---|
| **FIXED DECISION** | Already established | Follow it. Deviating requires a new decision entry superseding it |
| **FLEXIBLE IMPLEMENTATION CHOICE** | Implementation may choose among valid approaches without altering architecture or product intent | Record the choice in code/commit; no decision entry needed unless it later constrains others |
| **PLACEHOLDER** | Concrete technology or provider intentionally unresolved | Research first — see [`11_RESEARCH_AND_OPEN_DECISIONS.md`](11_RESEARCH_AND_OPEN_DECISIONS.md) |
| **OPEN DECISION** | Requires research, validation or explicit input | Do not decide unilaterally. Record and escalate |
| **CHANGE PROPOSAL** | A new idea that changes existing architecture or product intent | **Must not silently become implementation.** Requires an entry here with status `OPEN`, then approval |

---

## 3. Decision entry format

```text
Decision ID        DEC-nnn
Date
Area
Previous state
New state
Reason
Impact
Affected components
Affected contracts
Affected roadmap items
Migration required?
Tests affected?
Approved by / source
Status             ACCEPTED | SUPERSEDED | REVERSED | OPEN
```

---

## 4. Decision log

### DEC-001 — Architecture recomposed around Unified Application Understanding

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Architecture — central concept and document structure |
| **Previous state** | Architecture baseline v1 presented claims/evidence as the headline concept ("claims are the canonical unit"). Single-level document, ~2,100 lines |
| **New state** | Unified Application Understanding is the central product asset. Claims/assertions, evidence, graph, state, domain, coverage, document and retrieval structures are **supporting representations and projections**. Two-level document: executive overview plus collapsible detail |
| **Reason** | Architecture review found the claims-first framing obscured the product story and made the design read as dependent on one representation technique. It also made the document hard to review as a whole |
| **Impact** | Presentational and conceptual; no capability added or removed. Internal representation reclassified from FIXED to FLEXIBLE |
| **Affected components** | Understanding Domain (framing); all documents referencing the canonical model |
| **Affected contracts** | `UnderstandingQuery`, `UnderstandingContribution` — renamed conceptually from claim-centric to understanding-centric |
| **Affected roadmap items** | P2-W4, P2-W6 |
| **Migration required?** | No — no implementation exists |
| **Tests affected?** | None — no tests exist |
| **Approved by / source** | Architecture review, this engagement |
| **Status** | **ACCEPTED** |

**Also corrected in the same revision:**
- Runtime exploration reclassified from implicit prerequisite to **optional** (INV-4). Static-only is a fully supported operating mode
- Coverage language changed from "near-complete coverage is not achievable" to *the platform systematically pursues very high coverage, measured against explicit obligations and reported with uncovered and insufficient-evidence gaps*
- Phase 1 "production/functional/bounded depth" tiering removed. **All Phase 1 capabilities are real and functional within a stated bound.** Only the two provider bindings are placeholders
- Added the explicit principle *Independent Evolution by Contract*

Superseded document retained: [`architecture/architecture-baseline-v1-SUPERSEDED.html`](architecture/architecture-baseline-v1-SUPERSEDED.html)

---

### DEC-002 — Control-plane language and runtime not yet fixed

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Technology |
| **Previous state** | Provisionally recommended: analysis/domain in the ecosystem with the strongest static-analysis tooling; browser workers in the Playwright-native ecosystem |
| **New state** | **Recorded as OPEN.** The recommendation stands as a recommendation, not a decision |
| **Reason** | The split is defensible on ecosystem grounds, but team composition is unknown (OD-4) and this is a high-migration-cost choice. It must not be made silently by whoever writes the first file |
| **Impact** | Blocks P0-W1-T2 (module layout) and P0-W1-T3 (dependency management) from being *finalised*, though scaffolding can begin once chosen |
| **Affected components** | All |
| **Affected contracts** | None — contracts are technology-neutral by design |
| **Affected roadmap items** | P0-W1-T2, P0-W1-T3 |
| **Migration required?** | N/A |
| **Tests affected?** | N/A |
| **Approved by / source** | Architecture baseline §10 |
| **Status** | **OPEN** |

---

### DEC-003 — Documentation source-of-truth system established

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Engineering governance |
| **Previous state** | Project knowledge existed only in conversation and in one HTML file in the repository root. No git, no roadmap, no state tracking |
| **New state** | `/docs` tree established as the durable source of truth: 14 canonical documents, architecture consolidated into the repository, research areas, checkpoints, traceability matrix, working protocol |
| **Reason** | The conversation may disappear. A new session must be able to recover the entire project from the repository alone |
| **Impact** | All future work follows the documentation-first change process defined in §1 |
| **Affected components** | None (governance) |
| **Affected contracts** | None |
| **Affected roadmap items** | All — the roadmap now exists |
| **Migration required?** | No |
| **Tests affected?** | None |
| **Approved by / source** | Engineering governance bootstrap task |
| **Status** | **ACCEPTED** |

---

### DEC-004 — Architecture documents consolidated into the repository

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Documentation durability |
| **Previous state** | The canonical architecture existed as a published artifact and as files in temporary session storage. The repository held one standalone HTML file at its root |
| **New state** | Consolidated under `docs/architecture/`: `architecture-overview.html` (canonical, rendered), `architecture-standalone-v2.html` (self-contained, offline-capable), `architecture-baseline-v1-SUPERSEDED.html` (history), and all 15 diagram sources extracted to `architecture-diagrams/*.mmd` |
| **Reason** | The repository must not depend on temporary storage or a published artifact for its own architecture. Diagram sources in plain text survive any renderer |
| **Impact** | Architecture is recoverable from the repository alone |
| **Affected roadmap items** | None |
| **Migration required?** | No |
| **Approved by / source** | Bootstrap task §28 |
| **Status** | **ACCEPTED** |

**Canonical version:** `architecture-overview.html` is canonical for rendered content;
[`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) is canonical where the two differ.

---

### DEC-005 — Numeric thresholds reclassified as PROVISIONAL

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Validation and quality targets |
| **Previous state** | Fifteen numeric thresholds appeared as validation pass criteria and, in one case, as a quality requirement — reading as product obligations |
| **New state** | All classified `PROVISIONAL` in [`14_ARCHITECTURAL_DECISION_REGISTER.md`](14_ARCHITECTURAL_DECISION_REGISTER.md) §3 with full provenance metadata |
| **Reason** | Provenance audit found every one originated as an engineering hypothesis in the architecture validation plan. **None had recorded Product Owner approval.** A decision is not FIXED merely because it appears in the roadmap |
| **Impact** | No threshold may be traced to a product requirement without explicit approval. Validations still use them as decision rules |
| **Affected roadmap items** | Validation experiments A–F |
| **Migration required?** | No — no implementation exists |
| **Approved by / source** | Roadmap V2 correction, CHG-001 |
| **Status** | **ACCEPTED** |

**Flagged for decision:** THR-SAFETY-001 (destructive-action classification recall 1.00) is
recommended for confirmation as a **FIXED safety requirement**. A missed destructive action
can damage a real environment. It is the one threshold where PROVISIONAL may be the wrong
classification, and only the Product Owner can settle it.

---

### DEC-006 — LLM adapter count reclassified

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Provider architecture |
| **Previous state** | Two adapters stated as required from the start, in four places |
| **New state** | ADR-013: the **abstraction is FIXED**. ADR-030: the **count is PROVISIONAL** — at least one real adapter before LLM-dependent functionality can be validated; additional adapters independently extensible |
| **Reason** | Provenance is an engineering argument in the v1 architecture text — that a contract with a single implementation is an assumption rather than an abstraction. Sound reasoning, never an approved requirement. The architectural decision is provider-agnosticism, which the abstraction delivers regardless of count |
| **Impact** | P0-W7-T2 no longer requires two adapters to close |
| **Affected documents** | 02 §10, 04 §11 and §14, 09 §2.2 |
| **Migration required?** | No |
| **Approved by / source** | Roadmap V2 correction, CHG-001 |
| **Status** | **ACCEPTED** |

---

### DEC-007 — Reconciliation decoupled from the crawler phase

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Roadmap dependencies |
| **Previous state** | P5 (Reconciliation) listed P4 (Runtime Exploration) as a dependency; both shared checkpoint CP-02 |
| **New state** | P5 depends on **P2 only**. Runtime observations may be recorded, synthetic, replay fixtures or controlled datasets. Live `CrawlerProvider` is **optional enrichment**. Separate checkpoints: CP-09 exploration, CP-10 reconciliation |
| **Reason** | The dependency contradicted ADR-002 and INV-4, which state that static and runtime are independent evidence sources and that runtime exploration is optional. It made the crawler a hidden prerequisite for a capability that does not need one |
| **Impact** | Reconciliation can be implemented and validated before any provider is selected; both phases come off the critical path |
| **Affected roadmap items** | P4, P5; checkpoints CP-09, CP-10 |
| **Migration required?** | No |
| **Approved by / source** | Roadmap V2 correction, CHG-001 |
| **Status** | **ACCEPTED** |

---

### DEC-008 — Checkpoint model restructured; governance moved earlier

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Validation model and phase structure |
| **Previous state** | Nine checkpoints, four combining two capabilities. Governance validated only at the end. A single dependency relation |
| **New state** | Thirteen checkpoints (CP-00…CP-12), each requiring **both** product and engineering validation. Governance foundations moved to P0-W10. Four-way dependency model: development, validation, optional enrichment, parallel |
| **Reason** | Combined checkpoints let one capability mask another. A checkpoint proving only internal machinery is not a checkpoint. Governance arriving late is a retrofit the stages have already learned to work around. A single dependency relation cannot express *helps but is not required* |
| **Impact** | Checkpoint IDs remapped across documents 05, 06, 07, 08, 12 and every checkpoint file |
| **Affected roadmap items** | All phases |
| **Migration required?** | No |
| **Approved by / source** | Roadmap V2 correction, CHG-001 |
| **Status** | **ACCEPTED** |

---

### DEC-009 — Rolling detail, vertical slices, UX requirements, reference corpus

| Field | Value |
|---|---|
| **Date** | 2026-09-05 |
| **Area** | Roadmap method and requirement coverage |
| **Previous state** | Uniform decomposition intent across phases; no user-value validation model; UX and reference-AUT gaps recorded but open |
| **New state** | **Rolling-detail rule** — the active phase is decomposed to implementation-ready level, future phases stay at capability level. **Five vertical slices** ([`16_VERTICAL_SLICES.md`](16_VERTICAL_SLICES.md)). **47 UX requirements** with eleven prototype shortcuts explicitly excluded. **Reference AUT corpus definition** with every field UNKNOWN pending OD-3 |
| **Reason** | Decomposing future phases now would freeze choices evidence has not yet informed. Component progress can look healthy while delivering nothing usable. The UX and corpus gaps blocked P0-W5-T3 and all calibration respectively |
| **Impact** | Roadmap gains a decomposition-state table; traceability gains 47 requirements; new documents 15, 16 and `test-assets/` |
| **Migration required?** | No |
| **Approved by / source** | Roadmap V2 correction, CHG-001 |
| **Status** | **ACCEPTED** |

---

## 5. Superseded and reversed decisions

| ID | Superseded by | Note |
|---|---|---|
| DEC-002 | — | Still OPEN; also recorded as ADR-059 in the register |

---

## 6. Pending change proposals

| ID | Proposal | Raised | Status |
|---|---|---|---|
| CHG-001 | Roadmap V2 correction | 2026-09-05 | Approved — DEC-005…009 |

> A change proposal must not silently become implementation. Add it here with status
> `OPEN`, assess impact, obtain approval, then convert it into a decision entry.
