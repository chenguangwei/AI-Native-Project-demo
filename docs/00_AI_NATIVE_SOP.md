# AI-Native Coding 总纲 SOP

> 本文档回答“整个 SOP 流程是什么”。具体执行手册在 [07_sop/](07_sop/README.md)，岗位手册在 [06_handbooks/](06_handbooks/README.md)。

---

## 核心结论

AI-native coding 不是让 AI 一句话生成完整系统，而是把 AI 编排成受约束、可验证、可审查的软件工程执行层。

人的职责：

- 定目标、边界、优先级。
- 做架构取舍和风险接受。
- 确认契约、发布、合并。

AI 的职责：

- 探索代码库。
- 澄清需求。
- 拆任务。
- 写测试和实现。
- 跑验证。
- 修复反馈。
- 更新文档和记忆。

一句话流程：

```text
需求 → 架构 → 契约 → 垂直切片 → 小 PR → Review → 发布 → 复盘沉淀
```

---

## 三层入口

| 层级 | 文件 | 用途 |
|------|------|------|
| 总纲 | 本文档 | 统一心智和总流程 |
| SOP 工作台 | [07_sop/README.md](07_sop/README.md) | 单人、多人、简单项目、复杂项目的具体流程 |
| 角色手册 | [06_handbooks/README.md](06_handbooks/README.md) | 产品、前端、后端、测试、运维、AI 工程师操作手册 |

新成员只需要按顺序读：

1. 本文档。
2. [07_sop/README.md](07_sop/README.md) 选择场景。
3. [06_handbooks/README.md](06_handbooks/README.md) 选择岗位。
4. 使用 [任务卡模板](07_sop/templates/task-card.md) 开始工作。

---

## ROM 与 RAM

| 区域 | 定位 | 内容 | 修改时机 |
|------|------|------|----------|
| `docs/` | ROM，长期知识和团队契约 | PRD、架构、API、DB、测试策略、运维、手册 | 需求定稿、架构确认、契约变更、发布复盘 |
| `memory/` | RAM，当前上下文和断点 | 当前任务、交接、项目踩坑事实 | 接手任务、卡住、阶段完成、下班交接 |
| `.claude/rules/` | AI 行为底线 | TDD、反 AI 味、安全、记忆协议 | 脚手架升级或团队共识变化 |

强制规则：

- 长期稳定约定写入 `docs/`。
- 当前正在做什么写入 `memory/active-task.md`。
- 当前卡点和下一步写入 `memory/handoff.md`。
- 反复踩坑的事实写入 `memory/project-facts.md`。

---

## 0-7 阶段总流程

| 阶段 | 目标 | 关键产物 | Gate |
|------|------|----------|------|
| 0. 工程底座 | AI 能理解项目并运行验证 | `AGENTS.md`、`CLAUDE.md`、测试命令、CI、目录规则 | 能安装、构建、测试 |
| 1. 需求澄清 | 把口头需求变成可验收规格 | PRD、用户故事、AC、不做范围 | 每条需求可测试 |
| 2. 架构边界 | 确认模块、数据、权限和风险边界 | 系统图、模块边界、ADR | 人类确认架构 |
| 3. 契约优先 | 让前后端、多服务、多语言并行不互踩 | API、DB、事件、错误码、mock、contract test | 契约被确认 |
| 4. 垂直切片 | 先打通一个真实端到端闭环 | 一个页面/API/DB/E2E 或集成测试 | 切片可运行 |
| 5. 小批量功能 PR | 每个任务卡一个小 PR | 功能、测试、文档更新 | CI + Review |
| 6. Review 与硬化 | 补安全、性能、异常、可观测性 | 审查记录、测试报告、runbook | staging 验证 |
| 7. 发布与复盘 | 发布、回滚、经验沉淀 | changelog、postmortem、project-facts | 反馈闭环 |

完整执行细节见 [07_sop/lifecycle.md](07_sop/lifecycle.md)。

---

## 按场景选择流程

