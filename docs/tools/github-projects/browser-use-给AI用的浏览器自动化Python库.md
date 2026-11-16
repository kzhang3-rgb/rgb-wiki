# browser-use：给 AI 用的浏览器自动化 Python 库

> 一句话定位（README 自述）："**The open-source browser agent.**"——给它一个任务，模型**在真实浏览器里写 Python 完成它**。1.0 之后它的架构被重写成三件东西：**一个持久 REPL + `browser-harness` 辅助函数 + 原始 Chrome DevTools Protocol（CDP）**，跑在 Pydantic AI 上；模型可以是任意 LLM，浏览器可以是本地的、也可以是它家云端的。**最值得记的三点**：① 它是**开核商业项目**的典型——MIT 的库免费，但**模型推理、云浏览器、CAPTCHA 破解、住宅代理、联网搜索都走它的付费 Cloud**（$0.06/浏览器小时）；② 它**明确声明自己"不是沙箱"**——含一段关于"模型写的 Python 能读到密钥、本地调试端口无鉴权"的诚实说明；③ **发版极快（150 个 Release）**，README 反复要求**锁死精确版本**（`==1.0.3`，绝不用 `>=`）。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（291 行）、`LICENSE`、完整文件树与元数据；**未安装、未 `pip install`、未运行任何 Agent、未接任何模型 key、未试云端或本地浏览器、未跑 benchmark**）
- 适用范围：Python 3.11+（README 示例用 3.12）。三条路径——**托管 Cloud API** / **CLI** / **Python 库**；本地浏览器模式支持 `chromium` / `chrome`（含你已登录的 Chrome 与指定 profile）。仓库语言为 **Python**。

## 核心结论

**一句话定位（README 自述）**：开源浏览器 Agent（含 **Python 与 TypeScript** 两种实现），外加 **Browser Use Cloud**——**隐身浏览器 + 托管 Agent API**。核心演示：*"book the earliest driving test"*（找空位、选日期、过 CAPTCHA、完成预约）。

**1.0 的关键重写（README 自述事实）**：`browser-use 1.0` 里，"**模型对着原始 CDP 写 Python，在持久 REPL 里执行**"，底层是 **Pydantic AI**。README 用一张图概括："**The model, a REPL and CDP. That's the whole agent.**" 从 0.x 迁移见 `MIGRATING.md`。

**三个最值得记的点：**

1. **三种用法，能力边界不同**（README 明确区分）：
   | 路径 | 谁在跑 Agent | 谁在跑浏览器 |
   | --- | --- | --- |
   | **Fully Hosted Cloud** | Browser Use 云端 | Browser Use 云端 |
   | **CLI** | 你已有的 Agent（Claude Code / Codex / Hermes / OpenClaw / Cursor…） | 本地**或**云端 |
   | **Python Library** | 你自己的代码 | 本地**或**云端 |
   **只有"完全托管 Cloud"会把 Agent 和浏览器都放到它家服务器上**。
2. **MIT 只覆盖这个库；其余按量付费**（README 自述 + FAQ）：免费的是**库本身**；`bu-fast`/`bu-ultrafast` 默认模型、**云浏览器**、**联网搜索**（按次计费，计入 `result.cost_usd`）都走 Browser Use 的 key 与账单。**想完全本地也可以用**：`Browser('chromium')` + 本地模型（如经 `ChatOpenAI(base_url='http://localhost:11434/v1')` 接 Ollama）。
3. **它自己写清了信任模型的边界**（这是本篇最值得记的一段）：`sensitive_data={'password': ...}` 能避免密钥出现在 cell 输出、checkpoint、工具结果与结果里，但 **README 明说"不是沙箱"**——模型写的 Python 仍能读到该值，截图、`js()` 读取与云端实时视图都可能泄露；另外 **`Browser('chromium')` / `Browser('chrome', profile=...)` 会在 `127.0.0.1` 开一个无鉴权的调试端口，同一台机器上的其他本地用户可接入该浏览器及其登录态**（共享机器上建议用容器或云浏览器）。同样地，`allowed_domains` / `prohibited_domains` / `block_ip_addresses` 被描述为"**给对齐模型的护栏，而不是沙箱**"。

