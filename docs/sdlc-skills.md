# SDLC process and skills

How Deja is built with the course SDLC toolkit. The reasons for each adaptation are in `docs/process-log.md`.

## Source

`C:\Work\Personal\agentic-engineering-course\sdlc` — read-only reference; never edit it.
- `plugin/skills/<name>/` — skills (`SKILL.md` + `templates/` + `references/`), `plugin/skills/_shared/` — shared references.
- `plugin/agents/` — agents. `document-templates/` — cross-feature snippets. `examples/` — reference runs.
- `00-overview/` — process map, DoR/DoD, MVP vs full artifact set.

## Adoption rules

The toolkit is adopted piece by piece, not installed as a plugin. The user decides what to copy and when.
- Copy a skill to `.claude/skills/<name>/`, an agent to `.claude/agents/<name>.md`, the `_shared/` files it references to `.claude/skills/_shared/`.
- Adapt on copy: fix paths, drop the `sdlc:` agent namespace, point stack hints at `docs/architecture-map.md` (or the candidate table in `map-architecture/references/foundation.md` until the map exists), apply the language and role rules of `CLAUDE.md`. Where the course assumes a remote, PRs, CI or many users, branch on the `[assume:…]` tags in `CLAUDE.md` instead of hardcoding the current state.
- A skill proposes a commit; it never runs `git commit`.
- Before using an uncopied skill, read its `SKILL.md` in the source. Do not invent its protocol.
- Every process decision (copying or adapting a skill, dropping a course rule, changing the pipeline) gets an entry in `docs/process-log.md` the same day: what, why, what was rejected, where it lives.
- Artifact size scales with task size (`00-overview/mvp-vs-full.md`). When in doubt, use the MVP set.
- Deja-wide deviation: **no RICE anywhere** (`[assume:users=one]`). Priority is argued in words.

## Stages, in order

1. **Product** (P): `/interview product` → `docs/idea-brief.md`. «Складові продукту» is the feature list; «Фундамент і експлуатація» is the input for T.
2. **Foundation** (T): `/map-architecture` → `docs/architecture-map.md` + `docs/adr/` + `docs/features/_scaffold/tasks.json`. Confirms or replaces the stack candidates and states which capabilities technically depend on which. Then `/implement-tasks _scaffold` builds the skeleton.
3. **Order** (P): `/roadmap` → `docs/roadmap.md`. P orders the capabilities; T's dependency table is the only constraint.
4. **Features**: `/interview <slug>` → `write-prd` → `clarify-prd` → `architecture-design` → … → `ship-feature`, per feature. Feature slug: `kebab-case`, no epic number, from «Складові продукту».

`ship-feature` in this repo updates `CHANGELOG.md` and `docs/roadmap.md`, then the branch is ff-merged to `main` (`[assume:remote=none]`: no PR body).

## Adopted so far

Invoked as `/<name>`, from `.claude/skills/`:
- `interview` (product + feature modes), `fix-term`, `map-architecture` (role T; candidates confirmed, not menus; capability dependencies; owns the ADR template), `roadmap` (role P; runs after `map-architecture`).
- Shared refs: `_shared/ask-style.md` (incl. the role rule), `_shared/handoff.md`, `_shared/mermaid-check.md`.
- Agents: `researcher`, `strategist`, `analyst`, `devils-advocate`.

## All course skills, in order of use

🚪 means a hard gate: the skill refuses to run if its input artifact is missing. "Ad-hoc" means the skill can run at any stage.

## Idea and framing

1. **`interview`** (role P) — turns a raw idea into a structured brief. It runs a Socratic interview, competitive research, three strategic approaches, a multi-perspective review and a devil's advocate pass. Output: `docs/features/<slug>/idea-brief.md`. Deja adaptation: no RICE score (one user, the score ranks nothing); priority is argued in words in the brief. Two modes: `/interview product` writes the product brief `docs/idea-brief.md` with the capability list (next: `map-architecture`); `/interview <slug>` writes a feature brief that reads the product one.
2. **`scaffold`** — creates a new project from the course `base-tpl` template. It asks about the stack and optional parts (frontend, deploy, CI, observability) and removes what you do not pick. Used only in an empty folder, right after `interview`.
3. **`map-architecture`** (role T) — builds the architecture map that every later skill reads. On existing code it scans the repo once; on an empty repo it walks the stack candidates with you (confirm / replace / defer), writes foundational ADRs plus a scaffold `tasks.json`, and states which capabilities technically depend on which. Deja adaptation: candidates from its own reference table and the draft plan instead of menus; the dependency table is the only constraint `roadmap` accepts; answers the brief's §14 questions due at this stage; no CI task while `[assume:remote=none]`. Output: `docs/architecture-map.md`, `docs/adr/`, `docs/features/_scaffold/tasks.json`. Runs **before** `roadmap`.
4. **`classify-size`** (ad-hoc) — decides how big a feature is: XS, S, M, L or XL. It asks four questions (PR count, time, new module/API/migration, breaking changes) and maps the answers to a size. Output: `docs/features/<slug>/.size`; later skills use it to decide how much to write.
5. **`roadmap`** (ad-hoc, role P) — keeps one board of outcomes above single features. It adds items to Now / Next / Later with a RICE score and moves them between columns. Copied for Deja without RICE: the first run is seeded from the product brief and runs after `map-architecture`; P orders Next at each branching point, with T's dependency table as the only constraint; one reason per row in P's words. Output: `docs/roadmap.md`; `write-prd` moves an item to Now, `ship-feature` moves it to Shipped.
6. **`fix-term`** (ad-hoc) — fixes the meaning of a domain term before it drifts. It adds one definition plus "NOT to be confused with" to the glossary, in English with a Ukrainian translation, and checks for conflicts. Output: `CONTEXT.md`; `write-prd` needs it.

