# ai-trader-for-mt4：把 LLM 接到 MT4 的"受控执行"交易框架

> 记录一个**面向 MetaTrader 4（MT4）的 AI 辅助交易桌面应用**（`toki-plus/ai-trader-for-mt4`，README 自述"面向 MetaTrader 4 的 AI 辅助交易研究与**受控执行**框架"）。它的立意与"把交易权限交给大模型"相反：**把交易能力拆成结构化工具，用流程、状态管理、确定性校验把模型的执行边界框住**——只读分析、候选计划、实际执行三者分开；模型走 **`Prepare -> Scan -> Act -> Conclude`** 的标准循环，每一步都基于工具返回的结构化报告；仓位 / 止损 / 重复订单 / 订单状态由**确定性规则**检查。技术上最值得记的是**MT4–Python 的"文本文件桥"**：Python 与 EA（`.mq4`）在 `MQL4/Files` 目录下**读写 `.txt` 文件**通信（生产者–消费者、异步解耦），**刻意避开 DLL 调用的脆弱性**。规模很小（**30 Star、贡献者 1、单条 Release、51 个文件**），**仅 Windows + MT4**；README 有**加粗的免责声明**："**强烈建议仅在模拟账户运行，切勿在真实资金账户使用**"。未克隆、未运行、未接 MT4。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（205 行中文版）与 `README_en.md`、`LICENSE`（MIT）、文件清单（51 项）、目录与模块说明，及 ungh 镜像 / shields 徽章；**未 clone、未下载 Release 压缩包、未安装 MT4 / EA、未运行 `main.py`、未配置任何 LLM API Key、未打开 GUI、未跑集成测试、未审计源码**）
- 适用范围：**仅 Windows**（README：MT4 主要为 Windows 平台）+ **MetaTrader 4 终端** + **Python 3.8+**（开发者方式）；外汇 / CFD 等 MT4 可交易的品种；需至少一个受支持的 LLM API Key（**OpenAI / DeepSeek / Moonshot**），**Jina API Key 可选**（新闻检索用）

## 核心结论

**1. 定位：不是"让 AI 自由交易"，而是"给 AI 划定工具与风险边界"。** README"项目背景"明确列出"直接把行情丢给大模型生成指令"的四个问题：① 模型可能缺账户 / 持仓 / 市场状态；② 自然语言输出难以稳定映射为执行动作；③ **风险规则可能在多轮分析中被忽略**；④ 订单状态 / 异常 / 历史决策缺少可追踪记录。应对是**把交易能力拆成结构化工具 + 用流程与状态机 + 确定性校验约束执行**。

**2. Agent 工作流：类 ReAct 的四段循环 + 提示词里的硬规则。** AI 按 **`Prepare -> Scan -> Act -> Conclude`** 循环，每步基于工具的结构化报告而非凭空推断；`src/app/prompts.py`（**25,924 B**）定义系统级指令集——**单笔与组合风险上限、持仓数量限制、信号分级（A/B/C 与"无交易"区间）、不同形态的入场细则、分批止盈（TP1–TP8 利润阶梯）与移动止损**。还引入 **相对价差成本（Relative Spread Cost）**：让 AI 按不同资产类别的价差百分比评估流动性、避开高成本品种。**每一步的 Thought / Tool Calls / Observation 都记录并在 GUI 中展示**，便于逐条复盘。

**3. 两级工具系统。** 高级**工作流工具**（`src/tools/tool_flows.py`，32,296 B）把数十个原子操作封装为 `execute_prepare_flow` / `execute_scan_flow` / `execute_trade_flow` / `execute_management_flow` 四大指令，既提效也**强制 AI 走标准流程**；**原子工具层** 30 余个（`tool_market` / `tool_patterns` / `tool_news` / `tool_portfolio` / `tool_trade` / `tool_utility`），含价格行为扫描、谐波 / 供需区扫描、基于形态与 ATR 缓冲的**动态止损**、按风险金额**精确计算手数**，以及开平仓 / 修改 / 部分平仓等完整指令封装；`search_jina_and_read` 让 AI 在决策前**主动检索新闻**。

**4. ⭐ 技术看点：MT4–Python 的"文本文件桥"。** Python 后端与 MT4 EA（`MQL4/Experts/AI_Trader_for_MT4.ex4`，**133,076 B**）在 **`MQL4/Files` 目录读写 `.txt` 文件**通信，**生产者–消费者模式异步解耦**，README 明确说这是为了**避免 DLL 调用的脆弱性**：Python 生成带唯一 ID 的命令文件 → EA 轮询执行后写响应文件；EA 持续输出账户 / 持仓 / 报价 / 新 K 线，Python 端实时监控。

**5. SQLite 订单库不只是镜像，还承担"状态增强"。** `src/services/order_db_manager.py`（**37,211 B**）维护本地 SQLite：AI 订单的 `Comment` 遵循 **`L=0;G=A;S=PIN;M=N;ID=xxxx`**（利润阶梯等级 / 交易评级 / 信号类型 / 管理状态 / 唯一 ID），解析后作为**结构化状态驱动仓位管理**；**部分平仓时 MT4 会关旧单、开新单**，系统把新单的 **`extends` 字段指向原单并继承全部 AI 元数据**以保持管理连续；新单的利润阶梯价格（TP1–TP8）**入库时预计算**，加速后续决策。

