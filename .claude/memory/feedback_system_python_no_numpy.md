---
name: system-python-has-no-numpy
description: Homebrew system python3 (3.14) has NO numpy — pixel-math one-liners must use PIL-only math, check the import first, or run inside a project venv
type: feedback
date_written: 2026-07-04
last_validated: 2026-07-25
---

`python3` on this Mac (Homebrew 3.14) does not have numpy installed. Ad-hoc image/pixel-measurement one-liners that `import numpy as np` die with `ModuleNotFoundError`.

**Why:** Hit 2× on 2026-07-03 in separate sessions (803d1638, dca58e3d), both image-QC pixel-diff snippets for the vton/clay pipelines. Each cost a failed run + rewrite. Re-validated 2026-07-06 (same 803d1638 pipeline): first `python3 -c "import numpy"` matte snippet died, then work correctly switched to the project's `.venv-matte/bin/python` — the "How to apply" fallback worked as intended.

**How to apply:** Before writing a numpy-dependent snippet: (1) prefer pure-PIL math (`ImageChops.difference`, `Image.getdata`, histogram) for simple diffs/means; (2) if numpy is genuinely needed, run inside the project venv that has it (e.g. the project's `backend` venv) or `python3 -c "import numpy"` first and fall back; (3) do not `pip install` into system python (escalate-tier dependency per autonomy-policy). PIL itself IS available system-wide. Related: [[feedback_python_heredoc_literals]] (same ad-hoc-python-snippet family).
