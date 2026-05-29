# 简单项目 SOP

> 适合脚本工具、后台小模块、内部管理页、Demo、PoC、单服务小应用。目标是快速交付，但仍保留验证闭环。

---

## 适用条件

满足大多数即可使用轻量模式：

- 1-2 人维护。
- 生命周期短或范围稳定。
- 不涉及复杂权限、支付、审计、强一致性。
- 部署环境简单。
- 没有多个团队并行开发。

不满足时，使用 [complex-product.md](complex-product.md)。

---

## 最小目录集

| 文件 | 必须写什么 |
|------|------------|
| `docs/01_product/goals.md` | 目标、用户、范围、不做什么 |
| `docs/03_architecture/system_flow.md` | 主要流程、模块边界、外部依赖 |
| `docs/03_architecture/api_specs.md` | 有接口就写请求、响应、错误码 |
| `docs/04_qa/test_cases.md` | 验收用例和手动验证步骤 |
| `memory/active-task.md` | 当前任务清单 |
| `memory/handoff.md` | 当前断点 |

---

## 轻量流程

```text
1. 写 goals.md
  ↓
2. 写任务卡
  ↓
3. 先写测试或验证脚本
  ↓
4. 实现最小功能
  ↓
5. 运行验证
  ↓
6. 更新 memory
```

---

## 任务卡粒度

一次只做：

- 一个命令。
- 一个页面。
- 一个接口。
- 一个数据导入流程。
- 一个 Bug 修复。

不要一次做：

- 整个后台系统。
- 所有页面。
- 所有接口。
- 大范围重构。

---

## 验证命令示例

| 技术栈 | 常用验证 |
|--------|----------|
| Node / TypeScript | `npm test`、`npm run typecheck`、`npm run lint` |
| React / Vue | `npm run test`、`npm run build`、Playwright smoke |
| Java / Spring Boot | `mvn test`、`mvn verify` |
| Go | `go test ./...`、`go vet ./...` |
| Python | `pytest`、`ruff check`、`mypy` |

如果没有自动化测试，至少写清手动验证步骤，并把缺口记入 `docs/04_qa/test_cases.md`。

---

## 何时升级为复杂项目流程

出现任意情况就升级：

- 多人并行开发。
- 前后端契约频繁变化。
- 引入权限、审计、支付、消息队列。
- 需要灰度、回滚、监控。
- 一次 Bug 会影响生产核心流程。
