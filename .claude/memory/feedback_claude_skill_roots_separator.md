---
name: claude-skill-roots-separator-mismatch
description: CLAUDE_SKILL_ROOTS separator must match between settings.json (':') and every consumer script — a ';'-only split makes business-ops skills invisible to that tracker
type: feedback
date_written: 2026-05-15
last_validated: 2026-07-02
---

**Rule.** `$CLAUDE_SKILL_ROOTS` separator must match across the env var (in `~/.claude/settings.json`) and every consumer script (currently 7 files all use `IFS=';'`). Pick one convention and apply globally — either change settings.json from `:` to `;`, or change all consumer scripts to accept `:` (Unix-natural) or both (e.g. `tr ';' ':'` then split on `:`).

**Why:** Night consolidation 2026-05-15 found settings.json sets `CLAUDE_SKILL_ROOTS="/Users/ac/.claude/skills:/Users/ac/Projects/business-ops/.claude/skills"` while every consuming script (`scan-all-skills.sh`, `token-benchmark.sh`, `health-check.sh`, `drift-warning.sh`, `skill-tracker.sh`, `skill-judge.sh`, `capture-detector.sh`) does `IFS=';' read -ra SKILL_ROOTS <<< "${CLAUDE_SKILL_ROOTS}"`. Result: the whole colon-joined string is treated as one literal path that doesn't exist, both roots are silently dropped to `DEFAULT_ROOT`, and the 30 business-ops skills documented in `rules/skills.md` are invisible to every tracker, audit, and judge. This is a multi-file decision (cross-rule, ≥2 files in `rules/`-tier scripts) so it escalates per autonomy-policy — surfaced here for user direction.

**How to apply:** When the user asks about skill counts that look wrong, missing-skill audits, or "why doesn't X skill show up in the dashboard," check this separator first. When fixing, either flip settings.json (single-line change, fastest) or write a tiny helper at `automation/utils/parse-skill-roots.sh` (NOT built — optional DRY refactor, not an existing file) that normalizes both separators and have all 7 scripts source it (more invasive but DRY).

Related: [[feedback_test_before_ship]] — the original mismatch shipped without a smoke test that confirmed both roots actually resolved to existing directories.

**Status 2026-07-02 (night consolidation):** the tolerant `IFS=':;'` pattern is now the convention in ALL consumers — the last 3 `;`-only holdouts (`drift-warning.sh`, `token-benchmark.sh`, `capture-detector.sh`) were fixed and smoke-tested this run. The rule stays: any NEW consumer script must use `IFS=':;'` (or source a shared parser), never a single-separator split.

**Status 2026-08-03 — the 2026-07-02 "ALL consumers" claim was FALSE.** That sweep covered
shell scripts only. `automation/utils/skill-eval.py` split on `";"` alone the entire time, so
its `SKILL_ROOTS` resolved to the whole colon-joined string as ONE nonexistent path — the exact
2026-05-15 failure, still live 80 days later inside a file the "fix" never looked at. Fixed now
(`re.split(r"[:;]")`, verified: 2 roots, 37+20=57, matching health-check).

**The lesson is about the sweep, not the separator.** A fix scoped by file *extension* silently
excludes consumers written in another language. When closing a class of bug, enumerate consumers
by `grep -rn <symbol> .` with NO `--include` filter, then check each one — that is how the Python
holdout surfaced, and it is the only method that would have caught it in July.

Full consumer list, all verified 2026-08-03: `token-benchmark.sh` · `health-check.sh` ·
`drift-warning.sh` · `capture-detector.sh` (all `IFS=':;'`) · `scan-all-skills.sh` ·
`skill-tracker.sh` (both `${v//;/:}` then `IFS=':'`) · `skill-trigger-score.py` ·
`memory-validator.js:321` (both `split(/[:;]/)`) · `skill-eval.py` (fixed today). Also corrected
`prompts/audits/tier-map.json`, whose `locations_note` still said "semicolon-separated".

Related: [[feedback_dashboard_counts_rows_not_findings]] — a detector that never re-checks its own
prior finding reports the same stale item forever; here a *fix* that never re-checked its own scope
left a live instance behind while the log said "closed".
