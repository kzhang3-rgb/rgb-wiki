# opencode：开源终端编码 Agent（原名 sst/opencode，现属 anomalyco）

> 记录这个体量惊人的编码 Agent：20 万+ Star、默认分支是 `dev`、且**仓库已从 `sst/opencode` 迁到 `anomalyco/opencode`**。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README、目录树与 GitHub API 元数据，并核验旧路径重定向，未安装、未运行）
- 适用范围：终端 / 命令行环境；提供安装脚本与 npm / brew / pacman 等分发方式

## 核心结论

opencode 自我描述只有一句：**"The open source coding agent."**（README 里写作 "The open source AI coding agent."）——面向终端的开源编码 Agent，官网 <https://opencode.ai>。

它值得记录的首先是**规模**：**224,607 Star / 30,272 Fork**，订阅者 862——这是本站收藏里体量最靠前的项目之一。

其次是**两件容易踩坑的事实**：

1. **仓库是从 `sst/opencode` 迁移/改名而来**。用 API 核验旧路径 `https://api.github.com/repos/sst/opencode`，返回的 `full_name` 是 `anomalyco/opencode`、`id` 975734319——**同一仓库 id**，即原 SST 的 opencode 现在挂在 `anomalyco` 组织下。引用旧链接时会被重定向，但组织名已变。
2. **默认分支是 `dev`，不是 `main`**。`git clone` 默认分支、脚本里写 `main` 的路径、以及 README 徽章的构建分支都指向 `dev`——**按 `main` 拉取可能失败或拿到非预期内容**。

