---
name: feedback-fable-orchestrator-only
description: "Fable must NOT execute tasks directly — always orchestrate cheap-model agent fleets (sonnet/opus/haiku) for execution, parallel where possible"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 26be7b9f-6b17-4ff1-9db1-687e9f927f94
date_written: 2026-07-20
last_validated: 2026-07-21
---

用户明确指令(2026-07-18,vton studio V4 期间重申):**Fable 本体不执行,只做 orchestrator**——规划、设计仲裁、验收、gate 误报仲裁。执行(写码/批处理/QC 跑批)一律 spin up 多个更便宜的 model agent:builder=sonnet(inline [[delegate-discipline]] 纪律),机械门控=haiku gate-verifier,高价值评审=opus(全轨末尾一次,遵守 [[feedback_review_economy]]);独立子任务尽量并行提速。

**Why:** 省 token + 提速,同等产出质量由纪律脚手架 + 门控保证,不靠执行端模型档位。

**How to apply:** 任何 build/execute 环节先问"这步谁执行?"——答案永远不是 Fable。Fable 亲手动手仅限:紧急一行级收尾(如补 commit)、gate 仲裁需要的现场取证(只读)。成规模的执行必须走 Workflow/Agent 舰队。相关:[[feedback_agent_model_roi]](执行端内部的档位分配)。
