# 阶段 5: 命名与反证 (naming-and-validation)

> 加载时机: 跨事件归并完成,至少 1 个 mechanism_draft 通过候选人识别度门槛。
> 阶段目标: 给每个机制完成"命名 + 迁移测试 + 失效场景 + 候选人复述"四件事,生成 mechanism_card。

## 核心原则

**命名是候选人的事,AI 只提供候选名**。即使 AI 觉得自己的命名更精确,候选人否决就必须改名。

## 命名前必读

**强制阅读**: [assets/naming-style-guide.md](../assets/naming-style-guide.md)

特别关注:
- 命名公式: `[认知动作] + [情境/约束] + [价值方向] + "机制"`
- 严禁清单: 人格词、临床词、空洞赞美词、招聘话术、IQ 词、命运词

## 步骤(对每个 mechanism_draft 重复执行)

### 步骤 1: AI 给出 2-3 个候选名

每个候选名都符合命名公式。**严禁**给出只有 1 个候选名,避免暗示候选人必须接受。

**对话脚本**:

> "针对 [模式 X],我想了 3 个候选名,你可以挑、改、推翻、或者提全新的。
>
> **A**. [候选名 A]
> **B**. [候选名 B]
> **C**. [候选名 C]
>
> 你更认哪一个?或者你想换成什么?"

### 步骤 2: 迁移测试(transfer test)

候选人选定/修改名字后,**强制**做迁移测试:

> "假设你的资源变化了:
> - 没有权威(不是负责人,是新人)
> - 资源砍掉一半
> - 时间压缩到 1/3
>
> 在这种情况下,你的这套做法还成立吗?如果不成立,会变成什么?"

**判定**:
- 候选人能讲出"我会怎么改"或"我大概率还是会先 ___" → 机制有迁移性,**confidence_breakdown.cross_context_repetition ≥ 0.7**
- 候选人答"那我就不这么做了" → 机制是情境绑定的,不算"机制"而是"做法",降级为弱信号或重写边界

具体追问模板见 [question-bank/transfer-probes.md](../question-bank/transfer-probes.md)。

### 步骤 3: 失效场景(failure mode)

**强制**问候选人:

> "有没有一次你照这个方式做,结果反而不好?或者你回头看会改的?"

**判定**:
- 候选人能讲出 1 个具体反例 → 记录为 `failure_modes`,并生成 counterevidence card(E-CE-XXX)
- 候选人讲不出 → 给候选人 3 个常见过度使用风险让其选(参考 [question-bank/contradiction-probes.md](../question-bank/contradiction-probes.md))
- 候选人完全没有失效场景 → **降级为"有力假设",不能进入"已验证机制"**

### 步骤 4: 反事实测试(counterfactual)

> "如果你的工作方式里**没有**这条机制,你在最近这个事件里会做出什么不同的判断?"

**判定**:
- 候选人能立刻举出反例 → 机制可信度高
- 候选人答不上来或讲了无关的事 → 机制不成立,**回到 [prompts/incident-deep-dive.md](incident-deep-dive.md) 补证据**

### 步骤 5: 候选人复述

> "用你自己的话,把这条机制讲给一个完全没听过你工作的同行听,你会怎么说?"

**判定**:
- 复述与 AI 命名差异 < 30% → 采纳 AI 命名或候选人改名版本
- 复述大幅改写 → **以候选人版本为准**,不强推 AI 命名
- 复述卡壳或乱讲 → **不放入最终报告**,本机制降级或重做

### 步骤 6: 生成 Mechanism Card

完整填写 [schemas/mechanism-card.schema.json](../schemas/mechanism-card.schema.json) 的所有必填字段。

**关键字段填写规则**:

| 字段 | 来源 |
|---|---|
| `working_name` | 候选人最终确认的名字(或 AI 候选名 + 候选人轻微修改) |
| `one_sentence_definition` | 候选人复述提炼,不超过 200 字符 |
| `core_pattern` | 跨事件归纳的核心模式描述,基于 ≥ 2 张 evidence card |
| `trigger_contexts` | 从 evidence cards 的 context 字段聚合 |
| `input_cues` | 从 evidence cards 的 critical_cues 字段聚合 |
| `internal_operation` | 候选人在 CDM probe 中讲到的认知动作 |
| `action_signature` | 候选人讲到的外显行为(他人可观察) |
| `value_created` | 候选人讲到的价值方向,或 AI 总结候选人确认 |
| `boundary_conditions` | **必填**,源自迁移测试 |
| `failure_modes` | **必填**,源自步骤 3 |
| `development_stage` | 按 [references/confidence-rubrics.md](../references/confidence-rubrics.md) 判定 |
| `evidence_ids` | 来源的正向证据卡 id |
| `counterevidence_ids` | 反证卡 id |
| `candidate_validation_note` | 候选人是否认可、改了什么、复述是否顺畅 |
| `confidence_breakdown` | 5 项分项打分(0-1) |
| `confidence_final` | 按公式合成 |

### 步骤 7: 置信度合成与状态判定

按公式:

`confidence_final = 0.30 × event_richness + 0.25 × cross_context_repetition + 0.20 × outcome_linkage + 0.15 × candidate_recognition + 0.10 × counterevidence_pass`

按 [references/confidence-rubrics.md](../references/confidence-rubrics.md) 决定 `development_stage`:

| stage | 条件 |
|---|---|
| `validated` | ≥ 3 张 evidence、覆盖 ≥ 2 事件、含 ≥ 1 反证或失效场景、候选人识别 ≥ 0.8 |
| `strong-hypothesis` | ≥ 2 张 evidence、有迁移测试、候选人识别 ≥ 0.6 |
| `weak-signal` | 只 1 个事件或证据均来自简历推断 |

### 步骤 8: 候选人最终确认

把完整的 mechanism card(以人类友好格式)呈现给候选人:

> "这是我们一起完成的机制 M-XXX:
> 名称: ___
> 一句话定义: ___
> 核心模式: ___
> 适用场景: ___
> 失效场景: ___
> 候选人识别度: ___
> 置信度: ___ (___ 档)
>
> 这版你认可吗?如果有任何地方你觉得不像你,我们重做。"

候选人不认可 → 回到对应步骤重做。
候选人认可 → 进入下一个 mechanism_draft,重复步骤 1-8。

### 步骤 9: 全部机制完成后,更新 session_state

```
session_state.current_stage = "report"
session_state.mechanism_cards = [...]
```

进入 [prompts/final-report-assembly.md](final-report-assembly.md)。

## 不要做的事

- ❌ 不要 AI 单方面命名后就写入报告。
- ❌ 不要给出只有 1 个候选名(暗示性强制)。
- ❌ 不要跳过迁移测试或失效场景。
- ❌ 不要把候选人含糊的 "嗯还行" 当作 "认可"。
- ❌ 不要在 failure_modes 写"暂无"或"不适用"。如果候选人讲不出,**降级**而不是留空。

## 自检清单

- [ ] 每个 mechanism card 的 working_name 符合命名指南?
- [ ] 每个 mechanism card 有至少 2 张 evidence_ids?
- [ ] 每个 mechanism card 的 failure_modes 不是空?
- [ ] 候选人对每个 working_name 是否亲口认可?
- [ ] 置信度公式是否完整应用,confidence_breakdown 5 项齐全?
