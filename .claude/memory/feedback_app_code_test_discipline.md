---
name: Application code test discipline (Vitest + Playwright)
description: Every slice that ships application code must include automated tests for the new behavior. Santa-method dual-review is not a substitute. Stack defaults Vitest + Playwright + Neon branching for Next.js + Prisma projects.
type: feedback
date_written: 2026-04-28
last_validated: 2026-04-28
---

> ⚠️ STALE — not validated since 2026-04-28. Verify before relying on this.

## Rule: every application-code slice ships with tests

Every slice that adds or changes application code (server actions, React components, route handlers, pages, lib functions) MUST include automated tests for the new behavior before the slice is considered done.

Distinct from `feedback_test_before_ship.md` — that one covers automation scripts/hooks/config (bash, JSON). This one covers application code (TypeScript shipped to users).

**Why:** A user shipped warehouse-os for ~3 months with `tsc --noEmit` as the sole automated gate. Project memory's `feedback_verification_gap.md` flagged this gap when noticed but it was never closed — santa-method dual-review (qa + code-reviewer) was used as a partial substitute, but santa catches different bug classes than tests. On 2026-04-28, with multi-tenant RLS about to ship security-critical code, the user explicitly requested the test rule be enforced going forward. Closing this gap pre-pilot is non-negotiable.

**How to apply:**

### 1. Phasing — don't backfill 100%, do test new code + boundary tests

- **DO NOT** pause shipping to retrofit test coverage on existing code (low ROI; existing code is already hand-validated).
- **DO** add tests for every NEW slice's behavior — make it part of the slice's done-definition.
- **DO** retroactively add boundary tests for security-critical existing code (auth, permissions, multi-tenant isolation, billing/payment, anything an attacker could exploit) — these are non-negotiable for shipping to a paying customer.

### 2. Stack defaults for Next.js + Prisma + Postgres projects

- **Vitest** — unit + integration tests. Native ESM/TS, ~10× faster Jest startup. The 2024-2026 default for Vite/Next-shaped projects.
- **Playwright** — E2E + visual regression + accessibility (axe-core integration) + network mocking. Microsoft maintains it; it has clearly won vs Puppeteer (2024-2026). Built-in test runner, codegen, trace viewer, multi-browser.
- **Neon branching** for ephemeral test DB — only available on Neon, but it's the killer feature: each CI run gets a fresh branch in ~1s. No Docker needed. For other DB hosts: testcontainers or a fixed Docker Postgres.
- **GitHub Actions** — Vitest on every push, Playwright on every PR.
- **Lighthouse CI** for perf budgets — gates PRs that regress Core Web Vitals.

### 3. Santa-method is NOT a substitute for tests

Santa (code-reviewer + qa in parallel) catches design / correctness issues. Tests catch behavioral regressions over time. They cover different bug classes. For security-critical slices BOTH are required. Either-only is a gap. Don't conflate them.

### 4. AI leverage — codify it

Generating Vitest/Playwright tests from English flow descriptions is the highest-ROI use of Claude for testing.

The standard workflow:
1. Human describes the flow in natural language ("test that worker A from org X cannot see org Y's bundles via any path")
2. Claude writes the test file
3. Human runs locally + reviews assertions
4. Test goes into CI

Cheaper than human-only test writing. More thorough than human-alone. Codify this as the team practice — don't write tests by hand when an LLM can.

### 5. Coverage targets are an anti-pattern

DO NOT set a 90%/100% coverage goal. Industry consensus (Kent C. Dodds testing-trophy 2024 update, Martin Fowler "TestPyramid" current, Google Testing Blog "Code Coverage Best Practices" 2025): "test for confidence, not coverage." Goodhart's law: a coverage target makes engineers write tests-for-coverage that don't catch bugs. The "test new code + boundary tests" rule above gives confidence without the metric.

### 6. Speed budget

Test suite must run in **<60s** locally for the dev loop, **<5min** in CI. Slow suites get skipped — defeating the rule. Vitest is fast by default. Playwright runs in parallel via worker-mode. If the suite slows past these thresholds, fix the suite (parallelize, isolate, mock external services) — don't relax the rule.

### 7. The "what about commercial AI testing platforms?" question

testRigor / Functionize / MABL / Anthropic Computer Use for autonomous browser testing — defer until post-pilot unless an explicit signal demands them. Cost ($300-2000/mo or $0.10-0.50 per AI action), maturity (still 2025-rough), and vendor lock-in all argue against picking one early. Playwright + smart targeted tests + visual regression covers 95% of value.

## When this rule fires

- About to commit a slice that adds/changes application code → did the slice add tests? If no, NOT DONE.
- About to ship to a real customer → are auth/permissions/multi-tenant covered by E2E tests? If no, blocking.
- Asked "how should I add testing to project X" → recommend this stack first; only deviate if the project shape genuinely doesn't fit (not Next.js, not Postgres, etc.).
- Adding a new server action / page / component → think about its test alongside its implementation, not after.

## Relationship to other rules

- **`feedback_test_before_ship.md`** — sibling rule for automation scripts/hooks/config. Both are required; they cover different code types.
- **`feedback_verify_before_claiming.md`** — applies to claims about the testing stack itself (e.g., don't claim Vitest behavior without verifying against current docs).
- **`rules/agents.md` Verified Path** — santa pair (code-reviewer + qa) is part of the slice flow but is ADDITIVE to writing tests, not a replacement.