# Reference Sample Notes

These notes summarize the reusable pattern from the `wechat_source + content_scoring` evaluation report.

## What The Sample Did Well

- Treated two dependent skills as one evaluated chain.
- Kept the fixed three layers:
  - `运行框架层`: API key, WeChat API, logs, output directory, cost, latency.
  - `能力链路层`: article retrieval, backfill, full-text detail, cleaning, scoring, sorting.
  - `业务效果层`: whether useful AI news was identified, false positives, false negatives, boundary cases.
- Used real execution evidence:
  - four stages ran successfully
  - full-text detail improved relevant detection from 1/3 to 2/3
  - 46 raw candidates became 44 deduplicated samples
  - 16 disagreement samples were deposited for review
- Avoided a weighted overall score.
- Converted findings into concrete skill optimizations.

## Added Methodology Alignment

The updated sample also added:

- `可唤起性`: skill name/description, target prompts, similar skill interference, negative prompts.
- `存在必要性`: compare with no-skill baseline; if not run, mark as `待补测`.
- stage-aware dataset split: the evaluated chain was treated as `成长期`, so optimization and regression were both important.
- judge selection rules: code/rule/model/human each used for different problem types.
- model judge output aligned with key / score / comment style.

## Reusable Report Shape

使用这个顺序：

1. 执行摘要
2. Skill 理解与分层
3. 评测目标与指标体系
4. 评测流程设计
5. 测试方法与执行流程
6. 测试结果
7. Skill 优化方向
8. 产物与复现位置
9. 出处与参考

## Useful Caution

If the report adds new methodology requirements after a test was already run, do not retrofit fake results. Add a clear `补充检查` section and mark items as `待补测` or `间接证据`.
