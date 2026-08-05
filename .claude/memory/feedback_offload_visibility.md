---
name: offload-mode must be visible in the reply
description: When offload-mode is ON, name the vendor + tier for each delegated step (or say plainly that nothing was offloaded) — the flag gives the user no feedback otherwise, and silent non-use reads as wasted quota
type: feedback
date_written: 2026-07-21
last_validated: 2026-07-21
---

While `~/.claude/.offload-mode` is set, every reply that involved delegated execution or review must state **which vendor and tier actually ran it** (e.g. "review → Codex Terra", "executed inline on Opus, not offloaded because the step was judgment-heavy"). Never let an offload-eligible turn finish without saying where the work went.

**Why:** On 2026-07-20 the user independently voiced the same doubt in two different project dirs on the same day — `business-ops` session `267a85e6` (a long Q&A probing whether subagents share the parent context budget, whether Codex sub-agent use burns Claude context, and how GPT-Pro caps interact with Claude Max caps) and `design-extract` session `30dba680`: *"我现在是已经 offload 已经开了，但是如果说你没有用 GPT sub-agent，我会觉得很浪费"*. The flag is a routing posture with no user-visible signal, so from the outside "offload ON + no mention of Codex" is indistinguishable from "the flag is broken" — and the user paid for a separate ChatGPT quota pool specifically to relieve the Claude weekly cap. Two independent raises in one day is a mechanism gap, not a one-off question.

**How to apply:**
1. Offloaded a step → name it: vendor + model tier + why that tier (`--model gpt-5.6-terra` for bulk execution, Sol only for high-stakes review and always `--background`).
2. Did NOT offload despite the flag being ON → say so in one clause with the reason (dispatch-brake: too small / needs live context / security-sensitive / single unknown bug stays inline). Silence is the failure mode, not the decision itself.
3. This is a reporting rule only — it never lowers the bar for *when* to offload. `rules/offload-mode.md` still governs eligibility, and `rules/orchestration-default.md`'s dispatch-brake still applies.

Related: [[docs-to-mechanism]] — the durable fix is a mechanism (a status line or a per-turn routing note) rather than this prose reminder; if the doubt recurs after this rule lands, build the mechanism. Also [[feedback_no_fake_metrics]] — never claim a step ran on Codex when it didn't.
