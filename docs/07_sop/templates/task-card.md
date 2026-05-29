# AI Coding 任务卡模板

> 一个任务卡对应一个小闭环任务。任务卡不完整时，不进入实现。

## 任务目标

-

## AI 工具与 Skill Chain

AI 工具：

- [ ] Claude Code
- [ ] Codex
- [ ] 其他：

任务类型：

- [ ] 需求澄清
- [ ] 架构设计
- [ ] API / 契约
- [ ] 前端功能
- [ ] 后端功能
- [ ] Bug 修复
- [ ] QA 验收
- [ ] 发布上线
- [ ] 文档沉淀

推荐 skill chain：

```text
示例：/api-design → /tdd-workflow → /backend-patterns → /verification-before-completion → /review
```

每一步输入输出：

| 步骤 | Skill | 输入 | 期望输出 | 进入下一步条件 |
|------|-------|------|----------|----------------|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |

## 背景上下文

必须读取：

- `docs/01_product/...`
- `docs/03_architecture/...`
- `memory/handoff.md`

## 允许修改范围

-

## 禁止修改范围

-

## 实现要求

-

## AI 执行 Prompt

把下面内容交给 Claude Code、Codex 或其他 AI 编程工具：

```text
请按 docs/07_sop/task-driven-collaboration.md 执行本任务。
先不要写代码。
请先读取任务卡和背景上下文，输出：
1. 任务分类
2. skill chain
3. 实施计划
4. 测试计划
5. 需要人类确认的问题

确认后再进入 TDD 实现。
```

## TDD / 验收要求

先写或补齐：

- 单元测试：
- 集成测试：
- E2E / 手动验证：

验收标准：

- [ ]
- [ ]

## 必须运行的验证命令

```bash
# 示例，按项目替换
npm test
npm run typecheck
npm run lint
```

## 完成后输出

- 改动摘要。
- 验证命令和结果。
- 风险与未覆盖项。
- 需要人类确认的点。
- 是否更新了 `memory/active-task.md` 或 `memory/handoff.md`。

## 停止条件

出现以下任一情况，AI 必须停止实现并提问：

- 需要修改禁止范围。
- 验收标准不明确。
- 契约缺失或冲突。
- 需要不可逆 migration。
- 需要修改 CI/CD、secrets、生产权限。
- 验证命令无法运行且没有替代验证方案。
