# Metrics And Judging

Use this file when defining measurable evaluation criteria.

## Skill-Specific Gates

### 可唤起性

This answers: can an agent find the skill when needed?

Measure:

| Metric | Formula / Rule |
| --- | --- |
| 目标提示词命中率 | prompts that should trigger skill and do trigger / all positive prompts |
| 负例不误触率 | prompts that should not trigger skill and do not trigger / all negative prompts |
| 相似 skill 干扰正确率 | prompts with competing skills where the target or correct chain is selected / all interference prompts |
| 描述清晰度 | human or model assessment of whether `name` and `description` expose the right use cases |

Prompt categories:

- Direct: "use this skill to..."
- Indirect: describes the task without naming the skill
- Similar-skill interference: multiple nearby skills could match
- Negative: similar words but outside scope

### 存在必要性

This answers: why should this be a skill rather than general-model behavior?

Compare the target skill with a no-skill baseline:

| Dimension | Compare |
| --- | --- |
| quality | task correctness, relevance, completeness |
| stability | variation across repeated runs |
| reproducibility | whether outputs can be regenerated with artifacts |
| efficiency | latency, cost, manual review burden |
| asset creation | datasets, rubrics, regression samples, logs |

If the baseline is not run, mark it as `待补测`. Do not claim definitive lift.

## Judge Selection

| Scenario | Primary Judge | Secondary Judge |
| --- | --- | --- |
| command success, file generation, schema, numeric metrics | code judge | none or spot check |
| deterministic business rules | rule judge | model spot check |
| semantic relevance or fuzzy quality | model judge | human judge |
| disputed labels, gold data, business tradeoffs | human judge | model rationale |
| skill invocation / routing | router logs or trace judge | human review |
| no-skill comparison | paired evaluation | human review |

Model judge output should be structured:

| Field | Meaning |
| --- | --- |
| `key` | metric name or judgment dimension |
| `score` | numeric score or categorical label |
| `comment` | concise reason, with evidence |
| `confidence` | optional confidence label |
| `needs_human_review` | true for ambiguous or high-impact samples |

This mirrors the useful parts of evaluator frameworks such as LangSmith: a machine-aggregable key/score plus explanation for review.

## Disagreement Analysis

Create a table for samples where judges disagree.

Recommended columns:

| Column | Notes |
| --- | --- |
| sample id | stable id |
| dataset | optimization / regression / holdout |
| sample type | direct, boundary, exclusion, etc. |
| rule result | use colored labels if the output medium supports it |
| model result | use colored labels if possible |
| disagreement direction | rule underestimated / rule overestimated / boundary unclear |
| title or input | concise original input |
| rationale | why the disagreement happened |
| human result | leave blank if pending |
| human note | leave blank if pending |

Interpretation:

- `规则低估`: rule says irrelevant or low score, semantic judge says relevant.
- `规则高估`: rule says relevant or high score, semantic judge says irrelevant or weak.
- `边界不清`: both views are defensible; requires business policy.

## Reporting Results

Use measured results when available. Use these labels for unmeasured items:

- `已测`: measured in this run
- `间接证据`: supported by artifacts but not a controlled test
- `待补测`: not measured yet
- `人工待复核`: requires human gold judgment

Never hide uncertainty inside prose.

## Static Evaluation Result Labels

For workflow/documentation skills without executable scripts:

| Item | Label It As |
| --- | --- |
| frontmatter validation | `已测` |
| reference/template coverage | `已测` |
| safety boundary review | `已测` |
| skill invocation behavior without router logs | `待补测` or `间接证据` |
| final output quality without a real task run | `待补测` |
| existence necessity without a no-skill baseline | `间接证据` plus `待补测` |

## Metadata Compatibility

When validating a target skill, distinguish between "unusable" and "portable-risk":

| Finding | How To Report |
| --- | --- |
| strict validator fails because `name` contains underscores or uppercase | report as `可移植性风险` unless the current runtime demonstrably rejects it |
| missing `description` | report as `阻塞问题` because skill invocation depends on it |
| description is broad but plausible | report as invocation risk and add similar-skill tests |
| local absolute paths in commands | report as portability risk and suggest parameterization or setup notes |

Do not claim the skill cannot work solely because one validator uses stricter naming rules than the current deployment environment.
