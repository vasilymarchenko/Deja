# Process log

Decisions about *how we work*: adaptations of the course SDLC toolkit, deviations from it, and the reasons. Not a file list (git has that), not product decisions (the briefs and PRDs have those), not architecture (ADRs have that).

One entry per decision, appended at the end (chronological: the log is written more often than read, and appending needs no rewrite). Keep an entry to a few lines: what, why, what was rejected, where it lives. Write it the day the decision is made; a reason reconstructed later is a guess.

Format:

```
## YYYY-MM-DD — <decision in one line>
**Why:** <the reason, one or two sentences>
**Rejected:** <alternatives and why not>
**Lives in:** <files or skills that implement it>
```

---

## 2026-10-06 — Brief length is a word budget per depth, not "≤5 pages"
**Why:** the template alone is 730 words; `hard` asks for 5+ competitors; a fixed page count forced cutting exactly the research the depth requested. easy 1500 / medium 2500 / hard 3500, body only; product mode +500 for the capability table. When over, shorten synthesis (§8, §9), never evidence (§6, §7).
**Lives in:** `interview` → "Depth regulator", Phase 13.

## 2026-10-06 — Glossary terms are confirmed before `ExitPlanMode`; `interview` does not invoke `fix-term`
**Why:** the course had `fix-term` run inside the write phase, which meant two questions per term after the plan was approved. Now Phase 3 asks them in one batch during planning and Phase 12 only appends lines in the `fix-term` format. One definition of the line format stays in `fix-term` step 7.
**Rejected:** calling `fix-term` as a sub-skill (no real mechanism for "called from interview"; it would re-ask).
**Lives in:** `interview` Phases 3 and 12; `fix-term` → "Relation to `interview`".


## 2026-10-06 — Tech-term check in briefs is source-based, not a hardcoded word list
**Why:** the user rejected hardcoding words. Denylist is built at run time from `CLAUDE.md` → `## Stack`; allowlist is `docs/CONTEXT.md` glossary plus the prose of `second-brain.md`; the grey zone goes through the "audience test" (would a user need this word to describe what the product does?) and one `AskUserQuestion` batch. Both sources are living documents, so the check follows them.
**Rejected:** the course regex (`Postgres|pgvector|FastAPI|…`) — drifts from the real stack and contradicts the `CLAUDE.md` rule that domain terms like `embedding`, `chunk` stay in English.
**Lives in:** `.claude/skills/interview/SKILL.md` → "Product vs implementation terms", Phase 13.


## 2026-10-06 — No RICE anywhere in Deja
**Why:** one user. Reach is always 1, so the score cannot rank anything; filling the numbers becomes a calculator game. Priority is argued in words: brief §4 "Чому зараз" / "Чому цей продукт", roadmap "Чому в цьому порядку". The recommendation in the brief cites the top devil's-advocate vector instead of the score.
**Rejected:** replacing Reach with "uses per week" (still a made-up number for a product that does not exist yet).
**Lives in:** `interview` (Phase 9 removed, 14 sections), `roadmap`, `CLAUDE.md`.


## 2026-10-06 — `roadmap` copied without RICE; Next is hand-ordered with one reason per row
**Why:** see the RICE entry below. The first run is seeded from the product brief's two tables; infrastructure gets its own "Фундамент і експлуатація" section so it is neither lost nor mistaken for a feature. Shipped links the changelog, not a PR (no PRs in this repo).
**Rejected:** keeping RICE columns "for later" (dead fields invite fake numbers).
**Lives in:** `.claude/skills/roadmap/`.


## 2026-10-06 — `interview` gets an explicit `product` mode writing `docs/idea-brief.md`
**Why:** auto-detection by "empty folder" can never fire here. Product mode adds the "Складові продукту" table, which is the feature list, and hands off to `roadmap`. Feature mode reads the product brief and researches only the delta, so ten features do not repeat the same market scan.
**Rejected:** rewriting `second-brain.md` into the brief (it stays as the verbatim §1 input; the brief adds research on top).
**Lives in:** `.claude/skills/interview/SKILL.md`, `templates/idea-brief.md` ("Складові продукту" block).


## 2026-10-06 — Three levels of work: product → repo foundation → features
**Why:** the user wants to work out the product first and derive the feature list from it. The course has no product-level ideation for an existing repo; its greenfield mode only fires in an empty folder and hands off to `scaffold`. The plan's epics are not all features: epic 0 is foundation, epic 6 is three features, epic 7 is one feature plus ongoing tuning.
**Rejected:** course greenfield mode as-is (needs an empty folder, `scaffold` assumes a Go template); a product-level PRD (`write-prd` is per feature by design; the course models the product as roadmap + architecture foundation).
**Lives in:** `CLAUDE.md` → SDLC toolkit (levels, epic → feature table); `.claude/skills/interview/SKILL.md` → "Two modes".


## 2026-10-06 — Keep a process log instead of ADRs for process decisions
**Why:** process decisions are frequent and small; without a record the reasoning is lost within weeks and gets re-litigated. ADRs are too heavy for "dropped a score", `CHANGELOG.md` is for the product.
**Rejected:** ADRs (overhead), entries in `CHANGELOG.md` (wrong audience and wrong subject), only git history (shows what, not why).
**Lives in:** `docs/process-log.md`, rule in `CLAUDE.md` → SDLC toolkit.



