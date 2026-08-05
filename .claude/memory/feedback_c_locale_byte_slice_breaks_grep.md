---
name: c-locale-byte-slice-breaks-grep
description: Bash `${var:0:N}` slices by BYTES under LC_CTYPE=C — truncating CJK text mid-UTF-8-sequence corrupts the output file, after which BSD grep silently matches NOTHING in it (rc=1 on strings that are present)
type: feedback
date_written: 2026-07-30
last_validated: 2026-07-30
---

Two bugs that only bite together, and the second one is silent.

1. macOS shells here inherit `LC_CTYPE=C` (check with `locale`). Under it, `${#s}` counts **bytes** and `${s:0:177}` cuts at a **byte** offset — so a 3-byte CJK character gets sliced in half and the output contains invalid UTF-8.
2. A file with invalid UTF-8 is no longer "text" to BSD tooling — `file` calls it *"Non-ISO extended-ASCII text, with LF, NEL line terminators"* — and **`grep` then returns rc=1 for strings that are demonstrably in the file**. No error, no warning, no "Binary file matches". Just a clean, wrong, empty result.

**Why:** `automation/utils/scan-all-skills.sh` truncates each skill description to 177 chars. Once CJK descriptions landed in the registry (`code-graph-queries`, `excalidraw-diagram`), every regeneration produced a corrupt `skills-registry.md`. On 2026-07-29 the nightly audit grepped it for `client-site`, got nothing, and wrote a HIGH alert saying the skill was "listed in NEITHER index". It *was* listed — `python3 open(...,'rb').read().count()` found it twice. **The alert was measurement error reported as a system defect.** Fixed 2026-07-30 with `export LC_CTYPE=en_US.UTF-8` in that script (LC_CTYPE only — setting `LC_ALL`/`LC_COLLATE` would also reorder the glob and churn the diff).

**How to apply:**
1. Any bash script that truncates text which *might* contain non-ASCII → `export LC_CTYPE=en_US.UTF-8` at the top. Applies to registry/index/summary generators above all, since their output is what later greps trust.
2. **When a grep for something you have good reason to believe is present returns 0 hits, run `file` on the target before believing it.** `Non-ISO extended-ASCII` / `NEL line terminators` = the file is corrupt, not the claim wrong. Confirm with `python3 -c "print(open(p,'rb').read().count(b'needle'))"` — reading bytes is immune to this.
3. Never write an audit finding whose whole evidence is a *negative* grep result on a generated file without that check — see [[feedback_no_fake_metrics]] (a metric must be honest before it is surfaced).

Related: [[feedback_shell_syntax_platform_match]] (BSD-vs-GNU behavioural splits) · [[feedback_skill_triggers_audit_parsing]] (same failure family: the nightly audit's parser producing false findings) · [[feedback_dash_prefixed_paths_need_guard]] (silent wrong zeros from a different cause).
