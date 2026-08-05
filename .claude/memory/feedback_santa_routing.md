---
name: santa-method auto-routes to Tier 4 on high-stakes signals + Codex follow-up
description: When user invokes santa with any of {ultrathink, plan, best way, ship, production, important...}, default to Tier 4 (cross-model + 2 Opus + merger). After Tier 4 PASS, ALWAYS surface /codex:adversarial-review prompt to user.
type: feedback
date_written: 2026-05-10
last_validated: 2026-07-02
severity: high
---

## Rule

When user invokes `santa` / `use santa` / `审一下`, **route to Tier 4 by default** if ANY of these high-stakes signals co-occur in the same message:
`ultrathink`, `best way`, `plan`, `comprehensive`, `thorough`, `rigorous`, `parallelize`, `ship`, `production`, `important`, `critical`, `final`, `审一下`, `检查一下`, `ship 之前`, `上线前`

**Why**: User's typical prompt is something like:
> "plan ultrathink the best way to implement you may parallelize and use santa and reference our existing plans"

Pre-2026-05-10, "use santa" alone defaulted to Tier 2 (2x Opus). User reported that they "felt like Tier 3/4 wasn't being triggered" — correct, because the trigger wording didn't match. The fix: SKILL.md description now lists these high-stakes signals as Tier 4 routing keys.

User is on Max plan, so Tier 4 cost = $0 marginal vs Tier 2. The only downside is latency (~90s vs ~30s) — for high-stakes work this is acceptable.

## How to apply

1. **Detect signal first**: Read user's full prompt. If "santa" co-occurs with any high-stakes signal → Tier 4. Otherwise → Tier 2 (default).

2. **Tier 4 protocol** (santa-method skill auto-orchestrates):
   - Phase 0: bash health-check-providers.sh (catches Gemini/GPT-OSS outages early)
   - Phase 1: Generate (parent Opus)
   - Phase 2 PARALLEL: Path A (cross-model script Gemini+GPT-OSS) + Path B (2 Opus reviewers, **adversarial framing**)
   - Phase 3: Merger 4-bucket analysis (high_confidence / cross_model_unique / opus_unique / contested)
   - Phase 4: Verdict + structured output
   - Phase 5: Fix-loop if FAIL (max 3 rounds)
   - Phase 6: **ALWAYS surface to user** the recommendation:
     > "✅ Tier 4 santa complete. For true cross-vendor adversarial coverage, run: `/codex:adversarial-review`"

3. **Why Codex follow-up is recommend-only, not auto-invoke**: `/codex:adversarial-review` has `disable-model-invocation: true` in its frontmatter — only user can trigger. Surfacing the prompt = the integration point.

4. **Don't skip Phase 6**. The reason cross-model santa adds value is independent reviewers from different labs (Anthropic + Google + OpenAI open-weights). Codex adds OpenAI proprietary GPT-5 perspective — this completes the cross-vendor matrix.

## Standing rule for any santa invocation

- Default is Tier 2 only when user is explicit ("standard santa" / "classic santa")
- Default is Tier 4 in all other cases EXCEPT:
  - User says "quick santa" / "fast check" → Tier 1
  - User says "cross santa" / "cross-model santa" → Tier 3 (no Opus, fastest cross-lab)
- After Tier 3 or Tier 4, ALWAYS recommend `/codex:adversarial-review` for max coverage

## Reference

- SKILL.md: `~/.claude/skills/santa-method/SKILL.md`
- Health check: `~/.claude/automation/utils/health-check-providers.sh`
- Cross-model script: `~/.claude/skills/santa-method/scripts/cross-model-review.py`
- Codex slash command: `~/.claude/plugins/cache/openai-codex/codex/<version>/commands/adversarial-review.md` (version-pinned dir churns — 1.0.4 as of 2026-07-02; glob for it)
- Original ensemble principle: `~/vault/permanent/cross-model-ensemble-principle-2026-04-22.md`
