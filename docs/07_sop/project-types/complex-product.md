# 复杂产品 SOP

> 适合长期演进的产品、前后端分离系统、多语言架构、多服务系统、多人协作项目。

---

## 适用条件

满足任一条件就按复杂产品执行：

- 产品、前端、后端、测试、运维多人协作。
- Java、Go、Node、Python、Vue、React、TypeScript 等多栈并存。
- 有权限、审计、支付、消息、复杂数据一致性。
- 需要 staging、灰度、回滚、监控。
- 需求会长期演进。

---

## 必备产物

| 阶段 | 产物 |
|------|------|
| 需求 | `docs/01_product/prd_{feature}.md`、业务规则、AC |
| 设计 | `docs/02_design/design_system.md`、关键页面说明 |
| 架构 | `docs/03_architecture/system_flow.md`、模块边界、ADR |
| 契约 | `docs/03_architecture/api_specs.md`、DB schema、错误码、事件协议 |
| 质量 | `docs/04_qa/test_cases.md`、审计日志、E2E 场景 |
| 运维 | `docs/05_ops/runbook.md`、部署环境、回滚方案 |
| 协作 | `memory/active-task.md`、`memory/handoff.md` |

---

## 推荐里程碑

```text
M0. 工程底座可运行
M1. 需求与验收标准定稿
M2. 架构与契约定稿
M3. 第一个垂直切片通过
M4. 批量功能按小 PR 推进
M5. 测试、安全、性能硬化
M6. 发布与复盘
```

---

## 契约优先规则

前后端、多服务、多语言协作时，契约是唯一事实源：

- REST：OpenAPI 或 `docs/03_architecture/api_specs.md`。
- GraphQL：schema。
- 事件：topic、payload、幂等规则。
- DB：核心实体、索引、迁移策略。

契约修改流程：

1. 提交 [contract-change-proposal.md](../templates/contract-change-proposal.md)。
2. 前端、后端、QA 同时评估影响。
3. 人类确认后先合并契约变更。
4. 实现 PR 再跟进。

---

## 多语言执行路径

| 技术域 | 入口 | 关键 gate |
|--------|------|-----------|
| Java / Spring Boot | [../roles/README.md](../roles/README.md) 的后端路径 | `mvn test`、契约测试、权限测试 |
| Go 服务 | 后端路径 + Go 测试命令 | `go test ./...`、接口契约 |
| Node / TypeScript API | 后端路径 + typecheck | API contract、lint、测试 |
| React / TypeScript | 前端路径 | 组件测试、E2E、build |
| Vue / TypeScript | 前端路径 | 组件测试、E2E、build |
| QA 自动化 | 质量路径 | 用例覆盖、失败路径、回归测试 |

---

## 复杂任务拆分

复杂任务先拆 milestone：

```text
1. characterization tests：锁住现有行为
2. contract：明确新旧契约
3. adapter：加适配层，不改业务
4. migration：迁移一个调用点
5. rollout：迁移剩余调用点
6. cleanup：删除旧实现
```

每个 milestone 再拆成任务卡。

---

## 禁止事项

- 未经架构确认重写核心模块。
- 在功能 PR 中顺手大改契约。
- 一个 PR 同时改前端、后端、DB、CI 且没有垂直切片验证。
- 没有测试或替代验证就进入发布。
- 把重要决策只留在聊天记录里。
