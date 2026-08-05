---
name: zsh-glob-nomatch-aborts
description: In zsh an unmatched glob aborts the ENTIRE command ("no matches found") — guard multi-glob listings with (N) or existence checks
type: feedback
date_written: 2026-06-09
last_validated: 2026-07-29
---

**Rule.** On this Mac (zsh, `NOMATCH` on by default), a glob that matches nothing raises `zsh: no matches found: <pattern>` and the whole command is **aborted before it runs** — unlike bash, which passes the literal pattern through. If you list several globs in one command (`ls -d a/*/ b/*/ c/*/`) and even one directory is empty/absent, none of them run.

**Why:** 2026-06-09 phase0-vton-test session, `ls -d mvp-test-output/*/ mvp-generated/*/ 03-generated/*/ …` exited 1 with `(eval):1: no matches found: mvp-generated/*/`. The dirs that *did* exist were never listed because the one missing glob killed the command. `2>/dev/null` does **not** save it — the abort happens at glob-expansion time, before redirection helps, and the command still fails with exit 1.

**How to apply:** When a Bash command contains a glob over a directory that might be empty or missing:
1. Append the zsh null-glob qualifier `(N)` to each pattern → `ls -d a/*/(N) b/*/(N)` (empty globs silently vanish instead of aborting).
2. Or `setopt local_options null_glob` at the top of the command.
3. Or test existence first: `for d in a b c; do [ -d "$d" ] && ls -d "$d"/*/; done`.
4. Quick escape hatch for one-off listings: wrap in `( setopt null_glob; ls -d a/*/ b/*/ )`.
5. Same trap inside flag values: `grep -r --include=*.html …` aborts identically (`no matches found: --include=*.html`) — quote it: `--include='*.html'`. (Recurred 2026-07-13, prato-local-seo session.)

Prefer the Glob tool for file discovery — it sidesteps shell glob semantics entirely.

Related: [[feedback_shell_syntax_platform_match]] — sibling "write for the actual shell on this host" lesson (that one is PowerShell-vs-bash; this one is a zsh-vs-bash glob-expansion difference).
