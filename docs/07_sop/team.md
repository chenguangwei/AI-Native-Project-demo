# 多人协作 AI-Native Coding SOP

> 适合产品、前端、后端、测试、运维、架构人员共同协作。核心是用 `docs/` 固化共识，用 `memory/` 流转当前状态，用小 PR 控制风险。

---

## 协作原则

- `docs/` 是团队契约，不用聊天记录代替。
- `memory/` 是当前工作现场，接手前必读。
- API、DB、事件契约先于前后端并行实现。
- 每个任务卡只能有一个主责人。
- 多个 AI 可以并行 review，不要并行改同一批文件。

---

## 角色入口

| 角色 | 主入口 |
|------|--------|
| 产品 / PO | [../06_handbooks/traditional/pm.md](../06_handbooks/traditional/pm.md)、[../06_handbooks/ai-native/product-owner.md](../06_handbooks/ai-native/product-owner.md) |
| 前端 / Vue / React / TypeScript | [../06_handbooks/traditional/frontend.md](../06_handbooks/traditional/frontend.md) |
| 后端 / Java / Go / Node / Python | [../06_handbooks/traditional/backend.md](../06_handbooks/traditional/backend.md) |
| 测试 / QA | [../06_handbooks/traditional/qa.md](../06_handbooks/traditional/qa.md)、[../06_handbooks/ai-native/quality-engineer.md](../06_handbooks/ai-native/quality-engineer.md) |
| 运维 / DevOps | [../06_handbooks/traditional/devops.md](../06_handbooks/traditional/devops.md) |
| 全栈交付 | [../06_handbooks/ai-native/delivery-engineer.md](../06_handbooks/ai-native/delivery-engineer.md) |
| AI/Agent/LLM 编排 | [../06_handbooks/ai-native/ai-engineer.md](../06_handbooks/ai-native/ai-engineer.md) |

更细分的选择见 [roles/README.md](roles/README.md)。

---

## 团队流转

```text
产品定 PRD
  ↓
架构确认边界与契约
  ↓
QA 生成验收用例
  ↓
前后端基于契约并行
  ↓
垂直切片联调
  ↓
小 PR 持续交付
  ↓
多维 Review
  ↓
发布与复盘
```

---

## 阶段责任矩阵

| 阶段 | 主责 | 必须交付 | 其他角色如何接手 |
|------|------|----------|------------------|
| 需求澄清 | 产品 | PRD、AC、不做范围 | 研发只提风险，不直接实现 |
| 架构边界 | 架构/资深研发 | 模块边界、数据所有权、ADR | 产品确认取舍，QA 提测试风险 |
| 契约优先 | 后端/架构 | API、错误码、DB、事件契约 | 前端按 mock 开发，QA 写契约测试 |
| 垂直切片 | 交付工程师 | 最小端到端闭环 | 全员用它作为后续模板 |
| 批量功能 | 各任务主责 | 小 PR、测试、文档同步 | Review 只围绕任务卡和契约 |
| 发布硬化 | QA/DevOps | 验证报告、runbook、回滚方案 | 人类确认生产动作 |

---

## 任务领取规则

每个任务卡必须写：

- 主责人。
- 允许改哪些文件。
- 不允许改哪些文件。
- 上游依赖。
- 下游影响。
- 验证命令。

任务状态只写在：

- `memory/active-task.md`：主线进度。
- `memory/handoff.md`：当前断点、卡点、下一步。
- `docs/04_qa/audit_logs/`：测试或审计结论。

不要把状态散落在聊天记录里。

---

## 并行开发规则

可以并行：

- 前端按 mock 做 UI。
- 后端按 contract test 做接口。
- QA 按契约写测试。
- 架构师 review 模块边界。

不允许并行：

- 多人同时改 API contract。
- 多人同时改同一个 migration。
- 多个 AI 同时改认证、权限、CI workflow。
- 未同步 `memory/handoff.md` 就交接卡点。

---

## PR Gate

PR 必须包含：

- 任务卡链接或摘要。
- 改动范围。
- 验证命令和结果。
- 风险点。
- 是否改契约。
- 是否需要产品、QA、运维额外确认。

Review 至少覆盖：

- 正确性。
- 测试。
- 安全。
- 契约。
- 可维护性。

提 PR 前使用 [templates/review-checklist.md](templates/review-checklist.md)。

---

## 交接格式

任何人或 AI 中断时，更新 `memory/handoff.md`：

```markdown
**[角色 - 日期 时间]**

- **刚刚干了什么**:
  - 
- **剩下的坑 / Blocker**:
  - 
- **下一步要做什么 (Next Steps)**:
  - 
```

详细模板见 [templates/handoff.md](templates/handoff.md)。
