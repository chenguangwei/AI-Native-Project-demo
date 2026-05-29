
不要让 AI 一句话生成全栈系统，而要把它编排成受约束的工程单元：先规划再小批量实现，并依赖仓库规则、可运行验证和 PR 门禁。

关键判断是：瓶颈通常不在模型会不会写代码，而在上下文、任务粒度、验收标准和自动化反馈；所以要规格/契约/测试先行，小批量 PR，并由人控制架构与风险边界。
**必须按建设阶段区分编排方式**。大型前后端工程里，AI coding 的最优结果不是“让 AI 一口气写完整系统”，而是把 AI 当成一个**受约束、可验证、可审查的软件工程执行层**：人负责目标、边界、架构取舍和最终责任；AI 负责探索、拆解、实现、补测试、改 PR、写文档、跑检查。

核心原则可以概括为一句话：

> **不要编排 prompt，编排工程系统：需求文档、架构约束、任务边界、上下文文件、测试信号、PR 流程、自动化 gate。**

一些官方 coding agent 文档里也有类似共识：例如 Claude Code 明确建议“先探索、再规划、再实现、再提交”，并强调要给 agent 可运行的验证信号，比如测试、构建、lint 或截图对比；没有验证信号时，人就会变成唯一的反馈循环。([Claude Code][1]) Codex 的云环境也按“安装依赖/准备环境 → agent 编辑代码并运行检查 → 输出 diff → 创建 PR/继续追问”的模式工作。([OpenAI开发者][2])

---

## 一、总编排模型：从“AI 写代码”升级为“AI 执行 SDLC（软件开发生命周期）”

建议把 AI-native coding 编排成四层：

### 1. 上下文层：让 AI 每次启动都知道工程规则

仓库里要有 AI 可读的项目说明，不要每次靠口头 prompt 重复解释。可以用：

```text
AGENTS.md / CLAUDE.md / .cursor/rules / docs/ai/*
```

里面写清楚：

```text
1. 技术栈与包管理器
2. 项目目录结构
3. 前端架构规则
4. 后端架构规则
5. API 命名与错误码规范
6. 数据库 migration 规则
7. 测试命令、lint 命令、typecheck 命令
8. 禁止事项：不要改认证逻辑、不要新增生产依赖、不要改 CI secrets 等
9. PR 规范：小 PR、必须说明变更、必须附测试结果
```

Codex 的官方文档说明它会在开始工作前读取 `AGENTS.md`，并支持全局、仓库级、子目录级的指令叠加；仓库根目录可以放通用规则，具体服务目录可以放覆盖规则。([OpenAI开发者][3]) Claude Code 也建议用 `CLAUDE.md` 保存持久项目指令，例如 build 命令、代码风格、测试偏好和项目结构。([Claude API Docs][4])

### 2. 任务层：把需求拆成 AI 可执行的“小闭环任务”

每个 AI 任务必须有：

```text
目标
上下文
涉及文件范围
不允许做什么
验收标准
必须运行的验证命令
完成后输出内容
```

不要给 AI 这种任务：

```text
做一个完整 SaaS 系统，包含登录、支付、后台、报表。
```

要拆成这种任务：

```text
实现用户登录后查看订单列表的后端 API。

范围：
- 只修改 apps/api/src/orders/*
- 可新增测试文件
- 不修改认证中间件
- 不改数据库表结构，除非先提出 migration plan

输入：
- docs/api/orders.openapi.yaml
- docs/domain/order.md
- apps/api/src/auth/currentUser.ts

验收：
- GET /v1/orders 返回当前用户订单
- 未登录返回 401
- 只能看到自己的订单
- 包含分页参数 limit/cursor

验证：
- pnpm --filter api test orders
- pnpm --filter api typecheck
- pnpm --filter api lint

流程：
1. 先阅读相关文件并给出实现计划
2. 等计划明确后再编码
3. 写测试
4. 运行验证命令
5. 输出改动摘要、测试结果、风险点
```

### 3. 验证层：让 AI 能自己判断“是否完成”

AI coding 最怕“看起来完成”。所以每个任务都要有机器可验证信号：

```text
单元测试
集成测试
E2E 测试
类型检查
lint
API contract test
数据库 migration test
截图对比
性能 smoke test
安全扫描
```

Claude Code 官方最佳实践明确提到，应给 agent 一个可运行的检查，例如测试套件、build exit code、linter、fixture diff 或浏览器截图；agent 可以读取结果并迭代，直到检查通过。([Claude Code][1]) GitHub Copilot 的企业实践文档也提醒，不应未经测试就应用 AI 建议，也不能只依赖 AI review。([GitHub Docs][5])

### 4. 控制层：所有 AI 输出都走 issue → branch → PR → CI → review

AI 可以写代码，但不要直接进主干。推荐流程：

```text
需求 / issue
  ↓
AI 生成技术方案
  ↓
人审方案
  ↓
AI 在独立 branch / worktree 实现
  ↓
AI 自测
  ↓
AI 开 draft PR
  ↓
CI 跑完整 gate
  ↓
AI review + 人 review
  ↓
人合并
```

GitHub Copilot coding agent 和 OpenAI Codex 都支持围绕 issue、PR、代码审查和后续修复来工作，例如 agent 可以创建 PR，或者在 PR 评论中被要求修复 CI 问题、处理 review finding。([The GitHub Blog][6]) 但 GitHub 文档也明确要求：AI 生成的 PR 应像任何贡献一样被认真 review，尤其要检查 workflow 文件，因为 GitHub Actions 可能接触 secrets。([GitHub Docs][7])

---

## 二、按建设阶段的 AI 编排流程

下面是我建议的大型前后端工程 AI-native 编排。

| 阶段         | 人的职责            | AI 的最佳用法                         | 关键产物                            | Gate              |
| ---------- | --------------- | -------------------------------- | ------------------------------- | ----------------- |
| 0. AI 工程底座 | 定技术栈、仓库结构、权限边界  | 生成项目规则、测试命令、CI 模板、PR 模板          | `AGENTS.md`、`docs/ai/*`、CI、测试骨架 | AI 能安装、build、test |
| 1. 需求澄清    | 定业务目标、优先级、边界    | 访谈式澄清、写 PRD、列边界条件                | PRD、用户故事、非功能需求                  | 需求可验收             |
| 2. 架构设计    | 做关键技术取舍         | 生成方案对比、风险分析、ADR 草稿               | 架构图、ADR、模块边界                    | 人审架构              |
| 3. 契约优先    | 定 API、数据模型、事件模型 | 生成 OpenAPI、类型、mock、contract test | OpenAPI、DB schema、DTO           | 前后端可并行            |
| 4. 垂直切片    | 选一个端到端核心场景      | AI 实现最小闭环                        | 登录/核心实体/一个页面/API/DB/E2E         | E2E 通过            |
| 5. 批量功能建设  | 排优先级、拆任务        | 每个 issue 一个小 PR，AI 实现+自测         | 功能 PR、测试、文档                     | CI + review       |
| 6. 集成与硬化   | 决定上线标准          | AI 补异常处理、权限、性能、安全、可观测性           | 测试报告、runbook、监控                 | staging 验证        |
| 7. 发布与演进   | 控制发布、回滚、复盘      | AI 写 release notes、排查日志、修复回归     | changelog、postmortem、规则更新       | 生产反馈闭环            |

