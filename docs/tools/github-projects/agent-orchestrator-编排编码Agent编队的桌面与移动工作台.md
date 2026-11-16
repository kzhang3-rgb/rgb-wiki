# agent-orchestrator：把"一队编码 Agent"从任务管到合并 PR 的桌面 + 移动工作台

> 一个**商业公司（Orchestrator.inc）出品的开源（Apache-2.0）编码 Agent 编排工作台**：给项目一个"主 Agent"，它把需求拆成若干份、**每份起一个 worker、各在自己的 worktree 与分支**，失败检查 / 评审意见 / 冲突自动回到拥有该分支的 worker，直到 PR 被批准。首屏口号是 **"Stop babysitting agents. Start merging real work."**。值得记的是三件事：① **它是"公司产品"而非个人玩具**——仓库是 Apache-2.0，但同时有 `pricing/`（各家模型价格目录）与 `contracts/cloud/openapi.yaml`（35 万字节的云 API 契约），且工作流里有 **`public-boundary.yml` 与 `optional-private-submodule.yml`**，说明**公开仓库与私有部分是分开的**；② **默认开遥测**（PostHog：安装 ID + GitHub 用户名，登录 AO Cloud 后还会把该账号的用户 ID 与邮箱设为 PostHog 人物属性），可在设置里关掉；③ **版本号已到 v0.15.x，另有 343 条 Release（多为 nightly 预发布）**——迭代极快。**未安装、未运行、未给任何仓库授权、未配对手机、未审计源码**。

- 状态：草稿
- 最近核验：2026-11-16（阅读 `main` 分支 README 全文（211 行）、`LICENSE`、文件清单（6,878 条目）与元数据；Release 列表、`commits/main.atom`、ungh 镜像、GitHub 页面内嵌 JSON 与 shields 徽章。**未 clone、未安装、未运行、未接任何编码 Agent、未授权任何仓库、未配对手机、未审计源码**）
- 适用范围：**桌面 App（macOS / Windows / Linux）** + **移动 App（iPhone / Android）** + **Cloud Agents（托管工作区）**；前置条件是**本机已装并登录至少一个编码 Agent CLI**（如 Claude Code / Codex 等）

## 核心结论

**一句话定位（描述）**：`Plan, run and supervise fleets of agents from task to merged PR. Desktop (macOS, Windows, Linux) + iPhone & Android apps. Supports Claude Code, Codex & 35+ coding agents.`

**① 它要解决的是"一个人盯不过来的多个 Agent"。** README 的自述逻辑是：一个编码 Agent 能处理一个任务，但跑好几个时就要"拆分工作、避免分支互相撞车、把每次改动跟到 CI / 评审 / 合并"——它的定位就是**把这套麻烦收进一个本地桌面工作区**：每个任务配自己的 Agent、分支与 worktree，**代码留在你本机**。

**② 三层形态：本地桌面 → 手机遥控 → 云工作区。**
- **桌面（主形态）**：告诉项目主 Agent 要做什么，它自己规划、起 worker，只在"需要你"时回来；看板把会话按 Agent 状态、PR、CI、评审自动分到 `Working` / `Needs you` / `In review` / `Ready to merge` 车道。
- **移动 App**：iPhone / Android 上收推送通知（Agent 提问或要审批时）、在聊天里回复 / 批准 / 开终端、起新 worker 并选 Agent 与模型、看 PR 与评审结果；扫码配对，**在家用 Wi-Fi 与在外都可用**。
- **Cloud Agents**：把 worker 跑在**托管工作区**里（"Clone to the cloud" 选一个 GitHub 仓库开工），**合上笔记本任务也继续**，而你本机不落地代码——这一层是**把代码与 Agent 都放到厂商侧**。

**③ 支持的 Agent 是一张很长的名单（README 自述"35+ coding agents"）。** 首屏列出的包括 Claude Code、Codex、Cursor、opencode、Aider、GitHub Copilot、Grok、Kimi、Pi、Amp、Auggie、Droid、Crush、Cline、Goose、Qwen、Continue、Devin、Kiro、Kilo Code、Vibe、Muse、Agy、Autohand、Kimchi、Prime Agent、OMP、fx、Unreal Agent、MiMo Code、Gemini CLI、DeepSeek、OpenCode 2、Codewhale、Command Code、OpenHands 等；README 说明**worker / chat / reviewer 三种角色的支持程度按 Agent 而异**，以官方文档的矩阵为准。

