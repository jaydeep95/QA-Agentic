# 03 — Unified Application Understanding (UAU)

**Status:** ACTIVE — canonical for the central data asset
**Last updated:** 2026-09-05
**Controls:** what UAU is, how it is versioned, how evidence and confidence attach, how downstream artifacts reference it

---

## 1. What UAU is

The Unified Application Understanding is the platform's **evidence-backed, versioned model
of the application under test**. It is the central product asset: tests are regenerable,
the understanding is not.

It is a *semantic model of an application*, not a document about an application and not a
database of code facts. Every element in it answers three questions simultaneously:

1. **What is true of the application?**
2. **What supports that?**
3. **How much weight should it carry?**

## 2. Why it exists

| Without UAU | With UAU |
|---|---|
| Each generation stage re-derives context from source, inconsistently | One versioned model, consumed identically by every stage |
| No way to measure coverage — no denominator | Obligations derive from the model; coverage is arithmetic |
| Generated output cannot be traced to a cause | Every artifact traces to elements, evidence and a revision |
| A model can assert anything | Assertions carry evidence, inference type and confidence |
| Static and runtime knowledge live in separate silos | Both contribute to one model; disagreement is representable and valuable |
| Change impact is unknowable | Understanding diff identifies exactly what became stale |

---

## 3. What it represents

| Category | Elements |
|---|---|
| **Structure** | Entities · screens/pages · components · fields · APIs · backend services · data structures · configuration |
| **Relationships** | Navigation · component composition · frontend→backend call paths · service and data dependencies · cross-repository links |
| **Behaviour** | Validations · business rules · permissions and roles · states · transitions · error paths |
| **Runtime** | Observed pages · observed DOM · observed interactions · observed network requests · observed responses · observed state transitions · validated locators |
| **Grounding** | Evidence · provenance · inference type · oracle status · reconciliation status · confidence · version |

Every element carries its own grounding. A screen is not simply "known to exist" — it is
known **on the basis of specific evidence**, reached by a **specific kind of inference**,
**confirmed or not confirmed** at runtime, and carried at a **stated confidence**.

---

## 4. Internal representation

> **Classification: FLEXIBLE.** The technique below is recommended and empirically sound,
> but it *implements* the canonical model rather than defining it. A different internal
> representation that satisfies the same requirements is permitted, subject to a Decision
> Log entry.

### 4.1 Layers

| Layer | Is | Is not |
|---|---|---|
| **Evidence** | An immutable, anchored record that something was observed at a point in time, in a version of the world | An interpretation. Evidence carries no confidence — it either resolves or it does not |
| **Observation** | Structured output from an extractor or provider, before normalisation. Retained for reprocessing | Canonical. Nothing downstream reads it |
| **Assertion** | A normalised statement about the application, supported by ≥1 evidence record, carrying inference type, oracle status where applicable, reconciliation status and computed confidence | A fact. It may be wrong, superseded, contradicted or unverified — and the model records which |
| **Understanding version** | A named snapshot pinning the repository tuple, the included exploration sessions, and the assertion set | A branch. Versions are linear per workspace and immutable once referenced |

### 4.2 Evidence types

| Type | Anchor fields |
|---|---|
| `StaticEvidence` | repository id · revision · path · symbol · span · content hash |
| `RuntimeEvidence` | exploration session · step · URL · DOM hash · network event · artifact reference |
| `ProviderEvidence` | invocation id · provider identity · provider version |
| `ExecutionEvidence` | execution attempt · result · artifact reference |

**Requirement (INV-8):** no assertion may exist without at least one evidence reference.

**Requirement:** evidence records hold *anchors and references*. Large payloads (DOM
snapshots, traces, video) live in object storage and are fetched on drill-in. This is what
prevents evidence from turning every artifact into an unmanageable blob.

### 4.3 Content-derived identity

Each assertion carries an identity computed from its **normalised subject, relation and
object** — a route template, an endpoint method and path, a symbol's qualified name —
never from a database key, file offset or extraction order.

Four capabilities follow from this single choice:

| Capability | Mechanism |
|---|---|
| Understanding diff across revisions | Set difference on identity |
| Static ↔ runtime reconciliation | Intersection and difference of two populations sharing one key space — the same fact observed twice collides **by construction**, not by fuzzy matching |
| Corroboration | Two extractors producing the same identity means two independent evidence records on one assertion — exactly the signal confidence needs |
| Stable comparison across regeneration | Removes the identity fragility of index-based matching |

**Known cost — recorded honestly:** identity is only as stable as the normalisation
producing it. Renaming a route, endpoint or symbol reads as *replace*, not *rename*.
Phase 1 implements heuristic rename detection for routes and endpoints only and reports
other churn honestly. An incomplete diff is preferable to a misleading one.

---

## 5. Projections

Projections are **derived, rebuildable and disposable**. None is authoritative. None is
backed up — a projection that cannot be rebuilt from the canonical model is a design error.

| Projection | Purpose | Consumers |
|---|---|---|
| **Graph** | Relationship traversal, impact analysis | Trace queries, UI, exploration planning |
| **State / behaviour** | States and transitions | Path and transition test design |
| **Domain model** | Entities, fields, constraints | Field semantics, test data generation |
| **Coverage obligation** | Testable obligations | Coverage measurement |
| **Document** | Human-readable understanding | Review and approval surfaces |
| **Retrieval / index** | Search and context assembly | Transcript search, prompt context bundles |

> The graph is a **view**, not the model. The graph implementation may change, be replaced
> or be removed without touching the canonical model. (Architecture decision 5)

---

## 6. How information enters UAU

