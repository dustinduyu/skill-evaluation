# 指标与评判方式

定义可测量评估标准时，使用这份说明。可唤起性和存在必要性参考 skill 评测中“是否能正确调用 skill”和“有无 skill 对照组”的思路；模型评判输出结构参考 evaluator 的 key / score / comment 形式。[1][5]

## Skill 专项目标

### 可唤起性

这一项回答：agent 在需要时能不能找到并选中这个 skill。[1]

可测指标：

| 指标 | 计算规则 / 判断口径 |
| --- | --- |
| 目标提示词命中率 | 应触发且实际触发的提示词数 / 全部正例提示词数 |
| 负例不误触率 | 不应触发且实际未触发的提示词数 / 全部负例提示词数 |
| 相似 skill 干扰正确率 | 存在相似 skill 时，正确选择目标 skill 或目标链路的次数 / 全部干扰提示词数 |
| 描述清晰度 | 人工或模型判断 `name` 和 `description` 是否暴露了正确使用场景 |

提示词类型：

- 直说型：明确要求使用该 skill
- 隐含型：只描述任务，不说 skill 名称
- 相似 skill 干扰型：多个相近 skill 都可能匹配
- 负例型：出现相似词，但任务不在本 skill 范围内

### 存在必要性

这一项回答：为什么要把能力沉淀成 skill，而不是依赖通用模型临场处理。[1]

将目标 skill 与无 skill 基线对比：

| 维度 | 对比内容 |
| --- | --- |
| 质量 | 任务正确性、相关性、完整性 |
| 稳定性 | 多次运行结果波动 |
| 可复现性 | 是否能基于产物复现输出 |
| 效率 | 耗时、成本、人工复核负担 |
| 资产沉淀 | 数据集、评判口径、回归样本、日志 |

如果没有跑无 skill 基线，标为 `待补测`，不要写成确定性提升。

## 评判方式选择

| 场景 | 首选评判方式 | 辅助评判方式 |
| --- | --- | --- |
| 命令成功、文件生成、Schema、数值指标 | 代码评判 | 无或人工抽查 |
| 确定性业务规则 | 规则评判 | 模型抽查 |
| 语义相关性或模糊质量 | 模型评判 | 人工评判 |
| 有争议标签、金标、业务取舍 | 人工评判 | 模型理由 |
| skill 唤起 / 路由 | 路由日志或 trace 评判 | 人工复核 |
| 无 skill 对照 | 成对评测 | 人工复核 |

模型评判输出应结构化。建议至少包含：

| 字段 | 含义 |
| --- | --- |
| `key` | 指标名或判断维度 |
| `score` | 数值分或分类标签 |
| `comment` | 简洁理由，包含证据 |
| `confidence` | 可选置信度标签 |
| `needs_human_review` | 模糊或高影响样本标为 true |

这个结构参考 LangSmith 等 evaluator 框架中可聚合的 key / score，以及便于人工复核的解释性 comment。[5]

## 分歧分析

当不同评判方式不一致时，建立分歧样本表。

推荐字段：

| 字段 | 说明 |
| --- | --- |
| 样本 ID | 稳定 ID |
| 数据集 | 优化集 / 回归集 / 保留集 |
| 样本类型 | 直接相关、边界样本、强排除等 |
| 规则结果 | 输出媒介支持时使用醒目标识 |
| 模型结果 | 尽量使用醒目标识 |
| 分歧方向 | 规则低估 / 规则高估 / 边界不清 |
| 标题或输入 | 简洁原始输入 |
| 分歧理由 | 为什么发生分歧 |
| 人工结果 | 未复核时留空 |
| 人工备注 | 未复核时留空 |

解释口径：

- `规则低估`：规则判无关或低分，语义评判认为相关。
- `规则高估`：规则判相关或高分，语义评判认为无关或较弱。
- `边界不清`：两种判断都有理由，需要业务策略定夺。

