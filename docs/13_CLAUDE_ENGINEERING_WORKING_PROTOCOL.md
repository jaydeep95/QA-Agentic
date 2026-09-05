# 13 — Engineering Working Protocol

**Status:** ACTIVE — the operating manual for any engineer or AI agent working in this repository
**Last updated:** 2026-09-05

> This document tells you **how to work here**. It is short on purpose. Follow it exactly.

---

## 1. Before starting any work

Do all seven, in order. Do not skip to implementation.

```text
1. Read 00_PROJECT_SOURCE_OF_TRUTH.md          → what this is, where we are
2. Read 12_CURRENT_IMPLEMENTATION_STATE.md     → what actually exists today
3. Read the active phase in 05_DETAILED_DEVELOPMENT_ROADMAP.md
4. Read the active checkpoint in checkpoints/
5. Read the relevant architecture (02) and contracts (04) for what you are about to touch
6. Inspect the actual source tree and tests — code outranks documents on questions of STATE
7. Determine whether the requested task is already recorded in the roadmap
```

### If the task is not in the roadmap

**Stop.** Classify it first ([`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md) §2):

| Classification | Action |
|---|---|
| It implements an existing roadmap item | Proceed |
| It is a **flexible implementation choice** within an existing item | Proceed; note the choice in the commit |
| It is a **change proposal** — new behaviour, new scope, altered architecture | Record it as a proposal, assess impact, obtain approval. **Do not implement it** |
| It requires an **open decision** | Record the blocker, do the unblocked work, surface the question |
| It touches a **placeholder** provider | Research first ([`11`](11_RESEARCH_AND_OPEN_DECISIONS.md) §3) |

---

## 2. Before implementing a change

```text
1. Classify the change
2. Assess impact — components, contracts, data model, tests, checkpoints, security
3. Update documentation FIRST where the change-control rule requires it
4. Identify affected tests
5. Identify affected checkpoints
6. THEN implement
```

### When documentation must be updated first

If the change affects any of: product behaviour · architecture · component responsibility ·
API contract · domain model · workflow · data model · persistence · provider boundary ·
test semantics · coverage model · automation semantics · execution semantics · security
boundary · governance · traceability · phase scope.

> **Never implement first and document later** for these.
> Full rule: [`10_DECISION_AND_CHANGE_LOG.md`](10_DECISION_AND_CHANGE_LOG.md) §1.

---

## 3. After implementation

```text
1. Run the required tests
2. Inspect the results — do not assume they passed
3. Record evidence (CI run, commit hash, artifact, trace)
4. Update work item status in 05_DETAILED_DEVELOPMENT_ROADMAP.md
5. Update 12_CURRENT_IMPLEMENTATION_STATE.md
6. Update the PROJECT POSITION block if the stage changed
7. Record newly discovered risks, limitations or defects
8. Record any architectural decision made along the way in 10_DECISION_AND_CHANGE_LOG.md
```

Step 5 is mandatory. A session that implements without updating state leaves the next
session unable to answer "where are we?" — the exact failure this system prevents.

---

## 4. The ten invariants you may not violate

Listed in [`00_PROJECT_SOURCE_OF_TRUTH.md`](00_PROJECT_SOURCE_OF_TRUTH.md) §7 and enforced by architecture tests
([`09_TEST_STRATEGY.md`](09_TEST_STRATEGY.md) §2.5). Violating one requires a Decision Log entry and an
architecture update **before** any code.

Short form: UAU is central · no artifact without a UAU version · providers are replaceable ·
static and runtime are independent · mismatch is not defect · healing cannot change intent ·
coverage is obligation-based · evidence is first-class · one governance mechanism ·
workers are isolated.

---

## 5. Honesty rules

These are not style preferences. Breaking them corrupts the project's memory.

| Rule | Meaning |
|---|---|
| **Record UNKNOWN as UNKNOWN** | Use `UNKNOWN`, `REQUIRES_VALIDATION` or `OPEN_DECISION`. Never convert uncertainty into false certainty |
| **Never fabricate** | Not requirements, architecture, provider capabilities, test results, performance figures, compatibility, or implementation status |
| **A stub is not complete** | Placeholder code, fake success, simulated execution, hardcoded results and mock-only workflows may never be represented as completed functional capability |
| **Only two placeholders exist** | `CODE_INTELLIGENCE_PROVIDER` and `CRAWLER_PROVIDER`. Any additional placeholder requires an explicit Decision Log entry |
| **Documentation ≠ implementation** | A document existing makes something `DEFINED`, never `IMPLEMENTED` |
| **Do not mark PASS on a mock** | If a phase requires functional behaviour and it is mocked, the checkpoint is not PASS |
| **Report failures plainly** | If tests fail, say so and show the output. If a step was skipped, say so |

---

## 6. Status discipline

Use the status model from [`05_DETAILED_DEVELOPMENT_ROADMAP.md`](05_DETAILED_DEVELOPMENT_ROADMAP.md). Never use vague
states such as "mostly done". Each status has an objective condition:

| To claim | You must have |
|---|---|
| `IMPLEMENTED` | Code that compiles/type-checks |
| `TESTED` | Tests that exist and run |
| `VALIDATED` | A checkpoint with captured evidence |
| `COMPLETE` | Checkpoint result PASS or PASS WITH KNOWN LIMITATION |

---

## 7. Checkpoint-driven development cycle

```text
Read Source of Truth → Read Current State → Read Roadmap → Read Active Checkpoint
   → Confirm Scope → Implement → Run Tests → Validate Checkpoint
   → Capture Evidence → Update Documentation → Advance Roadmap
```

**Never skip the documentation and status update at the end.**

---

## 8. Working with the two placeholders

| Permitted | Not permitted |
|---|---|
| Define and refine the port | Selecting a provider without research |
| Build a contract-conformance fake **for tests** | Presenting a fake as working functionality |
| Implement degraded-mode behaviour for absence | Making the platform depend on the provider |
| Pass a checkpoint in degraded mode, clearly recorded | Passing a checkpoint because a mock returned success |

Research sequence before any binding: [`11_RESEARCH_AND_OPEN_DECISIONS.md`](11_RESEARCH_AND_OPEN_DECISIONS.md) §3.

---

## 9. Commit discipline

- Commit messages state **what changed and why**, referencing the work item ID (`P0-W1-T1`)
- A commit that changes behaviour and its documentation belongs together
- Never commit secrets, `.env` files, credentials, or large generated artifacts
- Do not commit or push unless asked, beyond the normal flow of an approved work item
- If on a default branch and about to make substantial changes, branch first

---

## 10. When you are unsure

In priority order:

1. Check whether an existing document already answers it — most do
2. Check whether it is an open decision ([`11`](11_RESEARCH_AND_OPEN_DECISIONS.md) §1) — if so, do not decide it alone
3. Check whether it is a flexible implementation choice — if so, choose, note it, and move on
4. If it changes architecture or product intent, record a change proposal and stop
5. Ask, with the specific question and what it blocks

**Do not resolve an open decision unilaterally.** Do the unblocked work, record the blocker,
surface the question.
