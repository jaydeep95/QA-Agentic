# Research — CRAWLER_PROVIDER

**Status:** `OPEN` — provider not selected
**Final decision:** `UNDECIDED`
**Decision gate:** OD-1 (write policy) + Validation C (state stability and safety)
**Last updated:** 2026-09-05

> **Do not silently finalise this provider.** The research-first sequence in
> [`../../11_RESEARCH_AND_OPEN_DECISIONS.md`](../../11_RESEARCH_AND_OPEN_DECISIONS.md) §3 is mandatory before any binding.

---

## 1. Problem statement

Static analysis cannot resolve everything. Four measured failure causes are **statically
unresolvable by construction** — most notably services injected as props through type-only
imports, which vanish at runtime and produce no relationship at all.

**One observed network request from a known page settles what static analysis could not.**

Runtime exploration exists to validate, correct and extend the understanding, and to
validate locator candidates against a real DOM. The question this research answers:
**what drives the browser, and under what safety controls?**

---

## 2. Current architectural boundary

The provider is a **browser actuator and sensor**. Everything constituting knowledge or
judgement stays with the platform.

| Provider owns | Platform owns |
|---|---|
| Session establishment; authentication strategy given secret references | Which environment, which identity, whether a session is permitted at all |
| Navigation; affordance enumeration; action execution | **Which** action, in what order, and whether it is allowed |
| DOM, accessibility tree, screenshot, network, console, validation messages, resulting URL | State identity; the state graph; deriving understanding; confidence |
| Action outcome reporting including failure and unexpected navigation | Interpretation; blocker detection; difference classification; continuation |
| Locator probing: exists, unique, visible, interactable | Locator lifecycle, validation history, healing decisions |

Contract: [`../../04_DOMAIN_AND_COMPONENT_CONTRACTS.md`](../../04_DOMAIN_AND_COMPONENT_CONTRACTS.md) §4.

> **Disqualifying criterion:** if a candidate provider demands ownership of the state
> model, planning or interpretation, it fails the contract regardless of other merits.

---

## 3. Capabilities required

| # | Requirement |
|---|---|
| C1 | Drive a real browser session against a configured environment |
| C2 | Apply an authentication strategy from secret **references**, never values |
| C3 | Enumerate interactive affordances on the current page |
| C4 | Execute a single requested action and report its outcome |
| C5 | Capture DOM, accessibility tree, screenshot, network exchanges, console output |
| C6 | Probe a candidate locator and report existence, uniqueness, visibility, interactability |
| C7 | Respect a domain allowlist enforced at the network layer |
| C8 | Report failure without crashing the session |
| C9 | Impose no requirement that the platform surrender planning, state identity, safety classification, reconciliation or interpretation |

---

## 4. The hard design problems

These belong to the **platform**, not the provider, but the provider choice affects how
well they can be solved.

### 4.1 State identity — the hardest problem in this workstream

URL alone is insufficient in both directions: two records under one parameterised route are
the same state; the same URL before and after a modal opens is not.

Composite identity under a per-application policy:

| Signal | Contributes | Failure mode alone |
|---|---|---|
| Route template | Anchors the observation to a static element, enabling reconciliation | Collapses distinct sub-states such as modal or wizard step |
| Structural fingerprint | Hash of the interactive skeleton — roles, test ids, labels, nesting — text and volatile ids stripped | Sensitive to incidental markup churn; over-splits on list length |
| Affordance set | The available actions — what exploration actually cares about | Merges visually different pages offering the same actions |
| Actor context | Authenticated role and tenant | Role-gated behaviour becomes invisible |

Classification: `same` · `parameterised variant` · `changed` · `new`. Variants collapse for
exploration but persist as data-variation evidence feeding equivalence partitioning.

### 4.2 Exploration safety

Because the understanding already knows which endpoint a control invokes and with which
verb, **risk is classified before the action occurs**.

