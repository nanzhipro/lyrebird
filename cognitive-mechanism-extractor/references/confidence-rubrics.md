# 置信度评估 Rubrics(按需加载)

> 用于阶段 4 / 5 / 6 中对 mechanism card 的置信度打分。
> 这是评估锚点,不是论文级精度。

## 1. 总公式

`confidence_final = 0.30 × event_richness + 0.25 × cross_context_repetition + 0.20 × outcome_linkage + 0.15 × candidate_recognition + 0.10 × counterevidence_pass`

每项打分 0-1。

## 2. event_richness(事件丰富度,权重 0.30)

| 分数 | 标准 |
|---|---|
| 1.0 | ≥ 4 张正向 evidence,覆盖 ≥ 3 个独立事件 |
| 0.8 | 3 张 evidence,覆盖 2-3 个事件 |
| 0.6 | 2 张 evidence,2 个事件 |
| 0.4 | 2 张 evidence 但仅 1 个事件 |
| 0.2 | 1 张 evidence |
| 0.0 | 仅来自简历推断,无访谈证据 |

## 3. cross_context_repetition(跨情境复现,权重 0.25)

| 分数 | 标准 |
|---|---|
| 1.0 | 覆盖 ≥ 3 种不同情境(不同公司/角色/时期/技术栈) + 候选人能讲出迁移测试调整 |
| 0.8 | 覆盖 2 种情境 + 候选人在迁移测试中表现具体 |
| 0.6 | 覆盖 2 种情境但候选人迁移测试模糊 |
| 0.4 | 仅 1 种情境的多个事件 |
| 0.2 | 候选人在迁移测试中表示"不会迁移" |
| 0.0 | 完全不迁移,纯情境绑定的"做法" |

## 4. outcome_linkage(结果关联度,权重 0.20)

| 分数 | 标准 |
|---|---|
| 1.0 | 至少 2 张证据卡的 outcome 字段有明确(候选人说出的)结果,且与机制因果链清晰 |
| 0.8 | 1 张证据卡有候选人说出的明确结果,其他为定性 |
| 0.6 | 所有 outcome 都是定性描述,但候选人能讲出"为什么这个动作产生了这个结果" |
| 0.4 | 结果模糊,但候选人坚持有效 |
| 0.2 | 候选人不确定结果,或机制与结果之间因果链断裂 |
| 0.0 | 无法关联结果 |

**注意**: 候选人量化的结果可以提分(若候选人自己说出"提升 30%"),但 AI **绝不**伪造量化。

## 5. candidate_recognition(候选人识别感,权重 0.15)

| 分数 | 标准 |
|---|---|
| 1.0 | 候选人主动说"这就是我做事的方式"、能复述、能改名时倾向于保留核心 |
| 0.8 | 候选人确认认可,复述基本一致 |
| 0.6 | 候选人说"有点像但 ___",经过 1 次改写后认可 |
| 0.4 | 候选人勉强同意,复述卡壳 |
| 0.2 | 候选人说"不太像我" |
| 0.0 | 候选人明确否决"这不是我" |

**硬规则**: 得分 < 0.4 → 机制不进入报告。

## 6. counterevidence_pass(反证通过度,权重 0.10)

| 分数 | 标准 |
|---|---|
| 1.0 | 候选人主动讲出 ≥ 1 个反例 + ≥ 1 个失效场景 + ≥ 1 个过度使用风险 |
| 0.8 | 候选人讲出反例 + 失效场景 |
| 0.6 | 候选人讲出反例或失效场景之一 |
| 0.4 | 候选人在 AI 提示下选择 failure 假说 |
| 0.2 | 候选人坚持"没有失效场景",但勉强同意一个常见过度使用风险 |
| 0.0 | 候选人完全拒绝承认任何边界 |

**硬规则**: 得分 < 0.4 → 机制不能升格为 validated,最多 strong-hypothesis。

## 7. 发展阶段(development_stage)的判定

按 `confidence_final` 与硬条件组合:

| stage | 必要条件 |
|---|---|
| **validated** | `confidence_final ≥ 0.75` AND 所有 5 项分数都 ≥ 0.6 AND 候选人识别度 ≥ 0.8 AND 至少 1 个失效场景 AND 至少 3 张 evidence |
| **strong-hypothesis** | `confidence_final ≥ 0.55` AND 候选人识别度 ≥ 0.6 AND 完成至少 1 个迁移测试 |
| **weak-signal** | `confidence_final < 0.55` 或不满足上述条件 |

## 8. 全局总置信度(confidence_overall)

`confidence_overall = mean(每个 validated 机制的 confidence_final)`

若没有 validated 机制:
- 仅有 strong-hypothesis → `confidence_overall = max(strong-hypothesis 的 confidence_final) × 0.8`
- 仅有 weak-signal → `confidence_overall ≤ 0.4`,报告必须明确写"萃取未达到稳定档"

## 9. AI 在打分时的禁忌

- ❌ 不要为了让机制"看起来高置信"而调高分数。
- ❌ 不要把候选人含糊的 "嗯" 当作 candidate_recognition: 1.0。
- ❌ 不要把简历自评直接转成 outcome_linkage 加分。
- ❌ 不要因为机制名好听就提高分数。

## 10. 打分自检

每完成一个 mechanism card 的打分,在内部确认:

- [ ] 5 项分数是否分别有依据?(每项写出 1 句理由)
- [ ] confidence_final 的计算是否完整应用公式?
- [ ] development_stage 是否符合硬条件?
- [ ] 若 candidate_recognition < 0.4,这个机制是否已经从报告中删除?
- [ ] 若 counterevidence_pass < 0.4,development_stage 是否已经降级?

## 11. 示例(虚构)

机制 M-001 的打分:

- event_richness = 0.8(3 张 evidence,覆盖 2 个事件)
- cross_context_repetition = 0.8(2 种情境 + 候选人迁移测试具体)
- outcome_linkage = 0.6(定性,但候选人能讲因果)
- candidate_recognition = 1.0(候选人主动说"就是我")
- counterevidence_pass = 0.6(候选人讲了 1 个失效场景)

`confidence_final = 0.30×0.8 + 0.25×0.8 + 0.20×0.6 + 0.15×1.0 + 0.10×0.6 = 0.24 + 0.20 + 0.12 + 0.15 + 0.06 = 0.77`

阶段判定: `confidence_final ≥ 0.75 ✓ 5 项都 ≥ 0.6 ✓ 候选人识别 ≥ 0.8 ✓ 有失效场景 ✓ 3 张 evidence ✓` → **validated**
