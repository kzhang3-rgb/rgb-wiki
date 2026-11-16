# Everything AI Coding：聚合编程 AI 扩展资源的周更索引与一键安装库

> 它把自己定位成"**编程类 AI 扩展资源的中转站**"：从 9 个以上上游来源持续抓取 MCP Server、Skill、Rule、Prompt、Plugin，去重、补全、用大模型打分，再以 Markdown 索引 + 网页目录 + 一条 CLI 命令的形态重新发布。**真正值得记的不是"清单很长"（约 4.4 万条），而是它把"谁值得装、装上安不安全"这件事拆成了可核查的评分与明确的免责声明——同时，它的一键安装脚本本身又是最需要谨慎对待的一环。**

- 状态：草稿
- 最近核验：2026-11-12（阅读 README（英文版）、`LICENSE`、`catalog/` 目录 README 片段、仓库文件树与元数据；**未 clone、未运行 `install.sh` / `install.ps1`、未安装任何命令、未抓取网页目录、未实际安装其中任何资源**）
- 适用范围：作为**编程类 AI 扩展资源的检索与选型入口**；创作者为组织账号 `zgsm-ai`（Costrict 团队）；本笔记只覆盖其"索引 + 安装器"层面，不评判被收录的第三方资源

## 核心结论

**一句话定位（README 自述）**：一个**持续采集、去重、补全、打分**的**编程 AI 扩展资源知识库**（"curated knowledge base"），覆盖 MCP Servers · Skills · Rules · Prompts · Plugins，**估算约 44000+ 条**。三种使用形态：**直接在 GitHub 上看目录**、**[网页目录](https://zgsm-ai.github.io/everything-ai-coding/)交互浏览**、或**用 Coding Hub 一条命令搜索与安装**。

**三个最值得记的点：**

1. **它把"收录"和"评估"分开了**：每条资源带一个 0–100 复合分 `final_score = LLM × 0.85 + health × 0.15`，其中 LLM 维度（编码相关性、文档完整度、描述准确度、写作质量、具体性、安装清晰度）以 1–5 打分，health 信号含新鲜度（🟢 Active / 🟡 Stale / 🔴 Abandoned）、热度（GitHub stars）与来源可信度；决策阈值是 **accept ≥ 65 / review 50–64 / reject < 50**。**这是"清单类项目"里少见的把"为什么推荐它"写清楚的做法。**
2. **它是"多上游 + 流水线"而不是人工精选**：上游覆盖 awesome-mcp-servers、Awesome-MCP-ZH、mcp.so、官方 MCP registry、anthropics/skills、skills.sh、awesome-cursorrules、prompts.chat 等；流程为 **Sync（周更抓取）→ Merge（跨源去重合并）→ Evaluate（单次 LLM 调用做六维打分 + 标签/摘要/技术栈补全 + 健康信号）→ Download → Bundle → Publish**，全部由 GitHub Actions 驱动。
3. **它同时是"资源库"和"客户端"**：除了数据，还提供四平台（Claude Code / Costrict / Opencode / VSCode Costrict）的**同名命令集**（`search` / `browse` / `recommend` / `install` / `uninstall` / `update` / `evo`），安装器按进程环境变量自动识别宿主平台。**也正因为它会往你的编码助手里装文件，它的安装方式才需要单独警惕（见"使用前需要注意"）。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zgsm-ai/everything-ai-coding](https://github.com/zgsm-ai/everything-ai-coding) |
| 描述 | 聚合精选编程 AI 扩展资源：MCP Servers、Skills、Rules、Prompts，周更索引 + 一键安装。 |
| 归属 | 组织账号 `zgsm-ai`（页面 `isOrg: true`、`isFork: false`、`isArchived: false`、`isMirror: false`、`Public`）；同组织下有更上层的 Costrict 平台 |
| 许可证 | **MIT**（`LICENSE` 1,065 字符，署名 `Copyright (c) 2025 Costrict`）——**只覆盖本项目自身（索引与工具），不覆盖被收录的第三方资源** |
| 主语言 | **Python 94.1%**（shields.io，2026-11-12；仓库内另有 TypeScript/React 的 `frontend/` 网页目录，占比很小） |
| 关注度 | **207 Star / 13 Fork / 1 关注 / 0 开放 Issue**（GitHub 页面内嵌 JSON 与 ungh.cc 镜像、shields.io 三者一致，2026-11-12） |
| 维护状态 | **2026-03-25 创建**；ungh 报 pushedAt **2026-11-09**、updatedAt **2026-11-09**（距核验日约 3 天，活跃）；页面 `sections.releases` 有值 |
| 默认分支 | `main` |
| 版本 | **40 Release / 40 tag**；并提供 Pages 站点（`pagesUrl: zgsm-ai.github.io/everything-ai-coding/`）、Packages 与 usedBy 区块 |
| 体积 | `main` 文件树约 **427 个文件**；但**数据体量很大**：`catalog/index.json` 约 **79 MB**、`catalog/search-index.json` 约 **37 MB**、`catalog/commands/index.json` 约 **2.9 MB** |
| 话题标签 | `agent-rules` · `ai` · `ai-coding` · `awsome-lists`（原文如此，疑为 awesome 拼写） · `developer-tools` · `mcp-servers` · `prompts-template` |
| 仓库结构 | `install.sh` / `install.ps1`（一键安装器）· `catalog/`（生成的目录数据：`index.json` / `search-index.json` + `mcp/` `skills/` `rules/` `prompts/` `plugins/` 各自 index + README）· `platforms/`（四平台的 skill 与 command 定义）· `scripts/`（约 50 个 sync / merge / 打分 / 生成脚本）· `ai-resource-eval/`（独立 Python 评估包 + 测试）· `frontend/`（React + Vite 网页目录）· `docs/wiki/`（架构、目录治理、运维手册等）· `.github/workflows/`（同步与发布流水线）· `tests/`（100+ 测试文件） |

## 它能做什么

按它自己给出的分类，收录规模（README 内嵌计数，2026-11-12）为：

| 类别 | 条目数 | 面向 | 说明（README 自述） |
| --- | --- | --- | --- |
| MCP Servers | **5167** | 连接外部工具 / 数据库 / 服务 | 支持 `mcp.so`、官方 registry（`active` + `isLatest`）等来源 |
| Skills | **26173** | 可复用能力与工作流 | 分三层来源，含 `skills.sh`（`install_count ≥ 1000`）等 |
| Rules | **245** | 编码约定与 AI 行为准则 | 来自 awesome-cursorrules、windsurfrules 集合（跨仓去重） |
| Prompts | **915** | 常用编码任务的提示词模板 | 来自 prompts.chat、wonderful-prompts |
| Plugins | **3372** | 打包型市场插件（skills + commands + agents + MCP） | 主要为 Claude Code；opencode 部分兼容；cursor / windsurf / costrict 无对应机制 |

（粗加约 3.6 万，README 顶部另有"约 44000+ curated"的近似计数；两者口径不同，**以 README 原文为准，不代为换算**。）

**Coding Hub 命令集**（四平台名称略有差异）：

| 命令 | 作用 |
| --- | --- |
| `search <keyword> [type:mcp\|skill\|rule\|prompt]` | 按关键词搜索，可按类型过滤 |
| `browse [category] [type:...]` | 按分类浏览 |
| `recommend [type:...]` | 按当前项目技术栈推荐 |
| `install <id>` / `uninstall <id>` | 安装 / 卸载某条资源 |
| `update` | 拉取最新的 Everything AI Coding 文件 |
| `evo <id>` | 用七维质量量表"进化"本地已装的 skill / prompt / rule（量表改编自 darwin-skill） |

**平台与路径差异**：Claude Code 命令分隔符用 `:`（`/eac:search`），其余三家用 `-`（`/eac-search`）；全局 skill 分别落在 `~/.claude/skills/eac/`、`~/.costrict/skills/eac/`、`~/.opencode/skills/eac/`；Costrict CLI 与 Opencode 的命令装在**项目内**（`.costrict/eac/commands/`、`.opencode/command/`），VSCode Costrict / Roo Code 装在 `~/.roo/commands/`。

**数据端点**（客户端从这里取数据）：搜索索引走 `https://zgsm-ai.github.io/everything-ai-coding/api/v1/search-index.json`，单条走 `/api/v1/{type}/{id}.json`，类型索引走 `/api/v1/{type}/index.json`，兜底用仓库内 `catalog/index.json`。

## 评分与数据来源（README 自述）

- **复合分**：`final_score = LLM × 0.85 + health × 0.15`。
- **LLM 维度**（1–5，每类最多 6 项）：编码相关性、文档完整度、描述准确度、写作质量、具体性、安装清晰度（仅 MCP 与 Skills）。
- **健康信号**：新鲜度（🟢/🟡/🔴）、热度（GitHub stars）、来源可信度。
- **决策**：accept ≥ 65 · review 50–64 · reject < 50；每个子目录 README 展示复合分 Top 100。
- **来源矩阵（部分）**：MCP ← awesome-mcp-servers、Awesome-MCP-ZH、mcp.so、registry.modelcontextprotocol.io（`active` + `isLatest`，README 称约 7,500 条）；Skills ← anthropics/skills、Ai-Agent-Skills、antigravity-awesome-skills、ai-agents-public、skills.sh 等；Rules ← awesome-cursorrules、rules-2.1-optimized、两份 awesome-windsurfrules（跨仓去重）；Prompts ← prompts.chat、wonderful-prompts。

## 使用前需要注意

- **⚠️ 一键安装是"管道进 shell"，这是最大风险点**：官方两种写法分别是 `curl -fsSL .../install.sh | bash` 与 PowerShell 的 `irm .../install.ps1 | iex`，**等于把远端脚本直接交给本机 shell 执行**（README 也提供了免 `curl | bash` 的手动下载回退路径）。脚本会把 skill 与命令文件写进你**编码助手的配置目录**（如 `~/.claude`、`~/.costrict`、`~/.roo`、项目内 `.opencode/`）——**装上什么、写到哪，最好先读脚本再执行**。
- **⚠️ "被收录"不等于"被安全审查"**：README 的 Disclaimer 明确声明**不保证第三方资源的安全性、可用性、准确性或合规性**，每条 MCP / Skill / Rule / Prompt **版权归原作者**。README 还专门带了一个 **SecurityBanner / 安全扫描**相关的组件与流程（文件树中有 `SecurityBanner.tsx`、`security_scan` 任务与 `test_*_security*.py`），说明它**尝试**做安全提示，但**"有评分"≠"已审计"**——安装前仍应自行看源码与许可。
- **⚠️ 仓库 MIT ≠ 你装的东西 MIT**：`LICENSE` 的 MIT 只覆盖这个索引与工具自身（署名 Costrict）。**被收录的每一条资源各自带自己的许可证**，遇到 AGPL / 商业双许可 / 无许可证的条目，风险按各自许可算。
- **数据体量大，克隆成本不低**：仅 `catalog/index.json` 就约 79 MB、`search-index.json` 约 37 MB，另有多个 MB 级中间产物；**若只想"看清单"，直接用网页目录或 raw 文件比全量 clone 更省事**。
- **它的"自动打分"依赖上游与模型**：分数由流水线中的 LLM 调用生成，**打分的模型、提示词版本与可复现性在本次核验范围内未逐项核对（未验证）**；把它当"参考信号"而非"客观评级"。
- **它带明显的自家产品导向（事实）**：README 有 "Why Costrict?" 段落，把用户引向同组织的 Costrict 平台，且数据来源与 `platforms/` 里反复出现 Costrict / csc / costrict-plugin-marketplace。**这是组织自营内容库，不是中立第三方索引**，选型时留意这层关系。
- **社区讨论很薄**：创建于 **2026-03-25**，**1 关注、0 开放 Issue**。**"0 开放 Issue"既可能是质量好，也可能是没人用**——缺少第三方问题沉淀，不宜当作"久经检验"。
- **我未做任何验证**：**未 clone、未运行安装脚本、未安装任何命令、未抓取网页目录、未安装其中任一资源**；上述分类计数、评分口径、来源矩阵与平台路径**均来自 README 与仓库文件自述**。

## 什么时候会再看

- 想给某个编码助手找**现成的 MCP Server / Skill / Rule / Prompt**，希望有一条统一入口 + 评分 + 分类时。
- 想参考"**多上游采集 → 跨源去重 → LLM 打分 → 生成索引与 README → 发布**"这套**内容流水线**怎么用 GitHub Actions 落地（`scripts/` 里有大量 `sync_*.py`、`merge_index.py`、`evaluate` 相关脚本与 100+ 测试）。
- 想研究"**把同一份资源目录适配到多个平台**"的写法（四套 `platforms/` 定义 + 按环境变量自动识别宿主平台）。
- 想对照"**清单类项目如何交代收录标准与免责边界**"（评分维度公开 + Disclaimer 明确 + 安全提示组件）。
- 评估是否值得把它的命令装进自己的助手时，**重点复核安装脚本会改动哪些目录**。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都覆盖"Skill 作为可复用能力包"，一端是检索、一端是生产**：[github-skill-forge：把任意 GitHub 仓库转成 Trae 技能](github-skill-forge-把GitHub仓库转成Trae技能.md)——本篇从生态里**检索、评分并安装**别人做好的 Skill；那篇是**把任意仓库自动变成 Skill**（脚本克隆 + 生成上下文包 + 写入 `.trae/skills/`，仓库**无许可证**且已停更）。两者合看覆盖"找 Skill"与"造 Skill"，且都涉及"往本地装东西"的供应链风险。
- **同为"围绕 MCP 的清单/入口"，但形态不同**：[servers：MCP 官方参考服务器合集](servers-MCP官方参考服务器合集.md)——那是**官方参考实现的少量样本**（现只保留约 7 个）；本篇是**跨多个上游聚合的海量目录 + 评分**。想"照着官方样本自己写 MCP"看前者，想"海选现成 MCP"看本篇。
- **同为"聚合清单 + Actions 校验流水线"，但一个自动、一个人工**：[public-apis：人工策展的免费 API 清单](public-apis-人工策展的免费API清单.md)——本篇是**多上游采集 → LLM 评分 → 一键安装**的重型流水线；那篇是**人工策展 + 只校验格式与链接**的轻量做法（`scripts/validate/format.py`、`links.py` + 三个 workflow）。两者可对照"校验到什么程度"与"谁来做判断"。
- **都覆盖"Skill 作为可复用能力包"**：[book-to-skill：把技术书转成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇讲**如何把一本书编译成一个 Skill**；本篇讲**如何从生态里检索、评分并安装别人做好的 Skill**。两者合看覆盖"造 Skill"与"找 Skill"两端。
- **都与"给编码助手下发项目级指令"相邻**：[agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——那篇是**AGENTS.md 这一格式约定**；本篇 `rules` 类目聚合的正是**编码助手的行为规范/规则集**，可对照"手写一份 AGENTS.md"与"从目录里挑现成规则"。
- **同属"把别人的 Agent 资产装到你机器上"，来源与规模不同**：[agency-agents：326 个 AI 专家角色的 Agent 定义库](agency-agents-326个AI专家角色的Agent定义库与多工具安装器.md)——那篇是**单一上游的自有成套资产**（326 个角色 `.md` + 约 15 种工具格式的 `convert.sh`/`install.sh`；README 自报 "230+" 而实测 326，OpenCode 只注册约 119 个）；本篇是**跨上游聚合 + 评分 + 一键安装**。可对照"**一家成套出品**"与"**海选聚合**"两种分发形态。
- **同属"Agent Skill 的聚合与索引"**：[qiaomu-seo：覆盖 Google、Bing 与 AI 搜索的 SEO 技能](qiaomu-seo-覆盖Google与Bing及AI搜索的SEO技能.md)——本篇是**技能 / 扩展的索引与一键安装入口**，那篇是**可直接装入的一个具体技能**。可对照"**资源目录**"与"**单体技能**"。
- **同属"分发给多种 Agent 的技能 / 资源库"，粒度不同**：[learn-from-materials：把 PDF 与书变成可测验学习网页的技能](learn-from-materials-把PDF与书变成可测验学习网页的技能.md)——本篇是**资源索引 + 一键安装**（跨上游聚合、评分、分发），那篇是**单一但完整的 Skill**。可对照"**目录 / 分发层**"与"**单体技能**"。

## 参考来源

- 项目仓库与 README（英文）：https://github.com/zgsm-ai/everything-ai-coding
- 简体中文 README：https://github.com/zgsm-ai/everything-ai-coding/blob/main/README.zh-CN.md
- 许可（MIT，Copyright (c) 2025 Costrict）：https://github.com/zgsm-ai/everything-ai-coding/blob/main/LICENSE
- 网页目录：https://zgsm-ai.github.io/everything-ai-coding/
- 仓库 Wiki / 治理文档：https://github.com/zgsm-ai/everything-ai-coding/tree/main/docs/wiki
- 元数据（Star / Fork / 关注 / Issue / 语言 / Release / 创建与推送时间）：GitHub 页面内嵌 JSON、ungh.cc 镜像与 shields.io，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**未 clone、未运行 `install.sh` / `install.ps1`、未安装任何命令、未抓取网页目录、未实际安装其中任何资源**；分类计数、评分口径、来源矩阵、平台路径与安全提示流程均来自 README 与仓库文件自述；打分流水的模型与提示词版本**未逐项核验**。**一键安装为"远端脚本管道进 shell"，被收录资源的安全性与许可由各自作者与许可决定，使用前请自行阅读脚本、源码与许可。**
