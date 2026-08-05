---
name: no-dead-infra
description: "Extract-only or defer when there's no clean live caller/consumer; don't wire speculative infra"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 9034ff2a-bc54-44aa-a4ac-69e5b4834209
date_written: 2026-07-20
last_validated: 2026-07-21
---

Don't wire a mechanism into a system that has no clean place for it, and don't build infra with no live consumer.

**Why:** cargo-culted wiring + speculative infrastructure = pure maintenance surface with zero payoff. A util nobody calls and a queue nothing reads are dead weight that still has to be understood and maintained.

**How to apply:**
- OpenClaw `handoff_briefing` was extracted as a ready util but **NOT force-wired** into tokenmaxx — tokenmaxx's failover is a subprocess runner chain (claude→codex) with no orchestrator/subagents to brief, so the "you are new LEADER, here are active subagents" template didn't fit. Extract-only; wire at the first real orchestrator-style failover.
- A3 "Sheets job-queue" was **DEFERRED** — flow_batch's local file queue already covers batching and no cloud consumer exists yet.
- Contrast (the positive case): the `transient_error` classifier DID have a clean fit (tokenmaxx runner failover → retry-later) → wired + integration-tested + kept.

Rule: wire/build only when a real caller/consumer exists. Links [[feedback_workflow_arg_injection_assert]]. Source: nate-research arc 2026-07-02.
