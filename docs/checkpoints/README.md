# Checkpoints

**Restructured in Roadmap V2** — 9 combined checkpoints became 13 distinct ones, so that
logically separate capabilities are validated separately.

Each checkpoint is written **before** implementation of its phase begins, and requires
**both** a product validation (what the QA user can do) and an engineering validation
(what the platform proves internally).

| ID | Name | Phase(s) | Vertical slice | Status |
|---|---|---|---|---|
| [CP-00](checkpoint-00-platform-foundation.md) | Platform Foundation | P0 | Prerequisite for Slice A | NOT_STARTED |
| [CP-01](checkpoint-01-source-ingestion.md) | Source Ingestion | P1 | Slice A (part 1) | NOT_STARTED |
| [CP-02](checkpoint-02-static-understanding.md) | Static Application Understanding | P2, P3 (contract only) | Slice A (part 2) — completes Slice A | NOT_STARTED |
| [CP-03](checkpoint-03-test-design.md) | Test Design & Coverage | P6 | Slice B (part 1) | NOT_STARTED |
| [CP-04](checkpoint-04-test-case-data.md) | Test Case & Test Data | P7 | Slice B (part 2) — completes Slice B | NOT_STARTED |
| [CP-05](checkpoint-05-automation.md) | Automation | P8 | Slice C (part 1) | NOT_STARTED |
| [CP-06](checkpoint-06-execution.md) | Real Execution | P9 | Slice C (part 2) — completes Slice C | NOT_STARTED |
| [CP-07](checkpoint-07-evaluation-findings.md) | Evaluation & Findings | P10 | Slice D | NOT_STARTED |
| [CP-08](checkpoint-08-governance.md) | Governance | P11 | Cross-cutting | NOT_STARTED |
| [CP-09](checkpoint-09-runtime-exploration.md) | Runtime Exploration | P4 | Enrichment of Slices A and C | NOT_STARTED |
| [CP-10](checkpoint-10-reconciliation.md) | Static/Runtime Reconciliation | P5 | Enrichment of Slices A, B and D | NOT_STARTED |
| [CP-11](checkpoint-11-vertical-slices.md) | Integrated Vertical Slices | P12 | Slice E — the full journey | NOT_STARTED |
| [CP-12](checkpoint-12-hardening.md) | Phase 1 Hardening & Release Readiness | P13 | All | NOT_STARTED |

## Dependency model

Each checkpoint distinguishes four dependency kinds, so that optional enrichment is never
mistaken for a prerequisite:

| Kind | Meaning |
|---|---|
| **Development dependency** | Must exist before implementation can begin |
| **Validation dependency** | Must exist before the capability can be fully validated |
| **Optional enrichment** | Improves the capability; not required for it to exist |
| **Parallel work** | Can safely be built independently |

> **The correction that matters most:** CP-10 (Reconciliation) has **CP-02 only** as its
> development dependency. Runtime observations may come from recorded sessions, synthetic
> observations or replay fixtures. A live crawler is *optional enrichment*, never a
> prerequisite — consistent with ADR-002 and INV-4.

Evidence is stored under `evidence/<checkpoint-id>/` and linked from each result record
and from [`../08_TRACEABILITY_MATRIX.md`](../08_TRACEABILITY_MATRIX.md).
