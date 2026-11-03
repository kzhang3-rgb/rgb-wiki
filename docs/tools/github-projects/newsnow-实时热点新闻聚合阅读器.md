# newsnow：实时热点新闻聚合阅读器

> 记录这个把各平台实时热榜聚合到一个界面里的阅读器——"Elegant reading of real-time and hottest news"。第一句先记清三件事：**仓库已从个人账号 `ourongxing` 迁到组织 `newsnext`**（同一 repo id 861790708）；**上游正处在"要发新版、不再收贡献"的过渡期**（README 顶部挂 NewsNext 等待名单，Contributing 段被删除线划掉）；**它自带 MCP server**，既能给人看，也能当 Agent 的信息源。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README（经 jsDelivr 取 `@main`）、GitHub API 元数据、默认分支 `main` 的 commits 接口、`git/trees/main` 目录树与 releases 列表；另用 `ourongxing/newsnow` 抓取做迁移核验。**未部署、未运行、未接入 GitHub OAuth、未审计源码**）
- 适用范围：浏览器访问的 Web 应用（另有 PWA 配置）；可自托管（Cloudflare Page / Vercel / Docker）

## 核心结论

它解决的是"**不想一个个平台翻热榜，想在一个界面里读完**"这个问题。把各平台的实时热搜聚到一处，支持 GitHub 登录后跨设备同步，并对外提供一个 MCP server 供 Agent 取用。

三点必须先看清：

**① 仓库换过地方了——它已不在个人账号下。** 用户给出的是 `newsnext/newsnow`；我另外抓了一次 `ourongxing/newsnow`，两次返回的是**完全相同的 JSON**（同一个 repo id `861790708`）。这说明**仓库已从作者个人账号 `ourongxing` 迁移到组织 `newsnext`**，旧地址会被 GitHub 重定向。收藏时以 `newsnext/newsnow` 为准即可。

**② 上游进入"新版在路上、不再收贡献"的过渡期。** README 顶部写着 "**NewsNext, the next gen of NewsNow, is coming soon**" 并附等待名单；正文的 Contributing 段**整体被 `~~` 删除线划掉**，改写为"The new version is coming soon and we will no longer accept contributions"。但仓库里 `CONTRIBUTING.md` 文件**仍在**（4,803 B）——**README 的口径与仓库现状不一致**，这类"自述与现状不符"的点值得留意（同类核对点此前在 galaxy-downloader「README 宣称下载桌面版但无发行物」、Easel「技能数与平台数两处口径不一」上都出现过）。

