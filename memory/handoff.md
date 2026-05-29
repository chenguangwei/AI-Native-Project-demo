# 🤝 会话交接记录 (Handoff)

> **前情提要**: 这不是流水账，这是给下一个会话（或者是给接手的人类）留的接力棒。旧的记录请随时清空。

## 最新交接点 
**[交付工程师 - 2026-05-29 14:45]**

- **刚刚干了什么**:
  - 基于 `docs/00_ai_system/ai-coding-guide.md` 完成 AI-native coding SOP 脚手架优化。
  - 新增 `docs/07_sop/`：生命周期总流程、单人流程、团队流程、简单项目、复杂产品、角色路由、任务卡/需求规格/契约变更/Review/handoff 模板。
  - 重写 `docs/00_AI_NATIVE_SOP.md`，让总纲直接回答“整个 SOP 流程是什么”。
  - 更新 `README.md`、`CHANGELOG.md`、`docs/06_handbooks/README.md`、`docs/06_handbooks/ai-native/README.md`、`docs/06_handbooks/traditional/README.md`、`docs/00_ai_system/README.md` 的入口导航。
- **剩下的坑 / Blocker**:
  - 无功能阻塞。
  - 工作区存在本次任务之外的既有改动：`.codex/config.toml`、`.codex/agents/*.toml`、`docs/01_product/*-prd-prompt.md`、`docs/00_ai_system/ai-coding-guide.md`。
- **下一步要做什么 (Next Steps)**:
  - 人类确认文档结构后再提交；不要未经确认 push。

**[交付工程师 - 2026-04-09 12:00]**

- **刚刚干了什么**:
  - 已定位并修复 “OMC agents 在 Claude Code 与 Codex 下不可用” 问题。
  - 根因：OMC vendored 目录有 `agents/*.md`（19 个），但项目根缺少 agent 发现入口：`.claude/agents` 与 `.agents/agents`。
  - 已扩展 `scripts/sync-omc-skills.sh`：
    - 同步 `agents/*.md` 到 `.claude/agents/omc-*.md` 与 `.agents/agents/omc-*.md`
    - 无冲突时创建别名：`architect.md -> omc-architect.md`（双目录）
  - 已回归验证：`synced=19`、`alias linked=38`，`architect/debugger/...` 与 `omc-architect/omc-debugger/...` 均可解析。
  - 已更新 `.agents/skills/omc-upgrade/SKILL.md`，将 agents 同步纳入标准升级流程。
- **剩下的坑 / Blocker**:
  - 无功能阻塞。
  - 当前未提交改动：`scripts/sync-omc-skills.sh`、`.agents/skills/omc-upgrade/SKILL.md`、新增 `.claude/agents/` 与 `.agents/agents/`。
- **下一步要做什么 (Next Steps)**:
  - 人类确认后提交并推送本次 agent 入口修复。
