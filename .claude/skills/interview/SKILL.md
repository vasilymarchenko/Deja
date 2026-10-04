---
name: interview
description: >-
  SDLC ideation phase for Deja — the single entry point from a raw idea to a confirmed
  idea-brief. Runs a Socratic interview, competitive research (researcher agent), three
  strategic approaches (strategist agent), a multi-perspective review (analyst agent), a
  clean-context devil's advocate (devils-advocate agent), then Claude-proposed RICE and
  Feasibility that the user confirms. Has a depth dial (easy / medium / hard). Output:
  docs/features/<slug>/idea-brief.md (15 sections, ≤5 pages, Ukrainian). Triggers on
  "/interview <slug>", "raw idea", "capture an idea", "interview a feature", "brief for X",
  "idea brief", "new feature X", "ideation for <slug>", "start epic N", "інтерв'ю фічі",
  "бриф ідеї", "нова фіча". Next stage: write-prd. Does not write ADRs — that is
  architecture-design.
---

# Skill: interview (SDLC ideation phase)

One autonomous, Claude-driven protocol for the ideation phase. Output: a single `docs/features/<slug>/idea-brief.md` with 15 sections (≤5 pages). No separate brainstorm or initiatives files.

Adapted from the course toolkit (`agentic-engineering-course/sdlc/plugin/skills/interview`). Changes for Deja: no greenfield mode (the repo exists), agents are local (`.claude/agents/`), the glossary is `docs/CONTEXT.md`, the brief is written in Ukrainian, the anti-pattern list uses the Deja stack.

## Why one skill

