---
status: Accepted
owner: "T"
updated_at: "2026-10-07"
stage: "00"
feature: "_foundation"
---

# 0001 — Use Python with FastAPI, SQLAlchemy 2.x async and Alembic for the backend

- **Status:** Accepted
- **Date:** 2026-10-07
- **Deciders:** T (the user) with Claude

## Context

Deja has no code yet. Every later feature builds into one backend, so its language and framework must be fixed before the scaffold. Changing them later means rewriting the backend. The draft plan names a Python stack; the owner's daily language is C#/.NET.

## Decision drivers

- The core work is text extraction, chunking, embeddings and LLM calls (brief «Складові продукту»); the library ecosystem for it decides speed and quality.
- Code is written by AI agents; the owner must still read and trust it (brief §10 «owner knows the code worse»).
- One small server, one user; no need for high throughput.

## Considered options

1. **Python 3.14 + FastAPI + SQLAlchemy 2.x async + Alembic + Pydantic v2** — the draft plan's candidate.
2. **.NET (ASP.NET Core + EF Core + Npgsql with pgvector)** — the owner's daily stack.
3. **Python + FastAPI + psycopg with plain SQL, no ORM** — fewer abstractions.

## Decision outcome

**Chosen:** Option 1. Python has the strongest tools for the core work (trafilatura for main-text extraction, transcript and parsing libraries, AI SDKs); .NET has only weaker ports. The owner-readability risk is handled by strict types (Pydantic + `mypy --strict`), tests and a simple module layout (ADR 0006), not by the language choice.

## Consequences

**Positive**
- Best-in-class extraction and AI libraries without bridges or a second runtime.
- FastAPI derives the OpenAPI contract from types; `api-forge` and the extension get it for free.
- SQLAlchemy Core keeps hand-tuned search queries close to SQL while still typed.

**Negative**
- Not the owner's daily language: reviewing agent code is slower, and debugging needs Python knowledge.
- Async SQLAlchemy has more sharp edges (session scope, lazy loading) than the sync API.

**Neutral**
- Switching to .NET later means a full rewrite of `backend/`; the API contract and DB schema could survive.

## Links

- Brief: [idea-brief](../idea-brief.md) §10, §11
- Map: [architecture-map](../architecture-map.md) § Stack, § Conventions
- Related ADR: [0006](0006-organize-backend-by-domain-modules.md)
