---
name: pnpm is the package manager — never npm, never mixed
description: Every JS/TS project on this machine uses pnpm; do not run `npm install`/`npx` for project deps, and when auditing disk usage flag npm-managed node_modules as a migration target rather than proposing deletion
type: feedback
date_written: 2026-08-04
last_validated: 2026-08-04
severity: medium
---

## Rule: Use `pnpm` for every project. Do not introduce or leave `npm`/`yarn` in any project on this machine.

**Why:** The user stated this twice in one session on 2026-08-03 (`-Users-ac-Projects`), both times unprompted and both times framed as a disk-space argument, not a style preference: 「我希望我整体上所有的这些 project，它们都能是用 PNPM，而不是一个用 C，一个用 N。我觉得要统一」 and 「如果用 PNPM 的话，我们很多 package 能节省很多地方」. pnpm's content-addressed store dedupes packages across every project; a mix of npm and pnpm defeats that entirely, so the inconsistency itself is the cost. `rules/principles.md` already lists **pnpm** as required per-project tooling, but as a setup ingredient — nothing said "never npm", which is why npm kept reappearing.

**How to apply:**
- Installing/adding/removing deps → `pnpm install` / `pnpm add` / `pnpm remove`. One-off binaries → `pnpm dlx`, not `npx`.
- Running scripts → `pnpm <script>` / `pnpm run <script>`.
- Global CLI installs are the one exception and stay escalate-tier anyway ([[autonomy-policy]] flags `npm install -g` as a new external dependency).
- On encountering a project with `package-lock.json` or `yarn.lock`: surface it as a migration candidate (`rm lockfile && pnpm import` then `pnpm install`) — do **not** silently migrate, and do **not** propose deleting its `node_modules` as "cleanup" ([[deletion-proposals-need-inventory]]).
- Never mix managers inside one repo — a stray `npm install` rewrites the lockfile and undoes the store dedupe.
