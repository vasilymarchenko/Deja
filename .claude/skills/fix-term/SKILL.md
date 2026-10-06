---
name: fix-term
description: >-
  Use to capture or update a Deja domain term in docs/CONTEXT.md before its meaning
  drifts — whenever a fuzzy word shows up in an interview, PRD, or review and you want
  one canonical definition plus a NOT-reference so a homonym cannot bite later.
  Triggers on "/fix-term <term>", "add term X", "fix term X", "what is X in our domain",
  "add to CONTEXT", "fix the glossary", "define X", "додай термін", "онови глосарій",
  "що означає X". Not invoked by the interview skill — see the end of the description.
  Lazy-bootstraps docs/CONTEXT.md from a template, checks for a conflicting entry, asks
  for a one-sentence definition + the concept it is confused with, and appends one
  bilingual entry (English line + Ukrainian translation) to ## Glossary. Refuses generic tech words (HTTP, queue, cache). Runs anytime, no gate.
  The interview skill does not invoke it: it asks the same questions in its own plan phase
  and appends lines in this format at its write phase.
---

# Skill: fix-term

Lazy utility. It fixes the meaning of a domain term in `docs/CONTEXT.md` the moment the term first appears, so its sense does not drift across the pipeline. For each term it records a one-sentence canonical definition and, when the word is ambiguous, a **NOT-reference** naming the concept it is confused with. It runs anytime with no upstream gate: one term spotted in a PRD, a review, or a chat. Later skills treat `## Glossary` as canonical.

**Relation to `interview`.** `interview` does not call this skill. It runs steps 2, 4–7 (filter, conflict check, definition, NOT-reference) as its own Phase 3 batch before `ExitPlanMode`, and steps 3, 8–10 (bootstrap, append, prune, stamp) in its Phase 12, so the user is never asked after the plan is approved. Both skills produce the same entry format; keep step 7 as the single definition of it.

Adapted from the course toolkit (`agentic-engineering-course/sdlc/plugin/skills/fix-term`). Changes for Deja: one glossary for the whole product (`docs/CONTEXT.md`), no multi-context mode, English canonical terms, every entry carries a Ukrainian translation.

This is a capture utility, not a Socratic stage. The one shared dependency is question phrasing:
→ [`../_shared/ask-style.md`](../_shared/ask-style.md)

## Owner

Whoever spots the ambiguity. In this project: the solo developer.

## Inputs

- `<term>` — the domain word or phrase. If missing, ask for it.
- (Optional) a batch of terms from `write-prd`. Process each term in turn.
- (Optional) phrasings already heard upstream — offer them as definition options instead of a blank question.

## Language

- `docs/CONTEXT.md` is **bilingual per entry** (see `CLAUDE.md` → Language requirements). The English line is canonical: the term is an English domain word (`record`, `chunk`, `save reason`) and code, schema and technical docs use it. The nested `uk:` line translates the same definition and boundary into Ukrainian; it is what product docs and UI text follow.
- The `uk:` line is headed by the Ukrainian product word (the UI word: «запис», «навіщо»). Inside it, other glossary terms are referred to by their Ukrainian words too, so the line reads as plain Ukrainian.
- If the user names a term in Ukrainian ("запис"), propose the English canonical name for the English line and keep the user's word as the head of the `uk:` line.
- The two lines say the same thing. Never put a detail in one language only; if the definition changes, both lines change together.
- `AskUserQuestion` text is Ukrainian, per `ask-style.md`.

## Protocol

1. **Target.** Always `docs/CONTEXT.md`. One glossary per product; never split a term across files.
2. **Generic-term filter.** Refuse words that name infrastructure or transport, not the business domain: HTTP, queue, cache, the datastore, a framework, JSON, REST, embedding model name. Say: "`<term>` is technical, not a domain word — its choice belongs in the SAD or an ADR, not the glossary." Continue only for real domain words.
   - Borderline: `chunk`, `job`, `worker` are project concepts with fixed meaning in `CLAUDE.md`. Accept them if the user wants them defined; the definition must describe the role, not the implementation.
