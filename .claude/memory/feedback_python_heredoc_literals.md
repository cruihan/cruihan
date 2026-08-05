---
name: python-heredoc-literals
description: Python embedded in bash heredocs/`-c` must use Python literals (True/False/None) and avoid backslash-escaped quotes inside f-string expressions
type: feedback
date_written: 2026-06-07
last_validated: 2026-06-27
---

**Rule.** When generating Python that runs inside a bash heredoc (`python3 - <<'PY'`) or `python3 -c`, write *Python* — not JSON or JS, and not shell-escaped strings:
1. Booleans/null are `True` / `False` / `None`, never `true` / `false` / `null`.
2. Do not put backslash-escaped quotes (`\"`, `\'`) inside an f-string's `{...}` expression part. Use a different outer quote or precompute the value into a variable first.

**Why:** 2026-06-07 phase0-vton-test session, two failures from the same root cause:
- `NameError: name 'true' is not defined. Did you mean: 'True'?` — a JSON/JS boolean was pasted into a Python block.
- `SyntaxError: unexpected character after line continuation character` from `print(f"… reg={reg or \"-\"} …")` — the `\"` inside the f-string expression was read as a line-continuation. Python's f-string grammar forbids backslashes in the expression part.

Both come from carrying JSON/JS habits or shell-quoting reflexes into generated Python.

**How to apply:** Before running a `python3` heredoc/`-c` block, scan the source for: bare `true`/`false`/`null` (→ `True`/`False`/`None`), and any `\"`/`\'` sitting inside `f"...{ }..."` braces. Fix the f-string by switching the inner literal to single quotes (`f"reg={reg or '-'}"`) or assigning it to a variable above the f-string. Prefer the `<<'PY'` quoted-heredoc form so the *shell* leaves the Python body untouched — then the only escaping that matters is Python's own.

Related: [[feedback_shell_syntax_platform_match]] — sibling "write for the right language/runtime" lesson; [[feedback_test_before_ship]] — a quick `python3 -c` smoke check catches these before the real run.
