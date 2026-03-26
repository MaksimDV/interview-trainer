# Data Seed

Seed data is used to bootstrap the system before a real admin UI / content pipeline exists.

For MVP, start with `data/seed/questions.sample.json`.

## Expected loader behavior (implementation detail)
- Load `topics` and `questions`
- For each question, load `options` and mark the single correct option
- Seed should be deterministic and suitable for local development

