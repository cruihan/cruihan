---
name: Execute the literal ask before any adjacent exploration
description: When the user names a specific deliverable, build exactly that first — do not substitute testing/exploring adjacent cases (more companies, more styles, more sites) for the requested work
type: feedback
date_written: 2026-07-21
last_validated: 2026-07-21
---

When the user asks for a specific artifact or behaviour ("build the annotate/edit mode", "make the toolbar draggable"), deliver that exact thing first and show it working. Do not widen the task into adjacent exploration — testing another client, sampling more styles, capturing more reference sites — before the named deliverable exists.

**Why:** On 2026-07-20, prato-local-seo session `34a08f2c`: *"你依旧没有根据我的要求去执行…你怎么让我去测试"* — Claude had gone off testing additional companies/styles instead of building the requested in-page annotation workflow, leaving the user nothing to test. The same shape appeared the same day in design-extract (`d7054769`): the first grading pass optimised the rubric across ~100 sites while the actual complaint was that the captures themselves were wrong (cookie banners un-dismissed, fabric pages instead of garment wholesale pages). In both cases the extra breadth was real work that did not advance the stated ask, and the user read it as not listening.

**How to apply:**
1. Restate the deliverable in one line before starting; if it names a single concrete artifact, that artifact is the whole scope of the turn.
2. Breadth (more samples, more cases, more variants) is a *follow-up*, offered after the named thing works — not a substitute for it, and never a reason to defer it.
3. When the ask is UI/interaction work, "done" means the user can actually operate it. Ship the interaction before adding coverage around it.
4. If the ask genuinely seems wrong or under-specified, say so in one sentence and ask — do not silently redirect the effort to a different target.

Related: [[Small atomic slices by default — earn the right to batch]] — sibling scoping rule (that one caps how *much* runs per slice; this one fixes *what* the slice is aimed at). Also [[feedback_verify_before_claiming]].
