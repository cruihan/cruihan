---
name: feedback_never_auto_escalate_fable
description: Never auto-escalate the main model up to Fable; ceiling is Opus. Delegating DOWN is fine; Fable is explicit-choice only.
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 894ffb84-1120-4969-9db9-4461d741880c
date_written: 2026-07-20
last_validated: 2026-07-21
---

Never auto-escalate to Fable 5. Escalation ceiling = **Opus**. Delegating DOWN (from whatever model the user picked — Opus or Fable — to a sonnet/haiku fleet) is fine and encouraged; auto-promoting the main loop UP, and especially reaching Fable automatically, is forbidden.

**Why:** User is explicitly wary — Fable is expensive/complex and its subagent fan-out eats a lot of tokens. They stated directly that auto-promotion to Fable is a fear ("非常害怕"), and confirmed delegating down is fine. This is a stated preference, not an inference.

**How to apply:** The cheap-first cascade + escalate-after-2 tops out at Opus. For a node that looks Fable-hard, first run **Opus + the `fable-mode` skill/procedure** (transfers the P0–P5 reasoning discipline without the Fable spend — see vault [[2026-07-08-scaffolding-vs-model-gap]]); invoke actual Fable ONLY on explicit user opt-in. The main-session model never auto-promotes — up-shifts are a manual `/model` choice.

**fable-mode is ALSO not a default/reflex.** Gate it on an explicit complexity judgment first — only genuinely hard/novel nodes get the deep-reasoning procedure. On routine/simple work it overthinks, so plain Opus/Sonnet (no procedure) is correct (see [[reference_claude_code_tuning]]: 'high' is the sweet spot; xhigh/deep-reasoning overthinks). User stated this directly. Codified in `rules/orchestration-default.md`. Consistent with [[feedback_fable_orchestrator_only]] and [[feedback_workflow_model_tiering]].