3. **Bootstrap (lazy).** If `docs/CONTEXT.md` is missing, copy [`./templates/CONTEXT.md`](./templates/CONTEXT.md) there. Otherwise read it.
4. **Conflict check.** `Grep` for `^- <term> ` (case-insensitive) in `docs/CONTEXT.md`.
   - Found, same sense → stop and report "already in the glossary".
   - Found, different sense → one `AskUserQuestion`: "`<term>` is already defined as `<existing>` — the same concept or a different one?" If different, propose two distinct names and fix both.
   - Not found → continue.
5. **Definition** — one `AskUserQuestion`: "Define `<term>` in one sentence, in the language of this product." Offer interview phrasings as options when available. Explain why the definition matters (it becomes the name in the PRD, the code and the DB).
6. **NOT-reference** — one `AskUserQuestion`: "Which concept is `<term>` confused with?" No plausible homonym → `None`.
7. **Compose one entry (two lines).**
   ```
   - <term> — <one-sentence definition>. NOT <confused concept + how it differs>.
     - uk: **<Ukrainian word>** — <the same definition in Ukrainian>. НЕ <the same boundary in Ukrainian>.
   ```
   When step 6 = None, drop the `NOT …` / `НЕ …` part from both lines. Claude writes the translation; show both lines to the user in the step 5 confirmation (or in one final "accept the entry" question), so the Ukrainian wording is confirmed too, not only the English.
8. **Append under `## Glossary`.** Insert the whole entry alphabetically by the English term if the section is sorted, else at the end. Never rewrite existing entries.
9. **Prune empty H2s.** On a fresh bootstrap, delete `## Invariants` / `## Out of scope` if they have no real content. `## Glossary` is mandatory.
10. **Stamp + commit + handoff.** Set `updated_at: <today>`. Propose a commit `Add <term> to glossary` (or `Add <term>, <term2> to glossary`). When run standalone, print the handoff block per [`../_shared/handoff.md`](../_shared/handoff.md) (utility variant). When called from another skill (`write-prd`) in a batch, fold into the caller's commit and skip the handoff block — the caller prints one.

## Definition of Done

- `docs/CONTEXT.md` contains `<term>` under `## Glossary` as "one-sentence definition + optional NOT-reference", with the nested `uk:` translation line.
- Any conflict is resolved (reported as duplicate, or split into distinct names).
- Generic tech words are refused, not stored.
- Empty H2 sections pruned on bootstrap.
- `updated_at` is today; a commit is proposed (or folded into the caller's).

## Anti-patterns

- **Glossary as a PRD or scratch pad.** Implementation detail ("stored as a vector of 1024 dims") belongs in the SAD or an ADR.
- **Empty H2 "for completeness".**
- **Silent edits.** Adding a term without the user confirming the definition.
- **"I'll add them later".** Capture each term when it appears.
- **Storing generic tech words.**
- **Rewriting on re-run.** Read and append only.
- **Ambiguous term without a NOT-reference.**
- **English-only entry, or the two lines drifting apart.** Every entry has a `uk:` line, and it says exactly what the English line says.

## Template

- [`./templates/CONTEXT.md`](./templates/CONTEXT.md) — output scaffold; inline comments are the per-section contract.

## Example invocation

> **User:** "/fix-term save reason"
> **Skill:** target `docs/CONTEXT.md` → missing → copy template. Generic filter: domain word → continue. Grep → not found. Definition question → "the user's own short answer to «why did I save this», attached to a record and used as a search signal". NOT-reference question → "NOT summary — a summary is generated from the content; the save reason comes from the user". Entry: `- save reason — the user's own short answer to "why did I save this", attached to a record and used as a search signal. NOT summary (generated from the content, not written by the user).` + `  - uk: **навіщо** — власна коротка відповідь користувача на «навіщо я це зберіг», прив'язана до запису й використана в пошуку. НЕ резюме (його створюють зі змісту, а не пише користувач).` → user accepts both lines → append → prune empty sections → `updated_at` → propose `Add save reason to glossary` → handoff block, *Run next*: resume the current stage.
