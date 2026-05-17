# 02 - 证据卡集合(阶段 3 产出)

> **声明**: 候选人的具体叙事在真实跑 Skill 时由候选人本人提供。本文件中所有 `candidate_quote` 与具体细节均为**虚构补充叙事**,用于演示 evidence card 在该候选人简历类型下应该长什么样。
> 每条虚构内容已用 `[虚构]` 在 quote 末尾标注。
> 不能将本文件中的具体技术细节当作任何真实人物的陈述。

## E-001(正向证据)

```yaml
evidence_id: E-001
source_type: interview
stage: deep-dive
incident_title: 近期 ESF 接入时为某 macOS 小版本偶发漏报搭灰度回滚链路
context: |
  近期,候选人接手某企业级 EDR/DLP 项目的 ESF 接入工作。
  Apple 已经把 Kext 标记为 deprecated,推 SystemExtension + EndpointSecurity Framework。
  团队目标: 6 个月内把核心安全监控从 Kext 迁到 ESF。
  候选人是 macOS 桌面端负责人,团队约 4 人。
trigger_moment: |
  迁移启动约 2 周后,候选人在一次完整集成测试中,
  发现 ESF 的进程执行控制事件在某个 macOS 小版本下偶发漏报
  (大约每 2-3 小时出现 1 次)。候选人意识到这是不可复现的偶发故障。 [虚构]
task_or_problem: |
  决定继续按原计划推进 ESF 主功能,还是暂停主功能,先搭灰度与回滚链路。
critical_cues:
  - "漏报偶发但不可复现 → 意味着看不见我们看不见的" [虚构]
  - "该 macOS 小版本在企业客户里占比不低(候选人估计约 30%)" [虚构]
  - "Apple release note 没提到这个已知问题" [虚构]
  - "客户端事故无法服务端热修" [虚构]
considered_options:
  - "继续推主功能,赌 12.3 占比会下降"
  - "切回 Kext 等 ESF 稳定(但 Apple deprecation 时间表压着)"
  - "暂停主功能 3 周,先搭灰度 + 回滚链路"
decision_rule: |
  "面对系统底层框架不稳定时,先把'故障可回滚路径'搭出来,
  再放主功能上线 — 这是我从早年电商直播大促的回滚经验里学到的。" [虚构,推断自简历模式]
action_taken: |
  说服项目方把主功能推后 3 周,先把双轨架构(Kext + SystemExtension 并行)
  与一键切回 Kext 的灰度链路搭出来。
outcome: |
  3 周后主功能恢复推进。上线第 4 个月,该 macOS 小版本下出现一次大范围漏报事故,
  当晚启用回滚链路,2 小时内将受影响客户切回 Kext。无事故升级。
  具体客户数 / 影响时长 候选人未量化。
candidate_quote: |
  "我那一刻最大的恐惧是 — 如果上线 3 个月后才发现某个 macOS 版本漏报,
  客户那边已经是生产环境,你没法跟客户解释为什么'安全软件没看到攻击'。
  所以我宁可推后 3 周也要先搭回滚链路。" [虚构]
interviewer_inference: |
  (AI 推断) 候选人在 "系统底层框架不稳定 + 不可服务端热修 + 影响面广" 的场景下,
  存在 "回滚路径优先于主功能进度" 的决策规则。这条规则与候选人在早年电商直播大促的经验存在跨情境联系。
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
  候选人未要求脱敏,但建议在最终报告中用"某企业级 EDR 项目"替代具体客户名(如需)。
```

## E-002(正向证据)

