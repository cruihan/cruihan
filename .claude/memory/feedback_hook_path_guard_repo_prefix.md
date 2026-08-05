---
name: hook-path-guard-must-anchor-repo-prefix
description: PostToolUse/path-matching guards must anchor on the repo PREFIX ("$REPO/"*), not a bare */.claude/* substring — per-project .claude/ dirs also match and cause "git add ... outside repository" fatals
type: feedback
date_written: 2026-07-06
last_validated: 2026-07-06
---

Any hook or script that decides "is this file inside the global `~/.claude/` repo?" must test the **repo prefix** (`[[ "$FILE_PATH" == "$REPO/"* ]]`), never a bare substring like `*"/.claude/"*`.

**Why:** `git-autocommit.sh` used `if [[ "$FILE_PATH" != *"/.claude/"* ]]; then exit 0`. That substring also matches per-project config dirs (`~/Projects/<p>/.claude/STATE.md`), so the hook fell through, `cd`'d to `~/.claude`, and ran `git add <that-file>` → non-blocking `fatal: ... is outside repository at '/Users/ac/.claude'`. Fired 8× in one 2026-07-06 session (phase0-vton-test STATE files) as PostToolUse hook noise. `git add` had no `2>/dev/null`, so the fatal leaked to hook stderr. Fixed by anchoring the guard to `"$REPO/"*`.

**How to apply:** Triggers whenever writing/reviewing a hook or automation script that filters paths to "the ~/.claude repo". Anchor on the absolute repo prefix, not `.claude` as a floating substring — every project now has its own `.claude/`. Same trap applies to any `*/.git/*` / `*/node_modules/*` style guard when a nested project can contain the same dir name. Suppress `git add` stderr too if a stray path can still reach it. Related: [[feedback_agent_repo_root_resolution]] (repo-root resolution family), [[feedback_test_before_ship]] (both guard branches tested before shipping this fix).
