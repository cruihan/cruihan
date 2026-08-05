---
name: shell-syntax-platform-match
description: Match shell command syntax to the actual shell/OS — no PowerShell idioms on bash, no GNU coreutils long-flags (mktemp --suffix, sed -i, date -d) on macOS BSD, and no bash word-splitting assumptions on zsh (unquoted $VAR in for-loops doesn't split)
type: feedback
date_written: 2026-05-22
last_validated: 2026-07-22
---

Before running a Bash tool command, confirm the syntax matches the active shell. On macOS/Linux, the shell is `bash`/`zsh` — never use PowerShell idioms like `2>$null`, `Select-String`, `Out-Null`, `Get-ChildItem`, `Where-Object`, or backtick line-continuations. Use `2>/dev/null`, `grep`, `> /dev/null`, `ls`/`find`, `grep`/`awk`, and `\` instead.

**Why:** Multiple sessions on this Mac (Darwin 25, zsh shell) emitted commands like `ls ~/vault/permanent/ 2>$null | Select-String -Pattern "..." -SimpleMatch` which fails with `Exit code 127 — Select-String: command not found, $null: ambiguous redirect`. Pattern is residue from prior Windows sessions; on this host the shell is bash and these tokens are nonsense.

**How to apply:** When the environment says `Platform: darwin` or `Shell: zsh/bash`, write bash. Common translations:

| PowerShell | bash/zsh |
|---|---|
| `2>$null` | `2>/dev/null` |
| `> $null` | `> /dev/null` |
| `Select-String -Pattern X` | `grep -E X` (use Grep tool when searching files) |
| `Get-ChildItem` | `ls` / `find` |
| `Out-Null` | `> /dev/null` |
| `Where-Object { $_.X -eq Y }` | `awk '$1 == "Y"'` / `grep` |
| backtick line-continuation | `\` |

If you genuinely need PowerShell (rare — only on a Windows host), the environment line says `Platform: win32`. Otherwise default to POSIX. When in doubt, the Grep tool covers file-content search without any shell at all.

## GNU vs BSD coreutils (the macOS trap)

Same family, different cause: macOS ships **BSD** coreutils, not GNU. Long-form GNU flags the model "remembers" silently fail here. This is NOT a live-Bash-only problem — it bites **committed automation scripts** hardest, because the failure recurs every scheduled run until someone reads the log (root cause of `dashboard-llm-summarizer.sh` failing on every night-consolidation for ~3 weeks: `mktemp --suffix=.txt` → `unrecognized option`, fixed 2026-06-21).

| GNU (Linux) | BSD / macOS portable form |
|---|---|
| `mktemp --suffix=.txt` | `mktemp "${TMPDIR:-/tmp}/name.XXXXXX"` (drop suffix; trailing X's only) |
| `sed -i 's/x/y/'` | `sed -i '' 's/x/y/'` (BSD needs the empty backup arg) |
| `date -d '2 days ago'` | `date -v-2d` |
| `date -d @1700000000` | `date -r 1700000000` |
| `find . -printf '%p\n'` | `find . -exec stat -f '%N' {} +` |
| `readlink -f path` | `readlink path` / `realpath` (or `python3 -c`) |
| `grep -P '\d'` | `grep -E '[0-9]'` (BSD grep has no `-P`) |
| `head -c -N` / `tac` | `tail -r` for tac; avoid negative `-c` |

**How to apply:** When `Platform: darwin`, prefer POSIX-portable forms or test the exact invocation once (`mktemp ...; echo $?`) before baking it into a committed script. If a script must run on both Linux and macOS, write the portable form — never the GNU long flag.

## zsh vs bash: unquoted `$VAR` does NOT word-split (the interactive-Bash-tool trap)

The Bash tool on this host runs **zsh**, and zsh does **not** perform word-splitting on unquoted parameter expansion the way bash does. So `SAMPLE="a b c"; for x in $SAMPLE; do ...` iterates **once** over the whole string `"a b c"`, not three times — and any per-item test (`[ -f "$x" ]`) then fails on the joined string. This silently produced two false "BROKEN ref" alarms during a 2026-07-22 skill audit (a `for r in $refs` over newline-joined grep output).

| Intent | zsh-safe form |
|---|---|
| Iterate words of a var | `for x in ${=SAMPLE}` (zsh split flag) · or `arr=(a b c); for x in $arr` · or `for x in a b c` (literal list) |
| Force bash word-splitting semantics | run the whole snippet under `bash -c '...'` |
| Iterate lines of command output | `grep ... | while read -r x; do ...; done` (pipe, don't capture-then-`for`) |

**Why:** A `for x in $VAR` loop that "should" fan out but doesn't is invisible — it runs, exits 0, and just processes one bogus item. Cost is wrong conclusions (false broken-reference reports), not a crash. **How to apply:** whenever a Bash-tool loop iterates over a captured variable's words, use a literal list, an array, `${=VAR}`, or a `while read` pipe — never bare `for x in $VAR` on zsh. Same root family as the PowerShell/GNU-BSD mismatches above: assuming bash semantics on a non-bash shell.
