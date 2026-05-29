# 任务驱动 AI 协作剧本

> 本文档解决“拿到一个任务后，如何和 Claude Code、Codex 等 AI 编程工具协作直到完成”的问题。

---

## 一句话执行模型

```text
任务输入
  ↓
任务分类
  ↓
选择 skill chain
  ↓
按模板给 AI 输入
  ↓
AI 产出计划 / 测试 / 代码 / 文档
  ↓
运行验证
  ↓
Review / 修复循环
  ↓
更新 docs + memory
```

不要让 AI 直接“开始写”。先让 AI 做 3 件事：

1. 读上下文。
2. 分类任务。
3. 生成本次执行链路。

---

## Claude Code 与 Codex 的使用方式

| 工具 | 如何触发技能 | 推荐输入方式 |
|------|--------------|--------------|
| Claude Code | 直接使用 slash command，例如 `/deep-interview`、`/tdd-workflow` | 贴任务卡 + 说明“按本项目 SOP 执行” |
| Codex | 在 Prompt 中明确写“使用 `tdd-workflow` / `api-design` / `verification-before-completion` skill” | 贴任务卡 + 指定允许修改范围和验证命令 |
| 其他 AI 编程工具 | 不一定支持本地 skills | 按本文 skill chain 的意图执行，至少保留输入、输出、验证和交接 |

Codex 示例：

```text
请使用本仓库 `docs/07_sop/task-driven-collaboration.md` 的任务驱动流程执行。
本任务类型是后端 API。
请使用 `api-design`、`tdd-workflow`、`verification-before-completion` 的工作方式。
先不要写代码，先读取上下文并输出计划。

[粘贴任务卡]
```

Claude Code 示例：

```text
/api-design

请按 docs/07_sop/task-driven-collaboration.md 执行。
先读取任务卡和上下文，不要直接实现。

[粘贴任务卡]
```

---

## 第 0 步：任务分类

拿到任务后，先判断任务类型：

| 任务类型 | 典型输入 | 主要输出 |
|----------|----------|----------|
| 需求澄清 | 一句话需求、会议纪要、用户反馈 | PRD、AC、任务卡 |
| 架构设计 | PRD、现有系统约束、技术问题 | 架构方案、ADR、模块边界 |
| API / 契约 | PRD、页面需求、服务间调用 | API 文档、DTO、错误码、contract test |
| 前端功能 | 设计稿、PRD、API 契约 | 页面/组件、测试、交互状态 |
| 后端功能 | API 契约、业务规则、DB schema | 接口/服务/数据层、测试 |
| Bug 修复 | 报错、复现步骤、日志 | 复现测试、根因、修复、回归验证 |
| QA 验收 | PR、测试用例、部署地址 | 测试报告、缺陷、回归结论 |
| 发布上线 | 版本说明、部署环境、变更范围 | release notes、runbook、回滚方案 |
| 文档沉淀 | 已完成变更、复盘材料 | docs 更新、memory 更新、技能沉淀 |

分类 Prompt：

```text
请先不要写代码。
请读取我给出的任务和仓库上下文，按 docs/07_sop/task-driven-collaboration.md 对任务分类。
输出：
1. 任务类型
2. 应使用的 skill chain
3. 必须读取的文件
4. 允许修改范围建议
5. 需要我确认的问题
```

---

## Skill Chain 速查

