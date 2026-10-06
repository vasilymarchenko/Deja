---
status: Living
updated_at: "2026-10-06"
---

# Domain Context — Deja

<!--
CONTEXT.md is the domain glossary of Deja — not a PRD and not a scratch pad. NO implementation
detail (no datastore / framework names, no API contracts) — only domain words and the
boundaries between them. Implementation choices live in sad.md and ADRs; behaviour lives
in PRD.md.

One glossary for the whole product. English terms; add the Ukrainian UI word in the
definition when it differs: (UI: «...»).

Terms are fixed the moment they appear in an interview / PRD / review — never batched.
Empty H2 → prune before commit. ## Glossary is mandatory; the other two are optional.
-->

## Glossary

<!-- One line per term: name · one-sentence canonical definition · one-sentence boundary
     (what it is NOT). Alphabetical once there are a few. -->
- anchor — a short "entity + aspect" label, 3–7 per record, proposed by the LLM, that names what the record is about in the words the user is likely to search with (UI: «якір»). NOT a tag: anchors are generated per record, not picked from a shared list, and nothing is organized or browsed by them.
- capture — the user's act of saving a source or an idea from a client in one step (UI: «зберегти»). NOT indexing: capture is what the user does and must feel instant; indexing is what the system does with the record afterwards.
- chunk — a piece of a record's content together with its position in the original: paragraph, README section, timecode (UI: «фрагмент»). NOT the record: search matches chunks, then shows the record and opens it at the chunk's position.
- idea — a record whose main content is the user's own short text; write-once, optionally linked to one or more trigger sources (UI: «ідея»). NOT a note: no editing, folders or manual links; it grows only when a similar thought is saved again.
- record — one saved item in the user's memory: a source or an idea, with its content, save reason or title, anchors and lifecycle fields (UI: «запис»). NOT a bookmark: a record holds the full content, and saving the same source again extends the same record.
- save reason — one line on why the user saves a source, proposed by the LLM at capture and accepted, edited or skipped by the user (UI: «навіщо»). NOT a summary: a summary says what the text is about; the save reason says why it matters to the user, often in words the text does not contain.
- source — a record whose main content comes from outside: a web page, a GitHub repo, a Telegram post, a video (UI: «джерело»). NOT an idea: in a source the external content is primary and the save reason sits on top of it. The kind (web page, repo, post, video) is the "source type".
- trigger — a source linked to an idea as what prompted it, attached at capture or by a later save (UI: «тригер»). NOT a manual link between records: the user never connects records by hand; the only links are idea → its triggers.
