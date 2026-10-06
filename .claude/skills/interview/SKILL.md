---
name: interview
description: >-
  SDLC ideation phase for Deja — the single entry point from a raw idea to a confirmed
  idea-brief. Runs a Socratic interview, competitive research (researcher agent), three
  strategic approaches (strategist agent), a multi-perspective review (analyst agent), a
  clean-context devil's advocate (devils-advocate agent), then a Claude-proposed Feasibility
  that the user confirms. Has a depth dial (easy / medium / hard). Output:
  docs/features/<slug>/idea-brief.md (14 sections, Ukrainian). Triggers on
  "/interview <slug>", "raw idea", "capture an idea", "interview a feature", "brief for X",
  "idea brief", "new feature X", "ideation for <slug>", "start epic N", "інтерв'ю фічі",
  "бриф ідеї", "нова фіча". Two modes: "/interview product" writes the product-level brief
  docs/idea-brief.md (next stage: map-architecture); "/interview <slug>" writes a feature brief
  under docs/features/<slug>/ (next stage: write-prd). Does not write ADRs — that is
  architecture-design.
---

# Skill: interview (SDLC ideation phase)

One autonomous, Claude-driven protocol for the ideation phase. Output: one idea-brief with 14 sections — `docs/idea-brief.md` in **product mode**, `docs/features/<slug>/idea-brief.md` in **feature mode**. No separate brainstorm or initiatives files.

Adapted from the course toolkit (`agentic-engineering-course/sdlc/plugin/skills/interview`). Changes for Deja: the course greenfield mode (auto-detected by an empty folder, hands off to `scaffold`) is replaced by an explicit **product mode** that works in the existing repo and hands off to `roadmap`; agents are local (`.claude/agents/`); the glossary is `docs/CONTEXT.md`; the brief is written in Ukrainian; **no RICE** (`[assume:users=one]` in `CLAUDE.md` — Reach is always 1, the score ranks nothing; priority is argued in §4 and in roadmap order); glossary terms are confirmed in the plan phase and written without `fix-term` questions; the tech-term check is derived from living docs instead of a hardcoded word list.

## Two modes

| | Product mode | Feature mode |
|---|---|---|
| Call | `/interview product` | `/interview <slug>` |
| Subject | the whole product: what Deja is and is not | one capability of the product |
| §1 input | `docs/initial-idea/second-brain.md`, offered verbatim | the epic text from `docs/initial-idea/second-brain-plan.md`, or the user's paragraph |
| Output | `docs/idea-brief.md` | `docs/features/<slug>/idea-brief.md` |
| Extra section | **"Складові продукту"** — the capability list that becomes the roadmap and the feature slugs | — |
| §4 heading | "Чому цей продукт" | "Чому зараз" |
| Phase 4 research | full market scan (default depth `hard`) | delta only: what the product brief §6 does not cover |
| Phase 6 lenses | judge the product approaches | judge the feature approaches; do not repeat product-level conclusions |
| Phase 8 devil's advocate | attacks the product thesis ("search by why I saved it") | attacks the feature |
| Phase 10 Feasibility | no repo scan; based on `CLAUDE.md` stack and the user's skills and time | repo scan + planned stack |
| Handoff | `/map-architecture` | `/write-prd <slug>` |

**Mode detection.** The slug `product` selects product mode. Anything else is feature mode. There is no auto-detect: the repo exists, so the course's empty-folder rule would never fire.

**Feature mode reads the product brief.** If `docs/idea-brief.md` exists, Phase 0 loads it. Its §6 table, §8 lenses, §10 risks and "Складові продукту" are upstream context for every agent prompt; the feature brief links it under "Пов'язане" and cites it instead of repeating it. If it does not exist, say so once and run the full protocol — do not block. The product brief is recommended before the first feature, not required.

## Why one skill