**个人判断（未验证）**：它是"**AI 浏览器自动化**"这条赛道上 Star 最高的库之一（12 万+），工程与文档成熟、API 面很宽（结构化输出、上传下载、密钥、域名限制、多轮、自定义工具、ultrafast 模式）。但它是**开核商业项目**：**默认路径会调它的网关模型与云浏览器**，独立使用需自己有 key；同时 **CAPTCHA 破解 + 隐身 + 住宅代理**这类能力本身带有"绕开站点防护"的含义，**用之前应自行核对目标站点的服务条款**。**我没有运行任何一次**，模型效果与 benchmark 数据均照录 README。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [browser-use/browser-use](https://github.com/browser-use/browser-use) |
| 描述 | "Open-source Python library for AI browser agents, plus Browser Use Cloud: stealth browsers and a hosted agent API." |
| 归属 | 组织账号 `browser-use`（组织名 "Browser Use"，ungh `orgs` 可查到）；`isFork: false`、`isArchived: false` |
| 许可证 | **MIT**（`LICENSE` 1,069 字符，`Copyright (c) 2024 Gregor Zunic`）——**只覆盖这个库；模型推理与云浏览器另行计费**（FAQ 与 ToS 链接） |
| 主语言 | **Python 98.7%**（shields.io，2026-11-13）；另提供 TypeScript 实现 |
| 技术栈 | Python 3.11+ · **Pydantic AI** · 原始 **Chrome DevTools Protocol（CDP）** · 全部主流模型接入（`ChatOpenAI` / `ChatAnthropic` / `ChatGoogle`，或任意 Pydantic AI 模型串）· 本地浏览器用 Playwright/Chromium 系 · 另有 [browser-harness](https://github.com/browser-use/browser-harness) 辅助库与 [benchmark](https://github.com/browser-use/benchmark) 仓库 |
| 关注度 | **120,879 Star / 13,516 Fork / 473 Watcher**（**页面值**，2026-11-13）；开放 Issue **169**、贡献者 **361**（shields.io，2026-11-13）。**ungh 镜像为 120,813 / 13,499 / 473，略有差异，以页面值记录** |
| 维护状态 | **2024-10-31 创建**；ungh 报 `pushedAt` **2026-11-12**、页面 `updatedAt` 同日（**活跃**）；累计 **150 Release / 161 tags**（页面值） |
| 默认分支 | `main` |
| 版本 | **150 Release / 161 tags**（README 示例锁 `browser-use==1.0.3`） |
| 话题标签 | `ai-agents` · `ai-tools` · `browser-agent` · `browser-automation` · `browser-use` · `cdp` · `cloud-browser` · `computer-use` · `headless-browser` · `llm` · `mcp` · `playwright` · `python` · `remote-browser` · `stealth-browser` · `web-automation` · `web-scraping` |
| 体积 | `main` 分支 **141 个条目**（ungh.cc files API）；顶层以 `browser_use`（49）、`tests`（23）、`skills`（19）、`.github`（16）、`static`（11）、`bin`（3）为主 |
| 官网 / 文档 | <https://browser-use.com> · <https://docs.browser-use.com> · 云控制台 <https://cloud.browser-use.com> |

## 能力一览（README 自述）

- **三种浏览器模式**：`Browser()`（默认，云浏览器，运行结束即停止）· `Browser('chromium')`（本地新开一个临时 profile 的 Chrome/Chromium/Edge，无显示时 headless，结束即删）· `Browser('chrome')`（**操控你已打开的 Chrome、带你的登录态**，需在 `chrome://inspect/#remote-debugging` 打开一次）；`Browser('chrome', profile='Work')` 用你某个 Chrome profile 的临时副本。
- **结果与数据**：`output_model_schema` 给**结构化输出**（`result.structured_output`）；上传（`workspace=...` + `upload_file(selector, path)`，云端也行、不限大小）；下载落到 `agent.workspace/downloads`（`result.downloads` 列出）。
- **密钥与限制**：`sensitive_data`（可按域名限定）；域名白/黑名单与 `block_ip_addresses`；README 明确这些都是**护栏不是沙箱**。
- **多轮与扩展**：`agent.add_new_task(...)` 续跑（配 `Browser(keep_alive=True)` 保持同一页面）；`@tools.action('description')` 注册**自定义工具**。
- **模式**：`mode='ultrafast'` 按 accessibility tree 的 id 操作（`bu-fast` / `bu-ultrafast` 上默认开启）；点击/输入处会显示 1 秒橙色高亮框（不出现在它自己的截图里，`highlight_actions=False` 可关）。
- **联网搜索**：`Agent(task, web_search=True)` 给 Agent 一个 `search(query, ...)` 工具（**按次计费到你的 Browser Use key**，且**不能与 `allowed_domains` 同用**）。
- **CLI 路径**：给已有编码 Agent 装浏览器能力——`uv tool install 'browser-use==1.0.3'` → `browser-use skill install`；出问题跑 `browser-use --doctor`。另提供 **MCP**（README 顶部标注 `mcp-name: com.browser-use/browser-use`）与反馈命令 `browser-use feedback '...'`。
- **Benchmark v2**：README 展示"**最难浏览器任务**"基准上的 rubric 分与每任务成本（**未在本笔记验证**）。

## 使用前需要注意

- **默认会走它的付费服务**：默认模型是它网关上的 `bu-fast`，默认浏览器是**云浏览器**，联网搜索按次计费。**要完全本地，需要显式换成 `Browser('chromium')` + 自己的模型/本地模型。**
- **"不是沙箱"是官方明说的**：密钥可能被模型写的 Python 读取；**本地浏览器会开一个无鉴权的 `127.0.0.1` 调试端口，同机其他本地用户可接入该浏览器与登录态**。在共享机器上应改用容器或云浏览器（README 建议）。
- **`Browser('chrome')` 会用你的真实登录态**：这既是它最强的能力，也是最大的风险面——**让 Agent 操作用你已登录的账号时，请自己想清楚权限边界**。
- **CAPTCHA 破解 / 隐身 / 住宅代理 / 反检测**：这类能力天然与很多站点的服务条款冲突。**README 未给出"请遵守目标站点条款"的提示**（我只读到一个 ToS/隐私政策链接指向它自家服务）。**用之前请自行核对目标站点条款与当地法规。**
- **发版极快，必须锁版本**：README 反复强调 `browser-use==1.0.3`（**绝不用 `>=`**），"releases move fast and can break APIs"。150 个 Release 也意味着**升级需读 release notes**。
- **169 个开放 Issue**：使用中很可能撞到未修问题。
- **我只读了文档与元数据**：**未安装、未运行、未接模型、未试云端**；benchmark 分数、模型效果、成功率**均照录 README，未独立验证**。

## 什么时候会再看

- 需要给一个 Agent / 编码助手**接上真实浏览器**（含登录态、表单、下载、上传）时，先来查它的 API 面与"本地 vs 云端"怎么选。
- 想对照"**浏览器自动化给 LLM 用**"的几种路线：本库（写 Python + CDP）· [playwright-mcp](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md)（Playwright 封成 MCP）· [bb-browser](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md)（把真实登录态当 API）。
- 想参考"**开核商业项目怎么写清免费/付费边界与信任模型**"时的范本（MIT 库 + 明说"不是沙箱" + 明说哪些走付费云）。
- 想研究"**AI 浏览器自动化的合规与反检测边界**"时——它是一个把 CAPTCHA 破解、隐身、住宅代理做成卖点的现实样本。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"把浏览器交给 LLM / Agent"**：[playwright-mcp：给 LLM 用的浏览器自动化 MCP 服务器](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md)——那篇走 **MCP + Playwright**（把浏览器能力以工具形式挂给任意客户端），本篇走 **Python 库 + 原始 CDP + 持久 REPL**（Agent 自己写代码驱动）。一篇偏"**工具化接入**"，一篇偏"**代码化驱动**"，可对照选型。
- **另一条"把真实浏览器登录态交给 Agent"的路线**：[bb-browser：把真实浏览器登录态给 Agent 当 API 的 CLI 与 MCP](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md)——它把**网站**变成命令行指令（sites → commands），本篇是**通用浏览器操作**。两者都在"Agent 如何安全地使用你的登录态"这件事上踩同一片雷区。
- **AI 抓取/研究方向的近邻**：[firecrawl：把网站转成 LLM 可用数据的抓取 API](firecrawl-把网站转成LLM可用数据的抓取API.md)——那是"**取数据**"的专精路线（抓取 → 干净 Markdown/JSON），本篇是"**像人一样操作网页**"的通用路线；需要"登录后点按钮办事"时只能选本篇这类。
- **同属"给 AI 用的浏览器自动化"，层次不同**：[moli：为 AI Agent 打造的 Rust 无头浏览器](moli-为AI-Agent打造的Rust无头浏览器.md)——本篇是**驱动浏览器完成任务的 Python 库**（Agent 层，写代码对着 CDP 执行）；moli 是**被驱动的独立无头浏览器内核**（执行层，Rust 自建、按需布局），可对照"**Agent 编排**"与"**内核 / 运行时**"。

## 参考来源

- 仓库主页与 README（18,479 字符 / 291 行，核验日期 2026-11-13）：https://github.com/browser-use/browser-use
- 许可证：`LICENSE`（MIT，1,069 字符，`Copyright (c) 2024 Gregor Zunic`）
- 迁移与信任模型：`MIGRATING.md`（README 指出 0.x → 1.0 迁移与 trust model 说明在此）
- 文档与云：<https://docs.browser-use.com> · <https://cloud.browser-use.com> · 企业页 <https://browser-use.com/enterprise>
- 关联仓库：<https://github.com/browser-use/browser-harness> · <https://github.com/browser-use/benchmark>
- 完整文件树（141 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档 / topics）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**与页面略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号经 ungh `orgs` 接口确认为组织 "Browser Use"

以上信息核验于 2026-11-13。**未安装、未运行、未接任何模型或云服务、未跑 benchmark**；架构（REPL + CDP + Pydantic AI）、三条路径、浏览器模式、计费方式与安全说明均来自 README 与 FAQ，"开核商业项目"的判断基于 README 对 Cloud/付费能力的描述。
