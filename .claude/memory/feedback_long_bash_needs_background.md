---
name: long-bash-needs-background
description: Foreground Bash hard-caps at 10m (default 2m) — batch/scrape/audit commands estimated over ~2 min must use run_in_background:true or chunk, or they die with exit 143 mid-work
type: feedback
date_written: 2026-07-14
last_validated: 2026-08-02
---

Foreground Bash calls are killed at their timeout (default 120s, max 600000ms) with exit 143 — partial work is lost or left in unknown state. Any command expected to run more than ~2 minutes (batch API calls, scraping loops, `du` over multi-GB trees, image-gen batches) must either (a) run with `run_in_background: true` (harness re-invokes on completion, no polling needed), (b) be chunked into shorter idempotent slices, or (c) at minimum set `timeout: 600000` explicitly and confirm the job fits.

**Why:** 2026-07-13, two independent hits in one day: creator-intel influencer-audit batch ("33 selected; est cost $0.91") killed at 10m 0s mid-batch, and a `du`-style size listing over ~/Projects killed at 2m 0s. Both had a cost/size estimate available up front that predicted the overrun.

**How to apply:** Before launching a command, if its own pre-flight estimate (item count × per-item time, or tree size) suggests > 2 min, choose background or chunking up front — don't discover the cap at exit 143. For resumable batches, make each chunk idempotent so a kill loses at most one chunk.

Related: [[no-foreground-sleep-to-wait]] — background work re-invokes the model on completion; [[workflow-tool-has-no-run-in-background]] — the flag belongs to Bash/Agent only.

**Scope update (2026-07-21):** same signature from cross-vendor offload — Codex **Sol** review calls foreground-blocked and died at exit 143 twice (4m, then 5m retry) on 2026-07-20. Per `rules/offload-mode.md`, Sol must always run `--background`; Terra is the default review tier precisely because it returns in seconds.
