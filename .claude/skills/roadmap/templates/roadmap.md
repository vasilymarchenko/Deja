---
status: living
updated_at: "<YYYY-MM-DD>"
---

# Roadmap — Deja

> **Напрямок, не обіцянка.** Найближча робота тверда; що далі пункт, то він орієнтовніший і
> імовірніше зміниться. Це **не** план релізів і тут **немає дат** — це результати, яких ми
> прагнемо, з певністю, що спадає з часом. *Рішення* для кожного пункту живе в його
> `docs/features/<slug>/PRD.md`, не тут.

## Зараз — узгоджено · описано · в роботі

<!-- instruction: features whose docs/features/<slug>/PRD.md exists and is being built. One ROW each:
the OUTCOME (the why), a link to the feature folder, a status (проєктування / реалізація / рев'ю).
No PRD detail — link, don't duplicate. write-prd promotes a row here; ship-feature moves it to Shipped. -->

| Результат (навіщо) | Фіча | Стан |
|---|---|---|
| <результат — яку проблему це закриває> | [<slug>](./features/<slug>/) | реалізація |

## Далі — проблеми та можливості (свідомо ще не описані)

<!-- instruction: the ordered candidate pool. Each row is an OUTCOME / PROBLEM, not a solution, and has
NO feature folder yet (it gets one when pulled into Now via write-prd). Hand-ordered, top = next to pull.
One line of reasoning per row; no scores, no dates. First run: seeded from docs/idea-brief.md
"Складові продукту" in the plan's vertical-slice order. -->

| Результат / проблема | Майбутній slug | Чому в цьому порядку |
|---|---|---|
| <формулювання проблеми> | <slug> | <залежність / що відкриває / прогалина з §6 brief> |

## Пізніше — результати та теми (орієнтовно)

<!-- instruction: coarse, directional one-liners only — one ROW each. No slugs, no reasons, no dates. -->

| Результат / тема |
|---|
| <результат або тема, до якої ми, ймовірно, дійдемо> |

## Фундамент і експлуатація

<!-- instruction: work without a direct user outcome — skeleton, job queue, backups, monitoring.
Not a horizon. Listed so it is neither lost nor mistaken for a feature. Destination: map-architecture,
an ADR, or a task without a PRD. Seeded from docs/idea-brief.md "Фундамент і експлуатація". -->

| Робота | Куди йде | Стан |
|---|---|---|
| <скелет і деплой> | map-architecture | не почато |

## Зроблено

<!-- instruction: ship-feature moves delivered rows here — one ROW each: date + outcome + link to the feature
+ the CHANGELOG.md entry. Keeps Now honest and records what landed. -->

| Дата | Результат | Фіча | Changelog |
|---|---|---|---|
| <YYYY-MM-DD> | <результат> | [<slug>](./features/<slug>/) | [запис](../CHANGELOG.md) |
