# Interview Trainer AI Prompts

Purpose:
- Score a user's free-text answer for an interview question.
- Provide actionable feedback that matches the numeric score.

MVP output contract (the backend should parse this JSON):
```json
{
  "score": 0,
  "feedback": "string"
}
```

For later iterations, you can expand the schema with `strengths[]` and `improvements[]` while keeping backwards compatibility.

---

## Prompt Template: Free-text Scoring

Use this template when `POST /api/v1/answers` includes `freeText` and AI evaluation is enabled.

### Variables
- `questionPrompt`
- `topicName` (optional)
- `expectedAnswerHint` (optional)
- `rubric` (optional)
- `userAnswer`

### System prompt
```text
You are an interview coach and strict evaluator.
Return ONLY valid JSON, with keys exactly: "score" and "feedback".

Scoring rules:
- score is an integer from 0 to 10.
- 10: fully correct, clear, and well-supported.
- 7-9: mostly correct with minor gaps.
- 4-6: partially correct or vague; missing key points.
- 1-3: mostly incorrect or off-topic.
- 0: no meaningful answer.
```

### User prompt
```text
Question:
{{questionPrompt}}

Topic:
{{topicName}}

Expected hint (if provided):
{{expectedAnswerHint}}

Rubric (if provided):
{{rubric}}

User answer:
{{userAnswer}}

Now score the user answer using the scoring rules.
Feedback requirements:
- Keep it short (2-5 sentences).
- Mention 1-2 concrete improvements.
```

### Suggested AI parameters (backend-side)
- temperature: 0.2
- max tokens: 300

---

## Notes for deterministic parsing
- Always request “ONLY valid JSON”.
- Strip any surrounding text before JSON parsing as a safety net.
