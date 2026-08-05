---
name: dash-prefixed-paths-need-guard
description: Paths starting with `-` (e.g. ~/.claude/projects/-Users-ac-Projects/) get parsed as CLI options — always use `--` or `./` prefix; with 2>/dev/null the failure becomes a silent wrong answer
type: feedback
date_written: 2026-07-25
last_validated: 2026-07-29
---

Always guard dash-prefixed file arguments with `--` (or prefix `./`) in grep/rm/ls/cat/etc. Never combine an unguarded dash path with `2>/dev/null`.

**Why:** Claude Code encodes project dirs as `-Users-ac-Projects-...` under `~/.claude/projects/`, so relative paths there start with `-`. Night consolidation 2026-07-25 Phase 1 ran `grep -o '"is_error":true' -Users-ac-Projects/x.jsonl 2>/dev/null | wc -l` — grep treated the path as options, errored, stderr was swallowed, and `wc -l` reported 0 for every file. The real count was 25 errors across sessions; the analysis phase almost concluded "no errors today" from a broken command.

**How to apply:** (1) Any command touching `~/.claude/projects/*` relative paths: write `grep pattern -- "$f"` or use absolute paths. (2) Treat a uniform all-zero/empty result from a piped command as a red flag — re-run once without `2>/dev/null` before trusting it. Related: [[feedback_zsh_glob_nomatch]], [[feedback_verify_before_claiming]].
