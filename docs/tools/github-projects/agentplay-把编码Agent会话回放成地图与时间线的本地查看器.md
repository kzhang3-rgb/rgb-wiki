# agentplay：把编码 Agent 的会话回放成"地图 + 时间线"的本地查看器

> 记录一个把"AI 编码助手到底干了什么"变得**可看**的工具（`Entelligence-AI/agentplay`，描述 `Replay your coding agent sessions as a map and a timeline`）：一条命令 `uvx agentplay@latest` 就会**扫描本机已有的各 Agent 会话记录**（`~/.claude`、`~/.codex`、`~/.cursor` …），把它们构建成**每一次会话的"地图 + 时间线"回放**，并在 `127.0.0.1` 上打开一个**纯本地查看器**；**无账号、无 API Key、会话数据不出机器**。三个值得记的点：**① 定义了一份独立的会话格式 `agentplay/v1`**（一个会话一份 JSON，配套 JSON Schema 与 CI 校验，明说"任何做 Agent / 可观测 / transcript 的工具都可以 emit 或读它"）；**② 有一份写进文档的"脱敏契约"**——地图里**永不包含**提示词 / 回复文本、工具输入输出、文件内容与命令参数（只留动词），并有 `relative / dirs_only / hashed` 三种路径模式；**③ 唯一一次网络调用是"可选的邮箱注册"**，可用 `AGENTPLAY_NO_NETWORK=1` / `DO_NOT_TRACK=1` 关掉。**未 clone、未 `uvx`/`pip install`、未运行任何命令、未在任何 Agent 会话上跑过、未审计源码。**

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（371 行）、`LICENSE`（Apache-2.0，11,358 B）首部、文件清单（280 项）、ungh.cc 镜像元数据与 releases、shields.io 徽章；**未 clone、未安装、未运行 `agentplay`、未在真实或构造的会话数据上验证任何解析 / 脱敏 / 回放、未审计源码**）
- 适用范围：装了**受支持编码 Agent**（Claude Code / Codex / Cursor / Copilot / OpenCode / Kiro / Gemini CLI / Antigravity CLI / Command Code / Goose）并想复盘"它到底做了什么"的个人与团队；想参考"**本地优先 + 脱敏契约**"或"**一份可被外部复用的会话格式**"设计的工具作者。需 **Python 3.11+**（**除标准库外无运行时依赖**）；查看器为 TypeScript / React

## 核心结论

**一句话定位（README）**："See what your coding agent actually did."——**本地、开源**，把每一次会话回放成**一张地图**（它碰过的所有东西）与**一条时间线**（可播放）。`uvx agentplay@latest` 一条命令：找到本机已有的会话 → 为每个会话构建**脱敏回放** → 打开本地查看器。**无需账号、无需 API Key，会话数据不离开本机。**

**① "地图 + 时间线"是两种视图。** 地图是**径向树**：仓库 / 目录 / 文件（读过的、看过的、改过的）、shell 动词、MCP 服务器与工具、技能、子 Agent、web 主机、错误类别、模型，以及产出的输出（提交 / PR / 工单）。时间线可按 **1x / 4x / 16x** 回放，支持拖动、切换"步骤轴 / 墙钟时间"；泳道展示提示词、错误、延迟、输出、编辑、探索、MCP、技能、工具、模型、成本、token、子 Agent、上下文大小，并标出压缩（compaction）。**每个视图都是一个 URL**，指向某个时刻的链接可由"在同一份数据上跑查看器"的人重新打开。

**② 支持十种编码 Agent（README 表格）。** Claude Code（**保真度最高**，直接从 JSONL 读，含 hooks / compactions / stop reasons / 延迟）· Codex（CLI / IDE / Desktop）· Cursor（agent CLI 与 IDE composer）· GitHub Copilot CLI · GitHub Copilot in VS Code · OpenCode · Kiro · Gemini CLI · Antigravity CLI（`agy`）· Command Code · Goose。多数非 Claude Code 的 Agent 是"**main lane only**"（会话自身的工具调用齐全，但子 Agent 工作不在地图上单独分泳道）。用 `agentplay agents` 可看本机发现了哪些。

