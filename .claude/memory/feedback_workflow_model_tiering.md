---
name: feedback-workflow-model-tiering
description: "Workflow/fan-out subagents default to inheriting the main-loop model — if that's Fable, a 30+ agent vision batch runs entirely on Fable = very expensive. Explicitly pin model per agent."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: e6f74ee4-1250-4a28-b1de-09ef75ba89a1
date_written: 2026-07-20
last_validated: 2026-07-21
---

When orchestrating a Workflow (or any large fan-out of subagents), **every `agent()` call without an explicit `model:` inherits the resolved session/main-loop model.** If the session default is Fable 5, a batch of 33 vision-analysis agents + QC + synthesis silently ALL run on Fable — the single most expensive tier — for work that is mostly mechanical visual tagging.

**Why:** user stopped a 41-agent shooting-DNA workflow mid-run on 2026-07-14 precisely because it was burning Fable tokens on per-account contact-sheet tagging. His instruction: "把 procedure 写得非常详细让 Opus 执行,省 Fable 的钱和 token;或拆成小 task 借 Haiku 做同样的东西。"

**Independent cross-vendor validation (Theo t3.gg, 2026-07-14):** Codex's "Ultra" sets parent AND every sub-agent to max reasoning → recursively-nested max → he burned a 5-hour quota in ~20 min, twice in an hour. Claude Code's ultra-code correctly defaults sub-agents to **xhigh, not max**. An outside expert hitting the exact failure this rule prevents = confidence↑ (converging cross-vendor evidence; also corroborates subagent curated-context [[reference_subagent_architecture]]).

**How to apply:**
- Decompose first (which is Fable-worthy? usually only the ONE novel-reasoning synthesis node — [[project_cost_aware_delegation]] Fable-budget-of-one).
- Pin `model:` explicitly on EVERY workflow agent: `opus` for vision/execution + reasoning synthesis, `haiku` for pure IO (load args, write file, echo), Fable only for a genuinely reasoning-hard node.
- Write each worker prompt as a **detailed numbered SOP** so a cheaper model executes reliably (procedure transfer ≠ reasoning depth — [[reference_claude_code_tuning]], `delegate-discipline`).
- **Resume trick to not waste already-spent Fable:** if a Fable batch already completed, `Workflow({scriptPath, resumeFromRunId})` with UNCHANGED prompts on the completed agents returns their cached results FREE; only add `model:'opus'` to the agents AFTER them (edited/new calls run live on the cheaper tier). Never edit an early agent's opts — "everything after the first edited call runs live" would blow the whole cache.
- Don't rely on a model to echo a big JSON blob verbatim to persist it — return it from the workflow and write it deterministically in the main loop.

Cross-links: [[project_cost_aware_delegation]], [[feedback_review_economy]], [[reference_subagent_architecture]].
