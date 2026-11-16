# bb-browser：用真实浏览器的登录态，让 AI Agent 直接"当人用"网站

> 记录它反直觉的那一面：不让网站提供 API，而是让 Agent 直接复用你已登录的真实 Chrome 会话，把"你已登录的一切"变成命令。

- 状态：草稿
- 最近核验：2026-11-05（阅读中英文 README、目录树与 GitHub API 元数据，未安装、未运行、未驱动浏览器）
- 适用范围：Node.js 18+；需要本机已登录的 Chrome（或走 OpenClaw 内置浏览器，无需扩展）

## 核心结论

bb-browser 的定位一句话写清了：**"Your browser is the API. No keys. No bots. No scrapers."**（你的浏览器就是 API，不需要 Key、不需要机器人、不需要爬虫）。

它换了一个思路解决问题：互联网 99% 的网站都没有给机器用的接口，于是**不再要求网站提供机器接口，而是让机器直接使用人类界面**——适配器跑在你自己的浏览器标签页里，用 `eval` 执行、用你的 Cookie 调 `fetch()`、或直接调用页面自己的 webpack 模块。**网站以为是你，因为它确实就是你。**

它按"已登录状态现成可用"这条差异做了很直白的对比表：

| | Playwright / Selenium | 爬虫库 | bb-browser |
| --- | --- | --- | --- |
| 浏览器 | 无头、隔离 | 无浏览器 | **你自己的真实 Chrome** |
| 登录态 | 无，要重新登录 | 需提取 Cookie | **本来就在** |
| 反爬 | 容易被识别 | 猫鼠游戏 | **隐形——它就是用户本人** |
| 复杂鉴权 | 复现不了 | 逆向 | **页面自己处理** |