---

## 三、阶段 0：先搭 AI 工程底座，不要先写业务代码

这是最容易被忽略、但对 AI 输出质量影响最大的阶段。

### 目录建议

```text
docs/
  product/
    PRD.md
    user-stories.md
  architecture/
    system-overview.md
    adr/
      0001-tech-stack.md
      0002-auth-model.md
      0003-api-style.md
  api/
    openapi.yaml
    error-codes.md
  domain/
    user.md
    order.md
    payment.md
  frontend/
    routing.md
    state-management.md
    design-system.md
  backend/
    module-boundaries.md
    database.md
    authz.md
  qa/
    test-strategy.md
    e2e-scenarios.md
  ai/
    task-template.md
    review-checklist.md
    prompt-playbook.md

AGENTS.md
.claude/CLAUDE.md       # 如果用 Claude Code
.github/
  pull_request_template.md
  workflows/
```

### `AGENTS.md` / `CLAUDE.md` 示例

```md
# Repository Instructions for AI Coding Agents

## Project
This is a full-stack web application.
- Frontend: React + TypeScript + Vite
- Backend: Node.js + TypeScript + PostgreSQL
- Package manager: pnpm
- Monorepo layout:
  - apps/web
  - apps/api
  - packages/shared

## Commands
- Install: pnpm install
- Frontend test: pnpm --filter web test
- Backend test: pnpm --filter api test
- Typecheck: pnpm typecheck
- Lint: pnpm lint
- E2E: pnpm e2e

## Architecture Rules
- Frontend must call backend through generated API client.
- Backend routes must not contain raw SQL.
- Shared DTOs live in packages/shared.
- All public API behavior changes require docs/api/openapi.yaml update.

## Testing Rules
- Any backend behavior change requires unit or integration tests.
- Any frontend user-flow change requires component test or E2E test.
- Before opening PR, run relevant tests and include output summary.

## Safety Rules
- Do not modify .github/workflows without explicit approval.
- Do not add production dependencies without explaining why.
- Do not edit auth, billing, or migration files without a plan first.
- Never commit secrets.
```

大型仓库尤其要避免“根目录一个超长规则文件”。Claude Code 的大型代码库指南建议按目录拆分指令，让 agent 只加载当前工作区域相关规则；也可以通过稀疏 worktree 限制 agent 只看到/编辑任务相关目录。([Claude Code][8])

---

## 四、阶段 1：需求阶段，AI 不应直接写代码，而应先“逼出规格”

这阶段 AI 的角色是：

```text
产品分析师
需求澄清者
边界条件发现者
验收标准生成器
测试场景生成器
```

推荐 prompt：

```text
你现在是资深产品工程师。我要建设一个 [系统简述]。

请先不要写代码。请用访谈方式向我提问，目标是产出一份可交给 coding agent 执行的 SPEC.md。

你需要覆盖：
1. 用户角色
2. 核心业务流程
3. 权限模型
4. 数据对象
5. 异常场景
6. 非功能需求：性能、安全、审计、可观测性
7. MVP 范围
8. 暂不做的范围
9. 可验收测试

提问不要泛泛而谈，要问会影响架构和实现的关键问题。
```

Claude Code 的实践文档也建议，对大型功能可以让 agent 先“采访”你，覆盖技术实现、UI/UX、边界条件和 tradeoff，并最终生成自包含 spec；完成 spec 后再开启新会话执行，避免旧上下文污染实现。([Claude Code][1])

阶段 1 的输出应该是：

```text
SPEC.md
user-stories.md
acceptance-criteria.md
non-functional-requirements.md
out-of-scope.md
test-scenarios.md
```

判断是否可以进入下一阶段的标准：

```text
每个需求都有明确验收标准；
核心流程可以写成 E2E 测试；
不做什么也写清楚；
权限、数据、异常、审计要求明确。
```

---

## 五、阶段 2：架构阶段，AI 应作为“方案生成器 + 反方审查者”

不要让 AI 单方面定架构。建议一正一反：

```text
Agent A：提出架构方案
Agent B：攻击该方案，找风险
人：最终拍板
```

架构阶段重点产物：

```text
system-overview.md
module-boundaries.md
data-model.md
api-style.md
auth-model.md
deployment.md
ADR 文档
```

推荐 prompt：

```text
请基于 docs/product/PRD.md 和 docs/domain/*.md，提出 3 种后端架构方案：
1. 模块化单体
2. 服务化但单仓库
3. 微服务

请比较：
- 开发复杂度
- AI coding 可执行性
- 测试复杂度
- 部署复杂度
- 团队协作成本
- 未来 12 个月演进空间
- 失败风险

最后给出推荐方案，并写出 ADR 草稿。
```

对大多数中早期复杂项目，我会优先建议：

```text
模块化单体 + 清晰领域边界 + contract-first API + monorepo
```

原因是 AI coding 对“上下文局部性”非常敏感。模块化单体既能保持部署简单，又能让前后端、shared types、测试、API contract 在一个仓库内被 agent 理解。等边界稳定后，再拆服务。

---

## 六、阶段 3：契约优先，让前后端 AI 并行但不互相踩踏

前后端工程最容易出问题的是：

```text
前端 agent 自己假设接口
后端 agent 自己定义字段
最后集成时大量返工
```

所以要先定 contract：

```text
OpenAPI / GraphQL schema
错误码规范
认证方式
分页规范
DTO 类型
数据库核心实体
事件模型
mock 数据
contract tests
```

编排方式：

```text
1. 架构 agent 生成 API contract 草稿
2. 后端 agent 审查是否可实现
3. 前端 agent 审查是否好用
4. QA agent 根据 contract 生成测试
5. 人确认
6. 再允许前后端并行实现
```

推荐规则：

```text
任何 API contract 变更必须单独 PR；
前端不得直接修改后端 contract；
后端不得随意改响应字段；
shared types 由 contract 生成；
mock server 跟 contract 同步。
```

前端任务示例：

```text
基于 docs/api/openapi.yaml 和 mock server，实现订单列表页面。
不要修改 API contract。
如果发现 contract 不满足 UI，需要输出 contract change proposal，不要直接改。
```

后端任务示例：

```text
实现 docs/api/openapi.yaml 中 GET /v1/orders。
必须通过 contract test。
不要修改响应 schema，除非先提出 proposal。
```

