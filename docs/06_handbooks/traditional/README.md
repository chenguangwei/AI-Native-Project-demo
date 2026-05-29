# 👥 团队协作手册（过渡期版）

> 适用于传统研发团队向 AI-Native 过渡阶段。按原有岗位职责划分，人人配备 AI 助手，协作方式不变，效率倍增。
>
> **过渡路径**：传统岗位 + AI 工具 → AI-Native 岗位体系（参见 [ROLE_SETUP.md](ROLE_SETUP.md)）

---

## 开始前先选流程

| 场景 | 先读 |
|------|------|
| 团队协作主流程 | [../../07_sop/team.md](../../07_sop/team.md) |
| 按岗位/语言栈找手册 | [../../07_sop/roles/README.md](../../07_sop/roles/README.md) |
| 交给 AI 执行任务 | [../../07_sop/templates/task-card.md](../../07_sop/templates/task-card.md) |
| 接口或 DB 契约变化 | [../../07_sop/templates/contract-change-proposal.md](../../07_sop/templates/contract-change-proposal.md) |

---

## 团队结构与角色配置

```
┌─────────────────────────────────────────────────────────┐
│                      产品团队                            │
│  产品经理 (PM)    ←→   设计师                            │
│  定义"做什么"          定义"怎么看"                       │
└──────────────────────────┬──────────────────────────────┘
                           │ PRD + 设计稿
┌──────────────────────────▼──────────────────────────────┐
│                      研发团队                            │
│  前端工程师           后端工程师                          │
│  负责 UI/交互         负责 API/数据/服务                   │
└──────────────────────────┬──────────────────────────────┘
                           │ 可测试版本
┌──────────────────────────▼──────────────────────────────┐
│                      测试团队                            │
│  测试工程师 (QA)                                         │
│  验收功能、保障质量                                       │
└──────────────────────────┬──────────────────────────────┘
                           │ 通过测试的版本
┌──────────────────────────▼──────────────────────────────┐
│                      运维团队                            │
│  运维工程师 (DevOps)                                     │
│  部署上线、监控运营                                       │
└─────────────────────────────────────────────────────────┘
```

---

## 角色配置速查

各岗位在 `.claude/project-config.json` 中设置对应角色值：

| 岗位 | currentRole 值 | Memory 目录 |
|------|---------------|-------------|
| 产品经理 | `product-owner` | `memory/roles/product-owner/` |
| 前端工程师 | `delivery-engineer` | `memory/roles/delivery-engineer/` |
| 后端工程师 | `delivery-engineer` | `memory/roles/delivery-engineer/` |
| 测试工程师 | `quality-engineer` | `memory/roles/quality-engineer/` |
| 运维工程师 | `delivery-engineer` | `memory/roles/delivery-engineer/` |

> 前端和后端统一使用 `delivery-engineer` 角色值——AI 不区分语言栈，你们自己知道自己在做什么。

---

## 一、产品经理

### 核心职责
定义"做什么"和"为什么做"，输出 PRD 和验收标准，是整个交付链路的起点。

### AI 工具配置

```json
// .claude/project-config.json
{
  "currentRole": "product-owner",
  "gitUser": "pm@company.com"
}
```

### 日常 AI 工作流

#### 需求挖掘
```
/deep-interview "用户反馈：登录流程太复杂"
```
Socratic 提问法深挖真实需求，歧义度降到 20% 以下才开始写 PRD。

#### 产品方向审视
```
/plan-ceo-review
```
CEO 视角评审：用户价值是否清晰？优先级是否合理？

#### YC 式压力测试
```
/office-hours
```
模拟 YC 导师质疑，逼出产品假设的核心弱点。

#### 复杂功能规划
```
/ralplan
```
Planner → Architect → Critic 三角评审，产出带风险评估的功能规划。

### PRD 输出规范

**文件位置**：`docs/01_product/prd_{feature}.md`

**必须包含**：
```markdown
## 背景与目标
- 解决什么用户问题
- 预期业务价值

## 用户故事
- As a [用户]，I want [功能]，So that [价值]

## 验收标准（AC）
- AC-01: Given [前置] When [操作] Then [结果]
- AC-02: ...

## 技术约束
- 性能：P99 响应 < Xms
- 安全：需要鉴权
- 兼容：支持移动端

## 优先级
- RICE 评分：Reach/Impact/Confidence/Effort
```

