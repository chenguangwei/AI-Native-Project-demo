# AI 会话启动 Prompt 模板

> 适用于 Claude Code、Codex 和其他 AI 编程工具。每次新任务开始时复制本模板。

```text
请按本仓库 AI-Native SOP 执行：
- docs/00_AI_NATIVE_SOP.md
- docs/07_sop/task-driven-collaboration.md
- docs/07_sop/templates/task-card.md
- AGENTS.md
- memory/active-task.md
- memory/handoff.md
- memory/project-facts.md

先不要写代码。

任务：
[粘贴任务描述或任务卡]

请先输出：
1. 任务分类
2. 推荐 skill chain
3. 必须读取的上下文文件
4. 任务卡缺失的信息
5. 允许修改范围建议
6. 禁止修改范围建议
7. 是否需要我确认后再执行

执行规则：
- 需求不清楚先使用 deep-interview 的方式澄清。
- 新功能先计划，再 TDD。
- API 先契约，再实现。
- 前端必须覆盖 loading / empty / error。
- Bug 先复现，再回归测试，再修复。
- 完成前必须运行验证命令。
- 完成后更新 memory。
```
