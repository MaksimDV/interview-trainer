# Interview Trainer — Production Backlog

Этот документ — **единый источник правды** по работам: что делаем, в каком порядке, как принимаем результат.

## Как мы ведём backlog
- Формат задач: чекбоксы GitHub `- [ ]` (TODO) / `- [x]` (DONE)
- Для работы “в процессе”: чекбокс остаётся `- [ ]`, но поле `Status` = `IN PROGRESS`
- Каждая задача имеет стабильный `Task ID` (`TRN-###`), чтобы ссылаться из PR/Issues/доков
- У каждой задачи должны быть: **приоритет**, **оценка**, **зависимости**, **acceptance criteria**

### Статусы
- `TODO`: не начато
- `IN PROGRESS`: в работе
- `BLOCKED`: заблокировано зависимостью
- `DONE`: выполнено

### Приоритеты
- `P0`: блокер релиза MVP
- `P1`: важно для “нормального” MVP, но можно с обходным путём
- `P2`: nice-to-have, после MVP

### Definition of Done (DoD) для задач MVP
- Реализовано и работает локально
- Обновлены спецификации в `docs/` (если менялись контракт/модель)
- Добавлена минимальная валидация входных данных
- Ошибки API возвращаются в согласованном формате

---

## Цели и границы MVP
### Цель MVP (Release 0)
Дать пользователю “петлю практики”:
1) получить вопрос → 2) ответить → 3) получить оценку/фидбек → 4) увидеть прогресс по темам

### Не в MVP
- Регистрация/аутентификация (кроме простого `userId`)
- Админка для контента
- Телеграм/очереди/микросервисы

---

## Release Plan (high-level)
- **R0 (MVP)**: TRN-001 .. TRN-009 (без AI)
- **R1 (AI)**: TRN-010 .. TRN-012
- **R2 (Channels/Scale)**: TRN-013 .. TRN-017

Оценки ниже — ориентиры для планирования. Для “продакшн-качества” обычно добавляют буфер \(~20–30\%\) на интеграцию и правки по фидбеку.

---

## EPIC A — Foundation (MVP, P0)

- [ ] **TRN-001** Repo & Structure — Status: TODO — Priority: P0 — Estimate: 0.5–1d
  - **Dependencies**: none
  - **Acceptance criteria**
    - В репозитории есть папки `backend/`, `frontend/`, `docs/`, `data/`
    - В `README.md` есть короткий “как запустить локально” (черновик ок)

- [ ] **TRN-002** API Contract (MVP) — Status: TODO — Priority: P0 — Estimate: 1d
  - **Dependencies**: TRN-001
  - **Notes**: контракт должен поддерживать будущие AI/адаптивность без ломки.
  - **Acceptance criteria**
    - `docs/api.md` описывает:
      - `GET /api/v1/questions/next` (или эквивалент) **с контекстом пользователя**
      - `POST /api/v1/answers` с валидацией “один из вариантов”: `selectedOptionId` **или** `freeText`
      - `GET /api/v1/progress`
    - Ошибки имеют единый формат

- [ ] **TRN-003** Data Model (MVP) — Status: TODO — Priority: P0 — Estimate: 1.5d
  - **Dependencies**: TRN-002
  - **Acceptance criteria**
    - `docs/data-model.md` описывает сущности и ограничения
    - `Answer` поддерживает два режима:
      - MCQ: `selectedOptionId` задан
      - Free-text (для будущего AI): `freeText` задан
    - Seed-данные согласованы с моделью (`data/seed/...`)

- [ ] **TRN-004** Backend Skeleton (Spring Boot) — Status: TODO — Priority: P0 — Estimate: 1–1.5d
  - **Dependencies**: TRN-001
  - **Acceptance criteria**
    - Приложение стартует локально
    - Есть health endpoint (`/actuator/health` или аналог)
    - Подключение к Postgres описано (локально можно через env)

---

