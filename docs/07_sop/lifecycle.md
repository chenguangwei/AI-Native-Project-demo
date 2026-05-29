# AI-Native Coding 生命周期 SOP

> 本 SOP 适用于单人、小团队和复杂团队。差别只在产物多少，不在流程原则。

---

## 阶段总览

| 阶段 | 人的职责 | AI 的职责 | 关键产物 | Gate |
|------|----------|-----------|----------|------|
| 0. 工程底座 | 定仓库结构、技术栈、权限边界 | 补规则、命令、模板、测试骨架 | `AGENTS.md`、`CLAUDE.md`、`docs/`、`memory/`、CI | AI 能安装、构建、测试 |
| 1. 需求澄清 | 定目标、用户、边界、优先级 | 访谈、补边界、生成验收标准 | PRD、用户故事、AC、非功能需求 | 需求可验收 |
| 2. 架构边界 | 做技术取舍和风险接受 | 给方案、反方审查、写 ADR 草稿 | 系统图、模块边界、ADR | 架构被人确认 |
| 3. 契约优先 | 确认 API、数据、事件协议 | 生成 OpenAPI、DTO、mock、contract test | API 契约、错误码、DB schema | 前后端可并行 |
| 4. 垂直切片 | 选择核心闭环场景 | 实现最小端到端路径 | 一个真实页面/API/DB/E2E | E2E 或集成测试通过 |
| 5. 小批量功能 PR | 拆 issue、控范围、审计划 | 每卡一个小 PR，TDD 实现 | 功能 PR、测试、文档更新 | CI + Review |
| 6. Review 与硬化 | 决定上线标准 | 补异常、安全、性能、可观测性 | 审查记录、测试报告、runbook | staging 验证 |
| 7. 发布与复盘 | 控制发布、回滚、规则沉淀 | release notes、日志分析、复盘草稿 | changelog、postmortem、project-facts | 生产反馈闭环 |

---

## 阶段 0：工程底座

必备动作：

1. 根目录存在 `AGENTS.md` 或 `CLAUDE.md`，说明技术栈、目录、测试命令和禁止事项。
2. `memory/active-task.md`、`memory/handoff.md`、`memory/project-facts.md` 可用。
3. `docs/01_product/`、`docs/03_architecture/`、`docs/04_qa/` 至少有占位文档。
4. 项目能跑最小验证命令，例如 `npm test`、`mvn test`、`go test ./...`、`pnpm typecheck`。

禁止：

- 没有验证命令就宣称工程可交付。
- 未经确认修改 CI secrets、生产环境配置、认证、计费、权限核心逻辑。

---

## 阶段 1：需求澄清

输入：

- 用户目标、业务背景、现有痛点。
- 设计稿、竞品、历史 issue 或会议纪要。

输出：

- `docs/01_product/prd_{feature}.md`
- 验收标准：Given / When / Then。
- 不做范围：明确写出本期不处理的内容。
- 初始任务树：`memory/active-task.md`

初级研发做法：

- 不直接让 AI 写代码。
- 先让 AI 按 [feature-spec.md](templates/feature-spec.md) 追问缺口。
- 需求没有 AC，不进入开发。

资深研发做法：

- 检查非功能要求：权限、审计、性能、可观测性、数据一致性。
- 把会影响架构的假设写入 PRD 或 ADR。

---

## 阶段 2：架构边界

输入：

- PRD、业务规则、现有系统约束。

输出：

- `docs/03_architecture/system_flow.md`
- `docs/03_architecture/api_specs.md`
- `docs/03_architecture/db_schema.md`
- 必要时新增 ADR。

关键要求：

- 简单项目优先模块化单体。
- 复杂项目先定领域边界，再定服务拆分。
- AI 可以给三套方案，但最终取舍必须由人确认。

Gate：

- 每个模块知道自己负责什么、不负责什么。
- 每个跨模块调用有契约。
- 数据所有权清楚。

---

## 阶段 3：契约优先

适用场景：

- 前后端分离。
- 多服务协作。
- Java、Go、Node、Python 后端和 Vue、React、TypeScript 前端并行开发。

流程：

1. 先写 API/事件/数据契约。
2. 前端按契约生成 mock 或客户端类型。
3. 后端按契约写 controller/service contract test。
4. QA 按契约生成接口测试和 E2E 场景。
5. 契约变化先提交 [contract-change-proposal.md](templates/contract-change-proposal.md)。

禁止：

- 前端自行假设字段。
- 后端随意改响应结构。
- 在同一个 PR 里同时大改契约和大改实现。

---

## 阶段 4：垂直切片

第一个切片必须打穿真实闭环：

```text
用户入口 → 页面/接口 → 业务逻辑 → 数据读写 → 错误处理 → 自动化验证
```

最小内容：

- 一个核心实体。
- 一个列表页或详情页。
- 一个真实 API。
- 一条数据库读写路径。
- loading / empty / error 状态。
- 一个 E2E、集成测试或可替代的 smoke test。

目的：

- 给后续 AI 生成提供可模仿的正确模式。
- 提前暴露认证、路由、跨域、数据格式、部署环境问题。

---

## 阶段 5：小批量功能 PR

每个任务必须来自任务卡：

- 推荐模板：[templates/task-card.md](templates/task-card.md)
- 一个任务卡对应一个小 PR。
- 任务卡必须写验证命令。

适合 AI 的任务：

- 一个页面。
- 一个 API。
- 一个领域内的小功能。
- 一个明确 Bug。
- 一组同类测试补齐。

不适合 AI 直接做的任务：

- 重写整个权限系统。
- 迁移所有数据库。
- 一次性重构全前端。
- “把系统做完”。

这类任务要先拆 milestone，再逐卡执行。

---

## 阶段 6：Review 与硬化

Review 分工：

| 视角 | 检查重点 |
|------|----------|
| 正确性 | 边界条件、并发、异常、状态机 |
| 安全 | 认证、授权、注入、敏感数据、越权 |
| 测试 | 是否断言行为，是否覆盖失败路径 |
| 架构 | 是否破坏模块边界和契约 |
| 前端体验 | 可访问性、加载态、错误态、响应式 |
| 运维 | 配置、日志、监控、回滚 |

执行：

1. AI 先自审。
2. 必要时用专用 review agent 或技能。
3. 人类最终 review。
4. 未通过 gate 不合并。

---

## 阶段 7：发布与复盘

发布前：

- smoke test checklist 已执行。
- migration 风险清楚。
- 回滚路径清楚。
- release notes 已写。

发布后：

- 生产问题写入 `docs/05_ops/runbook.md`。
- 项目长期坑写入 `memory/project-facts.md`。
- 当前任务状态写入 `memory/handoff.md`。
- 可复用经验沉淀到 `docs/00_ai_system/` 或对应手册。

---

## 通用完成定义

只有同时满足以下条件，才能说“完成”：

1. 代码或文档满足任务卡验收标准。
2. 必要测试、构建、lint、typecheck 或人工替代验证已执行。
3. 变更摘要清楚。
4. 风险和未覆盖项已写明。
5. `memory/` 已更新当前状态。
