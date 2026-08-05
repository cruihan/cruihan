---
name: Verify before claiming — never pattern-match unknown tools to known ones
description: When the user introduces a new tool/service/API I don't know for certain, read docs / source / run dry test before making claims about cost, behavior, or dependencies
type: feedback
date_written: 2026-04-19
last_validated: 2026-07-25
severity: high
---

## Rule: For anything new or unverified, research before asserting. Never infer from "similar" tools.

**Why:** On 2026-04-19, I told the user Graphify "costs $3–8 per medium project" and "needs ANTHROPIC_API_KEY for semantic extraction." Both claims were completely false.

- Graphify 0.4.x has **zero LLM dependencies** (no `anthropic` / `openai` / `litellm` in its deps)
- It runs on tree-sitter + Leiden/Louvain clustering — 100% deterministic, 100% free
- `graphify --help` literally prints `update <path>   re-extract code files and update the graph (no LLM needed)` — I had that information and ignored it
- I pattern-matched to LlamaIndex GraphRAG / SourceGraph Cody (which DO use LLMs) and inherited their cost profile without checking

**The real-world cost:** User bought a third-party Claude API key from a proxy service specifically because I said Graphify needed one. That money was spent solving a problem that didn't exist.

## How to apply

Before making ANY claim about a new/unfamiliar tool, service, API, library, or framework:

1. **Read `--help` / docs FIRST** — `<tool> --help`, `<tool> help`, or WebFetch the official docs
2. **Check dependencies** — for Python: `pip show <pkg>` or read `METADATA` / `pyproject.toml`. For Node: `package.json`. The dep list is the cheapest reality check.
3. **Run a dry test if cost is claimed** — `<tool> <command> --dry-run` or run on a tiny mock input with no credentials
4. **Grep the source for claimed behaviors** — if you're about to say "it calls Anthropic," grep for `anthropic\|api\.anthropic\|openai` in the source
5. **For financial-impact claims, spawn a research agent with web search** — any "costs $X" / "needs paid tier" / "requires subscription" claim needs a cited source URL

## Specific anti-pattern to kill

> "This is a knowledge-graph tool for code, so it probably uses an LLM like the similar tool X does, so it probably costs $Y."

Each of those "probably"s is a step toward making a false claim with real-user-money consequences. Every "probably" must become "I checked and here's the source" OR the claim doesn't get made.

## Specific trigger phrases that require verification

Before saying any of these, verify:

- "costs $X" / "will be around $X"
- "needs an API key"
- "uses LLM / calls Claude / calls OpenAI"
- "consumes tokens"
- "requires a paid plan"
- "needs configuration for X"
- "behaves like Y"
- **"Model X beats / is stronger than Model Y"** — added 2026-04-22

When uncertain, say "I don't know — let me check" and actually check. The user prefers honest uncertainty over false confidence.

## 2026-04-22 reinforcement: benchmark version pinning

Specific repeat-incident: claimed "Kimi K2.6 SWE-Bench Pro 58.6% beats Opus 4.6 53.4%" (true) but the user reasonably read it as "Kimi beats Opus 4.7" (false — Opus 4.7 scores 64.3% on the same benchmark). The model number jump (4.6 → 4.7) is small enough that humans gloss over it; the benchmark difference (53.4% → 64.3%) is large enough to flip the conclusion.

**New sub-rule**: Whenever quoting a benchmark to compare two models, **explicitly state the version of BOTH models** in the same sentence. "Kimi K2.6 (58.6%) vs Opus 4.7 (64.3%)" not "Kimi beats Opus." If you only have data for an older version (e.g., Opus 4.6), say so explicitly: "comparing K2.6 to Opus 4.6 (4.7 data not in my source)" — never let the user infer the latest version.

**Trigger**: Any sentence of the form "<model> [beats/wins/leads] <other model>" must include both version numbers AND the benchmark name AND the date of the benchmark, OR be downgraded to "I'm not sure — let me check current numbers."

## Remember this pattern

The user has explicitly said (2026-04-19): "when I give you something new or you haven't encountered before, please confirm first. If you don't know, check the docs, usage, and web. Don't just infer what you think is correct — it causes unnecessary resource loss. Remember this across every future conversation."

This rule exists to enforce that commitment.