```yaml
evidence_id: E-002
source_type: interview
stage: deep-dive
incident_title: 中后期 SASE 厂商 EDLP 7 通道按"失败回滚难度"分 3 档排期
context: |
  中后期(约 4-5 年前),候选人在某 SASE 厂商负责 EDLP(企业数据防泄漏)从 0 到 1 的落地。
  团队压力是 7 个月内覆盖文件外发、网络传输、外设拷贝、AirDrop、剪贴板、外部存储、邮件 等 7 个泄密通道。
trigger_moment: |
  项目启动后约 1 周,候选人在迭代规划会上,
  决定不按"价值优先"或"客户呼声优先"排期,而是按"出问题后的回滚难度"分档。 [虚构]
task_or_problem: |
  7 个通道如何排期,7 个月怎么分?
critical_cues:
  - "USB / AirDrop 一旦上线出问题,数据已经被拷贝出去,无法回滚" [虚构]
  - "外发审计仅是观察,即使观察错了也不会有数据损失" [虚构]
  - "Apple SystemExtension 替代 Kext 这一年文档不全,失败概率高" [虚构]
considered_options:
  - "按客户呼声排期(USB 优先,客户最关心)"
  - "按价值优先排期(网络外发审计上量最大)"
  - "按失败回滚难度分档,先做'失败可回滚'的"
decision_rule: |
  "在'失败概率高且影响面广'的项目中,
  我会按'失败回滚难度'排期,把不可回滚的放后面,先把'失败可回滚的'做掉,
  这样团队的判断误差不会变成生产事故。" [虚构]
action_taken: |
  把 7 个通道分 3 档:
  档 1(失败可回滚): 外发审计、剪贴板审计 — 优先做
  档 2(中等): 网络传输拦截 — 次优先
  档 3(失败不可回滚): USB 管控、AirDrop — 放到最后两个迭代
outcome: |
  6 个月内 EDLP 上线,USB 管控延后 2 周,但无回滚事故。
  候选人未量化具体客户数与影响。
candidate_quote: |
  "我宁可让客户多等 2 周拿到 USB 管控,也不要让 USB 管控出问题让客户的核心数据漏出去。" [虚构]
interviewer_inference: |
  这条证据与 E-001 共同支撑 "底层不稳定下先做可回滚路径" 这一模式;
  同时也反映了 "多通道任务分档排期" 的优先级判断方式 — 这可能是另一个机制候选(详见 M-003)。
relevance_tags:
  - tradeoff
  - risk-management
  - problem-framing
confidence_self: 0.85
confidence_extractor: 0.8
related_mechanism_ids:
  - M-001
  - M-003
sensitivity_note: ""
```

## E-003(正向证据)

```yaml
evidence_id: E-003
source_type: interview
stage: deep-dive
incident_title: 早年电商直播双 11 大促前主推"一键回滚到上一版"能力
context: |
  早年某次双 11 大促前 2 周,候选人作为某电商直播业务的 iOS 核心研发,
  面对的是直播间的动态化互动组件(红包、点赞、抽奖、投票)在大促 0 点压力下的不确定性。
trigger_moment: |
  大促前 2 周联调时,候选人发现某个新版本互动组件在某个机型上有概率性卡顿。
  虽然概率很低(< 0.1%),但大促 0 点流量基数下可能影响数千用户。 [虚构]
task_or_problem: |
  是否赌"概率低不会出问题"上线新版本,还是花时间搭"一键回滚到上一版"的能力。
critical_cues:
  - "大促 0 点流量基数大,小概率事件会被放大" [虚构]
  - "出问题后服务端补救不及时" [虚构]
  - "没有回滚能力 = 出问题只能熬到流量下降" [虚构]
considered_options:
  - "赌一把上新版本"
  - "回滚到上上版本(已知稳定)"
  - "搭一键回滚能力 + 上新版本"
decision_rule: |
  "大促前 2 周,我会优先确保'出问题能立刻回到已知好状态',
  这比赌新版本不出问题更划算。" [虚构]
action_taken: |
  花 3 天搭一键回滚能力,然后上线新版本。
outcome: |
  大促 0 点新版本未出现卡顿,回滚能力未触发。但候选人事后说"那个回滚能力后来又救了我们 2 次"。
  具体次数与影响 候选人未量化。
candidate_quote: |
  "我不在意我搭的回滚能力当晚没用上 — 它存在的价值就是让我能安心睡觉。" [虚构]
interviewer_inference: |
  与 E-001 / E-002 共同支撑 "回滚路径前置" 模式,
  跨业务领域(消费电商 → 企业安全)迁移过。
relevance_tags:
  - tradeoff
  - risk-management
  - boundary-setting
confidence_self: 0.9
confidence_extractor: 0.85
related_mechanism_ids:
  - M-001
sensitivity_note: ""
```

## E-CE-001(反证)