已知能力：**103 条命令覆盖 36 个平台**，且适配器由**独立仓库** [`epiral/bb-sites`](https://github.com/epiral/bb-sites) 维护（这就是本仓库里已收录的 [bb-sites](bb-sites-把网站变成命令行指令的适配器集.md)）。示例命令覆盖 Twitter / Reddit / YouTube / 知乎 / B站 / LinkedIn / GitHub / arXiv / 东方财富 / Boss / Wikipedia / StackOverflow 等。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [epiral/bb-browser](https://github.com/epiral/bb-browser) |
| 描述 | Your browser is the API. CLI + MCP server for AI agents to control Chrome with your login state. |
| 许可证 | MIT |
| 语言 | TypeScript |
| 关注度 | **6,259 Star、608 Fork、14 关注、88 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2026-01-31 创建**，最近推送 **2026-05-29**；默认分支 `main`；12 个分支 |
| 贡献者 | 9 人，主力 `yan5xu`（236 次提交） |
| 发行版 | Release 走 `release-please`，最新 `bb-browser-v0.11.6`（2026-05-11，**0 资产**，仅作版本记录） |
| 体积 | 约 592 KB（`size` 字段） |
| 归属 | 组织 `epiral`（User/Org 类型为 Organization） |
| 目录 | `packages/`（核心）、`skills/`（Agent 技能）、`web/`；根目录含 `AGENTS.md`（8.6 KB）、`CHANGELOG.md`（35 KB）、`Dockerfile`、`PRIVACY.md` |

## 用法要点

```bash
# 安装
npm install -g bb-browser

# 拉取社区适配器 / 看看哪些适配器匹配你的浏览习惯
bb-browser site update
bb-browser site recommend

# 直接用（复用当前登录态）
bb-browser site zhihu/hot
bb-browser site twitter/search "AI agent"
bb-browser site eastmoney/stock "茅台"
bb-browser site youtube/transcript VIDEO_ID
```

两条"免扩展"路径值得注意：

- **OpenClaw**：如果装了 [OpenClaw](https://openclaw.ai)，bb-browser 可直接走它内置的浏览器，**不需要 Chrome 扩展或常驻守护进程**：`bb-browser site reddit/hot --openclaw`，并可配 `--jq` 过滤输出。
- **MCP**：README 给出 Claude Code / Cursor 的 `mcpServers` 配置片段（服务名 `bb-browser`），把"浏览器能力"作为 MCP 工具提供给编码助手。

## 使用前需要注意

- **"复用你的登录态"是它最大的能力，也是最大的风险面**：Agent 拿到的等于你当前已登录的一切（邮箱、社交、网盘、股票账户……）。**是否允许某个 Agent 走这条路，是权限与信任问题，不是技术问题**。建议只在你信任的本地 Agent、且明确知道它要执行什么命令时使用。
- **合规边界**：用你自己的登录态访问网站，仍受各网站服务条款约束；批量抓取、自动化操作可能触及 ToS。README 的对比表把"隐形"当卖点——**这句话同时也意味着"对网站不可见/不可区分"**，用于非本人授权场景并不合适。
- **维护已明显放缓**：`pushed_at` 停在 **2026-05-29**（核验时已约 5 个多月无新提交）；`updated_at` 2026-11-05 是被 star 之类刷新，**不等于代码在更新**。适配器依赖第三方页面结构，页面一改就可能失效——这也是它把适配器拆到 `bb-sites` 单独维护、并要求 `site update` 的原因。
- **Release 0 资产**：安装走 npm 包，GitHub Release 只是版本标记，别去 Release 页找可执行文件。
- **`AGENTS.md` 存在**：说明它自身也按"给 Agent 交代上下文"的约定组织仓库（`skills/` 目录同样面向 Agent）。
- 我**未安装、未运行、未连接任何浏览器**：以上命令、平台清单均来自 README 自述，未验证实际可用性。

## 什么时候会再看

- 需要让 Agent **用你已有的登录态**做一件具体的事（搜一条推、取一个行情、读一段字幕），又不想为每个站点单独找 API 时。
- 想参考"**把浏览器当 API**"这条路线，以及它与 Playwright / 爬虫库的取舍时。
- 想对照"适配器与 CLI 分仓维护"（`bb-browser` vs `bb-sites`）这种做法时。
- 想给 Claude Code / Cursor / OpenClaw 加一个"操作真实浏览器"的 MCP 能力时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"让 Agent 用真实浏览器"的另一条路线**：[browser-use：给 AI 用的浏览器自动化 Python 库](browser-use-给AI用的浏览器自动化Python库.md)——那是**通用浏览器操控库**（可接管你已登录的 Chrome、官方明说"不是沙箱"、本地会开无鉴权调试端口）；本篇是**把网站变成命令、把真实登录态当 API**。两者都在"**Agent 如何安全地使用你的登录态**"这件事上踩同一片雷区。
- **它的适配器仓库（同作者、成对出现）**：[bb-sites：把网站变成命令行指令的适配器集](bb-sites-把网站变成命令行指令的适配器集.md)——那是"命令怎么实现"，本篇是"命令怎么跑在你的浏览器上"，**两篇合看才是完整链条**。
- **同为"给 Agent 的浏览器自动化"的另一条路线**：[playwright-mcp：给 LLM 用的浏览器自动化 MCP 服务器](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md)——那是微软的 MCP 服务器，用无头/隔离浏览器 + 无障碍树快照；本篇坚持用**你自己的真实浏览器 + 真实登录态**。一个"给 Agent 一个干净沙箱"，一个"让 Agent 借用你的身份"，取舍完全不同。
- **运行时宿主**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——bb-browser 可直接走 OpenClaw 内置浏览器（`--openclaw`），无需 Chrome 扩展。
- **同为"让 Agent 操作真实桌面/浏览器"的 MCP 实现**：[crossdesk：轻量跨平台远程桌面与 MCP 远控服务端](crossdesk-轻量跨平台远程桌面与MCP远控服务端.md)——那是远控整台桌面（含截图、点击），本篇聚焦网页取数；可与它的**授权模型**（设备白名单、密码不进模型上下文）对照"给 Agent 的边界怎么设"。
- **都强调"把真实浏览器能力交给 Agent"，入口不同**：[moli：为 AI Agent 打造的 Rust 无头浏览器](moli-为AI-Agent打造的Rust无头浏览器.md)——本篇**复用你已登录的真实 Chrome**（把网站变成命令、登录态当 API）；moli 是**自带的独立无头内核**（Rust 自建、按需布局、不依赖你既有的浏览器），可对照"**复用既有浏览器**"与"**自带内核**"。

## 参考来源

- 项目仓库：https://github.com/epiral/bb-browser
- 适配器仓库：https://github.com/epiral/bb-sites
- 官网 / 宿主：[OpenClaw](https://openclaw.ai)；ClawHub 技能页 `bb-browser-openclaw`
- npm 包：https://www.npmjs.com/package/bb-browser
- 元数据（Star / Fork / 分支 / Release / 贡献者 / 时间）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读中英文 README、目录树与元数据；未安装、未运行、未连接任何浏览器，平台与命令清单均为作者自述。
