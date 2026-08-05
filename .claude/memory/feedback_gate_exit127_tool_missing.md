---
name: gate-exit127-tool-missing
description: In a deterministic verify gate, exit 127 / "no such file or directory: node_modules/.bin/<tool>" means the TOOL is missing, not that the code failed — never report it as RED or let it trigger a tier escalation
type: feedback
date_written: 2026-07-20
last_validated: 2026-07-20
---

A verify gate must classify a non-zero exit before acting on it. `127` (and zsh's `no such file or directory: node_modules/.bin/eslint`) means the checker was never installed/resolved — the gate did not run, so it produced **no signal at all**. Report it as `SKIPPED (tool unavailable)`, not `FAIL`.

**Why:** On 2026-07-19 (`935edcea`, cockpit-app) a gate run reported `tsc exit=1` alongside `eslint exit=127` after `node_modules/.bin/eslint` was absent — the 127 carries zero information about the code, but a gate that lumps "non-zero = RED" would (a) fabricate a lint failure that no source line causes, and (b) under the cheap-first cascade in [[architecture]] falsely escalate the slice to a higher tier, burning the expensive model on a phantom finding. That inverts the whole point of the $0 gate. Same failure class as [[feedback_no_fake_metrics]]: a number reported without checking whether the measurement happened.

**How to apply:** Triggers in `gate-verifier`, `qa`, `verify-gate.sh`, and any script whose exit code feeds a GREEN/RED decision.
- Probe first: `[ -x node_modules/.bin/eslint ] || command -v <tool>` → if absent, emit `SKIPPED` + the reason, and say so in the report rather than silently dropping the dimension (per [[feedback_verify_before_claiming]], a skipped check is never a pass).
- Treat `127` (command not found) and `126` (not executable) as infrastructure, distinct from `1` (real finding).
- If every gate dimension SKIPs, the node is **ungated** — fall back to the static-effort path in [[claude-code-tuning]], don't claim GREEN.
- Related platform sibling: [[shell-syntax-platform-match]] / [[perl-alarm-timeout-unenforced]] (macOS lacks `timeout`, `pip`; the same "tool isn't there" class).
