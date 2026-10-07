# Greenfield foundation — calibrate, walk the candidates, fix, scaffold

When `map-architecture` finds no code, it does not write «greenfield — nothing here». It runs a short session with role T to **fix the foundation**, so the per-feature flow has something real to build into, then hands a skeleton to `implement-tasks`.

Deja difference from the course: the course picks a stack from menus. Deja already has **candidates** — the table below, taken from the T parts of `docs/initial-idea/second-brain-plan.md`. This table is the one place the candidates live (also the denylist of implementation words for `interview` until `docs/architecture-map.md` exists). The session confirms or replaces each candidate; it does not start from a blank menu. A candidate is never accepted silently.

## G2 — Calibrate (one question)

One `AskUserQuestion`, prefixed «T:», phrased per [`../../_shared/ask-style.md`](../../_shared/ask-style.md):

- **«Запропонуй набір, я підтверджу»** → *guided-default*: one coherent bundle, one confirm, every piece explained in plain words.
- **«Пройдімо кожен вибір з поясненнями»** → *guided-explained*: one question per decision, each option glossed. **Default for Deja** (the user knows general backend work, not this stack).
- **«Я обираю сам, коротко»** → *expert*: choices without long explanations, overrides accepted freely.

Calibration sets depth and phrasing, not the set of decisions.

## G3 — The decisions (one row each; candidate → confirm / replace / defer)

