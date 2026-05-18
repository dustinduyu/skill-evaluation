# Skill / Agent Evaluation Workflow

Use this file to design the evaluation before writing the report.

## 1. Understand The Target

Read:

- `SKILL.md`
- referenced files under `references/`, `scripts/`, `assets/`
- local code paths used by the skill
- existing logs, traces, metrics, datasets, prior reports
- user-provided methodology or reference samples

Capture:

| Field | What To Record |
| --- | --- |
| Target | Single skill, combined skills, or agent-like workflow |
| Core job | What the target is responsible for |
| Inputs | Prompt, file, dataset, API, upstream artifact |
| Outputs | Structured result, report, file, action, decision |
| Dependencies | APIs, keys, CLIs, models, repos, tools |
| Boundaries | What this skill does not own |

## 2. Use The Fixed Three Layers

Always use these three layers. If the target has agent-like capabilities, map them into these layers instead of adding a fourth layer.

| Layer | What It Observes | Example Metrics |
| --- | --- | --- |
| 运行框架层 | runtime, routing, permissions, API, logs, cost, latency, output files | success rate, error rate, cost, latency, invocation accuracy |
| 能力链路层 | data preparation, tool chain, state handoff, schema, intermediate outputs | schema completeness, step success, retrieval coverage, transformation consistency |
| 业务效果层 | final answer quality and task value | accuracy, relevance, false positives, false negatives, user usefulness, baseline lift |

Agent-like behavior mapping:

| Behavior | Put It Under | Why |
| --- | --- | --- |
| planning | 能力链路层 | It affects task decomposition and ordering |
| memory | 能力链路层 | It affects state continuity and repeated work |
| state management | 运行框架层 or 能力链路层 | Runtime state is framework; semantic state is chain |
| tool choice | 能力链路层 | It affects whether the right capability is used |
| self-correction | 业务效果层 plus 能力链路层 | It affects final quality and retry path |

## 3. Define Goals And Metrics

Every evaluation needs:

1. **Execution goals**: can it run reliably?
2. **Capability goals**: can its internal chain produce usable intermediate outputs?
3. **Business goals**: does it solve the user/business problem?
4. **Invocation goals**: can an agent correctly select this skill?
5. **Necessity goals**: does the skill outperform or stabilize a no-skill baseline?

For each metric, define:

| Field | Meaning |
| --- | --- |
| metric name | concise Chinese name |
| layer | one of the fixed three layers |
| formula | exact calculation or judgment rule |
| data source | log, JSON, trace, model output, human label |
| judge | code, rule, model, human, or hybrid |
| result | measured value or `待补测` |

Also check documentation-to-implementation alignment:

| Check | What To Compare |
| --- | --- |
| responsibility coverage | responsibilities in `SKILL.md` vs commands/scripts that actually execute them |
| command coverage | common commands vs all declared responsibilities |
| side-effect coverage | writeback/archive/send actions vs safety rules and identity/permission requirements |
| portability | absolute local paths, hardcoded credentials, local-only CLIs, naming compatibility |

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

## 6. Static Evaluation Mode

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
