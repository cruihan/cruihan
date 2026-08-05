---
name: scrape-autoclose
description: "Any scraping/browser-automation task MUST auto-close its browser/server when done (trap EXIT, not end-of-script) — user's repeated resource-waste concern"
type: feedback
date_written: 2026-07-17
last_validated: 2026-07-17
---

任何"打开浏览器/服务去爬取信息"的任务(CDP debug Chrome、Playwright MCP、chrome-devtools MCP、本地 review/scrape server),**跑完/失败/早退都必须自动关掉那个浏览器或 server**,绝不留在后台空转。

**Why:** 用户 2026-07-16~17 连续两次强调:反复发现 :9222 调试 Chrome、孤儿 python server 等爬完不关,一直在后台空转浪费内存/端口。这是他的明确高优先级顾虑。首记于 session 1780ab68(~/Projects 级 memory),因跨项目适用(italy-house-hunt、competitor-teardowns、batch-image-gen、site-extract)于 2026-07-17 夜间整合提升到全局。

**How to apply:**
- **脚本类爬虫**(launchd / bash 调 CDP):顶部 `trap cleanup EXIT INT TERM` 兜底关浏览器 —— 不是只在末尾关(末尾关碰到中途 `exit` 就漏)。参考实现 `~/Projects/italy-house-hunt/scripts/refresh_portals.sh`(`trap cleanup_chrome EXIT`)。
- **MCP 浏览器**(Playwright / chrome-devtools):任务结束显式关闭 browser;它们是 session 级的,不关就挂到会话结束。
- 调试 Chrome 用**独有 profile 路径**精确 `pkill -f "user-data-dir=<profile>"`,别误杀别的 Chrome/用户默认 Chrome。
- 完事顺手 `lsof -nP -iTCP:<port> -sTCP:LISTEN` 复查端口已释放。

这是 `rules/resource-cleanup.md` 的具体强化:该规则已存在,但用户实测到它没被严格执行 → 每次爬取都当作 ship-gate 检查一遍,而非事后想起。Related: [[feedback_test_before_ship]].
