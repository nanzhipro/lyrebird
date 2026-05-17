# Mechanism Card 示例(虚构)

> 本示例**完全虚构**,用于演示 mechanism card 的写法。
> 候选人原型为一份 20+ 年 Apple 端架构经验的虚构简历,事件细节为演示构造。

## 示例 1: M-001(validated)

```yaml
mechanism_id: M-001
working_name: 系统底层框架变更下的回滚路径前置机制
one_sentence_definition: |
  在底层系统能力变更或不稳定的场景下,先把"故障可回滚路径"搭出来,
  再放主功能上线,以确保即使最坏情况发生也能在客户端层面快速恢复。
mechanism_type: tradeoff
core_pattern: |
  在 [底层框架变更 / 不稳定 / 文档不全] 的场景下,
  候选人会先做 3 件事: (1) 评估回滚不可控的风险 → (2) 暂停主功能进度,前置回滚链路 → (3) 在回滚链路就绪后,主功能并行推进。
  这条模式跨 [早年电商直播大促支撑 / 中后期 PacketTunnel SASE 迁移 / 近期 ESF 接入] 三个场景反复出现。
trigger_contexts:
  - "底层系统框架被供应商标记为 deprecated 或在 minor version 上不稳定"
  - "客户端组件,事故无法服务端热修"
  - "影响面广(企业级、跨多客户、多版本)"
input_cues:
  - "供应商 release note 缺少关键已知问题"
  - "不可复现的偶发故障(意味着看不见我们看不见的)"
  - "下游客户分布在多个版本,无法统一升级"
internal_operation: |
  心智模型: "事故是必然的,关键是事故发生时还能不能回到上一个已知好状态"。
  动作序列: 识别回滚不可控信号 → 评估回滚链路设计成本 → 与项目方对齐进度风险 → 优先级反转。
action_signature: |
  外显特征: 在项目启动会上提出 "我们先用 X 周搭灰度回滚链路,主功能延后" 的提议,
  并提交一份双轨架构方案。
value_created: |
  在系统级框架迁移中,显著降低不可逆事故的发生与扩散概率;
  同时降低团队在事故发生时的应急压力。
boundary_conditions: |
  适用于: 底层、跨多客户、不可回退、影响面广的客户端项目。
  不适用于: 内部工具迁移、单客户场景、可热修的服务端组件、原型/POC 阶段。
failure_modes: |
  过度使用风险: 在低风险、影响面已清楚的场景下做完整回滚链路,
  会让团队觉得"过度防御",拖慢交付节奏(见 E-CE-001)。
  候选人已在 2020 年阿里内部工具迁移中遇到过这种过度使用并完成自我修正。
development_stage: validated
evidence_ids:
  - E-001  # 近期 ESF 接入回滚链路
  - E-003  # 中后期 PacketTunnel 切流量回滚设计
  - E-005  # 早年电商直播大促一键回滚
counterevidence_ids:
  - E-CE-001  # 中期某大型互联网公司内部资产盘点工具过度使用
transfer_scenarios:
  - "从游戏服务端运维到 iOS 客户端"
  - "从电商直播大促到企业安全 EDR"
  - "从 Kext 时代到 SystemExtension 时代"
resume_implication: |
  把 "负责 ESF 接入与 SystemExtension 架构设计" 改写为:
  "在 Apple ESF / SystemExtension 框架迁移期,主导从 Kext 到 SystemExtension 的双轨架构与一键回滚链路设计,保障企业客户在框架不稳定窗口期的事故可控性。"
interview_implication: |
  在自我介绍 60 秒版本中提到 "我最稳定的工作方式 — 系统底层不稳定时先搭回滚再上主功能" 作为身份锚点。
  STAR 叙事推荐 E-001(ESF 接入)。
  高压追问版本只用 2-3 句话讲 "为什么不直接推主功能"。
candidate_validation_note: |
  候选人对此机制识别度高。
  自己改名为: "底层框架变更下的回滚路径前置机制",AI 候选名为 "系统不稳定下的回滚优先机制",候选人采纳了自己的版本。
  复述顺畅,无卡壳。
  主动讲出失效场景(E-CE-001),无需 AI 提示假说。
confidence_breakdown:
  event_richness: 0.8
  cross_context_repetition: 0.9
  outcome_linkage: 0.7
  candidate_recognition: 1.0
  counterevidence_pass: 0.8
confidence_final: 0.83
```

