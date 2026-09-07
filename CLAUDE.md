# CLAUDE.md — Agentic QA Automation Platform

> **This file is a router, not a source of truth.** It tells you where to look.
> It deliberately does not restate product, architecture or status — those live in
> `docs/` and duplicating them here would create a second thing to keep in sync.

---

## Read this order before doing anything

```text
1. docs/00_PROJECT_SOURCE_OF_TRUTH.md        what this is, where we are, what to read next
2. docs/12_CURRENT_IMPLEMENTATION_STATE.md   what actually exists right now  (living)
3. docs/14_ARCHITECTURAL_DECISION_REGISTER.md  what is FIXED / FLEXIBLE / PROVISIONAL /
                                               PLACEHOLDER / OPEN
4. docs/05_DETAILED_DEVELOPMENT_ROADMAP.md   the active phase only
5. docs/checkpoints/                          the active checkpoint
6. docs/02 and docs/04                        architecture and contracts, as relevant
7. The actual source and tests                code outranks documents on questions of STATE
8. docs/11_RESEARCH_AND_OPEN_DECISIONS.md    what you must NOT decide alone
```

Do not read every document blindly. Read what the active work touches.
Full protocol: `docs/13_CLAUDE_ENGINEERING_WORKING_PROTOCOL.md`.

---

## Non-negotiables

1. **The repository is the source of truth**, not any conversation. Assume the
   conversation that produced this is gone.
2. **Documentation first** for anything affecting product behaviour, architecture,
   contracts, data model, security, governance or scope. Never implement first and
   document afterwards. Use `docs/15_CHANGE_IMPACT_TEMPLATE.md`.
3. **A recommendation is not a requirement.** Check the decision register before treating
   anything as fixed. **Every numeric threshold in this repository is PROVISIONAL.**
4. **A stub is not complete.** Placeholder code, fake success, simulated execution and
   mock-only workflows may never be presented as functional capability.
5. **Exactly two production placeholders exist:** `CODE_INTELLIGENCE_PROVIDER` and
   `CRAWLER_PROVIDER`. Contracts FIXED, bindings deliberately unselected. Test doubles are
   legitimate inside tests and must never be presented as capability.
6. **Record `UNKNOWN` as `UNKNOWN`.** Never convert uncertainty into false certainty.
7. **Advance a phase only when its checkpoint demonstrates the capability** — not because
   code exists. Every checkpoint needs both a product validation and an engineering one.

## The ten invariants

UAU is central · no artifact without a UAU version · providers are replaceable · static and
runtime are independent evidence sources · mismatch is not defect · healing cannot change
intent · coverage is obligation-based · evidence is first-class · one governance mechanism ·
workers are isolated.

Detail and enforcement points: `docs/00_PROJECT_SOURCE_OF_TRUTH.md` §7.
**Violating one requires a Decision Log entry and an architecture update before any code.**

## Stop and record before implementing when

The change conflicts with a FIXED decision · the requirement is not in the roadmap · a
component boundary is unclear · a provider decision is still open · a numeric threshold has
no provenance · a data model change is required · a checkpoint would stop proving its
capability · a product invariant would change.

Do not resolve any of these by picking an interpretation.
Full rule: `docs/15_CHANGE_IMPACT_TEMPLATE.md` §5.

---

## After any meaningful task

Update `docs/12_CURRENT_IMPLEMENTATION_STATE.md` and the PROJECT POSITION block in
`docs/05_DETAILED_DEVELOPMENT_ROADMAP.md`. A stale state document is worse than none —
it causes the next session to build on a false premise.

## Repository conventions

- Commit messages reference the work item ID (`P0-W1-T2`)
- Never commit secrets, `.env` files, credentials or large generated artifacts
- Execution artifacts (traces, video, screenshots) are evidence held by reference in the
  object store — they never enter git history
- Architecture diagrams are also stored as plain-text `.mmd` under
  `docs/architecture/architecture-diagrams/` so they survive any renderer

## Where the original source material lives

`intial Application Understanding and conceptual prototype/` holds the Phase 1 UX prototype
and the earliest architecture baselines. They are **historical inputs**, not current design.
The current architecture is `docs/02_ARCHITECTURE_BASELINE.md` and
`docs/architecture/architecture-overview.html`.

## Known recoverability gap

The empirical extraction measurements cited in `docs/02_ARCHITECTURE_BASELINE.md` §6 were
produced by a harness **outside this repository**. The figures are transcribed and survive;
the ability to re-measure does not. Tracked as **OD-9**.