## Requirements

7. **`write-prd`** 🚪 — turns the brief into a product spec. It interviews you, drafts goals, user stories, acceptance criteria, NFRs and KPIs, checks each criterion, then runs a clean-context critic. Output: `docs/features/<slug>/PRD.md`; needs `idea-brief.md` and `CONTEXT.md`.
8. **`clarify-prd`** — removes ambiguity from the PRD. A devil's-advocate agent lists places where two engineers could build different things; you resolve each one or move it to Open questions. Output: the same `PRD.md`, tightened.

## Architecture and design

9. **`architecture-design`** 🚪 — writes the Software Architecture Document. It covers Arc42 (12 sections) with C4 L1/L2 diagrams, chooses the target surfaces (API, UI, CLI…), and creates an ADR only for big, hard-to-reverse decisions. Output: `sad.md` + `adr/NNNN-*.md`; needs `PRD.md`.
10. **`decide-adr`** 🚪 (ad-hoc) — records a decision made outside `architecture-design`, for example in code or in a chat. It checks that the decision is worth an ADR, then fills context, options, outcome and consequences (Proposed → Accepted). Output: `adr/NNNN-<title>.md`; needs `PRD.md` and `sad.md`.
11. **`complete-sequence-diagrams`** — adds runtime flows to the SAD. It draws one Mermaid sequence diagram per critical flow, with happy and error paths, and confirms each flow with you. Output: `sad.md` §6; these flows later drive DB indexes and API errors.
12. **`generate-data-model`** — designs the DB schema and writes real migrations in one pass. It reads PRD entities, the ER diagram and the sequences, and writes paired up/down SQL migrations in a staging folder. Output: `data-model.md` + `migrations/` (staged; `implement-tasks` moves them into the live tree).
13. **`api-forge`** — derives the API contract; it is never hand-written. It builds OpenAPI from the data model, sequences and acceptance criteria, plus `events.md` / `cli.md` / `public-api.md` when that surface exists. Output: `contracts/openapi.yaml` + `api-sync-report.md`.
14. **`prepare-design-spec`** (ad-hoc, UI only) — writes a UI spec before any work in Figma or another design tool. It reads the repo and existing artifacts and adds a testable Definition of Done for the screen. Output: `design-spec.md`.

## Planning

15. **`break-tasks`** — splits the feature into tasks of one day or less. Each task has dependencies, a Definition of Done and links to the PRD, SAD, data model and API. Output: `tasks/_epic.md`, `tracker.md`, one file per task, and the machine-readable `tasks.json`.
16. **`plan-tests`** — plans tests before any test is written. It maps every acceptance criterion to at least one test and picks the test level (unit, integration, e2e, contract, load) and the data strategy. Output: `test-plan.md` (inline in `PRD.md` for XS/S).

## Build and verify

17. **`implement-tasks`** — builds the feature with TDD, task by task, from `tasks.json`. For each task it writes a failing test, makes it pass, refactors, runs lint/tests and commits. It can run as one agent, a team of agents, or a workflow, depending on the task graph.
18. **`verify-ui`** (ad-hoc, UI only) — checks acceptance criteria in a real browser. It opens the running app, runs the scenario and reads the real page state. It returns pass/fail per criterion with screenshots and never fixes code.
19. **`review-feature`** 🚪 — an independent code review of the whole feature diff in a clean context. Stage 1 checks the diff against the PRD and acceptance criteria; stage 2 checks quality; you resolve each finding. Output: `_review/review-<date>.md` with PASS or CHANGES REQUESTED.

## Release

20. **`ship-feature`** 🚪 — closes the loop after a PASS review. It runs the feature end to end, writes the changelog and the PR body, and moves the feature to Shipped in the roadmap. It never merges by itself; in this repo there is no PR step, the branch is ff-merged to `main`.
21. **`user-documentation`** (ad-hoc) — writes end-user docs from the live app. Playwright walks each flow and takes screenshots, and one agent per flow writes a guide. Output: flow guides plus a linked User Guide index.