## 示例 2: M-002(strong-hypothesis)

```yaml
mechanism_id: M-002
working_name: 跨领域跳跃时的"成败变量先识别"机制
one_sentence_definition: |
  进入新业务/新技术领域的前 3 个月,候选人会先识别决定该领域成败的 3-5 个关键变量,
  再决定怎么投入,而不是按上一个领域的习惯做事。
mechanism_type: problem-framing
core_pattern: |
  在 [跨领域跳跃] 的场景下,候选人不立刻动手做,而是花 2-4 周观察并访谈,
  抓出该领域真正的"成败变量"(可能与自己熟悉的领域完全不同),
  然后才决定投入哪些动作。
trigger_contexts:
  - "早年从游戏服务端转 iOS 移动端"
  - "中期从电商直播转企业安全 macOS"
  - "近期从 SASE 厂商转企业级 EDR 厂商"
input_cues:
  - "新领域里同事用的高频词跟自己以前用的不一样"
  - "上一个领域常见的'正确做法'在新领域反响平平"
  - "新领域的失败案例,失败原因与自己的预期不一致"
internal_operation: |
  暂停"立刻贡献"的本能,改为观察 2-4 周。
  关键问题: "这个领域里,什么样的人/事/产品被认为是成功的?成功的真正原因是什么?"
action_signature: |
  外显特征: 在新公司前 2 个月,大量与不同角色一对一聊;不急着提出大方案;
  写一份"我看到的成败变量"内部 doc 给老板。
value_created: |
  避免把上一个领域的"正确做法"硬移植到新领域,降低跨领域跳跃的失败率。
boundary_conditions: |
  适用于: 真正跨业务 / 跨技术栈 / 跨组织文化的迁移。
  不适用于: 同领域换公司、技术栈相近的转岗。
failure_modes: |
  过度使用风险: 候选人承认 "有时候这个观察期会被上级误读为'还没贡献'",
  在权力结构很重的组织中需要平衡观察期与可见贡献。
development_stage: strong-hypothesis
evidence_ids:
  - E-007  # 从游戏服务端到 iOS 的前 3 个月做了什么
  - E-009  # 从电商直播到企业安全的前 2 个月观察期
counterevidence_ids: []
transfer_scenarios:
  - "跨业务领域跳跃"
reason_not_validated: |
  仅 2 张正向 evidence,且无明确反证。
  迁移测试时候选人答得不够具体(cross_context_repetition = 0.6)。
  需要再补 1 个事件 + 1 个反例才能升格为 validated。
resume_implication: |
  把 "20 年以上软件开发经验" 这种笼统描述,改写为体现"跨领域跳跃时的元学习模式",
  例如: "横跨游戏服务端 / 电商 iOS / 企业安全 macOS 三个领域,每次跨领域时通过 4 周内识别该领域核心成败变量,再决定投入方式。"
interview_implication: |
  面试中可作为 "为什么你能跨领域稳定输出" 的回答主线,
  STAR 推荐 E-007。
candidate_validation_note: |
  候选人识别度中等,认可 "我确实是这么做的",但希望再补 1 个最近的事件。
  下一轮访谈应优先补证。
confidence_breakdown:
  event_richness: 0.6
  cross_context_repetition: 0.6
  outcome_linkage: 0.6
  candidate_recognition: 0.7
  counterevidence_pass: 0.4
confidence_final: 0.59
```

## 字段说明

- `mechanism_id` 唯一,与 evidence 中的 `related_mechanism_ids` 双向对齐。
- `failure_modes` **必填**,不可写"暂无"。
- `confidence_breakdown` 5 项齐全,可被复用为评审基线。
- `working_name` 必须符合 [naming-style-guide.md](../../assets/naming-style-guide.md)。
