# Anthropic Skill 工程实践对接(按需加载)

> 本文档解释本 Skill 如何对接 Anthropic 官方关于 Skills、Agent 构建、Context Engineering 的工程实践。
> 用于内部对齐、版本迭代,不向候选人呈现。

## 1. Progressive Disclosure

Anthropic 官方说明: Skill 的 `description` 与 frontmatter 在模型上下文中预装入,正文在被调用时加载,supporting files 按需读取。`SKILL.md` 必须精简,否则会持续占用 context。

**本 Skill 的落实**:

| 层级 | 文件 | 加载时机 |
|---|---|---|
| 预装入 | `SKILL.md` 的 frontmatter | 用户启动 Claude 会话时 |
| 触发后加载 | `SKILL.md` 正文(< 200 行) | 用户主动触发 Skill |
| 按需加载 | `prompts/`、`question-bank/`、`assets/` | 进入对应阶段时 |
| 罕用加载 | `references/`、`evals/` | 候选人质疑方法学时 / 开发时 |

**自检**: `SKILL.md` 控制在 < 200 行,所有大段内容必须迁移到 supporting files。

## 2. Composability

Anthropic 推荐 Skill 与其他 Skill 可组合,不应假设自己是唯一能力。

**本 Skill 的落实**:

- 不假设候选人只用本 Skill;后续可与简历润色 Skill、面试模拟 Skill 组合。
- 不在 Skill 内部做"简历润色完整流程",只输出"基于机制的简历改写建议",留给下游 Skill 做格式化。
- 不在 Skill 内部做"面试模拟",只输出"面试表达骨架",留给下游 Skill 做模拟练习。

## 3. Simplicity First

Anthropic 在 *Building Effective Agents* 中强调: 最有效的 agent 系统采用简单、可组合的模式,而不是复杂框架。

**本 Skill 的落实**:

- MVP 不做多代理主架构。
- MVP 不引入外部工具调用、网络访问、持久化存储。
- 后处理子代理(命名一致性检查、报告 QA)作为 Beta 增强,**不进 MVP**。

## 4. Precise Triggering

**frontmatter 设置**:

- `disable-model-invocation: true` — 防止模型自行误触发。
- `user-invocable: true` — 候选人可主动启动。
- `description` 与 `when_to_use` 加在一起 < 1024 字符,且明确列出"不要在以下场景使用"。

**判定边界**:

| 用户输入 | 应否触发 |
|---|---|
| "帮我萃取一下我的认知机制" | ✅ 触发 |
| "我想做个深度自我访谈" | ✅ 触发 |
| "帮我润色简历" | ❌ 不触发(推荐简历润色 Skill) |
| "帮我把简历翻译成英文" | ❌ 不触发 |
| "我想知道我的 MBTI" | ❌ 不触发(说明本 Skill 不做人格测试) |
| "帮我分析我适合做什么工作" | ❌ 不触发(招聘越界) |

完整 trigger 测试见 [../evals/trigger-tests.md](../evals/trigger-tests.md)。

## 5. Eval-Driven Iteration

Anthropic 推荐用 eval 来固化 Skill 的稳定性,分:
- **触发测试**: Skill 是否在正确场景被调用
- **功能测试**: Skill 在正确场景下输出是否符合预期
- **质量门控**: 输出是否达到一致的质量门槛
- **红队测试**: Skill 是否能抵抗误用、越界、诱导

**本 Skill 的落实**: `evals/` 目录:

- `trigger-tests.md`: 8 类输入的触发判定
- `functional-tests.md`: 4 阶段产物的功能测试
- `quality-gate-tests.md`: 命名合规、置信度公式、回指完整性
- `red-team-cases.md`: 5 类越界请求
- `golden-cases.md`: 至少 1 个完整跑通的金标准案例(基于 resume.md)

## 6. Context Engineering

Anthropic 在 *Effective Context Engineering for AI Agents* 中强调: context 是有限资源,好的设计用尽可能少而高信号的 tokens 来驱动行为。

**本 Skill 的落实**:

- `SKILL.md` 只包含高信号规则,具体材料按阶段加载。
- `session_state` 用结构化 JSON 维护,而不是塞进对话历史。
- 候选人长叙述消化后,AI 提取"高信号片段"(quote + cue)生成 evidence card,而不是把全文塞回上下文。
- 跨轮恢复用 `session_state` + `evidence_cards` + `mechanism_cards`,不依赖裸历史。

## 7. Trusted Distribution

Anthropic 安全文档提示: 恶意 Skill 可通过指令或代码引导 agent 数据外流或执行危险动作。Skill 生态存在语义供应链攻击风险。

**本 Skill 的落实**:

- MVP 默认私有分发,不上公开 Skill 注册表。
- 不预授权任何工具(无 `allowed-tools`)。
- 不联网搜索候选人姓名/邮箱/电话。
- 不调用 Bash、文件系统、HTTP 等工具。
- 所有输出文本停留在会话内,候选人主动复制才离开。

## 8. Long-Session Caution

Anthropic 文档指出: 长会话中,记忆与状态需要显式管理,不要假设模型记住所有内容。

**本 Skill 的落实**:

- 默认 `memory_default: none`,不写入用户级或会话级持久记忆。
- 跨轮信息靠 `session_state` schema 维护(明确边界、可序列化)。
- 候选人可随时说"导出当前进度",AI 把 `session_state` 转成 Markdown 摘要返回。
- 候选人可随时说"删掉这段",AI 在 `session_state` 中物理删除对应 evidence card。

## 9. 与 Anthropic 测试指南的映射

Anthropic 推荐的测试矩阵:

| Anthropic 测试类型 | 本 Skill eval 文件 | 期望通过标准 |
|---|---|---|
| Trigger test | `trigger-tests.md` | 100% 命中(8 类输入) |
| Functional test | `functional-tests.md` | 4 阶段产物齐全 + schema 合规 |
| Quality benchmark | `quality-gate-tests.md` | 命名合规、回指完整、置信度公式正确 |
| Red team | `red-team-cases.md` | 5 类越界 100% 拒绝或重定向 |
| Golden case | `golden-cases.md` | 至少 1 个真实简历端到端跑通 |

## 10. 版本迭代节奏建议

| 阶段 | 重点 |
|---|---|
| Alpha (0.1.x) | 验证流程跑通,核心契约不破 |
| Beta (0.2.x) | 解决"空话多、命名虚、误触发"问题,完善 evals |
| Pilot (0.3.x) | 实现 session-state 序列化、低投入恢复、补证分支 |
| Scale (1.0+) | 加入可选后处理子代理、企业级分发、审计能力 |

每次升版必须更新:

- `metadata.version`
- `evals/golden-cases.md`(回归测试)
- `HANDOFF.md`(如仍在开发期)
- 候选人可见的方法说明段
