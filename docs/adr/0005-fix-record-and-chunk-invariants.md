---
status: Accepted
owner: "T"
updated_at: "2026-10-07"
stage: "00"
feature: "_foundation"
---

# 0005 — Keep user_id everywhere, one chunk table for all record types, and lifecycle fields on every record

- **Status:** Accepted
- **Date:** 2026-10-07
- **Deciders:** T (the user) with Claude

## Context

Every source type (web page, idea, GitHub repo, Telegram post, later video) produces records and chunks. If each feature shapes its own tables, search changes with every new source. The draft plan fixes these invariants in epic 0 because changing them later touches every table.

## Decision drivers

- New source types must not change search (brief «Складові продукту»: six source capabilities).
- One user now, friends with keys next (brief §3, `[assume:users=one]`).
- Lifecycle fields exist in v1 without logic, and are exported (brief §5, `data-export`).
- The only link between records is idea → trigger (`docs/CONTEXT.md`).

## Considered options

1. **The draft plan's candidate:** `user_id` on every table; one `chunks` table (text, vector, record link, position); `expires_at`, `resurface_at`, `status` on every record; a record → record links table.
2. **Option 1 without lifecycle fields** — add them when proactivity is built.
3. **One chunk table per source type** — exact position columns per type.

## Decision outcome

**Chosen:** Option 1, with two additions: chunk position is `ordinal` (order in the record) + `locator` (JSONB, fields defined per source feature), and every vector stores `embedding_model` (ADR 0004). `record_links(from_record_id, to_record_id, kind)` has one `kind`: `trigger`. Option 2 saves little (a nullable column is cheap to add) and contradicts brief §5; option 3 forces search to `UNION` per type.

## Consequences

**Positive**
- A new source is an adapter that emits chunk drafts; search is untouched.
- Multi-user stays a configuration change, not a migration of every table.

**Negative**
- `locator` is untyped in the DB; each source feature must document and validate its fields.
- Lifecycle fields are carried and exported before anything uses them.

**Neutral**
- Where the save reason and anchors live (a chunk kind or a separate vector) is left to `save-reason-and-anchors`; it reuses this structure or extends it by ADR.

## Links

- Brief: [idea-brief](../idea-brief.md) §3, §5, «Складові продукту»
- Map: [architecture-map](../architecture-map.md) § Record and chunk invariants
- Related ADR: [0002](0002-use-postgres-for-vectors-and-full-text-search.md), [0004](0004-use-gemini-embedding-001-at-1536-dimensions.md)
