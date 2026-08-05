---
name: Decision-asking discipline (plain language + why + why-now + industry standard + recommendation)
description: When asking the user to decide ANYTHING non-trivial, frame the ask with five facets — plain-language summary, why it matters, why now, what industry does, and a concrete recommendation with reasoning. Never throw bare open questions.
type: feedback
date_written: 2026-04-27
last_validated: 2026-04-28
---

> ⚠️ STALE — not validated since 2026-04-28. Verify before relying on this.

## Rule: every non-trivial ask uses the 5-facet frame

When proposing options to the user OR asking them to make a non-trivial decision, structure the ask as:

1. **Plain-language summary** — what decision they actually need to make, in everyday terms. Strip the technical jargon. One sentence the user can repeat back without re-reading the rest.
2. **Why this matters** — the constraint or risk being addressed. The user should understand the cost of getting this wrong (data leak, slow page, ugly UX, etc.).
3. **Why now** — justify the TIMING. Is this gating a downstream task? Is there a deadline? Is the cost of deferring growing? If "now" is just convenience, say so — don't fake urgency.
4. **What industry / common practice does** — concrete examples (Linear, Slack, Notion, Figma, GitHub, Sentry, Stripe, etc.) of how peer teams handle this scenario. Cite specific products with the choice they made + outcome when known.
5. **My recommendation + reasoning + trade-off** — a concrete pick with the trade-off surfaced. Don't hedge to a 50/50 split — the user wants a default they can accept or override.

Never just throw an open question. Many times the user doesn't know either — making them pick blind is hostile UX, especially for technical decisions in domains they're not deep in.

**Why this rule:**

- User stated (2026-04-27): open questions without context are difficult to answer because they often don't know either. Framing with industry standards + recommendation lets them choose informedly.
- User restated + expanded (2026-04-28): the rule was being applied inconsistently — sometimes asks lacked the "simple language" facet (jargon-heavy questions) or the "why now" facet (timing left implicit). Both reduce the user's ability to engage. The 5-facet structure forces explicit coverage of every dimension a non-expert needs to make an informed call.
- Saves the user's cognitive load. Surfaces blind spots they'd otherwise miss. Keeps the collaboration high-leverage instead of overwhelming.

**How to apply:**

- Architecture choices → cite real apps that made each option + the trade-off they accepted.
- UX patterns → cite Material Design / Apple HIG / specific apps + the user-research rationale.
- Tooling picks (test framework, ORM, monitoring) → cite the 2024-2026 industry consensus + the criteria that drive it.
- Scope decisions (do X now vs defer) → make the deferral cost explicit ("if we defer, we accept these consequences").
- Anything where the user genuinely doesn't have peer context → research/recall industry practice BEFORE asking. Don't ask cold.
- When you genuinely don't know the industry standard → say so explicitly ("I don't have current data on what peer teams do here; my recommendation is based on first-principles reasoning") rather than fake confidence.

**The framing in concrete shape:**

```
**A. <Plain-language summary of the decision>** (1 sentence, no jargon)
Why it matters: <constraint / risk / cost>
Why now: <timing rationale, or honest "no urgency"
Industry: <peer products + their choice + outcome>
My recommendation: <concrete pick> — <reasoning + trade-off>
```

Apply this even for "small" decisions if the user is investing real time in the answer. The bar for the 5-facet frame is "would the user benefit from understanding industry context before answering?" — usually YES.

## Trivially small asks (the exception)

For decisions that are genuinely binary + obvious + low-stakes ("OK to overwrite this temp file?", "Re-run the build?"), skip the frame and just ask. Don't fetishize the structure for trivia. Use judgment — if you'd answer it instantly yourself, don't apply the full 5-facet frame.

## When this rule fires

- About to ask the user any decision that affects architecture, scope, design, security, or product direction → use the 5-facet frame.
- Reviewing a draft response and noticing a bare open question → rewrite with the frame.
- User says "I don't know, what do you think?" → that's the rule prompting itself; the original ask was missing facets. Re-do the ask with the frame.
- Multiple decisions in one ask → frame each individually rather than batching with shared context.

## Promotion history

- 2026-04-27: created in `~/.claude/projects/c--Users-AC-Projects/memory/` (warehouse-os auto-memory).
- 2026-04-28: promoted to GLOBAL memory (`~/.claude/memory/`) since the rule explicitly applies to all projects, not just warehouse-os. Project copy retained as snapshot but global is canonical going forward. New facets added: plain-language summary, why-now timing rationale.