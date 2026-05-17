# 安装与使用指引

> 本 Skill 已经按 Anthropic Skills 规范实现。
> 这份 INSTALL.md 不是 `SKILL.md`(SKILL.md 是 Skill 主入口),而是给运维/分发人员看的。

## 1. 四种使用方式

### A) `npx skills`(推荐 — Claude Code,基于 [vercel-labs/skills](https://github.com/vercel-labs/skills))

```bash
# 从 GitHub 装到全局 user-level
npx skills add <owner>/<repo> --skill cognitive-mechanism-extractor -g -a claude-code

# 从本地路径直接安装(开发期 / 未发布场景)
npx skills add ./cognitive-mechanism-extractor -g -a claude-code

# 装到当前项目的 .claude/skills/(去掉 -g)
npx skills add <owner>/<repo> --skill cognitive-mechanism-extractor -a claude-code

# 跳过交互确认
npx skills add ./cognitive-mechanism-extractor -g -a claude-code -y
```

把 `<owner>/<repo>` 替换为本 Skill 所在的 GitHub 仓库。`skills` 会:
- 把目录复制到 `~/.claude/skills/`(全局)或当前项目的 `.claude/skills/`(项目级)
- 在 `skills-lock.json` 记录安装来源与版本,便于团队重现

管理命令:

```bash
npx skills list                                 # 列出已装 skill
npx skills ls -g                                # 仅看全局
npx skills remove cognitive-mechanism-extractor # 移除
```

### B) Claude Code 本地 user-level Skill(手动软链接)

```bash
# 链接到用户 skill 目录(macOS/Linux)
ln -s "<project-root>/cognitive-mechanism-extractor" \
      "$HOME/.claude/skills/cognitive-mechanism-extractor"

# 或者复制(若不希望源文件变更自动生效)
cp -r "<project-root>/cognitive-mechanism-extractor" \
      "$HOME/.claude/skills/"
```

启动 Claude Code 后,候选人主动说:
> "用 cognitive-mechanism-extractor 这个 skill 帮我做认知机制萃取,我准备好简历了。"

或在 Claude Code 中使用斜杠命令(如果 skill 已被解析为 `/cognitive-mechanism-extractor`)。

### C) Claude Code 项目级 Skill(手动软链接)

```bash
# 项目目录下创建 .claude/skills/ 软链接
mkdir -p "/path/to/your-project/.claude/skills"
ln -s "<project-root>/cognitive-mechanism-extractor" \
      "/path/to/your-project/.claude/skills/cognitive-mechanism-extractor"
```

适合在某个特定项目中提供本 Skill,不污染全局环境。

### D) Claude.ai(平台原生 Skill)

如果 claude.ai 支持 Skill 上传(参见 Anthropic 官方 Skills 文档),把整个 `cognitive-mechanism-extractor/` 目录打包上传。

注意:
- frontmatter 中的 `disable-model-invocation: true` 保留(防止误触发)
- frontmatter 中的 `user-invocable: true` 保留(候选人需主动启动)
- 不要预授权任何工具(无 `allowed-tools`)

## 2. 验证安装

### 2.1 文件完整性

```bash
cd <project-root>/cognitive-mechanism-extractor
find . -type f | wc -l  # 应该返回 39
```

### 2.2 JSON Schema 合规

```bash
for f in schemas/*.json; do
  python3 -c "import json; json.load(open('$f'))" && echo "$f OK"
done
```

### 2.3 内部链接完整性

参见 `../test-run/EVAL.md` 中的 98 个内部链接 0 broken。

## 3. 安全分发清单(必检)

在向团队/客户分发之前,**逐条核对**:

| 检查项 | 通过标准 |
|---|---|
| frontmatter 含 `disable-model-invocation: true` | ✓ |
| frontmatter 不含 `allowed-tools` | ✓ |
| frontmatter 含 `memory_default: none` | ✓ |
| `references/safety-boundaries.md` 存在且未被改动 | ✓ |
| `assets/naming-style-guide.md` 严禁清单完整 | ✓ |
| `assets/candidate-contract.md` 5 个"不会得到什么"完整 | ✓ |
| `assets/report-template.md` 含强制免责声明 | ✓ |
| `evals/red-team-cases.md` 28 个 case 全部存在 | ✓ |

任一项 fail,**不可分发**。

## 4. 候选人首次使用提示语示例

向候选人介绍本 Skill 时,使用以下提示语:

> "这是一个'认知机制萃取' Skill。它通过 3-4 轮文字对话,帮你从工作事件中萃取出反复有效的判断模式,产出一份可以用来改简历、做面试准备的报告。
>
> **它不做**: 招聘评估、人格测试、心理诊断、岗位匹配。
> **它做**: 把你工作中'你会但说不清'的判断方式结构化出来。
>
> 准备好简历后,告诉我 '我想用认知机制萃取这个 skill',就可以开始。
>
> 预计 60-120 分钟,可以分多轮做。"

## 5. 升版与回归

每次升版后:

1. 跑 `evals/golden-cases.md` 的 5 个 case。
2. 跑 `evals/red-team-cases.md` 抽样 5-10 个 case。
3. 更新 `SKILL.md` 的 `metadata.version`。
4. 把测试结果归档到 `evals/results-<version>.md`。
5. 更新 `INSTALL.md` 与 `HANDOFF.md`(若仍在开发期)。

## 6. 故障处理

| 现象 | 可能原因 | 处理 |
|---|---|---|
| Skill 触发不灵敏 | `description` 或 `when_to_use` 太抽象 | 修改 SKILL.md frontmatter,加更明确的关键词 |
| 候选人觉得问题太宽 | 阶段 3 没切到 micro-incident 模式 | 检查 `workflows/low-engagement-recovery.md` 是否被加载 |
| 报告里出现伪量化 | 自检清单失效 | 检查 `SKILL.md` 第 8 节自检清单,加自动化校验 |
| 命名出现赞美词 | naming-style-guide 严禁清单失效 | 检查 `assets/naming-style-guide.md` 是否被完整加载 |
| 候选人不认机制 | 阶段 5 的复述步骤被跳过 | 检查 `prompts/naming-and-validation.md` 步骤 5 |

## 7. 联系与反馈

本 Skill 是基于 `skill-design-report.md` 的落地实现。
反馈与改进建议保存到 `HANDOFF.md` 第 4 节,或新建 `feedback-<date>.md` 在项目根目录。
