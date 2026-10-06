---
name: map-architecture
description: >-
  Role T (technical). Fixes the foundation of Deja — the architecture map every later skill reads.
  Greenfield (the repo has no code yet): walks the stack candidates from CLAUDE.md and the draft
  plan with the user, confirms or replaces each, writes docs/architecture-map.md as the target
  foundation plus foundational ADRs in docs/adr/, states the technical dependencies between the
  product capabilities (the only constraint the roadmap accepts), and emits a scaffold tasks.json
  for implement-tasks. Brownfield (code exists): scans once and refreshes the map. Triggers on
  "/map-architecture", "map the architecture", "fix the foundation", "set up the project",
  "карта архітектури", "заклади фундамент", "зафіксуй стек". Output: docs/architecture-map.md
  (+ docs/adr/NNNN-*.md + docs/features/_scaffold/tasks.json on greenfield). Never commits.
---

# Skill: map-architecture (SDLC stage 00 — the foundation)

Produces `docs/architecture-map.md` — the single source of "how the system is built" that `roadmap` (dependencies), `write-prd` (constraints), `architecture-design`, `generate-data-model` and `implement-tasks` read instead of re-discovering it. Two modes, auto-detected:

- **Greenfield** (no source under `backend/`, `extension/`, `deploy/`) → a foundation session: confirm or replace each candidate decision with the user, fix the result as the foundation + foundational ADRs, state capability dependencies, emit a scaffold `tasks.json`. Detail → [`./references/foundation.md`](./references/foundation.md).
- **Brownfield** (source exists) → scan once and persist the current architecture.

Adapted from the course toolkit (`agentic-engineering-course/sdlc/plugin/skills/map-architecture`). Changes for Deja: the skill is owned by role **T** and never asks "what to build" (that is the brief); the stack is not picked from menus but confirmed or replaced from the candidate table in `references/foundation.md` (the T parts of the draft plan); a new **Capability dependencies** section feeds `roadmap`, which runs after this skill; the open questions of `docs/idea-brief.md` §14 that are due at `/map-architecture` are answered here; no `explorer` agent (the built-in `Explore` agent scans); scaffold tasks have a local check command and a CI task only when `CLAUDE.md` `[assume:remote=none]` no longer holds; the skill proposes a commit and never runs it.

## Owner

Role **T** (technical). P supplies input only: `docs/idea-brief.md` (capabilities, «Фундамент і експлуатація», §14 open questions, §5 out of scope). Every `AskUserQuestion` starts with «T:» per [`../_shared/ask-style.md`](../_shared/ask-style.md) → Role. The skill never re-opens scope; if a candidate decision needs a P answer, it says so and stops at that point.

## Inputs

- `docs/idea-brief.md` — **required on greenfield**. Read «Складові продукту» (the capabilities to connect), «Фундамент і експлуатація» (the T work), §5 (what is out of scope, so the foundation does not pre-build it), §10 (risks the foundation must leave room for), §14 (open questions due at `/map-architecture`). Missing → stop: «run `/interview product` first».
- [`./references/foundation.md`](./references/foundation.md) → the decision table — the candidate stack. Candidates, not rules.
- `CLAUDE.md` → Assumptions — `[assume:…]` tags the scaffold branches on.
- `docs/initial-idea/second-brain-plan.md` — a draft. Its T parts (epic 0 skeleton and invariants, epic 5 queue, epic 8 operations, the per-source extraction notes) are candidates. Its order and scope are P's business; ignore them here.
- `docs/CONTEXT.md` — the domain terms the map must use (record, source, idea, chunk, save reason, anchor, capture, trigger).
- (Brownfield) the code, plus any authored architecture doc or existing ADRs — reconciled, never overwritten.
- (Optional) `docs/architecture-map.md` if it exists — freshness check.

## Language

`docs/architecture-map.md`, ADRs and `tasks.json` are **English** (technical docs, per `CLAUDE.md`). Questions to the user are Ukrainian with the «T:» prefix, glossed per ask-style. Domain terms from `docs/CONTEXT.md` stay in English.

## Protocol

1. **Detect mode + freshness.** If `docs/architecture-map.md` exists and its `reflects_commit` ≈ current HEAD → «T: карта свіжа (reflects `<commit>`). Використати чи оновити?»; STOP on reuse. Else: **brownfield** if `backend/`, `extension/` or `deploy/` contain source; otherwise **greenfield**.

### Greenfield path → [`./references/foundation.md`](./references/foundation.md)

