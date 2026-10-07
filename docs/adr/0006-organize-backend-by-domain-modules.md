---
status: Accepted
owner: "T"
updated_at: "2026-10-07"
stage: "00"
feature: "_foundation"
---

# 0006 — Organize the backend by domain modules with the same files inside each

- **Status:** Accepted
- **Date:** 2026-10-07
- **Deciders:** T (the user) with Claude

## Context

The backend layout is set by the scaffold and copied by every feature. Agents write the code; the owner reviews it (brief §10). A layout where one feature lives in one place keeps both the agent's change and the owner's review small. The draft plan named no candidate.

## Decision drivers

- The owner must be able to read and review agent code (brief §10).
- Six or more source types will be added over time («Складові продукту»).
- One module boundary rule that a reviewer can check from the file tree.

## Considered options

1. **Domain modules (vertical slices):** `core`, `records`, `sources`, `indexing`, `search`, `jobs`; each with `router.py`, `service.py`, `models.py`, `schemas.py`.
2. **Layers:** `api/`, `domain/`, `infra/` — the classic N-tier layout.
3. **Flat:** a few files in `app/`.

## Decision outcome

**Chosen:** Option 1. A new source is a new folder in `sources/`; a module calls another only through its `service.py`, so boundaries are visible in imports. Layers spread one feature over three folders; flat breaks down after a few sources.

## Consequences

**Positive**
- Small, local diffs per feature; review by folder.
- Same file names everywhere: an agent and a reviewer know where to look.

**Negative**
- `core/` can turn into a dumping ground; keep it to config, DB session, auth, errors, logging, IDs.
- Modules are not the brief's slugs; a feature may touch 2–3 modules, and the task breakdown must name them.

**Neutral**
- Moving from modules to layers later is a mechanical but wide refactor.

## Links

- Brief: [idea-brief](../idea-brief.md) §10, «Складові продукту»
- Map: [architecture-map](../architecture-map.md) § Module layout
- Related ADR: [0001](0001-use-python-fastapi-sqlalchemy-backend.md)
