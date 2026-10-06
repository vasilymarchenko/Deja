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

One glossary for the whole product. Every entry is bilingual: the English line is canonical
(code, schema, technical docs); the nested `uk:` line is its Ukrainian translation, headed by
the Ukrainian product word used in UI and product docs.

Terms are fixed the moment they appear in an interview / PRD / review — never batched.
Empty H2 → prune before commit. ## Glossary is mandatory; the other two are optional.
-->

## Glossary

<!-- One entry per term, two lines:
     - <term> — <one-sentence definition>. NOT <confused concept + how it differs>.
       - uk: **<Ukrainian word>** — <the same definition in Ukrainian>. НЕ <the same boundary>.
     Alphabetical by the English term. -->
- anchor — a short "entity + aspect" label, 3–7 per record, proposed by the LLM, that names what the record is about in the words the user is likely to search with. NOT a tag: anchors are generated per record, not picked from a shared list, and nothing is organized or browsed by them.
  - uk: **якір** — короткий ярлик виду «сутність + аспект», 3–7 на запис; його пропонує LLM, і він називає, про що запис, тими словами, якими користувач, імовірно, шукатиме. НЕ тег: якорі створюються для кожного запису окремо, а не вибираються зі спільного списку, і за ними нічого не впорядковують і не переглядають.
- capture — the user's act of saving a source or an idea from a client in one step. NOT indexing: capture is what the user does and must feel instant; indexing is what the system does with the record afterwards.
  - uk: **зберегти** — дія користувача: зберегти джерело або ідею з клієнта за один крок. НЕ індексація: зберігає користувач, і це має бути миттєво; індексацію система робить із записом уже потім.
- chunk — a piece of a record's content together with its position in the original: paragraph, README section, timecode. NOT the record: search matches chunks, then shows the record and opens it at the chunk's position.
  - uk: **фрагмент** — частина вмісту запису разом із її місцем в оригіналі: абзац, розділ README, таймкод. НЕ запис: пошук знаходить фрагменти, потім показує запис і відкриває його на місці фрагмента.
- idea — a record whose main content is the user's own short text; write-once, optionally linked to one or more trigger sources. NOT a note: no editing, folders or manual links; it grows only when a similar thought is saved again.
  - uk: **ідея** — запис, головний вміст якого — власний короткий текст користувача; зберігається один раз без редагування, може бути пов'язана з одним або кількома тригерами. НЕ нотатка: немає редагування, папок і ручних зв'язків; ідея доповнюється, лише коли схожу думку зберігають знову.
- record — one saved item in the user's memory: a source or an idea, with its content, save reason or title, anchors and lifecycle fields. NOT a bookmark: a record holds the full content, and saving the same source again extends the same record.
  - uk: **запис** — одна збережена річ у пам'яті користувача: джерело або ідея, з вмістом, «навіщо» або заголовком, якорями та полями життєвого циклу. НЕ закладка: запис містить повний вміст, а повторне збереження того самого джерела доповнює той самий запис.
- save reason — one line on why the user saves a source, proposed by the LLM at capture and accepted, edited or skipped by the user. NOT a summary: a summary says what the text is about; the save reason says why it matters to the user, often in words the text does not contain.
  - uk: **навіщо** — один рядок про те, чому користувач зберігає джерело; LLM пропонує його під час збереження, а користувач приймає, править або пропускає. НЕ резюме: резюме каже, про що текст; «навіщо» каже, чим він важливий для користувача, часто словами, яких у тексті немає.
- source — a record whose main content comes from outside: a web page, a GitHub repo, a Telegram post, a video. NOT an idea: in a source the external content is primary and the save reason sits on top of it. The kind (web page, repo, post, video) is the "source type".
  - uk: **джерело** — запис, головний вміст якого прийшов ззовні: веб-сторінка, GitHub-репо, Telegram-пост, відео. НЕ ідея: у джерелі головне — зовнішній вміст, а «навіщо» лежить поверх нього. Вид (веб-сторінка, репо, пост, відео) — це «тип джерела».
- trigger — a source linked to an idea as what prompted it, attached at capture or by a later save. NOT a manual link between records: the user never connects records by hand; the only links are idea → its triggers.
  - uk: **тригер** — джерело, пов'язане з ідеєю як те, що її викликало; прикріплюється під час збереження або пізнішим збереженням. НЕ ручний зв'язок між записами: користувач ніколи не з'єднує записи руками; єдині зв'язки — ідея → її тригери.
