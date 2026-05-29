# AI-Native Software Engineering & Coding Guide

不要让 AI 一句话生成全栈系统，而要将其编排成受约束的工程单元：**先规划再小批量实现，依赖项目规则、自动化验证与 PR 门禁。**

瓶颈通常不在于模型是否能编写代码，而在于上下文管理、任务粒度划分、验收标准定义和自动化反馈回路。核心原则是：
> **不要编排 Prompt，编排工程系统：需求文档、架构约束、任务边界、上下文文件、测试信号、PR 流程与自动化门禁。**

根据 DORA 2025 报告与行业实践，AI 是组织工程能力的放大器。真正的效率与质量收益来自于底层的软件工程约束（Harness），而非工具本身。工程师的角色已从单纯的“编写代码”转变为“设计环境、表达意图、构建反馈回路并监督可靠执行”。

---

## 一、 总体原则：人类领航，AI 执行 (Human Steers, Agents Execute)

在大型复杂工程中，AI 应当作为“有边界、有上下文、有自动验证”的工程执行层，人类则负责核心决策与最终责任：

1. **人类的核心职责**：
   - **定义业务目标与优先级**：明确什么值得做，什么不做。
   - **确定架构边界与关键技术取舍**：设计模块边界、核心数据模型、API 契约与安全模型。
   - **设计验收标准**：定义功能、性能、兼容性与安全指标。
   - **最终代码合并权与发布审批**：尤其是涉及鉴权、支付、数据迁移、CI/CD 配置与安全规则的改动。
2. **AI 的核心职责**：
   - **拆解任务与生成执行计划**。
   - **编写测试、编写实现、补充文档**。
   - **执行局部重构**，修复 CI、Lint 与类型检查错误。
   - **运行自动化测试与验证脚本**，基于错误日志进行自我修复。
   - **进行初步的 Code Review 与安全审计**。

---

## 二、 三层 AI 编码约束网 (Three-Layer AI Coding Harness)

为保证 AI 产出的代码具备生产级质量，必须构建由规则、环境和门禁组成的约束系统：

```
┌────────────────────────────────────────────────────────┐
│ 1. 知识与上下文约束 (Context Harness)                  │
│    AGENTS.md / 架构文档 / API 契约 / 安全矩阵 / 类型定义│
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│ 2. 执行与沙盒约束 (Execution Harness)                  │
│    Task Card / 独立分支与 Worktree / 本地开发环境 / MCP │
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│ 3. 验证与门禁约束 (Verification Harness)               │
│    自动测试 / Lint / 静态分析 / 安全扫描 / 人工 Review │
└────────────────────────────────────────────────────────┘
```

1. **第一层：知识与上下文约束 (Context Harness)**
   - 解决 AI **“知不知道该怎么做”**。
   - 仓库内包含 AI 可读的项目说明，避免在聊天中重复输入。
   - 包含根目录 `AGENTS.md` (或 `CLAUDE.md`) 提供全局规则，并在子目录使用特定 `AGENTS.md` 进行规则隔离。
   - 提供结构化的 `docs/` 目录作为版本化的系统事实来源 (ROM)。
2. **第二层：执行与沙盒约束 (Execution Harness)**
   - 解决 AI **“能不能安全、受控地做”**。
   - 采用**单任务单分支/Worktree**的开发模式，限制 AI 编辑范围。
   - 提供标准化的 **Task Card**、本地开发环境、Seed 数据与 Mock 服务。
3. **第三层：验证与门禁约束 (Verification Harness)**
   - 解决 AI **“做完以后是否真的对”**。
   - 构建机器验证信号：单元测试、集成测试、E2E 测试、类型检查、Lint、契约测试、安全扫描与人工双签 review 机制。

---

## 三、 AI 原生软件开发生命周期 (8-Stage AI-Native SDLC)

大型前后端工程的 AI 原生编排应分为以下 8 个阶段，重点是**先契约与校验，后代码开发**：

```
阶段 0: 工程底座 ──> 阶段 1: 需求规格 ──> 阶段 2: 架构与契约 ──> 阶段 3: 项目骨架/黄金路径
                                                                        │
                                                                        ▼
阶段 7: 发布与复盘 <── 阶段 6: 质量加固 <── 阶段 5: 小批量功能 PR <── 阶段 4: 垂直业务切片
```