---

## 七、阶段 4：先做一个“垂直切片”，不要横向铺满

AI coding 很容易在大系统里“局部正确、整体不可用”。所以第一阶段不要先做完整前端、完整后端、完整数据库，而是做一个端到端闭环：

```text
用户登录
  ↓
访问一个核心页面
  ↓
调用一个真实 API
  ↓
读写数据库
  ↓
返回真实数据
  ↓
前端展示
  ↓
E2E 测试通过
  ↓
部署到 staging
```

垂直切片应该包含：

```text
认证
路由
一个核心实体
一个列表页
一个详情页或创建页
数据库 migration
后端 API
前端 API client
基础错误处理
基础 loading / empty / error UI
日志
E2E 测试
CI
```

这个切片完成后，再让 AI 批量扩展同类功能，质量会明显提升，因为仓库里已经有“可模仿的正确模式”。

Claude Code 文档也强调给 agent 指向现有模式，例如让它参考已有 widget、已有 auth flow 或已有测试方式，而不是凭空实现。([Claude Code][1])

---

## 八、阶段 5：功能建设阶段，用“issue 工厂 + PR 工厂”编排 AI

成熟后，每个功能都按这个节奏走：

```text
Epic
  ↓
Feature spec
  ↓
Task card
  ↓
AI plan
  ↓
Human approve plan
  ↓
AI implement
  ↓
AI self-review
  ↓
AI tests
  ↓
Draft PR
  ↓
AI code review
  ↓
Human review
  ↓
Merge
```

### 任务拆分粒度

一个适合 AI 的任务通常满足：

```text
可以在一个 PR 内完成；
涉及一个领域或一个页面；
验收标准明确；
测试命令明确；
回滚成本低；
不需要同时改 10 个不相关模块。
```

不适合 AI 直接执行的任务：

```text
重构整个权限系统；
重新设计所有页面；
迁移数据库到新架构；
修复所有性能问题；
把系统做完。
```

这类任务要先拆成 milestone：

```text
Milestone 1：补 characterization tests
Milestone 2：抽象接口但不改行为
Milestone 3：迁移一个调用点
Milestone 4：迁移剩余调用点
Milestone 5：删除旧实现
```

Codex 官方工作流里也有类似模式：本地先规划复杂 refactor，明确约束、迁移步骤和回滚策略，再把具体 milestone 委托给云端任务实现。([OpenAI开发者][9])

---

## 九、阶段 6：Review 阶段要多 agent，但不要多 agent 同时乱写

多 agent 最适合做：

```text
代码审查
安全审查
测试覆盖审查
边界条件审查
性能审查
文档审查
```

不适合做：

```text
多个 agent 同时改同一批文件
多个 agent 同时改 API contract
多个 agent 同时改 migration
```

推荐 review 编排：

```text
实现 agent：完成功能
测试 agent：检查测试覆盖和缺失用例
安全 agent：检查认证、授权、注入、敏感数据
前端 review agent：检查交互、状态、可访问性
架构 agent：检查是否破坏模块边界
人：最终 review
```

Codex 的 subagent 文档说明，subagent 适合复杂且可并行的任务，例如代码库探索或多角度 PR review；但它们会消耗更多 token，并且需要明确要求才会启动。([OpenAI开发者][10]) Claude Code 文档也建议用 subagent 做调查和验证，以避免主会话上下文被大量文件读取污染。([Claude Code][1])

PR review prompt 示例：

```text
请审查当前 PR，相对于 main。

分 5 个角度分别检查：
1. 安全：认证、授权、注入、敏感信息
2. 正确性：边界条件、异常场景、并发问题
3. 测试：缺失测试、测试是否真的断言行为
4. 架构：是否违反 docs/architecture/module-boundaries.md
5. 前后端契约：是否符合 docs/api/openapi.yaml

请输出：
- 必须修复的问题
- 建议修复的问题
- 可以接受的 tradeoff
- 具体文件和行号
- 建议修改方案

不要直接改代码，先给 review。
```

---

## 十、阶段 7：发布和生产阶段，AI 可以辅助，但不能完全放权

上线阶段 AI 适合做：

```text
生成 release notes
生成回滚方案
生成 smoke test checklist
检查环境变量
检查 migration 风险
分析日志
总结错误堆栈
生成 runbook
```

但这些需要人确认：

```text
生产部署
数据库不可逆 migration
权限/IAM 变更
CI/CD workflow 变更
secrets 变更
支付/账务逻辑
安全策略
```

GitHub 文档特别提醒，AI agent 的 PR 中如果涉及 GitHub Actions workflow，需要在运行前人工检查，因为 workflow 可能有权限和 secrets 风险。([GitHub Docs][7])

---

## 十一、推荐的 agent 角色分工

不要一开始就搞复杂的“十几个 autonomous agents”。建议从下面 6 个角色开始：

### 1. Spec Agent

负责：

```text
澄清需求
生成用户故事
补边界条件
写验收标准
生成测试场景
```

### 2. Architect Agent

负责：

```text
模块边界
技术方案比较
ADR 草稿
风险分析
重构计划
```

### 3. Backend Agent

负责：

```text
API
数据库
领域逻辑
权限
集成测试
```

### 4. Frontend Agent

负责：

```text
页面
组件
状态管理
表单
API client
UI 测试
```

### 5. QA Agent

负责：

```text
测试策略
单测补全
集成测试
E2E
回归测试
测试数据
```

### 6. Reviewer / Security Agent

负责：

```text
代码审查
安全审查
依赖风险
权限风险
CI 风险
```

关键规则：

```text
写代码的 agent 不做最终裁判；
review agent 不直接 merge；
人类保留架构、权限、安全、上线的最终决策权。
```

---

## 十二、前后端大型工程的推荐仓库结构

如果是新项目，我建议：

```text
repo/
  apps/
    web/
      src/
      AGENTS.md
    api/
      src/
      AGENTS.md
  packages/
    shared/
      src/
      AGENTS.md
    api-client/
      src/
  docs/
    product/
    architecture/
    api/
    domain/
    qa/
    ai/
  scripts/
  .github/
  AGENTS.md
  package.json
  pnpm-workspace.yaml
```

根目录 `AGENTS.md` 写全局规则：

```text
包管理、提交规范、PR 规范、全局测试命令、禁止事项
```

`apps/web/AGENTS.md` 写前端规则：

```text
路由规范、组件规范、状态管理、样式规范、UI 测试命令
```

`apps/api/AGENTS.md` 写后端规则：

```text
API 规范、数据库访问规范、权限规则、集成测试命令
```

`packages/shared/AGENTS.md` 写共享类型规则：

```text
DTO 命名、兼容性、breaking change 流程
```

这种分层指令能降低上下文污染。Claude Code 的大型代码库指南也建议将 root 指令和子目录指令分开，让 agent 只加载当前任务相关的规则。([Claude Code][8])

