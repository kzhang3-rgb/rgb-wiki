# openfox：本地模型优先的编码助手（`co-l/openfox` 的 fork）

> 记录一个"**Local-LLM-first**"的自主编码助手：**默认服务本地模型（自动探测 vLLM / sglang / ollama / llamacpp）、自带 Web 前端、走 MCP、用 SQLite 存事件**。**先记清最关键的一件事：`quentin452/openfox` 是 `co-l/openfox` 的 fork**——上游 370 Star，本仓库仅 1 Star、0 Issue，且**许可证字段与语言字段都为空、`main` 分支停在 2026-08-07（落后上游）**。**另有一处口径冲突：README 写 "License: MIT"，但仓库根目录没有 `LICENSE` 文件（上游也没有）。**

- 状态：草稿
- 最近核验：2026-11-04（阅读 `README.md` 全文、`AGENTS.md` 全文、`package.json` 全文、目录树、Release 与元数据，并核验 fork 关系；**未安装、未运行、未连接任何本地模型、未审计源码**）
- 适用范围：**Node.js ≥ 24.0.0**（`package.json` 的 `engines.node`）；`npm i -g openfox` 全局安装；默认端口 10369（开发 10469）；需要一个本地 OpenAI 兼容服务（默认 `http://localhost:8000/v1`）

## 核心结论

**① 这是派生仓库，不是原始项目。** 查 `quentin452/openfox` 的 `parent` 得到 **`co-l/openfox`**（parent id `1178634902`）；`package.json` 的 `repository.url` 也写着 **`git@github.com:co-l/openfox.git`**——即**它的代码来源是上游 `co-l/openfox`**。对照一下：

| | `quentin452/openfox`（本仓库） | `co-l/openfox`（上游） |
| --- | --- | --- |
| Star | **1** | **370** |
| Fork / 开放 Issue | 0 Fork / 0 Issue | — |
| license 字段 | **空** | **空** |
| language 字段 | **空** | TypeScript |
| 最近推送 | 2026-09-02 | — |

**结论：要跟踪这个项目，应以上游 `co-l/openfox` 为准。**（个人判断：本 fork 只适合当"某一时刻的快照"，不适合当更新源。）

**② 它的定位很明确：不是"接云 API 的编码助手"，而是"服务本地模型的编码助手"。** README 首页写着 "**Local-LLM-first agentic coding assistant**"、"Autonomous coding agent for local LLMs with contract-driven execution"。首次启动会**自动探测 vLLM / sglang / ollama / llamacpp**；`OPENFOX_LLM_URL` 默认指向 `http://localhost:8000/v1`。

**③ 架构原则写在给 AI 的 `AGENTS.md` 里，值得记下来。** 仓库根目录有一份 **`AGENTS.md`（约 10 KB）**，直接写给编码 Agent 看，里面列了四条设计原则：

- **Dumb Client Smart Server**（前端只做展示，逻辑在服务端）；
- **Streaming / Fetch Parity**（流式与一次性返回的行为要一致）；
- **Event Sourcing**（状态以事件形式存储）；
- **`.openfox/` 契约**（以 `.openfox/` 目录承载约定）。

另外它给了构建命令、端口（dev 10469 / prod 10369、密码 `password`）与 TDD 工作流，并提醒 **precommit hook 可能超过 40 秒，提交要用更大的超时**。

