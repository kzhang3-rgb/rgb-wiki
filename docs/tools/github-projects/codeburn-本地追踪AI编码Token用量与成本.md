# CodeBurn：本地读取会话文件、算清 AI 编码花销的追踪工具

> 它把"你的 AI 编码账单只有一个总数"这件事拆开：**不装代理、不上传代码、不要账号**，只读你本机各工具已经写下的会话文件，按模型 / 项目 / 任务 / 分支给出花费，并告诉你哪些钱花在了浪费上。适合想复盘"钱到底花在哪、哪里白花了"的人；也正是它赖以取数的"会话文件只在本地、30 天后可能被删"这一点，决定了它数字的边界。

- 状态：草稿
- 最近核验：2026-11-12（阅读 README 全文、`LICENSE`、仓库文件树与页面元数据；**未 clone、未 `npx`/`npm install`、未运行任何命令、未接任何被追踪工具、未核对其计算准确度**）
- 适用范围：**macOS / Windows / Linux**；CLI 需 **Node.js 22.15+**（`.nvmrc` 为 `22.15.0`，README 徽章标 `node >=22`）；数据来自本机已安装工具写下的会话文件，**不代理你的流量**。桌面端另有 macOS、Windows、Linux 发行包与 GNOME 扩展。

## 核心结论

**一句话定位（README 自述）**："See where your AI spend goes."——GitHub 描述为：**免费的本地工具，跨 37 个工具与 Agent 追踪 AI 编码的 token 用量与成本**（Claude Code、Cursor、Codex、Gemini 等），按模型、项目、任务拆解，入口是 `npx codeburn`。

**它凭什么能拆开账单（事实）**：Claude Code、Codex、Cursor 等在每次使用时都会**各自写一份会话文件**，里面记着每次调用的 token 与模型；CodeBurn 读这些文件，套上**公开 API 价格**（价格来自 LiteLLM，缓存 24 小时），算出花费。**它不在你和工具之间，读的是磁盘上已有的文件，所以不上传 prompt、代码与项目名。**

**三个最值得记的点：**

1. **"只读本地文件、不要账号、不做代理"是它的核心取舍**：功能上换来"零配置识别已装工具"，隐私上换来"代码不出本机"，**代价是数字质量完全受上游会话文件质量约束**（见"使用前需要注意"）。
2. **它明确区分"测量"与"估算"**：多数工具的 token 是**实测**（读工具自己写的 input/output/cache 计数），价格是**公开定价套算**；少数工具（Cursor、Kiro、部分 Copilot）**只有内容长度估算**，并在表格里标 `estimated`。JSON 导出每条记录带 `costBasis`（`recorded` / `calculated` / `estimated` / `included` / `unpriced`）——**这个分级设计比"给你一个准数"更诚实，也最值得借鉴**。
3. **它不止"看账单"，还往前走一步到"省钱"与"防超支"**：`optimize` 找浪费并**可代改配置且可回滚**（`codeburn act undo --last`）；`guard install` 给 Claude Code 加**预算钩子**（过 $5 告警、$15 停止）；`quota` 用工具已存的凭据**向各家查询订阅余量**。

