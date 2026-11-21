# TradingAgents：把"交易公司"搬进多 Agent 的 LLM 金融交易框架

> 记录一个**极高关注度的多 Agent LLM 金融交易研究框架**（`TauricResearch/TradingAgents`，描述 `TradingAgents: Multi-Agents LLM Financial Trading Framework`）：它**把一家交易公司的角色分工搬进多 Agent 系统**——**分析师团队**（基本面 / 情绪 / 新闻 / 技术）并行出报告 → **研究员团队**（看多 vs 看空）结构化辩论 → **交易员 Agent**（定时机与仓位）→ **风险管理团队**（激进 / 保守 / 中立三方辩论）→ **组合经理**做最终审批；订单被送到**模拟交易所**执行。用 **LangGraph** 搭图，支持**几乎所有主流 LLM 提供商**（含用你自己 ChatGPT Plus/Pro 订阅登录）。三件事最值得记：① **体量惊人**——**123,049 Star / 23,592 Fork**（本 wiki 迄今见过的最高 Star 之一）；② **它的核心工程主题是"防前视 / 时点一致性（point-in-time）"**——美国财报走 **SEC EDGAR 按公告日**、其余走 Yahoo（按所属期而非发布日，故过去的日期会被"扣留"）、**任一"历史日期"的运行只能看到当天已公开的数据**；③ **Apache-2.0 开源**、15 条 Release、**`pushedAt` 就在核验前一天**。未 clone、未安装、未运行、未接任何 API。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（421 行 / 27,379 字节）、`LICENSE`（Apache-2.0，11,357 字节）、`CHANGELOG.md` 体积、`pyproject.toml` / `Dockerfile` / `docker-compose.yml` 条目、`tests/` 与 `tradingagents/` 文件清单（共 213 项）、ungh 镜像与 shields 徽章；**未 clone、未 `pip install`、未配任何 API Key、未运行 CLI / Python API、未跑回测或测试、未打开 tauric.ai、未审计全部源码**）
- 适用范围：**命令行 / Python 库**（`tradingagents` 命令或 `from tradingagents...` 导入）+ **Docker**；**Python 3.11+**（README 安装示例用 3.13）；覆盖 **Yahoo Finance 能取到的任何市场**（美 / 港股 / 东京 / 伦敦 / 印度 / 加拿大 / 澳大利亚 / **中国 A 股（沪 `.SS`、深 `.SZ`）** / 加密）；面向**研究**，非投资建议

## 核心结论

**1. 定位：模拟一家交易公司的"多 Agent 分析—辩论—决策"链路。** README：TradingAgents"mirrors the dynamics of real-world trading firms"——通过部署专门化的 LLM Agent（基本面分析师、情绪专家、技术分析师，到交易员、风控团队、组合经理），协作评估市场并形成交易决策，且 Agent 之间**动态讨论**以求最优策略。**明确声明"为研究目的设计"，不是投资建议**（`not intended as financial, investment, or trading advice`，另有 tauric.ai/disclaimer）。

**2. 角色分工（README）。**

| 团队 | 成员 | 职责 |
| --- | --- | --- |
| **分析师团队** | 基本面 / 情绪 / 新闻 / 技术 | 分别评估公司财务、聚合新闻 + StockTwits + Reddit 情绪、解读全球新闻与宏观、用 MACD / RSI 等技术指标找形态。**所选分析师同时开工，各自用自己的工具，全部报告到齐后才开始研究辩论** |
| **研究员团队** | 看多 + 看空研究员 | 对分析师结论做**结构化辩论**，在潜在收益与固有风险间求平衡 |
| **交易员 Agent** | — | 汇总分析师与研究员报告，做交易决策，**定时机与仓位大小** |
| **风控 + 组合经理** | 激进 / 保守 / 中立三方辩论 + 组合经理 | 风控团队持续评估市场波动、流动性等风险因子、调整策略并出评估报告；**组合经理批准 / 否决交易提案，通过则送模拟交易所执行** |

**3. ⭐ 核心工程纪律：防前视 / 时点一致性（point-in-time）。** README 用大段篇幅讲这件事（也是 15 条 Release 反复修的主题）：
- **美国公司财报走 SEC EDGAR**，EDGAR 记录每个数字的**申报日**——**一个"过去的日期"的运行只读到当天已存在的报表**：已结束但**尚未申报**的财年**不提供**；**后来被重述的数字仍按首次报告读**。README 举例：Apple 2008 年总资产按申报为 **$39.6B**、2010 年被重述为 $36.2B，**介于两者之间日期的运行读到 $39.6B**。
- **非美公司财报走 Yahoo Finance**，Yahoo 按**财报覆盖期**而非发布日归档 → **今天的运行能读到，但"过去的日期"被告知这些数据被扣留**（无法确定当时哪些已公开）。
- **内幕交易按发生日（而非申报日）归档**，故往期运行同样被扣留。
- **新闻 / 社交数据（Reddit、StockTwits）也做前视过滤**、**决策日志记忆做时点修复**（v0.4.0 起）、**回测只看到各分析日已发布的数据**（v0.5.x）。

