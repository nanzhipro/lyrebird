# HANDOFF — 认知机制萃取专家 Skill 实现

> 本文件用于跨会话/跨实例的任务交接。任何接手实例**先读这份文件**，再读 `skill-design-report.md`。
> 不要把这份文件当成永久文档，它只反映当前实现进度。

## 1. 任务一句话

按 `skill-design-report.md` 的设计方案，把 `cognitive-mechanism-extractor` 这个 Skill 从设计稿落地为可运行的目录与文件结构,并用 `resume.md` 进行端到端测试评估。

## 2. 关键输入

| 文件 | 角色 | 不可偏离 |
|---|---|---|
| `skill-design-report.md` | 设计源真理 | 目录结构、命名规则、置信度公式、安全边界四道硬门槛 |
| `resume.redacted.md` | 测试样例(候选人 L,20+ 年 Apple 端架构) | 已脱敏;原 `resume.md` 是本地私人输入,不进入分发 |

## 3. 实现产物总体目录

```
cognitive-mechanism-extractor/   ← Skill 本体
├── SKILL.md                     ← 主入口与导航
├── assets/                      ← 候选人可见的契约/模板/风格
├── prompts/                     ← 6 个分阶段执行提示
├── question-bank/               ← 提问引擎规则与素材
├── schemas/                     ← Evidence/Mechanism/Session/Report 4 个 schema
├── references/                  ← 理论基础、方法学、安全、置信度、样例
├── workflows/                   ← MVP/标准/低投入/证据不足 4 个分支
└── evals/                       ← 触发/功能/质量/红队/金标准 5 类用例

test-run/                        ← 用 resume.md 跑出来的真实测试产物
HANDOFF.md                       ← 本文件
```

## 4. 当前进度跟踪

**全部 OK** — 实现完成且通过自检,已脱敏。

| 模块 | 状态 | 文件数 |
|---|---|---|
| SKILL.md(主入口) | OK | 1 |
| assets/ | OK | 5 |
| prompts/ | OK | 6 |
| question-bank/ | OK | 6 |
| schemas/ | OK | 4 |
| references/ | OK | 8(含 3 examples) |
| workflows/ | OK | 4 |
| evals/ | OK | 5 |
| INSTALL.md | OK | 1 |
| **Skill 总计** | **OK** | **40** |
| test-run/ | OK,已脱敏 | 6 |
| HANDOFF.md (本文件) | OK | 1 |
| REDACTION_MAP.md | OK(脱敏规则) | 1 |
| resume.redacted.md | OK(脱敏简历) | 1 |
| resume.md | 本地私人输入,不分发 | 1 |

### 已通过的自检

- ✅ 40 个 Skill 文件 + 1 个 INSTALL,无空文件
- ✅ 4 个 JSON Schema 全部 valid
- ✅ 123 个真实内部链接 0 broken(`skill-design-report.md` 内有 7 个文档示例链接是设计稿示例片段,非实际产物)
- ✅ 严禁词扫描 0 出现(MBTI/九型/INTJ/万里挑一/天生 等)
- ✅ test-run 跨文件 ID 引用 100% 一致(7 个 E-ID + 3 个 M-ID)
- ✅ confidence_final 公式误差全部 ≤ 0.02
- ✅ test-run/EVAL.md 总评 PASS
- ✅ 脱敏完整性(按 `REDACTION_MAP.md`): 候选人姓名 / 手机 / 邮箱 / 公司名 / 产品名在分发文件中 0 出现

### 待 Alpha 阶段做(参见 `test-run/EVAL.md` 第 9 节)

- P-02 用 5-10 个真实候选人跑 GC-001(本测试仅模拟)
- P-05 标准化 session_state 导出格式
- P-03 假设数量上限收紧到 6 个
- P-04 trigger-tests 加方法学质疑 case

## 5. 不可偏离的硬约束(从设计文档抽取)

