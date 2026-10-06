# Deja

Personal "second brain": save pages, repos, posts, videos and own ideas with one click; find them later with one natural-language question. Search is by *why I saved it*, not only by text.

A learning project for an agentic engineering course. The process (stages, skills, adoption rules) is described in `docs/sdlc-skills.md`; the reasons behind it are in `docs/process-log.md`.

## Sources of truth

- `docs/initial-idea/` — the raw idea and a draft plan (Ukrainian, never edited). Drafts: nothing in them is decided until a stage of the process confirms it.
- `docs/idea-brief.md` — what the product is (P). Its «Складові продукту» table is the feature list.
- `docs/architecture-map.md` + `docs/adr/` — how it is built (T).
- `docs/roadmap.md` — in which order (P).
- `docs/CONTEXT.md` — the domain glossary, bilingual per entry.
- `docs/features/<slug>/` — every artifact of one feature.

Read whichever exist before designing anything. Code lives in `backend/`, `extension/`, `deploy/`.

## Two roles

- **P (product)** decides *what* to build: brief, roadmap order, feature briefs, PRDs.
- **T (technical)** decides *how*: foundation, ADRs, SAD, data model, code.

One owner per stage; the other role only supplies input. A skill never asks P a "how" question or T a "what" question, and it names the role in every `AskUserQuestion`.

## Assumptions

Facts about the project that skills depend on. Each is stated here once, not restated elsewhere. When one changes: edit this line, add a `docs/process-log.md` entry, grep the skills for the tag.

- `[assume:remote=none]` — no git remote, no PRs, no CI. Review and merge happen locally; the local check command is the CI.
- `[assume:users=one]` — one user (the developer). No scoring by reach (no RICE), no multi-tenant isolation, no billing. `user_id` is still kept everywhere so this can change.

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
- One exception: `docs/CONTEXT.md` is bilingual per entry. The English line is canonical; the nested `uk:` line translates it and gives the Ukrainian product word. Both lines live in one entry and change together.
- Never translate technical terms. Always write them in English, in every document and every answer, including Ukrainian prose (`embedding`, `chunk`, `worker`, `job`, `record`, `vector`).
- This covers identifiers, library names, file paths and domain terms of this project.

**SDLC artifacts:**
- Ukrainian: `idea-brief.md`, `PRD.md`, `docs/roadmap.md`, user documentation.
- English: everything else — `architecture-map.md`, `sad.md`, ADRs, diagrams, `data-model.md`, migrations, `contracts/`, `tasks/`, `test-plan.md`, `_review/`, `CHANGELOG.md`.
- Bilingual: `CONTEXT.md` (English entry + Ukrainian translation, see Rules).

## Workflow

- Git: branch `main`. Work on short branches, fast-forward merge to `main` locally. See `[assume:remote=none]`.
- Commits: short imperative subject. Claude proposes a commit message; the user commits or says «commit».
- Secrets only in `.env` (git-ignored). Keep `.env.example` current.
- Diagrams: Mermaid only.

## Commands

Fill in as the code appears (run, test, lint, migrate).
