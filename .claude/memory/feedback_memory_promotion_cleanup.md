---
name: memory-promotion-cleanup
description: When promoting a feedback_*.md from project-local to global, audit ALL project memory dirs for stale duplicates — not just the originating project
type: feedback
date_written: 2026-05-24
last_validated: 2026-05-24
---

# Memory-promotion cleanup must sweep all project memory dirs

When a `feedback_*.md` file is promoted from project-local memory to global
(`~/.claude/memory/`), the originating project's copy should be deleted to prevent drift.
**But other projects may already have the same file** — they need cleanup too.

## Why
2026-05-24 audit found `feedback_santa_routing.md` as exact MD5 duplicates in:
- `~/.claude/memory/feedback_santa_routing.md` (global, the canonical copy)
- `~/Projects/external/openclaw/.claude/memory/feedback_santa_routing.md` (stale duplicate)
- `~/AI/apps/ComfyUI/.claude/memory/feedback_santa_routing.md` (stale duplicate)

The 2026-05-24 STATE noted "deleted project-level copy" — but only deleted the
warehouse-os copy. openclaw + ComfyUI copies were missed because the promotion
procedure didn't audit other project memory dirs.

Cost of missing them: when the global rule evolves, the stale copies stay frozen
and silently override the global in their respective project contexts.

## How to apply
When promoting any feedback file to global, run:
```bash
find ~/Projects ~/AI ~/Documents -maxdepth 5 -type f \
  -path "*/.claude/memory/<filename>.md" \
  -not -path "*/node_modules/*" 2>/dev/null
```
For each match found, either:
- Delete it (if content matches the new global — `diff` to verify)
- Supersede it (if it has project-specific divergence worth preserving — rare; usually
  the answer is delete + restate the project-local override at the project's CLAUDE.md level)

## When this fires
- Any time `write-vault-note` / `upgrade-skill` / arc-wrap proposes promoting a
  project-local feedback to global memory.
- Any "promoted X to global, deleted project copy" decision in STATE.md.
