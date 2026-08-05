---
name: pin-mcp-server-versions
description: Always version-pin uvx/npx-launched MCP servers in project .mcp.json — unpinned = supply-chain RCE on repo open
type: feedback
date_written: 2026-07-02
last_validated: 2026-07-02
---

Every uvx/npx-launched MCP server in a project `.mcp.json` must be version-pinned (e.g. `uvx --from code-review-graph==2.3.5 code-review-graph serve`), never a bare unpinned package name.

**Why:** Opening a repo auto-starts its MCP servers; an unpinned PyPI/npm package name means "execute whatever the registry serves today" — a supply-chain RCE vector. Flagged by the security-review ship gate on 2026-07-01 in ecom-calc's `.mcp.json`; the finding survived adversarial re-review, and the pinned fix re-reviewed clean the same hour.

**How to apply:** When writing or reviewing any `.mcp.json` (including `setup-project-graph.sh` output), check every `uvx`/`npx` command for a version pin. If missing, add one (escalate — `.mcp.json` edits are escalate-tier per autonomy-policy, so surface it rather than auto-fix).
