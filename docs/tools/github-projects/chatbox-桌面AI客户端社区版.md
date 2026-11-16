# chatbox：桌面 AI 客户端的社区版（GPLv3）

> 记录一个"重新开源"的桌面客户端：Chatbox 社区版，与闭源 pro 仓库双向同步代码。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README、目录树与 GitHub API 元数据，未安装、未运行）
- 适用范围：Windows / macOS / Linux 桌面（Electron）

## 核心结论

Chatbox 是**桌面 AI 客户端**里的老牌项目之一，自我描述为 **"Your Ultimate AI Copilot on the Desktop"**——ChatGPT、Claude 及其他 LLM 的桌面客户端，覆盖 Windows / Mac / Linux。

本仓库的关键前提写在 README 里：**"This is the repository for the Chatbox Community Edition, open-sourced under the GPLv3 license."** 并附了一条指向 Issue #2266 的链接——标题是 **"Chatbox is going open-source Again!"**（Chatbox 再次开源）。也就是说，这个仓库是**社区版（Community Edition）**，而且 README 明确写着：

> We regularly sync code from the pro repo to this repo, and vice versa.

即**代码与闭源 pro 仓库双向定期同步**。这一点决定了它的定位：开源的部分能用、能读，但它不是商业版的全部，且公开的提交历史不等同于产品的全部开发过程。

一个容易被忽略的事实：仓库虽挂在 `chatboxai` 组织下，但贡献者榜前列仍是原作者 **`Bin-Huang`**（331 次提交），说明它是从作者个人仓库迁移/接管的。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [chatboxai/chatbox](https://github.com/chatboxai/chatbox) |
| 描述 | Powerful AI Client |
| 许可证 | **GPL-3.0**（`LICENSE` 约 35 KB） |
| 语言 | TypeScript |
| 关注度 | **42,254 Star、4,310 Fork、214 关注、1,364 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2023-03-06 创建**，最近推送 **2026-10-29**（活跃） |
| 发行版 | 最新 **v1.23.5**（2026-09-24），**Release 均为 0 资产** |
| 体积 | 约 64,314 KB（`size` 字段，约 63 MB）；`pnpm-lock.yaml` 单文件约 1.08 MB |
| 主页 | <https://chatboxai.app> |
| 主题标签 | `assistant`、`chatbot`、`chatgpt`、`claude`、`claude-code`、`copilot`、`deepseek`、`gemini`、`gpt`、`gpt-5`、`ollama`、`openai` |
| 贡献者 | 74 人，主力 `themez`（882）、`Bin-Huang`（331）、`cherishh`（276） |

## 它能做什么

- **多模型桌面客户端**：同时覆盖 OpenAI / Claude / Gemini / DeepSeek 等云端模型与 Ollama 本地模型（见主题标签）。
- **桌面三端**：Windows / Mac / Linux，Electron 打包（根目录 `electron.vite.config.ts`、`electron-builder.yml`、`vite.config.web.ts`）。
- **工程组织**：pnpm workspace，含 `src/`、`packages/`、`resources/`、`scripts/`、`tests`（`test/`）、`docs/` / `doc/`、`.github/`；另有 `team-sharing/`、`tasks/`、`features/` 等目录。

## 使用前需要注意

- **"社区版"是核心前提**：README 明说代码与 **pro 仓库双向同步**，**开源仓库不是完整产品**。判断能力边界时，应以"社区版有什么"为准，而不是假设"GitHub 上看到的就是全部"。
- **GPL-3.0（强传染）**：若要基于它分发衍生作品，需遵循 GPLv3 的源码开放义务；商业使用与闭源集成前务必读 `LICENSE`。
- **Release 均为 0 资产**：`v1.23.5` 等 Release **不挂安装包**，安装包走官网 `chatboxai.app`。别把 Release 页当作下载入口。
- **1,364 个开放 Issue**：`open_issues_count` 把开放 PR 一并计入，不代表全部是未解决的 bug。
- **不建议把模型凭证交给不信任的客户端**：作为本地客户端它通常把配置存放在本机，具体存取方式**我未审计**；涉及 API Key 时以"本地优先"为前提，但仍属自担风险的用法。
- 我**未安装、未运行、未登录任何模型**：功能与同步机制均来自 README 自述。

## 什么时候会再看

- 需要**一个开源的桌面 AI 客户端**，又想要接近商业产品的完成度时。
- 想研究"**开源社区版 + 闭源 pro 版双向同步**"这种商业模式对开源仓库的影响时（提交历史、Issue、Release 都只反映社区版这一侧）。
- 想参考一个 4 万+ Star 的 Electron + pnpm monorepo 如何组织 i18n、打包与测试时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同类桌面 AI 客户端（不同许可与开源策略）**：[cherry-studio：把模型、Agent 与知识库收进一个桌面工作台](cherry-studio-多模型Agent与知识库的桌面工作台.md)——那个是 AGPL-3.0 的单一主线、功能面更宽（Agent / 知识库 / 生图）；本篇是 GPLv3 的"社区版"、与闭源版同步。可对照**"开源即主线"与"开源为社区版"**两种做法。
- **自托管的个人 AI 助手**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——那是常驻自托管的助手形态，本篇是可安装在桌面的客户端。
- **被官方清单收录**：[awesome-deepseek-integration：DeepSeek 官方的"实用集成"清单](awesome-deepseek-integration-DeepSeek官方实用集成清单.md)——DeepSeek 官方为该清单里的条目写了 `docs/chatbox/README_cn.md`。注意"被收录"与"项目自身的许可 / 维护状态"是两件独立的事。
- **同属"聚合多家 AI 服务"，一个管用、一个管买**：[PriceAI：AI 订阅卡网渠道比价工具](PriceAI-AI订阅卡网渠道比价工具.md)——那篇是**聚合多家服务的价格与渠道来源、做购买前比价**的雷达；本篇是**把多家模型接进一个客户端使用**。可对照"**用起来**"与"**买得起**"两种取向。
- **同属"桌面 AI 客户端"，重量级不同**：[pinvou-agent：面向工作、设计与代码的开源桌面 AI Agent 工作台](pinvou-agent-开源桌面AI-Agent.md)——那篇是**任务 / 产物优先的重量级 Agent 工作台**（MIT，Tauri + Rust，含 Work / Design / Code 三条动线）；本篇是**轻量对话客户端**（GPLv3 社区版）。可对照"**聊天优先**"与"**任务 / 产物优先**"。
- **同属"多模型"，一个是给人用的客户端、一个是给程序用的库**：[aimux：用 Rust 写的统一 LLM 接入层](aimux-用Rust写的统一LLM接入层.md)——本篇是**给人用的桌面聊天客户端**（面向终端用户），aimux 是**给你自己写的程序用的库 / SDK**（面向开发者）。

## 参考来源

- 项目仓库：https://github.com/chatboxai/chatbox
- 官网与下载：<https://chatboxai.app>
- 重新开源说明：仓库 Issue #2266（"Chatbox is going open-source Again!"）
- 元数据（Star / Fork / 时间 / 贡献者）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读 README、目录树与元数据；未安装、未运行、未登录任何模型。
