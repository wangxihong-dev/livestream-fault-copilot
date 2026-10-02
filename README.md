# livestream-fault-copilot

直播技术支持故障排查辅助 Agent（MVP / PoC）。

## 要解决的业务问题

直播间技术支持的故障排查高度依赖个人经验。新人面对**黑屏、无声音、卡顿、断流**时，往往不知道该先问什么、先查什么，容易在信息不全的情况下猜测原因，或反复踩同一个坑。

本项目用「结构化故障知识 + RAG + Agent 决策」辅助技术支持人员完成四件事：

- **信息补全**：按每类故障的必填信息清单，缺什么就主动追问什么；
- **案例检索**：从历史故障案例与 SOP 中召回最相关的处置经验；
- **故障分析**：给出初步诊断，并说明依据来自哪条案例；
- **排查建议**：输出有来源、可执行的排查步骤，必要时提示转人工。

设计上刻意遵守一条原则：**信息不足时宁可追问，也不编造结论。**

## 当前状态

> **阶段 0 · 系统设计中，尚未编码。**

- 已确认第一版范围：仅覆盖 4 类故障（黑屏 / 无声音 / 卡顿 / 断流）。
- 数据全部为**自行构造的模拟数据**，不含任何真实公司资料、客户信息或内部 SOP。
- 开发路线为 15 步顺序推进（需求分析 → 数据模型 → FastAPI+MySQL → CRUD → 知识处理 → Embedding → Milvus → RAG → LangGraph → 追问 → Tool → 评估 → Vue → Docker → 部署），当前在 Step 1。

## 技术栈

`Python` · `FastAPI` · `Pydantic` · `SQLAlchemy (async)` · `MySQL` · `Milvus` · `LangGraph` · LLM API（Embedding + Tool Calling）

前端 `Vue3` **后置**：核心流程稳定前只用 FastAPI + Swagger 验证。

## 架构分层

| 组件 | 职责 |
|---|---|
| MySQL | 业务事实与结构化数据（故障案例） |
| Milvus | 可语义检索的知识片段 + metadata，仅作索引 |
| RAG | 找到与当前故障最相关的知识 |
| LangGraph / Agent | 决策下一步：追问 / 检索 / 调工具 / 诊断 / 转人工 |
| Tool | 获取需实时得到的信息（第一版仅一个模拟工具） |

> MySQL 存业务事实，Milvus 存可检索知识，Agent 负责用知识和做决策。

## 文档

- [`docs/project-context.md`](docs/project-context.md) —— 项目背景、目标、架构、技术选型、开发路线、验收标准
  - 其中 **第 12 节「设计决策记录」** 记录了每个设计选择的**结论 / 理由 / 推翻条件**，是理解本项目"为什么这么做"的入口。
- [`docs/design/data-model.md`](docs/design/data-model.md) —— 数据模型设计（**编写中**）
- [`docs/design/business-flow.md`](docs/design/business-flow.md) —— 业务流程设计（**编写中**）
- [`docs/design/rag-agent-boundary.md`](docs/design/rag-agent-boundary.md) —— RAG 与 Agent 职责边界（**编写中**）

## 说明

本项目是个人学习与技术准备性质的 PoC，**不追求生产级**，明确不做：用户认证 / RBAC、Redis、Kafka、Celery、Kubernetes、微服务、Multi-Agent、MCP、复杂 Memory、高并发与生产级 DevOps。