### 阶段 0：AI-Ready 工程底座
- **目标**：AI 能够自动拉取依赖、正常构建并运行测试。
- **职责**：人类定义仓库结构与环境规范，AI 协助生成项目规则、测试指令、CI 模板与 PR 模板。
- **产物**：`AGENTS.md`、`docs/ai/*`、测试框架骨架、CI 工作流配置。
- **Gate**：AI 能在本地沙盒一键运行 `install`、`build`、`test`。

### 阶段 1：产品规格与领域建模
- **目标**：将模糊的产品业务目标转化为可供 AI 执行的清晰技术规格。
- **职责**：人类确定业务取舍，AI 作为“需求澄清者”进行访谈式提问，补充边界条件并输出 Acceptance Criteria (AC)。
- **产物**：`PRD.md`、`user-stories.md`、权限控制矩阵 (`permission-matrix.md`)、验收测试用例。
- **Gate**：需求完全转化为可独立验证的单元与场景。

### 阶段 2：架构设计与技术契约
- **目标**：确立系统边界，通过 contract-first 规范实现前后端并行开发而不冲突。
- **职责**：人类决策关键技术选型（如 DB 设计、认证模型），AI 提供方案对比与 ADR (架构决策记录) 草稿。
- **产物**：API 契约 (`openapi.yaml` 或 GraphQL schema)、DTO 类型文件、数据库模型 schema、错误码定义。
- **Gate**：契约文档单独提交 PR 且通过人类审核。

### 阶段 3：项目骨架与黄金路径
- **目标**：建立项目基础架构标准，形成“可模仿的正确编写模式”。
- **职责**：AI 初始化 monorepo/项目骨架，打通一个最小的端到端“黄金路径”（如：登录 -> 创建实体 -> 存库 -> 前端展示 -> 权限校验 -> E2E 测试）。
- **产物**：API client 生成器、Docker Compose 环境、基础 UI layout、E2E smoke test。
- **Gate**：本地黄金路径测试 100% 通过，CI 管道跑通。

### 阶段 4：垂直切片开发 (Vertical Slice)
- **目标**：避免在大系统里“局部正确、整体不可用”，以业务功能为单位纵向贯穿开发。
- **职责**：AI 纵向实现一个包含数据表变更、API、后端逻辑、前端组件、权限与测试的切片。
- **产物**：端到端闭环的功能切片。
- **Gate**：该切片的集成与 E2E 测试全部通过。

### 阶段 5：小批量功能 PR
- **目标**：按任务卡小步快跑，每个 PR 只做一个行为变化。
- **职责**：AI 生成实现计划，人类确认后，AI 先写测试，再写实现，自测通过后提 Draft PR。
- **产物**：功能 PR、配套的自动化测试、文档更新。
- **Gate**：CI 自动门禁（Lint / Typecheck / Test）全部通过 + 人类 Review。

### 阶段 6：质量加固与多角色审查
- **目标**：补齐非功能性缺陷，使系统达到生产级标准。
- **职责**：调用不同角色定位的 Agent 进行对抗性审查（安全 Agent、性能 Agent、测试覆盖 Agent），人类作为最终把关人。
- **产物**：安全扫描报告、性能 smoke 报告、Runbook 与操作指南。
- **Gate**：Staging 环境自动化回归测试与人工体验验收通过。

### 阶段 7：发布、运维与持续演进
- **目标**：安全发布，并在运行中防止架构和规则发生熵增。
- **职责**：AI 编写 Release notes、分析异常日志、总结事故 postmortem；人类审核并发布到生产环境。
- **产物**：Changelog、生产监控告警策略、规则更新 (回写 `AGENTS.md`)。
- **Gate**：发布后 Canary 检查无异常。

---

## 四、 推荐的 Agent 角色分工

为了避免多个 Agent 乱改冲突或上下文过度稀释，应当按职责进行角色分工，并限制其在不同阶段的自主权：

