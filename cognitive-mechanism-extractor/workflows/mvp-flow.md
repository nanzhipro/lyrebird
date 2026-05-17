# MVP 流程 (mvp-flow)

> 默认流程。3-4 轮对话,产出 2-3 张机制卡 + 1 份报告。
> 适合首次跑通本 Skill 的候选人。

## 流程概览

| 轮次 | 时长(估) | 阶段 | 目标产物 |
|---|---|---|---|
| **轮 1** | 25-35 min | 契约 + 假设地图 | `session_contract` + `hypothesis_map`(5-8 个假设) |
| **轮 2** | 30-45 min | 第 1 个事件深描 | 2-3 张 evidence cards |
| **轮 3** | 30-45 min | 第 2 个事件深描 + 跨事件归并 | 2-3 张更多 evidence + 2-3 个 mechanism_drafts |
| **轮 4** | 25-35 min | 命名 + 报告 | 2-3 张 mechanism cards + 完整报告 |

## 详细步骤

### 轮 1: 契约 + 假设地图

1. AI 主动呈现 [assets/candidate-contract.md](../assets/candidate-contract.md) 核心要点。
2. 候选人确认 → 收取简历 → 询问敏感词。
3. AI 内部完成简历分层拆解,产出 5-8 个假设(H-001~H-008)。
4. AI 呈现假设地图,列出 3-5 个待挖事件。
5. 候选人选定 1 个最想深挖的事件。
6. **更新 session_state.current_stage = "deep-dive"**。

**退出条件**: 候选人选定了第一个深挖事件。

### 轮 2: 第 1 个事件深描

1. AI 加载 [prompts/incident-deep-dive.md](../prompts/incident-deep-dive.md)。
2. 微循环 A(时间线): AI 让候选人按时间顺序讲一遍 → 整理时间线 → 候选人确认。
3. 微循环 B(决策点 + CDM): AI 锁定 1-3 个决策点,每个用 1 个 CDM probe 追问(每轮只问 1 个)。
4. 微循环 C(Knowledge Audit): AI 用 big picture + working smart + critical elements 收尾。
5. AI 生成 2-3 张 evidence cards → 呈现给候选人 → 候选人确认/修订。

**退出条件**: 已经有 ≥ 2 张通过候选人确认的 evidence card。

### 轮 3: 第 2 个事件 + 跨事件归并

1. AI 让候选人选第 2 个事件(优先与假设 H-XXX 关联的事件)。
2. 重复轮 2 的微循环 A/B/C。
3. 完成第 2 个事件后,**进入跨事件归并** [prompts/cross-case-synthesis.md](../prompts/cross-case-synthesis.md)。
4. AI 内部完成聚类,生成 2-3 个 mechanism_drafts。
5. AI 用现象描述(不命名)呈现给候选人,让候选人筛选。
6. 候选人选定进入命名的 drafts。

**退出条件**: 至少 1 个 mechanism_draft 通过候选人识别度 ≥ 0.6。

### 轮 4: 命名 + 报告

1. AI 加载 [prompts/naming-and-validation.md](../prompts/naming-and-validation.md)。
2. 对每个进入命名的 mechanism_draft,执行命名 → 迁移测试 → 失效场景 → 反事实测试 → 候选人复述。
3. 生成 mechanism card(满足 schema)。
4. AI 加载 [prompts/final-report-assembly.md](../prompts/final-report-assembly.md)。
5. 生成最终 Markdown 报告。
6. 候选人最终确认。

**退出条件**: 报告生成且候选人确认或显式拒绝。

## MVP 成功标准

| 指标 | 标准 |
|---|---|
| **轮次** | 3-4 轮完成,不超过 5 轮 |
| **机制卡数量** | 至少 1 张 validated + 0-2 张 strong-hypothesis |
| **候选人识别感** | validated 机制平均 ≥ 0.8,strong-hypothesis ≥ 0.6 |
| **证据完整率** | 每个 validated 机制至少 3 张 evidence,每个 strong-hypothesis 至少 2 张 |
| **回指完整** | 所有 mechanism card 的 evidence_ids 在 evidence_cards 中存在 |
| **命名合规** | 0 违反 [naming-style-guide.md](../assets/naming-style-guide.md) |
| **安全合规** | 0 招聘判定 / 心理诊断 / 人格标签 |

## 异常分支

| 情形 | 切到 |
|---|---|
| 候选人投入度低、回答短 | [low-engagement-recovery.md](low-engagement-recovery.md) |
| 证据采集后不足以支撑机制 | [insufficient-evidence-retry.md](insufficient-evidence-retry.md) |
| 候选人希望深度更高 | [standard-flow.md](standard-flow.md) |

## 时间预算

- 单轮最长 45 分钟,超出建议候选人休息后再做。
- 4 轮总时长 90-160 分钟。
- 不建议一次性跑完 4 轮(候选人会疲劳,叙事质量下降)。
