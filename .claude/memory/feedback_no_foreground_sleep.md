---
name: no-foreground-sleep-to-wait
description: The harness BLOCKS foreground `sleep N && <cmd>` in Bash — use an `until <check>; do sleep 2; done` loop, Monitor, or run_in_background instead of sleeping to poll
type: feedback
date_written: 2026-07-04
last_validated: 2026-07-30
---

Foreground `sleep N` followed by another command is blocked by the harness before execution ("Blocked: sleep 60 followed by: …"). Sleeping to wait for a workflow/agent/file to appear never runs.

**Why:** Hit 2× on 2026-07-03 (sessions 803d1638 `sleep 60 && echo`, bc2123b5 `sleep 45 && cd …/workflows/wf_…`) — both were polling for background work. Background work tracked by the harness (Agent, Workflow, Bash run_in_background) re-invokes the model on completion, so the sleep was wasted even conceptually.

**How to apply:**
1. Waiting on a condition → single Bash call with an until-loop: `until [ -f out.json ]; do sleep 2; done` (inner sleeps inside a loop are allowed), or use the Monitor tool.
2. Waiting on harness-tracked background work (Workflow/Agent/background Bash) → just end the turn; completion re-invokes you.
3. Never use bare `sleep N` as a scheduling device in a foreground command.

Recurred 3× on 2026-07-30 (327c610b `sleep 30`/`sleep 45` polling a log + an elements count, bc27b355 `sleep 45` before `vercel ls`) — all three were polling for an external/background job. Pattern is stable; the fix is still the until-loop or Monitor.
