# Reference AUT and Calibration Corpus

**Status:** `OPEN` — **the reference AUT has not been selected**
**Created:** 2026-09-05 (Roadmap V2)
**Blocks:** Validations A, C, D, E, F · CP-02 framework packs · calibration

> **REFERENCE AUT → OPEN (ADR-052 / OD-3).**
> This document defines *what must be recorded* once an AUT is chosen. It does **not**
> invent one. Every field below reads `UNKNOWN` until the Product Owner names the
> application.

---

## 1. Why this matters

Calibration, coverage measurement and every validation experiment compare platform output
against a **known-correct expectation**. Without a pinned reference application and a
hand-written ground truth:

- confidence cannot be calibrated — it becomes a declared number again (the exact failure recorded in [`../02_ARCHITECTURE_BASELINE.md`](../02_ARCHITECTURE_BASELINE.md) §6)
- coverage has no denominator to check against
- Validations A, C, D, E and F have no dataset
- extraction quality claims are unfalsifiable

This corpus is required for meaningful calibration of: static understanding · runtime
exploration · reconciliation · coverage · scenario generation · automation · execution ·
evaluation.

---

## 2. Reference AUT definition — to be completed

| Field | Value |
|---|---|
| Application name | **UNKNOWN** |
| Purpose / domain | **UNKNOWN** |
| Frontend repository | **UNKNOWN** |
| Backend repository | **UNKNOWN** |
| Additional repositories | **UNKNOWN** |
| Pinned revision (per repository) | **UNKNOWN** — must be a fixed SHA, not a branch |
| Frontend framework | **UNKNOWN** — determines the first framework pack (P2-W2-T2) |
| Backend framework | **UNKNOWN** |
| API style | **UNKNOWN** |
| Persistence | **UNKNOWN** |
| Authentication mechanism | **UNKNOWN** — SSO or MFA could block CP-09 and CP-06 entirely |
| Stable test attributes present? | **UNKNOWN** (OD-8) — the single highest-leverage variable in locator reliability |
| Licence permits use as a fixture | **UNKNOWN** |

> **Fixtures must be pinned.** A reference AUT that moves invalidates the ground truth and
> silently decalibrates confidence.

## 3. Expected ground truth — to be authored

Written **by reading the source before any scoring is run**, so it is independent of what
the platform produces.

| Asset | Purpose | Used by | Status |
|---|---|---|---|
| Expected screens / pages | Inventory recall | Validation A; coverage denominator | **NOT AUTHORED** |
| Expected components | Inventory recall | Validation A | **NOT AUTHORED** |
| Expected API endpoints | Inventory recall | Validation A | **NOT AUTHORED** |
| Expected data structures / tables | Schema extraction | Validation A | **NOT AUTHORED** |
| Expected fields with types, optionality, enums, constraints | Schema semantics; test data | Validations A, F | **NOT AUTHORED** |
| **Expected frontend↔backend relationships** | The weakest extraction class | **Validation A** | **NOT AUTHORED** |
| Known validations and business rules | Rule extraction recall | Validations A, F | **NOT AUTHORED** |
| Expected workflows and user journeys | Workflow discovery | Validations A, F | **NOT AUTHORED** |
| Expected states and transitions | State model; path coverage | Validations A, C | **NOT AUTHORED** |
| Permissions and roles | Role-gated behaviour | Validation C | **NOT AUTHORED** |
| Intentional edge cases | Boundary and negative design | Validation F | **NOT AUTHORED** |
| Known defects in the AUT | Distinguishing real findings from false positives | Validation D | **NOT AUTHORED** |

## 4. Derived branches — to be created

| Branch | Contains | Required by |
|---|---|---|
| **Seeded-discrepancy** | Deliberate static/runtime mismatches: a removed confirmation step, a renamed response property, an endpoint with no UI path | Validation D · CP-10 |
| **Markup-churn** | Renamed classes, restructured containers, a relocated control — realistic churn, not gratuitous | Validation E · CP-05 |
| **Second revision** | A normal forward commit | Understanding diff and staleness · CP-02 |

## 5. Environment and data — to be established

| Item | Value | Status |
|---|---|---|
| Deployed environment URL | **UNKNOWN** | Blocks CP-06, CP-09 |
| Environment data-safety class | **UNKNOWN** | Blocks OD-1 |
| **State reset method** | **UNKNOWN** | Required before write-enabled exploration |
| Test accounts per role | **UNKNOWN** | Required for CP-09 and CP-06 |
| Approved test-data source | **UNKNOWN** | Rung 2 of the resolution ladder |
| Seeded application state | **UNKNOWN** | Many scenarios presume existing entities |
| Domain allowlist | **UNKNOWN** | Required by exploration and execution policy |

## 6. Action-risk control set — to be labelled

Validation C requires a **hand-labelled** set of AUT actions with known risk classes
(`read`, `benign write`, `destructive`, `external effect`, `unknown`), used to measure
destructive-action classification recall (THR-SAFETY-001).

**Status: NOT AUTHORED.** Without it, write-enabled exploration cannot be safely enabled.

## 7. Existing prior art — outside this repository

An evaluation harness at `c:\waynautic\skill-test\` produced the measured extraction
figures cited in [`../02_ARCHITECTURE_BASELINE.md`](../02_ARCHITECTURE_BASELINE.md) §6, using a hand-written ground truth on a
public full-stack TypeScript application.

| Fact | Detail |
|---|---|
| Location | Outside this repository, outside its version control |
| What survives its loss | The **figures** — transcribed into doc 02 §6 and the register |
| What does **not** survive | The **ability to re-measure** |
| Tracked as | **OD-9** — whether to vendor, rewrite or reference it |

> That application is **not** confirmed as the Phase 1 reference AUT. Its measurements are
> a design baseline from one codebase and **do not transfer to a different stack**.

## 8. What to do when the AUT is selected

```text
1. Complete §2 with real values, including pinned revisions
2. Author the ground truth in §3 BEFORE running any extraction against it
3. Create the derived branches in §4
4. Establish the environment and data in §5
5. Author the action-risk control set in §6
6. Record ADR-052 / OD-3 as resolved in the Decision Log and register
7. Update the roadmap: P2-W2-T2 (first framework pack) becomes unblocked
8. Run Validation A, then C
```

Step 2 before step 3 matters: a ground truth authored after seeing platform output is not a
ground truth.
