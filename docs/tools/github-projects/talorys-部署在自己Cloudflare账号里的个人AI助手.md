# talorys：部署在自己 Cloudflare 账号里的个人 AI 助手

> 一句话定位（README 自述）：**"你自己的云里，你的个人 AI Agent"**——把**聊天、记忆、任务、笔记、项目与定时提醒**整套跑在**你自己的 Cloudflare 账号**里，一条命令 `npx create-talorys@latest` 部署。最值得记的三点：① **没有任何由开发者运营的服务器 / 数据库 / 账号**，数据只落在你账号下的一个 SQLite Durable Object；② **单用户设计**（无注册、无团队，安装器设定 owner 密码）+ **免费额度友好**（Pages / Workers / Durable Objects / Workers AI 均在免费计划内）+ **无遥测**；③ 它把"AI 不可用"也当正常态——**Workers AI 配额用尽或不可用时，任务、笔记、记忆、提醒照常工作**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（290 行）、`LICENSE`（MIT，1,081 字符）与已核实元数据；**未 clone、未安装、未运行 `create-talorys`、未部署到 Cloudflare、未访问 Pages 站点**）
- 适用范围：面向**个人单用户**；部署依赖 **Node.js 22+** 与一个 **Cloudflare 账号**（免费计划即可，含 Workers / Pages / Durable Objects / Workers AI）；本地开发同样 Node 22+；**未在本地验证过任何一条命令**

## 核心结论

1. **运行位置是"你的账号"**（README 自述）：浏览器只与你的 `*.pages.dev` 站点通信；`/api/*` 由 Pages Function 经 **service binding** 转发给私有 Worker；该 Worker 以 `workers_dev: false`、`preview_urls: false` 部署，**没有公开 URL**；鉴权在 Worker 而非前端完成，聊天回复全链路以 **SSE** 流式返回。
2. **技术栈即 Cloudflare 全家桶**（README 架构图）：Pages（React 前端 + Pages Function）→ 私有 Worker（Hono 路由）→ `TalorysAgent`（Cloudflare Agents SDK 的 **Durable Object**）→ **SQLite** 存会话 / 记忆 / 任务 / 笔记 / 项目 / 自动化 / 会话凭据 / 设置 / 用量；模型走 **Workers AI `@cf/zai-org/glm-4.7-flash`**（流式 + 工具调用）；定时靠 **Durable Object alarms**。README 明确**不**开通 R2、D1、KV、Vectorize、AI Search、Workflows 或任何付费服务。
3. **数据边界清楚**：全部数据存于你账号内的单个 SQLite Durable Object；**无遥测 / 分析 / 跟踪 / 广告代码**，不向开发者发送任何东西。但 README 也如实写明：**Cloudflare 会为提供服务处理你的数据**（含在你的聊天消息与相关记忆上运行 Workers AI 推理）。
4. **AI 是可选增强，不是地基**：README 明写"**没有 AI 也能用**"——Workers AI 不可用或免费配额耗尽时，任务、笔记、记忆与提醒继续工作，数据不受影响；聊天会显示明确提示并在每日重置后恢复。