**4. 跨运行"记忆 + 结算 + 反思"（README "Memory log"）。** 记忆日志**始终开启**：每次完成的运行把决策追加到 `~/.tradingagents/memory/trading_memory.md`；**当后一次运行的分析师工作时，系统会把所有"持有期已过"的历史决策结算掉**——取实际收益（原始收益 + 相对该品种**区域基准的 alpha**）并生成一段反思；组合经理随后读同标的最近决策与其他标的的近期教训，**让每次分析带着"上次哪些有用 / 没用"**。

**5. 回测：把"一次决策"扩成"网格"并以 alpha 计分。** `run_backtest` 在**标 × 日期**的网格上跑同一条管线，**用自己的记忆日志**、对已过持有窗口的决策计分（**相对该品种的区域基准的已实现 alpha，按评级分组**）；决策在**其日期之后的第一个收盘**入场（因为运行会读到当日收盘后发布的新闻）；`run_id` 让中断的网格**续跑**。

**6. 可复现性（README 专章，态度诚实）：** 明确"**同一标的同一日期的两次运行可以有差异，这是 LLM 研究工具的正常现象，不是缺陷**"，并拆解来源——**模型采样非确定性**（即便温度固定也不保证逐字节一致，**推理型模型差异最大**，因为其内部推理本身被采样）；**实时数据在变**（新闻 / 社交源随时间返回不同内容，即使历史交易日相同）；给降低波动的建议（调低温度、**改用具名非推理模型**）。同时说明"**不再会变**"的两条：**被分析的公司身份在任何 Agent 运行前已由 ticker 确定性解析**；**市场分析师把价格与指标主张锚定在"已核验的数据快照"上**——README 称"早前那种跨运行出现'不同公司'或编造价格"的问题已由这两个机制处理。

