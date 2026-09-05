# UX and Product Behaviour Requirements

**Status:** ACTIVE · **Created:** 2026-09-05 (Roadmap V2)
**Derived from:** the Phase 1 UX prototype, as product behaviour — **not** as production design
**Controls:** what the QA user must be able to do and see

> **Capture the product behaviour, not the prototype's technical shortcuts.**
> The prototype established the interaction model. Its implementation was a demonstration
> and is explicitly not the target design — see §9.

---

## 1. Requirement identifiers

`REQ-UX-nnn`. Traced in [`../08_TRACEABILITY_MATRIX.md`](../08_TRACEABILITY_MATRIX.md) like any other requirement.

---

## 2. Workspace and onboarding

| ID | Requirement |
|---|---|
| REQ-UX-001 | A user can create a workspace and name the project |
| REQ-UX-002 | A user can register a repository with a label, Git URL, **branch/ref** and a **credential reference** |
| REQ-UX-003 | Multiple repositories are registered per workspace, each with a role (frontend / backend / other) |
| REQ-UX-004 | The workspace displays its **source of truth**: which repositories, at which resolved revisions |
| REQ-UX-005 | The user can see when the source was last acquired and at what revision |

> **Gap closed from the prototype:** its registration modal captured only a label and a URL.
> Ref and credential reference are mandatory (REQ-SRC-001) and must appear in the interface.

## 3. Workflow navigation

| ID | Requirement |
|---|---|
| REQ-UX-010 | A persistent workflow indicator shows the stages and which the user is in |
| REQ-UX-011 | Stage completion state is visible, not only the active stage |
| REQ-UX-012 | The user can navigate directly to any **durable artifact view**: Understanding, Scenarios, Test Cases, Test Data, Automation, Runs, Findings, Capabilities |
| REQ-UX-013 | Artifacts remain reachable after reload, navigation and session end |

> **Correction from the prototype:** it stored generated work inside a chat transcript, so
> nothing survived reload. Artifacts live in their own views; the transcript is a command
> and explanation surface, never storage.

## 4. Understanding review

| ID | Requirement |
|---|---|
| REQ-UX-020 | The user can read the application understanding as a document in business language |
| REQ-UX-021 | The user can inspect any element and reach its supporting evidence at a source location |
| REQ-UX-022 | Each element displays its inference type, oracle status (where applicable), reconciliation status and confidence — as **five distinct** indicators |
| REQ-UX-023 | Elements marked `unresolved` or `unverified` are visibly distinguished from confirmed ones |
| REQ-UX-024 | The user can review understanding **per item**, not only approve it wholesale |
| REQ-UX-025 | The user can view a diff between two understanding versions |
| REQ-UX-026 | The user can see which downstream artifacts a change made stale |

> **Correction from the prototype:** the understanding was four numbers and one bulk
> "Confirm understanding" button. It is the root of all lineage and needs per-item review.

## 5. Evidence and confidence presentation

| ID | Requirement |
|---|---|
| REQ-UX-030 | Confidence is displayed as one of three bands plus a numeric score |
| REQ-UX-031 | Evidence is presented as **inspectable, navigable references** — not prose labels |
| REQ-UX-032 | An evidence chain shows each hop and whether that hop is deterministic, derived or semantic |
| REQ-UX-033 | The user can see which evaluator checks ran on an artifact and their verdicts |
| REQ-UX-034 | Items are groupable by confidence band, with lower-confidence items surfaced by default |
| REQ-UX-035 | Where evidence is insufficient, the interface says so explicitly rather than showing a low number |

## 6. Review, approval and generation

| ID | Requirement |
|---|---|
| REQ-UX-040 | The user can review generated items with reasoning, evidence, confidence and traceability visible |
| REQ-UX-041 | The user can edit an item; the machine original is retained and confidence re-derived |
| REQ-UX-042 | The user can reject an item **with a recorded reason** |
| REQ-UX-043 | The user can approve a stage; approved versions become immutable and visibly frozen |
| REQ-UX-044 | The user can add a free-text note at approval time that steers the next stage |
| REQ-UX-045 | The user can request additional generation incrementally without discarding existing approved items |
| REQ-UX-046 | The user can configure generation limits, concurrency, environment and human/autonomous mode |
| REQ-UX-047 | Long-running operations show progress and can be cancelled |

