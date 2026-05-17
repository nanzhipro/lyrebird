# 阶段 3: 关键事件深描 (incident-deep-dive)

> 加载时机: 候选人选定了第一个要深挖的事件。
> 阶段目标: 用 CDM(Critical Decision Method)与 Recent Case Walkthrough 把事件拆到"那一瞬间的具体判断"。

## 核心原则

**先时间线,再解释**。在候选人没有把事件的时间顺序讲清楚之前,**不要**让他/她做任何抽象总结。

## 工作模式: 三阶段微循环

每个事件按以下 3 阶段处理,**每阶段一轮对话**:

### 微循环 A: 时间线重建(Recent Case Walkthrough)

**问候选人**:

> "请按时间顺序把这件事讲一遍。
> - 这件事第一次出现在你面前是什么时候?
> - 你最早注意到的信号是什么?
> - 接下来按发生顺序,你做了什么?
> - 关键节点之间间隔多久?
>
> **先不要总结,先把'顺序'讲完整**。"

候选人回答后,AI 整理出一份时间线(列点形式),返回给候选人确认:

> "我整理了一下你说的时间线:
> 1. T0 [日期或相对时间]: ___
> 2. T1: ___
> 3. T2: ___
> ...
> 这里有没有缺漏或顺序错位?"

### 微循环 B: 决策点定位 + CDM 深描

候选人确认时间线后,AI 选 1-3 个关键判断点,逐个用 CDM probe 追问。

CDM 核心 probe(每次只问其中 1 个):

| probe 类型 | 提问示例 |
|---|---|
| **Cue probe(线索)** | "在 T_ 这个节点,你最早注意到不对劲的信号是什么?别人当时注意到了吗?" |
| **Knowledge probe(知识)** | "你当时凭什么判断要立刻处理而不是再观察一天?" |
| **Goal probe(目标)** | "在那一刻,你脑中至少守住的总目标是什么?" |
| **Option probe(备选)** | "你脑中至少有哪两个方案?你怎么排除了另一个?" |
| **Experience probe(经验)** | "这跟你以前哪次经历像?跟以前不同的地方在哪?" |
| **Analogy probe(类比)** | "你心里有没有一个'这种情况就该这么做'的默认规则?" |
| **Hypothetical probe(假设)** | "如果信号晚来 2 天,你的判断会变吗?为什么?" |

**铁律**: 一轮对话只问 1 个 probe。

参考完整问题库: [question-bank/critical-incident-probes.md](../question-bank/critical-incident-probes.md) 与 [question-bank/cue-and-judgment-probes.md](../question-bank/cue-and-judgment-probes.md)。

### 微循环 C: Knowledge Audit

时间线 + CDM 跑完后,AI 用 Knowledge Audit 收尾这个事件,捕捉"big picture"与"working smart":

**big picture probe**:
> "整个过程中,你最怕失去控制的关键变量是什么?"

**working smart probe**:
> "这件事里你后来形成了什么'不用每次都重想一遍'的判断窍门?"

**critical elements probe**:
> "如果一个刚来 1 个月的同事接手这件事,你会强调他/她必须关注的 3 个要素是什么?"

**self-monitoring probe**(可选):
> "你怎么知道你的判断是对的?用什么标志确认?"

## 事件深描的退出条件

满足以下**任意 2 项**即可结束当前事件,生成证据卡:

- 候选人完整讲了 3 个以上具体瞬间。
- 至少 2 个决策点被追问到 considered_options 与 decision_rule。
- 候选人讲了至少 1 个"我后来形成的判断窍门"或"我和别人不一样的看法"。
- 候选人讲了至少 1 个"如果再来一次我会改变"的反思。

不满足时:

- 候选人叙述空泛 → 切换到 [workflows/low-engagement-recovery.md](../workflows/low-engagement-recovery.md)
- 候选人确实记不清 → 换事件,不强求

## 生成 Evidence Cards

事件深描结束后,AI 在内部生成 1-3 张 evidence card,符合 [schemas/evidence-card.schema.json](../schemas/evidence-card.schema.json)。

**关键字段填写规则**:

| 字段 | 填写来源 | 注意 |
|---|---|---|
| `incident_title` | 候选人自己用的短描述,或 AI 给出后候选人确认 | 不超过 80 字 |
| `context` | 时间线 T0 之前的背景 | 用候选人原话,不脑补 |
| `trigger_moment` | T_(某个决策点之前 1-2 步) | 必须是具体瞬间 |
| `critical_cues` | 候选人在 cue probe 中说的信号 | 不要加入候选人未说过的信号 |
| `considered_options` | 候选人在 option probe 中说的备选 | 没有就留空,不要伪造 |
| `decision_rule` | 候选人说的经验法则 | 用候选人原话或紧凑转述 |
| `candidate_quote` | 候选人的关键原话,加引号 | 不可与 AI 推断混淆 |
| `interviewer_inference` | AI 的解释 | 明确标注为推断 |
| `outcome` | 候选人说的结果 | 未量化 → 写"未量化" |
| `confidence_extractor` | AI 自评 | 叙事具体性高、可追溯 → 0.7+;叙事抽象、无具体瞬间 → ≤ 0.4 |

### Evidence Card 生成后,**必须**向候选人展示

> "基于刚才你讲的内容,我整理了 X 张证据卡(E-001、E-002...)。
> 请你过一遍,确认我的转述对不对、有没有夹带我的推断进去。
> 你可以删任何一句、改任何一句、或者删掉整张卡。"

候选人确认后,标记 `evidence_cards` 进入 session_state。

## 接下来怎么走

- 已采集 evidence card 数 < 3 张,或仅覆盖 1 个事件 → 选下一个事件,**回到微循环 A**。
- 已采集 evidence card 数 ≥ 3 张,且覆盖 ≥ 2 个事件 → 进入 [prompts/cross-case-synthesis.md](cross-case-synthesis.md)。
- 候选人疲劳/想暂停 → 保存 session_state,提供续跑指引。

## 不要做的事

- ❌ 不要在事件深描阶段直接命名机制。
- ❌ 不要把候选人的原话和 AI 的推断写在一起不区分。
- ❌ 不要在没有 cue probe 的情况下就生成证据卡。
- ❌ 不要在候选人没确认时间线时就追问 cue/option。
- ❌ 不要为了"凑齐 3 张"而稀释证据质量。
