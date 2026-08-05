---
name: No fake metrics — measure, don't estimate
description: When a measurement can be obtained by actually running the audit/script, run it. Never compute/estimate and present as measured.
type: feedback
date_written: 2026-04-19
last_validated: 2026-07-25
severity: high
---

## Rule: If it can be measured, measure it. Never present estimates as measurements.

**Why:** On 2026-04-19, after editing 15 skill descriptions to fix trigger-rate issues, I presented "post-audit rescore" numbers:

> "Avg score: 6.44 → 7.78 (+1.34), all 15 now PASS"

These numbers were **computed in my head from the rubric**, not obtained by actually re-running `audit-skill-triggers.md`. I labeled them as "rescore" — implying measurement — when they were estimates.

The user is using these numbers as the baseline for future drift detection. If the estimates were slightly off, every future comparison inherits the drift silently — and the whole re-auditability contract (`rules/skill-optimization.md`) is violated at its core.

## How to apply

Before presenting any quantitative claim:

1. Ask: "can I actually run this?" — if yes, run it
2. If I'm computing from a rubric in my head: label clearly as `estimate` / `computed`, NEVER as `measured` / `rescore` / `actual`
3. For audits specifically: always execute the real audit prompt, don't simulate it through reasoning
4. If real execution is genuinely too expensive: offer to run it, explain why I didn't, and label the number as an estimate

## Anti-pattern to kill

> "After the edits, the new avg score is 7.78 (+1.34)."

Should be one of:
- "I re-ran `audit-skill-triggers.md`; new avg is 7.78." (real)
- "Based on the rubric, my estimate of the new avg is ~7.8 — rerun the audit for the real number." (honest)

## Why this specifically matters for this system

The re-auditability architecture relies on dated JSON reports with sha256 provenance being trustworthy baselines. An estimate stored as a "measurement" = forever-compromised baseline. Every future diff comparison will be wrong.

## Applies beyond audits

- Performance claims ("this is 3x faster") — run the benchmark
- Cost claims ("this costs $X") — already covered in `feedback_verify_before_claiming.md`
- Behavior claims ("this flag does Y") — check --help / source, don't infer
