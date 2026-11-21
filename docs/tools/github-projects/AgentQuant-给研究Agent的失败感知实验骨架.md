# AgentQuant：给"研究型 Agent"的失败感知实验骨架

> 记录一个把"**研究 Agent 到底是在变聪明，还是只是在多跑实验**"当作核心问题的开源实验骨架——用量化策略做第一块试验田，把每次实验（**含失败**）都留痕，并让"改进"必须过一道测量门槛。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（3,047 字节）、`DESIGN.md`、`ROADMAP.md`、`CHANGELOG.md` 首部、`docs/EXPERIMENTAL_DETAILS.md`、`docs/RESEARCH_AGENT_DESIGN.md`、`pyproject.toml`、`LICENSE`；并对 README 引用的路径逐个 GET 核验、取 ungh 镜像与 shields 徽章；**未 clone、未安装、未运行 demo / CLI / notebook、未配任何 API Key、未跑回测或测试、未审计全部源码**）
- 适用范围：**Python 3.10+**；命令行 / Python 包（`agentquant`、`run-agent`）+ 可选 Streamlit 面板；**核心流程（离线 demo、Agent 搜索循环）不需要 API Key**，只有 LLM 提案与联网研究才需配提供商凭证

## 核心结论

**1. 它的问题意识和常见的"AI 炒股"项目不一样。** README 开篇两句话写得很直白：给研究 Agent 一个"**记住什么失败了**"的记忆，和一条"**验证什么有效**"的路径；它跑一个研究循环——**提出实验 → 测试 → 看结果 → 用这份证据决定下一个**，并把整段搜索（**包括失败尝试**）记录下来，好让你**在同样预算下比较不同研究方法、并回放它们的决策**。定量策略只是第一块试验田，真正要问的是："**这个 Agent 是变得更会挑实验了，还是只是跑得更多？**"

**2. 核心是一个"有界 + 留痕 + 过门槛"的研究循环。** `DESIGN.md` 描述的 `agent_graph` 是 5 个显式节点：

```
analyze ──► hypothesize ──► backtest ──► reflect
              ▲                            │
              └────── (retry ≤ N) ◄────────┘
                                           │
                                         store
```

`analyze` 从特征 + 市场状态识别器构造 `RegimeContext`；`hypothesize` 用 `ProposalGenerator` 出候选；`backtest` 把所有候选当"锦标赛"按 Sharpe 排名；`reflect` 若最优 Sharpe 未达 `min_acceptable_sharpe` 就重试；`store` 把最优结果落到 `StrategyMemory`。循环默认最多重试 `max_iterations = 3` 次，**到上限就接受当前最优**（而不是不产出）。

**3. 一个关键的设计取舍：让 LLM"从格子里选"，而不是"自由发挥"。** 所有提案被约束到一个规范化的 `ParameterGrid`——LLM **selects from** 这个网格、不生成自由整数（`DESIGN.md` 原话）。这样做的直接目的是让"**LLM 提案 vs 网格搜索**"的 A/B 比较在统计上成立。`ProposalGenerator` 的兜底链是：LLM（校验 JSON）→ 带状态先验的网格搜索（Crisis/Bear 取短窗、LowVol/Bull 取长窗）→ 网格内随机采样。

