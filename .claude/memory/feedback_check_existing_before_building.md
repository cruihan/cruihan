---
name: Check existing infrastructure before building parallel systems
description: Before designing new backup/logging/scheduling/caching, audit ~/.claude for whether the need is already solved
type: feedback
date_written: 2026-04-19
last_validated: 2026-07-25
severity: medium
---

## Rule: Grep the existing system before adding a parallel solution.

**Why:** On 2026-04-19, while designing audit prompts, I proposed a file-level `backups/<date>/` system where each audit would `cp SKILL.md` before editing. The user pointed out the existing system:

> "你已经设计得非常漂亮了 … 你有 auto-commit hook … .gitignore 里明确写着：backups/ # Covered by git itself"

Translation: "You've already designed it beautifully … you have an auto-commit hook … .gitignore explicitly says 'Backups (covered by git itself)'."

The existing git auto-commit hook + remote push already served as the canonical backup mechanism. My file-level backup was redundant — double maintenance, and `backups/` was gitignored anyway.

## How to apply

Before designing new infrastructure (backup / logging / scheduling / caching / notification):

1. **Read `.gitignore`** — comments often explain the design (e.g., `# Backups (covered by git itself)`)
2. **Read `settings.json` hooks** — existing `PostToolUse` / `Stop` hooks may already log / commit / notify
3. **Check `automation/scheduled/`** — existing cron work may already cover your need
4. **Grep for the keyword** — `grep -r backup ~/.claude` before building a backup system
5. **Ask the user directly**: "I see you have X already — does that solve Y?" — cheaper than building a duplicate

## Anti-pattern

> "This audit needs its own backup mechanism; I'll create `backups/<date>/<name>.md`."

Without first checking whether the existing git auto-commit already handles this. (In this case: yes, exactly.)

## Why duplicates are actively harmful (not just wasteful)

- Two mechanisms that both claim to "back up" → maintenance splits → behaviors diverge over time
- Neither ends up being the trusted source → user has to mentally track which one is "real"
- Audit prompts lie by implication ("we back up before editing") when git is already the real backup

## The broader principle

**Before adding, audit.** Every new system has maintenance cost forever. Existing well-integrated mechanisms almost always beat parallel new ones. Only build new if the existing one genuinely cannot be adapted.

## Related
- `rules/evolution.md` — "Don't duplicate rules across files — one canonical location each"
- `rules/memory-rules.md` — same principle applied to memory content
