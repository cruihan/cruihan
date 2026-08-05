---
name: deliverable-explicit-paths
description: Every deliverable mentioned in a final reply gets its explicit absolute path (folder + filename) — user must never hunt for outputs
type: feedback
date_written: 2026-07-02
last_validated: 2026-07-02
---

When a turn produces files (reports, galleries, scraped datasets, generated images), the final reply must state the explicit absolute path to each deliverable — not "saved to the project folder".

**Why:** On 2026-07-01 the user twice could not locate deliverables from completed sessions (instcre scrape gallery, positioning doc) and had to ask where they were. For a non-engineer operator juggling multiple projects, an unnamed output is functionally a lost output.

**How to apply:** Before ending any turn that wrote files the user will consume, list each artifact as `path — one-line description`. For batches, give the folder plus the entry-point file (e.g. `gallery.html`).