Claude does the research (competitors, approaches, perspectives, devil's advocate, RICE, Feasibility); the user confirms through `AskUserQuestion`. The user never types RICE numbers from nothing ("calculator game"). ADRs are not part of ideation; they appear at `architecture-design`. Ideation stays pure product.

## Owner

The idea author. In this project: the solo developer (Vasyl), who is also PM and Tech Lead.

## When to use

- "capture an idea <slug>", "brief for <feature>", "raw idea for <feature>", "ideation for <slug>".
- Starting an epic from `docs/initial-idea/second-brain-plan.md` ("start epic 1").
- `/interview <slug>` as the explicit call.
- Skip if `docs/features/<slug>/idea-brief.md` already exists with `status: Confirmed` and is fresh (≤2 weeks). Update it instead of rewriting.

## Inputs

- `<slug>` — kebab-case, short, no epic number (`web-page-slice`, `chrome-extension`). If missing, suggest 2–3 options from the idea.
- Product context (always read): `docs/initial-idea/second-brain.md`, `docs/initial-idea/second-brain-plan.md` (the matching epic), `CLAUDE.md`.
- Optional: `docs/roadmap.md`, `docs/architecture-map.md`, `docs/CONTEXT.md`, prior notes or links from the user.

## Language

- `idea-brief.md` — **Ukrainian** (product doc, see `CLAUDE.md` → Language requirements). Plain language, no code identifiers. Technical terms that have no Ukrainian form stay in English (`RICE`, `Feasibility`, `Approach A`).
- `AskUserQuestion` text — Ukrainian, per [`../_shared/ask-style.md`](../_shared/ask-style.md).
- Agent prompts — English. The user's answers may be Ukrainian; quote them as they are.

## Depth regulator (first checkpoint)

The first `AskUserQuestion` of every run picks the depth. It is written to the brief frontmatter (`depth:`).

- **easy** — 3–4 checkpoints. Phase 1 (idea); Phase 2 as one batch (2–3 questions: user and pain · success criterion · scope); Phases 9–11 merged into one final confirm (Claude proposes RICE + Feasibility + recommendation together). Phases 4–8 run by Claude itself, without agents: 1–2 quick searches for §6, three one-paragraph approaches, its own list of 3–5 risks. All 15 sections are filled, but compactly.
- **medium** (default) — the full protocol below.
- **hard** — the full protocol + one extra Socratic batch in Phase 2 + a wider §6 (5+ competitors).

Easy does not relax honesty: its 3–4 `AskUserQuestion` calls are real. Fabricating answers is forbidden at every depth.

## Mode handling

**Plan-mode native.** Phases 0–11 are read-only (Read, Grep, Glob, WebSearch, Agent, AskUserQuestion). At 11 → 12 the skill calls `ExitPlanMode` with the synthesized plan; Phase 12 then writes files.

If the session is not in plan mode (`ExitPlanMode` unavailable), Phase 12 runs right after Phase 11. Keep all content in session memory until Phase 12 either way.

**Auto Mode does not cancel checkpoints.** The `AskUserQuestion` calls in Phases 0.25, 1, 2, 9, 10, 11 are a data-input protocol, not "clarifying questions". Auto Mode covers "should I proceed" pauses between phases, not data input. If `AskUserQuestion` is denied, stop and tell the user; do not work around it.

## AskUserQuestion style

Every question follows [`../_shared/ask-style.md`](../_shared/ask-style.md). Short version:

1. **Ukrainian** labels and descriptions. Technical names (RICE, R/I/C/E, Feasibility ☑/☐, Approach A/B/C) stay as they are; actions are Ukrainian ("Прийняти Approach C", "Зменшити E", "Позначити TBD").
2. **`question`** — 3–4 sentences: CONTEXT (which phase, a 1-line recap of what is collected) · WHY IT MATTERS (what breaks with a wrong answer) · WHAT TO LOOK AT before choosing.
3. **Option `description`** — 3–5 sentences: what changes in the brief and which later phases it affects · what the option means in plain words · the hidden trade-off, stated right there. Example: "Mark recommendation as TBD" → "write-prd will refuse to run until status is Confirmed; this blocks the pipeline for this feature".
4. **Forbidden:** terse English labels ("Confirm", "Adjust"), one-line descriptions, unexplained jargon, trade-offs hidden in a follow-up.

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

## Protocol

**Phases 0–11 read-only. Phase 11.5 = ExitPlanMode. Phases 12–14 write, self-check, propose a commit.**

### 0. Pre-plan setup (read-only)

- **Read** `./templates/idea-brief.md` into session memory. Do not copy it yet.
- **Read** product context: `docs/initial-idea/second-brain.md`, the matching epic in `docs/initial-idea/second-brain-plan.md`, and `docs/roadmap.md` / `docs/architecture-map.md` if present.
- **Read** `docs/CONTEXT.md` if it exists; keep `## Glossary` as session state.
- **Check** `docs/features/<slug>/idea-brief.md`. If it exists with `status: Confirmed` and is ≤2 weeks old, stop and offer an update instead.
- **No Write / Edit / mkdir.**

### 0.25. Depth checkpoint (AskUserQuestion — mandatory)

One question: the depth (see "Depth regulator"). Confirm the slug in the same call if the user did not give one. easy → short route; medium / hard → full route.

### 1. Idea capture (AskUserQuestion — mandatory)

One `AskUserQuestion` for the raw paragraph: "Опиши ідею в 1–3 реченнях своїми словами." Offer the epic's text from the plan as one option, so the user can accept or rewrite it. Store the answer verbatim as §1. Do not edit it — it is the baseline.

### 2. Socratic deep dive (AskUserQuestion — mandatory)

Pick 3–5 questions from 5 categories, based on the shape of the idea:
- **Problem clarity** — what exactly hurts, for whom, how often.
- **Solution validation** — why this solution, what was tried before.
- **Success criteria** — what "it worked" means, as a concrete metric.
- **Constraints** — time, budget, solo capacity, dependencies on earlier epics.
- **Strategic fit** — how it fits the plan and the product goal ("find by why I saved it").

Ask in batches of 2–3, not all at once. Do not ask what the product docs already answer; cite them instead and ask only for gaps or contradictions.

### 3. Glossary capture (deferred fix-term)

For each new domain word in the user's answers, add it to the session list `pending_glossary_terms`. **Do not** call `fix-term` now — it writes `docs/CONTEXT.md`, which is not allowed before Phase 12. Skip generic tech words (HTTP, JSON, queue, cache, database).

### 4. Competitive research (`researcher` agent, read-only)

Dispatch `researcher`. Prompt inlines: raw idea, deep-dive answers, `docs/CONTEXT.md` path, and the depth (medium: 3–5 rows; hard: 5+ rows). It returns a table **Product · URL · Features · Value (1–5) · Gap**, every row footnoted with date and search query, plus one synthesis line (the biggest gap). Store it as the §6 draft.

No user input here. If the agent reports `RESEARCH_LIMITED`, keep that note in §6; never invent rows.

### 5. Strategic approaches (`strategist` agent, read-only)

Dispatch `strategist`. Prompt inlines: raw idea, deep-dive answers, the §6 synthesis line. It returns three genuinely different approaches:
- **A — Simplicity:** the shortest path, MVP, fewest moving parts.
- **B — Differentiation:** the wow-factor / moat / unique angle.
- **C — Balanced:** the trade-off between A and B.

Each has **Name** (3–5 words) · **Thesis** (1 sentence, product language, no tech names) · **For whom** (segment from §3) · **Outcome metric** (baseline → target) · **Key trade-off** · **Effort signal** S / M / L. Store as the §7 draft.

### 6. Multi-perspective review (`analyst` agent, read-only)

Dispatch `analyst`. Prompt inlines: raw idea + the three approaches from Phase 5. It returns, for each lens (**Engineer** — abstract, no library or DB names; **Executive**; **UX**), 3–5 bullets across the approaches, then the 3×3 matrix (+/0/−, ≤6-word reason per cell) and one synthesis line per approach. Store as the §8 draft.

### 7. Trade-offs + edge cases (synthesis, read-only)

Claude writes into session memory (no user input):
- Pros / cons table per approach.
- 5–8 edge cases any approach must handle (data, integrations, failure modes, operations).

### 8. Devil's advocate (`devils-advocate` agent, Mode B, clean context)

Dispatch `devils-advocate`. The prompt says: **"Mode B — no PRD yet"** and inlines the raw idea + the three approaches (mark the leading one, if any). Do not inline Claude's own optimism (trade-offs, matrix). It returns 5–10 attack vectors with trigger / breaks / signal.

The sharpest vector goes to §10 Risks. The rest feed §9 Edge cases.

### 9. Claude-proposed RICE (AskUserQuestion — mandatory)

Claude computes R / I / C / E from upstream sections:
- **Reach** ← §3 Users (users affected per quarter). For a personal product, state that honestly (e.g. 1 user now, N beta users later) and explain the scale used.
- **Impact** ← §2 problem severity + Executive bullets (0.25 / 0.5 / 1 / 2 / 3).
- **Confidence** ← number of TBDs and open questions: many unresolved → 0.5; all facts concrete → 1.0.
- **Effort** ← the recommended-candidate Effort signal from §7 (S = 1–2 person-weeks, M = 3–5, L = 6–12). Account for solo, part-time work.

Compute `R × I × C / E`. Ask per number (4 questions, or 1 multi-question batch) with options: confirm N / adjust higher / adjust lower / mark TBD. The rationale in the brief cites the upstream section.

### 10. Claude-proposed Feasibility (read-only repo scan + AskUserQuestion — mandatory)

Scan the repo read-only (`Glob` / `Grep` over `backend/`, `extension/`, `deploy/`, `docs/features/`, `docs/architecture-map.md`) for adjacent shipped features with similar tech or workflow. Early in the project there may be none — say so plainly and base the estimate on the planned stack in `CLAUDE.md` and on the user's answers, not on invented precedent.

Propose 3 checkboxes, each with a rationale:
- **Tech** ☑/☐ — "similar to <feature> in <module>" or "planned stack covers it: <reason>".
- **Skills** ☑/☐ — what the developer has already done; note learning curves (e.g. Chrome extension APIs).
- **Time** ☑/☐ — compare with the epic's estimate in the plan.

Ask per checkbox (or one batch): confirm ☑ / flip to ☐ with a reason / TBD.

### 11. Recommendation (AskUserQuestion — mandatory)

Claude picks one of the three approaches and writes a 3–5 sentence rationale. It MUST cite:
- RICE score (§11);
- Feasibility state (§12);
- ≥1 synthesis-matrix cell (§8);
- ≥1 competitive gap (§6).

Ask: accept the recommendation / pick a different approach / mark as TBD.

### 11.5. ExitPlanMode handoff (plan → execute)

Everything above lives in session memory. Now call `ExitPlanMode` with a plan:

1. Create `docs/features/<slug>/` if absent.
2. Copy `./templates/idea-brief.md` → `docs/features/<slug>/idea-brief.md`.
3. Apply `pending_glossary_terms` via the `fix-term` skill to `docs/CONTEXT.md`.
4. Fill the 15 sections + Related + DoD self-check from session memory (Phases 1–11), in Ukrainian.
5. Set frontmatter: `status: Confirmed`, `value_score.{rice,state,confirmed_at}`, `feasibility_state: confirmed`.
6. Run the Phase 13 self-check.
7. Propose a commit and print the handoff block.

If `ExitPlanMode` is unavailable, skip this step and go to Phase 12.

### 12. Execute: write the brief

- `mkdir` `docs/features/<slug>/` if absent.
- Copy the template → `docs/features/<slug>/idea-brief.md`.
- Apply pending glossary terms: invoke `fix-term` once per term (it asks the user for the definition; offer the phrasing from the interview as an option). If there are none, skip.
- Fill sections 1–15 + Related + DoD self-check. Remove template HTML comments that only instruct the filler; keep the `Why:` comment. Frontmatter:
  - `status: Confirmed`
  - `value_score.rice: <N>`, `value_score.state: confirmed`, `value_score.confirmed_at: <today YYYY-MM-DD>`
  - `feasibility_state: confirmed`
  - `updated_at: <today>`, `depth: <level>`
  - `feature_size` stays a placeholder — `classify-size` sets it.

Parked approaches (the 2 not recommended) go to §14 with a reason and a revisit trigger.

### 13. Self-check vs DoD

Run all checks (Read + Grep over the written file):
- **15 sections present** — 1–15 + Related + DoD self-check filled.
- **No tech terms in the body.** Regex, excluding the DoD self-check block and frontmatter:
  `\b(Postgres|PostgreSQL|pgvector|FastAPI|SQLAlchemy|Alembic|Pydantic|trafilatura|Caddy|Docker|Compose|Hetzner|Redis|Kafka|JSONB|SQL|BM25|HNSW|p99)\b`. Word boundaries matter.
- **Length ≤ 5 pages** (~2200 words ±10%). If over, compress §7 paragraphs and the §6 table.
- **Rationale citations** — §13 cites §6 (1 gap) + §8 (1 cell) + §11 (RICE) + §12 (Feasibility).
- **Language** — headings and prose are Ukrainian.

If a check fails, fix the section and re-check.

### 14. Propose commit + handoff

Suggest (do not run) a commit, in the project style — short imperative subject:

```
Add idea-brief for <slug>
```

If `docs/CONTEXT.md` changed, include it in the same commit.

Then print the handoff block per [`../_shared/handoff.md`](../_shared/handoff.md): *What I did* · *Review before continuing* (`docs/features/<slug>/idea-brief.md`, `docs/CONTEXT.md` if changed) · *Run next* = `/write-prd <slug>` (if `write-prd` is not yet in `.claude/skills/`, say it must be copied from the course toolkit first).

If the recommendation looks like a hard-to-reverse technical choice, note it in §15 Open questions. Do not open an ADR here.

## Definition of Done

- `docs/features/<slug>/idea-brief.md` created, in Ukrainian; commit proposed.
- All 15 sections filled (no empty H2; `<!-- TBD: ... -->` allowed where honestly missing).
- No tech terms in the body (Phase 13 regex).
- Length ≤ 5 pages (~2200 words ±10%).
- Frontmatter `status: Confirmed`, `value_score.state: confirmed`, `feasibility_state: confirmed`, `confirmed_at: <date>`.
- §13 rationale cites §11 RICE + §12 Feasibility + ≥1 cell of §8 + ≥1 gap of §6.
- `AskUserQuestion` checkpoints really fired in Phases 0.25, 1, 2, 9, 10, 11. If any answer was fabricated, the artifact is not DoD-valid.
- Handoff block printed; next stage `write-prd`.

## Anti-patterns

- **Inventing competitors.** `N/A — <reason>` is better than fake research. Rows need real URLs, features and value ratings.
- **User-input RICE.** Claude proposes from upstream sections; the user confirms or adjusts.
- **Tech terms in the brief body** (Postgres, pgvector, FastAPI, embeddings as a storage choice, latency targets). This is a product brief. Tech lives in the PRD NFRs, `sad.md` and ADRs.
- **One approach in §7.** Always three (Simplicity / Differentiation / Balanced).
- **Skipping the multi-perspective review.** All three lenses are needed.
- **Devil's advocate in the same context.** Phase 8 must use a clean-context agent.
- **Feasibility without a repo scan.** "Tech ☑ — we know how" without evidence is a guess.
- **Recommendation without the 4 citations.**
- **Proposing an ADR at the end.** ADRs belong to `architecture-design`.
- **Transcript dump.** §14 is a structured table, not a chat log.
- **Solution in §2 Problem.** §2 is the problem only.
- **Re-asking what the product docs already say.** Cite `docs/initial-idea/**`; ask only for gaps.
- **Fabricating answers under Auto Mode.**
- **Writing files before Phase 12.**

## Template

→ [./templates/idea-brief.md](./templates/idea-brief.md)

## Example invocation

> **User:** "/interview web-page-slice"
>
> **— Read-only —**
> 1. **Phase 0** — reads the template, `second-brain.md`, Epic 1 of the plan; no `docs/CONTEXT.md` yet.
> 2. **Phase 0.25** — depth question; user picks medium.
> 3. **Phase 1** — offers Epic 1's text as an option; user rewrites it in their own words; stored verbatim.
> 4. **Phase 2** — batch 1: "Яку сторінку ти зберігаєш найчастіше і як потім шукаєш?", "Що означає «знайшов» — за скільки секунд?"; batch 2: constraints and fit.
> 5. **Phase 3** — "record" and "save reason" go to `pending_glossary_terms` (glossary terms are English, see `CLAUDE.md`).
> 6. **Phase 4** — `researcher` returns 4 rows (read-later and bookmark-search tools) + the gap "no search by the reason for saving".
> 7. **Phase 5** — `strategist` returns A (save + text search), B (save + "why" + semantic search), C (B without auto-summaries).
> 8. **Phase 6** — `analyst` returns per-lens bullets + the matrix.
> 9. **Phase 7** — trade-offs + 6 edge cases.
> 10. **Phase 8** — `devils-advocate` (Mode B) returns 7 vectors; top: "pages behind login save as empty".
> 11. **Phase 9** — RICE proposed; user lowers Effort → confirmed.
> 12. **Phase 10** — repo has no code yet; Feasibility based on the planned stack; user confirms.
> 13. **Phase 11** — recommends C; rationale cites RICE, Feasibility, a UX cell, the competitive gap; user accepts.
>
> **— Execute —**
> 14. **Phase 12** — creates `docs/features/web-page-slice/idea-brief.md`; `fix-term` adds "record" and "save reason" to `docs/CONTEXT.md`.
> 15. **Phase 13** — self-check passes.
> 16. **Phase 14** — proposes `Add idea-brief for web-page-slice`; prints the handoff block with `/write-prd web-page-slice`.
