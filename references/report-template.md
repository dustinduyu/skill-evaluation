# 评估报告模板

使用这个结构产出简洁报告。除非用户要求深度附录，否则控制在管理者 7-8 分钟可读完的长度。

## 执行摘要

> 本节先给结论：这个 skill 是否可运行、是否有价值、主要问题是什么、下一步应该怎么改。

包含一张 5-8 行的关键结论表：

| 结论 | 证据 | 对 skill 的含义 |
| --- | --- | --- |
| 链路可以稳定跑通 | 真实运行证据 | 可以进入回归评测 |
| 质量有主要风险 | 分歧样本或失败数据 | 优化特定步骤 |
| 可唤起性待测 / 已测 | 唤起证据 | 优化 name / description 或路由 |
| 存在必要性待测 / 已测 | 无 skill 基线证据 | 证明或修正 skill 价值 |

## Skill 理解与分层

> 本节说明评测前先理解了什么：skill 做什么、边界在哪里、按三层如何拆。

包含：

- 评测对象
- 核心链路
- skill 职责
- 边界
- 三层表

## 评测目标与指标体系

> 本节回答“到底评什么”。每个目标都要落到可观测指标和计算规则。

包含：

- 评测目标
- 指标表，列为：指标 / 所属层级 / 计算规则 / 本轮结果
- 指标选择原则

必备指标：

- 成功率 / 错误 / 耗时 / 成本
- Schema 或中间产物完整性
- 任务质量
- 分歧或失败数量
- 可唤起性
- 存在必要性

## 评测流程设计

> 本节说明评测如何形成闭环：从理解对象，到沉淀 eval 资产和 skill 优化方案。

包含：

- 闭环流程
- 沉淀什么、沉淀到哪里
- 失败点如何进入后续 eval

## 测试方法与执行流程

> 本节说明这次评测怎么执行：数据从哪里来，如何准备，怎么拆分，什么时候用哪种评判方式。

包含：

- 必要时加入流程图
- 步骤表，列为：步骤 / 具体动作 / 对应层级 / 输出物
- 数据集拆分依据
- 评判方式选择表

## 测试结果

> 本节只展示本轮真实测试得到的数据、现象和归因。

Recommended subsections:

1. `本轮执行概览`
2. `可唤起性与存在必要性检查`
3. `样本池构建与拆分结果`
4. `端到端测试`
5. `回归样本测试`
6. `分歧样本明细`

If a subsection has no measured data, keep it but mark as `待补测` and explain the required next run.

For static-only evaluations of documentation/workflow skills, use these result subsections instead:

1. `本轮执行概览`
2. `静态结构检查`
3. `可唤起性与存在必要性检查`
4. `发现的问题`
5. `待补测计划`

Do not include fake endpoint, cost, latency, or sample-quality data when no live run occurred.

## Skill 优化方向

> 本节把评测结果反哺回 skill。每条优化方向都应能追溯到数据或失败样本。

Use a table:

| 优化项 | 来源证据 | 建议修改 | 验证方式 |
| --- | --- | --- | --- |

Include invocation and no-skill baseline follow-ups when relevant.

## 产物与复现位置

> 本节用于复现和后续接着做优化。

Include paths or links for:

- raw logs
- raw outputs
- full dataset
- optimization/regression/holdout sets
- judge outputs
- disagreement samples
- generated report
- optimization plan

## 出处与参考

Include methodology and external references when the report is guiding future practice.