> AC（验收标准）直接成为测试用例的来源，必须可量化、可验证。

### 与研发交接检查清单

- [ ] PRD 已在 `docs/01_product/` 定稿
- [ ] 所有 AC 已明确且可量化
- [ ] 设计稿已确认（链接写入 PRD）
- [ ] `memory/.index/active-tasks.json` 已添加新需求

### 推荐 Agents

| Agent | 触发时机 |
|-------|---------|
| `analyst`（内置）| 需求结构化分析 |
| `omc-analyst` | 复杂需求拆解 |
| `omc-critic` | 挑战产品假设 |

---

## 二、研发团队

### 前端工程师

#### 核心职责
将设计稿和 API 转化为用户可交互的界面。负责 `src/frontend/`。

#### AI 工具配置

```json
{
  "currentRole": "delivery-engineer",
  "gitUser": "fe@company.com"
}
```

#### 日常 AI 工作流

**启动新功能**：
```
1. 读取 PRD：cat docs/01_product/prd_{feature}.md
2. /brainstorming → 确认技术方向
3. /writing-plans → 拆分前端开发任务
4. /tdd-workflow → TDD 开发
```

**UI 实现流程**：
```
/frontend-design    → 生成高质量界面初稿
/react-best-practices → React/Next.js 性能优化
/adapt              → 响应式多屏适配
/fixing-accessibility → WCAG 无障碍检查
/baseline-ui        → 动画/排版基线验证
/ai-slop-cleaner    → 清理 AI 生成代码味道
```

**提 PR 前**：
```
/review             → 代码审查
/verification-before-completion → 完成验证
```

#### 前端专用技能速查

| 场景 | 命令 |
|------|------|
| 界面构建 | `/frontend-design` |
| 前端架构模式 | `/frontend-patterns` |
| Playwright 测试 | `/e2e-testing` |
| React 优化 | `/react-best-practices` |
| 响应式 | `/adapt` |
| 可访问性 | `/fixing-accessibility` |
| 动画性能 | `/fixing-motion-performance` |
| 视觉打磨 | `/polish` |
| UI 质量基线 | `/baseline-ui` |
| 文案优化 | `/clarify` |

#### 推荐 Agents

| Agent | 触发时机 |
|-------|---------|
| `code-reviewer`（内置）| 代码改动后自动触发 |
| `omc-code-reviewer` | PR 前代码审查 |
| `analyst`（内置）| 性能问题排查 |

---

### 后端工程师

#### 核心职责
设计并实现 API、业务逻辑和数据层。负责 `src/backend/`。语言栈（Java/Python/Node）由 AI 辅助，无需手动查文档。

#### AI 工具配置

```json
{
  "currentRole": "delivery-engineer",
  "gitUser": "be@company.com"
}
```

#### 日常 AI 工作流

**启动新 API**：
```
1. 读取 PRD + API 规范：
   cat docs/01_product/prd_{feature}.md
   cat docs/03_architecture/api_spec.md
2. /api-design → REST 接口设计审查
3. /writing-plans → 拆分后端开发任务
4. /tdd-workflow → 先写集成测试，再写实现
```

**开发中**：
```
/coding-standards   → 编码规范检查（语言无关）
/backend-patterns   → 仓库层/缓存/JWT/RBAC 模式
/systematic-debugging → 遇到 Bug 时结构化排障
/learner            → 复杂 Bug 修复后提取经验
```

**提 PR 前**：
```
/review                         → 代码审查
/verification-before-completion → 完成验证
/ai-slop-cleaner               → 清理 AI 代码味道
```

#### 后端专用技能速查

| 场景 | 命令 |
|------|------|
| API 设计 | `/api-design` |
| 通用编码规范 | `/coding-standards` |
| 后端架构模式 | `/backend-patterns` |
| Spring Boot 测试 | `/springboot-tdd` |
| Spring Boot 验证 | `/springboot-verification` |
| Spring Boot 架构 | `/springboot-patterns` |
| Java 规范 | `/java-coding-standards` |
| 安全扫描 | `security-reviewer` Agent |
| 性能分析 | `analyst` Agent |
| 构建报错 | `debugger` Agent |

#### 推荐 Agents

| Agent | 触发时机 |
|-------|---------|
| `code-reviewer`（内置）| 代码改动后 |
| `security-reviewer`（内置）| 认证/支付/权限代码 |
| `omc-security-reviewer` | 安全敏感 PR |
| `debugger`（内置）| 构建报错时立即触发 |