| Agent 角色 | 主要职责 | 禁止事项 | 阶段自主权 |
| :--- | :--- | :--- | :--- |
| **Product Spec Agent** | PRD、用户故事编写、边界场景发掘、验收标准生成 | 不直接编写业务代码 | 中 |
| **Architect Agent** | 模块边界定义、ADR 起草、技术选型比较、风险分析 | 不得绕过人工架构决策 | 低 |
| **Contract Agent** | 维护 API Schema、数据库 Model、共享 DTO、Mock | 不得随意破坏向前兼容性 | 低 |
| **Backend Agent** | 实现 Service 逻辑、数据库操作、后台任务与单元测试 | 不修改前端 UI 与样式 | 高 |
| **Frontend Agent** | 实现 UI 页面、组件交互、前端状态管理与组件测试 | 不直接修改后端核心业务契约 | 高 |
| **Test / QA Agent** | 编写单元测试、集成测试、E2E 测试与回归测试用例 | 不得为了通过测试而妥协断言 | 高 |
| **Review / Security Agent** | 对代码进行静态审查、漏洞检测、权限绕过与依赖分析 | 不得直接合并代码或修改 PR | 中 |
| **DevOps / Infra Agent** | 维护 CI/CD 配置、部署脚本、日志格式与监控告警 | 不得在 CI 配置文件中硬编码 Secrets | 中 |

### AI 自主权管控细则：
- **高风险区域**（鉴权、权限、支付、数据迁移、多租户隔离、删除数据）：AI 仅能起草技术方案和代码，**必须**由人类架构师 100% 审查。
- **低风险区域**（普通 CRUD 功能、测试补齐、文档同步、样式微调）：在自动化门禁通过的前提下，可给予 AI 较高的执行自主权。

---

## 五、 标准化任务驱动执行 (Task-Driven Workflow)

AI 原生开发中，每次对话和代码变更应当严格遵循标准流程：

