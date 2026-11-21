# ai-hedge-fund：把 AI 对冲基金做成可长期运行的终端系统

> 一句话定位（README 自述）：**"An AI Hedge Fund Team"**，仓库自述为**教育用途的概念验证**（"For **educational** purposes only… the system does not actually make any trades"）。最值得记的三点：① 它把"基金"做成一等对象，而不是一次性脚本——**三层可插拔**（基金 = CIO 分配器 over 策略；策略 = 组合策略 over 分析师；分析师 = 产出 `Signal` 的 alpha 模型）；② **一个动词 `advance` 跑三种模式**（回测 / 模拟盘 / 实盘），**回测与模拟盘走同一条代码路径**，自述"回测即实盘、逐字节一致"；③ **持久化哈希链账本**——每次会话产出一条 `SessionRecord`，append-only、replay 时校验链，撮合账本与账本不一致时**抛错而非修复**。

- 状态：草稿
- 最近核验：2026-11-13（阅读根 `README.md`（91 行）、`hedge_fund/README.md`（172 行）、`ROADMAP.md`（142 行）、`VISION.md`（176 行）、`LICENSE`（MIT，1,068 字符）、`pyproject.toml` 与已核实元数据；**未 clone、未 `pipx install aihf`、未运行 TUI、未回测、未模拟盘、未接任何数据源或模型**）
- 适用范围：**Python ^3.11 + Poetry**；终端交互界面基于 **Textual**。需要外部**付费行情数据源**（Financial Datasets API Key，或改用 x402 按次付费）+ **至少一个模型 API Key**。仅面向学习与研究，**明确声明不构成投资建议、不做真实交易**；**未在本地验证过任何一条命令**

## 核心结论

1. **组织架构照搬真实基金，只是把人换成 AI**（`VISION.md`）：`基金 = 分配器（CIO）over 策略`；`策略 = 组合策略 over 分析师`；`分析师 = alpha 模型 → Signal`（置信度 `[-1, +1]` + 书面 thesis）。对外的映射表把**研究员 → alpha 模型**、**策略 / pod → 策略**、**组合经理 → 组合构建**、**CIO → 分配器**、**风控官 → 风控硬限**、**交易台 → 执行 / 券商**、**后台账本 → 账本**一一对上。
2. **两类分析师共用一个接口**（`hedge_fund/README.md`）：**主观型**（Discretionary，由 LLM 投资者人格担任——巴菲特、芒格、格雷厄姆、林奇、德鲁肯米勒，判断本身即 edge）与**系统型**（Systematic，纯量化模型——已实现 PEAD 盈余漂移），都实现 `AlphaModel.predict(ticker, date, data_client) -> Signal`，**不改引擎即可插入**。
3. **一个动词、三种模式**（`VISION.md`）：`advance` = 一次会话（对账 → 执行上一会话的挂起决策 → 按收盘价 mark 出 NAV → 评估下一个决策）。三种模式**只换时钟、券商与状态存放位置**：`BACKTEST`（历史时钟 + 模拟券商 + 内存态）／`PAPER`（实时时钟 + 纸面券商 + 账本态）／`LIVE`（实时时钟 + 真实券商 + 账本态）。目前**回测与模拟盘已具备，实盘"差一个券商"**（自述）。
4. **原则写在明面上且互斥性强**（`VISION.md` / `hedge_fund/README.md`）：**时点诚实**（按 filing date 过滤，绝不用未来数据）；**失败要响**（基础设施错误抛异常，只有真正的"无数据"才返回空）；**LLM 不碰下单**（模型只形成观点与叙述，仓位大小与下单由确定性代码执行，风控是硬门）；**决定在 T、执行在 T+1**（不存在同一收盘价既评估又成交）；**账本 append-only**（哈希链，replay 校验，状态只派生不修改）。
5. **"研究实验室"与"生产基金"并排**（`VISION.md`）：Level 1 = **你自己**提候选（"加 Munger、去掉 PEAD、把分配器调到 70/30"）→ 回测 → 自己晋级；Level 2 = **基金自己跑实验室**（自述为**愿景、非当前能力**），且自我改进要过验证门（CPCV / PBO 过拟合检查）后才拿资本，晋级实盘默认需人工批准。