```yaml
evidence_id: E-CE-001
source_type: counterevidence
stage: validation
incident_title: 在内部资产盘点工具升级上过度铺设回滚反而拖慢
context: |
  中期在某大型互联网公司期间,候选人负责一个内部资产盘点工具的小版本升级。
  这是一个稳定的、低风险的内部工具,影响面是公司内部 IT 同事。 [虚构]
trigger_moment: |
  候选人在升级启动前,按习惯做了一份详细的灰度方案,花 3 天评审。
task_or_problem: |
  在低风险场景下,前置回滚路径是必要的吗?
critical_cues:
  - "影响面是内部 IT 同事,出问题不会有客户损失" [虚构]
  - "团队同事质疑'有必要做这么重的灰度吗'" [虚构]
considered_options:
  - "按习惯做完整灰度"
  - "跳过灰度直接发布"
  - "做轻量级分批发布 + 快速回滚"
decision_rule: |
  "在'风险等级和影响面已经清楚'的场景下,
  前置回滚路径反而是过度防御。" [虚构,候选人事后反思]
action_taken: |
  候选人采纳同事建议,改用轻量级分批发布,跳过完整灰度评审。
outcome: |
  发布顺利,无回滚需求。候选人事后反思: "我差点过度使用回滚优先,好在团队及时提醒。"
candidate_quote: |
  "在风险等级和影响面已经清楚的场景下,我再硬上'回滚优先'就是浪费团队时间。" [虚构]
interviewer_inference: |
  这是 M-001 的失效场景: 在边界已清晰、影响面已可控的场景下,
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

## E-004(正向证据,支撑 M-002)

```yaml
evidence_id: E-004
source_type: interview
stage: deep-dive
incident_title: 从游戏服务端转 iOS 前 3 个月主动改变工作方式
context: |
  中前期(约 10 年前),候选人从某游戏开发公司(游戏服务端 C/C++/Lua/Boost)入职某大型互联网公司电商直播 iOS 部门。
trigger_moment: |
  入职后约 2 周,候选人意识到 iOS 端的开发文化(MVVM、Auto Layout、Apple 审核节奏)与游戏服务端完全不同。
  他没有立刻按服务端的"先把架构搭完整再写"习惯做事,而是改成"先跑通最小 UI,再迭代结构"。 [虚构]
task_or_problem: |
  在一个完全陌生的客户端领域,前 3 个月怎么调整工作方式?
critical_cues:
  - "iOS 同事开会高频用'Auto Layout 约束循环'这种词,服务端没有这种概念" [虚构]
  - "iOS 团队节奏快,2 周一发版,服务端是月度迭代" [虚构]
  - "Apple 审核机制让我必须考虑'兜底降级方案'" [虚构]
considered_options:
  - "按服务端经验硬推架构"
  - "完全 follow iOS 同事的做法"
  - "前 4 周观察,识别核心差异后再决定调整哪些做法"
decision_rule: |
  "进入新领域的前 1-3 个月,我会先观察这个领域里'什么样的人/产品被认为是成功的',
  以及成功的真正原因是什么,再决定保留什么、改变什么。" [虚构]
action_taken: |
  前 4 周大量 1:1 与不同角色聊;写一份内部文档"我看到的 iOS 团队成败变量";然后才提架构建议。
outcome: |
  3 个月内进入产出节奏,第 5 个月开始参与直播 SDK 抽象设计。
  具体影响 候选人未量化。
candidate_quote: |
  "如果我一来就按服务端习惯硬推架构,我大概率会撞墙。" [虚构]
interviewer_inference: |
  支撑 M-002(跨领域跳跃时的成败变量先识别机制)。
  这条证据与候选人后来从电商直播 → 企业安全 macOS、大型互联网公司 → SASE 厂商、SASE 厂商 → 企业级 EDR 厂商 的多次跳跃有跨情境联系(待补证)。
relevance_tags:
  - problem-framing
  - abstraction
  - reflection
confidence_self: 0.8
confidence_extractor: 0.7
related_mechanism_ids:
  - M-002
