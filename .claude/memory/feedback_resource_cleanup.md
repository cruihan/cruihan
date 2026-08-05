---
name: feedback-resource-cleanup
description: Every long-running spawn (local server / tunnel / browser / test runner) needs a documented cleanup. Background-with-no-cleanup is forbidden. List-only audit via process-leak-check.sh.
metadata: 
  node_type: memory
  type: feedback
  originSessionId: c48aaaf2-8706-4c0a-9203-0903c1d4781c
date_written: 2026-07-20
last_validated: 2026-07-21
---

User flagged that **process leaks** (FastAPI on :8000, cloudflared tunnels, headless Chromium, hung Python / gemini) waste system resources and reduce velocity. Default discipline: **every spawn has a documented stop**.

**Why**: User isn't going to remember to `Ctrl-C` things hours after a task ended; Claude is the agent doing the spawning, so Claude owns cleanup hygiene. Resources eaten silently = "feels slow" without a clear cause.

**How to apply**:
1. Before spawning any long-running process, decide AND document the stop command (`pkill -f <pattern>`, `kill <pid>`, signal, `trap EXIT`).
2. If a skill spawns servers/tunnels/browsers, its SKILL.md **MUST** have a "Stop / Cleanup" section paired with its "Start" section.
3. When user notes "system feels slow / something keeps running", run `~/.claude/automation/utils/process-leak-check.sh` — list-only, never auto-kills.
4. Prefer FOREGROUND + Monitor over `&`-background unless explicit teardown is wired.
5. If unsure a stop pattern works, **leave it FOREGROUND** — user can `Ctrl-C` themselves.

**NOT in scope**: killing user-owned apps (Chrome, IDE, terminal), MCP stdio subprocesses (Claude lifecycle handles), launchd plists (own lifecycle).

Full discipline + spawn→cleanup mapping table: [resource-cleanup rule](file:///Users/ac/.claude/rules/resource-cleanup.md).