**④ 它自己会"回环修 CI"。** README 明确：**失败的检查、评审意见、合并冲突都会回到拥有该分支的 worker**，直到 PR 被批准；另有**评审 Agent 可以先于人把每个 PR 读一遍**。这既是它最省人的地方，也是**自主性风险最集中的地方**（见下）。

**个人判断（未验证）**：它切的是"**已经在用多个编码 Agent、但被分支与 CI 拖住**"的人。但有两点与个人开源项目不同：**一是它是商业公司的产品线**（官网、文档站、X、Discord、App Store / Play Store 应用、Cloud 付费面），仓库虽 Apache-2.0，却明显是**开放核心（open core）**的公开面；**二是它默认收集遥测**。用它等于**同时接受"把仓库读写权限交给一队会自己推送的 Agent"与"把一部分使用数据交给厂商"**这两件事。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [OrchestratorInc/agent-orchestrator](https://github.com/OrchestratorInc/agent-orchestrator) |
| 描述 | "Plan, run and supervise fleets of agents from task to merged PR. Desktop (macOS, Windows, Linux) + iPhone & Android apps. Supports Claude Code, Codex & 35+ coding agents." |
| 归属 | 组织账号 **OrchestratorInc**（品牌 **Orchestrator.inc**，官网 <https://orchestrator.inc>、文档 <https://docs.orchestrator.inc>）；X `@useOrchestrator`、Discord |
| 许可证 | **Apache-2.0**（`LICENSE` 11,339 字节，为完整 Apache 2.0 原文；shields 与 GitHub 均识别为 `Apache-2.0`）；README 末尾明写 "available under the Apache License 2.0" |
| 主语言 | **TypeScript 49.7%**（shields，2026-11-16）；另有 Go 后端（根目录 `go.work`） |
| 技术栈 | 单仓多包：`backend/`（2,953 条）、`frontend/`（2,171 条，TypeScript，含 Electron 桌面端 `frontend/src/renderer/…`）、`packages/`（1,430 条，npm workspaces）、`docs/`（153）、`pricing/`（24）、`contracts/`（2）、`.agents/`（5）、`.claude/`（3）；根目录含 `AGENTS.md`、`CLAUDE.md`、`CONTEXT.md`、`DESIGN.md`（47,536 字节）、`CONTRIBUTING.md`、`flake.nix`、`go.work`、`desktop-version-floor.json` |
| 关键工程物 | `contracts/cloud/openapi.yaml`（**351,109 字节**，云侧 OpenAPI 契约）、`pricing/catalog/v1/manifest.json` + `pricing/catalog/v1/providers/{anthropic,openai,zai}/*.json`（**各家模型的价目目录**）；`.github/workflows/` 含 **`public-boundary.yml`**、**`optional-private-submodule.yml`**、`harness-proof-build.yml`、`harness-release-feeds.yml`、`pricing-catalog-validate.yml`、`release-latest-guard.yml`、`version-floor-guard.yml`、`mac-update-e2e.yml`、`frontend.yml`、`go.yml`、`gitleaks.yml` 等 |
| 关注度（2026-11-16） | **14,726 Star / 42 关注 / 2,019 Fork**（ungh 镜像）；**页面内嵌 14,751 / 42 / 2,024**（略高，属时间差）；shields 显示 **15k Star、2k Fork**；**开放 Issue 458 个、贡献者 138 位**（shields） |
| 创建 / 最近推送 | **2026-02-13 创建**；`pushed_at` **2026-11-15**，`commits/main.atom` 最新提交同为 **2026-11-15T20:53:43Z**（`feat(artifacts): …`，作者 `neversettle17-101`）→ **判定为真实活跃** |
| 默认分支 | `main`；无 GitHub Pages（页面 `pagesUrl` 为空） |
| Release / 文件数 | **343 条 Release**（页面 `releaseCount`）；最新为 **`v0.15.3-nightly.202611151925`（prerelease，2026-11-15）**，即**大量 nightly 预发布 + 正式版并存**；`main` 共 **6,878 个文件** |
| 分发 | 桌面端自 <https://orchestrator.inc/download/> 或 GitHub Releases（`Orchestrator-darwin-arm64.dmg` / `-darwin-x64.dmg` / `agent-orchestrator-win32-x64.exe` / `.AppImage` / `.deb` / `.rpm`）；**应用会自我更新**；手机端走 App Store（`ao-mobile`，id 6792552173）与 Google Play（`aoagents.dev`） |

> 口径说明：数据来自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章 + `commits/main.atom`**（核验日未直接调用 api.github.com）。**shields 的 Issue / 贡献者数**为单独来源；Star / Fork 会随时间变化。

## 使用前需要注意

- **⚠️ 这是"商业公司的开放核心"，公开仓库不等于完整产品**：工作流里同时存在 **`public-boundary.yml`（公开边界检查）**与 **`optional-private-submodule.yml`（可选私有子模块）**，另有 `contracts/cloud/openapi.yaml` 与 `pricing/` 价目目录——**说明产品有明确的"公开 / 私有"切分**。读仓库能看懂架构与前端，但**不要假设你能从这份 Apache-2.0 代码里自建出完整的 Orchestrator.inc**；商用 / 自托管前先读官方文档与边界说明。
- **⚠️ 默认开启遥测**：README "Product telemetry" 一节自述——收集**有限的使用与可靠性数据**，**不含你的代码、提示词、Agent 对话或凭证**；内容包括**安装 ID 与你的 GitHub 用户名**，若登录 AO Cloud 还会把**该账号的用户 ID 与邮箱**设为 PostHog 人物属性（自述"**不读取 git config**"）。可在 **Settings → General → Privacy → Share usage analytics** 关闭（覆盖桌面、daemon 与已配对手机）。**默认开、需手动关**。
- **⚠️ 自主性最集中的地方是它会改你的仓库**：worker 各自在自己分支上干活，**失败的 CI、评审意见与冲突会自动回到该 worker 继续推**，直到 PR 批准；还有**评审 Agent 先读 PR**。请把它当成"**一支会自动提交、自动推送、自动迭代的机器人小队**"来划权限边界——仓库访问范围、可推送分支、审批门都要事先想清楚。
- **⚠️ "代码留在我本机"有例外**：README 明说桌面形态下代码留在你机器；但 **Cloud Agents 会把代码、worktree 与 Agent 放进托管工作区**（"Nothing on your machine"），这一层代码在厂商侧。用哪种形态，先分清。
- **⚠️ 迭代极快、预发布很多**：**343 条 Release**，最新是 **nightly 预发布**，且工作流里有 **`version-floor-guard.yml` / `release-latest-guard.yml` / `desktop-version-floor.json`**（版本底限守卫）。**追求稳定请只跟正式版，不要默认吃 nightly**。
- **⚠️ 458 个开放 Issue + 138 位贡献者**：活跃度高，但"问题池"也不小，说明**有的功能仍在飞行中**；README 提到 `docs/STATUS.md` 专门说明"`main` 上现在发什么、什么还在做"。
- **⚠️ 仓库曾经改过归属**：Release 说明里的 PR 链接指向 **`Untrivial-ai/agent-orchestrator`**（现名为 `OrchestratorInc/agent-orchestrator`），**说明组织名变过、历史链接可能不再一致**——引用旧链接时注意。
- **⚠️ 需要自带 Agent 与订阅**：它**不提供模型**，要你本机已装并登录编码 Agent CLI；`pricing/` 目录只是**各家模型的价目目录**（用于展示/估算），不代表它代付。
- **⚠️ 手机端是商店分发的闭源包装**：iOS / Android App 走 App Store / Play Store，**不是本仓库构建的开源产物**。
- **本笔记只读了 README、`LICENSE`、文件清单与元数据**：**未 clone、未安装、未运行、未接任何编码 Agent、未授权任何仓库、未配对手机、未审计源码**；功能、机制与遥测描述**均引自 README 与仓库文件**，未独立验证。

## 什么时候会再看

- 想**同时跑一支编码 Agent 小队**（每个任务一个 worker + worktree + 分支），并希望**失败的 CI / 评审意见自动回到对应 worker** 时。
- 想参考"**本地桌面工作区 + 手机遥控 + 云端工作区**"这套三层形态的交互与看板设计（`Working` / `Needs you` / `In review` / `Ready to merge`）时。
- 想研究**"公开边界（public boundary）+ 可选私有子模块"**这种开放核心仓库如何工程化地切分公开与私有面时（`public-boundary.yml` / `optional-private-submodule.yml`）。
- 想对照不同"多 Agent 编排"路线的取向时：见 [paseo](paseo-编排多个编码Agent的桌面与移动开发环境.md)（自托管 + 无遥测）与 [dontbemichael](dontbemichael-用现有订阅跑一屋子Agent的本地多Agent框架.md)（面向业务所有者）。
- 想看清"**把仓库读写权交给 Agent 小队**"这件事的风险面，作为划权限边界的参考时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **最直接的同类对照（同为"桌面 + 移动编排多个编码 Agent"）**：[paseo：跨桌面与移动编排多个编码 Agent 的开发环境](paseo-编排多个编码Agent的桌面与移动开发环境.md)——两者都是**桌面 App + 原生手机 App + 多 Agent / 多 worktree**，但口径相反：paseo **自托管、无遥测、不强制登录**（Apache-2.0，个人维护），本篇是**商业公司的开放核心**（Apache-2.0 公开面 + 私有子模块 + 默认遥测 + Cloud 托管面）。**两篇并列看"同一形态下自托管与商业化两条路"最清楚。**
- **同为"编排多个 Agent"，面向不同**：[dontbemichael：用现有订阅跑一屋子 Agent 的本地多 Agent 框架](dontbemichael-用现有订阅跑一屋子Agent的本地多Agent框架.md)——那篇是**面向业务所有者的"AI 办公室"**（角色 + 邮箱 + 审批）；本篇是**面向开发者的编码 Agent 工作台**（worktree + PR + CI 回环）。
- **"跑 Agent 的环境"与"Agent 用的宿主终端"**：[pebrel：给 AI CLI 用的 GPU 加速终端与 SSH 工作区](pebrel-给AI-CLI用的GPU加速终端与SSH工作区.md)——那篇把多个 CLI 会话收进原生终端 + SSH / SFTP；本篇把它们收进**带看板与 PR 流的编排工作区**。
- **"并行子代理"的两种用法**：[translate-book：用并行子代理翻译整本书](translate-book-用并行子代理翻译整本书.md)——那篇把并行子代理用在**一个具体批处理任务**（翻译）上；本篇把并行 Agent 做成**常驻的开发工作流（任务 → PR → 合并）**。
- **"给 Agent 请第二意见"的同类功能**：[oracle：给编码助手请第二意见的 CLI](oracle-给编码助手请第二意见的CLI.md)——本篇的"**评审 Agent 先读每个 PR**"与 paseo 的 `/paseo-advisor` 属同一类做法（让另一个 Agent 当审阅者），可对照"**嵌进工作流**"与"**单独一个 CLI**"两种形态。

## 参考来源

- 仓库主页与 README（`main` 分支，211 行；核验日期 2026-11-16）：https://github.com/OrchestratorInc/agent-orchestrator
- 许可证：`LICENSE`（Apache-2.0，11,339 字节）与 README "License" 一节
- 官网 / 文档 / 下载：<https://orchestrator.inc> · <https://docs.orchestrator.inc> · <https://orchestrator.inc/download/>
- 仓库内工程文档（README 指向）：`docs/documentation-map.md`、`docs/architecture.md`、`docs/backend-code-structure.md`、`docs/cli/README.md`、`docs/development.md`、`docs/STATUS.md`、`docs/telemetry.md`
- 遥测说明：README "Product telemetry" 一节（PostHog；安装 ID + GitHub 用户名；AO Cloud 用户 ID 与邮箱）；关闭入口 Settings → General → Privacy
- 手机端：App Store `ao-mobile`（id 6792552173）· Google Play（`aoagents.dev`）
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / 文件清单）：ungh.cc 镜像、GitHub 页面内嵌 JSON 与 `commits/main.atom`，2026-11-16
- 开放 Issue 数与贡献者数、许可证识别、主语言占比：shields.io 徽章，2026-11-16
- Release 列表（343 条，含 nightly）：ungh.cc releases API 与页面 `releaseCount`，2026-11-16

以上信息核验于 2026-11-16。Star / Fork、Issue 数与仓库内容随时可能变化；**未 clone、未安装、未运行、未接任何编码 Agent、未授权任何仓库、未配对手机、未审计源码**。功能、机制与遥测描述均引自 README 与仓库文件，**未独立验证**；README 中的社区与商业入口仅作事实记录，**不构成推荐**。
