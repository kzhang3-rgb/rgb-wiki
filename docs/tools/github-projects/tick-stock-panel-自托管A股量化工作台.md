# tick-stock-panel（TSP）：自托管的 A 股量化工作台

> 值得记的不是"功能多"，而是两件反差：① 它**主动划出"不做什么"**——"不对标同花顺 / 通达信""不内置 AI 荐股 / 涨停预测"；② README 写着"**严禁商业用途**"，但仓库 LICENSE 是**标准 MIT**（MIT 本身允许商用）——**声明与许可证打架**，用之前要自己判断。

- 状态：草稿
- 最近核验：2026-11-02（阅读仓库 README、LICENSE 与 GitHub API 元数据，未安装、未运行、未接数据源）
- 适用范围：Python ≥ 3.11、Node ≥ 20、`uv`、`pnpm`；Docker 单容器部署；只做 A 股

## 核心结论

一个**自托管的 A 股量化工作台**，把"选股 + 监控 + 回测"放在同一个本地 Web 界面里（默认后端 `localhost:3018`）。功能面很宽：选股引擎、指标流水线、回测、因子挖掘、市场环境判定、异动监控、监控中心、个股分析、连板梯队、数据源扩展。

技术选型偏"现代数据栈"：**Polars 做计算、DuckDB 做查询、Parquet 做存储**，后端 FastAPI（含 SSE 流式推送），前端 React 18 + Vite + TypeScript，Docker 两阶段构建成**单容器**。

