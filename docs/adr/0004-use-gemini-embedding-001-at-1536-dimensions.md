---
status: Accepted
owner: "T"
updated_at: "2026-10-07"
stage: "00"
feature: "_foundation"
---

# 0004 — Use gemini-embedding-001 at 1536 dimensions and store the model name with every vector

- **Status:** Accepted
- **Date:** 2026-10-07
- **Deciders:** T (the user) with Claude

## Context

Vector search compares vectors from one model only; the dimension is part of the column type. Changing the model means re-embedding the whole corpus. Brief §14 asks for this ADR at `/map-architecture`; brief §10 names the risk that a provider retires the model.

## Decision drivers

- Mixed Ukrainian, Russian and English content and questions (brief §9); multilingual quality decides the product thesis.
- Provider risk: the corpus must survive a model retirement (brief §10).
- One small server: 2 vCPU, 4 GB RAM (map § Constraints).
- pgvector's HNSW index supports at most 2000 dimensions for `vector`.

## Considered options

1. **Google `gemini-embedding-001`, truncated to 1536 dimensions** — top of multilingual leaderboards; supports Matryoshka truncation (a shorter prefix of the vector keeps most of the quality).
2. **OpenAI `text-embedding-3-small`, 1536** — cheapest hosted option, weaker on Ukrainian and Russian.
3. **A local model on the server (BGE-M3 or Qwen3-Embedding-0.6B, ~1024)** — free and private, but uses 1–2 GB of the 4 GB RAM and is slow on 2 CPUs.
4. **Defer the model to `web-page-slice`** — fix only the rules now.

(The draft plan named no candidate.)

## Decision outcome

**Chosen:** Option 1, plus a rule for all options: every vector row stores `embedding_model`, and switching the model is a re-embed job over the corpus. Re-embedding is cheap at this scale (about 15M tokens for 5000 records: roughly $2–3 at ~$0.15 per 1M tokens), so the provider risk becomes a matter of time and a job, not money or a rewrite.

## Consequences

**Positive**
- Best available multilingual quality for the core thesis.
- Model change is an operation, not a migration crisis.
- 1536 fits the pgvector HNSW limit.

**Negative**
- Every text and question goes to Google (privacy question for a second user, brief §14).
- Google has retired earlier embedding models; a re-embed will likely be needed some day.
- Public price sources disagree; verify on the first bill (`ai_calls`).

**Neutral**
- A local model stays possible later through the same re-embed path if privacy for friends requires it.

## Links

- Brief: [idea-brief](../idea-brief.md) §9, §10, §14
- Map: [architecture-map](../architecture-map.md) § Stack, § Record and chunk invariants
- Related ADR: [0002](0002-use-postgres-for-vectors-and-full-text-search.md), [0005](0005-fix-record-and-chunk-invariants.md)
- Sources checked 2026-10-07: checkthat.ai/answers/what-are-the-best-embedding-models, aiapiprices.com/embeddings-api-pricing, tokenmix.ai/blog/text-embedding-models-comparison, sparecores.com/server/hcloud/cx23
