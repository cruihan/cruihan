---
name: Wrap reusable runbooks in skills — no memorization debt
description: Any runbook / prompt / tool that will be invoked repeatedly must have a natural-language skill wrapper before being "done"
type: feedback
date_written: 2026-04-19
last_validated: 2026-07-25
severity: high
---

## Rule: If the user has to remember a file path or command name to invoke something, the interface is not done.

**Why:** On 2026-04-19, I built 6 audit prompts + 1 tool prompt under `~/.claude/prompts/` and considered the project complete. The user's verbatim feedback:

> "太多的 commands，我完全记不住这么多 … 既然这些 workflow 会长期定期，并且会固定地去应用，那我们为什么不能把它自动化呢？或者把它变成一个长期能用的 skills 呢？"

Translation: "Too many commands — I can't remember all this. Since these workflows will be long-term recurring and fixed, why can't we automate them or turn them into long-term skills?"

The correct end-state was **2 natural-language skills** (`system-audit` and `upgrade-skill`) that auto-route to the underlying prompts. I didn't catch this on my own — the user had to call it out.

## How to apply

When building reusable tooling for Claude Code:

1. Ask: "will this be invoked more than once?" If yes → it needs a skill wrapper.
2. The underlying prompt / script can still exist for manual or replication use — but the PRIMARY interface is the skill.
3. Description follows the Seleznov templates in `rules/skill-optimization.md`:
   - COMPETITIVE: `ALWAYS invoke when user asks about ... Do not X directly.`
   - DOMAIN: `Use when user asks to ...`
   - ONESHOT: `Invoked when user <action> <input>.`
4. Description must contain 3+ concrete user-speech trigger phrases (not task categories)
5. Default to auto-activation; explicit invocation is a fallback, not the main path

## The "can the user invoke this without docs?" test

- If they must remember a file path → fail
- If they must remember a flag name → fail
- If they must type an exact command → fail
- If they say something natural and the skill just activates → pass

## Anti-pattern

Building sophisticated prompts under `prompts/` without a skill wrapper and considering it "shipped".

## Related
- `rules/skill-optimization.md` (Seleznov templates + tier classification)
- Existing good examples: `meeting-notes`, `gmail-label`, `scrape-leads` — all auto-activate from natural language
- New examples (same-day fix): `system-audit`, `upgrade-skill`
