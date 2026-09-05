# Phase 1 Scope

**Status:** ACTIVE · **Derived from:** [`../01_PRODUCT_VISION_AND_SCOPE.md`](../01_PRODUCT_VISION_AND_SCOPE.md)

> This file is a **scope checklist**, not a second source of truth. Where it and
> document 01 differ, document 01 wins.

## In scope — all functionally delivered within a stated bound

| # | Capability | Phase | Bound |
|---|---|---|---|
| 1 | Repository onboarding, multi-repository workspace | P1 | None |
| 2 | Application/code understanding | P2 | Framework packs for the confirmed stack only |
| 3 | Application context graph (projection) | P2 | Derived, rebuildable |
| 4 | Frontend/backend relationship discovery | P2 | Quality reported per relationship type; unresolved marked, not guessed |
| 5 | Runtime application exploration | P4 | Budgeted sessions; action classes permitted per environment policy |
| 6 | Context reconciliation | P5 | Defect candidates only after deterministic checks |
| 7 | Functional application understanding | P2 | Rule recall known-partial |
| 8 | Context persistence and versioning | P2 | Heuristic rename detection for routes and endpoints only |
| 9 | Agentic test-scenario generation | P6 | Technique selectable only where evidence supports it |
| 10 | Coverage analysis | P6 | Reported with denominator and unverified share |
| 11 | Test-case generation | P7 | Semantic element references only |
| 12 | Test-data generation and state handling | P7 | Provisioning via approved paths, policy-gated |
| 13 | Evidence, provenance, confidence | P0, P2 | Confidence calibrated against a maintained corpus |
| 14 | Deterministic/derived/semantic inference classification | P2 | None |
| 15 | Evaluators | P10 | Deterministic first; semantic cannot overturn |
| 16 | Human-in-the-loop workflow | P11 | None |
| 17 | Autonomous workflow | P11 | Constrained by policy, confidence and evaluator verdicts |
| 18 | Playwright automation generation | P8 | Object model regenerates with the understanding |
| 19 | Locator discovery and validation | P8, P4 | Validation requires a running instance |
| 20 | Self-healing | P8 | Full detection, resolution and proposal; auto-apply ships disabled |
| 21 | Real Playwright execution | P9 | None |
| 22 | Environment handling | P9 | None |
| 23 | Secret handling | P0, P9 | References only; resolved in-worker |
| 24 | Worker execution | P0, P9 | No database credentials in workers |
| 25 | Concurrency | P9 | Minimum of three ceilings |
| 26 | Artifact storage | P0 | Retention tiered per class |
| 27 | Result evaluation and classification | P10 | Triage before defect classification |
| 28 | Findings | P10 | Evidence and lineage mandatory |
| 29 | Run history and comparison | P9, P10 | Comparison on content-derived identity |
| 30 | Excel export | P13 | Adapter behind an export contract |
| 31 | Configurable generation limits | P6 | Per workspace |

## Architecturally extensible, not necessarily populated

Multiple LLM providers (two adapters required) · external agents · MCP servers ·
external tools · enterprise advisory agents · future context sources · future integrations

## Out of scope for Phase 1

Mobile or desktop native testing · write-back of automation into the AUT repository ·
defect-tracker integration · multi-tenant SaaS operation · performance or security testing
of the AUT · non-code context ingestion such as requirements, wireframes or backlog items

## The only intentional placeholders

```text
CODE_INTELLIGENCE_PROVIDER
CRAWLER_PROVIDER
```

Provider-selection placeholders only. Their contracts are real architecture. They are not
licence to create fake functionality elsewhere.
