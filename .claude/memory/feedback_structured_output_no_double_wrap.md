---
name: StructuredOutput takes the schema object itself, not a wrapper
description: Subagents forced into a structured schema double-wrap the payload (`{"findings": {"findings": []}}`) — pass the schema's own top-level object, unnested.
type: feedback
date_written: 2026-07-29
last_validated: 2026-08-04
severity: low
---

## Rule: When calling `StructuredOutput` (or any schema-forced return), emit the schema's OWN top-level object — never nest it under a key named after itself.

Correct: `{"findings": []}` · Wrong: `{"findings": {"findings": []}}`

**Why:** 2026-07-29 night scan — 3 distinct sessions (be54e2a2, 6cf60e32, 07b2fcd2) each failed with
`Output does not match required schema: /findings: must be array`. All three were **schema-forced
subagents returning an empty result set**; each wrapped the whole schema object inside a property of
the same name. The empty case is where it bites: with real findings the model tends to build the array
inline and get it right, but "nothing to report" invites reaching for a wrapper. Each failure costs a
retry round-trip in a subagent whose only job was to return `[]`.

**How to apply:** When a `schema` option is supplied to `agent()` / a StructuredOutput tool is forced,
the argument object IS the schema instance. If the schema is `{findings: array}`, the tool input is
`{"findings": [...]}`. Applies equally to the empty case — an empty result is `{"findings": []}`, not
an omitted call and not a nested wrapper. Related: [[feedback_askuserquestion_requires_questions]]
(same class — validation-layer rejection before any human sees the output).
