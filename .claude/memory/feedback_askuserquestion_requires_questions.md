---
name: AskUserQuestion requires a non-empty questions array
description: AskUserQuestion fails with InputValidationError unless the required `questions` array param is present and non-empty.
type: feedback
date_written: 2026-06-10
last_validated: 2026-06-14
severity: low
---

## Rule: Never call AskUserQuestion without a populated `questions` array

**Why:** On 2026-06-10 (session 2d67ba01), AskUserQuestion was invoked 3 times in one session and each failed with:
> InputValidationError: AskUserQuestion failed due to the following issue: The required parameter `questions` is missing

The tool's only required parameter is `questions` (an array of 1–4 question objects). Calling it with no params — or intending to "ask a quick question" as free text — is rejected at the validation layer before the user ever sees it. Cost: 3 wasted tool round-trips.

## How to apply

When reaching for AskUserQuestion:

1. Always build the full `questions` array first — at least one object with `question`, `header`, `options` (2–4), and `multiSelect`.
2. If you only want to say something to the user (not gather a structured choice), don't use this tool at all — just write the text in your reply.
3. In plan mode, use `ExitPlanMode` to surface a plan, not AskUserQuestion.

## Related
- [[feedback_read_before_edit]] — same class: tool-precondition errors that cost silent round-trips
- AskUserQuestion tool schema (`questions` is the sole required field)