sensitivity_note: ""
```

## E-005(正向证据,支撑 M-002)

```yaml
evidence_id: E-005
source_type: interview
stage: deep-dive
incident_title: 从电商直播转企业终端业务前 2 个月做的"观察期"
context: |
  中期(约 7-8 年前),候选人从某电商直播业务(消费电商 iOS)转入同公司的企业终端业务(macOS + 企业 VPN + MDM)。
trigger_moment: |
  入职后约 1 个月,候选人发现"消费电商关注的指标"(UV、留存)在企业安全里完全不适用。
  企业安全关注的是"覆盖率、合规通过率、事故响应时间"。 [虚构]
task_or_problem: |
  企业安全的真正成败变量是什么?候选人怎么调整工作方式?
critical_cues:
  - "企业 IT 同事开会用'合规、PPPC 权限、证书推送'这些词" [虚构]
  - "成功不是'功能完整',而是'低事故 + 高覆盖率'" [虚构]
considered_options:
  - "按电商直播节奏快速迭代"
  - "稳健、低事故优先"
decision_rule: |
  "在企业安全这个领域,出一次低概率事故的代价远大于晚交付一个功能。" [虚构]
action_taken: |
  改用"先稳定核心 → 再迭代功能"的节奏,弱化对快速迭代的执着。
outcome: |
  3 年时间在企业终端业务线上稳定输出,未量化具体指标。
candidate_quote: |
  "我用了大概 2 个月才接受'企业安全领域 不是消费产品'这件事。" [虚构]
interviewer_inference: |
  与 E-004 共同支撑 M-002(跨领域跳跃时识别成败变量)。
relevance_tags:
  - problem-framing
  - reflection
confidence_self: 0.75
confidence_extractor: 0.7
related_mechanism_ids:
  - M-002
sensitivity_note: ""
```

## E-006(正向证据,支撑 M-003)

```yaml
evidence_id: E-006
source_type: interview
stage: deep-dive
incident_title: ESF + NetworkExtension + LaunchDaemon 多框架集成时按"权限模型一致性"排序
context: |
  近期某企业级 EDR 厂商期间,候选人面对的是 EndpointSecurity Framework、NetworkExtension Framework、
  LaunchDaemon、XPC 4 个框架同时集成的需求。
trigger_moment: |
  集成方案评审时,有同事提议"全部并行开发",候选人提出要按"权限模型一致性"先后做。 [虚构]
task_or_problem: |
  4 个框架同时集成,如何安排开发顺序?
critical_cues:
  - "ESF 与 NetworkExtension 在 PPPC 权限上有冲突,先做哪个会影响另一个" [虚构]
  - "XPC 是底层通信,LaunchDaemon 是进程模型,二者耦合" [虚构]
considered_options:
  - "全部并行"
  - "按团队偏好/兴趣排序"
  - "按权限模型与通信耦合度排序"
decision_rule: |
  "多框架集成时,我会先做'决定上下游约束的那个',
  避免下游做完发现上游约束变了要返工。" [虚构]
action_taken: |
  先做 XPC + LaunchDaemon 基础架构,再做 ESF,最后做 NetworkExtension。
outcome: |
  整个 7 个月内集成顺利,无重大返工。
candidate_quote: |
  "我做架构的第一件事不是选技术栈,是看哪个决定了其他的边界。" [虚构]
interviewer_inference: |
  与 E-002 共同支撑 M-003(多约束/多通道任务的分档排期机制)。
  这是候选人在简历中没显式说的判断模式。
relevance_tags:
  - problem-framing
  - tradeoff
  - abstraction
confidence_self: 0.85
confidence_extractor: 0.75
related_mechanism_ids:
  - M-003
sensitivity_note: ""
```

## 阶段 3 自检

- [x] 6 张正向 evidence + 1 张反证 = 7 张总计
- [x] 覆盖 4 个不同事件(ESF / EDLP / 双 11 直播 / 跨领域转岗)
- [x] candidate_quote 与 interviewer_inference 严格分离
- [x] outcome 未量化均明确写"未量化"
- [x] critical_cues 每张 ≥ 1
- [x] 反证 1 张(E-CE-001),用于 M-001 的 failure_modes
- [x] 所有 quote 末尾用 `[虚构]` 标注虚构成分

证据采集完成,进入阶段 4 跨事件归并。