**4. 明确写着"还没做到"的部分（这是它最值得记的一点）。** README《Where it stands》原文：有界搜索循环、记忆服务、回放产物与咨询型工具**已实现**；但"**经得起验证的自主自改进仍是一个研究目标**"——**实时策略比较、对 Sharpe 的"搜索感知"推断、完整的晋级门槛接入、前瞻性（prospective）评估**都还开着。同时 README 特意说明：**离线的 prompt-mutation 分支不调用 LLM，所以"分数对上了"不构成"学到了改进"的证据**。换句话说，这个仓库**自己把"看起来在学习"和"确实在学习"分开了**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [OnePunchMonk/AgentQuant](https://github.com/OnePunchMonk/AgentQuant) |
| 描述（GitHub） | `A reproducible, failure-aware harness for research agents that propose experiments, learn from evidence, and improve under measured gates.` |
| 作者 | Avaya Aggarwal（`LICENSE` © 2026；`pyproject.toml` 留邮箱 `aggarwal.avaya27@gmail.com`） |
| 许可证 | **MIT**（根目录 `LICENSE`，1,071 B）；shields 报 MIT |
| 主语言 | Python（shields 顶层语言 **99.1%**） |
| 关注度 | **222 Star / 48 Fork / 6 关注**（ungh，2026-11-19；shields 同期同为 222 / 48）；**21 个开放 Issue、贡献者 2 人**（shields） |
| 创建 / 最近推送 | **2025-08-12 创建**；`pushedAt` **2026-11-18**、`updatedAt` 2026-11-14（**活跃**） |
| 默认分支 / Release / 文件数 | `main`；**2 条 Release**（最新 **v0.3.1，2026-11-18**，0 资产；**v0.3.0，2026-09-19**，2 资产，自述"首个带 tag 的可安装版本"）；ungh 文件清单 **250 项**（**⚠️ 该清单不完整，见下**） |
| 运行形态 | CLI（`agentquant ...` / `run-agent`）· Python 包（`pip install -e .`）· Streamlit 面板（装 `.[ui]` 后 `python run_app.py --app`）· Docker / docker-compose |
| 环境要求 | **Python 3.10+**；核心依赖 pandas / numpy / pydantic v2 / yfinance / scipy / pyarrow / statsmodels / rich 等；可选 extras：`ui` / `llm` / `data` / `backtest` / `regime` / `dev` |
| 主要目录 | `src/agent/`（`agent_graph.py` 循环 · `proposal_generator.py` · `policy*.py` · `memory_layer.py` · `swarm/` 多 Agent）· `src/memory/`（统一记忆层）· `src/features/`（特征与 `regime.py`）· `src/backtest/` · `src/strategies/` · `src/research/` · `gate/`（`agentquant_gate` 晋级门槛 + 账本）· `experiments/` · `scripts/` · `tests/`（40+ 测试文件）· `docs/`（含 `MEMORY_LAYER_DESIGN.md` 27 KB · `PAPER_DRAFT.md`）· `.harness/`（v1–v6 的 harness 配置快照） |

## 它跑起来是什么样

**离线 demo（README 首推、无需 Key）：**

```bash
git clone https://github.com/OnePunchMonk/AgentQuant.git
cd AgentQuant
python -m venv .venv
source .venv/bin/activate
python -m pip install -e .
python run_app.py
```

README 明说这个 demo：在**合成价格**上跑三次搜索迭代，写出 `results/demo_run.json`，展示的是**循环与它的轨迹**，**不是交易业绩基准**。

**比较不同搜索策略（README 给的命令）：**

```bash
python scripts/fair_search_benchmark.py --episodes 3 --seeds 7 11 19 \
  --output results/fair_search_benchmark.json
```

同样在合成 fixture 上跑；README 强调离线 prompt-mutation 分支不调用 LLM。

**工程化的量化骨架（`DESIGN.md`）：** 市场状态用 **VIX 分位数（相对 252 日）** 而非写死的绝对阈值（原文吐槽"VIX > 30 就 High Volatility"在 2020 后基准位移时会失效）；特征含多周期实现波动率 / 动量 / SMA / RSI / MACD / 布林带 / ATR / 回撤 / VIX；回测引擎在**引擎层**做**前视偏差防护**（`WarmupEnforcer`、`@enforce_lookback`），预热不足会**抛 `InsufficientWarmupError` 而不是静默用旧指标**；交易成本按换手（`signal.diff().abs()`）比例计入；策略有 6 类（动量 / 均值回归 / 波动率 / 趋势跟随 / 突破 / 按状态分派）。

**记忆与"做梦"：** `StrategyMemory` 用 SQLite 存跨会话结果，`to_prompt_context(regime)` 把历史回灌进下一次 LLM 提示，让 Agent 少犯重复错；`CHANGELOG` 的 Unreleased 段里还有一套"统一记忆层"（每次回测都记为 trial、留出样本外证据、读用带**时点（`as_of`）截断**的 SQL、信念按证据加权）与一个 **`agentquant dream` 做梦模式**（回填历史记忆、在向前数据上重放、判决变化时写整固笔记）。

**多 Agent 与演化：** `src/agent/swarm/` 有协调者 / 批评者 / 记忆 / 状态分析师 / 专家等角色；`.harness/` 存 v1–v6 的 harness 配置（base → tool-aware → prompt-tuned → grid-evolved → multi-agent → research），配 `scripts/harness_evolution_6_epochs.py` 与 `results/harness_evolution_6epochs_results.json`、`HARNESS_EVOLUTION_RESULTS.md` 等"实验记录"型文档。

## 使用前需要注意

- **⚠️ 这是"研究阶段"的骨架，不是可用的交易系统**：README 自己把"**经得起验证的自主自改进**"列为**尚未达成的研究目标**，把"实时策略比较、搜索感知的 Sharpe 推断、完整晋级门槛、前瞻评估"列为 open；它自带的 demo **跑在合成数据上、明确不是业绩基准**。**本笔记不构成任何投资建议，也不对该项目的策略有效性作判断。**
- **⚠️ "在学"与"看起来在学"已被作者区分**：README 明说离线 prompt-mutation 分支**不调用 LLM**，因此分数吻合**不能**作为"学到了改进"的证据——读它的实验结论时，先分清哪些分支真的接了模型。
- **⚠️ ROADMAP 把"上真钱前必做"列成了待办**：Monte Carlo 路径分析、Kelly 仓位、状态切换自动重学、实时 Sharpe 衰减监控都被标为"交易前必须做"，且状态是"Ideas stage"；**实盘桥（paper trading / 券商 API / 风控限额）整块未建**。
- **⚠️ ungh 的文件清单不完整**：ungh 返回 **250 项**，但 README 引用的 `docs/AGENTQUANT_HARNESS_ARCHITECTURE.md`（53,663 B）、`docs/diagrams/research_harness.png`（72,637 B）、`optimizations/…` 等经 `raw.githubusercontent.com` 逐个 GET 均**确实存在**。**故本笔记的"文件数 250"只是下界，不代表仓库真实规模**——这是 ungh 镜像滞后，不是仓库缺文件。
- **⚠️ README 有 1 处失效内链**：`docs/SELF-IMPROVING_SEARCH.md`（连字符）返回 **404**，真实文件是 `docs/SELF_IMPROVING_SEARCH.md`（下划线，6,647 B）；其余被核验的路径均存在。
- **⚠️ 体量与维护面都小**：贡献者 **2 人**、21 个开放 Issue、创建约 15 个月；**属于个人研究项目**，接口与目录结构可能随研究节奏变动，引用具体路径时应记录提交。
- **⚠️ Windows 适配未验证**：README 的启动命令是 POSIX 写法（`source .venv/bin/activate`），本笔记在 Windows 上**未安装、未运行**，可用性未测。

## 什么时候会再看

- 想研究"**怎么让 Agent 的自改进变得可测量**"：把提案约束进规范参数网格（好让 LLM 与网格搜索可做 A/B）、把失败也留痕、给"接受"设 Sharpe 门槛——这套做法可迁移到任何"用 Agent 做实验搜索"的场景。
- 想找"**研究工具的诚实度写法**"范本：专门一节讲"哪些已实现、哪些还是研究目标"，并主动指出"分数对上 ≠ 学到东西"。
- 想参考"**把实验全链路留痕以便回放**"：trials / 样本外证据 / 带时点的记忆截断 / episode bundle 与 research memo 的导出。
- 想对照不同"AI + 交易 / 研究 Agent"形态时（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"用多个 Agent 做交易决策"，一个把重心放在"研究可信度"上**：[TradingAgents：把"交易公司"搬进多 Agent 的 LLM 金融交易框架](TradingAgents-把交易公司搬进多Agent的LLM金融交易框架.md)——那篇是 12 万 Star 级的"模拟一家交易公司"框架，重心在**角色分工与辩论编排 + 时点一致性 + 跨运行记忆**；本篇体量小得多（222 Star），重心在**"搜索本身是否在进步"这一研究问题 + 失败留痕 + 晋级门槛**。可对照"**多 Agent 决策编排**"与"**研究循环的可测量性**"。
- **同为"可回测的 AI 交易系统"**：[ai-hedge-fund：把 AI 对冲基金做成可长期运行的终端系统](ai-hedge-fund-可回测与模拟盘的AI对冲基金引擎.md)——那篇把"基金"当持久对象、重心在**回测 / 模拟盘同一条代码路径 + 哈希链账本**；本篇把"实验搜索"当对象，回测只是**评估提案**的一环。可对照"**结果导向的基金引擎**"与"**过程导向的实验骨架**"。
- **同为"给 Agent 做实验 / 复现"的资料**：[agentic-rl-lab：用 PyTRIO 复现 LLM / Agent 强化学习算法的中文实验手册](agentic-rl-lab-复现LLMAgent的强化学习算法实验手册.md)——那是把 GRPO / DAPO / GSPO 等 RL 算法做成能跑的实验手册；本篇用"网格约束 + 失败记忆 + 门槛"来研究 Agent 的搜索策略改进。可对照"**算法实验手册**"与"**Agent 实验骨架**"两种组织方式。
- **同为"把仿真 / Agent 接到外部系统、并如实标注未验证部分"**：[stonkfly：果蝇连接组交易实验](stonkfly-果蝇连接组交易实验.md)——那篇用真实果蝇连接组仿真驱动加密货币实验，最值得记的是**诚实的未验证声明写法**；本篇同样是"实验性质、不声称可用"，且 README 专门区分"看起来在学习"与"确实在学习"。可对照两者**如何划定"已验证 / 未验证"的边界**。

## 参考来源

- 仓库主页与 `README.md`（3,047 字节；核验日期 2026-11-19）：https://github.com/OnePunchMonk/AgentQuant
- `DESIGN.md`（技术设计 v2.0）· `ROADMAP.md`（状态：Ideas stage，标注"上真钱前必做"与实盘桥）· `CHANGELOG.md`（首部 Unreleased 段）· `docs/EXPERIMENTAL_DETAILS.md` · `docs/RESEARCH_AGENT_DESIGN.md` · `docs/MEMORY_LAYER_DESIGN.md`（27 KB）· `docs/PAPER_DRAFT.md`
- `pyproject.toml`（版本 0.3.0；依赖与 extras）· `LICENSE`（MIT，1,071 B，© 2026 Avaya Aggarwal）
- 逐路径核验：对 README 引用的 `docs/SELF-IMPROVING_SEARCH.md`（404）/ `docs/SELF_IMPROVING_SEARCH.md` / `docs/AGENTQUANT_HARNESS_ARCHITECTURE.md` / `optimizations/…` 等逐个 GET（2026-11-19）
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release / 文件数）：ungh.cc 镜像与 shields.io，2026-11-19

以上信息核验于 2026-11-19：仓库内容与 Star 数可能变化。**未 clone、未安装、未运行 demo / CLI / notebook、未配置任何 API Key、未跑回测或测试、未审计全部源码**；项目能力、实验结论与"自改进"进展均为 README / 文档 / 仓库结构自述，本笔记未独立验证，也不对该项目的策略表现或投资适用性作任何判断。