**④ 一处口径冲突，用前请注意。** README 写 **"License: MIT"**、`package.json` 也写 `"license": "MIT"`，但**仓库根目录没有 `LICENSE` 文件**（上游 `co-l/openfox` 的 `main` 同样取不到 `LICENSE`），**GitHub API 的 `license` 字段也为空**。因此"MIT"目前只是声明、缺少许可原文；正式使用或再分发前建议向上游确认。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [quentin452/openfox](https://github.com/quentin452/openfox)（**`co-l/openfox` 的 fork**） |
| 上游 | [co-l/openfox](https://github.com/co-l/openfox)（parent id `1178634902`，**370 Star**） |
| 描述 | "Local-LLM-first agentic coding assistant, with everything you need out of the box."（与上游描述一致） |
| 许可证 | **README 与 `package.json` 均声明 MIT，但仓库无 `LICENSE` 文件、API `license` 字段为空**（口径冲突，见上） |
| 主语言 | 代码为 TypeScript（**本 fork 的 `language` 字段为空，上游为 TypeScript**） |
| 关注度 | **1 Star、0 Fork、0 关注、0 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-08-08 创建**；`main` 分支最新提交 **2026-08-07**（tag `2.0.113`）；`pushed_at` 2026-09-02；**共 245 个 tag，最新 `v2.0.113`**；Release 最新 `v2.0.113`（2026-08-07） |
| 体积 | API `size` 12,203 KB（约 12 MB）；目录树 1,071 项 / **977 个 blob / 约 9.1 MB**；**`package-lock.json` 单文件约 604 KB**、`CHANGELOG.md` 约 80 KB |
| 版本 | `package.json` `version: 2.0.113`；`type: module`；`engines.node: ">=24.0.0"` |
| 目录结构 | 根即服务端：`src/`、`web/`、`e2e/`、`e2e-playwright/`、`docs/`、`scripts/`、`.openfox/`、`AGENTS.md`、`package.json`、`plugins-registry.json` |
| 话题标签 | **无**（fork 未设 topics） |

> 关于关注度：**上游 370 Star，本仓库仅 1 Star、0 Issue**，且许可证与语言字段皆空。这是一个"**为某个目的分叉出来的副本**"，不建议作为主要跟踪对象。

## 它能做什么

**快速开始（README）**

```bash
npm i -g openfox
openfox        # 首次运行自动探测本地模型后端（vLLM / sglang / ollama / llamacpp）
```

**CLI**

| 命令 | 作用 |
| --- | --- |
| `openfox [--port 8080] [--no-browser]` | 启动服务并打开 Web 界面 |
| `openfox config` | 配置 |
| `openfox provider add / list / use / remove` | 管理模型提供方 |

**2.0 的新特性（README 自述）**：Multi-Turn Agent Engine、Provider Dialog、Auto-Retry Patterns、统一图片处理、会话元数据、Workflow 子分组、`edit_file` 并行安全、Firefox 支持。

**配置与环境变量（README 表格节选）**

| 变量 | 含义 | 默认 |
| --- | --- | --- |
| `OPENFOX_LLM_URL` | 本地模型端点 | `http://localhost:8000/v1` |
| `OPENFOX_BACKEND` | 后端类型 | — |
| `OPENFOX_DB_PATH` | SQLite 数据库路径 | — |

默认端口 **10369**；插件目录按平台放置（macOS 为 `~/Library/Application Support/openfox/plugins`）。

**第三方认证插件（README 提到）**：`openfox-chatgpt`、`openfox-github-copilot`、`openfox-google-antigravity`——即除本地模型外，也支持通过插件接入云端账号。

**技术栈（来自 `AGENTS.md`）**

- 后端：TypeScript / Node 24+ / Hono 或 Express / WebSocket / `better-sqlite3`；
- 前端：React 19 / TailwindCSS / Zustand / Vite；
- 依赖含 `@modelcontextprotocol/sdk`、`node-pty`、`pyright`、`shiki`、`zod`（`package.json`）。

## 使用前需要注意

- **先确认你要的是哪个仓库。** 本条目记录的是 **fork**（1 Star、字段为空、落后）。**要跟项目请以上游 `co-l/openfox` 为准**；引用 commit、版本、Issue 时应指向上游。
- **许可只是"声明"，缺 `LICENSE` 原文。** README 与 `package.json` 写 MIT，但根目录没有 `LICENSE` 文件、API 字段为空。**在正式使用或再分发前建议向上游确认。**（这与之前记录的 MarkDew 双许可、videodl 非商业许可一样，属于"必须看文件、而不是看徽章或字段"的情形。）
- **会真的跑命令、改文件。** 它是"自主编码 Agent"，会执行构建 / 测试并编辑工作区文件；`AGENTS.md` 里还写了默认密码 `password`（本地开发用）——**不要把它暴露到公网**。
- **Node 版本门槛高。** 要求 **Node.js ≥ 24.0.0**，`npm i -g openfox` 前先确认本机版本。
- **安装脚本会执行。** `package.json` 里 `allowScripts.better-sqlite3: true`（允许其安装脚本执行），安装时留意。
- **本笔记的核验边界**：只读了 README、`AGENTS.md`、`package.json` 全文、目录树、Release 与元数据，并核验了 fork 关系；**未安装、未运行、未连接任何模型、未审计源码**。

## 什么时候会再看

- 想给**本地模型**（vLLM / ollama / sglang / llamacpp）配一个能自己改代码、跑命令的编码助手时；
- 想参考"**Dumb Client Smart Server / Streaming-Fetch Parity / Event Sourcing / 目录契约**"这套写给 Agent 的架构原则时；
- 想抄"**给 AI 看的 `AGENTS.md` 该写什么**"（构建命令、端口与密码、TDD 流程、hook 超时提醒）时；
- 需要复盘"**fork 与上游的追踪与合规**"（该跟上游、许可要看文件而非字段）时。

## 相关笔记

- **同为面向编码助手的工具**：[oracle：给编码助手请第二意见的 CLI](oracle-给编码助手请第二意见的CLI.md)——那边是"请第二意见"的旁路工具，本篇是"能自己动手"的编码 Agent 本体。
- **`AGENTS.md` 在真实项目里的用法样本**：[agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——openfox 根目录就放了一份 `AGENTS.md`，可直接对照"这类文件在工程里怎么写"。
- **同为"Agent 骨架 / 本体"的两端**：[NamiAgent：从零手写 Agent 的中文可运行教程](NamiAgent-从零手写Agent的中文可运行教程.md)——一个是教学用手写骨架，一个是已产品化的编码助手；可对照"骨架"与"成品"。
- **同为"能自己动手改代码"的编码 Agent**：[opencode：开源终端编码 Agent](opencode-开源终端编码Agent.md)——那是终端优先、体量最大的开源编码 Agent；本篇强调**本地模型优先**、自带 Web 前端，可对照"接主流大模型"与"优先本地模型"两种取向。
- **同类"本地优先 AI 应用"，侧重不同**：[cherry-studio：把模型、Agent 与知识库收进一个桌面工作台](cherry-studio-多模型Agent与知识库的桌面工作台.md)——那是以对话 / 知识库 / Agent 为中心的桌面工作台，与本篇"本地编码助手"是同一大类下的不同侧重。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 本 fork：https://github.com/quentin452/openfox
- 上游仓库（应以它为准）：https://github.com/co-l/openfox
- 仓库内说明：`README.md`（157 行）、`AGENTS.md`（235 行，面向 AI 的工程指南）、`package.json`
- npm 包：`openfox`（`npm i -g openfox`）
- 元数据（Star / Fork / 时间 / 体积 / parent）：GitHub REST API；tag 数与 Release 经 jsDelivr 与 `releases.atom` 核验，2026-11-04

以上信息核验于 2026-11-04。仅阅读 README、`AGENTS.md`、`package.json`、目录树、Release 与元数据，并核验了 fork 关系；**未安装、未运行、未连接任何模型，功能与效果未验证**。
