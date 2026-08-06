# Project State

_2026-08-05 首次真实填写，取代原样板（原文全是 `_(none)_` / `_Last updated: initialized_`）。下述每条均由磁盘 / git 核实。_

## Goal

**GitHub profile README 仓**（`github.com/cruihan/cruihan` —— 与用户名同名的特殊仓，其 `README.md` 显示在 GitHub 个人主页顶部）。定位：Andrew Chen，全栈工程师，为小企业做 AI 辅助系统；Vancouver，客户在欧洲与北美。

## Current Position

**已从占位内容重写为视觉化作品展示；工作区干净。**

- 磁盘只有两样：`README.md`（**154 行**，HTML 排版 + 技术栈徽章 + 客户站卡片）+ `assets/`（4 个文件，共 ~3.1 MB：三张客户站截图 `autofficina-montegrappa.webp` / `crossway-church.webp` / `pacific-christian.webp` + 一个 `theme-switch.gif` 1.5 MB）。
- git：**5 commits**，HEAD `d64aace`（2026-08-05）「Even out the first client card's caption length」；`main` 与 `origin/main` 同步；`git status --short` = **0**。
- 演进路径（commit 序）：`e25949b` 初始 → `cc91a33` 用真实 profile README 替换占位 → `f5b6c7d` 重建为视觉化展示 → `5c2ab6f` 让 Autofficina 卡片标题保持单行 → `d64aace` 拉平第一张卡片的说明文字长度。

## Open Blockers

- 无。

## Key Decisions

- **Autofficina 卡片必须说明它是未成交的 spec build。** README 已写明：该站带有一家真实商家的名称 / 地址 / 增值税号且**尚未被买下**，因此以 noindex 发布 —— 公开展示作品时不能让它读起来像已上线的客户官网（与 `client-sites/autofficina-montegrappa` 的 PREVIEW 红线是同一条）。
- 展示走**本地 `assets/` 里的静态截图**，不外链客户站实时页面（客户站可能改版 / 下架 / 换域名）。
- 卡片排版按「标题单行 + 说明文字等长」调平 —— 最近两个 commit 都在做这件事。

## Active Workstreams

- 无进行中改动。

---

_Last updated: 2026-08-05（核实命令：`git log`、`git status --short`、`ls -la assets/`、`wc -l README.md`）_