它是**个人开源项目**（作者自称"非隶属任何官方项目"），README 直接写"小白请绕路""谨作为本地量化提供解决思路与方案，**不作为投资软件或者看盘软件**"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [shy3130/tick-stock-panel](https://github.com/shy3130/tick-stock-panel) |
| 项目主页 | <https://tsp.shy313.com> |
| 许可证 | **LICENSE 文件为标准 MIT**；但 README 声明"仅供学习研究使用，**严禁商业用途**"——**与 MIT 冲突**，见下 |
| 语言与技术栈 | Python（FastAPI / Pydantic v2 / APScheduler / sse-starlette）+ TypeScript（React 18 / Vite / Tailwind）；计算 Polars、查询 DuckDB、存储 Parquet |
| 关注度 | **6,388 Star、1,517 Fork、37 人关注、36 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-06-18 创建，最近推送 2026-10-29；README 路线图自称推进到 **v0.2** |
| 体积 | 约 41,428 KB（`size` 字段，含 `screenshots/` 多张截图） |
| 数据源 | TickFlow 官方 SDK、fuyao（同花顺 REST）、插件化的 stock-sdk、YAML 自定义源 |

> 口径说明：开放 Issue 的 API 计数包含开放中的 PR（本 wiki 此前已记录过这个口径差异），**36 不代表纯问题数**。Star / Fork 数会随时间变化。

## 功能范围（README 自述）

README 用表格列了 11 个模块，覆盖范围如下：

| 模块 | 一句话 |
| --- | --- |
| 能力路由 | 日 K / 除权 / 实时 / 分钟 / 盘口 / 财务等多数据集**按数据源能力独立路由**，任选组合 |
| 选股引擎 | 25 个内置策略 + 分钟策略 + 自定义信号 + AI 生成，用 Polars 扫全 A 股 |
| 指标流水线 | MA/EMA/MACD/RSI/KDJ/布林/量比等 **68 列**指标与信号，落盘 enriched Parquet |
| 回测研究 | 因子 / 策略 / 分钟回测，含 T+1、费用、滑点约束，结果可导出 |
| 因子挖掘 | 嵌套样本外搜索多因子排名组合；**候选库显式发布、永不自动上线** |
| 市场环境 | 情绪周期 6 阶段（连板梯队驱动）+ 概念/行业主线排名 |
| 异动监控 | 竞价 / 盘中 / 偏移三类异动；偏移按交易所口径（主板 3 日 ±20% 等） |
| 监控中心 | 策略 / 个股信号 / 价格 / 异动四类规则，多条件 AND/OR + 语音播报 + 飞书推送 |
| 个股分析 | 9 类关键价位 + AI 四维分析（技术 / 基本面 / 财务 / 消息面） |
| 连板梯队 | 连板层级统计 + 概念涨幅轮动 + 盘后 AI 复盘 + 炸板/翘板预警 |
| 数据扩展 | 数据源插件化，扩展字段可配成一级页面同台分析 |

## 几个值得记的工程机制

README 在"关键机制"里写了几条口径，这部分比功能清单更有参考价值：

- **能力路由 fail-closed**：每个数据集按数据源**声明**的能力路由，注册表集中定义；插件源若声明了 `pct_unit` 却没有实现，**直接拒绝**（fail-closed），而不是猜一个默认值。同一数据集可随时换源，指标与回测口径不变。
- **财务多源合并按 PIT**：按 `(symbol, period_end)` 累积，多源取并集、逐列**按公告日取最新**（Point-in-Time）；**公告前一律空值，绝不填 0**。——这和已收录的 [US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md)、[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md) 是同一条原则：**缺失不等于 0**。
- **交易日探针**：fuyao 交易日历（含调休）→ tickflow 行情时间戳探针 → 工作日兜底；节假日自动停掉实时轮询与分钟增量，减少无效请求。
- **回测执行隔离**：回测跑在 spawn 出的子进程，持久 run ID，刷新 / 切页重连不丢任务。
- **分层缓存**：存储只留 15 列基础数据，读取时**现算** 68 列指标与信号，再加进程内快照缓存。

## 快速开始（README 摘要）

两条路，**前置依赖**：Python ≥ 3.11、Node ≥ 20、`uv`、`pnpm`。

```bash
# 方式 A：Dev 模式（二次开发推荐）
cp .env.example .env       # 按需填 TICKFLOW_API_KEY（留空 = None 模式）
./dev.sh                   # Windows: .\dev.ps1

# 方式 B：Docker（部署最省心）
cp .env.example .env
docker compose up --build
# 打开 http://localhost:3018
```

最常用的三项配置：`TICKFLOW_API_KEY`（留空 = None 模式，历史日 K 免费）、`AI_API_KEY`（留空 = 关闭 AI）、`PORT`（默认 3018）。README 说明"None / Free 档走 free-api，当日数据**盘后 1-2 小时**可用"。

## 使用前需要注意

- **许可证与声明冲突**：LICENSE 文件是**标准 MIT 原文**（未加限制条款），而 README 写"**严禁商业用途**"。**MIT 本身并不禁止商用**，README 的这句限制在 MIT 下缺乏法律约束力。若打算商用，请以 LICENSE 为准并自行评估风险；若只是学习研究，两者不冲突。这类"声明与许可证不一致"的情况，本 wiki 此前在 [prompt-optimizer](prompt-optimizer-提示词优化器.md)、[llm_wiki](llm_wiki-会自己长大的个人知识库桌面应用.md) 上也遇到过。
- **Docker 模式会给容器读本机 Codex 登录凭据的权限**：镜像内**内置固定版本的 Codex CLI**，Compose 把主机的 `${HOME}/.codex` **只读挂载**进容器。README 自己提示"**仅应在受信任的本机环境启用**"。**这是把本机 AI 凭据的可见面扩大了**，多用户或不受控机器上不要这么跑。
- **Windows 下 Docker 模式容易挂载失败**：纯 PowerShell / CMD 里 `HOME` 通常未设置，需要在 `.env` 显式指定 `CODEX_HOME_HOST=C:\Users\你的用户名\.codex`，否则容器读不到登录态。
- **数据源链接带邀请参数**：README 里的 TickFlow 注册地址带 `?ref=...` 邀请码（作者可获返利）。**这不是官方中立推荐，选择数据源时按需自行比较。**
- **数据源依赖第三方且部分是"非官方口径"**：fuyao 走**同花顺 REST**，TickFlow 为商业数据服务，均有限流、改版与合规风险；A 股行情接口本身也常变更。
- **AI 功能是"可选 + 自带 Key"**：需自配 OpenAI 兼容接口（DeepSeek / 通义 / Ollama 等）。**一旦启用，个股与持仓相关上下文会发往你配置的模型端点**，注意数据出境与合规。
- **它明确不预测**：项目自述"不内置 AI 荐股 / 涨停预测""不对标同花顺 / 通达信"，定位是"本地量化的解决思路与方案"。**回测表现不等于实盘收益**，任何策略结论都需自行验证。
- **作者定位就是"劝退新手"**：README 首段"⚠️ 小白请绕路"。这不是面向普通用户的开箱即用看盘工具。
- **我只读了 README、LICENSE 与 API 元数据**：未安装、未跑过任何策略与回测；**上面所有功能与机制描述均引自 README 自述，未独立验证**。

## 什么时候会再看

- 需要一套**自托管、数据留在本机**的 A 股"选股 + 监控 + 回测"工作台，且接受一定部署门槛时。
- 想参考**"多数据源按能力路由 + fail-closed"**怎么落地时——这比"支持 N 个数据源"的口号更可执行。
- 想参考**"缺失值 / PIT 口径"怎么处理**时：公告前一律空值、绝不填 0，可与 [Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)、[US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md) 的做法对照。
- 想研究**"AI 生成策略但候选永不自动上线"**这种人工闸门设计时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同类 A 股选股/工作台对照**：[KHunter](KHunter-A股量化选股系统.md)（更轻的形态选股系统，Flask + SQLite）、[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)（更偏"研究过程可追溯"的投研工作台）、[easy_investment_Agent_crewai](easy_investment_Agent_crewai-A股多Agent分析平台.md)（CrewAI 多角色分析）——可对照"轻量单机 / 研究可追溯 / 多 Agent 分工 / 大数据栈工作台"四种路线。
- **取数与依赖对照**：[a-stock-data](a-stock-data-A股全栈数据工具包.md)（把多数据源封成单个 Skill 文件）、[cli-anything-tdx-tq](cli-anything-tdx-tq-通达信TQ命令行工具.md)（本机通达信客户端 SDK）、[stock-tui](stock-tui-终端里看A股K线与基本面.md)（AKShare 终端查询）——本篇走的是"**商业数据服务 + 插件化自定义源**"路线。
- **回测与指标**：[indicator](indicator-Go技术指标与回测框架.md)、[ai_quant_trade](ai_quant_trade-AI量化交易操盘手.md)。
- **"数据缺失不填 0"的同类实践**：[US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md)、[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)。

## 参考来源

- 项目仓库与 README：https://github.com/shy3130/tick-stock-panel
- 许可证文件：https://github.com/shy3130/tick-stock-panel/blob/main/LICENSE
- 项目主页：https://tsp.shy313.com
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02，Star 数与仓库内容可能变化。仅阅读 README、LICENSE 与元数据；未安装、未接数据源、未运行策略或回测。功能与机制描述均引自 README 自述，未独立验证。本笔记不构成任何投资建议。
