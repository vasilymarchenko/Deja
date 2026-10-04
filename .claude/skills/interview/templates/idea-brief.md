---
status: Draft | Confirmed | Frozen
owner: "Vasyl Marchenko"
reviewers: []
updated_at: "<YYYY-MM-DD>"
feature_size: <XS|S|M|L|XL>     # set by classify-size, not here
stage: "01"
depth: easy | medium | hard     # interview depth used
epic: "<epic number from docs/initial-idea/second-brain-plan.md, or none>"
value_score:
  rice: <number>                # computed by Claude
  state: proposed | confirmed
  confirmed_at: "<YYYY-MM-DD>"
feasibility_state: proposed | confirmed
---

<!-- Stage 01 → .claude/skills/interview/SKILL.md -->
<!-- Why: capture the idea before it is forgotten or retold incorrectly -->

<!-- Filler rules (Claude self-check, remove this comment when filling):
     The body is UKRAINIAN, plain language, no code identifiers.
     Forbidden in the body: stack names (Postgres, pgvector, FastAPI, SQLAlchemy, Alembic,
     trafilatura, Caddy, Docker, Hetzner), table schemas, API endpoints, latency targets, SLOs.
     This is a PRODUCT brief. Tech lives in PRD NFRs, sad.md and ADRs. -->

# Idea Brief — <назва фічі>

## 1. Сира ідея
<1 paragraph, verbatim from the user, phase 1>

## 2. Проблема
<1-3 sentences, facts / numbers>

## 3. Користувачі
<who suffers, how often, segments>

## 4. Чому зараз
<trigger: what in the plan or in daily use makes this the next step>

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
- **Рекомендовано?** ◯ / ● (filled in §13)

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

## 11. RICE — пропозиція Claude
- **Reach (R):** <number> — <rationale, cites §3>
- **Impact (I):** <0.25 | 0.5 | 1 | 2 | 3> — <rationale, cites §2 and §8 Executive>
- **Confidence (C):** <0.5 | 0.7 | 0.8 | 1.0> — <rationale, cites the number of open questions in §15>
- **Effort (E):** <person-weeks> — <rationale, cites the Effort signal in §7>
- **RICE = R × I × C / E = <number>**
- **Стан:** proposed | confirmed

## 12. Feasibility — пропозиція Claude
- [☑/☐] **Tech:** <rationale — adjacent feature from the repo scan, or the planned stack>
- [☑/☐] **Skills:** <rationale>
- [☑/☐] **Time:** <rationale — compare with the epic estimate in the plan>
- **Стан:** proposed | confirmed

## 13. Рекомендація
**Обрано: Approach <X>** — <3-5 sentence rationale>

<Rationale MUST cite: RICE from §11, Feasibility from §12, ≥1 matrix cell from §8, ≥1 gap from §6.>

**Що ми фіксуємо:** <what this commits us to for the PRD stage>

## 14. Відкладені та відхилені підходи
| # | Підхід | Статус | Причина | Коли повернутися |
|---|---|:---:|---|---|
| <B> | <name> | відкладено | <reason> | <trigger> |
| <C> | <name> | відкладено | <reason> | <trigger> |

## 15. Відкриті питання
- [ ] <question> — відповідальний: <name>, термін: <date>

## Пов'язане
- <links: docs/CONTEXT.md, epic in docs/initial-idea/second-brain-plan.md, related features>

## DoD self-check
- [ ] 15 розділів заповнено
- [ ] Немає технічних термінів (назв стеку)
- [ ] Обсяг ≤ 5 сторінок (~2200 слів)
- [ ] Frontmatter status: Confirmed
- [ ] RICE підтверджено (state: confirmed)
- [ ] Feasibility підтверджено (state: confirmed)
- [ ] Рекомендація посилається на §6, §8, §11, §12
