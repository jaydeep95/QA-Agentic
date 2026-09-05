# Architecture Documents

**Last updated:** 2026-09-05 · **Consolidation decision:** DEC-004

---

## Which document is canonical

| Document | Role | Status |
|---|---|---|
| [`../02_ARCHITECTURE_BASELINE.md`](../02_ARCHITECTURE_BASELINE.md) | **CANONICAL for architecture.** Where it and any HTML below differ, this wins and the HTML is a defect to correct | ACTIVE |
| [`architecture-overview.html`](architecture-overview.html) | **CANONICAL for rendered content.** Two-level document, 15 diagrams. The version to review | ACTIVE — v2.1 |
| [`architecture-standalone-v2.html`](architecture-standalone-v2.html) | Same content, self-contained: full HTML document with a diagram renderer and offline fallback. For sharing outside this repository | ACTIVE — v2.1 |
| [`architecture-baseline-v1-SUPERSEDED.html`](architecture-baseline-v1-SUPERSEDED.html) | **SUPERSEDED** by v2 under DEC-001. Retained for history — do not build from it | SUPERSEDED |
| [`architecture-diagrams/`](architecture-diagrams/) | All 15 diagram sources as plain-text `.mmd`. Render in any Mermaid viewer | ACTIVE |

---

## Why v1 was superseded

Recorded in full as **DEC-001** in [`../10_DECISION_AND_CHANGE_LOG.md`](../10_DECISION_AND_CHANGE_LOG.md). Summary:

| Changed | From (v1) | To (v2) |
|---|---|---|
| Central concept | Claims presented as the canonical unit — "a claims system" | **Unified Application Understanding** is the central asset; claims, evidence, graph, state, document and retrieval structures are supporting representations |
| Runtime exploration | Read as an implicit prerequisite | **Optional.** Static-only is a fully supported operating mode (INV-4) |
| Coverage language | "Near-complete coverage is not achievable" | The platform systematically pursues very high coverage, measured against explicit obligations and reported with uncovered and insufficient-evidence gaps |
| Phase 1 depth | Production / functional / bounded tiering, which implied stubs | **All Phase 1 capabilities are real and functional** within a stated bound. Only the two provider bindings are placeholders |
| Structure | Single level, ~2,100 lines | Two levels: executive overview plus collapsible detail |
| Added | — | The explicit principle *Independent Evolution by Contract* |

**No capability was added or removed.** The change was conceptual and presentational.

---

## Diagram sources

The 15 diagrams, extracted as plain text so they outlive any renderer:

```text
diagram-1-architecture-at-a-glance.mmd        the whole system on one page
diagram-2-control-plane-and-workers.mmd       deployment shape
diagram-3-canonical-model-and-its-projections.mmd
diagram-4-static-and-runtime-contribution.mmd two evidence sources, one model
diagram-5-platform-lifecycle.mmd              the loop, with feedback
diagram-6-test-intent-to-executable-test.mmd  specification / binding split
diagram-7-coverage-model.mmd                  obligations to reported state
diagram-8-cross-cutting-quality-mechanism.mmd evaluators and confidence
diagram-9-human-and-autonomous-governance.mmd one gate, two deciders
diagram-10-trust-boundaries.mmd               security
diagram-11-components-connected-by-contracts.mmd  independent evolution
diagram-d1-lineage-chain.mmd                  evidence to finding
diagram-d2-static-understanding-pipeline.mmd
diagram-d3-exploration-loop.mmd
diagram-d8-execution-path.mmd
```

(Filenames are generated from diagram captions; the listing above is indicative — run
`ls architecture-diagrams/` for the exact set.)

---

## Recoverability note

The architecture previously existed only as a published artifact and in temporary session
storage. It is now fully contained in this repository: the Markdown canonical statement,
the rendered document, a self-contained shareable copy, and every diagram as plain text.

**The repository no longer depends on any external system to reconstruct its own architecture.**

One dependency remains outside version control: the empirical extraction measurements cited
in [`../02_ARCHITECTURE_BASELINE.md`](../02_ARCHITECTURE_BASELINE.md) §6 were produced by a harness at
`c:\waynautic\skill-test\`. The **figures** are transcribed into that document and survive
its loss; the **ability to re-measure** does not. That gap is tracked as **OD-9**.
