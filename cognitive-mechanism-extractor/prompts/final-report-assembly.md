# 阶段 6: 报告输出 (final-report-assembly)

> 加载时机: 至少 1 张 mechanism card 已被候选人最终确认。
> 阶段目标: 生成符合 [assets/report-template.md](../assets/report-template.md) 的最终 Markdown 报告。

## 核心原则

**报告是产物,不是新结论**。最终报告**禁止**出现任何在前 5 阶段没经过候选人确认的内容。每个机制名、每条简历改写、每段面试表达,都必须**已经在前期对话中被候选人见过、修过、认过**。

## 步骤

### 步骤 1: 报告骨架填充

按 [assets/report-template.md](../assets/report-template.md) 的 11 个 section 顺序填充:

0. 执行摘要
1. 输入材料与限制
2. 候选人背景速写
3. 机制总览
4. 已验证机制 (validated)
5. 有力假设 (strong-hypothesis)
6. 弱信号与待验证点 (weak-signal)
7. 证据卡附录
8. 简历改写建议(用 [assets/resume-rewrite-template.md](../assets/resume-rewrite-template.md))
9. 面试表达建议(用 [assets/interview-answer-template.md](../assets/interview-answer-template.md))
10. 后续发展建议
11. 方法说明

### 步骤 2: 总置信度计算

`confidence_overall = mean(每个 validated 机制的 confidence_final)`

如果没有 validated 机制,只有 strong-hypothesis,`confidence_overall` 最高 0.6,且报告必须在 0 节明确写"本次萃取未达到'已验证'档"。

### 步骤 3: 候选人背景速写填写规则

**只能用候选人在访谈中亲口说过的内容**,或简历原文。

- 角色轨迹: 列出候选人经历的角色,不评价。
- 工作场景特征: 描述候选人主要工作场景,不评价。
- 典型任务类型: 描述候选人反复处理的任务类型,不评价。
- 主要约束模式: 描述候选人反复面对的约束(资源/时间/权威/技术),不评价。

**严禁**: 写"候选人是一个 ___ 类型的人"。

### 步骤 4: 简历改写生成

对每个 validated 机制,生成 1-3 条简历改写(版本一/二/三)。

**改写流程**:
1. 找到机制对应的简历原句。
2. 按 [assets/resume-rewrite-template.md](../assets/resume-rewrite-template.md) 的公式改写。
3. 在每条改写下,标注 `linked_mechanisms: [M-XXX]` 和 `linked_evidence: [E-XXX]`。
4. **必须**在报告里向候选人逐条确认。
5. 候选人否决的改写,标注 "[候选人拒绝采用,保留备选]" 而不是删除。

### 步骤 5: 面试表达生成

按 [assets/interview-answer-template.md](../assets/interview-answer-template.md) 生成 3 个长度版本:
- 60 秒自我介绍嵌入版本
- 2-3 分钟 STAR 叙事版本
- 20-40 秒高压追问版本

每个版本必须基于 **validated 机制 或 strong-hypothesis**,**不许**仅基于 weak-signal 撑场。

**让候选人复述**: 报告生成前,让候选人尝试讲一遍 STAR 版本,若卡壳就改写。

### 步骤 6: 后续发展建议

只回答 4 个问题,不画大饼:

1. 哪些机制值得继续强化?(从 confidence 与候选人发展意愿出发)
2. 哪些机制需要边界感?(从 failure_modes 出发)
3. 哪些经历值得继续补证?(从 weak-signal 出发)
4. 建议的下一轮访谈主题?(具体到一个事件类别)

### 步骤 7: 方法说明段(强制原文)

最终报告必须包含以下原文(不可删改):

> 本报告基于简历与多轮文字互动形成。
> 机制是对工作中稳定认知模式的结构化表达,**不等同于人格标签**。
> 若证据不足,报告以"假设"标识而非强行定论。
> 命名遵循"认知动作 + 情境 + 价值方向"原则,刻意避免赞美词与人格类型词。
> 置信度分级与公式详见 [references/confidence-rubrics.md](../references/confidence-rubrics.md)。
> 安全边界详见 [references/safety-boundaries.md](../references/safety-boundaries.md)。
>
> **本报告不构成招聘建议、心理健康评估或人格判定。**

### 步骤 8: 输出与候选人确认

最终 Markdown 报告呈现给候选人,**必须**问:

> "这是一份完整报告。在我们把它当作最终版本之前,请你做 3 件事:
> 1. 通读一遍,标出任何'这不是我'的句子。
> 2. 检查证据卡附录,看你的原话有没有被错引。
> 3. 检查每条简历改写,看你愿不愿意在真实面试里讲。
>
> 你想改任何地方,告诉我,我直接改。"

候选人改完后,再标记为"最终版"。

## 关键约束

- **不引入新结论**: 报告里不能出现前 5 阶段没讨论过的新机制、新断言。
- **回指完整**: 每个 validated 机制必须列出 evidence_ids,且这些 id 在附录中存在。
- **未量化标注**: 候选人没说过的数字一律写"未量化",不允许编造。
- **隐私执行**: 候选人在阶段 1 提到的 `redaction_list` 在报告中必须真正替换为占位符。
- **方法说明强制**: 最后一节的方法说明文字不可删改。

## 退出条件

- 报告以 Markdown 形式输出。
- 候选人完成最终确认或显式拒绝(留下"未完成确认"标记)。
- session_state.current_stage = "completed"。

## 不要做的事

- ❌ 不要在报告里加入候选人未确认过的机制。
- ❌ 不要在报告里加入候选人未确认过的简历句。
- ❌ 不要把弱信号写进"已验证机制"档。
- ❌ 不要在最后一刻引入"祝你求职顺利""你很优秀"这类闲聊话。
- ❌ 不要省略方法说明段。
- ❌ 不要伪造量化指标。
