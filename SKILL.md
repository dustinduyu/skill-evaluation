---
name: skill-eval-report
description: Generate evidence-based Skill or Agent evaluation reports from a target SKILL.md, local repo, logs, traces, datasets, and reference methodology. Use when asked to evaluate a skill, complex skill, agent-like workflow, or combination of skills; design eval metrics, run or summarize tests, compare with no-skill baselines, assess skill invocation/discoverability, choose code/model/human evaluation methods, and produce a structured report for Feishu, Markdown, or another document system.
---

# Skill Eval Report

Use this skill to evaluate a Skill / Agent / complex skill chain and produce a concise, evidence-based evaluation report. The report should be useful both as a decision document and as a reusable eval artifact for future regressions.

## Non-negotiables

- Do not invent test data. If a result was not measured, mark it as `待补测` or `间接证据`, and explain what must be run next.
- Always inspect the target skill files and relevant code before designing metrics.
- Always use the fixed three-layer architecture: `运行框架层`、`能力链路层`、`业务效果层`.
- Always include two Skill-specific gates:
  - `可唤起性`: whether the skill can be correctly selected when needed.
  - `存在必要性`: whether the skill adds value over a no-skill / general-model baseline.
- Do not collapse everything into a weighted total score unless the user explicitly asks and accepts the tradeoff.
- If writing to Feishu or another live doc, prefer local section edits and preserve comments; avoid whole-document replacement.

## Workflow

1. **Collect context**
   - Read the target `SKILL.md`, referenced files, scripts, logs, trace/metrics files, test artifacts, and any existing report.
   - If a methodology doc or reference report is provided, read it first and use it as the structural baseline.
   - If the target is a combination of skills, identify the chain and dependencies.

2. **Understand the object**
   - Summarize what the skill does, its inputs, outputs, dependencies, and boundaries.
   - Separate actual responsibilities from adjacent modules such as publishing, notification, storage, or UI.
   - If the skill behaves like an agent, map planning, memory, state, tool choice, and self-correction into the fixed three layers.

3. **Build the three-layer model**
   - `运行框架层`: environment, credentials, permissions, API calls, logs, costs, latency, file outputs, router/invocation behavior.
   - `能力链路层`: internal capability chain, data transformation, schema integrity, tool orchestration, state handoff.
   - `业务效果层`: final task quality, relevance, accuracy, user value, false positives/negatives, business tradeoffs.

4. **Define goals and metrics**
   - Translate each layer into observable metrics and calculation rules.
   - Include `可唤起性` metrics such as target-prompt hit rate, negative-prompt non-trigger rate, and similar-skill interference accuracy.
   - Include `存在必要性` metrics comparing with a no-skill baseline on quality, stability, reproducibility, and artifact reuse.
   - For metrics, specify data source, formula, judge type, and interpretation.

5. **Design the test flow**
   - Specify data sources, preparation, filtering, dataset split, judging method, execution plan, and failure feedback.
   - Use `优化集 / 回归集 / 保留集` when repeated improvement is expected.
   - Select judge types by scenario: code judge for deterministic artifacts, rule judge for clear criteria, model judge for semantic triage, human judge for gold labels and business tradeoffs.

6. **Run or reconstruct tests**
   - Prefer running the target skill locally when safe and authorized.
   - Capture exact commands, exit codes, output files, cost, latency, and sample-level results.
   - If using historical logs only, state the evidence source clearly and avoid presenting reconstructed analysis as a new run.
   - If the target is a documentation/workflow skill with no executable entrypoint, run a static evaluation mode: inspect files, validate skill metadata, check workflow and template coverage, and mark live output quality as `待补测`.

7. **Analyze results**
   - Report measured data first, then interpretation.
   - Include disagreement samples when rules, model judgment, or human labels diverge.
   - Convert failures into eval assets and skill optimization items.

8. **Write the report**
   - Use the report structure in [report-template.md](references/report-template.md).
   - Keep the report readable for non-eval readers: open each major section with a short quote-style explanation.
   - Use tables for metrics, steps, dataset splits, sample details, and optimization items.
   - Use diagrams only when they clarify architecture or flow; diagrams should follow the same logic as the text.

## Reference Files

- Read [workflow.md](references/workflow.md) for the full methodology and data-split guidance.
- Read [metrics-and-judging.md](references/metrics-and-judging.md) when defining metrics, judges, and sample-level outputs.
- Read [report-template.md](references/report-template.md) before writing the final report.
- Read [feishu-output.md](references/feishu-output.md) when publishing or editing a Feishu document.
- Read [reference-sample-notes.md](references/reference-sample-notes.md) when you need a compact example of how the `wechat_source + content_scoring` report applied the method.

## Output Contract

The final report must include:

- Evaluation object and evidence scope
- Three-layer decomposition
- Goals and metrics, including `可唤起性` and `存在必要性`
- Test flow with step outputs
- Dataset preparation and split rationale
- Judge selection rules
- Measured results
- Failure/disagreement analysis
- Skill optimization directions
- Reproducible artifacts and references

For static-only evaluations, still produce the complete report structure. Put measured static findings under `已测`, mark unrun live behavior under `待补测`, and include the exact follow-up test needed.

If the user asks for a new live document, create or update it and return the link. If the user asks for a local artifact, write Markdown under the requested repo or working directory.
