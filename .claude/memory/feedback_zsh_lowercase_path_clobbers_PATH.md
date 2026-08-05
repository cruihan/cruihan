---
name: zsh-lowercase-path-clobbers-PATH
description: In zsh, the lowercase variable `path` is bound to `PATH` — using it as a loop/temp variable silently destroys PATH and every later command dies with "command not found"
type: feedback
date_written: 2026-07-26
last_validated: 2026-07-26
---

**Rule.** Never use `path` (lowercase) as an ordinary shell variable in a Bash-tool command on this Mac. In zsh, `path` is a **special array parameter tied to `PATH`** (like `cdpath`/`fpath`/`manpath`). `path="/servizi/"` rewrites `PATH` to `/servizi/`, and every subsequent `node` / `tail` / `python3` / `grep` in that command fails with `command not found` (exit 127) — while the failing line looks completely innocent.

**Why:** 2026-07-25 prato-local-seo session, twice in a row. A CDP crawl loop did:

```zsh
for pair in "/:home" "/chi-siamo/:chi" …; do
  path="${pair%%:*}"; slug="${pair##*:}"
  node deep-extract.mjs "$B$path" "$slug" | tail -1
done
```

→ `(eval):12: command not found: node`, then on the retry `command not found: tail` and `command not found: python3`. The retry had `export PATH="/opt/homebrew/bin:…"` prepended as the "fix" — which of course did nothing, because the loop body re-clobbers `PATH` on its first iteration. ~30 minutes lost chasing a PATH/sandbox theory when the bug was one variable name.

**How to apply:** When writing any multi-command Bash invocation:
1. Never name a variable `path`. Use `p`, `url_path`, `route`, `subpath`.
2. Same hazard for the other zsh-tied lowercase names: `cdpath`, `fpath`, `manpath`, `module_path`, `argv`, `status`.
3. Symptom → cause shortcut: if a command suddenly reports `command not found` for a **core tool that obviously exists** (`tail`, `node`, `python3`, `grep`), suspect a clobbered `PATH` in the same command before suspecting the environment. Check for a lowercase `path=` assignment first — do not "fix" it by exporting PATH at the top.
4. Prefer `${(q)}`-free plain names, or `local`-scope inside a function, but the rename is the real fix.

Related: [[zsh-glob-nomatch-aborts]], [[shell-syntax-platform-match]], [[gate-exit127-tool-missing]].