---

### 研发通用规范

#### TDD 开发循环（前后端统一）

```
红灯（写失败测试）
    ↓
绿灯（最简实现）
    ↓
重构（保持测试通过）
    ↓
重复 → 直到功能完成
```

#### Git 提交规范

```
feat: 用户登录 API 实现

Constraint: JWT 过期由客户端续期处理
Rejected: Session Cookie | 不支持移动端场景
Confidence: high
Scope-risk: narrow

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

#### 与测试交接检查清单

- [ ] 功能分支已合并至 `develop`
- [ ] 单元测试覆盖率 ≥ 80%
- [ ] API 文档已更新（`docs/03_architecture/api_spec.md`）
- [ ] `memory/.index/active-tasks.json` 状态改为 `ready_for_qa`

---

## 三、测试工程师

### 核心职责
基于 PRD 验收标准，保障功能质量、接口安全和性能达标。不只是"执行测试"——AI 时代测试工程师是**质量门禁的设计者**。

### AI 工具配置

```json
{
  "currentRole": "quality-engineer",
  "gitUser": "qa@company.com"
}
```

### 日常 AI 工作流

**制定测试计划**：
```
1. 读取 PRD 和验收标准：
   cat docs/01_product/prd_{feature}.md
2. 将 AC 拆解为测试用例
3. 输出至：docs/04_qa/test_cases_{feature}.md
```

**执行测试**：
```
/qa              → 自动化测试（单元 + 集成 + E2E）
/qa-only         → 只看报告，不自动修复
/ultraqa         → QA 循环，直到全部通过
browse 技能  → 视觉回归 + 交互 + 无障碍测试
/benchmark       → 性能基线测量
/cso             → 安全审计
```

**发现 Bug 后**：
```
/learner         → 提取 Bug 模式，沉淀为可复用知识
```

### 测试用例格式（基于 AC）

```markdown
## TC-001 正常登录流程

**对应 AC**：AC-01
**前置条件**：用户已注册，账号未锁定
**步骤**：
1. POST /api/auth/login { email, password }
**预期结果**：返回 200 + JWT token
**自动化**：是（已覆盖）
```

### 测试技能速查

| 测试类型 | 命令 |
|---------|------|
| 全量自动化 | `/qa` |
| 只看报告 | `/qa-only` |
| QA 循环至通过 | `/ultraqa` |
| 浏览器 UI 测试 | `browse` |
| 可访问性审计 | `/audit` + `/fixing-accessibility` |
| 安全测试 | `/cso` |
| 性能基线 | `/benchmark` |
| 设计还原度 | `/design-review` |

### 质量门禁（上线准入）

**必须全部通过才能交付运维**：

- [ ] 所有 AC 验收通过
- [ ] P0/P1 缺陷清零
- [ ] 自动化覆盖率 ≥ 80%
- [ ] 安全扫描无高危漏洞
- [ ] P99 响应时间达标

**测试报告位置**：`docs/04_qa/test_report_{version}.md`

### 与运维交接检查清单

- [ ] 测试报告已输出
- [ ] 质量门禁全部通过
- [ ] `memory/.index/active-tasks.json` 状态改为 `ready_for_deploy`

### 推荐 Agents

| Agent | 触发时机 |
|-------|---------|
| `omc-qa-tester` | 测试方案设计 |
| `omc-verifier` | 验收标准核查 |
| `omc-security-reviewer` | 安全测试 |
| `security-reviewer`（内置）| 自动安全扫描 |

---

## 四、运维工程师

### 核心职责
负责从"通过测试"到"稳定运行"的全程：部署、监控、应急响应、成本优化。

### AI 工具配置

```json
{
  "currentRole": "delivery-engineer",
  "gitUser": "ops@company.com"
}
```

### 日常 AI 工作流

**发布流程**：
```
/ship            → 发布前完整检查清单
/land-and-deploy → 合并 + 自动化部署
/canary          → 金丝雀发布监控（灰度 5%）
观察 15 分钟无异常 → 全量推送
```

**故障响应**：
```
/investigate     → 根因调查
/systematic-debugging → 结构化排障
/retro           → 故障事后复盘
/learner         → 提取故障处理经验，沉淀到 Runbook
```

**日常运维**：
```
/guard           → 高风险操作安全防护
/careful         → 破坏性命令前二次确认
/benchmark       → 定期性能基线检测
```

### 标准发布序列

```
1. 接收测试报告 → 确认质量门禁全部通过
       ↓
