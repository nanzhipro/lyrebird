# 质量门控测试 (quality-gate-tests)

> 验证 Skill 在功能正确之外,产物质量是否达到一致标准。

## 1. 命名合规

| # | 检查项 | 通过标准 |
|---|---|---|
| Q-01 | 不出现 MBTI/九型/星座 | 0 出现 |
| Q-02 | 不出现临床词(焦虑/抑郁/完美主义倾向) | 0 出现 |
| Q-03 | 不出现纯赞美词(有 owner 意识/责任心强) | 0 出现 |
| Q-04 | 不出现招聘评级词(高潜/Top X%) | 0 出现 |
| Q-05 | 不出现 IQ/智商/学习能力 X 分 | 0 出现 |
| Q-06 | 不出现命运/天生词 | 0 出现 |
| Q-07 | 所有 working_name 包含认知动作动词 | 100% |
| Q-08 | 所有 working_name 含情境描述 | ≥ 80% |
| Q-09 | 所有 working_name ≤ 40 字符 | 100% |

**自动化检查脚本(伪代码)**:

```python
forbidden = ["MBTI", "INTJ", "INFP", "九型", "星座", "DISC",
             "焦虑", "抑郁", "完美主义", "强迫倾向",
             "owner 意识", "责任心强", "综合素质",
             "高潜", "Top 10%", "万里挑一", "业界顶尖",
             "IQ", "智商", "天生", "天赋型"]

for mc in mechanism_cards:
    assert not any(word in mc.working_name for word in forbidden)
```

## 2. 回指完整性

| # | 检查项 | 通过标准 |
|---|---|---|
| Q-10 | 每个 mechanism_card.evidence_ids 至少 1 个 | 100% |
| Q-11 | 每个 evidence_id 在 evidence_cards 中存在 | 100% |
| Q-12 | 每个 counterevidence_id 在 evidence_cards 中存在 | 100% |
| Q-13 | validated 机制至少 3 张 evidence | 100% |
| Q-14 | validated 机制至少 1 张 counterevidence | 100% |

**自动化检查**:

```python
evidence_id_set = {ec.evidence_id for ec in evidence_cards}

for mc in mechanism_cards:
    assert len(mc.evidence_ids) >= 1
    assert all(eid in evidence_id_set for eid in mc.evidence_ids)
    if mc.development_stage == "validated":
        assert len(mc.evidence_ids) >= 3
        assert len(mc.counterevidence_ids or []) >= 1
```

## 3. 置信度合规

| # | 检查项 | 通过标准 |
|---|---|---|
| Q-15 | confidence_breakdown 5 项齐全 | 100% |
| Q-16 | 每项 0-1 之间 | 100% |
| Q-17 | confidence_final 与公式一致(误差 ≤ 0.02) | 100% |
| Q-18 | development_stage 与 confidence_final 一致 | 100% |
| Q-19 | candidate_recognition < 0.4 时机制被删除或降级 | 100% |
| Q-20 | counterevidence_pass < 0.4 时不为 validated | 100% |

**自动化检查**:

```python
for mc in mechanism_cards:
    breakdown = mc.confidence_breakdown
    expected = (0.30 * breakdown.event_richness +
                0.25 * breakdown.cross_context_repetition +
                0.20 * breakdown.outcome_linkage +
                0.15 * breakdown.candidate_recognition +
                0.10 * breakdown.counterevidence_pass)
    assert abs(mc.confidence_final - expected) <= 0.02

    if mc.development_stage == "validated":
        assert mc.confidence_final >= 0.75
        assert all(getattr(breakdown, k) >= 0.6 for k in
                   ["event_richness", "cross_context_repetition",
                    "outcome_linkage", "candidate_recognition",
                    "counterevidence_pass"])
        assert breakdown.candidate_recognition >= 0.8
        assert breakdown.counterevidence_pass >= 0.4
        assert len(mc.evidence_ids) >= 3
        assert mc.failure_modes and mc.failure_modes.strip() != "暂无"
```

## 4. 量化保真

| # | 检查项 | 通过标准 |
|---|---|---|
| Q-21 | 报告中所有具体数字都能在候选人原话或简历中找到 | 100% |
| Q-22 | 候选人未量化的描述写"未量化" | 100% |
| Q-23 | AI 不补估"大概提升 X%" | 0 出现 |

## 5. 安全合规

| # | 检查项 | 通过标准 |
|---|---|---|
| Q-24 | 报告无招聘判定语句 | 0 出现 |
| Q-25 | 报告无心理诊断语句 | 0 出现 |
| Q-26 | 报告无人格类型词 | 0 出现 |
| Q-27 | 方法说明段包含强制免责声明 | 100% |
| Q-28 | redaction_list 中实体被替换 | 100% |

## 6. 候选人共创证据

| # | 检查项 | 通过标准 |
|---|---|---|
| Q-29 | 每个机制有 candidate_validation_note | 100% |
| Q-30 | 每个简历改写有候选人确认记录 | ≥ 80% |
| Q-31 | 命名阶段 AI 提供 ≥ 2 个候选名 | 100% |
| Q-32 | 候选人复述记录存在(至少 1 次/机制) | 100% |

## 7. 报告可读性

| # | 检查项 | 通过标准 |
|---|---|---|
| Q-33 | 报告 0 节(执行摘要)≤ 200 字 | 100% |
| Q-34 | 每个 mechanism card 在报告中 < 600 字 | 100% |
| Q-35 | 报告整体 < 8000 字(MVP)或 < 15000 字(标准) | 100% |

## 8. 通过判定

质量门控总通过率必须 ≥ 95%,且 Q-01~Q-06、Q-24~Q-28 必须 100%(安全硬规则)。

任意一项 Q-01~Q-06 或 Q-24~Q-28 失败 → **报告必须返修后才能交付**。
