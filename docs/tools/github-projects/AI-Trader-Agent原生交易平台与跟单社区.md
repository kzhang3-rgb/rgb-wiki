# AI-Trader：HKUDS 的 Agent 原生交易平台与跟单社区

> 记录一个把"**AI Agent 当成交易者**"的平台（`HKUDS/AI-Trader`，描述 `AI-Trader: 100% Fully-Automated Agent-Native Trading`）：**接入方式是给 Agent 发一句话**——"`Read https://ai4trade.ai/SKILL.md and register.`"，Agent 自己读接入指南、装组件、注册，然后就能**发布交易信号 / 策略、参与讨论、一键跟单、跨券商同步信号、赚积分**；仓库同时是**这个在线平台（ai4trade.ai）的开源代码**（FastAPI 后端 + React 前端 + `skills/` 里的 Agent 技能 + `docs/api/` 的 OpenAPI）与**一份研究数据管线**（把平台数据做成论文用的可复现数据集与图表）。三条先记住：**它是"平台 + 托管服务"而非纯本地工具**（真跑在 ai4trade.ai 上）；**仓库里没有 LICENSE 文件**（README 徽章却标 MIT）；**代码自 2026-06-11 起未再推送**（约五个月），但 Star 仍在涨。未注册、未接入任何 Agent、未交易。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（209 行）、`README_ZH.md`（180 行）、`docs/README_AGENT.md`（258 行）、`docs/README_USER.md`、`research/README.md`、`service/README.md`、`.env.example`、文件清单（149 项）与 ungh 镜像 / shields 徽章；**未 clone、未注册平台、未发消息给任何 Agent、未安装 skill、未接任何券商或行情源、未跑 `research/` 脚本、未审计源码、未在 ai4trade.ai 上做任何操作**）
- 适用范围：**在线平台**（Dashboard <https://ai4trade.ai>、API 文档 <https://api.ai4trade.ai/docs>）+ **自托管源码**（`service/server` FastAPI 后端、`service/frontend` React 前端）；接入方为**任意 AI Agent**（README 点名 OpenClaw / nanobot / Claude Code / Codex / Cursor 等）与**人类交易者**（邮箱注册）

## 核心结论

**一句话定位（README）**：AI-Trader 是一个 **Agent-Native Trading Platform**——"就像人类有交易平台，**AI Agent 也需要属于自己的**"；Agent 在其中**交流观点、互相跟单、磨合交易能力**。

**① "加入"就是给 Agent 发一句话。** README 开篇：把下面这句发给 Agent 即可——

```
Read https://ai4trade.ai/SKILL.md and register.
```

Agent 会**自动**读接入指南、装必要组件、在平台注册。**这句话的机制值得注意**：它让 Agent 去**抓取并执行远端服务器上的指令**（`https://ai4trade.ai/skill/ai4trade` 等 skill 文件在运行时从服务器拉取），等于把"接入权"交给平台侧的内容。

**② 平台侧：信号 marketplace + 一键跟单 + 三类信号。** README 特性列表：**群体智能交易**（多 Agent 协作、辩论）、**跨平台信号同步**（保留你自己的券商，把交易同步到平台）、**一键跟单**（实时镜像顶尖交易者的仓位）、**通用市场接入**（股票 / 加密货币 / 外汇 / 期权 / 期货）、**三类信号体系**（`strategy` 策略用于讨论、`operation` 操作用于跟单、`discussion` 讨论用于协作）、**积分激励**（发布信号 +10 分、被跟单每采纳 +1 分）。`README_USER.md` 另述：新用户送 **100 积分**，**10 万美元模拟盘**练习，跟单与关注**免费**。

**③ 仓库结构（README "Architecture"）**：`skills/`（Agent 技能定义）· `docs/api/`（OpenAPI 规范）· `service/server`（FastAPI 后端）· `service/frontend`（React 前端）· `assets/`；文件清单里另有一整个 **`research/`** 子树。

**④ `research/` 是"论文管线"，不是产品功能。** `research/README.md` 自述：把平台数据变成**可复现的论文数据集 / 指标 / 统计表 / 图**，服务于"**4k+ Agent 竞争与合作研究**"；脚本含 `export_research_dataset.py`（导出匿名化数据集）、`analyze_experiments.py`（A/B、DiD、回归、HTE、bootstrap CI、FDR）、`generate_figures.py`（八张论文图）。**默认匿名化**（Agent 名哈希、元数据抹除 token / email / wallet / password / secret / session / auth 字段）。**这说明它同时是一个学术研究平台**（HKUDS = 港大数据智能实验室）。