2. /ship → 执行发布前检查清单
       ↓
3. 部署到预发环境 → 冒烟测试
       ↓
4. /canary → 金丝雀发布（灰度 5%，观察 15 分钟）
       ↓
5. 监控指标正常 → /land-and-deploy → 全量发布
       ↓
6. 验证关键指标（错误率/P99/业务指标）
       ↓
7. /retro → 发布复盘（每次必做）
```

### 运维技能速查

| 场景 | 命令 |
|------|------|
| 发布前检查 | `/ship` |
| 金丝雀发布 | `/canary` |
| 全量部署 | `/land-and-deploy` |
| 根因调查 | `/investigate` |
| 结构化排障 | `/systematic-debugging` |
| 故障复盘 | `/retro` |
| 高危操作保护 | `/guard` + `/careful` |
| 性能基线 | `/benchmark` |
| 经验沉淀 | `/learner` |
| 持续执行任务 | `/ralph` |

### 关键监控指标

| 指标 | 正常阈值 |
|------|---------|
| 错误率 | < 0.1% |
| P99 响应时间 | < SLA 阈值 |
| CPU 使用率 | < 80% |
| 内存使用率 | < 80% |
| 业务成功率 | > 99.9% |

### 应急响应流程

```
告警触发
    ↓
/investigate → 快速定位范围
    ↓
/systematic-debugging → 找到根因
    ↓
评估：能热修复？→ 修复并观察
      不能热修复？→ 回滚 → ops/runbook/rollback.md
    ↓
通知相关方（PM / 研发 / 测试）
    ↓
/retro → 故障复盘 → /learner → 更新 Runbook
```

**Runbook 位置**：`docs/05_ops/runbook.md`

---

## 跨岗位协作规范

### AI-Native API 协作与多微服务契约流转

在多微服务架构下，为打破前后端并行开发的阻碍，研发团队必须遵循“**API 契约先行 + AI 统管路由**”的协作模式。该模式依赖于前期 API 设计的高准确率。

1. **AI 统管 API 设计与网关路由**
   - PRD 确认后，必须由前后端共同（或架构师角色）唤醒 `/api-design`。
   - **生成绝对契约**：让 AI 依据 PRD 直接生成严格的统一契约（如 OpenAPI/Swagger 规范档），保存在 `docs/03_architecture/api_specs.md` 中。
   - **统管路由与版本**：在多微服务情况下，前端永远只面对**一个聚合网关的统一 URL 输出设计**。API 的版本控制和底层微服务路由转发规则变为纯粹的声明式配置文件，在这阶段由 AI 一并生成并统管。

2. **前端纯 Mock TDD**
   - 前端拿着这份神圣契约，让 AI 一键生成前端的 Mock Server 或是 Mock 数据源。
   - 接着前端可以直接通过 `/tdd-workflow` 编写 UI 测试、实现组件，完全不被真实的后端服务阻塞和拖延。

3. **后端微服务 TDD**
   - 后端团队根据同一份契约，分别在自己负责的微服务中，使用契约中的出入参来编写 Controller 层的契约测试和集成测试。
   - 跑通 `/springboot-tdd` 等严苛验证技能，以此将分散的底层微服务接口业务跑通即可。

### 任务状态流转

```
PM 创建需求
    ↓ [状态: ready_for_dev]
研发接单开发
    ↓ [状态: ready_for_qa]
测试验收
    ↓ [状态: ready_for_deploy]
运维发布上线
    ↓ [状态: done]
```

**状态文件**：`memory/.index/active-tasks.json`

### 阶段交接检查清单

**PM → 研发**：
- [ ] `docs/01_product/prd_{feature}.md` 已定稿
- [ ] 验收标准（AC）明确且可量化
- [ ] 设计稿已确认
- [ ] `active-tasks.json` 已更新

**研发 → 测试**：
- [ ] 功能分支已合并至 `develop`
- [ ] 单元测试覆盖率 ≥ 80%
- [ ] API 文档已更新
- [ ] `active-tasks.json` 状态 → `ready_for_qa`

**测试 → 运维**：
- [ ] `docs/04_qa/test_report_{version}.md` 已输出
- [ ] 质量门禁全部通过
- [ ] `active-tasks.json` 状态 → `ready_for_deploy`

### 团队每日同步

```bash
# 全团队共同维护，每日站会前查看
cat memory/.index/today-overview.md
```

**格式示例**：
```markdown
## 2026-03-27 团队进度