**③ ⭐ 独立的会话格式 `agentplay/v1`。** 每个会话一份 JSON 文档，字段含 `session` / `totals` / `repos` / `files` / `agents` / `tasks` / `prs` / `events` 等；配 **JSON Schema**（`schema/agentplay-v1.schema.json`，**CI 校验每一份构建出的地图**）。README 明确：**格式刻意独立于本工具**——"若你在做 Agent、可观测产品或 transcript 工具，可以 emit 或读 `agentplay/v1` 并复用其查看器"。兼容旧名 `entelligence.session-map/v1`（同形状）；**字段只增不改用途**，读者须忽略未知字段。

**④ ⭐ 写进文档的"脱敏契约"（Redaction Contract）。** README 有一整节 Privacy，明列**地图永不包含**：提示词文本 / 助手文本 / 推理、工具输入输出或文件内容、**命令参数**（shell 命令只记动词，如 `git log` / `pytest`，绝不记 `git log --author=...`）、子 Agent 的提示词 / 描述（只记类型与模型）、用户消息文本（只记长度与图片·链接计数）。**地图确实包含**：仓库名与 remote、仓库相对文件路径、工具 / MCP 服务器 / 技能名、模型名、token 计数、计时、错误类别、退出码；仓库外的路径会收敛成粗粒度桶（如 `/tmp (scratch)`、`~/Downloads`、`outside-repo`）。**路径模式**（`--path-mode`）三档：`relative`（默认）/ `dirs_only`（段名替换成 `•<hash>`）/ `hashed`（每段用 `AGENTPLAY_HMAC_KEY` 做 HMAC-SHA256，无 key 则退回 `dirs_only`）。完整契约与"强制它的测试"在 `docs/format.md`。

**⑤ 唯一一次网络调用是"可选的邮箱注册"。** README Privacy 专节说明：首次打开查看器会**邀请留邮箱**（发新版通知）。若填写，agentplay 会向 Entelligence 发送：事件（注册 / 之后断开）、该邮箱、一个随机 install id（存 `~/.agentplay/account.json`）、版本号与时间——**不含任何会话数据 / 路径 / 仓库名**。选"Continue without email"则什么都不发。`DO_NOT_TRACK=1` 或 `AGENTPLAY_NO_NETWORK=1` 可**关掉该邀请并阻断请求**。README 直接给出自查方式：`grep -rn urllib.request python/src/` 只会命中 `account.py`。**查看器自身不加载任何远程字体 / 脚本，也不主动向 Entelligence 取任何东西。**

**⑥ 一套 CLI + 本地只读服务。** 命令：`view`（默认）/ `build` / `list` / `stats` / `export` / `import` / `agents`。`stats` 按 Agent 汇总一个时间窗内的会话 / 成本 / 时长 / 调用 / 错误（**成本是按 `_core/pricing.py` 里公开标价的估算**，`n/a` 表示该 Agent 记不到可用的成本——Cursor 即是）。服务只监听 `127.0.0.1`（默认端口 **7317**），只接受来自自身 origin 的 POST。

**⑦ 关注度与状态（ungh 快照 / shields，2026-11-19）。** **369 Star / 48 Fork / 0 关注**（ungh；shields 同期 370 / 47）；**13 个开放 Issue、贡献者 15 人**（shields）；**2026-10-14 创建**、`pushedAt` **2026-11-11**、`updatedAt` **2026-11-15**；`main`；**0 Release**（走 PyPI 分发：`uvx agentplay@latest` / `pipx run agentplay` / `pip install agentplay`）；**280 个文件**；**Apache-2.0**（`LICENSE` 11,358 B + `NOTICE`）；shields 顶层语言 **68.4%**（Python）。

