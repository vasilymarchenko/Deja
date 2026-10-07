---
status: Accepted
owner: "T"
updated_at: "2026-10-07"
stage: "00"
feature: "_foundation"
---

# 0002 — Use one Postgres with pgvector and built-in full-text search for storage and search

- **Status:** Accepted
- **Date:** 2026-10-07
- **Deciders:** T (the user) with Claude

## Context

Search in Deja is hybrid: keyword search finds exact words (a library name, a person), vector search finds meaning across wording and languages. Records, chunks and both indexes need a home before the first feature. Moving data between stores later is a migration of the whole corpus.

## Decision drivers

- Scale: thousands of records, about 100k chunks (brief §3).
- One small server and one person operating it (brief §10 «broken processing stays unnoticed»).
- A record, its chunks and its vectors must be written atomically (brief §9 «a save must not be lost»).
- Mixed Ukrainian, Russian and English content and questions (brief §9).

## Considered options

1. **One Postgres + pgvector + built-in full-text search** — the draft plan's candidate.
2. **Postgres + a separate vector database (e.g. Qdrant)** — faster vector search at millions of vectors.
3. **Postgres + a search engine (Meilisearch or OpenSearch)** — real BM25 ranking and language analyzers.

## Decision outcome

**Chosen:** Option 1. At this scale one database is fast enough, gives one transaction for record + chunks + vectors, and one backup. The other options add a service, a sync path and a second backup with no gain at thousands of records.

## Consequences

**Positive**
- One store, one backup, one transaction boundary.
- Hybrid ranking is one SQL query (SQLAlchemy Core) over one table.

**Negative**
- Postgres `ts_rank` is not BM25 (the draft plan's wording is inaccurate); keyword ranking is weaker than a search engine's.
- No Ukrainian stemming in core Postgres; the `simple` configuration matches exact word forms only.

**Neutral**
- If `search-eval` shows weak keyword hits, ParadeDB `pg_search` adds BM25 inside Postgres without changing the datastore.

## Links

- Brief: [idea-brief](../idea-brief.md) §3, §9, §10
- Map: [architecture-map](../architecture-map.md) § Datastores, § Constraints
- Related ADR: [0004](0004-use-gemini-embedding-001-at-1536-dimensions.md), [0005](0005-fix-record-and-chunk-invariants.md)