**个人判断（未验证）**：它的看点不在"AI 会不会选股"，而在**工程结构**——把"基金"当成有持久状态的活物、用一个动词统一回测与模拟盘、用哈希链账本把每次决策的 thesis 都留下来。要判断它是否值得抄，关键看两点：**"回测与模拟盘逐字节一致"是否真的成立**（自述未复现），以及**验证门（CPCV / PBO）与实盘券商尚未落地**时它离"能实盘"还有多远。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [virattt/ai-hedge-fund](https://github.com/virattt/ai-hedge-fund) |
| 描述 | "An AI Hedge Fund Team" |
| 许可证 | **MIT**（`LICENSE` 1,068 字符，Copyright (c) 2024 Virat Singh；`pyproject.toml` 亦声明 `license = "MIT"`）|
| 关注度 | **ungh 64,648 Star / 658 Watcher / 11,375 Fork**；**页面 64,656 / 658 / 11,376**（2026-11-13）；shields 另报**开放 Issue 64、贡献者 41** |
| 维护状态 | **2024-11-29 创建**；ungh `pushedAt` **2026-11-09**、页面 `updatedAt` 2026-11-12；默认分支 `main`；**Release 页面 24 条**（ungh 单页返回 23） |
| 包名 / 版本 | Python 包 **`aihf`**，`pyproject.toml` 版本 **2.7.2**；命令行入口 `aihf = hedge_fund.run:main` |
| 技术栈 | **Python ^3.11 + Poetry**；LangChain（`langchain-anthropic` / `-openai` / `-deepseek` / `-google-genai` / `-xai`）、pandas / numpy / scipy、pydantic、rich、**textual ^8.2.8**（TUI）；可选 `x402` extra |
| 数据源 | [Financial Datasets](https://financialdatasets.ai)（行情 / 基本面 / 财报）；**或改用 x402 按次付费**（需持 USDC 的 Base 钱包私钥，`pip install 'aihf[x402]'`，自述限**近 365 天、非专业用途**） |
| 模型 provider | Anthropic · OpenAI · DeepSeek · Google · xAI · Kimi · TypeSafe (Jev) |
| 文件数 | 151（ungh `/files/main`：根级 + `.github/` + `assets/` + `hedge_fund/` 子树） |

## 功能与用法

### 1. 安装与运行（根 `README.md`）

```bash
pipx install aihf        # 或 uv tool install aihf，或 pip install aihf
aihf                     # 无参数：进入交互式终端应用
```

首次需要时应用会询问并保存 Key 到 `~/.hedge-fund/.env`（**shell 里导出的环境变量优先于该文件**）。所需 Key：**Financial Datasets 的 API Key**（或 x402 钱包私钥），外加**一个模型 provider 的 API Key**。

### 2. 交互式 TUI 的两种模式（根 `README.md`）

- **模拟盘（Paper trading）**：每个基金有**真实交易日 + 虚拟资金**的账本；高亮某基金可预览它下一轮要做什么，回车让它跑完**下一个已收盘的会话**（执行前有**审批步骤**展示即将执行的决策），`s` 看完整会话历史，`h` / `r` 是 **kill switch**（停机 / 恢复）；可从同一份清单新建基金（策略、资本、节奏、标的）。**每次会话追加进 `~/.hedge-fund/paper/<name>/` 里的哈希链账本**，所以 NAV 是**真实轨迹而非重置**。
- **回测（Backtesting）**：把某基金在历史区间重放，曲线对照基准；结果存到 `~/.hedge-fund/research/`。**基金（mandate）定义里不带标的**——每次回测时你才指定 universe。

### 3. CLI 与目录结构（`hedge_fund/README.md`）

```bash
aihf backtest ~/.hedge-fund/mandates/example.yaml --universe AAPL,MSFT,NVDA --start 2024-01-02 --end 2024-06-28
aihf paper create alpha --mandate ~/.hedge-fund/mandates/example.yaml --universe AAPL,MSFT
aihf paper tick alpha            # 幂等、绝不跳会话；cron 每个收盘后调一次即可
aihf paper tick alpha --again    # 重做最近一次会话，旧记录移入 ledger/superseded/
aihf paper run fast              # "分钟基金"（rebalance: minute）：常驻，每个 interval 一根 bar
aihf paper halt alpha --reason "..."   # kill switch
```

`~/.hedge-fund/` 下的落盘：`mandates/<name>.yaml`（策略 / 人员 / 风险 / 资本 / 节奏）、`research/`（每次回测一个 JSON）、`paper/<name>/`（`fund.yaml` 快照 + `broker.json` 券商账本 + `ledger/<session>.json` 哈希链记录 + `control.json` kill switch + `events.jsonl` 事件流 + `runner.pid`）。**所有 API 响应缓存到 `~/.hedge-fund/cache/`**，因此重跑快、免费、热身后可离线。

### 4. 引擎模块（`hedge_fund/README.md` 自述状态）

`数据（时点） → alpha 模型 → 组合 → 风险 → 执行 → 账本`。已具备：`data/`（`DataClient` 协议 + Financial Datasets 客户端 + 磁盘缓存）、`signals/`（`AlphaModel` 接口 + PEAD + `LLMAgent` 及 5 位投资者人格）、`llm/`（provider 协议 + Anthropic 客户端 + prompt 缓存）、`fund/`（`FundSpec`/`StrategySpec`，mandate 即 YAML）、`strategies/`（基础多空 / 深度价值 / 拐点 / 盈余漂移）、`portfolio/`、`risk/`（单仓与总敞口硬夹）、`brokers/`（`SimBroker` + `PaperBroker` + 交易成本）、`pipeline/`（`advance`）、`paper/`、`backtesting/`、`tui/`。**部分具备**：`features/`（时点基本面快照）、`brokers/`（实盘券商规划中）。**尚未实现**：`validation/`（CPCV 组合净化交叉验证、PBO 回测过拟合概率）。

### 5. 贡献面（`ROADMAP.md`）

- **加一个分析师**（写代码）：实现 `AlphaModel`，参照 `signals/buffett.py`（人格 = 一段 system prompt）或 `signals/pead.py`（量化）；加测试即可入引擎。
- **加一个策略**（不写代码）：往 `strategies/` 丢一个 YAML，把已有模型与混合策略打包，基金构建器自动识别。
- **加一个另类数据源**：卫星影像、网页 / 社媒搜索、App 下载趋势、航运数据等（规划中）。

## 使用前需要注意

- **仅教育 / 研究用途**：根 README 与 `VISION.md` 均反复声明——**不构成投资建议、不担保收益、创作者不承担财务损失责任、不面向真实交易或投资**；根 README 直接写"**该系统实际上不会做任何交易**"。**不要把它的回测结果当成可部署的收益预期**。
- **需要外部付费依赖**：行情数据必须来自 Financial Datasets（API Key 或 x402 按次付费），模型推理另需 provider 的 Key；**没有这些它跑不起来**，且成本由你的 Key 承担。
- **v2 是"进行中的重建"，且子文档与仓库现状已不一致**：`hedge_fund/README.md` 开头自称"**Work in progress**… developed alongside the shipped v1 app (`src/`, `app/`) but not yet wired into it"，但**当前仓库文件列表中已看不到 `src/` 与 `app/` 目录**（我实测这两个路径的原始文件均返回 404），`ROADMAP.md` 也写明"**retiring the v1 CLI**"。**即该子文档相对仓库现状已滞后**，看源码前需自行对齐版本。
- **多处能力仍在计划中**（均据 `ROADMAP.md` 自述，**未验证**）：`validation/`（CPCV / PBO 验证门）、**实盘券商**（Interactive Brokers / Alpaca，opt-in、默认关）、**内置日历感知守护进程**、**Ollama 本地免费路径**（自述是 v1 相对 v2 的最后优势）、**更多投资者人格**（Cathie Wood、Michael Burry 等还是未勾选行）、**Web 仪表盘**（脚手架存在但自述"仍跑在 v1 引擎上"）。
- **价格 / 关注度口径**：ungh 镜像 Star 64,648 与页面 64,656、Fork 11,375 与 11,376 **有细微差异**；**Release 页面 24 条而 ungh 单页只返回 23**（接口单页上限所致）。引用时注明来源与日期。
- **页面内嵌数据的许可证字段为空**：GitHub 页面内嵌 JSON 的 `license` 字段未填充，但**仓库内确有 `LICENSE`（MIT）**、shields 徽章也报 **MIT**、`pyproject.toml` 声明 MIT——**以仓库内文件为准**。
- **LLM 人格是"风格化近似"**：`VISION.md` 明说这些投资者代理是**对其公开理念的风格化近似，不是本人、也不构成背书**。
- **我只读了文档与元数据**：**未 clone、未安装 `aihf`、未运行 TUI、未回测、未模拟盘、未接任何数据源或模型、未复现任何数字**；模块状态表、逐字节一致性、成本模型等均来自文档自述。

## 什么时候会再看

- 想给"**多 Agent 分工做投研**"找一个**可回测、可模拟盘**的完整工程范本时。
- 想参考"**一个引擎三种模式（回测 / 模拟盘 / 实盘）+ 哈希链账本**"这种**可审计的持续系统设计**时。
- 想借鉴"**LLM 只出观点、确定性代码下单、风控是硬门**"这种职责切分的写法时。
- 想对照其它"**用 Agent 做投资分析**"的项目时（见下方互链）。
- 想研究"**mandate（不带标的的基金定义）与 universe（回测时再指定）分离**"这种可复用配置的建模时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"用多个 Agent 做投资分析"，工程重心不同**：[easy_investment_Agent_crewai：A 股多 Agent 分析平台](easy_investment_Agent_crewai-A股多Agent分析平台.md)——那篇用 CrewAI 编排四个角色分析 A 股、数据源为 akshare；本篇把"基金"当持久对象，重点在**回测 / 模拟盘同一条代码路径 + 哈希链账本**。可对照"**多 Agent 分工**"与"**可长期运行的基金引擎**"。
- **同属"AI + 量化交易"，形态不同**：[ai_quant_trade：AI 量化交易操盘手](ai_quant_trade-AI量化交易操盘手.md)——那篇是持续更新的量化交易**学习合集**（策略 / 机器学习 / 强化学习 / 大模型 / 实盘部署）；本篇是一个**具体项目的完整实现**。可对照"**资料索引**"与"**可运行系统**"。
- **同为"选股 / 量化"工具，市场与方法不同**：[KHunter：A 股量化选股系统](KHunter-A股量化选股系统.md)——那篇是**A 股形态选股**（11 种策略 + 五维评分 + 回测 + Web 界面）；本篇是**美股上的 LLM 人格 + 量化模型**基金。可对照两种市场与两种 entry。
- **都做"投资风险 / 表现的观测"**：[US_Stock_Crash_Monitor：美股风险监测面板](US_Stock_Crash_Monitor-美股风险监测面板.md)——那篇是**六因子风险监测面板**（强调可解释与数据缺失如实标注）；本篇在引擎内建**风控硬夹与账本对账**。可对照"**外部监测面板**"与"**系统内风控门**"。
- **同属"用 AI 做交易实验"，性质不同**：[stonkfly：果蝇连接组交易实验](stonkfly-果蝇连接组交易实验.md)——那篇用**果蝇连接组仿真**驱动加密货币实盘实验（偏"实验 + 诚实标注未验证"）；本篇是**结构化基金引擎**。可对照"**仿真驱动实验**"与"**工程化基金系统**"。
- **都关心"参与美股 / 交易的门槛与背景"**：[chinese-buy-us-stock-guide：中国人投资美股指南](chinese-buy-us-stock-guide-中国人投资美股指南.md)——那篇讲**开户 / 税务 / 合规 / 出入金**等现实环节；本篇只做**研究与回测**、不下真实单。可对照"**现实交易链路**"与"**研究型系统**"。
- **同为"选股 / 量化"工具，市场与方法不同**：[InStock：抓数到自动交易的 A 股量化系统](InStock-A股量化选股与自动交易系统.md)——那是 **A 股上的规则化系统**（指标 + 形态 + 策略 + 回测 + GUI 自动化自动交易，**不含 AI 选股**）；本篇是**美股上的 LLM 人格 + 量化模型**基金。可对照两种市场与两种实现路线。
- **同为"用 AI Agent 做交易"，一个单机、一个联网平台**：[AI-Trader：HKUDS 的 Agent 原生交易平台与跟单社区](AI-Trader-Agent原生交易平台与跟单社区.md)——那篇是**在线平台**（Agent 一句话接入、发三类信号、互相跟单、积分激励 + 把线上数据做成论文数据集的研究管线，且**仓库内无 LICENSE**）；本篇是**单机基金引擎**（自述不做真实交易、MIT）。可对照"**个人基金引擎的工程纪律（哈希链账本 / 一个动词三模式）**"与"**多 Agent 交易社区的平台设计**"。
- **同为"用多个 Agent 做投资决策"，一个像"基金引擎"、一个像"交易公司"**：[TradingAgents：把"交易公司"搬进多 Agent 的 LLM 金融交易框架](TradingAgents-把交易公司搬进多Agent的LLM金融交易框架.md)——那篇把"**交易公司**"当结构（分析师并行 → 多空辩论 → 交易员 → 风控三方辩论 → 组合经理，LangGraph + 模拟交易所，Apache-2.0、123k Star），重心在**角色分工与辩论编排 + 时点一致性 + 跨运行记忆**；本篇把"**基金**"当持久对象，重心在**回测 / 模拟盘同一条代码路径 + 哈希链账本**。可对照"**可研究的多 Agent 决策框架**"与"**可长期运行的基金引擎**"。
- **同为"用 AI 做交易"，一个把重心放在"研究 / 实验的可测量性"上**：[AgentQuant：给"研究型 Agent"的失败感知实验骨架](AgentQuant-给研究Agent的失败感知实验骨架.md)——那篇体量小得多（222 Star），把"基金 / 结果"换成"实验搜索 / 过程"：追问"Agent 是在变会挑实验，还是只是跑得更多"，把提案约束进规范网格、给"接受"设 Sharpe 门槛、把失败也留痕。可对照"**结果导向的基金引擎**"与"**过程导向的实验骨架**"。

## 参考来源

- 仓库主页与根 `README.md`（91 行；核验日期 2026-11-13）：https://github.com/virattt/ai-hedge-fund
- 引擎文档：`hedge_fund/README.md`（172 行；含 `advance` / 两模式 / `~/.hedge-fund/` 落盘 / 模块状态表 / 数据契约）
- 方向文档：`ROADMAP.md`（142 行）、`VISION.md`（176 行）
- 许可证：`LICENSE`（MIT，1,068 字符，Copyright (c) 2024 Virat Singh；核验日期 2026-11-13）
- 包定义：`pyproject.toml`（`name = "aihf"`、`version = "2.7.2"`、依赖与入口）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release / 文件数）：ungh.cc 与 GitHub 页面内嵌数据、shields.io 交叉核对（2026-11-13，**镜像与页面差异很小**）

以上信息核验于 2026-11-13。**未 clone、未安装 `aihf`、未运行 TUI、未回测、未模拟盘、未接任何数据源或模型、未复现任何数字**；结论均来自根 `README.md`、`hedge_fund/README.md`、`ROADMAP.md`、`VISION.md` 与元数据，**未逐一验证**。
