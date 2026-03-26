# Interview Trainer API

Conventions:
- Transport: JSON over HTTP
- Base path: `/api/v1`
- IDs: use UUID strings (even if MVP uses in-memory / seeded data)
- Scores: integer 0..10
- Error responses follow the same shape (see “Errors”)

Authentication / user context (MVP):
- For MVP, use `X-User-Id: <uuid>` header.
- Later phases can replace it with a real auth subject, while keeping DTOs the same.

---

## GET /api/v1/questions/next

Select the next question for a user session.

Query parameters:
- `topicSlug` (optional): filter by topic
- `count` (optional, default 1): number of questions to return (keep MVP at 1 if frontend is simple)

Headers:
- `X-User-Id` (required): UUID string

Request:
```http
GET /api/v1/questions/next?topicSlug=java&count=1
```

Response (MVP shape):
```json
{
  "question": {
    "id": "uuid-question",
    "topicSlug": "java",
    "prompt": "string",
    "options": [
      { "id": "uuid-opt-1", "text": "string" },
      { "id": "uuid-opt-2", "text": "string" }
    ]
  }
}
```

---

## POST /api/v1/answers

Submit the user answer for scoring and progress updates.

Headers:
- `X-User-Id` (required): UUID string

Request (MCQ):
```json
{
  "questionId": "uuid-question",
  "selectedOptionId": "uuid-option"
}
```

Request (free-text, used by AI evaluation later):
```json
{
  "questionId": "uuid-question",
  "freeText": "string"
}
```

Schema constraint (validation rule):
- Exactly one of `selectedOptionId` OR `freeText` must be provided.
- If the payload contains both (or neither), return `400 INVALID_ARGUMENT`.

Response (MVP):
```json
{
  "answerId": "uuid-answer",
  "score": 0,
  "feedback": "string",
  "topicSlug": "java"
}
```

Notes:
- `topicSlug` is included for UI routing (not an aggregated progress snapshot).
- `feedback` format is a single string in MVP.

---

## GET /api/v1/progress

Fetch user progress for the dashboard and feedback screens.

Headers:
- `X-User-Id` (required): UUID string

Request:
```http
GET /api/v1/progress
```

Response:
```json
{
  "overall": {
    "attempts": 0,
    "avgScore": 0
  },
  "topics": [
    { "topicSlug": "java", "attempts": 0, "avgScore": 0 },
    { "topicSlug": "sql", "attempts": 0, "avgScore": 0 }
  ]
}
```

---

## (Optional) GET /api/v1/topics

Used by the frontend to display filters or quick topic selection.

Response:
```json
{
  "topics": [
    { "topicSlug": "java", "name": "Java" },
    { "topicSlug": "sql", "name": "SQL" }
  ]
}
```

---

## Errors

Response shape:
```json
{
  "error": {
    "code": "INVALID_ARGUMENT|NOT_FOUND|INTERNAL_ERROR",
    "message": "Human readable message",
    "details": {}
  }
}
```

Suggested HTTP codes:
- `400` for invalid request
- `404` for missing question/topic/user
- `401` for missing/invalid `X-User-Id` (implementation choice)
- `500` for unexpected failures
