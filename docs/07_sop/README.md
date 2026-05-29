# AI-Native Coding SOP 工作台

> 本目录把 `docs/00_ai_system/ai-coding-guide.md` 的工程编排思想落成可执行流程。目标是让单人、多人团队、初级研发、资深研发、产品、测试、前端、后端、Java、Vue、React、TypeScript、Go 等角色都能找到自己的入口。

---

## 30 秒选择入口

| 你的场景 | 先读 | 再读 |
|---------|------|------|
| 一个人做简单工具、Demo、内部系统 | [solo.md](solo.md) | [project-types/simple-project.md](project-types/simple-project.md) |
| 一个人做复杂产品或全栈项目 | [solo.md](solo.md) | [project-types/complex-product.md](project-types/complex-product.md) |
| 多人协作，有产品/研发/测试/运维分工 | [team.md](team.md) | [roles/README.md](roles/README.md) |
| 不知道从哪开始 | [lifecycle.md](lifecycle.md) | [templates/task-card.md](templates/task-card.md) |
| 已有 PRD，要进入研发 | [lifecycle.md](lifecycle.md) 的阶段 2-4 | [templates/task-card.md](templates/task-card.md) |
| 已拿到一个具体任务，不知道怎么和 AI 协作 | [task-driven-collaboration.md](task-driven-collaboration.md) | [templates/ai-session-prompt.md](templates/ai-session-prompt.md) |
| 接口联调混乱 | [lifecycle.md](lifecycle.md) 的阶段 3 | [templates/contract-change-proposal.md](templates/contract-change-proposal.md) |
| 提 PR 前 | [templates/review-checklist.md](templates/review-checklist.md) | [../04_qa/test_cases.md](../04_qa/test_cases.md) |

---

## 任务驱动协作流

拿到任务时，先执行这条链：

```text
任务描述
  ↓
套用 ai-session-prompt
  ↓
AI 分类任务并选择 skill chain
  ↓
填 task-card
  ↓
按 skill chain 执行
  ↓
验证、review、修复
  ↓
更新 memory
```

入口：

- [task-driven-collaboration.md](task-driven-collaboration.md)：按任务类型选择 skills、输入模板和下一步动作。
- [templates/ai-session-prompt.md](templates/ai-session-prompt.md)：启动 Claude Code / Codex 的标准输入。
- [templates/task-card.md](templates/task-card.md)：把任务变成可执行 AI 工作单元。
- [templates/skill-chain-plan.md](templates/skill-chain-plan.md)：定义每一步 skill 的输入、输出和进入下一步条件。

---

## 总流程

```text
0. 工程底座
  ↓
1. 需求澄清
  ↓
2. 架构边界
  ↓
3. 契约优先
  ↓
4. 垂直切片
  ↓
5. 小批量功能 PR
  ↓
6. 多维 Review 与硬化
  ↓
7. 发布、复盘、规则沉淀
```

核心判断：

- AI 不直接“写完整系统”，AI 执行被约束、可验证、可审查的软件工程单元。
- 人负责目标、边界、架构取舍、风险接受和最终合并。
- AI 负责探索、拆解、测试、实现、修复、文档和检查命令。
- 每个任务必须有任务卡、文件范围、禁止事项、验收标准和验证命令。

---

## 目录说明

| 文件 | 用途 |
|------|------|
| [lifecycle.md](lifecycle.md) | 0-7 阶段总 SOP，所有角色共用 |
| [task-driven-collaboration.md](task-driven-collaboration.md) | 任务到 skill chain 到验证交接的完整协作剧本 |
| [solo.md](solo.md) | 单人使用流程，适合个人开发和小团队一人多职 |
| [team.md](team.md) | 多人协作流程，适合产研测运分工 |
| [project-types/simple-project.md](project-types/simple-project.md) | 简单项目轻量流程，避免文档过载 |
| [project-types/complex-product.md](project-types/complex-product.md) | 复杂产品完整流程，适合长期演进 |
| [roles/README.md](roles/README.md) | 按岗位、语言栈、资深度选择操作手册 |
| [templates/](templates/README.md) | 任务卡、需求规格、接口变更、Review 模板 |

---

## 和现有目录的关系

| 目录 | 定位 | 什么时候改 |
|------|------|------------|
| `docs/` | ROM，长期知识和团队约定 | 需求定稿、架构通过、接口确认、发布复盘 |
| `memory/` | RAM，当前任务和断点 | 每次接手、卡住、阶段完成、下班交接 |
| `.claude/rules/` | AI 行为底线 | 脚手架升级或团队共识变化 |
| `docs/06_handbooks/` | 角色手册 | 岗位职责或技能入口变化 |
| `docs/07_sop/` | SOP 操作台 | 流程、模板、项目规模分流变化 |

---

## 最小执行约束

任何 AI coding 任务，无论大小，都必须满足：

1. 有明确目标。
2. 有上下文文件。
3. 有允许修改的文件范围。
4. 有禁止事项。
5. 有验收标准。
6. 有验证命令。
7. 完成后更新 `memory/active-task.md` 或 `memory/handoff.md`。

任务描述统一使用 [任务卡模板](templates/task-card.md)。新会话启动统一使用 [AI 会话启动 Prompt](templates/ai-session-prompt.md)。
