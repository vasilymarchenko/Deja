<!-- Format: MADR (Markdown Any Decision Record). One file = one decision. English. -->
<!-- Owned by map-architecture (foundational ADRs in docs/adr/). architecture-design and decide-adr reuse this file for feature ADRs in docs/features/<slug>/adr/ — no second format. -->

---
status: Accepted            # Proposed → Accepted → Superseded by NNNN
owner: "T"                  # the role that owns the decision (T here; a feature ADR may name the stage)
updated_at: "<YYYY-MM-DD>"
stage: "00"                 # SDLC stage the ADR was born at: 00 foundation, 04-05 architecture-design, 11 decide-adr
feature: "_foundation"      # _foundation for docs/adr/; the slug for a feature ADR
---

# NNNN — <title in imperative: the DECISION, not the topic>

<!-- ✓ "Use Postgres for both vectors and full-text search"  -->
<!-- ✗ "Search storage strategy"                             -->

- **Status:** Accepted
- **Date:** <YYYY-MM-DD>
- **Deciders:** T (the user) with Claude

## Context

<2–4 sentences: what is happening, why this must be decided now, which brief section or map section triggered it.>

## Decision drivers

<!-- Each bullet comes from the brief (§10 risks, §11 feasibility, §14 open questions), the map constraints, or a stated quality goal. No invented drivers. -->

- <driver>
- <driver>

## Considered options

<!-- ALL options shown to the user, including the candidate from CLAUDE.md / the plan whether chosen or not, and the ones rejected. No straw men. -->

1. **<Option A>** — <one sentence>.
2. **<Option B>** — <one sentence>.
3. **<Option C>** — <one sentence>.

## Decision outcome

**Chosen:** Option <N>. <1–2 sentences: why it won, referencing the drivers.>

## Consequences

**Positive**
- <…>

**Negative**
- <…>

**Neutral**
- <e.g. switching later needs <what>>

## Links

- Brief: [[../idea-brief.md]] §<N>
- Map: [[../architecture-map.md]] §<section>
- Related ADR: <[[NNNN-other]] if any>
