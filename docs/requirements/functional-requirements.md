# Functional Requirements

**Status:** ACTIVE · **Identifiers:** canonical in [`../08_TRACEABILITY_MATRIX.md`](../08_TRACEABILITY_MATRIX.md)

> The traceability matrix holds the authoritative requirement list with `REQ-*`
> identifiers and their coverage across contract, roadmap, implementation, test,
> checkpoint and evidence. **That table is the source of truth for requirements.**
>
> This file records requirement *groupings* and the rationale the matrix format
> cannot carry. It deliberately does not duplicate the list.

## Requirement groups

| Group | Prefix | Count | Rationale for the grouping |
|---|---|---:|---|
| Source ingestion | `REQ-SRC` | 6 | Repository identity must be exact — every evidence anchor resolves against a pinned revision |
| Unified Application Understanding | `REQ-UAU` | 12 | The central asset; most requirements here defend an invariant |
| Static understanding | `REQ-STA` | 13 | Includes the four extractor rules derived from measured failure causes |
| Runtime exploration | `REQ-EXP` | 8 | Safety and optionality dominate — exploration is an actor in the world |
| Reconciliation | `REQ-REC` | 5 | The six statuses, and the prohibition on auto-labelling defects |
| Coverage and test design | `REQ-COV`, `REQ-TEST` | 15 | Coverage must be computed; techniques must be evidence-gated |
| Automation | `REQ-AUTO` | 7 | The specification/binding split and the healing constraint |
| Execution | `REQ-EXEC` | 8 | Isolation, secrets, concurrency, artifacts |
| Evaluation and findings | `REQ-EVAL` | 7 | Layer ordering and the triage ladder |
| Governance | `REQ-GOV` | 7 | One gate; autonomy bypasses nothing |
| Providers, security, platform | `REQ-PROV`, `REQ-SEC`, `REQ-PLAT` | 15 | Replaceability and the trust boundary |

**Total: 103 requirements.** All trace to a contract or component, and to a roadmap item.
**None is implemented** — see [`../12_CURRENT_IMPLEMENTATION_STATE.md`](../12_CURRENT_IMPLEMENTATION_STATE.md).

## Requirements that exist to defend an invariant

These are not features. Without them, an invariant erodes silently and no test notices.

| Requirement | Defends |
|---|---|
| REQ-UAU-002, REQ-UAU-006 | INV-8, INV-2 — no element without evidence; no artifact without context |
| REQ-STA-006 … REQ-STA-009 | The four extractor rules — under-report rather than mis-report |
| REQ-EXP-001 | INV-4 — static-only must work |
| REQ-EXP-004 | Exploration safety — unknown action targets denied by default |
| REQ-REC-003 | INV-5 — mismatch is not defect |
| REQ-COV-003 | INV-7 — no model path can set a coverage value |
| REQ-AUTO-005 | INV-6 — healing cannot reach intent |
| REQ-EVAL-005 | A failed assertion alone is not a defect |
| REQ-GOV-001, REQ-GOV-002 | INV-9 — one gate; autonomy bypasses nothing |
| REQ-SEC-001, REQ-EXEC-002 | INV-3, INV-10 — provider independence and worker isolation |

## Requirement stability rules

- Identifiers are **stable**. Do not renumber.
- A retired requirement is marked `RETIRED` with a Decision Log reference, never deleted.
- A new requirement gets the next number in its group and must trace to a contract and a
  roadmap item in the same change.

## Known requirement gaps

Recorded rather than omitted — see the gap analysis in
[`../08_TRACEABILITY_MATRIX.md`](../08_TRACEABILITY_MATRIX.md).

| Gap | Blocked by | Must be closed before |
|---|---|---|
| UX and artifact-view requirements not itemised | — | P0-W5-T3 |
| Performance and scale requirements absent | OD-4 | P13 |
| Accessibility requirements absent | Never discussed | P13 |
| Deployment and operability requirements thin | OD-7 | P13 |
