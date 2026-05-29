# AI-Native 操作手册

> 岗位不再按语言栈划分，而是按"你解决什么问题"来定义。

---

## 4 个岗位手册

| 岗位 | 手册 | 核心职责 | currentRole |
|------|------|---------|-------------|
| 交付工程师 | [delivery-engineer.md](delivery-engineer.md) | 端到端交付产品特性（含部署上线），不区分前后端语言栈 | `delivery-engineer` |
| AI 工程师 | [ai-engineer.md](ai-engineer.md) | Agent 编排、LLM 集成、AI 流水线 | `ai-engineer` |
| 质量工程师 | [quality-engineer.md](quality-engineer.md) | 全栈测试 + 安全 + 可靠性 + 可观测性 | `quality-engineer` |
| 产品负责人 | [product-owner.md](product-owner.md) | 需求策略、PRD、用户研究 | `product-owner` |

---

## 选哪个岗位？

```
今天主要做什么？
├── 构建功能 / 部署上线 / 维护基础设施
│   └── delivery-engineer
├── 设计 AI Agent / LLM 集成 / Prompt 工程
│   └── ai-engineer
├── 测试 / 安全审计 / 可靠性保障
│   └── quality-engineer
└── 写 PRD / 需求分析 / 方向评审
    └── product-owner
```

---

## 通用工作流

```
任务启动前  → karpathy-guidelines
需求不清楚  → /deep-interview
开始新功能  → /brainstorming
遇到 Bug   → /systematic-debugging
完成前      → /verification-before-completion
提 PR 前   → /review → /ai-slop-cleaner
发布前      → /ship
```

完整阶段 SOP：

- [生命周期总流程](../../07_sop/lifecycle.md)
- [单人流程](../../07_sop/solo.md)
- [团队流程](../../07_sop/team.md)
- [角色与语言栈路由](../../07_sop/roles/README.md)

---

## 与传统体系的对应关系

| AI-Native 岗位 | 替代了 |
|--------------|--------|
| 交付工程师 | 前端 + 后端 + 全栈 + 运维 |
| AI 工程师 | 架构师 + 新增 |
| 质量工程师 | 测试工程师 |
| 产品负责人 | 产品经理 |

过渡期协作指南 → [../traditional/](../traditional/README.md)
