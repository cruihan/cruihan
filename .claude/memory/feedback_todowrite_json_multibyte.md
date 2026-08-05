---
name: todowrite-json-parse-failures
description: TodoWrite AND AskUserQuestion intermittently fail InputValidationError ("could not be parsed as JSON") on long Chinese-content payloads — don't retry identical input; shorten/split
type: feedback
date_written: 2026-07-04
last_validated: 2026-07-21
---

TodoWrite calls carrying long todo lists with Chinese content intermittently fail with `InputValidationError: TodoWrite was called with input that could not be parsed as JSON` (the error echoes the first 200 bytes of an apparently truncated payload, e.g. 685–1141 bytes sent).

**Why:** Hit 3× on 2026-07-03 across 2 sessions (49d075a3, 64e42bba), all with multibyte (Chinese) todo content around 0.7–1.1 KB. The payload appears cut off mid-string, so the JSON never closes — retrying the exact same call reproduces the failure.

**Scope update (2026-07-18):** the same failure hit AskUserQuestion (session 0d165ee1, 1380-byte Chinese question payload cut mid-options). This is a tool-input family issue, not TodoWrite-specific — any tool call carrying ~0.7 KB+ of multibyte content can truncate.

**How to apply:** When TodoWrite (or AskUserQuestion) throws a JSON-parse InputValidationError: (1) do NOT re-send the identical payload; (2) shorten each `content` field (keep Chinese, but one short clause per todo) or split into two smaller TodoWrite calls; (3) if it persists, drop decorative detail from todo text — the todo list is a progress tracker, not documentation. Related: [[feedback_askuserquestion_requires_questions]] (same tool-input-validation family).
