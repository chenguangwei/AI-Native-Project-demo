# 📚 操作手册导航

> 两套体系并行，按团队当前阶段选择。

---

## 先选 SOP，再选岗位

| 场景 | 先读 |
|------|------|
| 不清楚完整交付流程 | [../07_sop/lifecycle.md](../07_sop/lifecycle.md) |
| 单人开发 | [../07_sop/solo.md](../07_sop/solo.md) |
| 多人协作 | [../07_sop/team.md](../07_sop/team.md) |
| 按角色或语言栈找入口 | [../07_sop/roles/README.md](../07_sop/roles/README.md) |
| 拿到任务后如何和 AI 工具协作 | [../07_sop/task-driven-collaboration.md](../07_sop/task-driven-collaboration.md) |
| 要交给 AI 执行 | [../07_sop/templates/task-card.md](../07_sop/templates/task-card.md) |

---

## 选哪套？

```
团队已全面拥抱 AI，不再区分前端/后端？
    → ai-native/（目标态）

团队仍按产品/研发/测试/运维分工？
    → traditional/（过渡期）
```

---

## AI-Native 手册（目标态）

> 4 个岗位，按"解决什么问题"而非"会什么技术"划分

**入口** → **[ai-native/README.md](ai-native/README.md)**

| 手册 | 岗位 |
|------|------|
| [ai-native/delivery-engineer.md](ai-native/delivery-engineer.md) | 交付工程师 — 端到端交付特性（含部署上线）|
| [ai-native/ai-engineer.md](ai-native/ai-engineer.md) | AI 工程师 — Agent 编排、LLM 集成 |
| [ai-native/quality-engineer.md](ai-native/quality-engineer.md) | 质量工程师 — 测试+安全+可靠性+可观测性 |
| [ai-native/product-owner.md](ai-native/product-owner.md) | 产品负责人 — 需求策略、PRD |
| [ai-native/product-workflow.md](ai-native/product-workflow.md) | 产品全流程（阶段视角）|

---

## 传统体系手册（过渡期）

> 4 个岗位分组，保留原有分工，人人配备 AI 工具

**入口** → **[traditional/README.md](traditional/README.md)**（完整团队协作说明）

| 手册 | 岗位 |
|------|------|
| [traditional/pm.md](traditional/pm.md) | 产品经理 |
| [traditional/frontend.md](traditional/frontend.md) | 前端工程师 |
| [traditional/backend.md](traditional/backend.md) | 后端工程师 |
| [traditional/qa.md](traditional/qa.md) | 测试工程师 |
| [traditional/devops.md](traditional/devops.md) | 运维工程师 |

---

## 通用参考

- [AI-Native 技能速查表](ai-native/SKILLS_INDEX.md) — 按 4 大 AI 原生岗位分类的 88 个技能
- [传统全栈/研发技能速查表](traditional/SKILLS_INDEX.md) — 适合旧有研发分工直接选用的技能大全
- [AI-Native 操作大纲 SOP](../00_AI_NATIVE_SOP.md) — 全局团队架构协作规范
- [AI-Native Coding SOP 工作台](../07_sop/README.md) — 单人/团队/简单项目/复杂产品的可执行流程
- [任务驱动协作剧本](../07_sop/task-driven-collaboration.md) — Claude Code / Codex 任务协作链路
- [AI 系统文档](../00_ai_system/) — Claude Code 技巧指南
- [RAM 记忆机制（内存流转）](../../.claude/rules/02-memory-protocol.md) — 跨会话的任务同步规定（必读）

### 运行时集成更新

- `api-design` 已完成跨运行时集成：项目内（`.agents/.claude/.codex`）与全局（`~/.claude/skills ~/.agents ~/.codex`）均可用，支持 Claude Code 与 Codex。
- `karpathy-guidelines` 已接入项目内 skills（`.agents/.claude/.codex`），用于统一执行四条工程约束：先澄清假设、优先简单实现、只做外科式改动、按可验证目标交付。

---

*AI-Native 开发方法论 v2.0 | 过渡期两套体系并行*