**⑤ 关注度与状态（ungh 快照 2026-11-19）。** **23,321 Star / 3,513 Fork / 190 关注**（shields 同期 23k / 3.5k）；**48 个开放 Issue、贡献者 20 人**（shields）；**2025-10-23 创建**、`pushedAt` **2026-06-11**、`updatedAt` **2026-11-18**；`main`；**0 Release**；**149 个文件**；**shields 顶层语言 72.6%**。

**⑥ ⚠️ 仓库里没有 LICENSE 文件。** 我对 `main` 与 `master` 两个分支的 `LICENSE` 与 `LICENSE.md` 均取到 **404**，文件清单（149 项）里也**没有任何许可文件**；但 `README.md` / `README_ZH.md` 顶部挂着一枚 **"License: MIT" 徽章**（该徽章链接指向的 `LICENSE` 文件并不存在），shields 则报 **`not specified`**。**"徽章说 MIT" ≠ "仓库授权 MIT"——在授权不明前不要把其中的代码当 MIT 使用。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [HKUDS/AI-Trader](https://github.com/HKUDS/AI-Trader) |
| 描述（GitHub） | `AI-Trader: 100% Fully-Automated Agent-Native Trading` |
| 组织 | **HKUDS**（香港大学数据智能实验室；README 顶部挂 Feishu / WeChat 群） |
| 在线平台 / API | Dashboard <https://ai4trade.ai>（含 `/financial-events` 看板）· API 文档 <https://api.ai4trade.ai/docs> |
| 许可证 | **⚠️ 仓库内无 `LICENSE` 文件**（main / master 的 `LICENSE`、`LICENSE.md` 均 404；文件清单 149 项无许可文件）；README 徽章标 **MIT** 并链接不存在的 `LICENSE`；**shields 报 `not specified`** |
| 主语言 | shields 顶层语言 **72.6%**（前端为 React + TypeScript，后端为 Python + FastAPI） |
| 关注度 | **23,321 Star / 3,513 Fork / 190 关注**（ungh，2026-11-19；shields 同期 23k / 3.5k）；**48 个开放 Issue、贡献者 20 人**（shields） |
| 创建 / 最近推送 | **2025-10-23 创建**；`pushedAt` **2026-06-11**、`updatedAt` 2026-11-18（**注意：约五个月无代码推送**） |
| 默认分支 / Release / 文件数 | `main`；**0 Release**；**149 个文件** |
| 根目录可见文件 | `README.md`（8,656 B）、`README_ZH.md`（7,368 B）、`.env.example`、`.gitignore`、`package.json`、`tsconfig.json`、`assets/logo.png`（693 KB） |
| 主要目录 | `skills/`（6 个 SKILL：`ai4trade` / `copytrade` / `tradesync` / `heartbeat` / `market-intel` / `polymarket`）· `docs/api/`（`openapi.yaml` 13.5 KB、`copytrade.yaml` 11.4 KB）· `docs/README_AGENT.md`（+ `_ZH`）· `service/server/`（FastAPI）· `service/frontend/`（React）· `research/`（schema + 脚本 + 实验日志 37 KB） |
| 兄弟项目 | [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading)（README 自述为同组织的伙伴项目） |
| 自托管 | 复制 `.env.example` 为 `.env`，二选一：**PostgreSQL**（设 `DATABASE_URL`）或 **SQLite**（留空 `DATABASE_URL`，用 `DB_PATH`，默认落 `service/server/data/clawtrader.db`） |

## 平台机制（据 README / 文档自述）

**1. 两条接入路径。**
- **Agent 交易者**：发那句"读 SKILL.md 并注册"→ Agent 自动注册。`README_AGENT.md` 给出注册调用：`POST https://api.ai4trade.ai/api/claw/agents/selfRegister`，body `{"name": "...", "email": "..."}`，返回 **`token`（`claw_` 前缀）+ `botUserId` + 100 积分**；此后所有调用带 `Authorization: Bearer claw_xxx`。
- **人类交易者**：访问 <https://ai4trade.ai> → 邮箱注册 → 浏览信号或跟随。

**2. 三种消息类型与积分。**

| 类型 | 端点（示例） | 说明 | 积分 |
| --- | --- | --- | --- |
| `strategy` 策略 | `POST /api/signals/strategy` | 发布投资策略供讨论 | +10 |
| `operation` 操作 | `POST /api/signals/realtime` | 实时买卖动作（`buy` / `sell` / `short` / `cover`），供跟单者**即时执行** | +10 |
| `discussion` 讨论 | `POST /api/signals/discussion` | 自由讨论 | +10 |

- 市场字段 `market`：`us-stock` / `a-stock` / `crypto` / `polymarket`；操作需带 `executed_at`（ISO 8601）。
- 浏览：`GET /api/signals/feed`（可按 `message_type` / `market` / `keyword` 过滤）。
- 实时通知：WebSocket `wss://ai4trade.ai/ws/notify/{client_id}`（通知类型含 `new_reply` / `new_follower` / `signal_broadcast` / `copy_trade_signal`）；或**拉模式** `POST /api/claw/agents/heartbeat`。

**3. 六大 Agent 技能（`skills/` 各自一份 `SKILL.md`）。** `ai4trade`（主入口 + 共享 API 参考）· `copytrade`（跟单·跟随者）· `tradesync`（交易同步·信号提供者）· `heartbeat`（心跳与实时通知）· `market-intel`（市场情报）· `polymarket`（Polymarket 公开数据：问题 / 结果 / token ID，文档明确"**不要把 Polymarket 市场发现流量走 AI-Trader**"）。

**4. 数据源与行情（`.env.example` 可见）。** 美股价格 **Alpha Vantage** 优先、缺省时**回退 yfinance**（README 2026-06-08 更新）；另有 **Adanos**（社媒 / 新闻 / 预测市场情绪）、**Hyperliquid**、**Polymarket**（Gamma / CLOB）等端点；后台任务含仓位刷新、价格抓取、Polymarket 结算（`POLYMARKET_SETTLE_INTERVAL=60`）、市场新闻 / 宏观 / ETF 流量 / 个股分析刷新等。

**5. 一次改名留下的痕迹（未完全核实）。** `.env.example` 用 `CLAWTRADER_*` 变量、SQLite 默认库名 `clawtrader.db`，Agent 注册端点带 **`/api/claw/`** 前缀，`README_AGENT.md` 的手动安装段仍写 `git clone https://github.com/TianYuFan0504/ClawTrader.git`——**项目疑似曾名 "ClawTrader"**。（我未核对改名时间线与两个仓库的关系。）

## 使用前需要注意

- **⚠️ 授权不明（最需要先确认的一条）**：**仓库内没有任何 `LICENSE` 文件**，但 README 徽章标 MIT 并指向不存在的 `LICENSE`。**在作者给出明确许可前，不要假定其中代码可按 MIT 复用 / 分发 / 商用。**
- **⚠️ 它是"平台 + 托管服务"，不只是本地代码**。真正的运行体是 <https://ai4trade.ai> 与 <https://api.ai4trade.ai>；仓库里的源码是它的（部分）实现与 Agent 技能。**平台侧的条款、数据政策、可用性不在本笔记所读内容里**。
- **⚠️ `service/README.md` 与其仓库现状矛盾**：该文件自述这是"**private / proprietary server implementation**"（私有 / 专有实现），但**仓库里确实包含完整的 `service/server/**` 代码**（FastAPI 路由、任务、测试等）。**"专有"与"公开代码"并存**——须自行判断这段声明的含义（可能指线上生产版本或历史遗留）。
- **⚠️ Agent 接入 = 让 Agent 执行远端指令**：那句"读 `https://ai4trade.ai/SKILL.md` 并注册"会让 Agent **在运行时从平台服务器拉取并执行指令**（`curl https://ai4trade.ai/skill/...`）。**这本质上是把一部分行为控制权交给平台侧内容**——接入前请自行评估（对照本 wiki 里关于"技能 / 提示词注入"的记录）。
- **⚠️ 涉及真实资金与真实市场，风险自负**：定位是"100% 全自动交易"，涉及跟单与跨券商同步，README 点名兼容 **Binance / Coinbase / Interactive Brokers** 等；同时提供 **10 万美元模拟盘** 与 **Polymarket 模拟执行**（真实行情 + 模拟成交）。**"跟单是否会替你下真实订单"在本次所读文档里没有一句话讲清**——这是**未核实**的关键点，勿凭"纸面交易"的印象就认为零风险。**本笔记不构成任何投资建议。**
- **⚠️ 代码停更快五个月，但热度仍在涨**：`pushedAt` 停在 **2026-06-11**（README"最新更新"最后一条也正是 2026-06-11），而 `updatedAt` 为 2026-11-18。**Star 在涨 ≠ 代码在更新**；引用能力 / 状态时请以仓库页与平台实际为准。
- **⚠️ 0 Release、无版本标签**：仓库没有任何 GitHub Release，**只能按提交 / 分支引用**。
- **⚠️ 我未做任何验证**：仅阅读上述文档、`.env.example`、文件清单与元数据；**未 clone、未注册、未发消息给 Agent、未安装 skill、未接券商 / 行情源、未跑 `research/` 脚本、未打开 ai4trade.ai 做任何操作、未审计源码**；平台能力、积分规则、跟单是否触达真实资金、研究汇总口径**均来自文档自述**。

## 什么时候会再看

- 想研究"**让 AI Agent 成为市场参与者**"这件事的**平台形态**：Agent 如何注册、如何发信号、如何互相跟单、如何用积分激励（而不是只做一个单机策略脚本）时。
- 想参考"**把 Agent 接入做成一句自然语言 + 一份远端 SKILL.md**"这种极低门槛的接入设计，以及它的**安全含义**时。
- 想研究"**Agent 交易平台同时又是学术研究平台**"的作法：`research/` 如何把线上数据做成匿名化、可复现的论文数据集与图（A/B、DiD、HTE、FDR）时。
- 想对照不同"AI + 交易"形态（见下方互链）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"用 AI Agent 做投资 / 交易"，一个单机、一个联网平台**：[ai-hedge-fund：把 AI 对冲基金做成可长期运行的终端系统](ai-hedge-fund-可回测与模拟盘的AI对冲基金引擎.md)——那篇是**单机基金引擎**（三层可插拔、`advance` 一个动词跑回测 / 模拟盘 / 实盘、哈希链账本），自述**不做真实交易、仅教育用途**；本篇是**联网平台**（Agent 注册、发信号、互相跟单、积分激励 + 论文研究管线）。可对照"**个人基金引擎的工程纪律**"与"**多 Agent 交易社区的平台设计**"。
- **都涉及"让 AI 下单"的能力与闸门，形态相反**：[QuantDinger：开源的 AI 交易操作系统](QuantDinger-开源AI交易操作系统.md)——那篇是**自托管**（凭据不出本机、local-first、Apache-2.0、`token scope + paper_only + 服务端开关 + 限额白名单` 四重闸门才能实盘）；本篇是**托管平台 + 公开代码**（Agent 一句话接入）。可对照"**本地掌控**"与"**平台托管**"两种边界，以及各自对"**Agent 能否下真实单**"的处理。
- **同为"交易系统把控制面 / 运行面分开"**：[weclaws：多用户微信 AI 智能体控制台](weclaws-多用户微信AI智能体控制台.md)——那篇的分界线是"网页只写意图、supervisor 拥有运行时"；本篇 README 2026-04-10 更新也提到"**FastAPI Web 服务与后台 worker 拆分运行**"。可对照同一条架构纪律在不同领域的落地。
- **同为"AI 交易"，一个是联网平台、一个是桌面受控执行**：[ai-trader-for-mt4：把 LLM 接到 MT4 的"受控执行"交易框架](ai-trader-for-mt4-把LLM接到MT4的受控交易框架.md)——那篇是**单机桌面框架**（MT4 文件桥 + 两级工具 + 提示词硬规则，**只读 / 计划 / 执行三分离**，明令仅用模拟账户）；本篇是**托管平台**（Agent 一句话接入、发信号互相跟单）。可对照"**把权限收紧到工具与规则里**"与"**把 Agent 放进一个交易社区**"。
- **同为"多 Agent 做交易"，一个是研究框架、一个是联网平台**：[TradingAgents：把"交易公司"搬进多 Agent 的 LLM 金融交易框架](TradingAgents-把交易公司搬进多Agent的LLM金融交易框架.md)——那篇是**可本地 / 离线运行的开源研究框架**（Apache-2.0、多 Agent 角色分工与辩论、防前视数据管线、模拟交易所执行）；本篇是**托管平台 / 跟单社区**（**仓库内却无 LICENSE**）。可对照"**在一台机器上模拟一家交易公司**"与"**把 Agent 放进社区互相跟单**"。

## 参考来源

- 仓库主页与 `README.md`（209 行 / 8,656 字节；核验日期 2026-11-19）：https://github.com/HKUDS/AI-Trader
- 中文总览 `README_ZH.md`（180 行 / 7,368 字节）· Agent 接入指南 `docs/README_AGENT.md`（258 行）· 用户指南 `docs/README_USER.md` · 研究管线 `research/README.md` · 服务端说明 `service/README.md`
- 环境变量样例 `.env.example`（数据库、行情源与后台任务配置）
- 在线平台 / API：<https://ai4trade.ai> · <https://api.ai4trade.ai/docs>
- 兄弟项目：<https://github.com/HKUDS/Vibe-Trading>
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-19

以上信息核验于 2026-11-19：仓库内容与 Star 数可能变化。**未 clone、未注册平台、未发消息给任何 Agent、未安装 skill、未接任何券商或行情源、未跑 `research/` 脚本、未审计源码**；**平台机制、积分规则、跟单是否触达真实资金、授权与"专有实现"声明均为文档自述，本笔记未独立验证，也未就许可或投资的合规性咨询任何专业意见。**
