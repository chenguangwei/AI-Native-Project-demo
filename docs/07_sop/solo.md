# 单人 AI-Native Coding SOP

> 适合一个人做 Demo、内部工具、MVP、全栈产品，也适合小团队中一人独立负责某个模块。

---

## 单人模式原则

- 一人可以兼任产品、架构、研发、测试，但不能跳过这些阶段。
- 简单项目减产物，不减 gate。
- AI 是执行层，人是产品判断、架构取舍和合并责任人。

---

## 每天启动

```bash
cat memory/active-task.md
cat memory/handoff.md
cat memory/project-facts.md
```

然后选择今天的模式：

| 今天要做什么 | 打开 |
|-------------|------|
| 刚拿到任务，不知道怎么喂给 AI | [task-driven-collaboration.md](task-driven-collaboration.md) + [templates/ai-session-prompt.md](templates/ai-session-prompt.md) |
| 需求还不清楚 | [templates/feature-spec.md](templates/feature-spec.md) |
| 要写功能 | [templates/task-card.md](templates/task-card.md) |
| 要改接口 | [templates/contract-change-proposal.md](templates/contract-change-proposal.md) |
| 要提 PR 或合并 | [templates/review-checklist.md](templates/review-checklist.md) |

---

## 单人闭环

```text
1. 用 ai-session-prompt 启动 AI
  ↓
2. 让 AI 分类任务并选择 skill chain
  ↓
3. 让 AI 追问需求和边界
  ↓
4. 产出任务卡
  ↓
5. 先写测试或验收脚本
  ↓
6. 最小实现
  ↓
7. 运行验证命令
  ↓
8. 自审 + 清理 AI 味
  ↓
9. 更新 memory
```

具体 Prompt 和 skill chain 见 [task-driven-collaboration.md](task-driven-collaboration.md)。

---

## 简单项目轻量执行

只维护 5 个文件即可：

| 文件 | 用途 |
|------|------|
| `docs/01_product/goals.md` | 目标、用户、范围 |
| `docs/03_architecture/system_flow.md` | 关键流程和模块边界 |
| `docs/03_architecture/api_specs.md` | 有接口时必填 |
| `docs/04_qa/test_cases.md` | 验收用例 |
| `memory/active-task.md` | 当前任务树 |

完整说明见 [project-types/simple-project.md](project-types/simple-project.md)。

---

## 复杂项目单人执行

复杂项目不要一次性让 AI 写完整系统。按下面节奏：

1. 先出 PRD 和验收标准。
2. 再出架构边界和 API 契约。
3. 先打一个垂直切片。
4. 后续每次只做一个任务卡。
5. 每 3-5 个任务卡做一次 review 和文档同步。

完整说明见 [project-types/complex-product.md](project-types/complex-product.md)。

---

## 初级研发用法

每次给 AI 的指令都带上这 6 句话：

```text
先不要写代码。
请先阅读相关文件并列出你理解的范围。
请指出需求中不明确的地方。
请先写测试或验收用例。
只能修改任务卡允许的文件。
完成后必须告诉我运行了哪些验证命令。
```

更完整的新会话模板见 [templates/ai-session-prompt.md](templates/ai-session-prompt.md)。

不要做：

- 让 AI 一次性生成全栈系统。
- 没有测试就接受“完成”。
- 看不懂 diff 就合并。

---

## 资深研发用法

把 AI 当作工程执行层，重点审这些点：

- 任务是否足够小。
- 数据所有权是否清楚。
- 契约是否稳定。
- 失败路径是否有测试。
- 安全边界是否被破坏。
- 是否有无意义抽象和模板化代码。

可以让 AI 做反向审查：

```text
请从架构、安全、测试、可维护性四个角度攻击当前方案。
只列会导致返工、线上事故或协作混乱的问题。
```

---

## 单人完成前检查

- [ ] 任务卡验收标准全部满足。
- [ ] 测试、构建、lint、typecheck 或替代验证已跑。
- [ ] 变更范围没有越界。
- [ ] 文档与契约已同步。
- [ ] `memory/active-task.md` 已打勾或更新。
- [ ] `memory/handoff.md` 说明当前状态和下一步。