**个人判断（未验证）**：talorys 的卖点不是"功能最全"，而是**"私有化 + 零运维 + 免费档"**这三者的组合——把通常需要自建服务器 / 数据库的个人助手，压进了一人一账号一条命令的形态。要判断它是否适合长期用，关键看两点：**Cloudflare 免费额度是否够用**，以及**你是否接受"数据虽在你自己账号、但推理仍由 Cloudflare 执行"**这个前提。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [rociiu/talorys](https://github.com/rociiu/talorys) |
| 描述 | "Your personal AI agent in your own Cloudflare account. Chat, memory, tasks, notes and scheduled reminders, deployed with one command: npx create-talorys@latest. Free-tier friendly, single-user, no telemetry." |
| 许可证 | **MIT**（`LICENSE` 1,081 字符，Copyright (c) 2026 The Talorys contributors） |
| 关注度 | **ungh 1,464 Star / 6 Watcher / 147 Fork**；**页面 1,466 / 6 / 147**（2026-11-13）；shields 另报**开放 Issue 4、贡献者 14** |
| 维护状态 | **2026-10-10 创建**；ungh `pushedAt` 2026-11-10、页面 `updatedAt` 2026-11-12；默认分支 `main`；**Release 页面 1 条** |
| 站点 | 官方站点 https://rociiu.github.io/talorys/ |
| 部署形态 | 一条命令 `npx create-talorys@latest`；或经 [Appflare](https://appflare.dev) 部署为**单个 Worker**（无 Pages） |
| 依赖服务 | Cloudflare Pages / Workers / Durable Objects（SQLite）/ Workers AI —— **均在免费计划内** |
| 数据位置 | 你账号内的单个 SQLite Durable Object（`personal-agent`） |

## 功能与用法

### 1. 能力清单（README）

- **聊天**：流式回复、Markdown、工具活动指示。
- **记忆**：可查看 / 编辑 / 删除的持久事实与偏好；每轮只把**最相关的记忆**送给模型。
- **语义召回（可选，默认关）**：按含义而非关键词找记忆，越新越靠前并带保存日期；也可按含义找任务 / 笔记 / 自动化。在 Settings → AI 底部开关，或安装时加 `--semantic`。
- **记忆历史**：随时间变化的事实——你说卖掉车时它会问旧车是否已成"过去式"，开启 AI 复核后可由 AI 直接处理；模糊冲突仍留给你确认。
- **任务 / 笔记 / 项目**：UI 与聊天均可做完整 CRUD（"Add a task to review my project tomorrow"）；聊天里输入的到期日会拒绝不存在的日历日期（含带 UTC 偏移的时间戳）。
- **AI 任务规划**：描述目标（"Plan the tasks to launch my iOS app next month"）→ 聊天里以**交互卡片**给出计划 → 你审查 / 编辑 / 删除 / 选项目 → 确认后**一次性、恰好一次**创建。
- **自动化**：一次性与重复提醒、每日任务摘要、可选 AI 例程，投递到应用内通知中心；运行在 Durable Object alarms 上，**不需要任何东西常开**。
- **推送通知**：标准 Web Push 端到端加密，Talorys 关闭时也能收到；iPhone / iPad 需先"添加到主屏幕"。
- **聊天提醒**：你在其它窗口时，回复完成或需要你批准会发系统通知（需应用保持打开）。
- **远程 MCP 服务器（可选）**：在 Settings → Integrations 连接（无鉴权 / bearer token / OAuth），逐个选择允许使用的工具；**工具默认关闭**，可能改变状态的一律等你批准；**定时例程不使用** MCP。
- **联网搜索（可选，默认关）**：用你自己的 [Tavily](https://tavily.com) 或 [Exa](https://exa.ai) API Key，各自有每月免费额度；默认每次查询等你批准；**只发送查询，Talorys 从不打开网页**。

### 2. 部署与运维（README）

```bash
npx create-talorys@latest                                                      # 交互式部署
TALORYS_OWNER_PASSWORD=... npx create-talorys@latest --yes --account-id <id>   # 非交互
```

安装器会：检查 Node.js 22+ 并使用自带 Wrangler → 校验 Cloudflare 登录（必要时打开授权页）→ 选账号与 Agent 名 → 设定 owner 密码（本地 PBKDF2-SHA256 哈希后仅作 Cloudflare secret 存储）→ 生成 256 位会话密钥与 Web Push 密钥对 → 部署私有 Worker（建 SQLite DO）、创建并部署带 service binding 的 Pages 项目 → **不跑任何 AI 推理**地校验线上部署 → 打印真实的 `https://….pages.dev`。中断后**重跑同一命令即可续做**（不重复建资源、不重问密码、**从不删数据**）。

常用命令：`reset-password`（换密码并登出所有设备）、`update`（重部署到**同一批**资源，DO 命名空间从不重建、密码与会话保留、schema 迁移在首个请求上自动事务执行）、`status`、`doctor`。

### 3. 数据与备份（README）

- **备份**：Settings → Privacy → Download backup 导出 `talorys-backup.json`（含会话 / 记忆 / 任务 / 笔记 / 项目 / 设置 / 自动化，**不含**会话凭据）。
- **导入**：校验后单事务合并，重复导入无害；备份后被本地改过的记忆 / 任务 / 笔记 / 项目**不会被覆盖**；备份被视为**不可信**——新自动化以暂停态进入，模型 / 限额 / 演示模式等设置保持不变（跳过项会列出）。

### 4. 本地开发

Node 22+，`git clone` 后 `npm install && npm run dev` 一条命令同时起 Worker（8787）、Pages Function 代理（8788）与 Vite UI（5173）；本地用**确定性 mock AI provider**，无需登录 Cloudflare；状态存在 `.wrangler/state`。另有 `build` / `test` / `test:e2e` / `test:pack` / `typecheck` / `lint`。

## 使用前需要注意

- **免费 ≠ 无限**：README 明说"**not 'unlimited free'**"——免费计划有账号级配额（请求、Durable Objects 用量、Workers AI 每日 Neuron 分配），**配额由 Cloudflare 设定且可能变化**；付费账号超额按你的计划由 Cloudflare 计费。内置护栏（Settings → AI 可调）：最大输出 token、最大上下文 token（旧历史会被摘要）、每次请求的最大工具调用与推理步数、每日最大 AI 请求数、每日最大定时 AI 运行数。
- **用量面板不是账单**：面板区分"**provider 上报 token**"与"**回退估算 token**"，并显示本地估算的 Neurons 与 USD 推理价值；README 明确它**不是 Cloudflare 账单或配额余额**，账号级用量与免费额度**不在其中**。未支持定价的模型**保持"未定价"而非免费**。
- **数据虽在你账号，推理仍经 Cloudflare**：README 直说 Cloudflare 为提供服务处理你的数据（**在你的聊天消息与相关记忆上运行 Workers AI 推理**），须审阅 Cloudflare 隐私政策与 Workers AI 条款。
- **MCP 是数据出口**：连接远程 MCP 并打开某工具后，**该次调用的参数会发往你 Cloudflare 账号之外的服务器**；不连接、不打开就什么都不发；**定时例程不用 MCP**。
- **联网搜索同样有出口**：启用后**只有查询词**会发给 Tavily / Exa，Talorys 不会打开网页（**自述未验证**）。
- **单用户、无多人**：无注册、无账号、无团队；安装器设定唯一 owner 密码，多设备登录都是同一 owner。忘记密码用 `reset-password` 重置并登出所有设备。
- **元数据口径**：README 列的贡献者名单（Roc Yu 等 7 人）与 shields 报的 **14 contributors** 口径不同；Star / Fork 我用 ungh（1,464 / 147）与页面（1,466 / 147），**差异很小**，引用时注明来源与日期。
- **我只读了 README 与 LICENSE**：**未 clone、未安装、未运行 `create-talorys`、未部署到 Cloudflare、未访问 Pages 站点**；上述能力与流程均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想给个人助手找一个**"数据在自己账号、零自建运维、免费档可跑"**的落点时。
- 想看**"个人 AI 助手 + 记忆 + 任务 + 定时提醒"**如何用 **Workers + Durable Objects（SQLite）+ Workers AI** 拼成一个单体部署时。
- 想评估**"AI 配额耗尽 / 模型不可用时系统降级"**该怎么做时（README 的做法：核心功能不依赖 AI，聊天给明确提示并等每日重置）。
- 想对照其它"自托管 / 私有个人助手"路线时（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"自托管的个人 AI 助手"，部署位置不同**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——那篇是自建服务式助手；本篇把整套塞进**你自己的 Cloudflare 账号**，一条命令部署。可对照"**自建服务器**"与"**借宿主平台免费档**"两种私有化路线。
- **都关心"AI 的长期记忆"**：[mnemos-mcp：让 AI 拥有自己长期记忆的 MCP 服务器](mnemos-mcp-让AI拥有自己长期记忆的MCP服务器.md)——那篇把记忆做成 **MCP 服务器**；本篇把记忆作为**应用内一等公民**（可查看 / 编辑 / 删除，带"记忆历史"与可选语义召回）。可对照"**记忆作为外部服务**"与"**记忆作为产品内置层**"。
- **同为助手加"记忆 / 生活层"**：[memory-brain：AstrBot 记忆与生活层](memory-brain-AstrBot记忆与生活层.md)——那篇给 AstrBot 挂记忆与生活层；本篇是**一体化单用户助手**（记忆 + 任务 + 笔记 + 自动化）。可对照"**插件式增强**"与"**单体产品**"。
- **都叫"个人 AI"，一个是助手本体、一个是单一用途流水线**：[Horizon：把自己关注的源收成一份每日新闻简报](Horizon-把自己关注的源收成一份每日新闻简报.md)——本篇把聊天 / 记忆 / 任务 / 笔记 / 提醒整套装进一个应用，Horizon 只把"**新闻简报**"这一件事做到闭环。
- **同样把部署压到 Cloudflare 免费档**：[edgeKey：零成本部署到 Cloudflare Workers 的开源发卡商城](edgeKey-零成本部署到CloudflareWorkers的开源发卡商城.md)——那篇用 Workers 做发卡商城；本篇用 **Pages + Workers + Durable Objects + Workers AI** 做个人助手。可对照同一平台上"**轻后端应用**"与"**带持久状态与定时任务的助手**"的不同复杂度。

## 参考来源

- 仓库主页与 README（290 行；核验日期 2026-11-13）：https://github.com/rociiu/talorys
- 许可证：`LICENSE`（MIT，1,081 字符，Copyright (c) 2026 The Talorys contributors；核验日期 2026-11-13）
- 站点：<https://rociiu.github.io/talorys/>
- 文档（据 README 引用）：`docs/architecture.md`、`docs/deployment.md`、`docs/development.md`、`docs/security.md`、`docs/troubleshooting.md`、`docs/mcp.md`、`docs/web-search.md`、`docs/semantic-recall.md`、`docs/memory-history.md`、`docs/remote-access.md`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：ungh.cc 与 GitHub 页面内嵌数据、shields.io 交叉核对（2026-11-13，**镜像与页面差异很小**）

以上信息核验于 2026-11-13。**未 clone、未安装、未运行 `create-talorys`、未部署到 Cloudflare、未访问 Pages 站点**；能力与流程描述均来自 README，**未逐一验证**。
