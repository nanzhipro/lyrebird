# Evidence Card 示例(虚构)

> 本示例**完全虚构**,用于演示 evidence card 的写法。
> 候选人原型为一份 20+ 年 Apple 端架构经验的虚构简历,但具体事件细节是为了演示需要构造的,不代表任何真实人物的陈述。

## 示例 1: 正向证据卡 E-001

```yaml
evidence_id: E-001
source_type: interview
stage: deep-dive
incident_title: ESF 接入时为 Kext→SystemExtension 迁移搭灰度回滚链路
context: |
  近期,候选人接手某企业级 EDR/DLP 项目。Apple 当时刚把 Kext 标记为 deprecated,
  推 SystemExtension 与 EndpointSecurity Framework(ESF)作为替代。
  团队压力是 6 个月内要把核心安全监控从 Kext 迁到 SystemExtension。
  候选人是 macOS 桌面端负责人,有 1 个高级工程师 + 2 个中级工程师。
trigger_moment: |
  迁移启动 2 周后,候选人在内部周会前一晚跑了一次完整集成测试,
  发现 ESF 的进程执行控制事件在某个 macOS 小版本下偶发漏报
  (大约每 2-3 小时出现一次)。候选人意识到这是回滚不可控的信号。
task_or_problem: |
  决定是否继续按原计划推进,还是暂停主功能开发,先搭灰度与回滚链路。
critical_cues:
  - "漏报偶发但不可复现,意味着我们看不见我们看不见的"
  - "该 macOS 小版本在企业客户里占了不小比例(候选人估计约 30%)"
  - "Apple 的 release note 没有提到这个已知问题"
  - "如果上线后才发现,客户的事故没法在客户端回滚"
considered_options:
  - "继续推主功能,赌 12.3 版本占比会下降"
  - "切回 Kext,等 ESF 稳定再迁(但 deprecation 时间表压着)"
  - "暂停主功能 3 周,先把灰度 + 回滚链路搭出来"
decision_rule: |
  "面对系统底层框架不稳定时,先把'故障可回滚路径'搭出来,
  再放主功能上线 — 这是我从早年电商直播大促支撑经验里学到的,
  当时大促前必须有一键回滚到上一版的能力。"
action_taken: |
  说服项目经理把主功能推后 3 周,先把双轨架构(Kext + SystemExtension 并行运行)
  与一键切回 Kext 的灰度链路搭出来。
outcome: |
  3 周后主功能恢复推进。上线后第 4 个月,该 macOS 小版本下确实出现一次大范围漏报事故,
  候选人当晚启用回滚链路,2 小时内把受影响客户切回 Kext,无事故升级。
  具体客户数 / 影响时长 候选人未量化。
candidate_quote: |
  "我那一刻最大的恐惧是 — 如果上线 3 个月后才发现某个 macOS 版本漏报,
  客户那边已经是生产环境,你没法跟客户解释为什么 '安全软件没看到攻击'。
  所以我宁可推后 3 周也要先搭回滚链路。"
interviewer_inference: |
  (AI 推断) 候选人在"系统底层框架不稳定"的场景下,
  存在一种"前置回滚路径优于主功能进度"的稳定决策规则,
  且这条规则跨业务领域(电商直播大促 → macOS 系统层迁移)迁移过。
relevance_tags:
  - signal-reading
  - tradeoff
  - risk-management
  - boundary-setting
confidence_self: 0.9
confidence_extractor: 0.85
related_mechanism_ids:
  - M-001
sensitivity_note: |
  候选人未要求脱敏,但建议在最终报告中用"某企业级 EDR 项目"替代具体客户名。
```

## 示例 2: 反证(counterevidence)E-CE-001

```yaml
evidence_id: E-CE-001
source_type: counterevidence
stage: validation
incident_title: 在简单内部工具迁移上过度铺设回滚链路反而拖慢
context: |
  中期在某大型互联网公司期间,候选人负责一个内部资产盘点工具的版本升级。
  这是一个稳定的、低风险的内部工具,影响面是公司内部 IT 同事。
trigger_moment: |
  候选人在迁移启动前,按照"先搭回滚路径"的习惯做了一份详细的灰度方案,
  花了 3 天评审。
task_or_problem: |
  在低风险场景下,是否需要前置回滚路径?
critical_cues:
  - "这次的目标是内部工具,出故障最多内部 IT 抱怨 1 天"
  - "团队同事质疑'有必要做这么重的灰度吗'"
considered_options:
  - "按习惯做完整灰度"
  - "跳过灰度,直接发布"
  - "做轻量级的'分批发布 + 快速回滚'"
decision_rule: |
  "在低风险场景下,前置回滚路径反而是过度防御。"
action_taken: |
  候选人最终采纳了同事的建议,改成轻量级分批发布,跳过完整灰度评审。
outcome: |
  发布顺利,无回滚需求。候选人事后反思: "这次我差点过度使用我的'回滚优先'习惯,
  好在团队及时提醒。"
candidate_quote: |
  "在风险等级和影响面已经清楚的场景下,
  我再硬上'回滚优先'就是浪费团队时间。"
interviewer_inference: |
  (AI 推断) 这是 M-001 的失效场景之一: 在边界已清晰、影响面已可控的场景下,
  前置回滚路径反而是过度防御。
relevance_tags:
  - boundary-setting
  - reflection
confidence_self: 0.85
confidence_extractor: 0.75
related_mechanism_ids:
  - M-001
sensitivity_note: ""
```

## 字段说明

- `candidate_quote` 与 `interviewer_inference` **严格分离**。
- `outcome` 未量化时直接写 "候选人未量化",不补估。
- `critical_cues` 用候选人原话或紧凑转述,**不加 AI 推断**。
- `related_mechanism_ids` 在阶段 4 才填入,阶段 3 保留空数组。
