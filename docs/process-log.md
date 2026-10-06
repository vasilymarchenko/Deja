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

