# 🎯 当前主线任务 (Active Tasks)

## AI-Native Coding SOP 编排脚手架优化（已完成）
- [x] 基于 `docs/00_ai_system/ai-coding-guide.md` 提炼 0-7 阶段 AI-native coding 总流程
- [x] 新增 `docs/07_sop/`，覆盖单人、团队、简单项目、复杂产品、角色路由和任务模板
- [x] 新增 `docs/07_sop/task-driven-collaboration.md`，补齐“任务输入 → skills → Prompt → 执行 → 验证 → Review → 交接”的可执行协作剧本
- [x] 新增 `templates/ai-session-prompt.md` 与 `templates/skill-chain-plan.md`，支持 Claude Code / Codex 新任务启动
- [x] 扩展 `templates/task-card.md`，加入 AI 工具、任务类型、skill chain、每步输入输出和停止条件
- [x] 重写 `docs/00_AI_NATIVE_SOP.md` 为可执行总纲
- [x] 更新 `README.md`、`docs/06_handbooks/`、`docs/00_ai_system/README.md` 导航入口
- [x] 运行本地 Markdown 相对链接检查，确认新增 SOP 导航无死链

## OMC 对齐升级（已完成）
- [x] 对齐上游 `oh-my-claudecode` 最新版本（`4.11.2`）
- [x] 同步 `.claude/skills/omc/` 与 `.agents/skills/omc/` vendored 内容
- [x] 执行 `scripts/sync-omc-skills.sh` 刷新 `.agents/skills/` 顶层 OMC skills
- [x] 同步 OMC 相关文档（`README.md`、`docs/06_handbooks/ai-native/SKILLS_INDEX.md`、`.agents/skills/omc-upgrade/SKILL.md`）

## OMC 在 Claude Code 侧不生效（已修复）
- [x] 复现并确认根因：`.claude/skills/` 缺少 OMC skills 顶层入口链接
- [x] 修复 `scripts/sync-omc-skills.sh`：同步 `.agents/skills` + 维护 `.claude/skills/<skill> -> omc/skills/<skill>`
- [x] 回归验证：37 个 OMC skill 链接已建立，`deep-interview`/`verify`/`wiki` 等入口可解析
- [x] 更新 `.agents/skills/omc-upgrade/SKILL.md`，避免后续升级回归
- [ ] 视需要提交 commit（等待人类确认）

## OMC Agents 在 Claude/Codex 不生效（已修复）
- [x] 复现并确认根因：缺少 `.claude/agents` 与 `.agents/agents` 入口目录
- [x] 扩展 `scripts/sync-omc-skills.sh`：同步 19 个 OMC agents 到双入口并创建安全别名
- [x] 回归验证：`omc-architect`/`architect`、`omc-debugger`/`debugger` 在双目录均可解析
- [x] 更新 `.agents/skills/omc-upgrade/SKILL.md`，将 agents 同步纳入标准流程
- [ ] 视需要提交 commit（等待人类确认）