G1. **Read the inputs** listed above. Build the candidate list: one row per foundation decision (see the table in the reference), each with the candidate from `CLAUDE.md` / the plan, or «no candidate» if neither names one.
G2. **Calibrate.** One `AskUserQuestion`: defaults-and-confirm / walk each choice with explanations / terse control. Sets depth and phrasing, not the set of decisions. Default: walk each choice (the user knows general backend work, not this stack).
G3. **Walk the decisions.** At the calibrated depth, for each foundation decision: the candidate, 1–2 real alternatives, the trade-off in plain words, a recommendation. The user confirms, replaces, or defers (deferred → listed in the map under «Open», not decided silently). Decisions that are a rewrite to change later (stack, module style, persistence and queue, embedding model and dimension, record/chunk invariants) get an ADR each.
G4. **Answer the §14 questions due here.** Read `docs/idea-brief.md` §14; every item with «термін: `/map-architecture`» is asked as a T question and the answer goes into the map (and an ADR if it is irreversible). Items that turn out to be P questions are returned to P with one line why.
G5. **Capability dependencies.** For every slug in «Складові продукту» propose what it technically needs first: nothing, a foundation piece, or another capability. One `AskUserQuestion` to confirm the list. This is the only thing `roadmap` takes as a constraint, so keep it to real technical needs («telegram-post needs background-indexing because a post is fetched and parsed outside the request») — never a preference about order.
G6. **Fix the foundation.** Write `docs/architecture-map.md` from [`./templates/architecture-map.md`](./templates/architecture-map.md) with `mode: greenfield-bootstrap`: target C4, module layout, conventions, datastores, capability dependencies, constraints, open items. Spawn one ADR per irreversible pick in `docs/adr/NNNN-<title>.md` from [`./templates/adr-template.md`](./templates/adr-template.md) (`NNNN` = count of existing files + 1, zero-padded; title names the decision, not the problem). Record `reflects_commit`. **Validate the C4 Mermaid per [`../_shared/mermaid-check.md`](../_shared/mermaid-check.md).**
G7. **Emit the scaffold.** Write `docs/features/_scaffold/tasks.json` per the contract in the reference: structure + entry point, test harness + smoke test, migration tool + empty migration, local check command, `CLAUDE.md` update (add a short «Stack» section pointing at the map, fill «Commands»). Each task's DoD is the skeleton smoke test.
G8. **Propose commit + handoff.** Print the proposed message `map-architecture: greenfield foundation + scaffold plan`. Do not run `git commit`. Then emit the stage-handoff block per [`../_shared/handoff.md`](../_shared/handoff.md) — *What I did* + *Review* (`docs/architecture-map.md`, each `docs/adr/NNNN-*.md`, `docs/features/_scaffold/tasks.json`) + *Run next*: `/roadmap` (role P orders the capabilities with the dependencies from G5), then `/implement-tasks _scaffold`. Name the skills not yet in `.claude/skills/`.

### Brownfield path (code exists)

B2. **Read authored docs first.** `CLAUDE.md`, existing ADRs, the previous map → authoritative input; reconcile, never overwrite.
B3. **Scan.** Dispatch the `Explore` agent (read-only): language + frameworks + versions; module layout and layers; wiring conventions; datastores and access; cross-cutting conventions (errors, IDs, tests, migrations) with one cited example each; 2–3 representative features as precedents; for `extension/` — the UI structure and a representative screen to reuse.
B4. **Synthesize + stamp + validate + write.** Fill the template (`mode: current`) with real `file:line` anchors, keep the capability-dependency section current, record `updated_at` + `reflects_commit`, validate Mermaid. Propose commit `map-architecture: architecture map (reflects <commit>)`; emit the handoff block — *Run next*: resume your backbone stage (usually `/write-prd <slug>`).

## Definition of Done

- `docs/architecture-map.md` exists with `updated_at`, `reflects_commit` and `mode`; authored docs reconciled, never overwritten.
- **Greenfield:** every foundation decision is confirmed, replaced or explicitly open; one ADR per irreversible pick in `docs/adr/`; the §14 items due here are answered or returned to P; «Capability dependencies» lists every slug from the brief; `docs/features/_scaffold/tasks.json` exists with the smoke-test DoD.
- **Brownfield:** C4 of what exists, module inventory, cited conventions, precedents with real anchors.
- No `git commit` was run by the skill.

## Anti-patterns

- **Treating the candidates as decided.** The reference table and the plan are input. Each decision is confirmed or replaced by the user, with alternatives shown.
- **Asking P questions.** «Should videos be in v1?» is scope. The brief answers it; if it does not, return the question to P and continue with the rest.
- **Dependencies that are preferences.** «Ideas before GitHub because it is smaller» is order, not dependency. Only «X cannot work without Y» goes into the map.
- **Re-scanning in downstream skills.** Scan once; others read the map.
- **A map with no `reflects_commit`.** It silently rots.
- **Placeholders or a guessed layout.** Cited, or `UNKNOWN`, or «open».
- **Running `git commit`.** Propose; the user commits.

## References & template

- [`./references/foundation.md`](./references/foundation.md) — greenfield: calibration, the decision table with Deja's candidates, the ADR list, the scaffold `tasks.json` contract.
- [`./templates/architecture-map.md`](./templates/architecture-map.md) — output scaffold (same file for current or foundation; `mode:` distinguishes).
- [`./templates/adr-template.md`](./templates/adr-template.md) — MADR scaffold for foundational ADRs. `architecture-design`, once copied, must reuse this file, not a second format.
- [`../_shared/mermaid-check.md`](../_shared/mermaid-check.md), [`../_shared/handoff.md`](../_shared/handoff.md), [`../_shared/ask-style.md`](../_shared/ask-style.md).
