# Changelog

所有版本变更记录遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

## [Unreleased]

### Added
- `docs/07_sop/`：新增 AI-Native Coding 可执行 SOP 工作台，覆盖单人、团队、简单项目、复杂产品、角色路由和任务模板。
- `docs/07_sop/templates/`：新增任务卡、需求规格、契约变更、PR Review、handoff 模板。

### Changed
- `docs/00_AI_NATIVE_SOP.md`：重写为 0-7 阶段总纲 SOP，明确 ROM/RAM、角色体系、任务最小单元和完成定义。
- `README.md`、`docs/06_handbooks/README.md`：补充 SOP 快速入口。
- `docs/06_handbooks/ai-native/README.md`、`docs/06_handbooks/traditional/README.md`：补充阶段 SOP 和角色路由入口。

---

## [2.1.1] - 2026-04-07

### Added
- `docs/00_ai_system/graphify-guide.md`：Graphify 接入与使用指南
- `.graphifyignore`：Graphify 忽略配置文件

### Changed
- `README.md`：更新版本号与日期，补充 Graphify 相关导航
- `docs/00_ai_system/README.md`：新增 Graphify 文档索引
- `docs/06_handbooks/ai-native/SKILLS_INDEX.md`：补充 Graphify 相关技能索引

---

## [2.1.0] - 2026-03-27

### Added
- `oh-my-claudecode` 作为全局 plugin 依赖，直接复用其原版能力（无需 vendor）
  - `deep-interview`：Socratic 需求澄清，数学化歧义评分
  - `ralph`：PRD 驱动的持续执行循环
  - `ultrawork`：并行执行引擎（Haiku/Sonnet/Opus 分层）
  - `autopilot`：idea → 全自动流水线
  - `ai-slop-cleaner`：行为安全优先的 AI 代码味清理
  - `learner`：项目级调试知识提取
  - `ultraqa` / `ralplan`：QA 循环 + 共识规划
- `/omc-upgrade` skill：一键升级 OMC 插件
- `analyst` agent：需求分析师（模糊需求 → 结构化规格）
- `critic` agent：批判性审查员（挑战方案、发现盲点）
- Git commit trailers 规范（`Constraint:`/`Rejected:`/`Directive:` 等）
- 模型分层路由规则（Haiku/Sonnet/Opus 三档明确指引）

### Changed
- `behaviors.md`：新增第 9 章（Git Trailers）、第 10 章（模型分层路由）
- `skill-triggers.md`：新增 deep-interview / learner / ai-slop-cleaner / critic 触发规则
- `CLAUDE.md`：新增 3 个命令 + 2 个 Agent 说明

---

## [2.0.0] - 2026-03-24

### Breaking Changes
- `memory/today.md`、`memory/active-tasks.json`、`memory/conductor-tasks.json` 已删除，统一到 `memory/roles/{role}/today.md` 和 `memory/.index/`
- `custom-skills/` 目录已迁移到 `.claude/skills/`

### Added
- 多技术栈支持：前端 React/Vue/Next.js，后端 Java(SpringBoot)/Python(FastAPI)
- `scripts/replace-placeholders.sh`：一键初始化新项目
- `scripts/lock.sh`：AI 并发任务锁工具（支持过期自动清理）
- `templates/`：前后端技术栈结构模板
- `src/shared/`：前后端共享类型/常量层
- `SCAFFOLD_VERSION`：脚手架版本追踪
- `CLAUDE.local.md.template`：本地配置覆盖模板
- `.claude/commands/switch-role.md`：角色切换命令
- `docs/00_ai_system/subtree-guide.md`：git subtree 接入手册

### Fixed
- `context-handoff` 技能读取路径修正
- `src/backend/README.md` 移除 Node.js 特定结构，改为 Java/Python 双模板
- `README.md` 目录结构图与实际路径对齐

### Changed
- `src/frontend/README.md`：改为多技术栈说明
- `src/backend/README.md`：改为 Java/Python 双模板

---

## [1.0.0] - 2026-03-23

### Added
- 初始版本发布
- CLAUDE.md AI 灵魂中枢
- 7 角色系统（fullstack/frontend/backend/pm/qa/devops/architect）
- 路径过滤规则（YAML frontmatter）
- 语言专属 Reviewer Agent（frontend/java/python）
- OWASP 安全审计员 Agent
- gstack 技能套件集成
- TDD + 人类确认双门禁规则