## EPIC B — Core Practice Loop (MVP, P0)

- [ ] **TRN-005** Questions: “Next question” — Status: TODO — Priority: P0 — Estimate: 1–1.5d
  - **Dependencies**: TRN-002, TRN-003, TRN-004
  - **Acceptance criteria**
    - API отдаёт вопрос с вариантами ответов
    - Поддерживается фильтр/контекст по теме (минимально)

- [ ] **TRN-006** Answers: Submit + Score (MCQ) — Status: TODO — Priority: P0 — Estimate: 2–3d
  - **Dependencies**: TRN-005
  - **Acceptance criteria**
    - `POST /api/v1/answers` сохраняет попытку
    - Возвращает `score` (0..10) и короткий `feedback`
    - Валидирует вход (нет “оба поля заполнены” / “оба пустые”)

- [ ] **TRN-007** Progress Aggregation — Status: TODO — Priority: P0 — Estimate: 1–1.5d
  - **Dependencies**: TRN-006
  - **Acceptance criteria**
    - `GET /api/v1/progress` возвращает общий прогресс и per-topic статистику
    - Данные соответствуют сохранённым ответам

---

## EPIC C — Minimal Frontend (MVP, P1)

- [ ] **TRN-008** Frontend: Practice UI — Status: TODO — Priority: P1 — Estimate: 2–3d
  - **Dependencies**: TRN-005, TRN-006, TRN-007
  - **Acceptance criteria**
    - Экран вопроса + варианты
    - Отправка ответа и показ результата
    - Экран/блок прогресса

---

## EPIC D — Docs & Product Readiness (MVP, P1)

- [ ] **TRN-009** Documentation Polish (MVP) — Status: TODO — Priority: P1 — Estimate: 0.5–1d
  - **Dependencies**: TRN-002, TRN-003
  - **Acceptance criteria**
    - `docs/architecture.md`, `docs/api.md`, `docs/data-model.md`, `docs/roadmap.md` согласованы между собой
    - В `README.md` есть ссылки на ключевые доки

---

## Phase: AI (Post-MVP, P2 until MVP shipped)

- [ ] **TRN-010** AI Evaluation (free-text) — Status: TODO — Priority: P2 — Estimate: 3–5d
  - **Dependencies**: TRN-002, TRN-003, TRN-006
  - **Acceptance criteria**
    - Free-text ответы получают AI score + feedback
    - `docs/prompts.md` содержит финальную промпт-схему и контракт ответа

- [ ] **TRN-011** Adaptive Rotation — Status: TODO — Priority: P2 — Estimate: 2–3d
  - **Dependencies**: TRN-007, TRN-010 (частично)
  - **Acceptance criteria**
    - `GET /api/v1/questions/next` умеет “смещать” выбор в слабые темы

- [ ] **TRN-012** Topics & Tags Expansion — Status: TODO — Priority: P2 — Estimate: 1–2d
  - **Dependencies**: TRN-003, TRN-005

---

## Phase: Channels & Scaling (Post-MVP, P2)

- [ ] **TRN-013** Telegram Bot — Status: TODO — Priority: P2 — Estimate: 3–5d
- [ ] **TRN-014** Async jobs / Broker — Status: TODO — Priority: P2 — Estimate: 2–4d
- [ ] **TRN-015** Stats Dashboard — Status: TODO — Priority: P2 — Estimate: 2–4d
- [ ] **TRN-016** Microservice Split (if needed) — Status: TODO — Priority: P2 — Estimate: 5–10d
- [ ] **TRN-017** Documentation Update (scale) — Status: TODO — Priority: P2 — Estimate: 1–2d

---

## Risks / Open Questions (track explicitly)
- API контракт должен сразу учитывать два типа ответов (MCQ vs free-text), иначе потом будет больная миграция.
- Нужен “контекст пользователя” при выдаче следующего вопроса, иначе адаптивность не из чего считать.