### 1. 任务最小单元：Task Card
任何委托给 AI 执行的任务必须满足：**目标单一、边界清楚、上下文明确、验证命令可运行**。任务卡模板请参考 [docs/07_sop/templates/task-card.md](file:///Users/chenguangwei/Documents/workspace/med-ai-native-project-demo/docs/07_sop/templates/task-card.md)。

### 2. 单个任务的最佳执行循环 (Interactive Loop)
1. **探索 (Explore)**：AI 先读取上下文和相关文档，摸清现有代码的可复用模式。
2. **计划 (Plan)**：AI 制定详细的实现步骤，列出需要修改的文件、测试策略和潜在风险。**人类确认计划后方可开始编码**。
3. **实现 (Implement)**：AI 先写测试（TDD 模式），再编写最小实现。
4. **验证 (Verify)**：AI 在沙盒中运行 `Lint`、`Typecheck`、`Test` 和 `Build`，并依据 stdout 的报错日志自动进行修复。
5. **审查 (Review)**：调用 Reviewer Agent 审查代码质量与安全性，人类人类确认最终 Diff 并合并。
6. **交接 (Handoff)**：将当前进度、踩坑点和下一步指示写入 `memory/handoff.md`。

### 3. Step-by-Step Prompt 编排模式

在日常交互中，不要一次性喂给 AI 所有要求，分步骤向 AI 发送以下 Prompt 指令：

*   **步骤 1：探索阶段**
    ```text
    请先不要修改任何代码。请阅读以下文件并总结当前的实现逻辑：
    - [涉及文件/文档 1]
    - [涉及文件/文档 2]
    
    请输出：
    1. 当前的业务流程与关键入口
    2. 可以参考和复用的设计模式
    3. 潜在的隐性风险或兼容性问题
    4. 实现此需求需要创建或修改哪些文件
    ```
*   **步骤 2：计划阶段**
    ```text
    基于刚才的探索，请制定实现计划。
    要求：
    - 拆解为可单独提交的微小步骤
    - 明确每一步需要修改的文件与理由
    - 标明具体的测试与验证策略
    - 不要写任何实现代码，等我确认计划后再动手
    ```
*   **步骤 3：实现与自测阶段**
    ```text
    请按已确认的计划开始实现。
    要求：
    - 遵循 TDD 规范，先编写或补齐对应的测试代码
    - 保证修改范围被限制在 Task Card 约定的范围内
    - 实现后，在本地终端运行验证命令：[填入验证命令，如 pnpm test]
    - 确认所有测试通过，并输出测试结果的 Summary 和最终修改文件的 Diff
    ```
*   **步骤 4：Review 阶段**
    ```text
    请作为 Senior Reviewer Agent 审查当前的修改。
    重点检查：
    1. 业务逻辑正确性与边界处理（空值、并发、异常）
    2. 安全性（是否存在鉴权漏洞、注入漏洞、数据泄露）
    3. 代码坏味道与反 AI 味（是否有无意义的注释、过度抽象）
    4. API 契约与数据库 Schema 兼容性
    ```

---

## 六、 质量门禁与管理指标 (Gates & Metrics)

工程的稳定性不依赖人的细心，而依赖机械化的反馈信号。

### 1. 自动化质量门禁 (Mandatory Gates)
- **静态规则**：Format、Lint、Typecheck 无 error，禁止使用无解释的 `any` 或 `ts-ignore`。
- **架构规则**：禁止跨层引用（如前端组件直调后端底层逻辑），文件大小与圈复杂度控制在阈值内。
- **测试门禁**：单元测试通过、API 契约测试通过、E2E 冒烟测试通过。
- **发布规则**：数据库 Migration 可逆，配置 Secrets 必须在环境变量中，禁止明文提交。

### 2. 核心管理指标 (AI Coding KPI)
为管理 AI Coding 团队的健康度，建议跟踪以下指标以评估 Scaffolding 的有效性：
- **AI PR 首次 CI 通过率**：过低说明本地验证命令与 CI 命令不一致，或 AI 未运行验证。
- **平均人介入 Review 修改数**：过高说明项目的 `AGENTS.md` 规则或架构文档表达不清晰。
- **缺陷溢出率 (Canary/Production)**：由于边界处理不当导致线上故障的比例。
- **任务一次成功率**：AI 执行任务时不需要反复重试的比例。

---

## 七、 常见错误与避坑指南 (Anti-Patterns)

1. **一句话让 AI 生成完整系统**
   - *现象*：输入 "做一个包含注册、支付和后台管理的 SaaS 系统"。
   - *后果*：代码结构混乱、存在大量隐性 Bug、缺乏测试、后期重构成本极其高昂。
   - *避坑*：严格执行“规格 -> 架构 -> 契约 -> 黄金路径 -> 垂直切片”的演进路线。
2. **只写实现不写测试**
   - *现象*：AI 写完了功能逻辑，人直接手动点点看就算完成。
   - *后果*：缺乏测试保护的代码在下一轮 AI 修改时会被轻易破坏，人肉 Review 成为灾难性的效率瓶颈。
   - *避坑*：无测试不合并，TDD 规范强制化。
3. **缺少 API 契约就并行开发**
   - *现象*：前端和后端各自开发，联调时发现字段对不上。
   - *后果*：前后端代码大量推倒重来，造成无谓的 Token 消耗和时间浪费。
   - *避坑*：契约先行，DTO 与 API Client 均由 Schema 自动化生成。
4. **将知识保留在代码库外部**
   - *现象*：项目的核心规范和踩坑记录写在飞书、Notion 或聊天记录中。
   - *后果*：AI 无法获取外部知识，反复犯相同的低级错误。
   - *避坑*：所有系统事实、SOP 与避坑经验必须在代码库内落盘（如 `docs/` 和 `memory/project-facts.md`）。

---

## 八、 最推荐的实际落地顺序

建议接入 AI 原生脚手架的项目，按以下顺序逐步建立约束网：

```
第 1 步：搭建 docs/ 结构与编写全局 AGENTS.md / CLAUDE.md
       ↓
第 2 步：锁定核心技术栈，编写关键模块的 ADR 架构决策
       ↓
第 3 步：定义 API Contract (OpenAPI) 与权限矩阵
       ↓
第 4 步：初始化项目骨架，配置 Lint、Typecheck、CI 与测试框架
       ↓
第 5 步：打通端到端的黄金路径 (Golden Path)
       ↓
第 6 步：拆解 Task Card，开始垂直切片批量开发
       ↓
第 7 步：引入专门的 Reviewer/Security Agent 预检 PR
       ↓
第 8 步：总结 review 反馈，将其提炼并沉淀为 Lint 规则或项目文档
```

---

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
[12]: https://dora.dev/dora-report-2025/ "DORA | State of AI-assisted Software Development 2025"
[13]: https://openai.com/index/harness-engineering/ "Harness engineering: leveraging Codex in an agent-first world | OpenAI"
[14]: https://martinfowler.com/articles/harness-engineering.html "Harness engineering for coding agent users"
[15]: https://claude.com/blog/how-claude-code-works-in-large-codebases-best-practices-and-where-to-start "How Claude Code works in large codebases: Best practices and where to start | Claude"
[16]: https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/ "Copilot coding agent now supports AGENTS.md custom instructions - GitHub Changelog"
[17]: https://developer.harness.io/docs/platform/harness-ai/harness-agents "Worker Agents | Harness Developer Hub"
