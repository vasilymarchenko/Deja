# Deja

Personal "second brain": save pages, repos, posts, videos and own ideas with one click; find them later with one natural-language question. Search is by *why I saved it*, not only by text.

Product and plan (Ukrainian): `docs/initial-idea/second-brain.md`, `docs/initial-idea/second-brain-plan.md`. Read them before designing anything.

This is a learning project for an agentic engineering course. The process follows the course SDLC toolkit (see below).

## Stack (planned)

- Backend: Python, FastAPI, SQLAlchemy 2.x (async), Alembic, Pydantic v2
- DB: Postgres + pgvector; BM25 via Postgres FTS
- Queue: `jobs` table in Postgres, worker with `SELECT ... FOR UPDATE SKIP LOCKED`
- Extraction: trafilatura (web), GitHub API, YouTube transcripts, `t.me/s/` preview
- Client: Chrome extension (API-first; the extension is just the first HTTP client)
- Deploy: Docker Compose (Caddy, FastAPI, Postgres, worker) on Hetzner CX23; nightly `pg_dump`

## Layout (target)

- `docs/initial-idea/` — original product idea and plan
- `docs/architecture-map.md` — codebase survey, written once, read by every stage
- `docs/roadmap.md` — Now / Next / Later / Shipped
- `docs/features/<slug>/` — all artifacts of one feature (idea-brief, PRD, sad, adr, data-model, contracts, tasks, test-plan, review)
- `docs/adr/` — cross-cutting ADRs only; feature ADRs live in the feature folder
- `docs/CONTEXT.md` — the one domain glossary for the whole product (written by `fix-term`)
- `docs/sdlc-skills.md` — overview of all course skills, in order of use
- `backend/` — FastAPI app, Alembic migrations, tests
- `extension/` — Chrome extension
- `deploy/` — Compose, Caddyfile, backup scripts
- `CHANGELOG.md` — repo root

## Rules that must hold

- `user_id` on every table. API keys per user.
- One chunk structure for all record types: text, vector, record link, position (paragraph, timecode, README section).
- Lifecycle fields on every record: `expires_at`, `resurface_at`, `status`. Empty in v1.
- Embedding dimension is fixed in the schema. Changing the model means reindexing.
- Ideas are write-once. No editing, no folders.
- Work in vertical slices, epic by epic (see the plan).

## Language requirements

Language is chosen by **audience**, not by file type. If a user of the product could read the text — Ukrainian. If only a developer or an AI tool will — English.

**Ukrainian (product level):**
- all UI text of the extension and any web client, user-facing error messages, notifications, demo data;
- product docs — `docs/initial-idea/**` and any later product specification;
- plain language: no untranslated technical jargon, no code identifiers in the prose.

**English (technical level):**
- code, identifiers, code comments, DB schema, API contracts, log and error messages for developers;
- commit messages, branch names;
- `README.md`, `CLAUDE.md`, ADRs, design docs, implementation plans, `deploy/**`.

**English only (AI tooling):**
- skills, agents, hooks, prompts, `.claude/**`, memory files;
- prompts sent to an LLM at runtime (summaries, "why I saved it" extraction, query rewriting). The user's saved text may be Ukrainian; the instructions around it are English.

**Rules:**
- Never keep the same document in two languages. Split by level of detail, not by language.
- Never translate technical terms. Always write them in English, in every document and every answer, including Ukrainian prose (`embedding`, `chunk`, `worker`, `job`, `record`, `vector`).
- This covers identifiers, library names, file paths and domain terms of this project.

**SDLC artifacts:**
- Ukrainian: `idea-brief.md`, `PRD.md`, `docs/roadmap.md`, user documentation.
- English: everything else — `architecture-map.md`, `sad.md`, ADRs, diagrams, `data-model.md`, migrations, `contracts/`, `tasks/`, `test-plan.md`, `_review/`, `CONTEXT.md`, `CHANGELOG.md`.

## SDLC toolkit (course)

Source: `C:\Work\Personal\agentic-engineering-course\sdlc` (read-only reference; do not edit it).
- `plugin/skills/<name>/` — skills (`SKILL.md` + `templates/` + `references/`), `plugin/skills/_shared/` — shared references.
- `plugin/agents/` — agents. `document-templates/` — cross-feature snippets. `examples/` — reference runs.
- `00-overview/` — process map, DoR/DoD, MVP vs full artifact set.

The toolkit is adopted piece by piece, not installed as a plugin. The user decides what to copy and when.
- Copy a skill to `.claude/skills/<name>/`, an agent to `.claude/agents/<name>.md`. Copy the `_shared/` files a skill references, to `.claude/skills/_shared/`.
- Adapt on copy: fix paths, drop the `sdlc:` agent namespace, replace generic stack hints with this project's stack, apply the language rules above.
- Process changes for this repo: no PRs and no remote. `ship-feature` updates `CHANGELOG.md` and `docs/roadmap.md`, then ff-merges the branch to `main`; skip the PR body.
- Before using an uncopied skill, read its `SKILL.md` in the source. Do not invent its protocol.
- Feature slug: one epic from the plan = one feature, `kebab-case`, no epic number (`web-page-slice`, `chrome-extension`).
- Artifact size scales with task size (`00-overview/mvp-vs-full.md`). When in doubt, use the MVP set.
- Diagrams: Mermaid only.

Adopted so far (local, invoked as `/<name>`):
- Skills: `interview`, `fix-term`. Shared refs: `_shared/ask-style.md`, `_shared/handoff.md`.
- Agents: `researcher`, `strategist`, `analyst`, `devils-advocate`.

## Workflow

- Git: branch `main`, no PRs, no remote yet. Work on short branches, fast-forward merge to `main` locally.
- Commits: short imperative subject.
- Secrets only in `.env` (git-ignored). Keep `.env.example` current.

## Commands

Fill in as the code appears (run, test, lint, migrate).
