---
name: Verification probes must be read-only — verify write paths on a dummy fixture
description: A probe run "just to check it doesn't error" must never touch a control that mutates live state; to verify a write path, create a throwaway fixture and act on that instead.
type: feedback
date_written: 2026-08-03
last_validated: 2026-08-03
severity: high
---

## Rule: a probe verifies **that a path works**, not **on the user's real data**.

When verifying UI/automation behavior (CDP clicks, headless-Chrome `--dump-dom`, a local `serve` instance, an API smoke test), only exercise controls that **read**. Never click / POST / invoke anything that writes, deletes, merges, or renames real records — not even to confirm "it doesn't throw".

**Why:** On 2026-08-03, a probe written purely to confirm that clicking the artifacts-library 「整理」 button did not error **actually ran the mutation on the live library and destroyed one tag**. The probe's stated intent was read-only; its mechanism was not. The failure is that "does this control error?" and "what does this control do?" are the same click — there is no observe-only mode on a write control, so intent provides zero protection. Recorded in `rules/local-artifacts.md` as a hard artifact-library constraint; this memory is the general form.

## How to apply

1. Before a probe fires, classify every control/endpoint it will touch as READ or WRITE. If you can't classify one, treat it as WRITE.
2. Probe only the READ set against live data.
3. To verify a WRITE path, first create a **dummy fixture** (throwaway slug / test record / temp copy of the tree) and run the probe against that. The assertion is the same; the blast radius is not.
4. Prefer a reversible container when a fixture isn't possible: a git worktree, a copied directory, or a snapshot taken immediately before.
5. State in the report which set you exercised. "Probe passed" without saying read-only-vs-fixture is an unverifiable claim — see [[feedback_verify_before_claiming]].

## Anti-pattern to kill

> "Clicked 整理 to confirm the panel opens without an error — it works."

Should be:

> "Opened 整理 via a dummy-slug fixture library; panel rendered, no error. Live library untouched."

## Cross-links

- [[feedback_verify_before_claiming]] — the claim side of the same discipline
- [[feedback_destructive_cleanup_compound]] — destructive-action gating
- [[feedback_test_before_ship]] — a test that mutates production is not a test
