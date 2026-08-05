---
name: Read before Edit — tool precondition
description: Must use Read tool (in current session) before Edit OR Write on any existing file. A prior Write does NOT satisfy this — only Read does.
type: feedback
date_written: 2026-04-19
last_validated: 2026-08-04
severity: medium
---

## Rule: Always Read a file in the current session before Editing it

**Scope update (2026-07-04):** the precondition applies to **Write on existing files too**, not just Edit. Night scan of 2026-07-03 sessions found 15+ "File has not been read yet. Read it first before writing to it." failures across 6 sessions — most were Write calls overwriting files known only from earlier context or summaries. Same fix: Read (even a 1-line ranged Read) before Write/Edit on any file you didn't create this session.

**Why:** On 2026-04-19, during a batch of 3 parallel Edit calls (CLAUDE.md + AGENTS.md + GEMINI.md mirror-sync), 2 of 3 failed with:
> "File has not been read yet. Read it first before writing to it."

The `Edit` tool explicitly requires `Read` in the current session. **`Write` does not count** — even though I had written AGENTS.md and GEMINI.md earlier in the session, Edit still rejected them.

Cost: 3 extra tool calls (1 parallel Read, then re-issue the failed Edits) and broken parallelization.

## How to apply

Before issuing `Edit` on a file:

1. Have I **`Read`** this file in the CURRENT conversation? (Previous sessions don't count — context resets across sessions)
2. If not: `Read` it first, even if I "know" the content from having written it earlier
3. `Write` does NOT satisfy the precondition
4. When batching parallel Edits across multiple files, Read them all first in one parallel block

## Pattern that works

```
Block 1: Read A  |  Read B  |  Read C     (parallel)
Block 2: Edit A  |  Edit B  |  Edit C     (parallel)
```

## Anti-pattern

```
Block 1: Edit A  |  Edit B  |  Edit C     (2 fail because never Read)
Block 2: Read B  |  Read C                (recovery)
Block 3: Edit B  |  Edit C                (re-issue)
```

3 round-trips instead of 2.


**Scope update (2026-07-21):** the sibling failure `String to replace not found in file` hit 3× on 2026-07-20 (sessions 34a08f2c, 38a41a0d, agent-adafb3ae) — each time the `old_string` was reconstructed from memory/an earlier summary rather than a fresh Read of the current region. Same root cause, same fix: re-Read the exact region and copy the string verbatim; never guess a second variant after the first miss.

## Related
- `rules/principles.md` #4 — "test every core file change before moving on"
- Claude Code built-in Edit tool docstring