**7. 规模与状态（ungh 快照 2026-11-19）。** **123,049 Star / 23,592 Fork / 919 关注**（shields 同期 123k / 24k）；**51 个开放 Issue、贡献者 23 人**；**2024-12-28 创建**、`pushedAt` **2026-11-17**（**活跃**）、`updatedAt` 2026-11-18；`main`；**15 条 Release**（最新 **v0.7.1，2026-10-27**）；**213 个文件**；shields 顶层语言 **99.9%**（Python）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) |
| 描述（GitHub） | `TradingAgents: Multi-Agents LLM Financial Trading Framework` |
| 组织 | **Tauric Research**（官网 / 免责声明 <https://tauric.ai/disclaimer/>；社区含 Discord、X） |
| 论文 | **arXiv:2412.20138**（`q-fin.TR`）：*TradingAgents: Multi-Agents LLM Financial Trading Framework*，作者 **Yijia Xiao / Edward Sun / Di Luo / Wei Wang**（2025） |
| 许可证 | **Apache-2.0**（`LICENSE` 11,357 字节）；**ungh 的 `license` 字段为空**，shields 报 **Apache-2.0** |
| 主语言 | Python（shields 顶层语言 **99.9%**） |
| 关注度 | **123,049 Star / 23,592 Fork / 919 关注**（ungh，2026-11-19；shields 同期 123k / 24k）；**51 个开放 Issue、贡献者 23 人**（shields） |
| 创建 / 最近推送 | **2024-12-28 创建**；`pushedAt` **2026-11-17**、`updatedAt` 2026-11-18（**活跃**） |
| 默认分支 / Release / 文件数 | `main`；**15 条 Release**（最新 **v0.7.1，2026-10-27**）；**213 个文件** |
| 运行形态 | CLI（`tradingagents` 命令 / `python -m cli.main`）· **Python 库**（`from tradingagents.graph.trading_graph import TradingAgentsGraph`）· **Docker**（`docker compose run --rm tradingagents`，数据落 `tradingagents_data` 卷）· Ollama profile |
| 环境要求 | **Python 3.11+**（示例用 3.13）；`pip install .`；Docker 方式只需 API Key |
| 主要目录 | `tradingagents/`（`agents/` 分析师·研究员·风控·交易员·经理；`dataflows/` 数据路由与各 vendor；`graph/` LangGraph 装配与 checkpoint；`llm_clients/` 各提供商客户端与模型目录；`memory/` 记忆·反思·结算；`backtest.py`、`report_html.py`）· `cli/`（交互与无提示运行）· `tests/`（**100+ 个测试文件**，含回测 / 前视 / EDGAR / 情绪 / 检查点等）· `main.py` · `Dockerfile` · `.github/workflows/`（CI + release assets） |
| 兄弟项目 | [Trading-R1](https://github.com/TauricResearch/Trading-R1)（2026-11 起早期访问；技术报告 arXiv:2509.11420） |

## 数据源、模型与用法（README）

**数据源（vendors）**：**Yahoo Finance**（默认，覆盖任意市场）· **Alpha Vantage** · **SEC EDGAR**（按申报日的"as filed"基本面，无需账号 / Key）· **FRED**（宏观，免费可选）· **Reddit** · **StockTwits** · **Polymarket** · **TypeSafe 的 Jev**（可选，对社交帖子做**筛选**：与公司无关的丢弃，并按 bullish / bearish / neutral / unclear 给出余量计数；无 Key 则不过筛）。

**LLM 提供商**：OpenAI / Google / Anthropic / xAI / DeepSeek / Qwen（阿里 DashScope 国际 + 中国两套端点）/ GLM（智谱）/ MiniMax（全球 + 中国）/ OpenRouter / Mistral / Kimi（Moonshot）/ Groq / NVIDIA NIM / **AWS Bedrock**（`pip install ".[bedrock]"`）/ **Azure OpenAI**（`.env.enterprise`）/ **Ollama 本地**（默认 `http://localhost:11434/v1`）/ **任意 OpenAI 兼容端点**（vLLM、LM Studio、llama.cpp、自建 relay）/ **直接用你自己的 ChatGPT Plus 或 Pro 订阅**（CLI 选 "Subscription"，`tradingagents login chatgpt`，凭据存 `~/.tradingagents/chatgpt-auth.json`，用量计入你套餐额度，**该套餐不接受 `temperature` / `max_tokens`**）。

**两种模型档位（tier）可各自换提供商**：quick 模型服务分析师 / 研究员 / 辩论者 / 交易员，deep 模型服务研究经理与组合经理；例如**经理跑 Claude、其余跑 OpenAI**（`deep_think_provider = "anthropic"`）。

**组合上下文**：默认 Agent **不知道你持有什么**，给出的建议面向"读者自行套用自己的仓位"；传入 `PortfolioContext`（现金 / 币种 / 持仓）则交易员、风控与组合经理**针对你的真实账本**工作（**空持仓列表 = 空仓，与"不传"不同**）。

**输出**：除报告树外还输出 **`complete_report.html`**（单页报告，侧栏列章节，可在浏览器 / 手机 / 打印阅读）；启动时会**一天最多一次**在后台检查新版本（发不出关于你运行的任何数据；`TRADINGAGENTS_NO_UPDATE_CHECK=1` 可关，无终端运行不检查）。

## 使用前需要注意

- **⚠️ 研究工具，非投资建议、非收益保证**：README 多渠道强调"为研究设计"、"回测结果**不保证**与任何已发表数字一致"，"**把它当成研究多 Agent 分析的脚手架，而不是一个收益固定可复现的策略**"。**本笔记同样不构成投资建议。**
- **⚠️ 两次运行结果会不同（且这是设计使然）**：LLM 采样 + 实时数据变化共同导致；即便固定温度、换非推理模型也只能**降低**而非消除差异——**不要把它当确定性系统**。
- **⚠️ 数据可用性有硬边界（README 自述）**：EDGAR 的机读申报**自 2009 年起**、且**第四季度按"不可用"处理而非推算**（申报方只把它放进年报）；EDGAR 需要通过请求头带联系方式（默认会发一个，建议设 `SEC_EDGAR_USER_AGENT` 成你自己的）；**非美公司（如港股 / A 股）财经数据落到 Yahoo**，且往期日期会被"扣留"。
- **⚠️ 大量外部 API 依赖与配额**：LLM 提供商、Alpha Vantage、FRED、社交源等任一不可用都可能影响运行；社交源（StockTwits / Reddit / Yahoo 新闻）**会随平台变化而失效**（README 的 release 记录里多次出现"Yahoo 新闻源故障期间改从 Yahoo 搜索读"这类修补）。
- **⚠️ 消耗真实模型额度**：用订阅方式登录会**计入你 ChatGPT 套餐的使用限额**；用 API Key 则按各提供商计费——**回测网格 × 多 Agent × 多轮辩论会显著放大调用量**。
- **⚠️ "执行"是模拟交易所**：README 明确订单送**模拟交易所**执行——**这不等于把框架接到你的真实券商**；但若自行改接，风控 / 合规 / 资金风险须自行承担。
- **⚠️ 我未做任何运行验证**：仅读 README、LICENSE、文件清单与元数据；**未 clone、未安装、未配 Key、未跑 CLI / Python API、未跑回测或测试、未打开 tauric.ai、未审计全部源码**——所有能力均为 README / 仓库结构自述，"前视防护""时点一致性""身份确定性解析"等**均未独立复现**。

## 什么时候会再看

- 想参考"**多 Agent 金融分析的分工与辩论编排**"：分析师并行 → 研究员多空辩论 → 交易员 → 风控三方辩论 → 组合经理审批，以及用 **LangGraph** 落地这套图。
- 想研究"**回测与研究的时点一致性（point-in-time）纪律**"：**按申报日读财报（EDGAR）**、**拿不到发布日的来源就"扣留"而非倒填**、**回测按"决策后第一个收盘"入场**——这是一套可迁移到任何"用历史数据做研究的 LLM 系统"的规则。
- 想参考"**让 Agent 跨运行学习**"的工程：**始终开启的记忆日志 + 到期决策的自动结算与反思 + 组合经理回读教训**。
- 想借鉴"**研究工具的诚实度写法**"：专门一章讲**为什么同一输入两次运行会不同**，并分清"哪些还会变、哪些已经不再变"。
- 想对照不同"AI + 交易"形态（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"用多个 Agent 做投资决策"，一个像"基金引擎"、一个像"交易公司"**：[ai-hedge-fund：把 AI 对冲基金做成可长期运行的终端系统](ai-hedge-fund-可回测与模拟盘的AI对冲基金引擎.md)——那篇把"**基金**"当持久对象，重心在**回测 / 模拟盘同一条代码路径 + 哈希链账本**（人格化投资者 + 量化信号，自述不做真实交易）；本篇把"**交易公司**"当结构，重心在**角色分工与辩论编排 + 时点一致性 + 跨运行记忆**（Apache-2.0、模拟交易所）。可对照"**可长期运行的基金引擎**"与"**可研究的多 Agent 决策框架**"。
- **同为"AI Agent 做交易"，一个是研究框架、一个是联网平台**：[AI-Trader：HKUDS 的 Agent 原生交易平台与跟单社区](AI-Trader-Agent原生交易平台与跟单社区.md)——那篇是**托管平台 / 跟单社区**（Agent 一句话接入、发三类信号、互相跟单、积分激励，**仓库内却无 LICENSE**）；本篇是**可本地/离线运行的开源研究框架**（Apache-2.0、15 条 Release、明确"模拟交易所执行"）。可对照"**把 Agent 放进社区互相跟单**"与"**在一台机器上模拟一家交易公司**"。
- **同为"让模型在明确工具与风险边界内交易"，一个学术框架、一个 MT4 桌面**：[ai-trader-for-mt4：把 LLM 接到 MT4 的"受控执行"交易框架](ai-trader-for-mt4-把LLM接到MT4的受控交易框架.md)——那篇是 **Windows / MT4 桌面**应用（文件桥 + 两级工具 + 提示词硬规则，明令仅用模拟账户）；本篇是**跨平台研究框架**（LangGraph + 多提供商 LLM + 防前视数据管线）。可对照"**桌面受控执行**"与"**研究型多 Agent 管线**"。
- **同为"用 LLM/Agent 做交易研究"，一个把重心放在"研究可信度"上**：[AgentQuant：给"研究型 Agent"的失败感知实验骨架](AgentQuant-给研究Agent的失败感知实验骨架.md)——那篇体量小得多（222 Star），不看"多 Agent 怎么分工"，而追问"**研究 Agent 是在变会挑实验，还是只是跑得更多**"：把提案约束进规范参数网格、把失败也留痕、给"接受"设 Sharpe 门槛，并自陈"经得起验证的自主自改进仍是研究目标"。可对照"**把交易公司搬进多 Agent**"与"**把搜索过程本身做成可测量对象**"。

## 参考来源

- 仓库主页与 `README.md`（421 行 / 27,379 字节；核验日期 2026-11-19）：https://github.com/TauricResearch/TradingAgents
- `LICENSE`（Apache-2.0，11,357 字节）· `CHANGELOG.md`（53,618 字节）· `pyproject.toml` · `Dockerfile` · `docker-compose.yml` · `.env.example` / `.env.enterprise.example`
- 论文与引用：arXiv:2412.20138（q-fin.TR，Yijia Xiao 等）· 兄弟项目 Trading-R1（arXiv:2509.11420）
- 官网 / 免责声明：<https://tauric.ai/disclaimer/>
- 目录结构取自 `main` 分支文件清单（213 项）：`tradingagents/agents/**` · `tradingagents/dataflows/vendors/**` · `tradingagents/graph/**` · `tradingagents/llm_clients/**` · `tradingagents/memory/**` · `cli/**` · `tests/**`（100+ 测试文件）
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-19

以上信息核验于 2026-11-19：仓库内容与 Star 数可能变化。**未 clone、未安装、未配置任何 API Key、未运行 CLI / Python API、未跑回测或测试、未审计全部源码**；框架能力、数据源行为、前视防护与复现性说明均为 README / 仓库结构自述，本笔记未独立验证，也未就该框架的收益表现或投资适用性作任何判断。