| Class | Examples | Default policy |
|---|---|---|
| `read` | Navigation, queries | Permitted |
| `benign write` | Creates and updates within the workspace | Per environment policy |
| `destructive` | Deletes, cancellations, irreversible transitions | Denied unless explicitly permitted |
| `external effect` | Payments, email, third-party calls | Denied |
| `unknown` | Target undeterminable | **Denied by default** — becomes an agenda item requiring escalation, never a gamble |

### 4.3 State explosion control
Variant collapse · revisit caps · agenda-value frontier prioritisation · hard budgets on
actions, wall-clock and artifact volume. **Partial exploration is a valid result.**

### 4.4 Blockers
A durable, attributable, resumable work item stating what is required, why, which strategies
were attempted and why each was insufficient. Supplied values are classified on arrival —
secret, personal data, test data or entity reference — and a value classified secret is
stored as a reference and never enters an artifact, log or prompt.

---

## 5. Candidate approaches

| Candidate | Notes | Status |
|---|---|---|
| **Bespoke explorer over the browser automation foundation** | Full control of the loop; the platform already needs Playwright for execution, so no new runtime dependency. Highest implementation effort | CANDIDATE |
| **State-exploration crawler approaches** (Crawljax-class) | Mature state-abstraction and event-flow-graph heuristics worth mining. Adoption as a production dependency is a separate question from learning from the approach | STUDY, NOT ADOPT BY DEFAULT |
| **Browser-agent frameworks** | Handle novelty well; non-reproducible, costly per step, hard to bound, unpredictable safety profile | REJECTED as the primary loop; possible for narrow underdetermined decisions |

**Control model recommendation: hybrid.** Deterministic where the agenda predicts the next
action; agentic only where genuinely underdetermined. **An LLM should not choose every click.**

---

## 6. Evaluation criteria

| Criterion | Threshold |
|---|---|
| State-count stability across three identical read-only sessions | Variance < 10% |
| Agenda coverage — statically known screens reached | ≥ 60% |
| **Destructive-action classification recall** | **1.00 — no missed destructive action is acceptable** |
| Locator probe reliability | Consistent results across repeat probes |
| Blocker frequency and quality | Blockers explain themselves; no silent failures |
| Replaceability | A second implementation binds with no change to state model or planning |

---

## 7. Implications

| Dimension | Assessment |
|---|---|
| **Security** | Highest boundary in the platform. Holds AUT credentials as references; bound by environment and domain allowlists; page content is untrusted input |
| **Performance** | Long-lived sessions, high artifact volume; drives retention tiering |
| **Integration** | One adapter behind the port; the platform owns the loop |
| **Blast radius** | Exceeds test execution because exploration is broad rather than scenario-scoped — this is why OD-1 gates the entire phase |
| **Licensing/deployment** | Depends on candidate |

---

## 8. Empirical validation

**Validation C** — see [`../../07_CHECKPOINT_AND_VALIDATION_PLAN.md`](../../07_CHECKPOINT_AND_VALIDATION_PLAN.md) §5.

Result: **NOT RUN** — blocked on OD-1 (write policy) and OD-3 (AUT and environment).

> **Any missed destructive action blocks write-enabled exploration entirely.**
> Read-only exploration may proceed on a lower bar, but it cannot observe state
> transitions — which removes reconciliation's strongest evidence class.

---

## 9. Decision record

| Field | Value |
|---|---|
| Recommendation | Hybrid control model behind `CrawlerProvider`; candidate undecided |
| Final decision | **UNDECIDED** |
| Decided by | — |
| Date | — |
| Migration impact | Bind adapter → run conformance suite → run Validation C → update Decision Log → update roadmap Phase 4 |

When decided, follow the seven-step sequence in
[`../../11_RESEARCH_AND_OPEN_DECISIONS.md`](../../11_RESEARCH_AND_OPEN_DECISIONS.md) §3 before implementing.
