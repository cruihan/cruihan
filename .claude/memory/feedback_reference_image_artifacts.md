---
name: reference-image-artifacts-propagate
description: AI image gen (NBP/Gemini) faithfully copies defects from reference images — artifact-check every synthetic swatch/anchor before feeding it in
type: feedback
date_written: 2026-06-12
last_validated: 2026-06-12
---

Any reference image fed to an image-generation model (Nano Banana Pro / Gemini) must be artifact-checked first — the model reproduces reference defects faithfully, it does not "clean them up."

**Why:** On 2026-06-11 (phase0-vton-test), a synthetic background swatch had concentric banding rings from float-gradient rounding. It was judged "harmless" without inspection; NBP copied the rings into every generated background, costing a regeneration round. Same session: background tones were verified by objective RGB/luminance measurement (lum + B−R cold-cast), which precisely confirmed the user's eyeball complaints — measurement, not impressions, is the verification path for image quality.

**How to apply:** Before using any programmatically-generated swatch/anchor/reference in batch-image-gen: (1) open and inspect it at full size, (2) check gradients for banding (use ≥16-bit intermediates or add dither), (3) after generation, verify output properties (background lum, color cast) with a pixel-measurement script rather than visual impression.