| Decision | Candidate (from the draft plan) | Real alternatives to show | ADR? |
|---|---|---|---|
| **Backend stack** | Python, FastAPI, SQLAlchemy 2.x async, Alembic, Pydantic v2 | a .NET stack (the user's daily language); a thinner Python stack without an ORM | yes |
| **Datastore + search** | Postgres + pgvector; BM25 via Postgres FTS | a separate vector DB; a search engine beside Postgres | yes |
| **Queue** | `jobs` table in Postgres, worker with `SELECT … FOR UPDATE SKIP LOCKED` | a broker (Redis / RabbitMQ); no queue in v1 (sync only) | yes |
| **Embedding model + dimension** | no candidate (the plan says «embeddings»); brief §14 asks for an ADR | a hosted model (fixed dimension, cost per record); a local model (free, slower, needs CPU/RAM on the server) | yes |
| **Record / chunk invariants** | `user_id` on every table (`[assume:users=one]` may change); one chunk structure (text, vector, record link, position); lifecycle fields `expires_at`, `resurface_at`, `status` empty in v1; record → record links table | drop lifecycle fields until needed; per-type chunk tables | yes |
| **Module structure** | no candidate | `backend/app/{api,domain,infra}` layered; one package per capability; flat | yes |
| **IDs** | no candidate | UUID v7 (time-sortable, app-generated); DB serial | no (map only) |
| **Extraction adapters** | trafilatura, GitHub API, YouTube transcripts, `t.me/s/` preview | — (per-source choice belongs to each feature's `architecture-design`; the map records only the adapter *contract*: one interface, chunks in the shared structure) | no |
| **Client** | Chrome extension, API-first | — (P decided; the map records «API is the product, the extension is a client») | no |
| **Auth** | API key per user | — | no (map only) |
| **Deploy** | Docker Compose (Caddy, API, Postgres, worker) on Hetzner CX23 | a PaaS; a single VM without containers | no (map only) |
| **Backups** | nightly `pg_dump`; brief §14 asks where the off-server copy lives and when the first restore test is | off-server copy to object storage; restore test as a scaffold task vs an epic-8 task | yes if the user wants it as a rule |
| **Monitoring + AI cost accounting** | brief §14 asks whether basic monitoring of broken sources and stuck jobs comes earlier than epic 8 | structured logs now, dashboard later; cost per record in the `jobs` table from day one | no (map «Open» or a scaffold task) |
| **Conventions** | no candidate | error envelope; test layout (unit + integration on a real Postgres); migration naming; one local check command (`make check` or a script); CI runs the same command | no |

Rules for the walk:
- Present the candidate first, then the alternatives, then the recommendation. The user may pick an alternative; then the map records the replacement and the ADR lists the candidate as a rejected option.
- A «defer» answer is written under «Open» in the map with the reason and the stage that must decide it. It is never dropped.
- Rows marked «P decided» are read from the brief and confirmed in one line, not re-asked.
- Keep the §5 out-of-scope list in mind: the foundation does not pre-build a web UI, a bot, multi-tenant RLS or billing. It only leaves the door open (`user_id` everywhere).

## G4 — Brief §14 items due here

Read `docs/idea-brief.md` §14 and pick every item whose «термін» is `/map-architecture`. Ask each as a T question with the trade-off. The answer goes into the map section that owns it («Datastores», «Operations», «Open») and into an ADR when irreversible (the embedding model is the typical case: changing it means re-embedding the whole corpus). An item that turns out to be a P question (scope, priority) is returned: one line in the handoff block «back to P: …».

## G5 — Capability dependencies (the roadmap's only constraint)

For every slug in «Складові продукту», state what it technically needs before it can exist. Format, in the map:

| Capability | Needs first | Why (technical) |
|---|---|---|
| `<slug>` | `_scaffold` / `<other slug>` / foundation piece | one line |

Only hard technical needs: «cannot run without». Order preferences, dogfooding value, risk testing are P arguments and belong to `roadmap`. Confirm the table in one `AskUserQuestion`; the user may remove a dependency they consider soft.

## G6 — Fix the foundation

Write `docs/architecture-map.md` from the template with `mode: greenfield-bootstrap`. C4 and module layout describe the **target baseline**; the conventions section is the rule set the scaffold and every feature follow. Same file brownfield mode produces later, so downstream skills do not care which mode created it.

Foundational ADRs go to `docs/adr/` (cross-cutting; feature ADRs live in the feature folder). One ADR per «yes» row above, from [`../templates/adr-template.md`](../templates/adr-template.md): status `Accepted`, `owner: T`, `stage: "00"`, the candidate listed under considered options whether chosen or not, honest Negative consequences. Title names the decision («Use Postgres for both vectors and full-text search»), not the topic.

## G7 — Scaffold `tasks.json` contract (handed to `implement-tasks`)

Emit `docs/features/_scaffold/tasks.json`, same shape as the `break-tasks` contract, `layer: scaffold`. Deja baseline (adjust to the decisions made):

```json
{
  "slug": "_scaffold",
  "tasks": [
    { "id": "S1", "title": "Create backend/ layout + app entry point + health endpoint", "layer": "scaffold", "deps": [],
      "acs": [], "dod": "the API boots locally and answers the health endpoint", "files_hint": ["backend/app/", "backend/pyproject.toml"] },
    { "id": "S2", "title": "Wire the test harness + a boot smoke test", "layer": "scaffold", "deps": ["S1"],
      "acs": [], "dod": "empty test suite runs green; the boot smoke test passes", "files_hint": ["backend/tests/"] },
    { "id": "S3", "title": "Set up Alembic + an initial migration with the shared invariants", "layer": "scaffold", "deps": ["S1"],
      "acs": [], "dod": "migration applies and reverts cleanly on a fresh Postgres with pgvector", "files_hint": ["backend/alembic/"] },
    { "id": "S4", "title": "Compose file for local dev (API, Postgres+pgvector, worker placeholder)", "layer": "scaffold", "deps": ["S1"],
      "acs": [], "dod": "docker compose up boots the stack; S2 and S3 pass inside it", "files_hint": ["deploy/"] },
    { "id": "S5", "title": "One local check command (lint + tests + migration check)", "layer": "scaffold", "deps": ["S2", "S3"],
      "acs": [], "dod": "one command runs everything and exits non-zero on any failure", "files_hint": ["Makefile or scripts/"] },
    { "id": "S6", "title": "Update CLAUDE.md: add a Stack section pointing at the map, fill Commands, .env.example", "layer": "scaffold", "deps": ["S5"],
      "acs": [], "dod": "CLAUDE.md matches docs/architecture-map.md; .env.example lists every variable", "files_hint": ["CLAUDE.md", ".env.example"] }
  ]
}
```

Add a CI task that runs the same local check command on every PR; keep it to that one command. If a backup or monitoring decision in G3/G4 became «now», add it as S7+.

**The skeleton smoke test is the TDD anchor.** Scaffold tasks have no feature AC, so `implement-tasks` anchors red → green on the structural smoke test: RED = «does not boot / tooling does not run», GREEN = «boot + empty suite + migration apply/revert all succeed».

After the scaffold the repo is real, `docs/architecture-map.md` describes it, and the per-feature flow (`interview <slug> → write-prd → … → implement-tasks`) builds features into it.
