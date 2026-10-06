---
status: Living
updated_at: "<today YYYY-MM-DD>"
---

# Domain Context — Deja

<!--
CONTEXT.md is the domain glossary of Deja — not a PRD and not a scratch pad. NO implementation
detail (no datastore / framework names, no API contracts) — only domain words and the
boundaries between them. Implementation choices live in sad.md and ADRs; behaviour lives
in PRD.md.

One glossary for the whole product. Every entry is bilingual: the English line is canonical
(code, schema, technical docs); the nested `uk:` line is its Ukrainian translation, headed by
the Ukrainian product word used in UI and product docs.

Terms are fixed the moment they appear in an interview / PRD / review — never batched.
Empty H2 → prune before commit. ## Glossary is mandatory; the other two are optional.
-->

## Glossary

<!-- One entry per term, two lines:
     - <term> — <one-sentence definition>. NOT <confused concept + how it differs>.
       - uk: **<Ukrainian word>** — <the same definition in Ukrainian>. НЕ <the same boundary>.
     Alphabetical by the English term. -->
- <term> — <one-sentence definition>. NOT <concept it is confused with + how it differs>.
  - uk: **<українське слово>** — <те саме визначення українською>. НЕ <та сама межа>.

## Invariants

<!-- Domain rules that hold across the whole product, phrased «X always must / can never».
     Rules ABOVE any single acceptance criterion. Prune if there are none. -->
- <invariant>

## Out of scope

<!-- Concepts explicitly placed outside this domain, with a one-line reason. Prune if none. -->
- <concept · reason>