| 任务类型 | 推荐 skill chain | AI 期望输出 |
|----------|------------------|-------------|
| 需求不清楚 | `/deep-interview` → `/ralplan` → `feature-spec.md` | 澄清问题、PRD 草稿、AC、任务拆分 |
| 新功能全流程 | `karpathy-guidelines` → `/brainstorming` → `/writing-plans` → `/tdd-workflow` → `/verification-before-completion` → `/review` | 计划、测试、实现、验证结果、review 修复 |
| 前端页面/组件 | `/frontend-design` → `/frontend-patterns` → `/tdd-workflow` → `/e2e-testing` → `/fixing-accessibility` → `/polish` → `/verification-before-completion` | UI、组件测试/E2E、可访问性修复、视觉验收 |
| React / TypeScript | `/react-best-practices` → `/frontend-patterns` → `/e2e-testing` | React 实现、类型检查、交互测试 |
| Vue / TypeScript | `/frontend-patterns` → `/e2e-testing` → `/verification-before-completion` | Vue 实现、组件/E2E 验证 |
| 后端 API | `/api-design` → `/tdd-workflow` → `/backend-patterns` → `/verification-before-completion` → `/review` | API 实现、单元/集成测试、验证结果 |
| Java / Spring Boot | `/api-design` → `/springboot-tdd` → `/springboot-patterns` → `/java-coding-standards` → `/springboot-verification` | Spring 测试、实现、架构规范校验 |
| Go 服务 | `/api-design` → `/tdd-workflow` → `/backend-patterns` → `/verification-before-completion` | Go 测试、实现、`go test ./...` 结果 |
| Bug 修复 | `/systematic-debugging` → `/trace` → `/tdd-workflow` → `/verification-before-completion` → `/learner` | 根因、复现测试、修复、项目经验 |
| 安全敏感改动 | `/cso` 或安全 review agent → `/review` → `/verification-before-completion` | 安全发现、修复建议、验证结果 |
| QA 验收 | `/qa-only` → `/ultraqa` → `/verification-before-completion` | 缺陷列表、修复循环、最终报告 |
| 发布上线 | `/ship` → `/canary` → `/document-release` → `/retro` | 发布检查、监控、发布文档、复盘 |
| 文档/知识沉淀 | `/wiki` → `/remember` → `/skillify` | docs 更新、项目记忆、自定义 skill 草稿 |

说明：

- 不支持某个 slash command 时，按对应 skill 的意图执行，不要跳过该阶段。
- 涉及 UI 的任务完成前必须有截图、浏览器验证或 E2E 结果。
- 涉及 API 的任务必须有契约和失败路径验证。

---

## 标准协作循环

### 1. 启动会话

输入：

```text
请按 docs/07_sop/task-driven-collaboration.md 执行本任务。
先不要写代码。

任务：
[粘贴任务或任务卡]

请输出：
1. 任务分类
2. 推荐 skill chain
3. 你要读取的文件
4. 你认为任务卡缺失的信息
5. 下一步是否可以进入计划
```

期望输出：

- 任务类型。
- skill chain。
- 上下文读取清单。
- 缺失问题。
- 是否可以进入计划。

下一步：

- 如果需求不清楚，进入 `/deep-interview`。
- 如果上下文足够，进入计划。

---

### 2. 产出任务卡

使用模板：[templates/task-card.md](templates/task-card.md)

输入：

```text
请把当前任务整理成 docs/07_sop/templates/task-card.md 格式。
必须补齐：
- AI 工具
- skill chain
- 允许修改范围
- 禁止修改范围
- 验收标准
- 验证命令

如果信息不足，先问问题，不要编造。
```

期望输出：

- 可直接执行的任务卡。
- 待确认问题。

下一步：

- 人类确认任务卡。
- AI 进入计划或 TDD。

---

### 3. 计划

输入：

```text
请基于已确认任务卡制定实施计划。
要求：
1. 不写代码。
2. 说明会新增或修改哪些测试。
3. 说明会修改哪些文件。
4. 说明每一步的验证方式。
5. 标出需要人类确认的风险。
```

期望输出：

- 3-7 步实施计划。
- 测试计划。
- 文件范围。
- 风险点。

下一步：

- 小任务可直接执行。
- 高风险任务先让资深研发或架构 review。

---

### 4. TDD 执行

输入：

```text
请按任务卡执行 TDD：
1. 先写失败测试或验收脚本。
2. 运行测试并展示失败原因。
3. 写最小实现。
4. 运行验证命令。
5. 只在测试通过后重构。
6. 不要修改任务卡禁止范围。
```

期望输出：

- 测试文件。
- 实现代码。
- 验证命令结果。
- 未覆盖项。

下一步：

- 失败则进入 debug chain。
- 通过则进入 review chain。

---

### 5. Debug 循环

输入：

```text
请使用 `/systematic-debugging` 的方式处理当前失败。
要求：
1. 先复述失败现象和命令。
2. 列出 2-4 个假设。
3. 为每个假设设计验证动作。
4. 只根据证据修改代码。
5. 修复后补回归测试。
```

期望输出：

- 根因。
- 证据。
- 修复。
- 回归测试。

下一步：

- 修复通过后用 `/learner` 把项目坑写入 `memory/project-facts.md`。

---

### 6. Review 与清理

输入：

```text
请按 docs/07_sop/templates/review-checklist.md 自审当前 diff。
重点检查：
1. 是否越过任务卡范围
2. 是否缺测试
3. 是否破坏契约
4. 是否有 AI 味代码或无意义抽象
5. 是否有安全风险

先输出 findings，不要直接改。
```

