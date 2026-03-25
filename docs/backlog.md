# Interview Trainer Backlog

This file contains the high-level backlog for the Interview Trainer project.  
Tasks are organized by phases, with rough time estimates and placeholders for further sub-tasks.

---

## Phase 1 — MVP (Flashcard Trainer)
**Goal:** implement the core flow: question → answer → scoring → progress tracking

- **Project Setup** (0.5 day) `TODO`
  - Create repository, folder structure (`src/`, `docs/`, `data/`)
  - Add root README.md

- **Data Model** (1 day) `TODO`
  - Define entities: `Question`, `Answer`, `User`
  - Create initial JSON seed (MDM)
  - _Subtasks can be added here_

- **Backend API** (2 days) `TODO`
  - Endpoints: `GET /questions/random`, `POST /answers`, `GET /progress`
  - _Subtasks: input validation, error handling, etc._

- **Scoring Logic** (1 day) `TODO`
  - Implement simple scoring system (0–10)
  - Store answers and progress

- **Minimal Frontend** (1–2 days) `TODO`
  - Display questions and multiple-choice answers
  - Send user response to backend
  - Show feedback

- **Basic Docs** (0.5 day) `TODO`
  - Create skeleton in `docs/` (architecture, data-model, api, backlog, roadmap, prompts)

---

## Phase 2 — AI & Adaptive Questions
**Goal:** add AI evaluation for free-text answers and adaptive question rotation

- **AI Evaluation** (2–3 days) `TODO`
  - Connect AI for free-text answers scoring and feedback
  - Define prompt templates for AI

- **Adaptive Rotation** (1–2 days) `TODO`
  - Repeat questions based on weak topics
  - Track topic performance

- **Topics & Tags** (1 day) `TODO`
  - Organize questions by topic
  - Add filtering capabilities

- **Documentation Update** (0.5 day) `TODO`
  - Add instructions for AI and adaptive features

---

## Phase 3 — Telegram & Microservices
**Goal:** enable Telegram interaction and prepare for microservices scaling

- **Telegram Bot** (2–3 days) `TODO`
  - Send questions via Telegram
  - Collect user answers

- **Async Processing / Broker** (1–2 days) `TODO`
  - Integrate message broker (RabbitMQ/Kafka)
  - Async answer handling

- **Microservice Split** (2–3 days) `TODO`
  - Separate services: Questions, Answers, Users, Stats

- **Stats Dashboard** (1–2 days) `TODO`
  - Show user progress and analytics
  - Graphs for weak/strong topics

- **Documentation Update** (0.5 day) `TODO`
  - Update architecture and roadmap documentation

---

## Notes
- Task statuses: `TODO`, `IN PROGRESS`, `DONE`
- Each task can be further broken down into subtasks as development progresses
- Time estimates are rough and can be adjusted
