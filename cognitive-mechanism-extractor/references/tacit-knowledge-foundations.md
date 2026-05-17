# 默会知识理论基础(按需加载)

> 仅在需要解释 "为什么这样设计" 或被候选人质疑方法学时加载。常规流程中不预加载。

## 1. Polanyi — 默会维度的优先性

> "我们所知道的,常常多于我们所能说出来的。" — Michael Polanyi, *The Tacit Dimension*

工作中真正高价值的能力,常常无法用 "我擅长 X" 这种显性语言完整表达。默会知识(tacit knowledge)是显性知识(explicit knowledge)的根基,不是其补充。

**对本 Skill 的启发**:
- 简历是显性化失败的产物 — 候选人努力把默会过程表述出来,但语言层永远 lossy。
- AI 的任务不是 "替候选人改简历",而是 "帮候选人把默会过程从沉默里拉出来"。

## 2. Nonaka — SECI 与 externalization

Nonaka 把 tacit↔explicit 的转化分成 4 个模式(SECI 模型):

| 模式 | tacit/explicit | 例子 |
|---|---|---|
| **Socialization** | tacit → tacit | 师傅带徒弟,在做中学 |
| **Externalization** | tacit → explicit | 用比喻、故事、类比把默会过程说出来 |
| **Combination** | explicit → explicit | 把多份文档/数据整合成新框架 |
| **Internalization** | explicit → tacit | 学了一套方法,变成肌肉记忆 |

**对本 Skill 的启发**:
- 本 Skill 的核心动作是 externalization。
- Externalization 依赖 "有意义的对话",不是问卷或量表。
- AI 不能替候选人完成 externalization,只能用合适的问题让候选人自己外化。

## 3. Brown & Duguid — 正式描述 vs 实际实践

> "组织的正式描述、培训手册、岗位说明书,常常与人们真实完成工作的方式相差很大。"

很多有效的工作能力,是通过 **community of practice 的叙事** 而不是文档保存的。意味着:

- **简历的职责描述**(canonical) ≠ 真实工作机制(actual practice)
- **候选人的"故事"** 比 "我的能力" 更接近机制本身
- **岗位话术** 是失真最严重的部分

**对本 Skill 的启发**:
- 提问要打破岗位话术,优先让候选人讲 "你做了什么不是你岗位说明里的事"。
- 候选人讲故事时不要打断逻辑,先记下,再做拆解。

## 4. Eraut — 专业工作中的 tacit 元素

Eraut 把专业工作中的 tacit knowledge 拆成 3 类:

| 类型 | 描述 |
|---|---|
| **Tacit understanding of people and situations** | 对人、情境的默会理解(比如"这个客户接下来要说什么") |
| **Routinized actions** | 被熟练化的动作,不再需要意识思考(比如"看到 stack trace 直接知道哪一层") |
| **Tacit rules underpinning intuitive judgment** | 支撑直觉判断的隐性规则(比如"这种 PR 必须 review 两次") |

**对本 Skill 的启发**:
- 证据卡的 `critical_cues` 字段抓 "tacit understanding"。
- 证据卡的 `decision_rule` 字段抓 "tacit rules"。
- 证据卡的 `action_taken` 字段抓 "routinized actions"。

## 5. Sternberg — Practical Intelligence

Sternberg 关于 practical intelligence 的研究指出: 真正与工作绩效相关的,往往是那些在真实情境里习得、但从未被正式教过的 **practical know-how**。这些 know-how 通常以 tacit knowledge 的形式存在。

**对本 Skill 的启发**:
- 萃取重点不是教科书能力(formally taught),而是工作场景里的 practical know-how。
- 不要追求 "全能力素描",优先抓住 2-4 条真正有迁移性的 practical know-how。

## 6. 方法学共识: CTA / CDM / CIT

| 方法 | 起源 | 核心 | 对本 Skill 的用法 |
|---|---|---|---|
| **Flanagan CIT (1954)** | 二战飞行员训练 | 收集对结果有正负影响的"关键事件" | 阶段 3 选事件 |
| **Klein CDM (1989+)** | 消防员/急诊医生决策研究 | 用 probe 重建关键决策点 | 阶段 3 深描 |
| **Cognitive Task Analysis** | 工业训练 / 教育 | 三阶段: elicitation → analysis → representation | 整个 Skill 的骨架 |
| **Recent Case Walkthrough** | CDM 简化版 | 进入领域、建立 rapport | 阶段 3 第一轮 |
| **Knowledge Audit** | CDM 衍生 | 抓 big picture、working smart、critical elements | 阶段 3 收尾 |

详细方法见 [elicitation-methods.md](elicitation-methods.md)。

## 7. 反对的几种常见误区

| 误区 | 本 Skill 的立场 |
|---|---|
| "默会知识可以一次性问出来" | 错。需要多个 probe + 跨事件归纳。 |
| "默会知识就是经验" | 错。经验 ≠ 可迁移机制。 |
| "STAR 法则就够了" | 不够。STAR 是叙事框架,不是认知机制萃取框架。 |
| "测试人格就能知道一个人的认知特征" | 错。人格测验跨场景预测力弱,机制萃取依赖具体事件。 |
| "AI 可以直接从简历推断机制" | 错。简历是 lossy 投影,事件才是证据本体。 |

## 8. 进一步阅读建议

候选人若追问理论来源,可以推荐:

- Polanyi, M. (1966). *The Tacit Dimension*. Routledge.
- Nonaka, I., & Takeuchi, H. (1995). *The Knowledge-Creating Company*. Oxford University Press.
- Brown, J. S., & Duguid, P. (1991). Organizational learning and communities-of-practice. *Organization Science*.
- Eraut, M. (2000). Non-formal learning and tacit knowledge in professional work. *British Journal of Educational Psychology*.
- Klein, G. (1998). *Sources of Power: How People Make Decisions*. MIT Press.
- Flanagan, J. C. (1954). The Critical Incident Technique. *Psychological Bulletin*.
- Crandall, B., Klein, G., & Hoffman, R. R. (2006). *Working Minds: A Practitioner's Guide to Cognitive Task Analysis*. MIT Press.
