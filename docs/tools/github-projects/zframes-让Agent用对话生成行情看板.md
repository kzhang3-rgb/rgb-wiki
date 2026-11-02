# zframes：让 Agent 用对话生成行情看板的框架

> 一句话值得记住的取舍：它把"AI 生成看板"落成了一个**带校验闭环的 JSON 契约**——Agent 只写 `dashboard.json`，渲染完全由框架负责，写错就逐帧报错让 Agent 自己改。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文与 GitHub API 元数据，未安装 skill、未运行 CLI）
- 适用范围：面向支持 skills 标准的编码助手（Claude Code 为一等支持）；行情以加密与美股为主

## 核心结论

zframes 想做的是：**你不用写前端，用自然语言描述你想要的行情看板，由 AI 助手生成并跑起来**。

它的分工很干净，也解释得比较清楚：

| 角色 | 做什么 |
| --- | --- |
| 你 | 描述想要的看板（"给我一个 TSLA + NVDA 的终端，带资金费和恐慌指数"） |
| Agent | 读**frame 目录** → 写 `dashboard.json` → 用 `zframes lint` 校验 → `zframes serve` 起服务 |
| 框架 | 拥有全部渲染逻辑；**Agent 一行 React 都不用写** |

关键的工程设计是**校验闭环**：Agent 面对的契约只有两样——**目录（frame 名 + 配置 schema）**和**校验器（逐帧报错）**。`dashboard.json` 里某一帧写得不合法，**就只让那一帧报错并给出可读原因**，Agent 据此自我纠正。README 明说这个"生成循环是内建在渲染契约里的"。

数据侧的设计同样值得记：

- **单文件、可 diff、可 git 跟踪**：整个看板就是一份 `dashboard.json`（版本、标题、作者、栅格、背景、主题、货币、以及各帧的位置与配置），CLI 在本地服务它，**没有托管服务、没有锁定**。
- **帧是类型化的**：`defineFrame({ name, description, capabilities, schema, component })`，其中 Zod schema **每个字段都带 `.describe()`**，通过 `catalogueForAI()` 导出成 JSON Schema——**同一份 schema 既是运行时校验，也是给 AI 看的 API 文档**。
- **能力路由是首匹配**：帧声明需要什么能力（`quote-stream`、`ohlcv`、`tvl`、`sentiment` 等），运行时把需求路由给第一个覆盖它的 provider。**覆盖不到时渲染成一张错误卡片，而不是一个悄悄空白的组件**——这条和"绝不静默失败"是同一类取舍。
- **显示货币统一在后端换算一次**：provider 永远报美元，看板声明 `currency`（146 个代码）后按实时汇率换算；美国宏观序列（国债收益率、CPI、国债总额）**故意保持美元**。
- **声明与渲染分离**：背景、provider 都由 spec 声明、由宿主渲染，重的那部分（动画引擎、图表库）不进 spec，也不进那条"不依赖 React"的工具链。

**frame 规模**：README 称内置 **284 个 frame、分 14 类**（行情、链上、衍生品、宏观利率、金属大宗、情绪新闻、持仓、决策日志、布局媒体、甚至小游戏）。README 自己也承认**权威清单只能以 `zframes catalogue` 的实时输出为准**，不手抄表格。

