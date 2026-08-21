# 把 Agent 的失败变成可诊断、可迭代的问题

> Turning scattered agent failures into actionable workflow improvements.

这是一份偏研究笔记风格的作品展示：我在 Deep Agents / Terminal-Bench 项目中关注的不是单次任务有没有跑通，而是当 Agent 在复杂 terminal task 中频繁失败时，如何判断失败集中在哪里、为什么失败，以及下一轮应该优化 prompt、tool、环境还是评测流程。

**网页版：** [GitHub Pages 展示页](https://mogwai911.github.io/agent-failure-diagnosis-case-study/)；本地预览可打开 [index.html](index.html)。
**详细文档：** 见 [项目复盘](docs/case_study.md)、[工作流](docs/workflow.md)、[评测笔记](docs/evaluation.md) 和 [失败案例](docs/failure_cases.md)。

## 30 秒概览

这个项目处理的是一个系统优化问题：Agent 在复杂 terminal task 中失败率较高，pass/fail 只能告诉我们有没有通过，却不能解释为什么失败。

我的工作是建立一条从失败记录到优化动作的路线图：抽取失败轨迹，人工复核失败原因，区分模型理解、环境、输出契约、调试策略和评测边界，再把诊断结果转化为下一轮 workflow 调整。

核心结论很简单：优化 Agent 不是盲目换模型，而是先知道失败属于系统的哪一层。

## 问题

很多 Agent 失败并不是“完全不会做”，而是原因混杂：

- 模型可能理解错任务；
- 环境、依赖、权限或限流可能导致失败；
- 输出路径、格式或接口可能不符合 verifier；
- Agent 可能陷入局部调试循环；
- 评测边界本身也可能需要被明确。

因此，这个项目追问的是：

> 如何把分散的 Agent 失败变成可诊断、可讨论、可行动的优化信号？

## 诊断路线图

主页将工作流压缩成六步：

```text
Agent Runs -> Verifier Result -> Failure Sampling -> Human Review -> Failure Diagnosis -> Optimization Decision
```

每一步都区分三件事：发生了什么、AI / 系统提供什么、我负责什么判断。这样读者能看到具体方法，而不是只看到一组 benchmark 数字。

## 从失败类型到优化动作

| Failure type | What it means | What to change |
|---|---|---|
| Contract mismatch | 产物看起来对，但路径、格式或接口不符合验收 | 加强输出契约和 post-run check |
| Environment failure | 依赖、权限、限流或环境配置导致任务失败 | 隔离环境问题，不误判成模型能力失败 |
| Debug loop drift | Agent 局部修补很多，但没有改变失败策略 | 设置停止条件，回到 strategy-level 修正 |
| Task meaning error | 方法合理，但理解错了任务目标 | 改任务解析和隐藏约束提取 |

## 诊断一致性

这些数字不是 leaderboard 成绩，而是用来检查诊断标签与人工判断的一致性。

| Comparison | Strict | Relaxed |
|---|---:|---:|
| Development | 88% | 92% |
| Test | 70% | 75% |

任务通过率快照和更细证据保留在 [docs/evaluation.md](docs/evaluation.md) 和 [assets/result_table.md](assets/result_table.md)，不作为主页重点。

## 仓库结构

```text
.
├── index.html
├── README.md
├── docs/
│   ├── case_study.md
│   ├── workflow.md
│   ├── evaluation.md
│   └── failure_cases.md
├── assets/
│   ├── workflow.mmd
│   └── result_table.md
└── sanitized_examples/
    ├── example_task.md
    ├── example_agent_trace.md
    └── example_evaluation.md
```

## 详细文档链接

- [项目复盘](docs/case_study.md) — 问题、协作、结果与反思
- [工作流笔记](docs/workflow.md) — 详细执行循环与人工复核边界
- [评测笔记](docs/evaluation.md) — L1/L2 标签、严格/宽松一致性与数据边界
- [失败案例](docs/failure_cases.md) — 脱敏后的代表性失败模式与修正方向
- [脱敏轨迹](sanitized_examples/example_agent_trace.md) — 公开的执行轨迹示例
- [结果表](assets/result_table.md) — 带来源的紧凑快照表

## 公开边界与说明

这是一份基于公开 benchmark 任务整理的脱敏作品案例研究。公开边界不是回避数据集本身，而是排除个人信息、本地路径、凭证、环境配置、备份和原始本地运行轨迹。

发布或分发仓库前，仍需确认：

- 不存在任何私有路径、凭证、token、邮箱、电话或个人信息；
- 若页面未托管在标准 `github.io/<repo>` 路径下，`index.html` 中的 GitHub 仓库 URL 和默认分支正确；
- 许可证或使用说明符合预期的公开发布。