**6. GUI：PyQt5 + qasync + pyqtgraph。** 基于 **PyQt5 + qasync**（PyQt 与 asyncio 结合）做不卡顿桌面界面，集中展示账户净值 / 持仓 / AI 状态 / 思考日志；可切换模型与策略档案（如 `15-Minute` 日内、`4-Hour` 波段）、管理 API 密钥；集成 **pyqtgraph**，双击持仓即调出对应品种 K 线图。

**7. 规模与状态（ungh 快照 2026-11-19）。** **30 Star / 15 Fork / 1 关注**（shields 同期一致）；**2 个开放 Issue、贡献者 1 人**（**solo 项目**）；**2026-01-19 创建**、`pushedAt` **2026-08-08**、`updatedAt` **2026-11-02**；`main`；**1 条 Release（v1.0.0，2026-01-19）**；**51 个文件**；shields 顶层语言 **100.0%**（Python）。README 末尾"更多项目"列出同作者的 `ai-trader-for-mt5`（MT5 版）与一系列 AI 视频 / 内容流水线项目。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [toki-plus/ai-trader-for-mt4](https://github.com/toki-plus/ai-trader-for-mt4) |
| 描述（GitHub） | "一个用于将大型语言模型 (LLM) 与 MetaTrader 4 (MT4) 连接起来的 Python 框架，用于创建自主的、工具驱动的算法交易机器人。"（中英双语） |
| 许可证 | **MIT**（`LICENSE` 1,061 B，`Copyright (c) 2026 Toki`；README 结尾 "See LICENSE"）；**ungh 的 `license` 字段为空**，shields 报 **MIT** |
| 主语言 | Python（shields 顶层语言 **100.0%**）；另含 **MQL4**（EA 源码 `.ex4`） |
| 关注度 | **30 Star / 15 Fork / 1 关注**（ungh，2026-11-19）；**2 个开放 Issue、贡献者 1 人**（shields） |
| 创建 / 最近推送 | **2026-01-19 创建**；`pushedAt` **2026-08-08**、`updatedAt` 2026-11-02 |
| 默认分支 / Release / 文件数 | `main`；**1 条 Release（v1.0.0，2026-01-19）**；**51 个文件** |
| 技术栈 | **Python 3.8+ · PyQt5 · qasync · pyqtgraph · asyncio · LLM API（OpenAI / DeepSeek / Moonshot）· SQLite 本地状态 · MetaTrader 4 EA Bridge** |
| 主要目录 | `src/bridge/`（`mt4_bridge.py` + `ai_client.py` 28,386 B）· `src/app/`（`agent.py` / `prompts.py` / `trading_engine.py`）· `src/tools/`（flow + 6 个原子工具模块）· `src/services/`（配置 / 日志 / 订单库 / 结构化日志）· `src/gui/`（主窗 + 图表查看器 32,658 B）· `config/`（`mt4_config.json` / `news_calendar.json` / `strategy_profiles.json`）· `tests/test_integration_suite.py`（**56,966 B**） |
| 分发方式 | ① **免环境直跑**：从 Releases 下载 `AI-Trader-For-MT4.zip`，把 EA 拖到 MT4 图表、配 `.env`、双击 exe；② **源码方式**：`pip install -r requirements.txt` 后 `python main.py` |
| 环境变量要点 | 至少一个 LLM Key（OpenAI / DeepSeek / Moonshot）+ 可选 `Jina` Key；**关键**：`MT4_DATA_PATH` 必须指向 MT4 数据目录下 **`MQL4/Files`** 的绝对路径 |
| 同作者项目 | [ai-trader-for-mt5](https://github.com/toki-plus/ai-trader-for-mt5)（MT5 版）等 |

## 系统架构与工作流程（README）

```
MetaTrader 4
    <-> File-based bridge / compiled EA
        <-> Python application
            -> Market data tools
            -> Portfolio and order tools
            -> Strategy and risk configuration
            -> LLM-assisted agent
            -> Signal and execution workflows
            -> Structured logs and local database
```

```
Market and account state
    -> Prepare workflow
    -> Read-only analysis
    -> Candidate trade plan
    -> Deterministic validation
    -> Controlled execution
    -> Position management
    -> Logs and review
```

**"只读分析 / 候选计划 / 实际执行"三分离 + 确定性校验夹在中间**，是本项目与"让模型直接下单"最本质的差别。

## 使用前需要注意

- **⚠️ 哥白尼式提醒（README 原文加粗）**：本项目披露"**自动化交易系统固有高风险，可能导致重大财务损失**"，并**强烈建议仅在模拟账户（Demo Account）上运行、切勿在真实资金账户使用**；不保证收益；作者不对任何损失负责。**本笔记不构成任何投资建议**——若真要碰，请先在模拟账户与隔离环境验证。
- **⚠️ 依赖外部 LLM 与新闻 API**：决策质量受所选模型、API 可用性与配额影响；README 把"模型输出不具确定性、不能替代风险规则和人工监督"列入**当前限制**。
- **⚠️ 明列的四条限制**：① **MT4 文件桥存在轮询延迟、依赖终端持续运行**；② 新闻与模型服务依赖外部 API；③ 模型输出非确定性；④ **项目尚未配置持续集成，测试需使用者在受控环境自行执行**（`python tests/test_integration_suite.py` 会**真实访问交易流程**，必须在隔离测试环境跑）。
- **⚠️ 平台面很窄**：仅 **Windows**（MT4 属性）+ 具体 MT4 终端；数据经 MQL4/Files 目录交换，**MT4 侧是 `.ex4` 编译 EA**（源码请在 Release / 仓库确认口径）。
- **⚠️ 规模小、单人维护**：**30 Star、贡献者 1、`pushedAt` 停在 2026-08-08**（约三个月未推送）；作为学习 / 研究样本合适，**不要当成熟生产系统**。
- **⚠️ 我未做任何运行验证**：仅读 README、LICENSE、文件清单与元数据；**未 clone、未装 MT4 / EA、未跑 `main.py`、未配 API Key、未打开 GUI、未跑集成测试、未审计源码**——所有功能描述均为 README 自述。

## 什么时候会再看

- 想参考"**如何把 LLM 的开放输出收进一条确定性执行链**"：**只读分析 → 候选计划 → 确定性校验 → 受控执行**，以及提示词层面写死的风险上限 / 信号分级 / 止盈阶梯。
- 想研究"**交易系统里 Agent 的工具分层**"：把数十个原子工具封成 4 个"工作流动词"来**限制而非放大**模型自由度。
- 想借鉴"**MT4 与外部程序通信**"的一种工程选择：**用 `MQL4/Files` 文本文件桥替代 DLL 调用**（牺牲实时性换健壮性），以及"**部分平仓产生的关联订单用 `extends` 继承元数据**"这类细节。
- 想对照"**桌面单机受控执行**"与"**联网多 Agent 平台 / 引擎**"两种 AI 交易形态（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"AI 交易"，一个是桌面受控执行、一个是联网跟单社区**：[AI-Trader：HKUDS 的 Agent 原生交易平台与跟单社区](AI-Trader-Agent原生交易平台与跟单社区.md)——本篇是**单机桌面框架**（MT4 + LLM，强调"只读 / 计划 / 执行"三分离与确定性校验、**明令仅用模拟账户**）；那篇是**托管平台**（Agent 一句话接入、发信号互相跟单、积分激励，**仓库内却无 LICENSE**）。可对照"**把权限收紧到工具与规则里**"与"**把 Agent 放进一个交易社区**"两种思路。
- **同为"自托管 + 给 Agent 划风险边界"的 AI 交易系统，市场与技术栈不同**：[QuantDinger：开源的 AI 交易操作系统](QuantDinger-开源AI交易操作系统.md)——那篇面向**加密交易所**、自托管 local-first、凭据不出本机、实盘需过"`token scope + paper_only + 服务端开关 + 限额白名单`"四重闸门；本篇面向 **MT4（外汇 / CFD）**、用 `Prepare/Scan/Act/Conclude` 流程与提示词硬规则约束执行。可对照"**同一件事（不让 AI 乱下单）在两种市场与两种架构里怎么落地**"。
- **同为"让模型在明确工具与风险边界内交易"，一个桌面、一个研究框架**：[TradingAgents：把"交易公司"搬进多 Agent 的 LLM 金融交易框架](TradingAgents-把交易公司搬进多Agent的LLM金融交易框架.md)——那篇是**跨平台研究框架**（多 Agent 角色分工与辩论编排 + 防前视数据管线 + 模拟交易所，Apache-2.0）；本篇是 **Windows / MT4 桌面**应用（文件桥 + 两级工具 + 提示词硬规则，明令仅用模拟账户）。可对照"**桌面受控执行**"与"**研究型多 Agent 管线**"。

## 参考来源

- 仓库主页与 `README.md`（205 行 / 10,874 字节中文版；核验日期 2026-11-19）：https://github.com/toki-plus/ai-trader-for-mt4
- 英文版 `README_en.md`（11,883 字节）· `LICENSE`（MIT，1,061 字节）· `config/*.json` · `requirements.txt` · `main.py`
- 模块说明与源码体量取自 `main` 分支文件清单（51 项）：`src/app/prompts.py`（25,924 B）· `src/tools/tool_flows.py`（32,296 B）· `src/services/order_db_manager.py`（37,211 B）· `src/gui/chart_viewer.py`（32,658 B）· `tests/test_integration_suite.py`（56,966 B）
- 同作者：<https://github.com/toki-plus/ai-trader-for-mt5>
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-19

以上信息核验于 2026-11-19：仓库内容与 Star 数可能变化。**未 clone、未安装 MT4 / EA、未运行程序、未配置 API Key、未跑集成测试、未审计源码**；功能与工程细节均为 README 自述，本笔记未独立验证，也未就交易或投资的合规性咨询任何专业意见。**自动化交易风险极高，请仅使用模拟账户。**
