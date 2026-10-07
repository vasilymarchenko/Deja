---
status: Accepted
owner: "T"
updated_at: "2026-10-07"
stage: "00"
feature: "_foundation"
---

# 0003 — Use a Postgres jobs table with SKIP LOCKED as the background queue

- **Status:** Accepted
- **Date:** 2026-10-07
- **Deciders:** T (the user) with Claude

## Context

Extraction, chunking, embeddings and LLM calls take seconds per record. Done inside the HTTP request, capture is slow, and a save is lost when the AI service is down. This ADR fixes only the **mechanism** of background work. **When** it arrives is P's open question (brief §14, decided in `/roadmap`).

## Decision drivers

- A save must not be lost when the AI service is unavailable (brief §9).
- Capture must feel instant (`docs/CONTEXT.md` → capture).
- One small server, one person operating it (brief §10).
- AI cost per record must be countable (map § Operations).

## Considered options

1. **Own `jobs` table in Postgres; worker claims with `SELECT … FOR UPDATE SKIP LOCKED`** — the draft plan's candidate.
2. **A library over Postgres (e.g. procrastinate)** — retries and statuses ready-made.
3. **A broker (Redis or RabbitMQ with Celery or arq)** — the common choice for high load.
4. **No queue in v1, all synchronous** — a timing question for P, not a mechanism.

## Decision outcome

**Chosen:** Option 1. No new service; the job row is written in the same transaction as its record, so a job cannot be lost or orphaned; extra fields (`record_id`, attempts, error) are ours to add. Option 2 owns its tables; option 3 adds a service and splits the transaction.

## Consequences

**Positive**
- Atomic record + job; restart of the worker loses nothing.
- Job state is plain SQL: easy to inspect and to report (processing health in `background-indexing`).

**Negative**
- Retries, backoff and statuses are our code (~100–200 lines) and need their own tests.
- Polling the table adds a small constant load; fine at this scale.

**Neutral**
- Moving to a broker later replaces the `jobs` module only; enqueue and handler signatures can stay.

## Links

- Brief: [idea-brief](../idea-brief.md) §9, §10, §14
- Map: [architecture-map](../architecture-map.md) § Conventions → Background work
- Related ADR: [0002](0002-use-postgres-for-vectors-and-full-text-search.md)
