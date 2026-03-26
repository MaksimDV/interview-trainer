# Interview Trainer Roadmap

This file is the high-level view of where the project is heading.

Backlog (task-by-task source of truth): `docs/backlog.md`

---

## Milestones

### MVP baseline (Phase 1)
- Outcome: a working loop of `question -> answer -> score -> progress`
- Target tasks: `TRN-001`..`TRN-009`
- Deliverable expectations:
  - `GET /api/v1/questions/next` returns seeded MCQ
  - `POST /api/v1/answers` calculates score and stores the attempt
  - `GET /api/v1/progress` aggregates topic averages

### AI + adaptive learning (Phase 2)
- Outcome: free-text answers get AI-scored, and question selection biases toward weak topics
- Target tasks: `TRN-010`..`TRN-012`

### Telegram + scaling path (Phase 3)
- Outcome: users can practice via Telegram, and the system can grow via async jobs/microservices
- Target tasks: `TRN-013`..`TRN-017`

---

## Execution strategy
- Start monolith-first (Spring Boot) but keep domain boundaries in code.
- Treat AI scoring and adaptive rotation as pluggable services behind clear interfaces.
- Keep API contracts stable so a later microservice split does not break the frontend/bots.

