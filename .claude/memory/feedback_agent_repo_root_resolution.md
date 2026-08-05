---
name: agent-repo-root-resolution
description: Automated agents fed relative file lists must resolve the repo root from cwd first — never guess /home/user/… paths; working-dir denials are path-based, don't retry with command variants
type: feedback
date_written: 2026-07-02
last_validated: 2026-07-29
---

Any automated agent (security-review, code-reviewer, qa) given relative file paths must resolve the actual repo root FIRST (pwd / Glob / the cwd named in the first error message) before its first Read. Never guess a Linux-container root like `/home/user/<relpath>` — this machine is macOS, repos live under `/Users/ac/Projects/`. Corollary: a working-directory denial ("may only list files in the allowed working directories") is **path-based, not command-based** — retrying the same path with `cat`→`head` variants is pure waste; report `unverified — needs repo-root scope` instead.

**Why:** 2026-07-01: 8 of 10 tool errors across 5 automated security-review sessions in phase0-vton-test/studio were the same `/home/user/...` root-guess, each costing 1–2 wasted turns; one session burned 3 consecutive blocked attempts (ls→cat→head) on a sibling package (`cockpit/`) outside the sandbox. Also a review-quality gap: reviews scoped to a sub-package structurally cannot trace cross-package sinks — launch cross-package reviews from the repo root.

**How to apply:** When authoring/patching agent or review prompt templates, include the absolute repo root in the prompt, or mandate "resolve root via cwd before first Read". When an agent hits a path denial once, stop retrying that path. Agent file `~/.claude/agents/security-review.md` patched 2026-07-02.

**Recurrence 2026-07-29 — the guessed root is NOT always `/home/user`.** 12 more failed first-Reads in 24h across prato-local-seo / business-ops sessions, and the guessed roots now include **arbitrary hallucinated home dirs**: `/home/user/…`, `/home/user/repo/…`, `/Users/nickvincent/dev/…`, `/Users/ksd/dev/…`. So a guard that only pattern-matches `/home/user` under-catches. The invariant to check is the opposite one: **the first Read of an agent given relative paths must start with `/Users/ac/`** (this machine's only real repo root) — anything else is a guess, regardless of how plausible the username looks. Cost stays low (self-recovers in 1–2 turns from the cwd in the error text); still the single largest error signature in the nightly scan.

**Recurrence 2026-07-03 (still open):** ~20 more `/home/user/…` guessed Reads in phase0-vton-test/studio sessions. These come from the **security-guidance plugin's stop-time review** (canned prompt "Review this change for security vulnerabilities" + relative file list) — a separate entry point from `agents/security-review.md`, so the 07-02 patch doesn't cover it. Each session self-recovers after 1–2 failed Reads (correct cwd is shown in the error), so cost is low but constant. Fix would require patching the plugin's prompt template (third-party, overwritten on update) — accepted as known noise unless the plugin exposes a prompt override.
