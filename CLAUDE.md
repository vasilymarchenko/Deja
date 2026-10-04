# Deja

Personal "second brain": save pages, repos, posts, videos and own ideas with one click; find them later with one natural-language question. Search is by *why I saved it*, not only by text.

Product and plan (Ukrainian): `docs/initial-idea/second-brain.md`, `docs/initial-idea/second-brain-plan.md`. Read them before designing anything.

## Stack (planned)

- Backend: Python, FastAPI, SQLAlchemy 2.x (async), Alembic, Pydantic v2
- DB: Postgres + pgvector; BM25 via Postgres FTS
- Queue: `jobs` table in Postgres, worker with `SELECT ... FOR UPDATE SKIP LOCKED`
- Extraction: trafilatura (web), GitHub API, YouTube transcripts, `t.me/s/` preview
- Client: Chrome extension (API-first; the extension is just the first HTTP client)
- Deploy: Docker Compose (Caddy, FastAPI, Postgres, worker) on Hetzner CX23; nightly `pg_dump`

## Layout (target)

- `docs/` — product docs and ADRs
- `backend/` — FastAPI app, Alembic migrations, tests
- `extension/` — Chrome extension
- `deploy/` — Compose, Caddyfile, backup scripts

## Rules that must hold

- `user_id` on every table. API keys per user.
- One chunk structure for all record types: text, vector, record link, position (paragraph, timecode, README section).
- Lifecycle fields on every record: `expires_at`, `resurface_at`, `status`. Empty in v1.
- Embedding dimension is fixed in the schema. Changing the model means reindexing.
- Ideas are write-once. No editing, no folders.
- Work in vertical slices, epic by epic (see the plan).

## Workflow

- Git: branch `main`, no PRs, no remote yet. Work on short branches, fast-forward merge to `main` locally.
- Commits: short imperative subject.
- Secrets only in `.env` (git-ignored). Keep `.env.example` current.

## Commands

Fill in as the code appears (run, test, lint, migrate).