## 2026-10-06 — Glossary entries are bilingual: English line + Ukrainian translation
**Why:** the user asked for it after the first product interview. Product docs and UI are Ukrainian, so the Ukrainian word and its exact meaning must be fixed in the same place as the English term; otherwise each brief and PRD translates the definition again and the meanings drift. The English line stays canonical for code and technical docs.
**Rejected:** a separate Ukrainian glossary file (two documents in two languages drift; breaks the `CLAUDE.md` rule); only the Ukrainian word in `(UI: «…»)` (names the word but not its meaning or boundary).
**Lives in:** `fix-term` (Language, step 7 entry format, template), `interview` Phase 3 and 12, `CLAUDE.md` → Language requirements (one exception to "never two languages"), `docs/CONTEXT.md`.


## 2026-10-07 — Two roles, P and T; one owner per stage; the draft plan is a candidate, not a rule
**Why:** the first `/roadmap` run took its order from `docs/initial-idea/second-brain-plan.md` and `CLAUDE.md` carried the plan's stack and schema as rules. Neither had been worked through: the idea went through `interview`, the plan went through nothing. The plan mixes "what" (epics, scope — P) and "how" (stack, queue, chunk structure — T). Now P decides what, T decides how; each stage has one owner, the other role only supplies input; every `AskUserQuestion` names its role. The plan is split by role: its P parts are candidates for `roadmap` and `interview <slug>`, its T parts are candidates for `map-architecture`. `CLAUDE.md` keeps only decided things (language, process, git); "Stack (planned)" became "Stack candidates (not decided)" and "Rules that must hold" was removed (they are T candidates for ADRs).
**Rejected:** keeping the plan as the default order "to save questions" (silently converts a draft into a decision); splitting T into architect / tech lead / developer (one person, no value in the distinction now).
**Lives in:** `CLAUDE.md` → Two roles, Stack candidates, SDLC toolkit (levels); `_shared/ask-style.md` → Role; `roadmap` Inputs + step 2a; `map-architecture` Inputs.

## 2026-10-07 — Stage order after the product brief: map-architecture (T) → roadmap (P) → scaffold → features
**Why:** the course runs `map-architecture` first (step 0); the previous adaptation put `roadmap` before it. P cannot order capabilities without knowing which technically depend on which, and that is T's knowledge. So T fixes the foundation and writes a "Capability dependencies" table; P then orders Next at each branching point with that table as the only constraint.
**Rejected:** roadmap first with "obvious" dependencies guessed by Claude (that is how the plan leaked in); merging roadmap into map-architecture (mixes the roles in one session).
**Lives in:** `CLAUDE.md` → levels; `_shared/handoff.md` table; `interview` Phase 14; `roadmap` step 2a and step 7; `map-architecture` G5, G8.

## 2026-10-07 — `map-architecture` copied: candidates instead of menus, §14 questions, dependency table, no commit
**Why:** the course greenfield path picks a stack from generic menus and asks "what is this project" (G3). Deja has a brief and candidates, so the session confirms or replaces each candidate with real alternatives, skips the intent question, answers the brief's §14 items due at this stage, and adds the dependency table `roadmap` needs. The `explorer` agent is replaced by the built-in `Explore` agent. The scaffold has no CI task (no remote); one local check command is the CI. The MADR ADR template now lives in this skill (`templates/adr-template.md`); `architecture-design` and `decide-adr` must reuse it when copied.
**Rejected:** copying as-is and overriding in conversation (the protocol would still say "pick from the menu"); a separate `foundation` skill (the course's single-file map is what downstream skills expect).
**Lives in:** `.claude/skills/map-architecture/`, `_shared/mermaid-check.md`.

## 2026-10-07 — Skills propose commits; they never run `git commit`
**Why:** the roadmap run committed right after writing the file, before the user had seen it. The rule was already in the handoff ("the user says commit") but not in the skills. Now every skill prints the proposed message and stops; new product documents are shown in the reply before they are written.
**Rejected:** a git hook blocking commits from Claude (the user also wants Claude to commit on request).
**Lives in:** `_shared/handoff.md` → Rules; `roadmap` step 7; `map-architecture` G8 + Anti-patterns; `CLAUDE.md` → SDLC toolkit.

## 2026-10-07 — `CLAUDE.md` keeps only lasting rules; process moved to `docs/sdlc-skills.md`; assumptions tagged
**Why:** `CLAUDE.md` had grown into a description of the planning phase (stage list, adopted skills, stack candidates). Those go stale when development starts. Now it holds what stays true: sources of truth, the two roles, language rules, git workflow, commands, and an **Assumptions** block with tags (`[assume:remote=none]`, `[assume:users=one]`). Skills branch on the tags instead of restating «no remote» or «one user»; changing an assumption is one line plus a grep. Stack candidates moved to `map-architecture/references/foundation.md`, the only place they are used.
**Rejected:** leaving the stage list in `CLAUDE.md` «until development starts» (nobody remembers to remove it); a separate `docs/assumptions.md` (one more file to forget; `CLAUDE.md` is read every session).
**Lives in:** `CLAUDE.md`; `docs/sdlc-skills.md` → Source / Adoption rules / Stages / Adopted so far; the `[assume:…]` tags in `roadmap`, `map-architecture`, `interview`, `_shared/handoff.md`.
