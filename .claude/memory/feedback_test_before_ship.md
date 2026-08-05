---
name: Test core files before shipping
description: Every change to automation scripts, hooks, or config files must be tested for syntax + functional correctness before being considered done
type: feedback
date_written: 2026-04-05
last_validated: 2026-07-05
---

## Rule: Test every core file change — syntax + functional

> **Scope:** automation infrastructure only — bash scripts, hooks, JSON config,
> .gitignore, memory files, anything in `~/.claude/`. For **application code**
> (TypeScript / React / server actions shipped to users) see the sibling rule
> `feedback_app_code_test_discipline.md` (Vitest + Playwright + Neon branching).
> Both rules apply; they cover different code types.

**Why:** On 2026-04-05, a full audit of the automation system found 3 bugs that had been committed and pushed without testing:
1. `error-tracker.sh` — missing `)` in node one-liner caused silent parse failure; all Bash errors were silently dropped
2. `git-autocommit.sh` — operator precedence bug (`a || b && c` = `(a||b)&&c`) meant tracked files were never committed — the main use case, completely broken
3. `.gitignore` — newly created log files (`rejection-tracker.log`, `skill-usage.log`) were missing, causing them to appear as untracked noise in every `git status`

All three were caught only in an explicit audit session. None were caught at write time.

**How to apply:**

After writing or editing ANY of these file types, run the corresponding test before moving on:

### Shell scripts (`*.sh`)
```bash
bash -n script.sh              # syntax check — always
echo '{"mock":"json"}' | bash script.sh   # functional smoke test with mock input
```

### Hook scripts (error-tracker, skill-tracker, rejection-tracker, git-autocommit)
- Always test with at least 2 mock inputs: one that SHOULD trigger the action, one that should NOT
- Verify the log file is actually written (check line count before/after)
- Verify false-positive cases produce no output

### Node one-liners embedded in bash
- Run the node snippet in isolation first with a known input before embedding it
- Count parentheses manually: every `process.stdin.on('end', ()=>{` needs a matching `})` at the close

### `.gitignore`
- After adding any new log file or auto-generated file to the system, immediately check: `git status --short` — if it appears as `??`, add it to .gitignore

### Memory files
- After writing: verify frontmatter has `name`, `description`, `type`, `date_written`, `last_validated`
- After updating MEMORY.md: verify the new entry is linked to a real file

**Checklist before marking a core file change as done:**
- [ ] `bash -n` passes
- [ ] Functional test with mock input passes
- [ ] Negative test (should-not-trigger) passes  
- [ ] `git status` shows no surprise untracked files