**⑧ 商业背景（README 明写）。** 由 **[Entelligence](https://entelligence.ai)** 维护，后者另有**托管团队版**（把评审意见追溯到引发它的会话编辑、跨工程师视图、团队级建议、采纳度度量）。README 声明：**开源工具不依赖它，也不会为了卖它而被降级。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Entelligence-AI/agentplay](https://github.com/Entelligence-AI/agentplay) |
| 描述（GitHub） | `Replay your coding agent sessions as a map and a timeline` |
| 归属 | 组织 **Entelligence-AI**；维护方 [Entelligence](https://entelligence.ai)（另有托管团队版） |
| 许可证 | **Apache-2.0**（`LICENSE` 11,358 B；另有 `NOTICE`、`THIRD_PARTY_NOTICES.md`）；`Copyright 2026 Entelligence AI, Inc.` |
| 主语言 | shields 顶层语言 **68.4%**（Python；查看器为 TypeScript / React） |
| 分发 | PyPI 包 **`agentplay`**（`uvx agentplay@latest` / `pipx run agentplay` / `pip install agentplay`）；需 **Python 3.11+**，**除标准库外无运行时依赖** |
| 关注度 | **369 Star / 48 Fork / 0 关注**（ungh，2026-11-19；shields 同期 370 / 47）；**13 开放 Issue、贡献者 15 人**（shields） |
| 创建 / 最近推送 | **2026-10-14 创建**；`pushedAt` **2026-11-11**、`updatedAt` **2026-11-15** |
| 默认分支 / Release / 文件数 | `main`；**0 Release**；**280 个文件** |
| 支持的 Agent | Claude Code · Codex · Cursor · GitHub Copilot CLI · Copilot in VS Code · OpenCode · Kiro · Gemini CLI · Antigravity CLI（`agy`）· Command Code · Goose |
| 会话格式 | **`agentplay/v1`**（一会话一 JSON），JSON Schema 见 `schema/agentplay-v1.schema.json`；兼容旧名 `entelligence.session-map/v1` |
| 仓库结构 | `python/`（129）· `viewer/`（124）· `.github/`（7）· `docs/`（5）· `scripts/`（4）· `schema/`（1） |
| 根目录可见文件 | `README.md`（27,100 B）、`install.md`（3,744 B）、`CONTRIBUTING.md`、`SECURITY.md`、`CODE_OF_CONDUCT.md`、`THIRD_PARTY_NOTICES.md`（4,320 B）、`NOTICE`（575 B）、`LICENSE`、`Makefile` 等 |
| 关键环境变量 | `AGENTPLAY_HOME`（默认 `~/.agentplay`）、各 `AGENTPLAY_*_HOMES`、`AGENTPLAY_HMAC_KEY`、`DO_NOT_TRACK` / `AGENTPLAY_NO_NETWORK`、`NO_COLOR` |

## 它能做什么

- **把已有会话变成可读的回放**：不需要当初就开录制——**数据本来就在**（会话 transcript 已在 `~/.claude` / `~/.codex` / `~/.cursor` 等目录里），agentplay 只是把它变得可看。
- **回答"过程"问题**：改这个文件前读了什么、哪些命令失败了、同一个命令重试了几次、上下文在何处填满、子 Agent 带回了什么、这次会话产出了哪些 PR / 提交 / 工单。
- **增量构建**：多次运行只重建 transcript 有变化的会话（示例输出"Built 412 new, 0 unchanged, 0 skipped"）。
- **导出 / 导入地图**：`export` 写出单会话地图（`.json` / `.json.gz`）并提示其中含哪些仓库名与多少路径；`import` 可把队友 / 另一台机器的地图加入本地库。
- **把地图当数据用**：`agentplay/v1` 独立于工具，可被外部 Agent / 可观测产品 emit 或读取，并复用查看器。
- **查看器可单独跑**：在 `viewer/` 里 `pnpm dev` 会加载三份合成演示会话；也可把任意 `agentplay/v1` 文件**拖到页面上**。

## 使用前需要注意

- **⚠️ "本地 / 脱敏"是设计目标，但本笔记未验证**：README 声称数据不出机器、地图不含提示词与文件内容——**这是项目的自述与它自己的测试**，我**未审计源码、未实际构建过一份地图去核对**。授权 / 分享前建议按 README 的 `grep -rn urllib.request python/src/` 自查，并对公开分享的会话用 `--path-mode dirs_only` 重建。
- **⚠️ 仍会读你的完整 transcript**：脱敏发生在**构建地图时**；解析阶段读的是原始会话文件（含原文）。本地索引 `~/.agentplay/index.json` 还会存**会话标题与任务标签（提示词开头片段）**，可用 `--no-titles` 关闭并清除已存内容（该 flag 为**单次运行**，下次不带又会写回）。
- **⚠️ 地图仍含敏感元信息**：仓库名与 remote、仓库相对路径、模型名、错误类别、退出码等**会**进入地图；README 提示"公开分享前先删 `repos[].remote`"。
- **⚠️ 已知限制（README "Known limitations"）**：**Cursor 会话没有 token / 成本数字**（它不写盘、agentplay 也不调其 API）；整体写入的"新建 vs 覆盖"可能判断不出（只在会话已见过该文件时才记为编辑）；**成本是按公开标价的估算**，未建模套餐 / 企业 / 批量价；**大会话每张地图上限 20,000 事件**（超出标记 `truncated`），**transcript 超 512 MB 跳过**；**Windows 支持但测试少于 macOS / Linux**。
- **⚠️ 极新且 0 Release**：**2026-10-14 创建**，核验时约一个月；**0 Release**、280 文件；解析器随各 Agent 版本变动而**易碎**（README 专门征集"解析坏掉的 fixture transcript"）。
- **⚠️ 版本 / 关注度为快照**：369 / 48 / 15 等为 **2026-11-19 快照**；`uvx agentplay@latest` 取的是最新 PyPI 版本，与本笔记核验时点可能不同。
- 依赖边界：**Python 侧除标准库无运行时依赖**；但从源码构建查看器需 **Node 22+ 与 pnpm 10.x**（版本锁定在 `viewer/package.json`）。

## 什么时候会再看

- 想复盘"**这次编码会话到底改了什么、读了什么、错在哪**"、又不想把会话上传到任何云时；
- 想给**自建 / 自托管**的编码 Agent 工作流补一层"**本地可观测 + 可分享的脱敏回放**"时；
- 想研究"**多 Agent transcript 如何归一成一份中立格式**"（`agentplay/v1` 的字段设计、只增不改的兼容策略、Schema + CI 校验）时；
- 想参考"**隐私优先设计怎么写成可执行契约**"（脱敏永不包含清单 / 路径模式 / 唯一网络调用的自查方式）时；
- 想对照"**AI 编码的观测工具**"的两端：[codeburn](codeburn-本地追踪AI编码Token用量与成本.md) 算**花了多少钱** · 本篇看**做了什么事**。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都读本机 AI 编码会话文件、都"本地优先 / 不要账号"，一个算钱一个回放过程**：[CodeBurn：本地追踪 AI 编码 Token 用量与成本](codeburn-本地追踪AI编码Token用量与成本.md)——那是**事后计量成本 / token**（按模型 / 项目 / 任务 / 分支算花销）；本篇是**把会话回放成地图 + 时间线**（看它读了什么、改了什么、错在哪）。两者都从"本地已写下的会话文件"取数、都不上传，正好构成"**花了多少**"与"**做了什么**"两面。
- **"跑与编排 Agent" ↔ "回放它们到底做了什么"**：[paseo：跨桌面与移动编排多个编码 Agent 的开发环境](paseo-编排多个编码Agent的桌面与移动开发环境.md)——那边是**把多个编码 Agent CLI 编排进同一工作区**（跨设备、worktree 并行）；本篇是**事后回放某个 Agent 的会话**。两者覆盖编码 Agent 的不同环节，可对照"**编排运行**"与"**复盘观测**"。

## 参考来源

- 仓库主页：<https://github.com/Entelligence-AI/agentplay>
- README（371 行）：<https://github.com/Entelligence-AI/agentplay/blob/main/README.md>
- 会话格式规范：<https://github.com/Entelligence-AI/agentplay/blob/main/docs/format.md>（含脱敏契约）
- JSON Schema：<https://github.com/Entelligence-AI/agentplay/blob/main/schema/agentplay-v1.schema.json>
- 面向 Agent 的安装说明：<https://github.com/Entelligence-AI/agentplay/blob/main/install.md>
- 注册端点说明：<https://github.com/Entelligence-AI/agentplay/blob/main/docs/signup-endpoint.md>
- PyPI 包：<https://pypi.org/project/agentplay/>
- 许可证（Apache-2.0）：<https://github.com/Entelligence-AI/agentplay/blob/main/LICENSE>
- 核验时间：2026-11-19（ungh.cc 镜像的 repo / releases / files(main) 元数据、shields.io 徽章，以及 raw 通道的 `README.md` 与 `LICENSE`）
