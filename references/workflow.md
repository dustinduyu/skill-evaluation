# Skill / Agent 评测流程

写报告前，先用这份文件设计评测流程。

## 1. 理解评测对象

需要阅读：

- `SKILL.md`
- referenced files under `references/`, `scripts/`, `assets/`
- local code paths used by the skill
- existing logs, traces, metrics, datasets, prior reports
- user-provided methodology or reference samples

需要记录：

| 字段 | 记录内容 |
| --- | --- |
| 评测对象 | 单一 skill、组合 skill，或接近 agent 的工作流 |
| 核心任务 | 这个对象主要负责什么 |
| 输入 | prompt、文件、数据集、API、上游产物 |
| 输出 | 结构化结果、报告、文件、动作、判断 |
| 依赖 | API、key、CLI、模型、仓库、工具 |
| 边界 | 这个 skill 不负责什么 |

## 2. 使用固定三层

始终使用这三层。如果目标具备接近 agent 的能力，也把这些能力映射进三层，不新增第四层。

| 层级 | 观察对象 | 示例指标 |
| --- | --- | --- |
| 运行框架层 | 运行环境、路由、权限、API、日志、成本、耗时、输出文件 | 成功率、错误率、成本、耗时、唤起准确率 |
| 能力链路层 | 数据准备、工具链、状态交接、Schema、中间产物 | Schema 完整性、步骤成功率、召回覆盖、转换一致性 |
| 业务效果层 | 最终答案质量和任务价值 | 准确性、相关性、误判、漏判、用户价值、基线提升 |

接近 agent 的行为映射：

| 行为 | 放入哪一层 | 原因 |
| --- | --- | --- |
| 规划能力 | 能力链路层 | 影响任务拆解和执行顺序 |
| 记忆能力 | 能力链路层 | 影响状态连续性和重复处理 |
| 状态管理 | 运行框架层或能力链路层 | 运行态归运行框架，语义态归能力链路 |
| 工具选择 | 能力链路层 | 影响是否使用了正确能力 |
| 自我修正 | 业务效果层 + 能力链路层 | 影响最终质量和重试路径 |

## 3. 定义目标和指标

每次评测都需要覆盖：

1. **运行目标**：能否稳定运行。
2. **能力目标**：内部链路能否产出可用中间结果。
3. **业务目标**：是否解决用户或业务问题。
4. **唤起目标**：agent 能否正确选择这个 skill。
5. **必要性目标**：相比无 skill 基线，是否有质量或稳定性收益。

每个指标都要定义：

| 字段 | 含义 |
| --- | --- |
| 指标名 | 简洁中文名称 |
| 所属层级 | 固定三层之一 |
| 计算规则 | 明确公式或判断口径 |
| 数据来源 | 日志、JSON、trace、模型输出、人工标签 |
| 评判方式 | 代码、规则、模型、人工或混合 |
| 结果 | 实测值或 `待补测` |

同时检查文档与实现是否一致：

| 检查项 | 对比内容 |
| --- | --- |
| 职责覆盖 | `SKILL.md` 中声明的职责 vs 实际命令/脚本是否覆盖 |
| 命令覆盖 | 常用命令 vs 已声明的全部职责 |
| 副作用覆盖 | 写回、归档、发送等动作 vs 安全规则、身份和权限要求 |
| 可移植性 | 绝对本地路径、硬编码凭证、本地 CLI、命名兼容性 |

## 4. Dataset Split

Use three sets when the target will be improved over time:

| Set | Purpose | Equivalent Idea |
| --- | --- | --- |
| 优化集 | tune rules, prompts, thresholds, behavior | training / development |
| 回归集 | rerun after every change to catch regressions | validation / regression |
| 保留集 | stage-gate acceptance; use sparingly | test / holdout |

Suggested ratios by skill stage:

| Stage | 优化集 | 回归集 | 保留集 |
| --- | --- | --- | --- |
| 起步阶段 | 50%-60% | 25%-35% | 10%-15% |
| 成长期 | 35%-45% | 35%-45% | 15%-20% |
| 稳定应用阶段 | 20%-30% | 50%-60% | 15%-25% |
| 高风险上线前 | 15%-25% | 50%-60% | 20%-30% |

Split rules:

- Use stratified splitting when sample types differ meaningfully.
- Keep edge cases and known failures in the regression set after they are confirmed.
- Keep holdout labels and samples stable; do not tune against them repeatedly.
- If the sample pool is small, explain the limitation and emphasize qualitative failure analysis.

## 5. Closed Loop

The evaluation ends only after findings are deposited into durable artifacts:

| Deposit | Where It Goes |
| --- | --- |
| all samples | dataset directory |
| optimization set | `optimization_set.*` |
| regression set | `regression_set.*` |
| holdout set | `holdout_set.*` |
| disagreement samples | report table and dataset |
| judging rubric | report or skill references |
| skill fixes | optimization plan, issues, or PR |
| invocation prompts | invocation eval set |
| no-skill baseline | baseline artifact |

## 6. 静态评测模式

Use static mode when the target skill has no safe executable entrypoint, or when the user only provides a skill repository and does not authorize live external actions.

Static mode still produces a full report. It should not pretend to have tested live output quality.

| Step | What To Do | Result Label |
| --- | --- | --- |
| metadata validation | validate `SKILL.md` frontmatter and skill name/description | `已测` |
| file inventory | read `SKILL.md`, references, scripts, README, agents metadata | `已测` |
| workflow coverage | check whether the skill describes inputs, outputs, boundaries, workflow, quality checks | `已测` |
| dependency and safety review | identify external tools, credentials, live writeback, destructive actions | `已测` |
| invocation design | design positive/negative/similar-skill prompts | `待补测` unless router logs are run |
| live output quality | run the skill on a realistic task and judge output | `待补测` unless actually run |
| no-skill baseline | compare against general-model behavior | `待补测` unless actually run |

In static mode, the `测试结果` section should contain:

- `本轮执行概览`
- `静态结构检查`
- `可唤起性与存在必要性检查`
- `发现的问题`
- optional `待补测计划`
