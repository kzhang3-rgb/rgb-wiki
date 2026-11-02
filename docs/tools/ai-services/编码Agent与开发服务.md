# 编码 Agent 与开发服务

[返回 AI 服务与模型入口](README.md) · [返回工具使用](../README.md) · [返回首页](../../../README.md)

这一页收录**把模型封装成编码助手的产品**。它们本身不是大模型厂商，而是消费多家模型的能力，提供 CLI、IDE 或桌面端，替你读写代码、跑命令、提改动。

- 状态：草稿
- 最近核验：2026-11-02（阅读官网首页与定价页，未注册、未安装 CLI、未订阅）

## 收录标准

- 只收官方站点；标明形态（CLI / 桌面端 / IDE 插件）、是否订阅制、是否单独提供 API。
- 官方自述的规模、性能与效果数字一律标注"官方自述、未验证"，不作为结论。
- 价格、积分额度、可选模型数量变化快，**逐条标注核验日期**。

## 服务列表

| 服务 | 提供方 | 能做什么 | 官网 | 开放 API | 免费额度 / 计费 | 限制与注意 | 核验日期 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Command Code | commandcode.ai | 面向开源模型的编码 Agent：命令行（`npm i -g command-code`）加桌面应用，可读写代码、跑命令、接 MCP，内置 skills、记忆与 hooks | <https://commandcode.ai> | 有，GOAT 档起含 API access；另有独立的 Provider 计划，提供 OpenAI / Anthropic 兼容端点 | 订阅制：Go \$1/月（含 \$10 credits）、GOAT \$10/月（最高 \$100 credits）、Max \$100/月（\$150 credits）；Provider 计划 \$15/月 + 按量付费、零加价。各档另收"处理费" | 主打"开源模型"但仍含 premium 档；积分与按模型额度会变；需联网并接受代码上传 | 2026-11-02 |
| OpenCode | opencode.ai；仓库 [anomalyco/opencode](https://github.com/anomalyco/opencode)（原 `sst/opencode`） | 开源（MIT）编码 Agent：终端界面、桌面端与 IDE 扩展三种形态；可多会话并行，经 Models.dev 支持 75+ 提供方与本地模型 | <https://opencode.ai> | 无自有编码 API；通过第三方提供方、已有订阅（ChatGPT Plus/Pro、GitHub Copilot）或官方 Zen 服务接入模型 | **工具本身 100% 免费**，自带一组免费模型（无需账号）；官方 Zen 为付费模型服务；接自有提供方按其自身计费 | 官网自称"22 万+ Star"与 API 的 157,204 不一致；仓库已由 `sst/opencode` 转为 `anomalyco/opencode`；开放 Issue 6,486 个，体量很大 | 2026-11-02 |

## 条目说明

### Command Code

定位是"**best coding agent for open models**"（官方措辞）——不自己造大模型，而是把 DeepSeek、Qwen、Kimi、GLM、MiniMax、腾讯混元、Groq 等约 50 个模型接进来做编码助手。官网给出的可选模型里，开源系与国产系占比明显，这是它与 Claude Code / Cursor 这类产品的主要差异点。

形态有两条：

- **命令行**：`npm i -g command-code`，支持交互式、无头（`-p`）、`--yolo` 与后台沙箱等模式。
- **桌面应用**：提供 macOS / Linux / Windows 版本，含改动审阅、命令执行与应用预览。

几个值得注意的设计点：

- **hooks**：可拦截不安全命令、强制项目规则——这是把"危险操作闸门"做进产品的一层。
- **记忆与技能**：`/agents`、`/memory` 跨会话保留，`/skills`、`/commands`、`/mcp`、插件可复用，且强调"可改"。
- **taste-1**：官方称会从每次接受/拒绝/编辑中学习"你的编码口味"，并生成项目级技能。属官方自述，未验证效果。
- **协作**：`/share` 分享会话，`npx taste push/pull` 在团队间同步"口味"。

定价页把档位分得比较细（Go / GOAT / Max / Provider），其中 **Provider 是单独面向 API 使用者的计划**：按量付费、零加价、OpenAI 与 Anthropic 兼容端点。也就是说它在"编码 Agent"之外，**兼有聚合网关的属性**——如果你只想要统一的多模型 API，走 Provider 比订阅 Agent 更对口。

官网自述的规模数字（10 万+ 开发者、4 万+ 付费用户、130T+ tokens served），以及首页提到的 \$5M 种子轮，均为**官方自述、未验证**。

### OpenCode

开源（MIT）的编码 Agent，主推终端界面，另有桌面端与 Web / IDE 扩展。与 Command Code 最关键的差异是**它自己不锁定模型售卖**：

- **三种用模型的方式**：① 用自带的免费模型（官方称无需创建账号）；② 复用已有订阅（ChatGPT Plus/Pro、GitHub Copilot）；③ 接任意提供方（经 Models.dev 支持 75+，含本地模型）或官方 Zen。
- **Zen** 是官方提供的模型服务，官方说法是"挑过、并针对编码 Agent 测试过"的模型集合。它属付费服务，也是这个开源项目的主要变现点。
- **隐私边界要连起来读**：官网称 OpenCode 不存储代码与上下文数据，但同一页的 FAQ 又写"只有在你使用我们的免费模型时才会存储数据与信息"。**两句话要一起看**——用自带免费模型的代价，是数据会留下。

**两处必须注意的数据问题（本次核验发现）**：

1. **官网数字与 GitHub API 不一致**。官网首页自称"220,000+ GitHub stars、950 contributors、20,000+ commits、每月 1600 万开发者"，而 GitHub API 显示主仓库为 **157,204 Star、18,311 Fork、610 关注、6,486 个开放 Issue**（2026-11-02）。差额不小，官网数字可能包含 fork / 镜像，或属宣传口径。**本页按 API 值记录，并保留该差异。**
2. **仓库换过 owner**。官网 FAQ 指向 `anomalyco/opencode`，而更早流传的地址是 `sst/opencode`——两者 **API `id` 相同（975734319），是同一个仓库**，只是 owner 从 `sst` 换到了 `anomalyco`。直接抓旧路径会拿到较早的快照（33,568 Star、最后推送 2025-11-22），**不要当成两个不同项目**。

另有两处使用提醒：默认分支是 `dev` 而非 `main`；API 返回的最后推送时间为 2026-05-09，与项目实际热度不太相符（疑似抓到缓存数据），需要判断活跃度时**建议直接看仓库页面**。

## 相关笔记

- [oracle：给编码助手请第二意见的 CLI](../github-projects/oracle-给编码助手请第二意见的CLI.md)：同样面向编码场景，但定位是"请第二个模型复核"，而非主力编码 Agent。
- [prompt-optimizer：提示词优化器](../github-projects/prompt-optimizer-提示词优化器.md)：支持多家模型与 MCP，可对照其多模型接入方式。
- [agents.md：给编码助手用的 AGENTS 文件格式](../github-projects/agents.md-给编码助手用的AGENTS文件格式.md)：编码 Agent 的项目级指令约定，与这里的 `/agents`、`/memory` 是同一类问题。

## 参考来源

Command Code：

- 官网首页：<https://commandcode.ai>
- 定价页：<https://commandcode.ai/pricing>
- 官方文档：<https://commandcode.ai/docs>

OpenCode：

- 官网首页：<https://opencode.ai>
- 仓库（当前 owner）：<https://github.com/anomalyco/opencode>
- 仓库（旧地址，同一仓库）：<https://github.com/sst/opencode>

核验日期：2026-11-02
