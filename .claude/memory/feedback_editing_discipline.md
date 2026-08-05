---
name: Editing discipline — verify before deleting
description: Mistakes made deleting CLAUDE.md content that appeared redundant but wasn't — rules for safe editing
type: feedback
date_written: 2026-04-05
last_validated: 2026-07-05
---

## Rule: Never delete content from CLAUDE.md without line-by-line verification

**Why:** On 2026-04-05, deleted two sections thinking they were redundant with Layer 4. Both were wrong calls:

1. **Deleted "Background (optimization layer)" flows** — Thought it repeated Layer 4's definitions. It didn't. Layer 4 names the processes; the flows show the *execution diagrams* (how to actually run them). These are complementary, not duplicate.

2. **Deleted "Background Optimization: What It Is"** — Same error. Layer 4 lists four processes. This section added unique content not in Layer 4:
   - "Idle agents" concept (entirely absent from Layer 4)
   - Ordered phases: Log → Consolidate → Evaluate → Evolve
   - Critical rule: "Do not skip to Phase 4 without Phases 1–3"

**How to apply:** Before deleting any section:
- Read the section to delete AND the section it supposedly duplicates, side by side
- Ask: "Does every concept, rule, and example in the deleted section already exist word-for-word or meaning-for-meaning elsewhere?"
- If any concept is unique (even one bullet), do NOT delete — trim instead
- When in doubt, ask the user first rather than acting

---

## Rule: Script success messages must be conditional on actual success

**Why:** `fix-task-path.ps1` printed `[OK] Task now points to: ...` unconditionally — even when `Register-ScheduledTask` threw "Access is denied". User ran it and saw green [OK] but the task was never updated.

**How to apply:** Always gate success messages behind actual result checks:
```powershell
# Wrong
Register-ScheduledTask ... | Out-Null
Write-Host "[OK]" -ForegroundColor Green   # runs even on failure

# Correct
$result = Register-ScheduledTask ...
if ($result) { Write-Host "[OK]" } else { Write-Host "[FAIL]" -ForegroundColor Red }
```
Same principle applies to bash: check `$?` or exit codes before printing success.