期望输出：

- 必须修复问题。
- 建议修复问题。
- 可接受风险。

下一步：

- 对必须修复问题执行修复。
- 再跑验证。

---

### 7. 完成与交接

输入：

```text
请完成收尾：
1. 汇总改动。
2. 汇总验证命令和结果。
3. 更新 memory/active-task.md。
4. 更新 memory/handoff.md。
5. 如果有长期规则，更新 memory/project-facts.md。
6. 列出还需要人类确认的事项。
```

期望输出：

- 改动摘要。
- 验证摘要。
- 风险摘要。
- memory 已更新。

下一步：

- 需要合并时走 PR Review。
- 需要上线时走 `/ship`。

---

## 常见任务剧本

### 剧本 A：一句话需求变成可执行任务

输入：

```text
我要做一个订单列表，用户登录后能看到自己的订单。
请使用 `/deep-interview` 和 docs/07_sop/templates/feature-spec.md，把它变成可开发规格。
不要写代码。
```

AI 应输出：

- 澄清问题。
- PRD 草稿。
- AC。
- API/页面/测试任务拆分。

完成条件：

- `docs/01_product/prd_orders.md` 可落地。
- `memory/active-task.md` 有任务树。

---

### 剧本 B：实现一个后端 API

输入：

```text
请按 docs/07_sop/task-driven-collaboration.md 执行。
任务类型：后端 API。
使用 skill chain：/api-design → /tdd-workflow → /backend-patterns → /verification-before-completion → /review。

[粘贴任务卡]
```

AI 应输出：

- API 契约检查。
- 集成测试。
- 实现代码。
- 验证结果。
- review 修复。

完成条件：

- 成功路径、未登录、越权、参数错误都有验证。
- API 文档同步。

---

### 剧本 C：实现一个前端页面

输入：

```text
请按 docs/07_sop/task-driven-collaboration.md 执行。
任务类型：前端页面。
使用 skill chain：/frontend-design → /frontend-patterns → /tdd-workflow → /e2e-testing → /fixing-accessibility → /verification-before-completion。

[粘贴任务卡]
```

AI 应输出：

- 页面结构计划。
- 组件测试或 E2E。
- loading / empty / error 状态。
- 响应式和可访问性检查。
- 构建或测试结果。

完成条件：

- 页面不依赖后端未确认字段。
- UI 状态完整。
- 浏览器或 E2E 验证通过。

---

### 剧本 D：修复一个 Bug

输入：

```text
请使用 `/systematic-debugging`。
Bug 现象：
[粘贴报错、日志、复现步骤]

要求：
1. 先复现。
2. 再写回归测试。
3. 再修复。
4. 修复后用 `/verification-before-completion` 验证。
5. 如果是项目规律，使用 `/learner` 方式沉淀到 memory/project-facts.md。
```

AI 应输出：

- 复现命令。
- 根因证据。
- 回归测试。
- 修复代码。
- 项目经验。

---

### 剧本 E：PR 前审查

输入：

```text
请按 docs/07_sop/templates/review-checklist.md 审查当前 diff。
请先输出 findings，按严重度排序。
不要直接修改代码。
```

AI 应输出：

- P0/P1/P2 findings。
- 文件和行号。
- 缺失测试。
- 风险和建议。

下一步：

- 对 P0/P1 必须修复。
- 修复后再次验证。

---

## 不同资深度的执行边界

| 使用者 | 可以让 AI 做 | 人必须确认 |
|--------|--------------|------------|
| 初级研发 | 读代码、写测试、实现小任务、解释 diff | 任务卡、API 字段、合并、上线 |
| 资深研发 | 方案对比、反方审查、重构拆分、测试设计 | 架构取舍、数据边界、风险接受 |
| 产品/测试 | PRD、AC、测试用例、缺陷报告 | 业务目标、验收口径、优先级 |
| 运维 | runbook、发布检查、日志分析 | 生产部署、回滚、权限和 secrets |

---

## 停止条件

出现以下情况，AI 必须停止实现，改为提问或输出提案：

- 任务卡缺验收标准。
- 需要修改禁止范围。
- 契约不满足实现。
- 需要不可逆 migration。
- 需要修改 CI/CD、secrets、生产权限。
- 验证命令不存在且没有替代验证。
