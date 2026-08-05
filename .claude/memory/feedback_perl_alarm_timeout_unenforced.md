---
name: perl-alarm-timeout-unenforced
description: On macOS (no GNU `timeout`), the `perl -e 'alarm shift; exec @ARGV'` fallback does NOT reliably kill a Node-based child (`claude -p`) — caps silently fail; install coreutils `gtimeout` or kill the process group
type: feedback
date_written: 2026-06-30
last_validated: 2026-07-20
---

When a scheduled script needs a hard wall-clock cap on a child process and falls back to `perl -e 'alarm shift; exec @ARGV' "$secs" "$@"` because macOS lacks GNU `timeout`, the cap is **not actually enforced** against a Node.js CLI like `claude`. The pending SIGALRM survives `exec`, but the launched `claude` either ignores SIGALRM or spawns the real node process as a non-exec child that orphans past the alarm — so the watchdog kills nothing.

**Why:** `evolution-armed-council.sh` sets `EVO_COUNCIL_TIMEOUT=1800` (30 min hard cap) and routes through `run_capped` → perl-alarm fallback (this Mac has neither `timeout` nor `gtimeout` on PATH, confirmed 2026-06-30). On 2026-06-28 the council `claude -p` call ran **11204s (~3.1 h)** before failing — 6× over the cap — burning the slot and contributing to two consecutive night-consolidation LLM-phase `exit=1` runs (failure_count 1→2, Jun 28 & 29). The script even labeled it "likely timeout, not transient" yet had no way to actually stop it. This is the timeout-flavored sibling of [[shell-syntax-platform-match]] (macOS = BSD, no GNU coreutils).

**How to apply:** Triggers whenever a macOS scheduled script must bound a long `claude -p` / node / python child.
- Preferred fix: `brew install coreutils` → prefer `gtimeout` in the `command -v` ladder (`if command -v timeout … elif command -v gtimeout … else …`). (New dependency = escalate-tier per [[autonomy-policy]] — surface to user, don't auto-install in a background run.)
- If staying dependency-free: don't rely on bare `alarm+exec`. Spawn the child in its **own process group** and on timeout `kill -TERM -$pgid` (then `-KILL`), so orphaned node children die too. A sleep-watchdog backgrounded PID that `kill`s the group is more reliable than perl `alarm` for non-exec'ing wrappers.
- Verify any fix functionally (`EVO_COUNCIL_TIMEOUT=5` + a sleeper) before trusting it — per [[feedback_test_before_ship]] a timeout that "looks right" but doesn't kill is worse than none (false sense of a cap).