## 结果呈现

有实测结果时优先展示实测值。未测项使用以下标签：

- `已测`：本轮已经测得
- `间接证据`：有产物支持，但不是受控测试
- `待补测`：尚未测量
- `人工待复核`：需要人工金标判断

不要把不确定性藏在长段文字里。

报告首页需要把运行框架层和能力链路层的关键检查项做成状态总览。状态建议使用：

| 状态 | 使用场景 |
| --- | --- |
| `✅` | 本轮有直接证据证明满足 |
| `❌` | 本轮有直接证据证明不满足 |
| `部分满足` | 关键路径可用，但有边界、稳定性或覆盖不足 |
| `待补测` | 尚无直接证据，不能下结论 |

状态总览至少覆盖：

- 可唤起性 / 路由选择
- 运行成功率
- 成本、耗时、错误可观测性
- 中间产物完整性
- 工具调用和状态交接
- 评判结果可复现性

## 数据记录表

评判输出必须保存到可复用的数据记录表。前期可以是 CSV / JSONL / Markdown 表，后续可以升级为 SQLite、LangSmith、飞书多维表格或正式数据库。

推荐拆成三类记录：

| 表 | 粒度 | 典型字段 |
| --- | --- | --- |
| `eval_runs` | 一次评测运行 | run_id、命令、环境、数据版本、开始时间、结束时间、总耗时、总成本、输出目录 |
| `eval_cases` | 一条样本或一个任务 | run_id、case_id、dataset_split、input_ref、expected_ref、actual_output_ref、artifact_path、error |
| `eval_metrics` | 一个指标结果 | run_id、case_id、layer、metric_key、judge_type、score、pass_fail、comment、review_status |

如果只维护一个表，至少包含：

| 字段 | 说明 |
| --- | --- |
| `run_id` | 本次运行 ID |
| `case_id` | 样本 ID |
| `dataset_split` | optimization / regression / holdout / ad_hoc |
| `input_ref` | 输入文本或文件路径 |
| `expected_ref` | reference output 或人工金标路径 |
| `actual_output_ref` | 实际输出路径 |
| `layer` | 三层之一 |
| `metric_key` | 指标名 |
| `judge_type` | code / rule / llm / human / mixed |
| `score` | 数值分或分类标签 |
| `pass_fail` | pass / fail / partial / pending |
| `comment` | 简洁解释 |
| `artifact_path` | 日志、trace、截图或输出文件路径 |
| `review_status` | unreviewed / reviewed / needs_human |

## 静态评测结果标签

对于没有可执行脚本的文档型 / 工作流型 skill：

| 项目 | 标记 |
| --- | --- |
| frontmatter 校验 | `已测` |
| reference / template 覆盖 | `已测` |
| 安全边界检查 | `已测` |
| 没有 router 日志的 skill 唤起行为 | `待补测` 或 `间接证据` |
| 没有真实任务运行的最终输出质量 | `待补测` |
| 没有无 skill 基线的存在必要性 | `间接证据` + `待补测` |

## Metadata 兼容性

校验目标 skill 时，要区分“不可用”和“可移植性风险”：

| 发现 | 报告方式 |
| --- | --- |
| 严格 validator 因 `name` 含下划线或大写而失败 | 除非当前 runtime 明确拒绝，否则标为 `可移植性风险` |
| 缺少 `description` | 标为 `阻塞问题`，因为 skill 唤起依赖它 |
| description 较宽泛但基本合理 | 标为唤起风险，并补相似 skill 测试 |
| 命令里有本地绝对路径 | 标为可移植性风险，并建议参数化或补安装说明 |

不要只因为某个 validator 的命名规则更严格，就断言该 skill 在当前环境不可用。

## 出处与参考

1. LangChain, Evaluating Skills：https://www.langchain.com/blog/evaluating-skills
1. LangSmith, Code evaluator：https://docs.langchain.com/langsmith/code-evaluator