```text
Repositories ──► Static Understanding ──┐
                                        ├──► Normalisation ──► Assertions ──► UAU version
Running AUT  ──► Runtime Exploration ───┘                            ▲
                                                                     │
                        Reconciliation ─────────────────────────────┘
                        (assigns status, may add/adjust)
```

### 6.1 Static contribution
Extractors produce observations; the platform normalises them into assertions with
`StaticEvidence`, an inference type and a prior confidence derived from calibrated
extractor reliability. Where corroborated by another extractor, the assertion gains a
second independent evidence record.

### 6.2 Runtime contribution
Exploration produces observations; the platform derives assertions with `RuntimeEvidence`.
The crawler provider never writes to UAU (INV-3).

### 6.3 Reconciliation
Compares the static-supported and runtime-supported populations and assigns each assertion
a reconciliation status, which then feeds confidence:

| Status | Effect on understanding |
|---|---|
| `matched` | Confidence raised — independently corroborated by two evidence families |
| `runtime-only` | Assertion added with runtime evidence; the static extractor gap is recorded for calibration |
| `static-only` | Confidence unchanged; marked unconfirmed |
| `different` | Both retained; neither deleted |
| `conflict` | Both retained with conflict status; confidence suppressed for both |
| `unverified` | No change — explicitly **not** evidence of absence |

---

## 7. Confidence

Confidence is **computed by the platform**, never self-reported by a model.

**Inputs:** calibrated extractor reliability · evidence strength and independence ·
inference type · reconciliation status · evaluator verdicts · oracle status.

**Outputs:** a numeric score plus one of three user-facing bands —
`Mostly Confident` · `Somewhat Confident` · `Insufficient Evidence`.

**Five concepts stay distinct** in the data model and in the interface. Collapsing them
into a single number is the failure mode this design exists to prevent:

| Concept | Question | Values |
|---|---|---|
| Evidence | What supports this? | Source location · runtime artifact · execution result · provider invocation |
| Inference type | How was it established? | Deterministic · Derived · Semantic |
| Oracle status | Is expected behaviour known? *(expectation-bearing elements only)* | Confirmed · Partially confirmed · Unknown |
| Reconciliation | Do static and runtime agree? | Matched · Runtime-only · Static-only · Different · Conflict · Unverified |
| Confidence | How much weight should this carry? | Numeric + band |

**Oracle status applies only to expectation-bearing elements.** "This route exists" needs
no oracle. "Submitting an end date before the start date must show a validation message"
does.

**Calibration is a platform component**, not a dev script: a maintained ground-truth corpus,
per-extractor precision and recall by element type, never averaged into a headline figure,
feeding the confidence function as measured priors. Bands do not gate autonomous decisions
until calibration is reproduced on a second application. Rationale: [`02_ARCHITECTURE_BASELINE.md`](02_ARCHITECTURE_BASELINE.md) §6.

---

## 8. Versioning

### 8.1 What a version pins

```text
UnderstandingVersion {
  id
  workspace
  repository_tuple    [ {repo, revision}, ... ]   ← ALL repositories, not one
  exploration_sessions [ session_ids ]            ← empty in static-only mode
  assertion_set        (immutable once referenced)
  created_at, created_by, mechanism
  parent_version                                  ← linear chain
}
```

### 8.2 Version lifecycle

| Event | Result |
|---|---|
| New repository revision | Diff-scoped rebuild → new understanding version |
| Exploration session completes | Reconciliation runs → new understanding version |
| Human edit to an understanding element | New version; machine original retained; confidence re-derived, not inherited |
| Projection rebuild | **No** new version — projections are derived |

### 8.3 Incremental rebuild
Changed files determine which extractors rerun. Unchanged assertions carry forward with
evidence re-anchored where content hashes match. Assertions whose supporting evidence no
longer resolves become `stale`.

---

## 9. The downstream invariant

> **INV-2 — No downstream artifact is generated against an implicit, unversioned or
> unknown application context.**

```text
Understanding V12
      ↓
Scenario V12
      ↓
Test Case V12
      ↓
Automation V12
      ↓
Execution
```

**Every** downstream artifact records the UAU version it was generated from, as a required,
non-nullable field. This is enforced at the schema level, not by convention.

### 9.1 Staleness detection

When a new understanding version supersedes the one an artifact was built against:

1. Diff the two versions by assertion identity
2. For each changed/removed assertion, find artifacts whose lineage references it
3. Mark those artifacts `stale` with a reason and the specific changed elements
4. **Surface the staleness — do not auto-resolve it**

Regeneration after an upstream change is a **governance decision**, never a background job.
An approved artifact stays approved and frozen; a new version supersedes it only through a gate.

### 9.2 What staleness does not mean
A stale artifact is not invalid. It was correct for its version and its evidence is intact.
It simply may no longer reflect the current application, and a human or policy decides
whether that matters.

---

## 10. Requirements this document imposes on implementation

| ID | Requirement |
|---|---|
| UAU-R1 | No assertion without ≥1 evidence reference |
| UAU-R2 | Evidence is append-only and immutable |
| UAU-R3 | Assertion identity is content-derived and deterministic |
| UAU-R4 | Every understanding version pins a full repository tuple |
| UAU-R5 | Every downstream artifact carries a non-nullable UAU version reference |
| UAU-R6 | Projections are rebuildable from the canonical model with no data loss |
| UAU-R7 | Confidence is computed by platform code; no model output path can set it |
| UAU-R8 | Evidence, inference type, oracle status, reconciliation status and confidence are five separate persisted fields |
| UAU-R9 | Large evidence payloads are stored by reference, not inline |
| UAU-R10 | Staleness is computed and surfaced, never auto-resolved |

These are traced in [`08_TRACEABILITY_MATRIX.md`](08_TRACEABILITY_MATRIX.md) and validated at checkpoint CP-01.
