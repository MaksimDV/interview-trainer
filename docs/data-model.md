# Interview Trainer Data Model

This doc describes the MVP entities and how they evolve for AI + adaptive rotation.

## Entity overview
- `User`: someone practicing interview questions (MVP can be anonymous via `userId`).
- `Topic`: a category label (e.g., `java`, `sql`).
- `Question`: interview question prompt belonging to a topic.
- `QuestionOption`: multiple-choice options for a question.
- `Answer`: a user submission for a question (stores selected option and optional free-text).
- `TopicProgress`: aggregates user performance per topic.

## Tables (suggested MVP schema)

```sql
-- Users
create table users (
  id uuid primary key,
  created_at timestamptz not null default now()
);

-- Topics
create table topics (
  id uuid primary key,
  slug text unique not null,
  name text not null
);

-- Questions
create table questions (
  id uuid primary key,
  topic_id uuid not null references topics(id),
  prompt text not null,
  -- For AI scoring later (optional for MVP)
  expected_answer_hint text,
  rubric text,
  created_at timestamptz not null default now()
);

-- Options
create table question_options (
  id uuid primary key,
  question_id uuid not null references questions(id),
  sort_order int not null,
  text text not null,
  is_correct boolean not null default false
);

-- Answers (one per question attempt)
create table answers (
  id uuid primary key,
  user_id uuid not null references users(id),
  question_id uuid not null references questions(id),
  selected_option_id uuid references question_options(id),

  -- optional: only used when AI evaluation is enabled
  free_text text,

  -- Exactly one input type must be provided per attempt:
  -- MCQ uses selected_option_id, free-text uses free_text.
  -- If both are present or both are absent -> 400 INVALID_ARGUMENT (validated at API layer).
  constraint answers_one_of_selected_or_free_text
    check (
      (selected_option_id is not null and free_text is null)
      or
      (selected_option_id is null and free_text is not null)
    ),

  score int not null,          -- 0..10
  feedback text not null,     -- normalized user-facing message
  ai_model text,               -- optional: provider/model identifier
  created_at timestamptz not null default now()
);

-- Aggregated progress per topic
create table topic_progress (
  user_id uuid not null references users(id),
  topic_id uuid not null references topics(id),

  attempts int not null default 0,
  avg_score numeric(5,2) not null default 0,

  last_answered_at timestamptz,
  primary key (user_id, topic_id)
);
```

## Indexes (recommended)
```sql
create index idx_questions_topic_id on questions(topic_id);
create index idx_answers_user_id on answers(user_id);
create index idx_answers_question_id on answers(question_id);
```

## How adaptive rotation can use the model
For “weak topic” bias:
- derive weakness from low `avg_score` and/or high recent failure rate
- selection strategy uses `topic_progress` as the signal

## Seed data mapping
The seed file `data/seed/questions.sample.json` should provide:
- topics (slug + name)
- questions (prompt + topic slug + optional rubric fields)
- options (text + which one is correct)
