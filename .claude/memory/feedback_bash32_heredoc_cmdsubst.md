---
name: bash32-heredoc-cmdsubst
description: On macOS /bin/bash 3.2, never nest a here-doc inside $(...) when building LLM prompts — write to a temp file instead
type: feedback
date_written: 2026-06-18
last_validated: 2026-06-18
---

On macOS the only `bash` is `/bin/bash` 3.2.57 (no bash 4+/5 installed; `#!/usr/bin/env bash` resolves to it). bash 3.2 mis-parses a here-doc that is nested inside command substitution — `cmd -p "$(cat <<'EOF' ... EOF)"` — when the here-doc body contains parentheses: it miscounts parens, closes the `$(...)` early, and then parses the rest of the body as LIVE shell. Any backtick span in that tail (e.g. markdown inline-code `` `verdict` ``) is executed as a command, producing `command not found` noise, silently blanking those spans out of the prompt, and — worst — actually running whatever the backtick contains (a real `bash …/evolution-apply.sh …` invocation fired nightly this way).

**Why:** Single-quoted here-doc delimiters (`<<'EOF'`) normally suppress all expansion, so this looks safe. It is safe on bash 4.4+ and even in simple short bodies on 3.2, which is why it passes `bash -n` and casual testing — but the full multi-line body with parens triggers the 3.2 parser bug. Root-caused 2026-06-18 in `night-consolidation.sh:107` (Phase E backticks were being executed every night); confirmed by exact-signature reproduction. `dream-flow.sh:14` uses the same risky pattern but currently does NOT trigger (its paren layout stays balanced AND it has zero backticks) — left unchanged, but one stray backtick would break it.

**How to apply:** When a shell script must build a large prompt and pass it to a CLI (`claude -p`, etc.), do NOT inline `$(cat <<'EOF' … EOF)`. Write the here-doc to a temp file with a plain redirect first, then read it back:
```bash
PF="$(mktemp -t prompt.XXXXXX)"; trap 'rm -f "$PF"' EXIT
cat > "$PF" <<'EOF'
…body with parens and `backticks` is now 100% literal…
EOF
mycli -p "$(cat "$PF")"; rm -f "$PF"; trap - EXIT
```
The here-doc is now a standalone command (no `$(...)` around it) so 3.2 parses it correctly; reading the file back never re-evaluates its bytes. Related: [[shell-syntax-platform-match]], [[python-heredoc-literals]].
