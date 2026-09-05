# Agentic QA Automation Platform

> **New here — human or AI agent? Start with [`docs/00_PROJECT_SOURCE_OF_TRUTH.md`](docs/00_PROJECT_SOURCE_OF_TRUTH.md).**
> It tells you what this project is, exactly where development stands, and what to read next.

---

## What this is

A platform that builds and maintains a durable, evidence-backed **understanding of an
application under test**, and generates measurable test coverage, maintainable Playwright
automation, real execution and explainable findings from it.

```text
Understand AUT → Unified Application Understanding → (optional) Runtime Exploration
   → Reconciliation → Test Design → Test Case + Data → Automation
   → Execution → Evaluation + Findings → Knowledge Update ↺
```

The platform is not fundamentally "an AI that writes tests". It is a knowledge system that
happens to emit tests. The durable asset is the understanding; tests are regenerable.

---

## Current status — 2026-09-05

| | |
|---|---|
| **Phase** | Phase 0 — Repository & Platform Foundation |
| **Checkpoint** | CP-00 Foundation — `NOT_STARTED` |
| **Implementation** | **None. No platform source code exists yet.** |
| **Next step** | `P0-W1-T1` — initialise git repository |
| **Documentation** | Complete — source-of-truth system established |

This repository currently contains **architecture and planning documentation only**.
Nothing is implemented, tested or validated. That is recorded honestly in
[`docs/12_CURRENT_IMPLEMENTATION_STATE.md`](docs/12_CURRENT_IMPLEMENTATION_STATE.md) rather than implied otherwise.

---

## Repository layout

```text
docs/
├── 00_PROJECT_SOURCE_OF_TRUTH.md      ← start here
├── 01_PRODUCT_VISION_AND_SCOPE.md     what we are building
├── 02_ARCHITECTURE_BASELINE.md        how it is designed
├── 03_UNIFIED_APPLICATION_UNDERSTANDING.md   the central data asset
├── 04_DOMAIN_AND_COMPONENT_CONTRACTS.md      interfaces and replaceability
├── 05_DETAILED_DEVELOPMENT_ROADMAP.md        what to build, in order
├── 06_IMPLEMENTATION_PHASES.md               phase rules and completion definitions
├── 07_CHECKPOINT_AND_VALIDATION_PLAN.md      how completion is proven
├── 08_TRACEABILITY_MATRIX.md                 requirement → test → evidence
├── 09_TEST_STRATEGY.md                       how the platform itself is tested
├── 10_DECISION_AND_CHANGE_LOG.md             every change to a recorded decision
├── 11_RESEARCH_AND_OPEN_DECISIONS.md         what is not yet decided
├── 12_CURRENT_IMPLEMENTATION_STATE.md        what actually exists (living)
├── 13_CLAUDE_ENGINEERING_WORKING_PROTOCOL.md how to work in this repository
├── architecture/     canonical architecture documents + diagram sources
├── requirements/     scope, functional and quality requirements
├── research/         the two open provider decisions
└── checkpoints/      one document per checkpoint
```

---

## Working rules

These are not conventions — they are enforced by the working protocol and, once
implemented, by architecture tests.

1. **The repository is the source of truth**, not any conversation. Assume the conversation is gone.
2. **Documentation first** for any change affecting product behaviour, architecture, contracts, data model, security, governance or scope. Never implement first and document later.
3. **A stub is not complete.** Placeholder code, fake success, simulated execution and mock-only workflows may never be presented as functional capability.
4. **Only two placeholders exist:** `CODE_INTELLIGENCE_PROVIDER` and `CRAWLER_PROVIDER`. Their contracts are real architecture; their bindings are deliberately unselected.
5. **Record `UNKNOWN` as `UNKNOWN`.** Never convert uncertainty into false certainty.
6. **Advance a phase only when its checkpoint demonstrates the capability** — not because code exists.

Full protocol: [`docs/13_CLAUDE_ENGINEERING_WORKING_PROTOCOL.md`](docs/13_CLAUDE_ENGINEERING_WORKING_PROTOCOL.md)

---

## The ten architectural invariants

Violating any requires a Decision Log entry and an architecture update **before** code.

UAU is central · no artifact without a UAU version · providers are replaceable ·
static and runtime are independent evidence sources · mismatch is not defect ·
healing cannot change intent · coverage is obligation-based · evidence is first-class ·
one governance mechanism · workers are isolated.

Detail: [`docs/00_PROJECT_SOURCE_OF_TRUTH.md`](docs/00_PROJECT_SOURCE_OF_TRUTH.md) §7

---

## Architecture at a glance

Open [`docs/architecture/architecture-overview.html`](docs/architecture/architecture-overview.html)
in a browser — a two-level document with 15 rendered diagrams: an executive overview
readable in a few minutes, then collapsible technical detail.

Diagram sources are also stored as plain-text `.mmd` files in
[`docs/architecture/architecture-diagrams/`](docs/architecture/architecture-diagrams/) so they
survive any renderer.