Claude does the research (competitors, approaches, perspectives, devil's advocate, Feasibility); the user confirms through `AskUserQuestion`. ADRs are not part of ideation; they appear at `architecture-design`. Ideation stays pure product.

## Owner

The idea author. In this project: the solo developer (Vasyl), who is also PM and Tech Lead.

## When to use

- "capture an idea <slug>", "brief for <feature>", "raw idea for <feature>", "ideation for <slug>".
- Starting an epic from `docs/initial-idea/second-brain-plan.md` ("start epic 1").
- `/interview <slug>` as the explicit call; `/interview product` for the product-level brief.
- Skip if the target brief already exists with `status: Confirmed` and is fresh (≤2 weeks). Update it instead of rewriting.

## Inputs

- `<slug>` — `product`, or kebab-case, short, no epic number (`web-page-slice`, `chrome-extension`). If missing, suggest 2–3 options from the idea; prefer a slug from the product brief's "Складові продукту" when it exists.
- Product context (always read): `docs/initial-idea/second-brain.md`, `docs/initial-idea/second-brain-plan.md` (the matching epic in feature mode; the whole plan in product mode), `CLAUDE.md`.
- Feature mode: `docs/idea-brief.md` (the product brief) if present.
- Optional: `docs/roadmap.md`, `docs/architecture-map.md`, `docs/CONTEXT.md`, prior notes or links from the user.

## Language

- `idea-brief.md` — **Ukrainian** (product doc, see `CLAUDE.md` → Language requirements). Plain language, no code identifiers. Domain terms stay in English as `CLAUDE.md` requires (`record`, `chunk`, `save reason`); see "Product vs implementation terms" below for what may appear at all.
- `AskUserQuestion` text — Ukrainian, per [`../_shared/ask-style.md`](../_shared/ask-style.md).
- Agent prompts — English. The user's answers may be Ukrainian; quote them as they are.

## Product vs implementation terms

The brief describes **what the user gets**, never **how we build it**. There is no fixed word list; the rule is a test plus two living sources.

**The audience test.** A word may appear in the brief body if a user of the product would need it to describe what the product does for them. If swapping the word for another vendor, library, engine or algorithm would change nothing the user sees, it is an implementation term and must not appear. Examples: "пошук за змістом" passes; `embedding`, `BM25`, `pgvector` fail (the user sees search results, not the index). "запис", `record`, `save reason` pass; `jobs` table, `worker` fail.

**Allowed by source (no judgment needed):**
- every term under `## Glossary` in `docs/CONTEXT.md` — the `fix-term` generic-term filter already keeps infrastructure words out of it;
- every term that `docs/initial-idea/second-brain.md` uses in its prose — it is the product doc written for the user.

**Forbidden by source (no judgment needed):**
- every product, library, service and protocol name in the stack section of `docs/architecture-map.md` or, until it exists, in the candidate table of `.claude/skills/map-architecture/references/foundation.md` (build the list at run time; do not copy it here);
- numeric engineering targets: latency, percentiles, dimensions, sizes, SLOs.

**Grey zone.** A word that is in neither source goes through the audience test. If Claude is not sure, it does not decide alone: Phase 13 collects all such words into one `AskUserQuestion` batch (keep as product term / replace with plain wording / move to §14 Open questions as a PRD-level detail). Terms confirmed as product terms are candidates for `docs/CONTEXT.md` (Phase 3).

## Depth regulator (first checkpoint)

The first `AskUserQuestion` of every run picks the depth. It is written to the brief frontmatter (`depth:`).

- **easy** — 3–4 checkpoints. Phase 1 (idea); Phase 2 as one batch (2–3 questions: user and pain · success criterion · scope); Phases 3, 10 and 11 merged into one final confirm (glossary terms + Feasibility + recommendation together). Phases 4–8 run by Claude itself, without agents: 1–2 quick searches for §6, three one-paragraph approaches, its own list of 3–5 risks. All 14 sections are filled, but compactly.
- **medium** (default in feature mode) — the full protocol below.
- **hard** (default in product mode) — the full protocol + one extra Socratic batch in Phase 2 + a wider §6 (5+ competitors).

Easy does not relax honesty: its 3–4 `AskUserQuestion` calls are real. Fabricating answers is forbidden at every depth.

**Word budget by depth** (body only — frontmatter and HTML comments excluded):

| depth | budget | why |
|---|---|---|
| easy | ≤ 1500 | compact run, no agent output |
| medium | ≤ 2500 | full table of 3–5 competitors, three approaches, three lenses |
| hard | ≤ 3500 | 5+ competitors, extra Socratic batch |
| product mode, any depth | +500 | the "Складові продукту" table |

The budget scales with the content the depth produces; a fixed page count would force cutting exactly the research the depth asked for.

## Mode handling

**Plan-mode native.** Phases 0–11 are read-only (Read, Grep, Glob, WebSearch, Agent, AskUserQuestion). At 11 → 12 the skill calls `ExitPlanMode` with the synthesized plan; Phase 12 then writes files.

If the session is not in plan mode (`ExitPlanMode` unavailable), Phase 12 runs right after Phase 11. Keep all content in session memory until Phase 12 either way.

**Auto Mode does not cancel checkpoints.** The `AskUserQuestion` calls in Phases 0.25, 1, 2, 3, 10, 11 (and the grey-zone batch in 13, when needed) are a data-input protocol, not "clarifying questions". Auto Mode covers "should I proceed" pauses between phases, not data input. If `AskUserQuestion` is denied, stop and tell the user; do not work around it.

**Phase 12 asks nothing.** Everything that needs the user's input is confirmed before `ExitPlanMode`. After it, Claude only writes, checks and proposes a commit. The one exception is the grey-zone term batch in Phase 13, which exists only if the self-check finds words that neither source decides.

## AskUserQuestion style

Every question follows [`../_shared/ask-style.md`](../_shared/ask-style.md). Short version:

1. **Ukrainian** labels and descriptions. Technical names (Feasibility ☑/☐, Approach A/B/C) stay as they are; actions are Ukrainian ("Прийняти Approach C", "Позначити TBD").
2. **`question`** — 3–4 sentences: CONTEXT (which phase, a 1-line recap of what is collected) · WHY IT MATTERS (what breaks with a wrong answer) · WHAT TO LOOK AT before choosing.
3. **Option `description`** — 3–5 sentences: what changes in the brief and which later phases it affects · what the option means in plain words · the hidden trade-off, stated right there. Example: "Mark recommendation as TBD" → "write-prd will refuse to run until status is Confirmed; this blocks the pipeline for this feature".
4. **Forbidden:** terse English labels ("Confirm", "Adjust"), one-line descriptions, unexplained jargon, trade-offs hidden in a follow-up.
5. **Ideation has no tables, endpoints or files yet.** The "concrete names" rule of `ask-style.md` means here: name the brief section and the later stage affected, not code artifacts.

## Agents

| Phase | Agent (`subagent_type`) | Mode |
|---|---|---|
| 4 | `researcher` | competitive + adjacent-solution table |
| 5 | `strategist` | three approaches A / B / C |
| 6 | `analyst` | per-lens bullets + 3×3 synthesis matrix |
| 8 | `devils-advocate` | Mode B — failure-mode hunt |

Rules (from the course agent contract):
- **Clean context.** An agent does not see this conversation. The prompt string is the only channel: inline the raw idea, the deep-dive answers, the relevant upstream outputs, and the path `docs/CONTEXT.md` (if it exists). Write the prompt in English.
- Only the agent's final message comes back. Store it in session memory; do not paste it to the user raw.
- If a local agent is unavailable at runtime, fall back to `general-purpose` with the same prompt plus the agent file's instructions (Read `.claude/agents/<name>.md` and inline it).
- At **easy** depth, do not dispatch agents (see Depth regulator).

## Phase → section map

Phase numbers and brief section numbers differ. Use this table when a phase says "cite §N".

| Phase | Writes section |
|---|---|
| 1 | §1 Сира ідея |
| 2 | §2 Проблема · §3 Користувачі · §4 Чому зараз · §5 Поза межами |
| 3 | `docs/CONTEXT.md` (glossary lines, written at Phase 12) |
| 4 | §6 Аналіз конкурентів |
| 5 | §7 Стратегічні підходи |
| 6 | §8 Погляд з трьох сторін |
| 7 | §9 Компроміси та граничні випадки |
| 8 | §10 Ризики (+ feeds §9) |
| 10 | §11 Feasibility |
| 11 | §12 Рекомендація · §13 Відкладені підходи |
| 7, 8, 13 | §14 Відкриті питання |
| 11 (product mode) | "Складові продукту" — derived from the recommended approach |

## Protocol

**Phases 0–11 read-only. Phase 11.5 = ExitPlanMode. Phases 12–14 write, self-check, propose a commit.**

### 0. Pre-plan setup (read-only)

- **Read** `./templates/idea-brief.md` into session memory. Do not copy it yet.
- **Read** product context: `docs/initial-idea/second-brain.md`, `docs/initial-idea/second-brain-plan.md` (the matching epic; in product mode the whole plan — its epics are candidate capabilities), and `docs/roadmap.md` / `docs/architecture-map.md` if present.
- **Feature mode:** read `docs/idea-brief.md` if present; keep its §6, §8, §10 and "Складові продукту" as upstream context. Note which row of "Складові продукту" this feature is.
- **Read** `docs/architecture-map.md` → Stack (or, until it exists, the candidate table in `.claude/skills/map-architecture/references/foundation.md`); keep the list of names as the session denylist for Phase 13.
- **Read** `docs/CONTEXT.md` if it exists; keep `## Glossary` as session state (allowlist for Phase 13, conflict check for Phase 3).
- **Check** the target brief (`docs/idea-brief.md` or `docs/features/<slug>/idea-brief.md`). If it exists with `status: Confirmed` and is ≤2 weeks old, stop and offer an update instead.
- **No Write / Edit / mkdir.**

### 0.25. Depth checkpoint (AskUserQuestion — mandatory)

One question: the depth (see "Depth regulator"). Confirm the slug in the same call if the user did not give one. easy → short route; medium / hard → full route.

### 1. Idea capture (AskUserQuestion — mandatory)

One `AskUserQuestion` for the raw paragraph: "Опиши ідею в 1–3 реченнях своїми словами." Offer as one option: in feature mode the epic's text from the plan; in product mode the "Одним рядком" paragraph plus "Ключова відмінність" from `second-brain.md`. The user accepts or rewrites. Store the answer verbatim as §1. Do not edit it — it is the baseline.

### 2. Socratic deep dive (AskUserQuestion — mandatory)

Pick 3–5 questions from 5 categories, based on the shape of the idea:
- **Problem clarity** — what exactly hurts, for whom, how often.
- **Solution validation** — why this solution, what was tried before.
- **Success criteria** — what "it worked" means, as a concrete metric.
- **Constraints** — time, budget, solo capacity, dependencies on earlier epics.
- **Strategic fit** — how it fits the plan and the product goal ("find by why I saved it").

Ask in batches of 2–3, not all at once. Do not ask what the product docs already answer; cite them instead and ask only for gaps or contradictions.

Product mode: `second-brain.md` already answers most of "problem" and "for whom". Spend the questions on what it does not say: what the user tried before and why it failed, what "7 of 10" is measured against, what would make them abandon the product, and which parts of "Межі v1" are product decisions versus implementation habits.

### 3. Glossary confirmation (AskUserQuestion — mandatory when there are terms)

Collect every new domain word from §1 and the Phase 2 answers. Apply the `fix-term` generic-term filter: skip infrastructure and transport words (HTTP, JSON, queue, cache, database, framework names). Skip words already in `## Glossary` with the same sense. If nothing is left, say so and move on.

For the rest, one `AskUserQuestion` batch (one question per term, up to 4 per call; a second call if more). For each term Claude proposes, from the interview phrasing:
- the English canonical name and the Ukrainian product word (the user's word if they said it in Ukrainian);
- a one-sentence definition;
- the NOT-reference (the concept it is confused with), or `None`;
- the Ukrainian translation of the definition and the NOT-reference.

Show both lines in the question, so the user confirms the Ukrainian wording too. Options: accept the entry as proposed / give your own wording / drop the term. A term found in `## Glossary` with a **different** sense gets its own question: same concept, or a new name is needed.

Store the confirmed entries in `pending_glossary_lines`, already in the `fix-term` entry format (step 7 of `fix-term` is the single definition):
```
- <term> — <definition>. NOT <confused concept + how it differs>.
  - uk: **<Ukrainian word>** — <the same definition in Ukrainian>. НЕ <the same boundary>.
```
**Do not write `docs/CONTEXT.md` now** — Phase 12 does. Do not invoke `fix-term`; its protocol is applied here (questions) and in Phase 12 (file write), so the user is never asked after `ExitPlanMode`.

At **easy** depth this batch is merged into the final confirm (Phases 10–11).

### 4. Competitive research (`researcher` agent, read-only)

Dispatch `researcher`. Prompt inlines: raw idea, deep-dive answers, `docs/CONTEXT.md` path, and the depth (medium: 3–5 rows; hard: 5+ rows). It returns a table **Product · URL · Features · Value (1–5) · Gap**, every row footnoted with date and search query, plus one synthesis line (the biggest gap). Store it as the §6 draft.

Feature mode with a product brief: inline the product §6 table and ask for the **delta** only — competitors or adjacent solutions specific to this capability that the product table does not cover. The feature §6 holds the delta rows plus one line "see product brief §6 for the market scan". Product mode: this is the full market scan; it is the one place the money on research is spent.

No user input here. If the agent reports `RESEARCH_LIMITED`, keep that note in §6; never invent rows.

### 5. Strategic approaches (`strategist` agent, read-only)

Dispatch `strategist`. Prompt inlines: raw idea, deep-dive answers, the §6 synthesis line (feature mode: plus the product brief's recommended approach, so the feature approaches stay inside the product's chosen direction). It returns three genuinely different approaches:
- **A — Simplicity:** the shortest path, MVP, fewest moving parts.
- **B — Differentiation:** the wow-factor / moat / unique angle.
- **C — Balanced:** the trade-off between A and B.

Each has **Name** (3–5 words) · **Thesis** (1 sentence, product language, no tech names) · **For whom** (segment from §3) · **Outcome metric** (baseline → target) · **Key trade-off** · **Effort signal** S / M / L. Store as the §7 draft.

### 6. Multi-perspective review (`analyst` agent, read-only)

Dispatch `analyst`. Prompt inlines: raw idea + the three approaches from Phase 5 (feature mode: plus the product §8 synthesis lines, with the instruction not to repeat product-level conclusions). It returns, for each lens (**Engineer** — abstract, no library or DB names; **Executive**; **UX**), 3–5 bullets across the approaches, then the 3×3 matrix (+/0/−, ≤6-word reason per cell) and one synthesis line per approach. Store as the §8 draft.

### 7. Trade-offs + edge cases (synthesis, read-only)

Claude writes into session memory (no user input):
- Pros / cons table per approach.
- 5–8 edge cases any approach must handle (data, integrations, failure modes, operations).

### 8. Devil's advocate (`devils-advocate` agent, Mode B, clean context)

Dispatch `devils-advocate`. The prompt says: **"Mode B — no PRD yet"** and inlines the raw idea + the three approaches (mark the leading one, if any). Do not inline Claude's own optimism (trade-offs, matrix). It returns 5–10 attack vectors with trigger / breaks / signal.

Product mode: the target is the product thesis itself — "search by why I saved it" beats search by text; one user's dogfooding is enough to tune it; the capture habit survives six months. Feature mode: the target is the feature; inline the product §10 so the agent does not re-report product-level risks.

The sharpest vector goes to §10 Risks. The rest feed §9 Edge cases.

### 9. (removed) RICE

Not used in Deja. Priority is argued in prose in §4 "Чому зараз" (what in the plan or in daily use makes this the next step) and the Effort signal of the recommended approach in §7. Do not compute a score, do not add a `value_score` to the frontmatter.

### 10. Claude-proposed Feasibility (read-only repo scan + AskUserQuestion — mandatory)

Feature mode: scan the repo read-only (`Glob` / `Grep` over `backend/`, `extension/`, `deploy/`, `docs/features/`, `docs/architecture-map.md`) for adjacent shipped features with similar tech or workflow. Early in the project there may be none — say so plainly and base the estimate on the planned stack in `CLAUDE.md` and on the user's answers, not on invented precedent.

Product mode: no repo scan — there is nothing to scan and the product is not "feasible" the way a feature is. Base the three checkboxes on the planned stack in `CLAUDE.md`, the user's skills from Phase 2, and the plan's total estimate; **Time** compares the plan's total (10–11 weeks) with the user's real weekly capacity.

Propose 3 checkboxes, each with a rationale:
- **Tech** ☑/☐ — "similar to <feature> in <module>" or "planned stack covers it: <reason>". The rationale in the brief stays product-level ("the planned storage already supports this kind of search"); stack names from the scan stay in session memory.
- **Skills** ☑/☐ — what the developer has already done; note learning curves (e.g. browser extension APIs).
- **Time** ☑/☐ — compare with the epic's estimate in the plan.

Ask per checkbox (or one batch): confirm ☑ / flip to ☐ with a reason / TBD.

### 11. Recommendation (AskUserQuestion — mandatory)

Claude picks one of the three approaches and writes a 3–5 sentence rationale. It MUST cite:
- Feasibility state (§11);
- ≥1 synthesis-matrix cell (§8);
- ≥1 competitive gap (§6);
- the sharpest devil's-advocate vector (§10) and how the chosen approach survives it.

Ask: accept the recommendation / pick a different approach / mark as TBD.

**Product mode adds "Складові продукту".** From the accepted approach, Claude derives the capability list: one row per capability — name (the future feature slug, kebab-case), the user outcome in one sentence, the "Межі v1" boundary it belongs to, and the plan epic it comes from (or "new", if the approach added it; or a note if the approach dropped an epic). Capabilities that are infrastructure, not user outcomes (the skeleton, the job queue as such, backups, monitoring) are listed in a second short table "Фундамент і експлуатація" with the note that they go to `map-architecture` and ADRs, not to feature briefs. Confirm the list in the same `AskUserQuestion` as the recommendation, or in one more call if it is long (accept / edit rows / drop rows).

At **easy** depth, Phases 3, 10 and 11 are one `AskUserQuestion` call.

### 11.5. ExitPlanMode handoff (plan → execute)

Everything above lives in session memory. Now call `ExitPlanMode` with a plan:

1. Feature mode: create `docs/features/<slug>/` if absent. Product mode: target is `docs/idea-brief.md`.
2. Copy `./templates/idea-brief.md` → the target path.
3. Append `pending_glossary_lines` to `docs/CONTEXT.md` (bootstrap it from the `fix-term` template if missing).
4. Fill the 14 sections + Related + DoD self-check from session memory (Phases 1–11), in Ukrainian.
5. Set frontmatter: `status: Confirmed`, `feasibility_state: confirmed`, `depth`, `updated_at`.
6. Run the Phase 13 self-check.
7. Propose a commit and print the handoff block.

If `ExitPlanMode` is unavailable, skip this step and go to Phase 12.

### 12. Execute: write the brief (no questions)

- Feature mode: `mkdir` `docs/features/<slug>/` if absent. Product mode: no folder.
- Copy the template → the target path. Product mode: rename §4 to "Чому цей продукт", fill the "Складові продукту" block (it is in the template as an HTML-commented block; uncomment it), set `epic: product`, `feature_size: n/a`. Feature mode: delete the "Складові продукту" block, keep `feature_size` as a placeholder.
- **Glossary.** If `pending_glossary_lines` is non-empty: if `docs/CONTEXT.md` is missing, copy `.claude/skills/fix-term/templates/CONTEXT.md` there and prune empty H2s except `## Glossary`. Append each two-line entry under `## Glossary` (alphabetically by the English term if the section is sorted, else at the end). Never rewrite existing entries. Set `updated_at: <today>`. This is the `fix-term` file protocol (steps 3, 7–10) applied without its questions, because the questions already ran in Phase 3.
- Fill sections 1–14 + Related + DoD self-check. Remove template HTML comments that only instruct the filler; keep the `Why:` comment. Frontmatter:
  - `status: Confirmed`
  - `feasibility_state: confirmed`
  - `updated_at: <today>`, `depth: <level>`
  - `feature_size` stays a placeholder — `classify-size` sets it.

Parked approaches (the 2 not recommended) go to §13 with a reason and a revisit trigger.

### 13. Self-check vs DoD

Run all checks (Read + Grep over the written file):
- **14 sections present** — 1–14 + Related + DoD self-check filled (product mode: plus "Складові продукту" with ≥1 row per epic of the plan, or an explicit "dropped" note).
- **Product brief linked** — feature mode with `docs/idea-brief.md` present: "Пов'язане" links it and names the "Складові продукту" row.
- **No implementation terms in the body.** Build the check at run time, excluding frontmatter and HTML comments:
  1. Grep for every name from the Phase 0 denylist (the map's Stack section / the candidate table), word boundaries on. Any hit → rewrite the sentence in product language.
  2. Grep for numeric engineering targets (`ms`, `p9\d`, `dims?`, `SLO`, `QPS`). Any hit → move to §14 Open questions as "for the PRD".
  3. Read the body once more for grey-zone words (English technical nouns not in `## Glossary` and not in `second-brain.md`). Apply the audience test. Words Claude cannot decide go to **one** `AskUserQuestion` batch: keep / replace with plain wording / move to §14.
- **Word budget** per depth (see Depth regulator), body only. If over: shorten §8 bullets and the §9 pros/cons table first, then §10. Never drop a competitor row from §6 or a field from a §7 approach — that is the research the depth asked for.
- **Rationale citations** — §12 cites §6 (1 gap) + §8 (1 cell) + §10 (1 vector) + §11 (Feasibility).
- **Language** — headings and prose are Ukrainian; domain terms in English per `CLAUDE.md`.

If a check fails, fix the section and re-check.

### 14. Propose commit + handoff

Suggest (do not run) a commit, in the project style — short imperative subject:

```
Add idea-brief for <slug>
```

Product mode: `Add product idea-brief`.

If `docs/CONTEXT.md` changed, include it in the same commit.

Then print the handoff block per [`../_shared/handoff.md`](../_shared/handoff.md): *What I did* · *Review before continuing* (the brief, `docs/CONTEXT.md` if changed) · *Run next*:
- feature mode: `/write-prd <slug>` (if `write-prd` is not yet in `.claude/skills/`, say it must be copied from the course toolkit first). `feature_size` is not set at this stage; do not report a default size — `write-prd` establishes it.
- product mode: `/map-architecture` (role T) to fix the foundation and name the technical dependencies between capabilities, then `/roadmap` (role P) to order them, then `/interview <first slug>`. Name the skills that are not yet in `.claude/skills/`.

If the recommendation looks like a hard-to-reverse technical choice, note it in §14 Open questions. Do not open an ADR here.

## Definition of Done

- The brief created at the mode's path, in Ukrainian; commit proposed. Product mode: "Складові продукту" filled and confirmed.
- All 14 sections filled (no empty H2; `<!-- TBD: ... -->` allowed where honestly missing).
- No implementation terms in the body (Phase 13 check: Stack denylist, numeric targets, grey-zone batch resolved).
- Body within the word budget of the chosen depth.
- Frontmatter `status: Confirmed`, `feasibility_state: confirmed`, `depth` set.
- §12 rationale cites §11 Feasibility + ≥1 cell of §8 + ≥1 gap of §6 + the top vector of §10.
- Glossary lines, if any, appended to `docs/CONTEXT.md` with `updated_at` stamped; no question was asked after `ExitPlanMode` except the grey-zone batch.
- `AskUserQuestion` checkpoints really fired: medium / hard — Phases 0.25, 1, 2, 3 (when terms exist), 10, 11; easy — Phases 0.25, 1, 2 and the merged final confirm. If any answer was fabricated, the artifact is not DoD-valid.
- Handoff block printed; next stage `write-prd`.

## Anti-patterns

- **Inventing competitors.** `N/A — <reason>` is better than fake research. Rows need real URLs, features and value ratings.
- **Scoring instead of arguing.** No RICE, no made-up numbers for a one-user product. §4 says in words why this is next.
- **Implementation terms in the brief body** (stack names, index types, embeddings as a storage choice, latency targets). This is a product brief. Tech lives in the PRD NFRs, `sad.md` and ADRs.
- **Hardcoding the term list in this skill.** The denylist comes from `docs/architecture-map.md` or the `map-architecture` candidate table, the allowlist from `docs/CONTEXT.md` and `docs/initial-idea/`. Update those, not this file.
- **One approach in §7.** Always three (Simplicity / Differentiation / Balanced).
- **Skipping the multi-perspective review.** All three lenses are needed.
- **Devil's advocate in the same context.** Phase 8 must use a clean-context agent.
- **Feasibility without a repo scan.** "Tech ☑ — we know how" without evidence is a guess.
- **Recommendation without the 4 citations.**
- **Asking the user after `ExitPlanMode`** (except the grey-zone batch). Glossary questions belong to Phase 3.
- **Invoking `fix-term` from inside this skill.** Apply its line format and file protocol; do not run its interactive protocol a second time.
- **Proposing an ADR at the end.** ADRs belong to `architecture-design`.
- **Transcript dump.** §13 is a structured table, not a chat log.
- **Solution in §2 Problem.** §2 is the problem only.
- **Re-asking what the product docs already say.** Cite `docs/initial-idea/**`; ask only for gaps.
- **Fabricating answers under Auto Mode.**
- **Writing files before Phase 12.**
- **Cutting research to fit a length.** Shorten synthesis (§8, §9), not evidence (§6, §7).
- **Feature brief that repeats the product brief.** With `docs/idea-brief.md` present, §6 and §8 hold the delta and a link, not a copy.
- **Infrastructure in "Складові продукту".** The skeleton, queue, backups and monitoring are foundation work for `map-architecture`, not capabilities with a user outcome.
- **Product mode without `second-brain.md` as §1.** The initial idea is the baseline; the brief adds research to it, it does not replace it.

## Template

→ [./templates/idea-brief.md](./templates/idea-brief.md)

## Example invocation

> **User:** "/interview web-page-slice"
>
> **— Read-only —**
> 1. **Phase 0** — reads the template, `second-brain.md`, Epic 1 of the plan, the candidate table of `map-architecture`; no `docs/CONTEXT.md` yet.
> 2. **Phase 0.25** — depth question; user picks medium.
> 3. **Phase 1** — offers Epic 1's text as an option; user rewrites it in their own words; stored verbatim.
> 4. **Phase 2** — batch 1: "Яку сторінку ти зберігаєш найчастіше і як потім шукаєш?", "Що означає «знайшов» — за скільки секунд?"; batch 2: constraints and fit.
> 5. **Phase 3** — proposes lines for `record` and `save reason` with definitions from the answers; user accepts both. Stored as `pending_glossary_lines`.
> 6. **Phase 4** — `researcher` returns 4 rows (read-later and bookmark-search tools) + the gap "no search by the reason for saving".
> 7. **Phase 5** — `strategist` returns A (save + text search), B (save + "why" + semantic search), C (B without auto-summaries).
> 8. **Phase 6** — `analyst` returns per-lens bullets + the matrix.
> 9. **Phase 7** — trade-offs + 6 edge cases.
> 10. **Phase 8** — `devils-advocate` (Mode B) returns 7 vectors; top: "pages behind login save as empty".
> 11. **Phase 10** — repo has no code yet; Feasibility based on the planned stack; user confirms.
> 12. **Phase 11** — recommends C; rationale cites Feasibility, a UX cell, the competitive gap, the login-wall vector; user accepts.
>
> **— Execute —**
> 13. **Phase 12** — creates `docs/features/web-page-slice/idea-brief.md`; bootstraps `docs/CONTEXT.md` and appends the two glossary lines. No questions.
> 14. **Phase 13** — Stack grep is clean; the word "парсер" is grey-zone → one batch, user replaces it with "витягування тексту"; word count 2100 ≤ 2500.
> 15. **Phase 14** — proposes `Add idea-brief for web-page-slice`; prints the handoff block with `/write-prd web-page-slice`.
