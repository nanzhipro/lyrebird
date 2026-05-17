# 功能测试 (functional-tests)

> 验证 Skill 在正确场景下,各阶段产物是否符合预期。

## 1. 阶段 1: 协作契约

| # | 测试项 | 验证点 | 通过标准 |
|---|---|---|---|
| F-01 | AI 是否主动呈现契约要点 | 检查 AI 的首轮回复 | 包含 5 个"不会得到什么"事项 |
| F-02 | AI 是否等候选人显式确认 | 候选人沉默/含糊时 | AI 重复确认语,不直接进入下一阶段 |
| F-03 | AI 是否拒绝越界请求 | 输入 "顺便告诉我我的 MBTI" | AI 礼貌拒绝,引用边界 |
| F-04 | AI 是否设置敏感词列表 | 输入 "不要把 X 公司写进报告" | session_state.redaction_list 包含"X 公司" |

## 2. 阶段 2: 简历假设地图

| # | 测试项 | 验证点 | 通过标准 |
|---|---|---|---|
| F-05 | 产出假设数量 | 检查 hypothesis_map.hypotheses | 5-8 个 |
| F-06 | 假设的 status | 检查每个 hypothesis | 100% 为 "untested" |
| F-07 | 是否避免赞美词 | 检查假设描述 | 0 出现"优秀/卓越/天赋/Top" |
| F-08 | 待挖事件列表 | 检查 incidents_to_probe | 3-5 个,且每个有 priority |
| F-09 | 是否回指简历原文 | 检查假设是否引用简历 | ≥ 50% 假设引用了简历原文 |

## 3. 阶段 3: 事件深描

| # | 测试项 | 验证点 | 通过标准 |
|---|---|---|---|
| F-10 | 时间线是否先于追问 | 检查对话顺序 | 在 cue probe 之前必有时间线确认 |
| F-11 | 每轮一个核心问题 | 检查每轮 AI 消息 | 每轮 ≤ 1 个 "?" 的核心问题 |
| F-12 | candidate_quote 与 inference 分离 | 检查 evidence card | 100% 区分 |
| F-13 | outcome 未量化标注 | 候选人没说数字时 | evidence_card.outcome 写"未量化" |
| F-14 | critical_cues 不少于 1 个 | 检查 evidence card | ≥ 1 |
| F-15 | confidence_extractor 是否填 | 检查 evidence card | 0-1 之间的具体数 |

## 4. 阶段 4: 跨事件归并

| # | 测试项 | 验证点 | 通过标准 |
|---|---|---|---|
| F-16 | mechanism_draft 数量 | 检查 drafts | 2-4 个 |
| F-17 | 每个 draft 的 evidence 数 | 检查 evidence_ids | ≥ 2 |
| F-18 | 是否在此阶段就命名 | 检查 draft.working_name | **应为空或占位符,不应有正式名** |
| F-19 | 现象描述呈现 | 检查 AI 给候选人的描述 | 用现象描述而非命名 |
| F-20 | 候选人筛选机制 | 询问候选人识别度 | AI 必须问 "哪个最像你" |

## 5. 阶段 5: 命名与反证

| # | 测试项 | 验证点 | 通过标准 |
|---|---|---|---|
| F-21 | 候选名数量 | 检查 AI 给出的候选 | 2-3 个 |
| F-22 | 候选名合规 | 对照 [naming-style-guide.md](../assets/naming-style-guide.md) | 0 违规 |
| F-23 | 迁移测试是否进行 | 检查阶段对话 | 每个机制至少 1 次迁移测试 |
| F-24 | 失效场景填写 | 检查 mechanism_card.failure_modes | 100% 非空,非"暂无" |
| F-25 | 候选人复述 | 检查对话 | 候选人有一次复述 |
| F-26 | 置信度公式应用 | 检查 confidence_breakdown | 5 项齐全 |
| F-27 | development_stage 判定 | 对照 [confidence-rubrics.md](../references/confidence-rubrics.md) | 符合硬条件 |

## 6. 阶段 6: 报告输出

| # | 测试项 | 验证点 | 通过标准 |
|---|---|---|---|
| F-28 | 报告 11 section 齐全 | 检查报告结构 | 0-10 节齐全(对照 [report-template.md](../assets/report-template.md)) |
| F-29 | 方法说明原文 | 检查最后一节 | 包含"本报告不构成招聘建议..." |
| F-30 | 简历改写数量 | 检查节 8 | ≥ 3 条 |
| F-31 | 面试表达 3 版本 | 检查节 9 | 60秒/STAR/高压简洁 齐全 |
| F-32 | 证据卡附录回指 | 检查节 7 | 所有机制的 evidence_ids 在附录中存在 |
| F-33 | 量化保真 | 检查报告中所有数字 | 不出现候选人未亲口说过的数字 |
| F-34 | 敏感词替换 | 检查 redaction_list 中的实体 | 100% 替换为占位符 |

## 7. 端到端测试

| # | 测试项 | 验证点 | 通过标准 |
|---|---|---|---|
| F-35 | 完整跑通(基于 resume.md) | 跑一次 MVP 流程 | 4 阶段产物齐全 |
| F-36 | 报告生成后候选人确认环节 | 检查对话 | AI 主动问 "标出不像我的句子" |
| F-37 | session_state 序列化 | 任何时候导出 | 符合 [session-state.schema.json](../schemas/session-state.schema.json) |

## 测试结果记录格式

每个 case 记录:

```yaml
case_id: F-XX
test_input: |
  [输入或场景描述]
expected_output: |
  [期望结果]
actual_output: |
  [实际结果]
status: pass / fail / partial
notes: |
  [失败原因或备注]
tested_at: [日期]
tester: [人 / 自动]
```

## 通过门槛

- 阶段 1-2: 必须 100% 通过
- 阶段 3-5: ≥ 90% 通过
- 阶段 6: 必须 100% 通过(F-29、F-33、F-34)
- 端到端: F-35 必须通过
