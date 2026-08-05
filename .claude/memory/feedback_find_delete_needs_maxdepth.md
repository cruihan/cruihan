---
name: find-delete-needs-maxdepth
description: danger-block hook blocks `find ... -delete` on non-tmp paths unless -maxdepth is present — add -maxdepth or pipe to mv-trash
type: feedback
date_written: 2026-05-19
last_validated: 2026-08-02
---

**Rule.** Any `find <path> ... -delete` targeting a non-`/tmp` path MUST include an explicit `-maxdepth` (e.g. `-maxdepth 1`) or be rewritten to pipe matches to a trash-move. Otherwise the `danger-block.sh` PreToolUse hook blocks the command.

**Why:** Night consolidation 2026-05-19 Phase 0b ran `find ~/.claude/shell-snapshots/ -type f -mmin +2880 -delete` and got `🛑 [danger-block] BLOCKED: find -delete without -maxdepth on non-tmp path is risky`. An unbounded recursive `-delete` can walk into unexpected subtrees; the hook enforces a depth bound as a guardrail. Re-running with `-maxdepth 1` succeeded immediately.

**How to apply:** Whenever writing a cleanup command that uses `find ... -delete` (shell-snapshots, stale logs, abandoned session jsonls, report pruning), add `-maxdepth N` matching the intended depth before the `-delete`. For deeper trees where a depth bound is wrong, list matches and `mv` them to `~/.Trash` instead. The bundled `night-consolidation.sh` cleanup phase and any ad-hoc Phase 0b deletions both hit this.

**Sibling danger-block rule (`rm -rf` → trash):** the same hook also blocks `rm -rf` on any non-trash path with `🛑 Prefer mv to ~/.Trash/ for reversibility`. Same family, same fix: during bulk teardown/cleanup work, default to `mv <targets> ~/.Trash/` rather than `rm -rf`. Both rules fired in the 2026-06-30 instcre-teardown cleanup arc (find -delete 2×, rm -rf 1×) — the guard is working as designed, so write the trash-move form up front instead of hitting the block and retrying.

**False-positive caveat (2026-06-16):** `danger-block.sh` matches the literal token `find ... -delete` anywhere in the command *string*, not just when it's the executed verb. A `grep -ril "find -delete" memory/` lookup got blocked even though it deletes nothing. When grepping/echoing about this pattern, reword the search string (e.g. search `maxdepth` or `mv-trash` instead) to avoid tripping the guard.

Related: [[feedback_test_before_ship]] — the block is caught at runtime, so test cleanup commands on a dry path first; [[feedback_editing_discipline]].
