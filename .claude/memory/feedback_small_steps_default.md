---
name: Small atomic slices by default — earn the right to batch
description: For multi-step projects, propose small reviewable slices unless user explicitly authorizes batching
type: feedback
date_written: 2026-04-19
last_validated: 2026-07-25
severity: medium
---

## Rule: Default to small atomic slices with review between each. Only batch with user's explicit OK.

**Why:** On 2026-04-19, I proposed a 13-slice project and was about to execute most of it in parallel batches. The user pushed back:

> "我们可以一个一个全部都执行 … 但是麻烦你就是采取小的步骤吧，小一些。呃，就是不用一口气全都跑完，这是我主要的要求。"

Translation: "We can execute one by one but please take small steps, smaller. Don't run all at once, that's my main requirement."

The risk of large batches is blast radius: if one step is wrong, rolling back is expensive. Small slices = small failures = small rollbacks + user can catch drift early.

## How to apply

For any project proposed with >2 steps:

1. Propose the FULL plan upfront (so user sees the arc)
2. Default execution mode: **one slice per turn**, with self-review + "continue?" at each end
3. Explicitly ASK before batching multiple slices in one turn
4. Exception: truly independent AND reversible slices MAY be batched if the user has established a "go" pattern for similar work (cite the precedent)

## Anti-patterns

- "I'll now execute slices 1–7 in parallel to save time" — NO (even if independent)
- "I batched these because they seemed related" — NO (ask first)
- "I'll propose a plan but also start executing slice 1" — NO (plan, THEN get approval, THEN execute)

## Good patterns

- "Slice N done. Self-review: [findings]. Continue to slice N+1?"
- "These 3 slices are truly independent — shall I batch them?"
- "You said 'go' for the previous batch of similar work, so I'll batch these 3 also"

## Why this specifically matters

Each slice is a review checkpoint. Skipping checkpoints means the user can't catch drift until it's compounded. And for this specific user, they've been explicit that reviewing each step is how they want to work — ignoring that is ignoring user preference.

## Related
- `rules/evolution.md` — "change only on repeated pattern, not single incident" (opposite principle for system changes, same small-step ethos)
- ML training intuition: smaller learning rates + more feedback = more stable convergence