| 场景 | 使用 |
|------|------|
| 一个人做简单工具、Demo、内部小系统 | [07_sop/solo.md](07_sop/solo.md) + [07_sop/project-types/simple-project.md](07_sop/project-types/simple-project.md) |
| 一个人做复杂全栈产品 | [07_sop/solo.md](07_sop/solo.md) + [07_sop/project-types/complex-product.md](07_sop/project-types/complex-product.md) |
| 多人协作，有产研测运分工 | [07_sop/team.md](07_sop/team.md) + [07_sop/roles/README.md](07_sop/roles/README.md) |
| 初级研发接任务 | [07_sop/templates/task-card.md](07_sop/templates/task-card.md) + 对应岗位手册 |
| 资深研发做架构/审查 | [07_sop/lifecycle.md](07_sop/lifecycle.md) + [07_sop/templates/review-checklist.md](07_sop/templates/review-checklist.md) |
| 前后端接口协作混乱 | [07_sop/templates/contract-change-proposal.md](07_sop/templates/contract-change-proposal.md) |

---

## 角色体系

本脚手架支持两套并行体系。

AI-Native 体系：

| 角色 | 解决的问题 | 手册 |
|------|------------|------|
| 产品负责人 | 做什么、为什么做、如何验收 | [product-owner.md](06_handbooks/ai-native/product-owner.md) |
| 交付工程师 | 端到端交付功能，不按语言栈切割 | [delivery-engineer.md](06_handbooks/ai-native/delivery-engineer.md) |
| 质量工程师 | 测试、安全、可靠性、可观测性 | [quality-engineer.md](06_handbooks/ai-native/quality-engineer.md) |
| AI 工程师 | Agent、LLM、AI 流水线 | [ai-engineer.md](06_handbooks/ai-native/ai-engineer.md) |

传统过渡体系：

| 岗位 | 手册 |
|------|------|
| 产品经理 | [pm.md](06_handbooks/traditional/pm.md) |
| 前端工程师 | [frontend.md](06_handbooks/traditional/frontend.md) |
| 后端工程师 | [backend.md](06_handbooks/traditional/backend.md) |
| 测试工程师 | [qa.md](06_handbooks/traditional/qa.md) |
| 运维工程师 | [devops.md](06_handbooks/traditional/devops.md) |

语言栈不是流程入口。Java、Go、Vue、React、TypeScript 只影响验证命令和局部最佳实践，不改变 SOP。

---

## 任务最小单元

任何 AI coding 任务都必须使用任务卡描述：

- 目标。
- 上下文文件。
- 允许修改范围。
- 禁止修改范围。
- 验收标准。
- 必须运行的验证命令。
- 完成后输出。

模板：[07_sop/templates/task-card.md](07_sop/templates/task-card.md)

不接受这种任务：

```text
帮我把系统做完。
```

接受这种任务：

```text
基于 docs/03_architecture/api_specs.md，实现 GET /v1/orders。
只修改 src/backend/orders 和对应测试。
不得修改认证中间件和 API 契约。
先写集成测试，验证未登录 401、只能看自己的订单、分页可用。
运行 mvn test 或对应模块测试，并输出结果。
```

---

## 完成定义

只有同时满足以下条件，才能宣称完成：

1. 任务卡验收标准已满足。
2. 必要测试、构建、lint、typecheck 或替代验证已执行。
3. 变更范围没有越界。
4. 文档和契约已同步。
5. 风险和未覆盖项已说明。
6. `memory/active-task.md` 或 `memory/handoff.md` 已更新。
7. 需要人类确认的事项没有被 AI 擅自执行。

---

## 禁止事项

- 未经确认直接 push。
- 没有测试或验证结果就说完成。
- 在功能 PR 中顺手大改架构、契约、CI 或权限。
- 前端自行假设 API 字段。
- 后端随意改响应结构。
- 把重要决策只留在聊天记录里。
- 写模板化、无意义、反人类阅读的 AI 味文档或代码。
