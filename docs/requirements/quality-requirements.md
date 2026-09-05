# Quality Requirements

**Status:** ACTIVE · **Derived from:** [`../02_ARCHITECTURE_BASELINE.md`](../02_ARCHITECTURE_BASELINE.md)

Each attribute states the architectural consequence it forces and how it is measured.

> **An attribute with no measurement is an aspiration, not a requirement.**
> Where a target is `UNKNOWN`, that is recorded honestly rather than invented.

| Attribute | Architectural consequence | Measured by | Target | Status |
|---|---|---|---|---|
| **Correctness** | Extractors individually evaluable against ground truth; provenance identifies the responsible extractor | Per-extractor precision and recall by element type, never averaged | Relationship precision ≥ 0.85 (Validation A) | REQUIRES_VALIDATION |
| **Evidence and provenance** | Evidence is a first-class entity; no element without a reference | Share of elements with resolvable anchors; share still resolving after N commits | 100% / TBD | NOT_MEASURED |
| **Explainability** | Reasoning, evidence and verdicts travel as data, not prose | Interactions needed to reach source evidence from a review item | ≤ 2 | NOT_MEASURED |
| **Traceability** | Typed lineage on every artifact version | Any finding resolves to a repository revision in one query; zero orphans | 100% | NOT_MEASURED |
| **Determinism where required** | Extraction, matching, coverage arithmetic and triage are deterministic | Share of artifacts produced deterministically; model spend per run | Maximise; spend bounded | NOT_MEASURED |
| **Modularity** | Bounded domains with explicit contracts; no provider type in domain logic | Static check for provider imports in domain modules | Zero | NOT_MEASURED |
| **Extensibility** | Techniques, evaluators, extractors and providers are registered strategies | A new technique or evaluator ships with no orchestration change | Demonstrated by test | NOT_MEASURED |
| **Scalability** | Worker fleets scale independently of the control plane | Execution throughput at fixed control-plane resources | **UNKNOWN** — blocked on OD-4 | NOT_DEFINED |
| **Reliability** | Durable workflow state; idempotent stages; partial results are valid results | Run resumption success after induced worker termination | 100% at every stage boundary | NOT_MEASURED |
| **Security** | One egress point; secret references; untrusted-content boundary; workspace scoping | Secrets found in artifacts, logs or prompts; egress attempts outside the invoker | Zero / zero | NOT_MEASURED |
| **Isolation** | Process and container isolation; workers hold no database credentials | Blast-radius test: kill or poison a worker | Core and store unaffected | NOT_MEASURED |
| **Observability** | Structured, correlated telemetry per stage, invocation and model call | Ability to reconstruct a run decision path from telemetry alone | Demonstrated | NOT_MEASURED |
| **Auditability** | Append-only decision log; immutable approved versions | Every state transition attributable to an actor and a policy version | 100% | NOT_MEASURED |
| **Cost efficiency** | Budgets are first-class: token, time, action and exploration ceilings | Cost per understanding build and per scenario set | **UNKNOWN** — no ceiling set | NOT_DEFINED |
| **Performance** | Read models for interface queries; long work asynchronous with progress | p95 API latency for artifact views; time to first reviewable item | **UNKNOWN** | NOT_DEFINED |
| **Recoverability** | Canonical store is the only backup-critical component; projections rebuildable | Verified rebuild of all projections; restore drill time | Demonstrated | NOT_MEASURED |
| **Portability** | Containerised; no managed-service-specific primitives in the core | Full stack runs locally and in the target environment unchanged | Demonstrated | NOT_MEASURED |
| **Provider independence** | Capability contracts with conformance tests; two LLM adapters from day one | Conformance suite passes per adapter; provider swap requires no domain change | 100% | NOT_MEASURED |

## Attribute conflicts — named, not hidden

| Conflict | How the architecture responds |
|---|---|
| **Determinism vs coverage of understanding** | Deterministic extraction under-reports; semantic inference over-reports. Resolved by *labelling* rather than choosing: both are admitted as understanding, tagged with inference type, and weighted differently in confidence and in what they are permitted to gate |
| **Cost vs exhaustiveness** | Very high coverage and a bounded token budget pull against each other. The obligation ledger makes the trade explicit — uncovered obligations are reported rather than silently skipped. The tension is real and permanent |
| **Isolation vs latency** | Workers that cannot reach the database must post results through the API, costing a round trip and forcing batching. Accepted deliberately (INV-10) |

## Attributes not yet definable

The `UNKNOWN` targets above are blocked on OD-4 (delivery parameters) and OD-7 (hosting
and tenancy). They **must** be defined before Phase 13 hardening, and P13 cannot pass its
checkpoint while a quality attribute it claims to harden has no target.
