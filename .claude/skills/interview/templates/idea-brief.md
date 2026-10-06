---
status: Draft | Confirmed | Frozen
owner: "Vasyl Marchenko"
reviewers: []
updated_at: "<YYYY-MM-DD>"
feature_size: <XS|S|M|L|XL>     # set by classify-size, not here; "n/a" in product mode
stage: "01"
depth: easy | medium | hard     # interview depth used
epic: "<epic number from docs/initial-idea/second-brain-plan.md, none, or product>"
feasibility_state: proposed | confirmed
---

<!-- Stage 01 → .claude/skills/interview/SKILL.md -->
<!-- Why: capture the idea before it is forgotten or retold incorrectly -->

<!-- Filler rules (Claude self-check, remove this comment when filling):
     The body is UKRAINIAN, plain language, no code identifiers.
     Product terms only — the audience test in SKILL.md: a word stays if the user needs it to
     describe what the product does for them. Allowed by source: docs/CONTEXT.md glossary,
     docs/initial-idea/second-brain.md. Forbidden by source: every name under "## Stack" in
     CLAUDE.md, plus numeric engineering targets. No RICE — priority is argued in §4.
     This is a PRODUCT brief. Tech lives in PRD NFRs, sad.md and ADRs. -->

# Idea Brief — <назва фічі>

## 1. Сира ідея
<1 paragraph, verbatim from the user, phase 1>

## 2. Проблема
<1-3 sentences, facts / numbers>

## 3. Користувачі
<who suffers, how often, segments>

## 4. Чому зараз
<feature mode: what in the plan or in daily use makes this the next step.
 product mode: rename the heading to "Чому цей продукт" — why this product and not an existing tool, why build it now>

## 5. Поза межами
- <bullet>

## 6. Аналіз конкурентів
| # | Продукт · URL | Можливості | Цінність кожної (1-5) | Прогалина |
|---|---|---|---|---|
| 1 | <name> · <url> | <features> | <ratings> | <gap> |

<Footnote per row: date and search query used. End with one line: the biggest gap.>

## 7. Стратегічні підходи

### Approach A — <назва, 3-5 слів>
- **Теза:** <1 sentence, product language>
- **Для кого:** <segment from §3 that benefits most>
- **Метрика результату:** <1 KPI: baseline → target>
- **Головний компроміс:** <1 line>
- **Effort signal:** S / M / L
- **Рекомендовано?** ◯ / ● (filled in §12)

### Approach B — <назва>
<same structure>

### Approach C — <назва>
<same structure>

## 8. Погляд з трьох сторін

### Engineer
- <3-5 bullets: concerns / value / risks — abstract, no library or DB names>

### Executive
- <3-5 bullets>

### UX
- <3-5 bullets>

### Зведена матриця
|            | Engineer | Executive | UX |
|------------|:--------:|:---------:|:--:|
| Approach A | <+/0/−> | <+/0/−> | <+/0/−> |
| Approach B | <+/0/−> | <+/0/−> | <+/0/−> |
| Approach C | <+/0/−> | <+/0/−> | <+/0/−> |

<≤6-word reason in each cell.>

## 9. Компроміси та граничні випадки

### Компроміси підходів
| Підхід | Плюси | Мінуси |
|---|---|---|
| A | <...> | <...> |
| B | <...> | <...> |
| C | <...> | <...> |

### Граничні випадки
- <5-8 items>

## 10. Ризики
- <top devil's advocate vector, phase 8>
- <other risks>

## 11. Feasibility — пропозиція Claude
- [☑/☐] **Tech:** <rationale — adjacent feature from the repo scan, or the planned stack>
- [☑/☐] **Skills:** <rationale>
- [☑/☐] **Time:** <rationale — compare with the epic estimate in the plan>
- **Стан:** proposed | confirmed

## 12. Рекомендація
**Обрано: Approach <X>** — <3-5 sentence rationale>

<Rationale MUST cite: Feasibility from §11, ≥1 matrix cell from §8, ≥1 gap from §6, the top risk from §10 and how the chosen approach survives it.>

**Що ми фіксуємо:** <what this commits us to for the PRD stage>

## 13. Відкладені та відхилені підходи
| # | Підхід | Статус | Причина | Коли повернутися |
|---|---|:---:|---|---|
| <B> | <name> | відкладено | <reason> | <trigger> |
| <C> | <name> | відкладено | <reason> | <trigger> |

## 14. Відкриті питання
- [ ] <question> — відповідальний: <name>, термін: <date>

<!-- PRODUCT MODE ONLY — uncomment in product mode, delete in feature mode.

## Складові продукту
<One row per capability with a user outcome. The name is the future feature slug.
 Derived from the recommended approach in §12. Each epic of the plan appears here,
 or is marked as dropped with a reason.>

| Складова (slug) | Результат для користувача | Межа v1 | Епік плану |
|---|---|---|---|
| <web-page-slice> | <one sentence> | <джерела / інтерфейс / пошук> | <1 / new / dropped: reason> |

### Фундамент і експлуатація
<Work without a direct user outcome: skeleton, job queue, backups, monitoring.
 Goes to map-architecture and ADRs, not to feature briefs.>

| Робота | Епік плану | Куди йде |
|---|---|---|
| <skeleton and deploy> | <0> | map-architecture |

-->

## Пов'язане
- <feature mode: docs/idea-brief.md (product brief) + the "Складові продукту" row this feature is; docs/CONTEXT.md; the epic in docs/initial-idea/second-brain-plan.md; related features>
- <product mode: docs/initial-idea/second-brain.md, docs/initial-idea/second-brain-plan.md, docs/CONTEXT.md>

## DoD self-check
- [ ] 14 розділів заповнено (product mode: + «Складові продукту»)
- [ ] Немає термінів реалізації (перевірка за `## Stack` у CLAUDE.md, числові цілі, сіра зона)
- [ ] Обсяг у межах бюджету слів для обраної глибини (easy 1500 / medium 2500 / hard 3500)
- [ ] Frontmatter status: Confirmed
- [ ] Feasibility підтверджено (state: confirmed)
- [ ] Рекомендація посилається на §6, §8, §10, §11
