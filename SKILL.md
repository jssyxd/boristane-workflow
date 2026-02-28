---
name: boristane-workflow
description: Boris Tane workflow + minimal 3-variant ensemble (A/B/C) + reviewer fusion for complex tasks (>4 sub-steps). Trigger by mentioning 'boris'. Auto-spawns dynamic Agent Team + temporary skills.
version: 1.0
author: Grok + User
tags: [boris, ensemble, research, planning, annotation, tdd, fusion]
---

# boristane-workflow

**适用规则**（强制执行，永远遵守）：
- 仅当用户提及 "boris" 时启动。
- 第一步必须判断子任务数量：>4 个子任务 → 复杂模式；≤4 个 → 直接回复 "simple task，是否跳过 boris？"
- 每条响应严格控制在 1200 tokens 以内，无任何多余解释。
- 强制 TDD：所有实现必须先写测试 → 通过测试 → 才算完成。
- 所有输出必须引用 research.md 中的关键事实。
- 融合时 100% 优先用户在 plan.md / fused-plan.md 中的 Annotation。
- 输出格式：每一步必须以 `## Step X: 标题` 开头，使用 Markdown 表格/代码块，结尾永远加 **等待下一步指令**。
- 只在磁盘创建以下文件：research.md、plan.md、review.md、fused-plan.md，其余全部内存。

## 完整流程（复杂模式 >4 子任务）

### Step 1: Agent Team 初始化
- 根据项目和任务，动态生成 Agent Team（数量不固定）：
  - Leader（必须有 1 个）
  - Reviewer（可 1-2 个）
  - Executor（可 1-3 个）
- Leader 立即输出团队组成表格。
- Leader 自动调用 /find-skills 为当前项目查找并临时安装合适的 skills（开发、测试、部署相关），记录在 temp-skills.log。

### Step 2: Research
- Leader 指挥一个 Executor 深入分析项目代码/目录。
- 输出 research.md（包含结构、关键逻辑、依赖、潜在风险）。
- 必须引用具体文件和行号。

### Step 3: Planning + Annotation Cycle（Leader 判断是否开启）
- Leader 输出 plan.md（完整架构、步骤拆分、风险）。
- Leader 判断是否需要 Annotation Cycle：
  - 如果需要：回复 "plan.md 已生成，请编辑并回复 address notes"
  - 如果不需要：直接进入 Step 4。
- 用户回复 “address notes” 后，Leader 根据用户注释更新 plan.md（可循环多次，直到用户满意）。

### Step 4: Todo List
- 在 plan.md 末尾生成可打钩的 Todo 列表（粒度到函数/文件）。

### Step 5: Ensemble 抽卡（仅复杂模式）
- 生成 3 个独立变体（顺序输出）：
  - ### Variant A - 极简版
  - ### Variant B - 鲁棒版
  - ### Variant C - 性能版
- 每个变体必须先写完整测试用例 → 实现代码 → 说明如何通过测试。
- 每个变体独立存于内存，不覆盖文件。

### Step 6: Review & Fusion
- Reviewer 输出 review.md：
  - 打分表格（满分 100，5 个维度各 20 分）
  - 每个 Variant 的 3 个优点 + 2 个缺点
- Leader 给出融合决策（以哪一个为基础 + 从其他融合哪些部分）。
- 生成 fused-plan.md（融合后最终计划）。

### Step 7: Fused Implement
- Executor 根据 fused-plan.md 执行实现。
- 边做边在 fused-plan.md 的 Todo 上打钩 ✅。
- 每实现完一个 Todo 就运行对应测试。
- 全部完成后运行全项目测试。

### Step 8: Cleanup & 完工
- 删除所有临时 skills（执行 /remove-temp-skills）。
- 输出最终总结表格（文件改动、测试通过率）。
- 回复 “项目已按 boristane-workflow 完成，等待下一步指令”

**结束语**：本 skill 仅输出结构化内容，绝不添加任何额外聊天。用户下一条指令决定下一步。
