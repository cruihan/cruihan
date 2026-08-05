---
name: workflow-tool-has-no-run-in-background
description: The Workflow tool always runs in background and rejects a run_in_background param — passing it throws InputValidationError
type: feedback
date_written: 2026-06-16
last_validated: 2026-07-06
---

**Rule.** Do NOT pass `run_in_background` (or `&`-style background flags) to the **Workflow** tool. It already runs in the background by design — the call returns immediately with a task ID and a `<task-notification>` arrives on completion. Its schema has no such parameter.

**Why:** A 2026-06-16 session called Workflow with `run_in_background: true` and got `InputValidationError: Workflow failed due to the following issue: An unexpected parameter run_in_background was provided`. The param belongs to the **Bash** and **Agent** tools (which default to foreground and need the flag to detach) — not Workflow, whose execution model is inherently async.

**How to apply:** When invoking Workflow, pass only its real fields (`script` / `scriptPath` / `name`, `args`, `resumeFromRunId`). To wait on the result, just let the task-notification fire — no flag needed. Reserve `run_in_background: true` for `Bash` and `Agent` calls where foreground is the default. Same applies to other inherently-async tools.

Related: [[feedback_askuserquestion_requires_questions]] — both are tool-schema-precondition lessons; verify a tool's actual parameter schema before adding flags by analogy from a different tool.