> **Gaps closed:** the prototype had no reason capture on rejection, no execution
> governance controls at all, and simulated sub-10-second operations. Real stages take
> minutes and must be observable and interruptible.

## 7. Artifacts, execution and findings

| ID | Requirement |
|---|---|
| REQ-UX-050 | Scenarios display technique(s), rationale, obligation links, evidence and confidence |
| REQ-UX-051 | Test cases display steps, semantic element references, expected results and **declared data requirements** |
| REQ-UX-052 | Test data is viewable as a first-class artifact, not only inline in steps |
| REQ-UX-053 | Coverage is displayed with denominator, understanding version and unverified share — **never a bare percentage** |
| REQ-UX-054 | Automation displays the specification and binding **separately** |
| REQ-UX-055 | Execution shows per-test status, and artifacts (trace, video, screenshots, logs) are reachable |
| REQ-UX-056 | Findings display classification by cause, evidence, and lineage to case, scenario and understanding version |
| REQ-UX-057 | Items never executed are visibly distinguished from executed results and excluded from pass rates |
| REQ-UX-058 | Run history supports run-over-run comparison with change indicators |
| REQ-UX-059 | Scenarios, test cases and test data are exportable to `.xlsx` |
| REQ-UX-060 | Blockers and escalations appear in one queue with what is needed, why, what was attempted and why it failed |

## 8. Capabilities and conversation

| ID | Requirement |
|---|---|
| REQ-UX-070 | The user can see the capabilities the platform offers, with category and description |
| REQ-UX-071 | The capability list reflects the **actual registry**, not a hardcoded list |
| REQ-UX-072 | The user can invoke a capability from the interface |
| REQ-UX-073 | A conversational surface accepts requests and explains what the platform did |
| REQ-UX-074 | Model attribution is visible on generated content |
| REQ-UX-075 | The conversational surface can answer questions over stored artifacts (findings, history, capabilities) |

## 9. Prototype shortcuts that must NOT be carried forward

The prototype was a demonstration. These are explicitly excluded from the production design:

| Shortcut | Why excluded | Correct approach |
|---|---|---|
| **In-memory state** | Nothing survived reload | Durable, versioned artifacts (REQ-UX-013) |
| **Hardcoded QA items** | The data was fictional | Generated from the UAU with real lineage |
| **Simulated execution** | "completed 6.8s" was a timer | Real Playwright execution (REQ-EXEC-001) |
| **Hardcoded confidence** | Scores were hand-authored; one card was a literal 94% | Computed from calibrated inputs (REQ-UAU-008) |
| **Inherited confidence across stages** | Test cases reused scenario scores verbatim | Each artifact carries its own computed confidence |
| **Raw unsanitised HTML rendering** | Generated strings injected via `innerHTML`; those strings originate from client source code | All generated content rendered as text (REQ-SEC-003) |
| **Keyword routing as the interaction mechanism** | Free text matched by substring | A real intent mechanism; keyword matching is not a production design |
| **Hardcoded coverage (73%)** | A literal, not a measurement | Computed from the obligation ledger (REQ-COV-002) |
| **Array-index item identity** | Run comparison broke on regeneration | Content-derived identity (REQ-UAU-004) |
| **Bulk-only understanding approval** | The root artifact was least reviewable | Per-item review (REQ-UX-024) |
| **Non-executed items in pass rates** | Corrupted every trend | Assessments excluded (REQ-UX-057) |

## 10. What is NOT specified here

| Area | Status |
|---|---|
| Visual design system, component library | **OPEN** — no design system confirmed |
| Accessibility requirements | **UNKNOWN** — never discussed. Must be defined before CP-12 |
| Multi-user collaboration (concurrent reviewers, assignment, comments) | **OPEN** — single-user assumed |
| Responsive/mobile behaviour | **OPEN** |
| Localisation | **OPEN** |

These are recorded as gaps rather than invented.