**个人判断（未验证）**：它当前热度很高（见项目信息），且"读本地会话文件"这条取材路径对个人开发者很顺；**但它的价值高度绑定在对各家工具会话格式的持续跟进上**，README 自己承认"42 个集成一直在变"，这是它长期可用性的主要变量。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [getagentseal/codeburn](https://github.com/getagentseal/codeburn) |
| 描述 | "Free, local tool to track AI coding token usage and cost across **37 tools and agents** (Claude Code, Cursor, Codex, Gemini and more), by model, project, and task. npx codeburn"（**注意：描述写 37，README 标题写 "Works with 42 tools"，口径不一致，见下**） |
| 归属 | 账号 `getagentseal`（**组织**，页面 `isOrg: true`）；`isFork: false`、`isArchived: false`、`isPrivate: false` |
| 许可证 | **MIT**（页面 `spdxId: MIT`；`LICENSE` 首行 `MIT License`，署名 `Copyright (c) 2026 AgentSeal`） |
| 主语言 | **TypeScript 78.1%**（shields.io，2026-11-12） |
| 技术栈 | **CLI（`src/`，TypeScript，通过 `tsup` 打包，发布到 npm）+ 桌面应用（`app/`，Electron 主进程 + React renderer）+ macOS 菜单栏（`mac/`）+ Windows 托盘（`windows/`）+ VS Code 扩展（`vscode/`）+ GNOME 扩展（`gnome/`）+ Web 面板（`dash/`）**；另含 `.semgrep` 规则、`.gitleaks.toml`、`.oss-scanner`（含 `threat_model.md`） |
| 关注度 | **11,774 Star / 908 Fork / 33 Watcher / 23 开放 Issue / 8 开放 PR**（**页面值**，2026-11-12；Star/Fork/Watcher 取自 GitHub 页面内嵌数据，Issue/PR 数取自 shields.io。**页面值与 ungh.cc 镜像不一致：镜像报 11,766 Star / 907 Fork，此处记录页面值**） |
| 维护状态 | **2026-04-13 创建**；页面 `updatedAt` **2026-11-11**、ungh 报 `pushedAt` **2026-11-09**（距核验日约 1–3 天，**活跃**）；已累计 **84 Release / 115 tags** |
| 默认分支 | `main` |
| 版本 | **84 Release / 115 tags**；发布 npm 包（页面 `packages: true`），并标记 `usedBy: true`（有下游依赖方） |
| 话题标签 | `ai-coding` · `claude-code` · `cli` · `codex` · `cost-tracking` · `cursor-ide` · `developer-tools` · `menubar` · `observability` · `terminal-ui` · `token-usage` · `web-dashboard` |
| 体积 | `main` 文件树约 **1,686 个文件**（ungh.cc files API） |
| 仓库结构 | 顶层按目录分层：`tests`（472）、`app`（325）、`mac`（286）、`src`（215）、`windows`（115）、`docs`（82）、`assets`（51）、`vscode`（40）、`.github`（22）、`dash`（21）、`scripts`（13）、`gnome`（10）、`plugins`（4）、`perf`（3）、`.oss-scanner`（2）；**`docs/providers/` 为每个被追踪工具各留一页**（约 40+ 篇，含 `openclaw.md`、`opencode.md`、`claude.md`、`codex.md` 等），`docs/` 另有 `architecture.md`、`cli.md`、`how-it-works.md`、`optimize.md`、`mcp.md`、`guard.md` 等 |
| 官网 | <https://codeburn.app/> |

## 它解决什么问题（README 自述）

"你的账单只给你一个月度总数，它不按项目、按模型、按任务拆开，也不告诉你哪一部分是浪费掉的。"CodeBurn 的做法是：**读取各工具自己写的会话文件，重建这份拆解**。入口三种形态：终端（`codeburn`）、桌面应用（macOS / Windows / Linux）、浏览器（`codeburn web`），**四类界面（桌面、菜单栏、Capacity Dock、Claude 卡片）读的是同一批本地文件，所以数字一致，只差各自刷新时间**。

## 数字从哪来：测量、估算与"没写下来的部分"

**这一节是它可信度的核心（README 自述）：**

- **token 多为实测**：Claude Code、Codex、Gemini、Zed、OpenCode 等把每次调用的真实 input/output/cache 计数写进会话文件，CodeBurn 直接读。
- **价格是套算**：对实测 token 套**公开 API 定价**，所以"美元"是**你在按 API 价付费本应付的钱**，**不是账单**。订阅（如 Claude Max）下它标为 **API-equivalent**。
- **少数工具是估算**：Cursor、Kiro 与部分 Copilot 会话**没有计数**，只能按内容长度估，表格里标 **estimated**。
- **它可能比官方计数器少**：README 指出 Claude Code **有些调用不写进 transcript**（部分 advisor 调用、只保留部分用量的子代理回复），这些 CodeBurn 看不到。
- **每条 JSON 记录带来源标签**：`costBasis` ∈ `recorded`（工具自报）/ `calculated`（实测 token × 公开价）/ `estimated`（估的 token）/ `included`（订阅已含，$0）/ `unpriced`（无价格，$0）。**`codeburn audit` 会逐 provider / model 说明每个数字的来源。**
- **历史与会话分离**：CodeBurn 在 `~/.cache/codeburn/` 保留**按天的持久历史**（README 称保留十年），所以**即使上游会话文件被删**（Claude Code 30 天清理），`codeburn report` / `status` 仍能回答当天；但 **per-session 明细会随 transcript 一起消失**，`sessions` / `models` / `spend` / `compare-periods` 对旧区间会少于 `report`。

## 支持的工具（"37"还是"42"？）

**GitHub 描述写 37 个工具与 Agent，README 标题写 "Works with 42 tools"，这是同一仓库内的口径不一致**（我未核实哪个为准）。README 的 "All 42 tools" 明细列出约 41 个具名条目 + Vercel AI Gateway，覆盖：**Claude Code、Cline、CodeWhale、Codex、Cursor、cursor-agent、Devin、Forge、Gemini CLI、Mistral Vibe、GitHub Copilot、IBM Bob、Kiro、OpenCode、OpenClaw、Pi、OMP、Droid、KiloCode、Qwen、Kimi、Goose、Antigravity、Crush、Warp、Mux、Zerostack、Grok Build、Grok Bot、ZCode、Zed、Hermes Agent、LingTai TUI、Amp、Codebuff、Command Code、DeepSeek Harness、Open Design、OpenClaude、Quick Desktop、Kimi Code** 等；另可读 **Vercel AI Gateway** 报告 API（因是网关而非工具，**单独一行，默认不计入总数**）。README 说"新增一个工具就是加一个文件，见 `src/providers/codex.ts`"。

## 主要命令与入口（README 自述）

| 命令 / 入口 | 作用 |
| --- | --- |
| `npx codeburn` | 不安装直接跑；终端仪表盘（方向键切时段、`p` 切工具、`q` 退出）；配 `npm install -g codeburn` 或 `brew install codeburn` 常驻 |
| `codeburn web` | 浏览器面板；也可在局域网内跨设备合并用量（`codeburn share --pair` / `devices add` / `devices`） |
| `codeburn optimize` | 读最近 30 天会话与 `~/.claude/` 配置，找"花了 token 却没产出"的点，给 A–F 评分与预估可省；`--apply` 可代改并可 `codeburn act undo --last` 回滚，改前备份到 `~/.config/codeburn/actions/` |
| `codeburn quota` | 用各工具**已存在本机的凭据**查询订阅余量（如 Claude 的 5 小时 / 每周窗口）。**不预测何时耗尽**，只告诉你窗口用了多少、何时重置 |
| `codeburn plan set claude-max` | 声明订阅档位（也支持 `claude-pro`、`cursor-pro`、`copilot-pro`、`google-ai-pro`、`custom --monthly-usd`），显示"本周期已花占计划的比例"与超支预测 |
| `codeburn guard install` | **可选、本地**：给 Claude Code 加钩子，过 $5 告警、$15 停止，也能拦死循环与失败重试 |
| `codeburn context` | 分析某会话上下文构成（assistant/user/tool/system 分解，含压缩次数）；**计 token 而非美元** |
| `codeburn sessions --by-branch` / `--by-pr` / `--by-work-unit` | 按分支 / PR / 编排根（含子代理）汇总花费 |
| `codeburn yield` | 把会话与 commit 关联，分成 productive / reverted / abandoned；**从不调用 GitHub**，只看会话自己记的链接 |
| `codeburn menubar` | 装 macOS 菜单栏 / Capacity Dock（屏幕边缘挂一圈"计划余量"环） |
| `codeburn mcp` | 起一个 **stdio 本地 MCP 服务器**，暴露 `get_usage` 与 `get_savings` 两个工具；**自己不发网络请求**，项目名默认**化名**（pseudonymized） |
| `/plugin install codeburn --marketplace getagentseal/codeburn` | 在 Claude Code 内装插件，加 `/codeburn:cost` |

## 隐私与遥测（README 自述，值得单记）

- **CLI 什么都不发**："No wrapper, no proxy, no phoning home." **桌面应用 / macOS 菜单栏 / Windows 托盘默认可发匿名遥测**，可关闭。
- **明确列出"发什么"**：仅一个本机随机 id、只带**日期**（不带时刻），量级一律**分桶**（如花费 `<1` / `1-10` / `10-50` … USD）；事件如 `app_open`、`cli_error`（**错误文本不上传**，只传在本地判定的 reason 标签）、`update_result` 等。
- **明确列出"绝不上传"**：prompt、代码、文件内容、文件 / 文件夹名、项目名、分支名、工作目录、会话标题、PR 链接、API 密钥、精确金额、精确计数、时刻、IP 级位置（仅到国家）。**但会收集你用到的模型 / 工具 / 技能 / MCP 服务器名称**（表格已列出，且经白名单净化）。
- **数据出网面**：价格取自 LiteLLM（≤ 每天 1 次）、`quota` 用本机凭据问各厂商、非美元时汇率取自 Frankfurter、Cursor 用其登录态从 cursor.com 下载你自己的用量导出（≤ 约每小时 1 次，可用 `CODEBURN_CURSOR_SYNC=0` 关）。**它不代理你的流量，可自行用网络监控观察。**

## 使用前需要注意

- **⚠️ 版本口径一句话不一致**：**GitHub 描述写 "37 tools and agents"，README 标题写 "Works with 42 tools"**。**我未核实哪个是当前真值**——引用"支持多少个工具"时请注明口径来源，别把 42 当唯一答案。
- **⚠️ "美元"是套算价，不是账单**：订阅下它显示的是**按 API 定价该花多少**（API-equivalent），README 自己标注"not an invoice"。**用订阅的人尤其别把这个数字当成实际支出**；`plan set` 只是把花费对照你的计划价做比例。
- **⚠️ token 精度取决于上游工具**：Cursor、Kiro、部分 Copilot **是内容长度估算**（标 `estimated`）；Claude Code **部分调用根本不写 transcript**，所以 **CodeBurn 可能少于 Claude Code 自己的费用计数**。**它给的是"基于可观测量"的可解释数字，不是账务真相**（我未做任何准确度验证）。
- **⚠️ 会话明细会随上游清理而丢失**：Claude Code 30 天删会话；CodeBurn 只保留**按天的聚合历史**（README 称十年），**per-session 明细不保留**。旧区间上 `sessions` / `models` / `spend` / `compare-periods` 会比 `report` 少。**要长期留存逐会话明细，得自己另做归档。**
- **⚠️ `optimize --apply` 会改你的配置文件**：它会写 `CLAUDE.md`（追加标记块）、从 `~/.claude.json` 或项目 `.mcp.json` 删服务器条目、把未用技能移到 `~/.claude/skills/.archived/`。**虽会先展示、先备份、可 `act undo`，但这是对你自己配置的写入**——首次请先看 diff 再确认。
- **⚠️ `guard` 会给 Claude Code 装钩子**：`guard install` 会在 Claude Code 上加 hook，**过阈值会真的"停止会话/工具调用"**。是 opt-in 且本地，但**会改变你日常工具的行为**，要按 `codeburn guard status` 确认钩子位置，不用时 `guard uninstall`。
- **⚠️ 桌面端遥测默认开**：CLI 不发任何数据；**桌面应用与 macOS 菜单栏默认开启匿名遥测**（Settings > Privacy & data 可关）。介意者先关再日用。
- **它很新、迭代很快（事实）**：**2026-04-13 创建**，核验日已 **84 Release / 115 tags**、`pushedAt` 仅 2–3 天内——**高活跃同时也意味着行为与格式仍在快速变动**，README 与文档可能滞后。
- **我未做任何运行验证**：**未 clone、未安装、未运行任何命令、未接任何被追踪工具、未核对任何数字的准确度**；上述能力、命令、遥测清单、隐私边界**均来自 README、`LICENSE` 与仓库文件自述**。

## 什么时候会再看

- 想找一套**"读本地会话文件、按项目 / 模型 / 任务 / 分支复盘 AI 编码花费"**的现成工具（比我手写脚本省事）。
- 想参考它**"测量 / 计算 / 估算 / 已含 / 无价"五档 `costBasis`** 的做法——**在数字可信度上做显式分级**，这是可迁移的设计思路。
- 想抄"**由 CLI 统一算、桌面端 / 菜单栏 / 托盘 / Web 读同一批本地文件**"的架构，让多端数字一致。
- 想研究"**本地 MCP 服务器把用量喂给 Agent 自己**"（`get_usage` / `get_savings`，项目名默认化名）的隐私取舍写法。
- 评估**订阅制下的成本可见性**：`plan set` + `quota` + `guard` 这套"看占用比例 + 设预算上限"的组合，是否适合接入自己的工作流。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **CodeBurn 把 OpenCode 当被追踪对象，它本身是"观测工具"而非"Agent"**：[opencode：开源终端编码 Agent](opencode-开源终端编码Agent.md)——仓库里确有 `docs/providers/opencode.md` 与对应 provider 图标；两篇合看正好是"跑编码 Agent"与"量它花了多少钱"的两端。
- **CodeBurn 显式支持追踪 OpenClaw**：[openclaw：自托管的个人 AI 助手](openclaw-自托管个人AI助手.md)——仓库里有 `docs/providers/openclaw.md` 与 `app/renderer/assets/providers/openclaw.jpg`；若要给"本地 / 自托管 AI 助手"做用量观测，这是直接对应关系。
- **同为"读本机文件、不要账号"的本地可观测 CLI**：[witr：追踪进程端口容器是怎么起来的](witr-追踪进程端口容器是怎么起来的.md)——思路相近（都从本机既有痕迹还原事实），但一个看**进程 / 端口 / 容器**、一个看**AI 会话花费**。

## 参考来源

- 项目仓库与 README：https://github.com/getagentseal/codeburn
- 官网：https://codeburn.app/ ・ 隐私说明页：https://codeburn.app/docs/privacy
- 许可（MIT）：https://github.com/getagentseal/codeburn/blob/main/LICENSE
- 命令手册：https://github.com/getagentseal/codeburn/blob/main/docs/cli.md
- 工作原理（定价 / 任务分类 / 各工具数据位置）：https://github.com/getagentseal/codeburn/blob/main/docs/how-it-works.md
- Optimize 说明：https://github.com/getagentseal/codeburn/blob/main/docs/optimize.md
- Guard 说明：https://github.com/getagentseal/codeburn/blob/main/docs/guard.md
- MCP 说明：https://github.com/getagentseal/codeburn/blob/main/docs/mcp.md
- 被追踪工具一览：https://github.com/getagentseal/codeburn/tree/main/docs/providers
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 语言 / 默认分支 / Release 数 / topics）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12；Issue / PR 数取自 shields.io，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**未 clone、未安装、未运行任何命令、未接任何被追踪工具、未做任何准确度验证**；功能、命令、遥测清单与隐私边界均来自 README、`LICENSE` 与仓库文件自述；**"支持 37 还是 42 个工具"的仓库内口径不一致未核实**。