---

## 十三、每个 AI coding 任务的标准模板

建议把下面模板放进 `docs/ai/task-template.md`。

````md
# AI Coding Task

## Goal
实现什么业务结果，不是简单描述代码动作。

## Background
相关业务背景、用户场景、现有实现。

## Scope
允许修改：
- ...
不允许修改：
- ...

## References
- docs/product/...
- docs/api/...
- docs/architecture/...
- 参考现有文件：...

## Requirements
1. ...
2. ...
3. ...

## Edge Cases
- 未登录
- 无权限
- 空数据
- 并发
- 超时
- 重复提交
- 数据不存在

## Acceptance Criteria
- [ ] ...
- [ ] ...
- [ ] ...

## Verification Commands
```bash
pnpm --filter api test
pnpm --filter web test
pnpm typecheck
pnpm lint
````

## Required Output

* 实现计划
* 修改文件列表
* 测试结果
* 风险点
* 后续建议

````

---

## 十四、Prompt 编排模式

### 1. 探索 prompt

```text
先不要修改代码。请阅读以下文件并总结当前实现：
- docs/architecture/system-overview.md
- apps/api/src/auth/*
- apps/web/src/routes/*

请输出：
1. 当前流程
2. 关键文件
3. 可复用模式
4. 潜在风险
5. 实现该需求需要改哪些文件
````

### 2. 计划 prompt

```text
基于刚才的探索，请制定实现计划。

要求：
- 按步骤列出
- 每步说明要改的文件
- 标出风险
- 标出测试策略
- 不要写代码
- 等我确认后再实现
```

### 3. 实现 prompt

```text
按已确认计划实现。

要求：
- 保持 PR 小而聚焦
- 遵守 AGENTS.md
- 写必要测试
- 运行验证命令
- 如果遇到不确定，不要扩大范围，先记录 blocking question
```

### 4. 自测 prompt

```text
请检查你刚才的改动。

重点：
1. 是否满足验收标准
2. 是否有未覆盖边界条件
3. 是否违反架构规则
4. 是否需要补测试
5. 是否有不必要的改动

然后运行相关测试并修复失败。
```

### 5. Review prompt

```text
请作为 reviewer 审查当前 diff。

不要直接改代码，先输出 review comments。
重点检查：
- 正确性
- 安全
- 测试覆盖
- 可维护性
- API contract
- 是否有过度设计
```

---

## 十五、AI coding 的质量指标

要管理 AI coding，必须量化，不然团队会感觉“快了但不稳”。

建议跟踪：

```text
AI PR 首次 CI 通过率
AI PR 平均 review comment 数
AI PR 返工次数
需求到合并周期
线上缺陷率
回滚次数
测试覆盖变化
架构违规次数
agent 任务一次成功率
人类介入次数
```

比较实用的判断：

```text
如果 AI 写得快但 review 成本高，说明任务边界或上下文文件不够好。
如果 AI 经常改错文件，说明目录级规则和 scope 不清楚。
如果 AI 经常漏边界条件，说明验收标准和测试模板不够好。
如果 AI 经常破坏架构，说明架构文档和 review gate 不够强。
如果 AI 经常跑不通测试，说明本地/云端环境没有标准化。
```

---

## 十六、常见错误

### 错误 1：一个超大 prompt 让 AI 做完整系统

结果通常是：

```text
结构混乱
接口不一致
测试缺失
大量隐性 bug
后期返工
```

正确做法：

```text
规格 → 架构 → contract → 垂直切片 → 小 PR 迭代
```

### 错误 2：只让 AI 写代码，不让 AI 写测试

正确做法：

```text
测试和实现同一个任务里完成；
没有测试的 AI PR 默认不合格；
关键流程先写验收测试。
```

### 错误 3：前后端并行但没有 API contract

正确做法：

```text
OpenAPI / schema 先行；
前后端都基于 contract；
contract 变更单独审批。
```

### 错误 4：AI review 代替人工 review

正确做法：

```text
AI review 是预筛选；
人 review 是最终质量责任；
敏感区域必须 code owner 审批。
```

### 错误 5：不给 AI 明确禁止事项

正确做法：

```text
禁止改 CI、secrets、auth、billing、migration、生产依赖，除非先提交计划。
```

---

## 十七、如果你用的是 Harness AI / Harness 平台

如果你这里的 “harness AI” 指的是 Harness 的 AI-native software delivery 产品，而不是泛指“利用 AI”，那它可以放在**交付编排层**：Harness 官方文档描述其 AI Code Agent 支持 IDE 内联建议、chat、生成文件、写测试、debug，并根据当前文件、相关库、历史交互和选中代码提供上下文；Harness AI 也强调运行时 awareness、IDE 工作流、多文件编辑、agentic task completion、build file generation，以及基于 commit、bug、测试失败等事件触发自动化。([Harness 开发者中心][11])

但即使用 Harness，也不要跳过上面的工程纪律。平台负责触发和自动化，你仍然需要：

```text
AGENTS.md / 项目规则
issue 拆分
API contract
测试 gate
PR review
权限边界
发布审批
```

---

## 十八、我建议你采用的最终流程

最推荐的落地流程是：

```text
第 0 步：建立 AI 工程底座
- AGENTS.md / CLAUDE.md
- docs/ai/task-template.md
- PR 模板
- CI
- 测试命令
- 禁止事项
- 子目录规则

第 1 步：AI 访谈生成 SPEC
- PRD
- 用户故事
- 边界条件
- 验收标准
- 非功能需求

第 2 步：AI 生成架构方案，人拍板
- 架构图
- ADR
- 模块边界
- 数据模型
- 权限模型

第 3 步：contract-first
- OpenAPI / schema
- DTO
- error codes
- mock
- contract tests

第 4 步：做一个垂直切片
- 一个核心页面
- 一个核心 API
- 一个实体
- DB migration
- E2E
- staging 部署

第 5 步：按 issue 小 PR 批量推进
- AI 先计划
- 人确认
- AI 实现
- AI 自测
- AI 开 PR
- AI review
- 人 review
- merge

第 6 步：集成硬化
- 安全
- 权限
- 性能
- 可观测性
- 异常处理
- 文档
- runbook

第 7 步：复盘并更新 AI 规则
- 把重复踩坑写回 AGENTS.md
- 把复杂流程沉淀为 skill/playbook
- 把高风险区域加入 review checklist
```

最重要的落点是：**先让项目变得“AI 可执行”，再让 AI 大规模写代码。**
AI-native coding 的关键不是更多 prompt，而是更强的工程约束、更清晰的上下文、更小的任务边界和更硬的验证闭环。

[1]: https://code.claude.com/docs/en/best-practices "Best practices for Claude Code - Claude Code Docs"
[2]: https://developers.openai.com/codex/cloud/environments "Cloud environments – Codex web | OpenAI Developers"
[3]: https://developers.openai.com/codex/guides/agents-md "Custom instructions with AGENTS.md – Codex | OpenAI Developers"
[4]: https://docs.anthropic.com/en/docs/claude-code/memory "How Claude remembers your project - Claude Code Docs"
[5]: https://docs.github.com/en/copilot/tutorials/roll-out-at-scale/drive-downstream-impact/accelerate-pull-requests "Accelerating pull requests in your company with GitHub Copilot - GitHub Docs"
[6]: https://github.blog/ai-and-ml/github-copilot/github-copilot-coding-agent-101-getting-started-with-agentic-workflows-on-github/?utm_source=chatgpt.com "GitHub Copilot coding agent 101: Getting started with ..."
[7]: https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/review-copilot-output "Review output from Copilot - GitHub Docs"
[8]: https://code.claude.com/docs/en/large-codebases "Set up Claude Code in a monorepo or large codebase - Claude Code Docs"
[9]: https://developers.openai.com/codex/workflows "Workflows – Codex | OpenAI Developers"
[10]: https://developers.openai.com/codex/subagents "Subagents – Codex | OpenAI Developers"
[11]: https://developer.harness.io/docs/platform/harness-ai/code-agent "Harness AI Code Agent | Harness Developer Hub"


核心结论：**AI native coding 的最优编排不是“让 AI 一次性写完整系统”，而是把软件工程改造成“规格驱动、上下文可导航、任务小颗粒、测试强约束、PR 可回滚、持续反馈”的生产线。**
需要按建设阶段区分，而且每个阶段给 AI 的角色、上下文、权限、验收标准都不同。

近期官方实践也支持这个方向：DORA 2025 把 AI 描述为组织系统的“放大器”，它会放大团队已有的工程能力和混乱度；真正的收益来自底层工程系统，而不是单个工具本身。([Dora][1]) OpenAI 的 harness engineering 实践也强调，工程师的主要工作会从“亲手写代码”转向“设计环境、表达意图、构建反馈回路，让 agent 可靠执行”。([OpenAI][2])

---

## 一、总体原则：Human steers, Agents execute

大工程里，AI 最适合做“有边界、有上下文、有自动验证”的任务。人类应该保留这些职责：

1. **定义产品目标和取舍**：什么值得做，什么不做。
2. **确定架构边界**：模块边界、数据模型、API 契约、安全要求。
3. **设计验收标准**：功能、性能、兼容性、可维护性。
4. **最终代码合并权**：尤其是核心架构、鉴权、支付、数据迁移、权限、安全相关改动。

AI 负责：

1. 拆解任务、生成计划。
2. 写测试、写实现、补文档。
3. 做局部重构。
4. 修复 CI、lint、类型错误。
5. 做初步 code review、安全 review、回归测试。
6. 根据失败日志自我修正。

也就是说，**不要把 AI 当“自由发挥的程序员”，要把它放进一个工程 harness：规则、上下文、工具、测试、权限、反馈、审查共同约束它。** Martin Fowler 对 coding agent harness 的定义也是类似思路：外层 harness 的目标是提高 agent 一次做对的概率，并提供自动纠错反馈回路，减少人工 review 负担。([martinfowler.com][3])

---

## 二、最优编排流程总览

推荐采用这个主流程：

```text
阶段 0：AI-ready 工程底座
        ↓
阶段 1：产品规格与领域建模
        ↓
阶段 2：架构设计与技术契约
        ↓
阶段 3：项目骨架与黄金路径
        ↓
阶段 4：按垂直业务切片批量实现
        ↓
阶段 5：系统集成、质量加固、安全加固
        ↓
阶段 6：发布、监控、运维、持续演进
```

最关键的变化是：**先做契约和校验，再让 AI 写代码。**
前后端复杂项目尤其应该走 **contract-first**：先定 API、数据模型、状态流、权限模型，再让前端和后端并行开发。

---

## 三、阶段 0：先建设 AI-ready 工程底座

这是最容易被忽略、但对结果影响最大的阶段。

### 0.1 建立“仓库内知识库”，不要只靠聊天记录

OpenAI 的实践里，一个重要经验是：不要把一个巨大的 `AGENTS.md` 当百科全书，而是把它当目录；真正的知识应该放在结构化的 `docs/` 目录里，作为仓库内的系统事实来源。([OpenAI][2]) Anthropic 在大代码库实践中也强调，AI 在大仓库里的能力受限于它能否找到正确上下文；过多上下文会拖累表现，过少上下文会让它盲目探索。([Claude][4])

推荐目录：

```text
/AGENTS.md
/ARCHITECTURE.md
/docs/
  product/
    PRD.md
    user-stories.md
    glossary.md
  architecture/
    system-context.md
    module-boundaries.md
    ADR-0001-tech-stack.md
    ADR-0002-auth-model.md
  api/
    openapi.yaml
    api-conventions.md
  data/
    erd.md
    migration-rules.md
  frontend/
    design-system.md
    routing.md
    state-management.md
  backend/
    service-patterns.md
    error-handling.md
    observability.md
  security/
    threat-model.md
    permission-model.md
  quality/
    test-strategy.md
    definition-of-done.md
  exec-plans/
    active/
    completed/
```

`AGENTS.md` 只放导航和硬规则，例如：

```md
# Agent Instructions

## Project Map
- Product specs: /docs/product/
- Architecture decisions: /docs/architecture/
- API contract: /docs/api/openapi.yaml
- Test strategy: /docs/quality/test-strategy.md
- Security model: /docs/security/permission-model.md

## Non-negotiable Rules
- Do not change public API without updating /docs/api/openapi.yaml.
- Do not add new dependencies without explaining why.
- Do not bypass auth/permission middleware.
- Every feature change must include or update tests.
- Run the validation commands before claiming completion.

## Validation Commands
- pnpm lint
- pnpm typecheck
- pnpm test
- pnpm test:e2e
- pnpm build
```

GitHub Copilot agent 已支持 `AGENTS.md`，也支持 `.github/copilot-instructions.md`、`.github/instructions/**.instructions.md`、`CLAUDE.md`、`GEMINI.md` 等格式；官方说明里也建议用 custom instructions 告诉 agent 如何理解项目、构建、测试和验证变更。([The GitHub Blog][5])

### 0.2 让 AI 能“看懂项目”

为 AI 准备这些能力：

| 能力                                               | 目的            |
| ------------------------------------------------ | ------------- |
| 清晰目录结构                                           | 降低上下文搜索成本     |
| `AGENTS.md` / `CLAUDE.md` / Copilot instructions | 固化项目规则        |
| OpenAPI / GraphQL schema / protobuf              | 前后端并行开发的契约    |
| 类型系统                                             | 让错误尽早暴露       |
| Lint / formatter / import rules                  | 防止风格漂移        |
| 单元测试 / 集成测试 / E2E                                | 让 AI 有自动反馈    |
| 架构边界测试                                           | 防止乱调依赖        |
| CI 必跑命令                                          | 防止“看似完成”      |
| 可本地启动的 dev 环境                                    | 让 agent 能复现问题 |
| 日志、trace、seed data                               | 让 agent 能定位问题 |

Anthropic 的大代码库建议里也提到：应使用分层的上下文文件、按子目录定义测试和 lint 命令、排除生成文件和第三方代码、必要时用代码库地图，并通过 LSP 让 agent 按符号而不是纯文本搜索。([Claude][4])

---

## 四、阶段 1：产品规格与领域建模

这一阶段不要直接让 AI 写代码。目标是让 AI 帮你把模糊需求变成可执行规格。

### AI 应做什么

让 AI 输出：

1. PRD。
2. 用户角色与权限矩阵。
3. 用户故事。
4. 业务流程图。
5. 核心实体与关系。
6. 边界场景。
7. 验收标准。
8. 非功能需求：性能、安全、可用性、审计、合规、可观测性。

### 人类必须把关什么

1. 业务目标是否正确。
2. 范围是否过大。
3. MVP 与后续版本边界。
4. 复杂度是否值得。
5. 权限和数据边界是否清楚。

### 该阶段输出物

```text
/docs/product/PRD.md
/docs/product/user-stories.md
/docs/product/glossary.md
/docs/security/permission-matrix.md
/docs/quality/acceptance-criteria.md
```

推荐 prompt：

```text
你是资深产品架构师。请基于以下业务目标，输出适合 AI coding 执行的产品规格。
要求：
1. 区分 MVP、V1、V2。
2. 每个功能必须有用户故事、前置条件、主流程、异常流程、验收标准。
3. 标记不确定问题，不要自行假设。
4. 输出实体、状态机、权限矩阵。
5. 最后给出可拆分的工程 epic 列表。
```

---

## 五、阶段 2：架构设计与技术契约

这是前后端大工程能否顺利 AI 化的关键。

### 最优策略：先 contract，再代码

前后端不要各写各的。先让 AI 协助生成：

1. API 契约：OpenAPI / GraphQL schema。
2. 数据库模型。
3. DTO / request / response schema。
4. 错误码规范。
5. 鉴权与权限规则。
6. 前端路由和页面状态模型。
7. 后端模块边界。
8. 异步任务、消息队列、缓存策略。
9. 日志和审计字段。

Harness AI Code Agent 官方文档也把 API spec generation 作为能力之一，可以基于 API 路由、controller、数据模型生成 OpenAPI/Swagger 规格；这类能力适合用于“契约生成和维护”，但仍需要人工 review。([Harness 开发者中心][6])

### 该阶段输出物

```text
/docs/architecture/system-context.md
/docs/architecture/module-boundaries.md
/docs/architecture/ADR-0001-tech-stack.md
/docs/api/openapi.yaml
/docs/data/schema.md
/docs/security/authz-model.md
```

### 人类决策点

这里不要让 AI 自动拍板。必须人工确认：

1. 技术栈。
2. 单体、模块化单体、微服务的选择。
3. 数据库设计。
4. 认证授权模型。
5. 多租户模型。
6. 关键第三方服务。
7. 部署架构。
8. 是否需要事件驱动、队列、缓存。
9. 安全边界。

推荐原则：**AI 给 2–3 个方案和权衡，人类选一个并写入 ADR。**

---

## 六、阶段 3：项目骨架与黄金路径

不要一开始就全量开发。先让 AI 建一个“黄金路径”，也就是一个最小但完整的端到端业务闭环。

例如：

```text
注册/登录
  → 创建一个资源
  → 后端保存
  → 前端展示
  → 权限校验
  → 日志记录
  → 测试覆盖
  → CI 通过
  → 可部署
```

这个阶段的目标不是功能多，而是建立标准模式。

### AI 应完成

1. monorepo 或多 repo 结构。
2. 前端框架初始化。
3. 后端框架初始化。
4. 数据库 migration。
5. API contract。
6. typed client。
7. auth 基础能力。
8. 基础 UI layout。
9. 单元测试、集成测试、E2E smoke test。
10. CI pipeline。
11. Docker / deployment scaffold。
12. 日志、错误处理、健康检查。

### 推荐骨架

```text
/apps/
  web/
  api/
/packages/
  ui/
  shared/
  domain/
  config/
/infra/
  docker/
  terraform/
/tests/
  e2e/
  fixtures/
/docs/
  ...
```

### 关键验收标准

```text
pnpm install
pnpm lint
pnpm typecheck
pnpm test
pnpm test:e2e
pnpm build
docker compose up
```

全部可跑通后，再进入批量功能开发。

---

## 七、阶段 4：批量功能开发，用“垂直切片”编排

复杂前后端项目不要按“先全后端、再全前端”开发，也不要让 AI 一次做一个巨大 epic。最优方式是 **vertical slice**：

```text
一个业务功能 =
  数据模型变更
  + API 契约
  + 后端实现
  + 前端页面/组件
  + 权限
  + 测试
  + 文档
```

每个切片独立成 PR。

### 单个 feature 的最佳执行循环

```text
1. 人类/AI 生成 feature spec
2. AI 生成 execution plan
3. 人类确认高风险点
4. AI 先写或更新测试
5. AI 实现后端
6. AI 实现前端
7. AI 做集成联调
8. AI 运行 lint/typecheck/test/build
9. AI 自审
10. 独立 reviewer agent 审查
11. 人类最终 review
12. merge
13. 失败经验回写 AGENTS.md / docs / lint 规则
```

GitHub Copilot cloud agent 的官方说明也支持类似工作方式：它可以研究仓库、创建实现计划、修 bug、实现增量功能、提升测试覆盖、更新文档，并在 GitHub Actions 驱动的临时开发环境中运行测试和 linters。([GitHub Docs][7]) 但官方也指出它一次只能在一个 repo、一个 branch 上工作，并且每个任务有执行时长限制；复杂任务应拆成更小、更聚焦的任务。([GitHub Docs][7])

### 前后端并行方式

最佳顺序：

```text
API contract / schema 先定
        ↓
后端 agent 实现 API
前端 agent 基于 typed client / mock 实现 UI
        ↓
integration agent 联调
        ↓
test/review agent 验证
```

不要让前端 agent 和后端 agent 同时修改同一个 contract 文件。契约文件应该由“contract owner”或 architecture agent 修改，其他 agent 只消费。

---

## 八、推荐的 Agent 角色编排

不一定需要真的创建多个工具账号；可以是不同 prompt、不同 custom agent、不同 workflow。GitHub 官方文档也提到可以创建 specialized custom agents，例如前端、文档、测试等不同任务类型。([GitHub Docs][7])

| Agent 角色           | 主要职责                          | 不应做什么      |
| ------------------ | ----------------------------- | ---------- |
| Product Spec Agent | PRD、用户故事、验收标准                 | 不直接写代码     |
| Architect Agent    | 架构方案、ADR、模块边界                 | 不绕过人工架构决策  |
| Contract Agent     | OpenAPI、DTO、错误码、schema        | 不随意破坏兼容性   |
| Backend Agent      | service、controller、DB、job     | 不改前端 UI 逻辑 |
| Frontend Agent     | 页面、组件、状态、表单                   | 不改后端业务规则   |
| Test Agent         | unit/integration/e2e、fixtures | 不为实现妥协测试   |
| Review Agent       | code review、边界、安全、性能          | 不直接合并      |
| Security Agent     | 鉴权、输入校验、依赖风险                  | 不替代正式安全流程  |
| DevOps Agent       | CI/CD、部署、日志、告警                | 不改业务逻辑     |
| Doc Agent          | 更新 docs、ADR、runbook           | 不制造过时文档    |

对于 Harness 平台用户，可以把 IDE 里的 Harness AI Code Agent 用于代码生成、测试生成、解释和局部修改；它支持 `@Codebase`、`@File`、`@Search` 等上下文提供方式。([Harness 开发者中心][6]) 而 Harness Worker Agents 更适合放进 CI/CD pipeline，作为可治理的步骤运行，例如 PR review、pipeline failure summarizer、compliance check、incident response 等；官方定义中，Worker Agent 是运行在 Harness pipeline 里的 AI 自动化单元，可组合 prompt、model connector 和 MCP server。([Harness 开发者中心][8])

---

## 九、每个任务都应该用这个 Task Card

这是让 AI 输出稳定的关键模板：

```md
# Task: 实现组织成员邀请功能

## Goal
允许 organization owner 通过邮箱邀请新成员加入组织。

## Business Context
见 /docs/product/team-management.md

## Scope
- 新增 invite API
- 新增 invite 表
- 前端新增邀请弹窗
- 邮件发送先用 mock provider
- 添加权限校验

## Out of Scope
- 不实现真实邮件服务
- 不实现批量导入
- 不改 billing 逻辑

## Relevant Files
- /docs/api/openapi.yaml
- /docs/security/permission-matrix.md
- /apps/api/src/modules/orgs/
- /apps/web/src/features/orgs/

## Contract
- POST /orgs/{orgId}/invites
- GET /orgs/{orgId}/invites
- DELETE /orgs/{orgId}/invites/{inviteId}

## Acceptance Criteria
- owner 可以邀请
- admin 可以邀请
- member 不可以邀请
- 重复邮箱返回明确错误
- invite 过期时间为 7 天
- 所有 API 有测试
- 前端表单有 loading/error/success 状态

## Validation Commands
- pnpm lint
- pnpm typecheck
- pnpm test -- org-invite
- pnpm test:e2e -- invite-flow
- pnpm build

## Risks
- 权限绕过
- 重复邀请
- 邮箱大小写归一化
- 多租户数据隔离

## Do Not
- 不要引入新邮件服务依赖
- 不要修改 auth middleware 行为
- 不要跳过 OpenAPI 更新
```

好的 AI coding 任务应该满足：**目标单一、边界清楚、输入文件明确、验收标准可测试、验证命令可运行。**

---

## 十、质量门禁：AI 代码必须自动证明自己

大工程里，AI 生成代码不是问题，**验证成本**才是问题。必须把质量门禁前移。

### 必备 gate

```text
1. format
2. lint
3. typecheck
4. unit test
5. integration test
6. contract test
7. e2e smoke test
8. build
9. dependency audit
10. secret scan
11. migration check
12. architecture boundary check
13. docs updated check
```

OpenAI 的 harness engineering 实践也提到，随着 agent 写码速度提高，需要用自定义 lint、结构测试和规则把架构与风格约束机械化；当规则可以编码时，应把 review 反馈沉淀成文档或工具规则，而不是靠人反复提醒。([OpenAI][2])

推荐增加几类 agent-friendly 检查：

```text
- 禁止跨层 import：frontend 不可直接引用 backend internals
- API breaking change 检查
- DB migration backward compatibility 检查
- 日志字段检查：requestId、userId、tenantId
- 权限测试覆盖检查
- 文件大小限制
- cyclomatic complexity 限制
- 禁止 TODO without issue link
- 禁止 any / ts-ignore 无说明
```

---

## 十一、不同阶段的 AI 自主权应该不同

| 阶段     | AI 自主权 | 人类控制点         |
| ------ | -----: | ------------- |
| 需求探索   |      中 | 产品目标、范围、优先级   |
| 架构设计   |      低 | 架构选型、边界、ADR   |
| 骨架搭建   |      中 | 技术栈、目录、CI     |
| 普通功能开发 |      高 | PR review、验收  |
| 高风险功能  |      低 | 安全、支付、权限、数据迁移 |
| 测试补齐   |      高 | 测试策略          |
| 重构     |     中低 | 行为兼容、回滚方案     |
| 运维脚本   |      中 | 权限、环境隔离       |
| 文档维护   |      高 | 核心事实准确性       |

高风险功能包括：

```text
鉴权
权限
支付
数据迁移
多租户隔离
加密
审计
外部系统集成
删除数据
批量任务
生产部署
```

这些任务可以让 AI 写草稿，但必须有人类架构师或 senior engineer 审查。

---

## 十二、推荐的 PR 编排策略

每个 PR 只做一个行为变化。不要让 AI 提交“史诗级 PR”。

推荐 PR 类型：

```text
type: contract
type: backend
type: frontend
type: integration
type: test
type: refactor
type: docs
type: infra
type: fix
```

推荐节奏：

```text
PR 1: 新增 API contract + mock
PR 2: 后端实现 + 后端测试
PR 3: 前端实现 + 组件测试
PR 4: E2E + 集成修复
PR 5: observability + docs
```

对于复杂 feature，不要一个 PR 同时做完所有事。AI 速度快，但人类 review 带宽有限；小 PR 更容易发现问题，也更容易回滚。

---

## 十三、建设阶段的具体最优流程

### 阶段 A：从 0 到 MVP

重点：速度与结构并重。

```text
1. PRD
2. 架构 ADR
3. OpenAPI / schema
4. 项目 scaffold
5. 黄金路径
6. 3–5 个核心垂直切片
7. CI/CD
8. 基础监控
9. 内测
```

AI 主要做：

```text
- 生成 scaffold
- 生成 API 和 types
- 写 CRUD 和页面
- 写测试
- 修 CI
- 补文档
```

人类主要做：

```text
- 定义 MVP 边界
- 审架构
- 审数据模型
- 审权限
- 做体验验收
```

### 阶段 B：从 MVP 到 V1

重点：稳定性、可维护性、权限、安全、真实业务复杂度。

```text
1. 补齐边界场景
2. 强化权限模型
3. 增加 E2E 覆盖
4. 加入 feature flag
5. 完善日志、metrics、trace
6. 增加错误处理标准
7. 增加后台任务和重试机制
8. 性能基线测试
```

AI 主要做：

```text
- 补测试
- 查找未处理异常
- 生成 migration 脚本
- 生成 observability code
- 做局部重构
- 根据 bug report 修复
```

### 阶段 C：从 V1 到生产级

重点：可靠性、发布治理、审计、可观测性。

```text
1. 灰度发布
2. 回滚方案
3. 数据备份与恢复演练
4. 安全扫描
5. dependency audit
6. load test
7. runbook
8. incident playbook
9. SLO / alert
```

AI 主要做：

```text
- 生成 runbook
- 总结 CI/CD 失败
- 生成 release note
- 分析日志和 trace
- 生成安全 review checklist
- 自动补充回归测试
```

### 阶段 D：长期演进

重点：防止熵增。

```text
1. 每周 doc-gardening
2. 每周 tech debt triage
3. 每月 dependency update
4. 每月 architecture drift review
5. 每次事故后回写测试和规则
6. 每次人工 review 重复意见沉淀为 lint / instruction
```

Anthropic 也建议大型团队维护 agent 配置本身，包括定期 review 上下文文件、skills、hooks；因为模型能力和工具会演进，旧指令可能变成负担。([Claude][4])

---

## 十四、最推荐的实际工作流

日常开发可以这样跑：

```text
Backlog item
   ↓
AI 生成 task card
   ↓
人类确认边界
   ↓
AI 生成 plan，不写代码
   ↓
人类确认 plan，或者对低风险任务自动通过
   ↓
AI 写测试
   ↓
AI 写实现
   ↓
AI 本地运行验证命令
   ↓
AI 自审并修复
   ↓
Reviewer Agent 审查
   ↓
CI
   ↓
人类 review
   ↓
merge
   ↓
经验回写 docs / AGENTS.md / lint
```

对于低风险任务，可以把人类确认 plan 这一步弱化；对于高风险任务，必须保留。

---

## 十五、你可以直接采用的“AI 编排看板”

建议把任务分成这些泳道：

```text
Discovery
  需求澄清、方案探索

Spec Ready
  已有验收标准、上下文、边界

Contract Ready
  API/schema/数据模型已确认

AI Implementing
  AI 正在实现

AI Self-Review
  AI 自测、自审、修复

Human Review
  人类审查

Integration
  联调、E2E、回归

Release Ready
  可发布

Post-release Learning
  线上反馈、经验沉淀
```

每个任务进入 `AI Implementing` 前，必须有：

```text
- 明确目标
- 相关 docs
- 涉及路径
- 不做什么
- 验收标准
- 验证命令
```

---

## 十六、最容易失败的模式

这些要避免：

1. **一句话让 AI 写完整系统**
   结果通常是 demo 能跑，工程不可维护。

2. **没有 API contract 就前后端并行**
   后期联调成本会爆炸。

3. **把所有规则塞进一个超长 prompt**
   上下文会稀释，规则会过期。OpenAI 和 Anthropic 的实践都更推荐短入口文件 + 分层文档。([OpenAI][2])

4. **多个 agent 同时改同一批核心文件**
   容易冲突、重复实现、破坏架构。

5. **没有测试就让 AI 大量产码**
   人类 review 会成为瓶颈。

6. **把知识放在聊天记录、飞书、Notion，而不是 repo**
   Agent 运行时看不到的知识，等于不存在；OpenAI 的实践也强调仓库内、版本化的 artifacts 更适合 agent 使用。([OpenAI][2])

7. **只检查代码风格，不检查业务行为**
   lint 通过不代表系统正确。

8. **不把失败沉淀成规则**
   同样问题会重复出现。

---

## 十七、最终推荐架构：三层 AI coding harness

你可以把整套系统理解成三层。

### 第 1 层：Context Harness

解决“AI 知不知道该怎么做”。

```text
AGENTS.md
docs/
ADR
OpenAPI
schema
test strategy
permission matrix
codebase map
```

### 第 2 层：Execution Harness

解决“AI 能不能安全地做”。

```text
branch per task
worktree
local dev env
seed data
mock services
MCP
LSP
sandbox permissions
task card
execution plan
```

GitHub Copilot cloud agent、Claude Code、Harness Code Agent、Codex/Cursor 等工具都可以放在这一层。GitHub 官方文档中也提到可通过 custom instructions、MCP servers、custom agents、hooks、skills 等方式增强 agent 的上下文、工具和验证能力。([GitHub Docs][7])

### 第 3 层：Verification Harness

解决“AI 做完以后是否真的对”。

```text
lint
typecheck
unit test
integration test
contract test
e2e
security scan
dependency audit
architecture test
migration test
review agent
human review
production monitoring
```

---

## 十八、给你的最优落地顺序

建议按这个顺序实施：

```text
第 1 步：建立 docs/ + AGENTS.md
第 2 步：确定技术栈和架构 ADR
第 3 步：建立 OpenAPI/schema/权限矩阵
第 4 步：搭建 scaffold + CI + 测试框架
第 5 步：做一个黄金路径
第 6 步：按 vertical slice 批量开发
第 7 步：引入 reviewer agent 和 security agent
第 8 步：把重复 review 意见变成 lint / docs / hooks
第 9 步：把 AI 扩展到 CI/CD、测试、发布、运维
第 10 步：每 2–4 周做一次 harness review
```

最重要的一句话：**不要追求“AI 一次写完”，要追求“AI 每次只改一个清晰切片，并且能自动证明这个切片是正确的”。**

[1]: https://dora.dev/dora-report-2025/ "DORA | State of AI-assisted Software Development 2025"
[2]: https://openai.com/index/harness-engineering/ "Harness engineering: leveraging Codex in an agent-first world | OpenAI"
[3]: https://martinfowler.com/articles/harness-engineering.html "Harness engineering for coding agent users"
[4]: https://claude.com/blog/how-claude-code-works-in-large-codebases-best-practices-and-where-to-start "How Claude Code works in large codebases: Best practices and where to start | Claude"
[5]: https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/ "Copilot coding agent now supports AGENTS.md custom instructions - GitHub Changelog"
[6]: https://developer.harness.io/docs/platform/harness-ai/code-agent "Harness AI Code Agent | Harness Developer Hub"
[7]: https://docs.github.com/copilot/concepts/agents/coding-agent/about-coding-agent "About GitHub Copilot cloud agent - GitHub Docs"
[8]: https://developer.harness.io/docs/platform/harness-ai/harness-agents "Worker Agents | Harness Developer Hub"
