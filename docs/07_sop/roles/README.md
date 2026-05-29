# 角色与语言栈路由

> 先按“今天要解决的问题”选角色，再按语言栈选择验证命令。不要反过来让语言栈决定流程。

---

## 我是谁，该读哪份手册？

| 你今天的身份 | 读这份 | 主要输出 |
|-------------|--------|----------|
| 产品经理 / PO | [../../06_handbooks/traditional/pm.md](../../06_handbooks/traditional/pm.md)、[../../06_handbooks/ai-native/product-owner.md](../../06_handbooks/ai-native/product-owner.md) | PRD、验收标准、不做范围 |
| 初级前端 | [../../06_handbooks/traditional/frontend.md](../../06_handbooks/traditional/frontend.md) | 页面、组件、E2E 或组件测试 |
| 资深前端 / 前端架构 | [../../06_handbooks/traditional/frontend.md](../../06_handbooks/traditional/frontend.md) + [../lifecycle.md](../lifecycle.md) 阶段 3-6 | 前端架构、状态管理、契约审查 |
| 初级后端 | [../../06_handbooks/traditional/backend.md](../../06_handbooks/traditional/backend.md) | API、服务、测试 |
| 资深后端 / 架构 | [../../06_handbooks/traditional/backend.md](../../06_handbooks/traditional/backend.md) + [../project-types/complex-product.md](../project-types/complex-product.md) | 模块边界、数据所有权、ADR |
| 测试 / QA | [../../06_handbooks/traditional/qa.md](../../06_handbooks/traditional/qa.md)、[../../06_handbooks/ai-native/quality-engineer.md](../../06_handbooks/ai-native/quality-engineer.md) | 用例、自动化测试、质量报告 |
| 运维 / DevOps | [../../06_handbooks/traditional/devops.md](../../06_handbooks/traditional/devops.md) | 部署、监控、回滚、runbook |
| 全栈交付 | [../../06_handbooks/ai-native/delivery-engineer.md](../../06_handbooks/ai-native/delivery-engineer.md) | 端到端功能闭环 |
| AI 工程 / Agent 编排 | [../../06_handbooks/ai-native/ai-engineer.md](../../06_handbooks/ai-native/ai-engineer.md) | Agent、LLM 集成、AI 流程 |

---

## 按语言栈选择验证命令

| 语言/框架 | 常用手册 | 推荐验证 |
|-----------|----------|----------|
| Java / Spring Boot | 后端手册、Spring Boot skills | `mvn test`、`mvn verify`、契约测试 |
| Go | 后端手册 | `go test ./...`、`go vet ./...` |
| Node.js / TypeScript API | 后端手册 | `npm test`、`npm run typecheck`、`npm run lint` |
| React / TypeScript | 前端手册 | `npm run test`、`npm run build`、Playwright E2E |
| Vue / TypeScript | 前端手册 | `npm run test`、`npm run build`、Playwright E2E |
| Python / FastAPI | 后端手册 | `pytest`、`ruff check`、`mypy` |
| 数据库 / Migration | 后端手册、运维手册 | migration test、回滚演练 |

---

## 初级研发操作边界

初级研发可以让 AI 多做执行，但要强制 AI 先解释：

```text
请先读任务卡和相关文件。
请列出你会修改的文件。
请先写失败测试或验收用例。
请不要修改任务卡未允许的目录。
请完成后给出验证命令和结果。
```

禁止：

- 不看 diff 合并。
- 没有验收标准就开工。
- 让 AI 自行决定 API 字段。

---

## 资深研发操作边界

资深研发重点使用 AI 做：

- 方案对比。
- 反方审查。
- 测试缺口扫描。
- 安全边界检查。
- 重构拆解。
- 文档同步。

资深研发必须亲自确认：

- 架构取舍。
- 权限和数据边界。
- 不可逆 migration。
- CI/CD 和生产配置。
- 是否接受风险。

---

## 角色切换规则

同一人可以切换角色，但每次切换都要更新上下文：

1. 读 `memory/handoff.md`。
2. 说明当前以哪个角色接手。
3. 只处理该角色职责内的任务。
4. 如果发现上游产物缺失，退回上游，不硬写代码。

示例：

```text
我现在以后端工程师身份接手。
请先读取 docs/01_product/prd_x.md、docs/03_architecture/api_specs.md、memory/handoff.md。
如果 API 契约不足，先输出 contract change proposal，不要直接实现。
```