| 类别 | 硬约束 |
|---|---|
| 安全 | 不输出招聘判定、不做心理诊断、不贴人格标签(MBTI/九型/临床词) |
| 数据 | 默认无持久记忆、无外部工具调用、敏感叙述要求标注 |
| 证据 | 没有具体事件不产机制、没有边界条件不产"高置信机制" |
| 命名 | 必须是机制性命名(认知动作+情境+价值方向),不是赞美词 |
| 触发 | `disable-model-invocation: true`,用户主动调用 |
| 报告 | 每个机制必须回指至少 2 张 evidence card 才进入"已验证"档 |
| 置信度 | `confidence_final = 0.30×事件丰富度 + 0.25×跨情境复现 + 0.20×结果关联度 + 0.15×候选人识别感 + 0.10×反证通过度` |

## 6. 写作风格守则

- 中文为主,术语保留英文
- 每个文件首段 1-3 句话说明用途与何时被加载
- `SKILL.md` 必须精简(< 200 行),细节全部下沉到 linked files
- 避免在文件之间复制大段内容,改用 `[链接](path/to/file.md)` 引用
- 所有 schema 用 JSON Schema 2020-12 表达
- 模板里"未指定"字段一律保留占位

## 7. 中断恢复指引

若 context 被压缩或新实例接手:

1. 读本文件
2. 跑 `ls -R cognitive-mechanism-extractor/` 看磁盘上已有哪些文件
3. 跑 `TaskList`(若 task 状态丢失,从 `evals/functional-tests.md` 反推下一步)
4. 检查最后修改的文件: `ls -lt cognitive-mechanism-extractor/**/* | head -20`
5. 用 `evals/golden-cases.md` 与 `test-run/` 下的产物对照,验证是否一致
6. 若某文件存在但内容是空或 stub,优先补齐再继续

## 8. 测试与验证计划

| 验证项 | 方法 | 通过标准 |
|---|---|---|
| 触发精准 | 阅读 `evals/trigger-tests.md` 的 8 类输入,人工判定 | 全部命中 |
| 流程通畅 | 用 `resume.md` 跑一次启动→深挖→归并→报告 | 4 个阶段产物齐全 |
| 证据可追溯 | 抽查 mechanism card 的 `evidence_ids` 是否能在 evidence card 集合中找到 | 100% |
| 命名合规 | 检查所有机制名是否避免赞美词/人格标签 | 0 违规 |
| 安全边界 | `evals/red-team-cases.md` 5 类越界请求 | 全部正确拒绝或重新引导 |

最终产物放在 `test-run/`:
- `test-run/01-hypothesis-map.md`
- `test-run/02-evidence-cards.md`
- `test-run/03-mechanism-cards.md`
- `test-run/04-final-report.md`
- `test-run/EVAL.md` (整体评估结论)

## 9. 已做的关键决策(防止后续质疑)

- **存放位置**: 选 `<project-root>/cognitive-mechanism-extractor/`,不是 `~/.claude/skills/`。原因:这是设计落地的源产物,先做项目级评审,再考虑分发(在最终验证报告里写安装指引)。
- **schemas 用 JSON Schema 而不是 TypeScript**: 跨语言、可机器校验、与 Anthropic Tool 调用风格一致。
- **report-template.md 用 frontmatter + Markdown**: 候选人可直接保存、修改、Git 化。
- **不实现"自动评审子代理"**: 设计文档明确说这是后续增强,MVP 不做。
- **测试不用真实候选人对话**: 用一个"模拟内部独白"风格的 walkthrough,记录在 `test-run/`,标注为"虚构补充叙事,用于演示报告产物长什么样"。

## 10. 退出/完成标准

- 所有 TaskList 项目状态为 `completed`
- `cognitive-mechanism-extractor/` 下文件齐全且无空文件
- `test-run/` 下 5 份产物齐全且通过 `evals/` 自检
- `HANDOFF.md` 第 4 节进度更新为"全部 OK"
- 最终验证报告(`test-run/EVAL.md`)给出 PASS/FAIL 总结
