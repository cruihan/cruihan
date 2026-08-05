---
name: feedback_auth_outage_not_script_failure
description: "A circuit breaker over a headless `claude -p` call must read STDOUT too — entitlement/auth/quota rejections print there, and counting them as failures disables a working phase"
metadata:
  node_type: memory
  type: feedback
date_written: 2026-08-02
last_validated: 2026-08-02
---

When a scheduled script wraps `claude -p` behind a consecutive-failure circuit breaker,
classify the failure before advancing the counter — and read **stdout as well as stderr**.
The CLI prints entitlement rejections to stdout:
`Your organization has disabled Claude subscription access for Claude Code · Use an
Anthropic API key instead`.

**Why:** night-consolidation's breaker exempted transient *network* errors but grepped only
`$LLM_ERR`, so the 2026-08-01 subscription-access outage was recorded as a real failure
(`failure_count=2`, one short of tripping). A tripped breaker needs a manual `rm` to reset,
so a 3-night auth blip would have silently disabled the LLM phase long after access returned.
Same class as network outage: outside the script, self-healing, harmless either way.

**How to apply:** any breaker/retry wrapper around a CLI that can be rejected by
auth/quota/entitlement — capture both streams, and exempt auth + quota + network from the
counter. Reserve the counter for failures the script itself can cause. Fixed 2026-08-02 in
`automation/scheduled/night-consolidation.sh` (stdout captured to `$LLM_OUT`, tail logged,
grep widened to both files).

Related: [[feedback_gate_exit127_tool_missing]] · [[feedback_no_fake_metrics]] ·
[[feedback_verify_before_claiming]]
