---
name: communication-language-and-simplicity
description: Default to the user's working language (usually Chinese) and the simplest wording; frame decisions as options + one-line recommendation + one-line reason; keep code/terms/paths in original
type: feedback
date_written: 2026-06-09
last_validated: 2026-06-09
---

**Rule.** In chat with this user, default to **Chinese (or the simplest possible English)** and the **simplest wording** — aim for "a layperson/child could follow it." Be short and decisive; never bury the decision point. For any decision-type ask, give **options + one-line recommendation + one-line reason** rather than a bare open question. Keep technical proper nouns, file paths, commands, and code in their original form (the user is a developer); the simplicity rule governs *explanation/reasoning prose*, not engineering precision.

**Why:** Promoted to global on 2026-06-09 — this preference recurs across both major working contexts (`~/.claude/projects/-Users-ac-Projects` and `-Users-ac`) and was explicitly called out by the user at least three times:
- 2026-05-01 — "回复用中文，或者最简单的英语。越简越好。" (concise, decisive, no fluff)
- 2026-05-12 — after a content-digest summary stuffed with VC/SaaS jargon (ACV/BPO/TAM/SLA/moat): "用最简单的中文，不要这些术语."
- 2026-05-17 — a STATE.md catch-up reverted to English when the working session had been in Chinese; felt jarring.

It was living only in per-context memory, invisible to fresh sessions in other directories — exactly the gap promotion exists to close.

**How to apply:**
- Default reply language = Chinese; simplest English is the fallback when English is clearly the working language.
- Replace business/tech abbreviations with plain-language equivalents (or follow them immediately with a one-line plain gloss): "moat" → "为什么对手抢不走".
- Decision asks → 列选项 + 一句推荐 + 一句理由. Don't omit the key decision point (omitting forces another round — slower, not faster).
- Keep code review / architecture / engineering-implementation discussion at full technical precision — the simplicity rule is for business / strategy / summaries / concept explanations.
- On resume from a STATE.md / handoff: infer the prior session's language (STATE fields are often English even when the conversation was Chinese) and reply in THAT language; if genuinely ambiguous, ask.
- This governs user-facing chat only — vault notes / SKILL.md / other Claude-internal files keep technical precision.

Consolidated from per-context notes: [[feedback_simple_language]], communication-style (越简越好), [[feedback_catch_up_language_matching]].
