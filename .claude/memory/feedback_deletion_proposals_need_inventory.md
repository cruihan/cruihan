---
name: Never propose deleting a directory whose contents you have not enumerated
description: Disk-cleanup recommendations must list what is actually inside each candidate path first; "looks like cache/junk" is not evidence, and the user has had to rescue real working assets from such proposals
type: feedback
date_written: 2026-08-04
last_validated: 2026-08-04
severity: high
---

## Rule: Before recommending ANY directory for deletion, enumerate its contents and state what each part is. Size alone is never a reason.

**Why:** During the 2026-08-03 disk-cleanup session (`-Users-ac-Projects`), a cleanup proposal was assembled largely from directory names and sizes. The user had to manually rescue two separate paths from it:
- `~/Projects/external` — 「External 为什么你建议我们把它删掉呢？我觉得其实 external 这个东西应该还是有用的」 — it held previously-downloaded projects, agents and tools still in use.
- Instagram-related browser profiles — 「IG 这些我一定要保留的，麻烦你别删」, repeated a second time later in the same session after the first rescue.

Both were judged disposable because of what their parent folder looked like, not because anything inside had been read. A cleanup pass that needs the user as its safety net has inverted the point of running it — and the failure is asymmetric: keeping junk costs disk, deleting a working asset costs unrecoverable work.

**How to apply:**
1. For every deletion candidate, run an inventory first (`du -sh <dir>/*`, `find <dir> -maxdepth 2`, and actually read a sample) and put the findings in the proposal.
2. Classify each item as **regenerable** (model weights re-downloadable, build output, HTTP cache) vs **irreplaceable** (logged-in browser profiles, scraped corpora, downloaded repos/agents/tools). Only regenerable items may be proposed for deletion, and say in one line *how* it regenerates.
3. Anything you cannot classify is a **question, not a candidate** — ask what it is rather than defaulting it into the delete list.
4. Treat login-bearing browser profiles as irreplaceable by default: their value is the session state, which is invisible to `du`.
5. Execution still follows the existing mechanics — separate minimal destructive calls ([[destructive-cleanup-not-compound]]), `mv` to `~/.Trash/` over `rm -rf` ([[find-delete-needs-maxdepth]]).
