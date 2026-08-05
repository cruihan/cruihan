---
name: docs-to-mechanism
description: "When adding a discipline rule, ask if it can be *physically enforced* (slash command / shell alias / symlink / hook) before settling for *documented intent* (feedback file / CLAUDE.md prose). Mechanism wins; docs rot."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 222ae630-3a63-4de2-b95f-4124864a3396
date_written: 2026-07-20
last_validated: 2026-07-21
---

When user (or you) propose a new "discipline rule" — e.g., "remember to unload the local model", "always sync these two files", "use this trigger phrase before X task" — DO NOT immediately write it to a feedback file or CLAUDE.md prose. First ask: can this be *physically enforced* instead?

**Mechanism options to consider, in order:**
1. **Shell alias / wrapper** (e.g., `lms-load` that traps EXIT to unload) — enforces cleanup at shell level
2. **Symlink** (e.g., `~/.agents/skills` → `~/.claude/skills`) — physically eliminates drift between two locations
3. **Slash command** (e.g., `/daily <goal>` instead of "remember to type 'daily:' prefix") — Claude-native guaranteed fire
4. **Hook** (PreToolUse / PostToolUse / Stop / SessionStart) — runs without LLM remembering
5. **Auto-loaded rule with explicit trigger phrases** — fallback if 1-4 don't fit

**Why**: Validated 3x in ULTRAPLAN arc 2026-05-17→18. Every place we converted intent → mechanism (slash commands, lms aliases, ~/.agents/skills symlink), the rule stopped getting violated. Every place we left it as "Claude will remember", it eventually drifted.

**How to apply**: When user says "remember to X" or "next time, do Y" — push back gently: "Should this be a [alias/symlink/slash command/hook] instead of a memory entry?" Then either build the mechanism or, if it must be a memory, explain why mechanism didn't fit.

**Edge case — when mechanism is wrong choice**:
- One-off task (no recurrence) → memory or just inline notes
- Mechanism would require destructive change (e.g., deleting user-owned files) → memory + escalate
- Cost > benefit (e.g., writing PreToolUse hook to enforce a once-a-month thing) → memory

**Cross-links**:
- [[2026-05-18-ultraplan-arc-lessons]] vault note (the source incident)
- [[autonomy-policy]] rules — mechanism changes ≤ 50 lines and reversible are auto-apply OK
