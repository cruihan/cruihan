---
name: destructive-cleanup-not-compound
description: Never bundle destructive cleanup (pkill / rm -rf) into compound Bash commands with other steps — the user denies the WHOLE compound; issue minimal standalone destructive calls (or mv to Trash)
type: feedback
date_written: 2026-07-18
last_validated: 2026-08-04
---

Compound Bash commands that mix destructive cleanup (`pkill -f …`, `rm -rf …`) with benign steps (echo, du, lsof, git clone) get denied as a unit — the benign work is lost along with the destructive part, and the denial gives no signal about which segment the user objected to.

**Why:** 3 denials across 2 sessions on 2026-07-17: `pkill … ; rm -rf /tmp/pfs-shot-profile | echo … | lsof …` (a6f2266d), `rm -rf "$SC/skillui-audit" | git clone …` and a `du … | rm -rf …/scratchpad/…` compound (39701feb). Each time the whole command died on the destructive segment.

**How to apply:** (1) Separate destructive steps into their own minimal Bash call so approval/denial is scoped to exactly that action. (2) Prefer `mv` to `~/.Trash/` over `rm -rf` for non-tmp paths (sibling rule: [[find-delete-needs-maxdepth]]). (3) Run the benign steps first; only then issue the cleanup call. (4) For browser/scraper teardown, follow [[scrape-autoclose]] — bake cleanup into the script's `trap` instead of ad-hoc pkill compounds.
