---
name: rename-blast-radius
description: Naming rules for ~/Projects/ live in ~/Documents/PERSONAL_SYSTEM/NAMING_CONVENTION.md Part 2 — read it before any dir/repo/skill rename, count references first, and prove 0 live hits of the old name afterwards
type: feedback
date_written: 2026-07-30
last_validated: 2026-08-03
---

Renaming a project dir, GitHub repo, or skill is a **cross-tree** operation, and the canonical rules are NOT in `~/.claude/`.

**Where the rules live:** `~/Documents/PERSONAL_SYSTEM/NAMING_CONVENTION.md` — Part 1 = personal documents, **Part 2 = `~/Projects/` directory naming** (8 rules + a blast-radius checklist + the 2026-07-30 precedent table). Nothing under `~/.claude/` points there, so it will not be found by browsing; this memory is the routing entry.

**Why:** On 2026-07-30 the user raised naming inconsistency in three separate sessions — local folder vs GitHub repo out of sync (`prato-local-seo` local / `bottega` remote), pinyin-English mixes, ordinal prefixes (`P0`/`P05`) they wanted gone, and most sharply: *"之前我教你去更改名字的这些很多 skill，你是否有更改？我感觉好像你没有更改"* — renames reported as applied that were not. A rename that lands in one of {local dir, remote repo, README/pointers, executable path references, memory/rules files} and not the others is worse than no rename: two names now both look current.

**How to apply:**
1. Before proposing a rename, read NAMING_CONVENTION.md Part 2 — the rule set already covers status words, pinyin, version chains, geographic lock-in, and folder-context duplication.
2. Measure the blast radius **before** touching anything: `grep -rl '<oldname>' ~/Projects ~/.claude --exclude-dir=.git | wc -l`. Report the count; a big number is a reason to ask, not to proceed quietly.
3. Rename all five surfaces together: local dir · GitHub repo · README/pointer docs · executable path references (devctl, scripts, plists) · memory + rules files.
4. **Verify, don't assert.** Finish with a grep sweep proving the old name has 0 live hits (read-only history/archives excluded, and say which you excluded). Claiming "renamed" without that sweep is the exact failure above — see [[feedback_verify_before_claiming]].
5. Retired projects go to `~/Projects/_archive/<name>` with an entry in `~/Projects/_archive/INDEX.md`, not deleted.

Cross-links: [[feedback_docs_to_mechanism]] (the convention file IS the mechanism — don't restate its rules here) · [[feedback_verify_before_claiming]] · [[feedback_deliverable_paths]].