### feat-auth 用户认证（本迭代）
- 产品：✅ PRD 已定稿
- 前端：🔄 登录页 80%（今日完成）
- 后端：✅ 登录 API 完成
- 测试：⏳ 等待前端完成
- 运维：⏳ 等待测试通过

### 跨岗位阻塞
- 前端等待：后端密码重置 API（预计今日）
- 测试等待：前端注册页面
```

### 并行开发（多功能同时进行）

```bash
# 使用 git worktree 隔离不同功能
git worktree add ../feat-auth feature/user-auth
git worktree add ../feat-payment feature/payment

# 各自独立开发，互不干扰
```

详见 `using-git-worktrees` 技能。

---

## OMC 技能各岗位推荐

所有岗位均可使用 OMC 技能（`/omc-upgrade` 安装）：

| 技能 | 产品 | 前端 | 后端 | 测试 | 运维 |
|------|:----:|:----:|:----:|:----:|:----:|
| `/deep-interview` 需求澄清 | ★★★ | ★★ | ★★ | ★ | ★ |
| `/ralph` 持续执行 | ★ | ★★ | ★★★ | ★★ | ★★★ |
| `/ultraqa` QA 循环 | ★ | ★★ | ★★ | ★★★ | ★ |
| `/ai-slop-cleaner` 代码清理 | ★ | ★★★ | ★★★ | ★ | ★ |
| `/learner` 经验沉淀 | ★★ | ★★ | ★★ | ★★★ | ★★★ |
| `/ralplan` 架构共识 | ★★★ | ★★ | ★★★ | ★ | ★★ |
| `/autopilot` 全自动 | ★★★ | ★★★ | ★★ | ★ | ★ |

---

## 快速命令速查表

| 阶段 | 命令 | 用途 |
|------|------|------|
| **产品** | `/deep-interview` | 需求深度挖掘 |
| 产品 | `/plan-ceo-review` | CEO 视角评审 |
| 产品 | `/office-hours` | YC 压力测试 |
| 产品 | `/ralplan` | 复杂功能规划 |
| **前端** | `/brainstorming` | 新功能启动 |
| 前端 | `/frontend-design` | UI 构建 |
| 前端 | `/frontend-patterns`| 前端架构模式 |
| 前端 | `/e2e-testing`     | Playwright 测试 |
| 前端 | `/react-best-practices` | 性能优化 |
| 前端 | `/ai-slop-cleaner` | 代码清理 |
| **后端** | `/api-design` | 接口设计 |
| 后端 | `/backend-patterns` | 架构模式 |
| 后端 | `/springboot-patterns` | SB 架构模式 |
| 后端 | `/springboot-tdd` | SB 单元集成测试 |
| 后端 | `/springboot-verification` | SB 项目验证 |
| 后端 | `/java-coding-standards` | Java 编码规范 |
| 后端 | `/systematic-debugging` | Bug 排查 |
| 后端 | `/learner` | 经验沉淀 |
| **测试** | `/qa` | 自动化测试 |
| 测试 | `/ultraqa` | QA 循环 |
| 测试 | `/cso` | 安全审计 |
| 测试 | `/benchmark` | 性能基线 |
| **运维** | `/ship` | 发布检查 |
| 运维 | `/canary` | 金丝雀发布 |
| 运维 | `/investigate` | 根因调查 |
| 运维 | `/retro` | 复盘 |
| **全员** | `/omc-upgrade` | 安装/升级 OMC |

---

## 相关文档

| 文档 | 说明 |
|------|------|
| [product-workflow.md](../ai-native/product-workflow.md) | 产品全流程（阶段视角）|
| [ROLE_SETUP.md](ROLE_SETUP.md) | AI-Native 岗位体系（目标态）|
| [SKILLS_INDEX.md](SKILLS_INDEX.md) | 全量技能索引 |
| [Claude Code 使用指南](../../00_ai_system/claude-code-40-best-practices.md) | 40+ 使用技巧 |

---

*过渡期协作手册 v1.0 | 2026-03-27 | 目标：6 个月内迁移到 AI-Native 岗位体系*
