---
name: feedback_new_agent_needs_session_restart
description: "A newly written ~/.claude/agents/*.md is NOT callable as subagent_type in the session that created it — run it via general-purpose + 'adopt this file as your instructions'"
metadata:
  node_type: memory
  type: feedback
date_written: 2026-08-02
last_validated: 2026-08-02
---

The Agent tool's `subagent_type` registry is snapshotted at session start. A subagent file
you just wrote to `~/.claude/agents/<name>.md` will fail with
`Agent type '<name>' not found. Available agents: ...` for the rest of that session.

**Why:** agent discovery runs once at session init; writing the file does not re-register it.
Hit 2026-08-02 twice in a row during a planner A/B (`planner-noclause`), costing two dead
Agent calls before the workaround was found.

**How to apply:** when an experiment or measurement loop needs a *temporary* agent variant
(the common case under `measure-before-mutate.md` — arm A vs arm B of one agent body), do
NOT create a new agent type. Instead spawn `general-purpose` (or the tier you want) with:

> Read `<abs path to the variant .md>` in full and adopt it as your complete operating
> instructions for this task — you ARE that agent. Follow its output contract exactly.

Same body, same model tier, zero restart. Reserve real agent files for permanent roster
additions, and expect those to go live only in the *next* session — verify with a probe call
before assuming a new agent is wired. Delete temp variant files when the experiment ends.

Related: [[reference_subagent_architecture]] · [[feedback_no_dead_infra]]
