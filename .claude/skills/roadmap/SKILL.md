---
name: roadmap
description: >-
  Use to keep the portfolio layer above individual features — one living docs/roadmap.md of
  outcomes, structured Now / Next / Later / Shipped, that links to per-feature docs without
  duplicating them. Triggers on "roadmap", "what's next", "prioritize the roadmap", "add to the
  roadmap", "show the roadmap", "/roadmap", "роадмеп", "що далі", "пріоритети", "додай у roadmap".
  First run takes the "Складові продукту" table of docs/idea-brief.md (product mode of the
  interview skill) and places every capability into a horizon. Captures a later candidate as an
  outcome/problem, promotes/demotes between horizons, renders the board. No RICE, no dates:
  order inside Next is the owner's decision, argued in one line per row. write-prd promotes an
  item to Now; ship-feature moves it to Shipped. Output: docs/roadmap.md (repo root, Ukrainian).
---

# Skill: roadmap

The **portfolio layer** above the per-feature pipeline. The pipeline builds one feature at a time under `docs/features/<slug>/`; `roadmap` is the single living view *across* features — what we are doing now, what is next, what is directional later — kept at **outcome altitude** (the "why" / problem), with each item linking to its feature folder rather than restating the PRD.

A roadmap is **direction, not a promise**, and **not a release plan**: feature-and-date roadmaps project false certainty, go stale fastest the further out they reach, and commit to solutions before discovery. So this roadmap encodes *decreasing certainty over time* and never carries dates. Repo-level utility — one file serves the whole repo.

Adapted from the course toolkit (`agentic-engineering-course/sdlc/plugin/skills/roadmap`). Changes for Deja: **no RICE** (one user — Reach is always 1, the score ranks nothing; the order inside Next is argued in one line per row instead); the first run is seeded from the product brief's "Складові продукту"; the file is Ukrainian (product doc, see `CLAUDE.md` → Language requirements); no PR links in Shipped (no PRs in this repo — link the changelog entry).

## Owner

Whoever owns product direction. In this project: the solo developer.

## Inputs

- (Optional) a candidate to capture (an outcome / problem in one line), or an action: prioritize / promote / demote / render.
- `docs/idea-brief.md` — the product brief; its "Складові продукту" and "Фундамент і експлуатація" tables seed the board on the first run.
- `docs/features/*/` — to link items to existing feature folders and read their status.
- `docs/initial-idea/second-brain-plan.md` — the plan's vertical-slice order is the default order of Next on the first run.

## Language

`docs/roadmap.md` is **Ukrainian**: a user of the product could read it. Slugs, file paths and skill names stay English. `AskUserQuestion` text is Ukrainian, per [`../_shared/ask-style.md`](../_shared/ask-style.md).

## Protocol

1. **Lazy-create.** If `docs/roadmap.md` is absent, copy [`./templates/roadmap.md`](./templates/roadmap.md) there (the non-commitment disclaimer + Now / Next / Later / Shipped + Фундамент, **each rendered as a table**, one row per item).
2. **Seed from the product brief (first run only).** If `docs/idea-brief.md` exists and the board is empty: every row of "Складові продукту" becomes a Next or Later row (outcome = the row's user outcome; slug in the row); every row of "Фундамент і експлуатація" goes to the **Фундамент** table with its destination (`map-architecture`, ADR, task). Propose the split and the Next order (default: the plan's vertical-slice order — the first capability that closes "зберіг → знайшов" first) in one `AskUserQuestion`; the user confirms or reorders. If there is no product brief, say so and build from the user's candidate.
3. **The three horizons — the content type changes per horizon** (the load-bearing rule):
   - **Now** — committed work whose `docs/features/<slug>/PRD.md` exists and is being built. Row = outcome one-liner + link to the feature folder + status (designing / implementing / review). Promoted here only after `write-prd`.
   - **Next** — problems / opportunities deliberately **not yet spec'd**. Row = outcome one-liner + the intended slug + one line "чому в цьому порядку". No feature folder yet. Ordered by the owner, top = next to pull.
   - **Later** — outcomes / themes, directional only (v2 web UI, v3 bot and MCP, proactivity). No slugs, no reasons.
   - **Фундамент** — work without a user outcome (skeleton, queue, backups, monitoring). Row = what + where it is handled. Not a horizon; listed so it is not lost and not mistaken for a feature.
4. **Capture a candidate** → add to **Next** (or Later) as an outcome / problem. **Never** write a solution or feature detail here — that is `write-prd`'s job when the item is pulled into Now.
5. **Prioritize.** Order Next by hand. Each row carries one line of reasoning (dependency on an earlier slice, unlocks dogfooding, biggest gap from the product brief §6). Changing the order = changing the line.
6. **Promote / demote.** Move rows between horizons as certainty changes. Promote Next → Now only when the item is about to be `write-prd`'d. Demote freely; far-out items stay coarse.
7. **Render / write + commit + handoff.** Update `docs/roadmap.md`, set `updated_at`, propose commit `Update roadmap: <what changed>` (first run: `Add roadmap`). Then emit the stage-handoff block per [`../_shared/handoff.md`](../_shared/handoff.md) (utility variant) — *What I did* + *Review* (`docs/roadmap.md`) + *Run next*: after the first run `/map-architecture` (say it must be copied from the course toolkit if absent), otherwise resume your backbone stage; `/clear` optional.

## Sync hooks (delivery keeps it current)

- **`write-prd`** registers its feature on the roadmap and promotes the row to **Now** (outcome + link to `docs/features/<slug>/` + status). A brand-new feature with no prior candidate is added directly to Now.
- **`ship-feature`** moves the row to **Shipped** (date + link to the feature folder + the `CHANGELOG.md` entry) and removes it from Now.

## Definition of Done

- `docs/roadmap.md` exists with the disclaimer + Now / Next / Later / Shipped / Фундамент, rows at **outcome altitude**, each Now / Shipped row **linking** to its `docs/features/<slug>/`.
- Next is hand-ordered with one reason per row; no scores, no dates anywhere; no feature-level detail in Later.
- `updated_at` reflects the change.

## Anti-patterns

- **A feature-and-date roadmap.** Rows are outcomes; dates are absent; the solution lives in the PRD.
- **Scoring instead of arguing.** No RICE. One line of reasoning per Next row.
- **Duplicating the PRD** on the roadmap. Link to `docs/features/<slug>/`; the roadmap holds the *why*.
- **Over-detailing Later.** Far-out rows are directional one-liners.
- **Promoting to Now before `write-prd`.** Now = committed + spec'd.
- **Infrastructure as a Next row.** Skeleton, queue, backups go to Фундамент.
- **Letting it rot.** `write-prd` / `ship-feature` keep it live.

## Template

→ [`./templates/roadmap.md`](./templates/roadmap.md)
