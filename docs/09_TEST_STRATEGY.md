# 09 — Test Strategy

**Status:** ACTIVE — canonical for how the platform itself is tested
**Last updated:** 2026-09-05

---

## 1. The distinction that must never blur

| | Testing **the QA platform** | The QA platform **testing an AUT** |
|---|---|---|
| Subject | Our own code | A customer application |
| Who writes the tests | Us | The platform, from the UAU |
| Where they live | This repository | Generated artifacts, stored per workspace |
| When they run | CI, on every commit | On execution request |
| What failure means | Our platform is broken | A finding about the AUT (after triage) |

**Both are required.** This document covers the first. The second is a product capability
specified in [`01_PRODUCT_VISION_AND_SCOPE.md`](01_PRODUCT_VISION_AND_SCOPE.md) §6–§10.

> A generated test passing tells us nothing about whether our platform works.
> A platform test passing tells us nothing about the AUT.

---

## 2. Test layers

### 2.1 Unit tests
**Subject:** domain logic and deterministic components in isolation.

| Target | Examples |
|---|---|
| Identity and normalisation | Content-derived identity is deterministic and stable |
| Coverage arithmetic | Obligation states compute correctly from links |
| Confidence function | Given fixed inputs, produces a fixed score and band |
| Triage ladder | Each rung classifies correctly and in order |
| Reconciliation set algebra | All six statuses produced from constructed populations |
| Policy evaluation | Gate decisions for a given package and policy version |
| Locator ranking | Strategy stability ordering |

**Rules:** no I/O, no network, no model calls. Deterministic components must be tested
deterministically — this is most of the platform by design (PP-4).

### 2.2 Contract tests
**Subject:** the boundaries in [`04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](04_DOMAIN_AND_COMPONENT_CONTRACTS.md).

Each contract has a **conformance suite**. This is the mechanism that turns
"replaceable" from a claim into a property.

| Contract | The suite must prove |
|---|---|
| `CodeIntelligenceProvider` | Platform builds understanding with the adapter removed |
| `CrawlerProvider` | A second implementation drives exploration with no change to state model or planning |
| `LanguageModelProvider` | A real adapter honours the typed output contract; provenance records which was used. Cross-adapter equivalence is tested **when a second adapter exists** (ADR-030) |
| `Capability` | A new tool registers and invokes with no stage-level code |
| `EvaluatorContract` | A new evaluator is added with no orchestration change |
| `AutomationSpecification`/`Binding` | One specification executes against two environments; the healing path cannot reach the specification |
| `UnderstandingContribution` | A new evidence source contributes and joins reconciliation with no schema change |
| `ScenarioContract` | A ninth technique is registered with no orchestration change |

**Adapter acceptance is gated on its conformance suite passing.**

### 2.3 Integration tests
**Subject:** components against real infrastructure.

| Area | Coverage |
|---|---|
| Persistence | Migrations, transactional writes, workspace scoping cannot be bypassed |
| Artifact storage | Round-trip, retention tiering, reference resolution |
| Queue and workers | Enqueue → lease → execute → callback → record; idempotency; cancellation; resumption after induced kill |
| Worker isolation | Worker cannot open a database connection (negative test) |
| Provider invocation | Timeout, retry, circuit breaker, budget enforcement, redaction, provenance recording |
| Secrets | Reference resolution in-worker; scan proves absence from logs and artifacts |
| Extraction pipeline | Real repository → understanding version with evidence |

### 2.4 End-to-end tests
**Subject:** complete platform workflows.

**Two paths must both pass** (INV-4):

| Path | Coverage |
|---|---|
| **Static + exploration** | Source → understanding → exploration → reconciliation → coverage → scenarios → cases → automation → execution → evaluation → findings |
| **Static-only** | The same chain with exploration disabled; must complete and produce output at reduced confidence |

Both run against a controlled reference AUT (see §4).

### 2.5 Architecture tests
**Subject:** invariants that would otherwise erode silently.

| ID | Test |
|---|---|
| ARCH-1 | Zero provider SDK imports in domain modules (INV-3) |
| ARCH-2 | Module dependency graph matches the declared domain boundaries |
| ARCH-3 | No artifact type permits a null UAU version (INV-2) |
| ARCH-4 | No public path allows setting a coverage value (INV-7) |
| ARCH-5 | Healing components have no write access to specification storage (INV-6) |
| ARCH-6 | Exactly one gate implementation exists (INV-9) |
| ARCH-7 | No understanding element can persist without an evidence reference (INV-8) |

These run in CI on every commit. An architecture test failure blocks merge.

---

## 3. Testing non-deterministic components

Model-backed components cannot be tested by asserting exact output. Strategy:

| Technique | Application |
|---|---|
| **Recorded interactions** | Model requests/responses recorded once and replayed in CI, so agentic workflows are regression-testable without spend or variance |
| **Structural assertions** | Assert the shape and contract of output (required fields, valid references, obligation links present), not its prose |
| **Property assertions** | Every generated scenario links to ≥1 obligation; every test case references only existing UAU elements; no case contains a literal locator |
| **Golden corpus** | A fixed reference repository with known-correct expectations, used for calibration and regression |
| **Evaluator agreement** | Deterministic evaluators must pass on all generated output — a generation that fails a deterministic check is a bug regardless of how good the prose looks |

> The platform's own quality machinery is reused as its test oracle: if a deterministic
> evaluator rejects generated output, the test fails.

---

## 4. Test data and reference AUT

| Need | Approach | Status |
|---|---|---|
| Reference AUT for extraction tests | A real, open, full-stack application pinned at a fixed revision | **OPEN — OD-3.** A representative application was used for the prior measurements; whether it becomes the reference AUT is unconfirmed |
| Ground-truth corpus | Hand-written expected extraction results, used for calibration | Must be created in P2-W7-T1 |
| Seeded discrepancy branch | A variant with deliberate static/runtime mismatches | Required for Validation D |
| Markup-churn branch | A variant with renamed classes and restructured containers | Required for Validation E |
| AUT environment | A deployed instance for exploration and execution | **OPEN — OD-1** (write policy), OD-3 |

**Fixtures must be pinned.** A reference AUT that moves invalidates the ground truth and
silently decalibrates confidence.

---

## 5. What is not acceptable

| Anti-pattern | Why it fails |
|---|---|
| A checkpoint passing because a mock returned success | Violates the advancement rule ([`06`](06_IMPLEMENTATION_PHASES.md) §4) |
| Asserting on model prose | Non-deterministic and meaningless |
| Testing only the happy path of an agentic workflow | Partial results and failures are the common case |
| Integration tests against a live third-party provider in CI | Non-deterministic, costly, and an egress event |
| Skipping the static-only end-to-end path | INV-4 erodes immediately without a test defending it |
| Treating a passing generated test as evidence the platform works | Confuses the two subjects in §1 |

---

## 6. CI requirements

Every commit runs: lint · type-check · unit · contract · architecture tests.
Integration and end-to-end run on merge to the main branch and before any checkpoint.

**Merge is blocked by:** any failing test, any architecture-test violation, any secret
detected by the scanner, any drop below the coverage floor for deterministic components.

Checkpoint evidence must reference a **specific CI run and commit hash**
([`07`](07_CHECKPOINT_AND_VALIDATION_PLAN.md) §6).