**③ 它自带 MCP server，这是本条目最值得记的一点。** README 明确列出"MCP server"特性，配置形如 `npx -y newsnow-mcp-server`，`BASE_URL` 可指向自建实例。也就是说，**它同时是"给人读的界面"和"给 Agent 读的数据源"**——这是本仓库里少见的、同一项目横跨两个消费方的例子。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [newsnext/newsnow](https://github.com/newsnext/newsnow)（旧地址 `ourongxing/newsnow` 已重定向至此） |
| 描述 | "Elegant reading of real-time and hottest news" |
| 许可证 | **MIT**（`LICENSE` 1,067 B；README 页脚写 `MIT © ourongxing`） |
| 主语言 | TypeScript（另有 Dockerfile、`pwa.config.ts`、`uno.config.ts`、`vite.config.ts`、`vitest.config.ts`） |
| 关注度 | **22,239 Star、6,137 Fork、68 关注、161 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2024-09-23 创建；`pushed_at` **2026-10-13**；默认分支 `main` 最新提交 `0560c993`「chore: release v0.0.43」为 **2026-10-13**，前一提交 2026-10-12「fix: update Kaopu and AIHOT RSS feeds」，再往前是 2026-09-16 → **约月度节奏，核验时距上次提交约 3 周** |
| 体积 | 约 6.1 MB（API `size` 6,243 KB；其中 `pnpm-lock.yaml` 就占约 517 KB） |
| 线上站点 | https://newsnow.busiyi.world （README 所填 homepage，**作者自建服务，非官方站点**） |
| Topics | 仅 `elegant`、`news` |
| 其他开关 | 未开启 Discussions / Wiki / Pages（API 均返回 false） |
| 版本节奏 | 最新 **v0.0.43**（2026-10-13，**由 `github-actions[bot]` 自动发布，无附件资产**）；上一版 v0.0.42（2026-09-16）、v0.0.41（2026-06-26） |
| 开发工具痕迹 | 根目录有 `.cursorindexingignore`（说明用 Cursor 开发）；未见 `.claude/` 或 `AGENTS.md` |

> **关于 `updated_at` 与 `pushed_at` 的差别**：API 的 `updated_at` 是 **2026-11-03**（核验当天），但 `pushed_at` 与默认分支最新提交都停在 **2026-10-13**。`updated_at` 会被 star、Issue 等边缘活动刷新，**不代表代码有更新**——判断活跃度仍以默认分支的提交为准。

> **关于关注度指标的悬殊**：22,239 Star 对应的"关注者"（`subscribers_count`）只有 **68**。GitHub API 的 `watchers_count` 与 `stargazers_count` 恒等（历史遗留），真正看"盯着这个仓库的人"要看 subscribers——两者相差两个数量级，说明它是**高收藏、低跟踪**的类型。

## 它能做什么

**使用方式**

- 网页应用：打开站点直接看各平台实时热榜；用 GitHub OAuth 登录可跨设备同步阅读进度与偏好。
- 自托管（README 给了三条路）：Fork 后部署到 **Cloudflare Page / Vercel**；或直接用仓库自带 `docker-compose.yml` 跑 `docker compose up`。
- 给 Agent 用：配置 **MCP server**（`npx -y newsnow-mcp-server`），把热榜数据接进支持 MCP 的客户端。

**核心特性（README 列表）**

- 干净的阅读界面（"Clean UI"）＋ 实时热点聚合。
- **GitHub OAuth 登录**并同步数据。
- **默认 30 分钟缓存**，抓取间隔**自适应、最小 2 分钟**——用来降低对上游平台的压力与频控风险。
- **支持 MCP server**。
- Roadmap 写明：多语言、个性化、扩充数据源。

**部署相关（README 摘要）**

- 构建命令 `pnpm run build`，产物输出到 `dist/output/public`。
- GitHub OAuth 回调路径 `/api/oauth/github`。
- 关键环境变量：`G_CLIENT_ID` / `G_CLIENT_SECRET` / `JWT_SECRET` / `INIT_TABLE` / `ENABLE_CACHE`（仓库提供 `example.env.server` 与 `example.wrangler.toml` 样例）。
- 数据库**推荐 Cloudflare D1**（README 指向 db0.unjs.io/connectors）。
- 本地开发要求 **Node.js >= 20**。
- 想加数据源，README 指向 `shared/sources` 与 `server/sources` 两处。

## 使用前需要注意

**① 抓的是别人的热榜，条款与频控风险由部署者自担。** 这类聚合器的数据来自各平台公开接口；项目已用"30 分钟缓存 + 最小 2 分钟自适应间隔"来降频，但**是否合规、是否会触发平台频控，取决于部署者的使用方式**——自建站点对外提供时尤其要想清楚。

**② 能力边界由上游决定，上游一变就得跟着改。** v0.0.43 的修复内容正是"更新 Kaopu 和 AIHOT 的 RSS 源"——**数据源中断是常态，不是意外**。看这个仓库的提交记录，很大一部分会花在跟着上游改数据源上。

**③ 上游已明说"不再接受贡献"。** 想提 PR 之前先看 README 现状；`CONTRIBUTING.md` 虽还在，但按 README 口径，**当前阶段大概率不会合并外部贡献**。

**④ README 里有赞助收款二维码。** 页面底部 Sponsorship 段落含 `./screenshots/reward.gif`，属正常个人项目赞助，记录在此以便核验时对照。

**⑤ 未部署、未运行、未审计源码。** 本笔记仅基于 README、目录树、API 元数据与提交/release 记录，**没有实际拉起服务、没有完成 GitHub OAuth 流程、没有审查数据抓取实现**。

## 什么时候会再看

- 想自建一个"只看热榜"的聚合站，或找一份可部署到 Cloudflare / Vercel 的现成实现时。
- 想给 Agent 接一个**新闻/热榜信息源**（MCP）时——这是它区别于普通阅读器的地方。
- 跟踪 **NewsNext 新版**是否落地、是否改为新架构或转向托管服务时。

## 相关笔记

- **同为"给人读、也给 Agent 读"的站点**：[lexfridman-cn：Lex Fridman 播客中文站](lexfridman-cn-LexFridman播客中文站.md)——那边靠自建 MCP server 供 Agent 取数据，本篇提供 `llms.txt` + 全文 RSS + JSON-LD；可对照"接一个 MCP 服务"与"把站点做成可被 LLM 直接读的结构"两种做法。
- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"给 Agent 用的信息入口"，层级不同**：[servers](servers-MCP官方参考服务器合集.md) 是**官方参考服务器清单**（协议与实现样例），本篇是**一个自带 MCP server 的具体应用**（热榜数据源）。
- **同属"把外部内容变成可用数据"，取向不同**：[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md) 是**通用抓取 API**（把任意网页转成 LLM 可用数据），本篇是**面向人的热榜聚合**（顺带提供一个 MCP 出口）——一个偏"取任意页面"，一个偏"聚固定榜单"。
- **同为"提供 MCP server"，暴露的东西不同**：[playwright-mcp](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md) 通过 MCP 暴露**浏览器自动化能力**（等于给 Agent 一双会点鼠标的手），本篇通过 MCP 暴露**热榜数据**（给 Agent 送数据）——可对照"给数据"与"给操作能力"两类 MCP 服务器。
- **同属"取站点内容"，路线不同**：[bb-sites](bb-sites-把网站变成命令行指令的适配器集.md) 是在**你已登录的浏览器会话里执行适配器**（不碰 Cookie、不绕反爬），本篇是**服务端抓各平台热榜并加缓存**——前者贴身、后者集中，平台条款风险同样自担。

## 参考来源

- 项目仓库与 README：https://github.com/newsnext/newsnow
- 旧地址（已重定向，用于核验仓库迁移）：https://github.com/ourongxing/newsnow
- 线上站点：https://newsnow.busiyi.world
- NewsNext 等待名单：https://tally.so/r/yPBBYg
- MCP server 包：`npx -y newsnow-mcp-server`
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-03
