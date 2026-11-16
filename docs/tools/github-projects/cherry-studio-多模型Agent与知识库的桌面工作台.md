# cherry-studio：把模型、Agent 与知识库收进一个桌面工作台

> 记录 CherryHQ 组织的主线项目：一个跨平台 AI 工作台，把多模型对话、Agent、知识库、生图与自动化放进同一个 Electron 客户端。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README、目录树与 GitHub API 元数据，未安装、未运行）
- 适用范围：Windows / macOS / Linux 桌面（Electron）；另有移动版另仓维护

## 核心结论

Cherry Studio 是 **CherryHQ 组织下 Star 最高的主线项目**（组织共 36 个公开仓库，其余多为移动版、文档站、官网、MCP 市场等配套）。它给自己的定位是 **"Your AI workspace for models, agents, and productivity."**——一个把"聊天 + Agent + 知识库 + 生图 + 自动化"收进同一处的桌面工作台，而不是单纯的多模型聊天框。

README 的开场描述就是功能清单：**"Chat with multiple models, work with agents, build knowledge bases, create images, and automate everyday tasks in one place."** 它支持 Windows / macOS / Linux 三端，并有独立的移动版（iOS / Android，见 `cherry-studio-app`）。

仓库的组织方式反映它是一个**成熟的 Electron 单体应用**：根目录有 `electron.vite.config.ts`、`electron-builder.yml`、`pnpm-workspace.yaml`（pnpm monorepo）、`packages/`、`src/`、`native/`、`migrations/`、`tests/`、`patches/`，并用 changesets（`.changeset/`）做版本管理。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [CherryHQ/cherry-studio](https://github.com/CherryHQ/cherry-studio) |
| 描述 | Your AI workspace for models, agents, and productivity. |
| 许可证 | **AGPL-3.0**（`LICENSE` 约 34.5 KB） |
| 语言 | TypeScript |
| 关注度 | **53,262 Star、5,178 Fork、205 关注、2,464 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2024-05-24 创建**，最近推送 **2026-11-05**（非常活跃） |
| 发行版 | 最新 **v2.1.10**（2026-10-31，23 个资产），发版节奏密集 |
| 体积 | 约 775,822 KB（`size` 字段，约 758 MB）；`pnpm-lock.yaml` 单文件约 1.15 MB |
| 主页 | <https://cherryai.com> |
| 主题标签 | `agent-skills`、`agent-workspace`、`agentic-workspace`、`ai-agents`、`ai-models`、`ai-workspace`、`llm`、`mcp-client`、`ollama` |
| 归属 | 组织 **CherryHQ**（其官网 blog 为 cherryai.com） |

## 它能做什么

- **多模型对话**：一个客户端里同时接多家模型（主题标签含 `ollama`，说明兼顾本地模型）。
- **Agent 工作台**：README 截图分两块——"Agents：与文件、工具和多步任务协作"与"Conversations：与模型和助手探索想法"。
- **知识库 / 生图 / 自动化**：README 明列为内置能力。
- **移动端**：`CherryHQ/cherry-studio-app`（约 4,056 Star）为 iOS / Android 版本。
- **配套仓库**：`cherry-studio-docs`（文档）、`cherry-studio-website` / `-docusaurus`（官网）、`cherry-studio-hmos-desktop`（鸿蒙）、`mcpmarket`（MCP 市场）、`embed-js`（Node RAG 框架）、`hanlin-ai`（SwiftUI LLM 聊天）、`stella`（Go 多用户多 Agent 记忆）。

## 使用前需要注意

- **许可为 AGPL-3.0（强传染）**：若要基于它做二次开发并对外提供服务，需评估 AGPL 的源码提供义务；商业使用前务必读 `LICENSE`（34.5 KB 全文）与 `commercial` 相关链接（README badges 里有 commercial 项）。
- **2,464 个开放 Issue**：对活跃的桌面客户端来说不算异常，但**这个数字把开放 Pull Request 也算在内**（GitHub API 的 `open_issues_count` 口径），不等于"未解决的 bug 有 2464 个"。
- **仓库体积约 758 MB**：属于大型 Electron monorepo，**建议浅克隆**（`git clone --depth 1`），否则拉取较慢。
- **仓库里"给 AI 看的元文件"体量差异很大**：`CLAUDE.md` 约 22 KB、`DESIGN.md` 约 10.5 KB、`PRIVACY.md` 约 16 KB、`.claude/` 与 `.agents/` 目录齐全，但根目录 `AGENTS.md` **只有 9 字节**（近乎空壳）——**别把"有 AGENTS.md"当成"有可读的协作约定"**。
- **本笔记记录的是组织下的旗舰仓库**：用户给出的是组织地址 `https://github.com/CherryHQ`，其中 Star 最高、最先对应"Cherry Studio"的就是本仓库；组织其余仓库见上"它能做什么"。
- 我**未安装、未运行、未订阅任何模型**：功能清单与截图描述均来自 README。

## 什么时候会再看

- 需要**一个客户端同时接多家模型**（含本地 Ollama），并想把知识库、Agent、生图放在一起时。
- 想对照"**桌面 AI 客户端**"这一类的产品形态与工程组织（Electron + pnpm monorepo + changesets）时。
- 需要参考"一个 Star 5 万+ 的 AGPL 桌面项目如何组织多语言文档（README.zh / CONTRIBUTING.zh / i18n 配置）"时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同类桌面 AI 客户端（不同许可、不同路线）**：[chatbox：桌面 AI 客户端社区版](chatbox-桌面AI客户端社区版.md)——那个是 GPLv3 的"社区版"，且**与闭源 pro 仓库双向同步代码**；本篇是 AGPL-3.0 的单一主线。两者都做"多模型桌面客户端"，可对照**许可策略与开源版定位**的差别。
- **自托管的个人 AI 助手**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——那是以自托管/常驻服务为形态的助手，本篇是本地安装的桌面客户端；一个"提供服务"，一个"给你一个客户端"。
- **面向编码场景的 Agent**：[opencode：开源终端编码 Agent](opencode-开源终端编码Agent.md)、[openfox：本地模型优先的编码助手](openfox-本地模型优先的编码助手-co-l-openfox的fork.md)——那两者聚焦"改代码"，本篇覆盖对话 / 知识库 / 生图等更宽的工作台场景。
- **被官方清单收录**：[awesome-deepseek-integration：DeepSeek 官方的"实用集成"清单](awesome-deepseek-integration-DeepSeek官方实用集成清单.md)——DeepSeek 官方为该清单里的条目写了 `docs/cherrystudio/README_cn.md`。注意"被收录"与"本项目自身的许可 / 维护状态"是两件独立的事。
- **同属"聚合多家 AI 服务"，用途不同**：[PriceAI：AI 订阅卡网渠道比价工具](PriceAI-AI订阅卡网渠道比价工具.md)——本篇是**把多家模型接进一个客户端使用**（偏"用起来"），那篇是**比较这些服务的价格与渠道来源**（偏"买得起"）；可对照"用起来"与"买得起"。
- **同为"多模型 + 知识库的桌面工作台"，广度不同**：[pinvou-agent：面向工作、设计与代码的开源桌面 AI Agent 工作台](pinvou-agent-开源桌面AI-Agent.md)——两篇都做多模型接入、知识库与 Agent 编排，那篇额外把**可视化设计与 ACP 编码 Agent（Codex / Claude Code / Kimi）**收进同一壳；可对照"知识库 + Agent"与"工作 / 设计 / 代码三合一"。
- **都在"多模型"上用力，层级不同**：[aimux：用 Rust 写的统一 LLM 接入层](aimux-用Rust写的统一LLM接入层.md)——本篇是**面向用户的桌面客户端**（一个界面里用多家模型），aimux 是**面向开发者的接入层**（一个接口调多家模型）；可对照"**多模型产品**"与"**多模型基础设施**"。

## 参考来源

- 项目仓库：https://github.com/CherryHQ/cherry-studio
- 官网与文档：<https://cherryai.com>、<https://cherryai.com/docs/en/>
- 移动版：https://github.com/CherryHQ/cherry-studio-app
- 组织仓库列表（README / 元数据）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读 README、目录树与元数据；未安装、未运行、未订阅任何模型。