**数据源**：29 个免费无鉴权 provider（Hyperliquid、Nasdaq、CoinGecko、DeFiLlama、Deribit、Cboe、mempool.space、美国财政部、纽约联储、BLS、SEC EDGAR、FRED、Zillow、FHFA 等）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zentryHQ/zframes](https://github.com/zentryHQ/zframes) |
| 描述 | **仓库无描述**（GitHub API 返回空） |
| 许可证 | MIT（Copyright 2026 Zentry；`NOTICE` 列出第三方组件 liveline、d3、Unicorn Studio 引擎） |
| 语言 / 依赖 | TypeScript（strict）、React、Vite、pnpm；CLI 已发布到 npm |
| 关注度 | **68 Star、14 Fork、0 关注、11 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-06-20 创建，最近推送 2026-11-01；最新 Tag `v0.18.0`（另有 v0.17.0、v0.16.3） |
| 体积 | 约 35,347 KB（`size` 字段，约 34 MB） |

## 用法要点

安装与使用都围绕"skill"展开，而不是自己建 Node 工程：

```bash
npx skills add zentryhq/zframes
```

它会把仓库里的 [`skills/zframes/SKILL.md`](https://github.com/zentryHQ/zframes) 拉进助手的 skills 目录（Claude Code 是 `~/.claude/skills/`）。此后：

```
/zframes build me a TSLA + NVDA terminal with funding and fear-greed
  → agent reads the frame catalogue  (zframes catalogue)
  → agent writes dashboard.json and lints it  (zframes lint)
  → agent serves it live and opens the browser  (zframes serve)
```

CLI 自己也能手动跑：

```bash
pnpm zframes init [name|dir]        # 写一个合法的空信封给 Agent 填
pnpm zframes catalogue              # frame 目录导出为 JSON Schema
pnpm zframes lint <name|file>       # 校验，退出码 1 + 逐帧可读错误
pnpm zframes snapshot [name|file]   # 取 spec 里符号的无鉴权行情快照（JSON）
pnpm zframes serve [name|file]      # 在 :37263 起一个可实时编辑的看板
```

想改框架本身才需要从源码跑（`pnpm install` + `pnpm dev`）。CLI 已发布到 npm，所以助手的 `npx zframes serve` **每次运行都会拉一遍完整运行时**，不用 clone。

README 也交代了**支持范围**：Claude Code 是"已端到端测试"的一等支持；Cursor、Gemini CLI、Codex 标"兼容（遵循 skills 标准）"；其他遵循该标准的助手标"应该能用"。

## 使用前需要注意

- **"无鉴权"不等于"不依赖第三方"**：29 个 provider 全是不需要 key 的公开源，但**它们随时可能限流、改版或下线**。README 对此有部分防护——FX 那条链是四个上游串成一条能力，"死一个就顺延而不是整块挂掉"；但**其余 provider 没有同等冗余**，某个源挂了对应帧就只能变错误卡片。
- **默认的"美股行情"是永续合约，不是股票本身**：README 说股票是主用例，走的是 **Hyperliquid HIP-3 的股票永续（`xyz:TSLA`、`xyz:NVDA`、`km:US500`）**，与真实股票报价**不是同一个东西**（衍生品价格、有资金费、与标的可能有价差）。要真实合并行情（consolidated tape），需要**显式把帧钉到 `source: "nasdaq"`**。**如果你把它当成"看真实股价"，可能会看错口径。**
- **官方美国数据要靠同源代理**：Treasury、NY Fed、OFR、BLS、FINRA、SEC、FRED、FHFA、Cboe 都是无鉴权但**被浏览器 CORS 拦住**，运行时通过一个**同源白名单代理**转发。Zillow 是例外（它自己发 `Access-Control-Allow-Origin: *`），所以能在纯静态托管上直连。**这意味着本地跑 `zframes serve` 时，这些请求会先到你自己的本机代理**，而不是直连数据源。
- **存在一套"带 key 的分层"，但没接进已发布的 CLI**：连接 Binance 账号和公开链上钱包地址的 `portfolio` 能力存在，README 明确说**它与无鉴权集分开、且没有接进已发布的 CLI**。也就是说你看 npm 上的 CLI 时，这部分是拿不到的。
- **项目很新、关注度低**：68 Star、14 Fork、**0 关注**，最早提交 2026-06-20。tags 已到 `v0.18.0`，**版本号仍在小数第一位，属早期高速迭代**，接口与配置可能变。
- **仓库无描述**：GitHub 上连一句简介都没填，README 的营销口径（"29 帧一次生成""全部流式无鉴权数据"）**属作者自述，我未运行验证**；284 个 frame 的实际可用比例、各 provider 的真实可用性、`lint` 报错的可读程度都未验证。
- **`npx` 每次拉全量运行时**：README 把它当优点（不用 clone/安装），代价是**每次启动都依赖 npm 可用**，并且你运行的是一份会随发布变化的运行时。
- **我只读了 README 与元数据**：未安装 skill、未跑 CLI、未生成过任何看板。

## 什么时候会再看

- 需要**给桌面做一个自用的行情/数据汇总看板**，又不想写前端时：这条"描述 → 生成 JSON → 校验 → 本地起服务"的链路是现成的。
- 想参考**"把 AI 生成约束成可校验契约"**时：**schema 双用（运行时校验 + AI 文档）+ 逐帧可读报错 + 覆盖不到就报错卡片**，这三件套可以直接搬到任何"让 AI 产出配置"的场景。
- 想参考**"声明与渲染分离"**时：spec 只做声明（背景、provider、货币），重逻辑全部留在宿主，保持工具链不依赖 React。
- 研究 **无鉴权公开数据源的聚合与降级**时：29 个 provider 的清单、FX 四上游降级链、同源代理处理 CORS，都是可借鉴的样本。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"AI 生成应用/产物"的路线对照**：[deepseek-harness](deepseek-harness-DeepSeek开源Agent框架.md)、[openclaw](openclaw-自托管个人AI助手.md)——那些是通用 Agent 框架/助手，本篇是**收敛到单一产物（看板 spec）**的窄场景实现，可对照"通用 vs 专用契约"的差别。
- **skill 分发方式同类**：[dsh-plugins-store](dsh-plugins-store-DSH插件市场.md)、[agents.md](agents.md-给编码助手用的AGENTS文件格式.md)——本篇的 `npx skills add` 依赖 skills 标准，和 AGENTS.md 一样属于"给助手喂上下文/能力"的约定层。
- **金融数据与看板类**：[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)（研报工作台）、[US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md)（风险面板）、[global-stock-data](global-stock-data-美股与全球市场数据工具包.md)（美股取数）、[wealthfolio](wealthfolio-本地优先个人财务追踪器.md)——可对照"**取数**（数据源）与**呈现**（看板/面板）"的分工；zframes 的价值主要在呈现层与 AI 契约，取数依赖外部免费源。

## 参考来源

- 项目仓库与 README：https://github.com/zentryHQ/zframes
- npm 包：https://www.npmjs.com/package/zframes
- skills 标准（README 引用）：https://github.com/obra/skills
- 元数据（Star / Fork / 时间 / 许可证 / Tag）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README 与元数据；未安装 skill、未运行 CLI、未生成看板。284 个 frame、29 个 provider 的可用性，以及"股票永续"与真实股票报价的差异影响，均以 README 自述为准，未独立验证。本笔记不构成任何投资建议。