第三条是版本线索：安装脚本用 `https://opencode.ai/v2/install`、Homebrew 用 `anomalyco/tap/opencode-v2`——**当前明确走的是 v2 这条线**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [anomalyco/opencode](https://github.com/anomalyco/opencode)（旧路径 `sst/opencode` 重定向至此） |
| 描述 | The open source coding agent. |
| 许可证 | MIT |
| 语言 | TypeScript |
| 关注度 | **224,607 Star、30,272 Fork、862 关注、4,026 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2025-04-30 创建**，最近推送 **2026-11-05**（极高活跃）；默认分支 **`dev`** |
| 发行版 | 最新 **v2.2.5**（2026-10-30，**27 个资产**），发版极密集（v2.2.1～v2.2.5 集中在 10-29～10-30） |
| 体积 | 约 631,155 KB（`size` 字段，约 616 MB）；`bun.lock` 单文件约 690 KB（用 Bun） |
| 主页 | <https://opencode.ai> |
| 贡献者 | 100 人（API 上限），主力 `thdxr`（2,998）、`opencode-agent[bot]`（2,695）、`rekram1-node`（2,640） |
| 目录 | monorepo：`packages/`、`sdks/`、`services/`、`infra/`、`specs/`、`script/`、`nix/`、`.opencode/`；根目录含 `AGENTS.md`（16.5 KB）、`CHANGELOG.md`（123 KB） |

## 用法要点

```bash
# YOLO 一键安装
curl -fsSL https://opencode.ai/v2/install | bash

# 包管理器
npm i -g @opencode/cli@latest        # 或 bun / pnpm / yarn
brew install anomalyco/tap/opencode-v2   # macOS / Linux（推荐，更新更快）
brew install opencode                    # 官方 formula（更新较慢）
sudo pacman -S opencode                  # Arch Linux
```

README 提供约 22 种语言版本（中 / 繁 / 日 / 韩 / 德 / 法 / 西 / 俄 / 阿拉伯语等），说明国际化投入很大。

## 使用前需要注意

- **4,026 个开放 Issue**：这是**开放 Issue + 开放 PR 的合计**（GitHub API 口径），体量大也反映项目热度与迭代速度；**不等于 4026 个未修 bug**。
- **默认分支是 `dev`**：这是最实际的坑——按 `main` 克隆/引用会出错或拿到旧内容，任何自动化脚本都要显式写 `dev`。
- **改名/迁移要跟**：旧文档、旧博客、老 CI 里写的 `sst/opencode` 现在会重定向到 `anomalyco/opencode`；**引用仓库归属时应以当前 `full_name` 为准**。
- **200 MB+ 级别的仓库**（约 616 MB）：建议浅克隆。
- **`opencode-agent[bot]` 是第二大提交者**（2,695 次）：说明项目本身大量使用 Agent 参与开发；结合根目录 `AGENTS.md`（16.5 KB），这也是一个"**真实项目里 AGENTS.md 怎么写**"的大样本（可对照 [agents.md](agents.md-给编码助手用的AGENTS文件格式.md)）。
- **安装脚本是 `curl | bash` 形式**：方便但等于执行远端脚本；对供应链敏感的环境建议改用包管理器或先读脚本。
- 我**未安装、未运行、未在真实项目里用它改代码**：以上均为 README 与元数据信息。

## 什么时候会再看

- 需要**一个开源、终端优先的编码 Agent**（而非 IDE 插件）时。
- 想参考"**编码 Agent 的 monorepo 如何组织**"（`packages/` + `sdks/` + `services/` + `infra/` + `specs/`）时。
- 想研究"**用 Agent 开发 Agent**"的真实样本（`opencode-agent[bot]` + 16.5 KB 的 `AGENTS.md`）时。
- 需要复盘"**仓库迁移 / 改名后如何避免引用失效**"（`sst/opencode` → `anomalyco/opencode`，默认分支 `dev`）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **被"上层环境"编排的 CLI 之一**：[paseo：跨桌面与移动编排多个编码 Agent 的开发环境](paseo-编排多个编码Agent的桌面与移动开发环境.md)——OpenCode 是它的**原生 provider**（其 provider 文档与仓库里的 `patches/@opencode-ai+sdk` 都有对应），并让 Agent 之间**跨 provider** 建 worktree、互相发消息。可对照"**Agent 本体**"与"**编排它的环境**"。
- **同为 AI CLI 生态，分工不同**：[pebrel：给 AI CLI 用的 GPU 加速终端与 SSH 工作区](pebrel-给AI-CLI用的GPU加速终端与SSH工作区.md)——那篇是**承载 AI CLI 的工作台**（区分图标、抓答案、看活动状态）；本篇是**终端编码 Agent 本体**。可对照"**CLI 本体**"与"**宿主终端**"。
- **同为"给编码助手"的工具与框架**：[openfox：本地模型优先的编码助手](openfox-本地模型优先的编码助手-co-l-openfox的fork.md)（本地模型优先、自带 Web 前端）、[deepseek-harness：DeepSeek 开源 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)（插件化 Agent 框架）、[oracle：给编码助手请第二意见的 CLI](oracle-给编码助手请第二意见的CLI.md)（把上下文交给第二个模型复核）。opencode 属于"**直接动手改代码的 Agent**"这一类，可与"框架"和"辅助工具"分层对照。
- **拆解主流编码 Agent 的中文教程**：[NamiAgent：从零手写 Agent 的中文可运行教程](NamiAgent-从零手写Agent的中文可运行教程.md)——其第三部分专门拆解 Claude Code / Codex 等，可对照"从零手写"与"成熟产品"的差别。
- **真实项目里的 `AGENTS.md` 样本**：[agents.md：给编码助手用的 AGENTS.md 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——本仓库根目录的 `AGENTS.md`（16.5 KB）可作大型样本，那篇讲格式约定本身。
- **跑编码 Agent ↔ 量它的花销**：[codeburn](codeburn-本地追踪AI编码Token用量与成本.md)——它把 OpenCode 列为原生被追踪对象（仓库内有 `docs/providers/opencode.md`），本地读取会话文件算 token 用量与花费；两篇是"跑 Agent"与"量它花了多少钱"的两端。

## 参考来源

- 项目仓库：https://github.com/anomalyco/opencode（旧路径 `sst/opencode` 重定向至此）
- 官网：<https://opencode.ai>；npm 包 `@opencode/cli`
- 旧路径重定向核验：`GET /repos/sst/opencode` → `full_name=anomalyco/opencode`、`id=975734319`（GitHub REST API，2026-11-05）
- 元数据（Star / Fork / 分支 / Release / 贡献者）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读 README、目录树与元数据并核验旧路径重定向；未安装、未运行、未实际使用。
