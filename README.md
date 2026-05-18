# Skill Eval Report

`skill-eval-report` 是一个用于生成 Skill / Agent 评估报告的 Codex / OpenSkills skill。

它会根据目标 `SKILL.md`、本地仓库、代码、日志、trace、metrics、测试产物和参考工作法，完成评测对象理解、三层拆解、指标设计、测试执行或复盘、失败归因，并输出一份结构化、可复用的评估报告。

## 适用场景

- 评测一个新的 skill 是否可用、是否值得保留。
- 评测一个复杂 skill 或组合 skill 的能力链路。
- 对接近 agent 的工作流做分层评估。
- 根据真实运行日志、测试数据或历史产物生成评估报告。
- 评估 skill 的可唤起性、存在必要性、无 skill 基线和失败回流。
- 输出 Markdown 或飞书文档可用的评测报告。

## 核心方法

评估报告固定使用三层架构：

| 层级 | 关注点 |
| --- | --- |
| 运行框架层 | 环境、凭证、权限、API、日志、成本、耗时、输出文件、可唤起性 |
| 能力链路层 | 数据准备、工具链、状态交接、Schema、字幕/正文/中间产物、流程串联 |
| 业务效果层 | 最终任务质量、相关性、准确性、误判/漏判、用户价值、存在必要性 |

每份报告都必须包含：

- 评测对象和证据范围
- 三层拆解
- 评测目标与指标
- 可唤起性评估
- 存在必要性 / 无 skill 基线评估
- 测试流程与执行结果
- 失败点、分歧样本和优化方向
- 可复现产物与参考来源

## 安装

将仓库克隆到 Codex / OpenSkills 可发现的 skills 目录中：

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/dustinduyu/skill-eval-report.git ~/.codex/skills/skill-eval-report
```

如果你的环境使用 `.agents/skills`：

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/dustinduyu/skill-eval-report.git ~/.agents/skills/skill-eval-report
```

重启 Codex 后即可使用。

## 使用示例

```text
使用 $skill-eval-report 评测这个本地 skill：
/Users/dustin/Documents/new_project/zaowang-ai-news/bilibili_source/SKILL.md
请实际运行可安全执行的测试，并输出 Markdown 评估报告。
```

```text
使用 $skill-eval-report，根据这份工作法和历史日志，评测某个组合 skill。
报告需要包含三层拆解、可唤起性、存在必要性、测试结果和优化方向。
```

```text
使用 $skill-eval-report，更新这篇飞书评估报告。
请保留评论，做局部章节更新，不要整篇覆盖。
```

## 目录结构

```text
SKILL.md
agents/openai.yaml
references/
  workflow.md
  metrics-and-judging.md
  report-template.md
  feishu-output.md
  reference-sample-notes.md
```

## 参考文件

- `workflow.md`：完整评测工作法、三层架构、数据集拆分和闭环沉淀。
- `metrics-and-judging.md`：指标设计、评判方式选择、静态评测标签、metadata 兼容性判断。
- `report-template.md`：评估报告结构模板。
- `feishu-output.md`：飞书文档创建和局部更新规范。
- `reference-sample-notes.md`：`wechat_source + content_scoring` 参考样例提炼。

## 设计原则

- 不编造测试数据；未测项必须标记为 `待补测` 或 `间接证据`。
- 优先读取目标 skill 和相关代码，再设计指标。
- 有可执行入口时尽量真实运行；没有可执行入口时使用静态评测模式。
- 不默认加权汇总总分，除非用户明确要求。
- 在线文档更新优先局部修改，尽量保留评论。

