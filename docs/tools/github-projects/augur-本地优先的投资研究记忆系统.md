# augur：本地优先的投资研究记忆系统

> 一句话定位（README 自述）："**不是又一个 AI 荐股工具。是一个帮你记住'你何时知道什么、什么变了、谁在什么事实上分歧'的本地研究记忆系统。**" 它让 **18 位投资大师 personas** 各自独立分析、再算加权共识，但**真正的卖点是"可复查"**：每条证据带**业务时点 / 可得时点 / 获取时点**三重时间语义，缺数据时**显式标 `missing` 并让相关大师 `abstain`**（而不是静默填 0），每次运行存成**不可变的 RunBundle** 可导出/对比/复查，数据全部落在本地 `~/.augur`、**无遥测**。**最值得记的三点**：① 它把"**研究记忆**"而非"**结论**"当产品——三件事做到位：**证据带时间语义**、**缺数据不编**、**每次运行可复现对比**；② 引擎**规则化模拟**大师理念，且**自己把'回测置信区间偏乐观'这类局限写在 README 里**；③ **CLI + Dashboard + MCP** 三入口（MCP 提供 14 个工具、7 个 prompt、5 类只读资源）。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（458 行，含 `README_EN.md` 存在性核对）、`LICENSE`、完整文件树与元数据；**未 clone、未安装、未运行任何命令、未接 MCP、未联网取任何行情或 SEC 数据、未复核任何分数或回测**）
- 适用范围：**Python 3.10+**；`pip install -e ".[data]"`（含 yfinance 实时数据，可选 `mcp` / `export` extra）。取美股财报数据需**联网**并设 `AUGUR_EDGAR_CONTACT_EMAIL`（SEC EDGAR 要求真实联系邮箱）。数据目录默认 `~/.augur`，可用 `AUGUR_DATA_DIR` 改。仓库语言为 **Python**。

## 核心结论

**一句话定位（README 自述）**：大多数 AI 金融工具**给你一个答案**；Augur 给你的是**可复查的研究产物**。

| | 普通 AI 工具 | Augur（README 对比表） |
| --- | --- | --- |
| 给你什么 | 一段"一方面…另一方面…" | **18 位大师的独立判断** + 他们在哪里一致、在哪里打架 |
| 数据从哪来 | 不清楚 | 每条证据带**来源、业务时点、可得时点、获取时点** |
| 缺失数据 | 静默填 0 | **显式标 `missing`**，依赖该字段的大师 `abstain` 并说明原因 |
| 三个月后回头看 | 不记得上次说了什么 | 每次运行存为**不可变 RunBundle**，可导出、比较、复查 |
| 数据在哪 | 云端 | **本地 `~/.augur`**，**无遥测** |

**三个最值得记的点：**

1. **"研究记忆"才是产品**：它反复强调**时间语义**（业务时点 / 可得时点 / 获取时点）与**可复查的 RunBundle**——即"**你何时知道什么、什么变了**"。这比"给个分数"要硬得多，也更接近真实投研的痛点（**避免事后诸葛亮**）。Dashboard 的 `/thesis`（投资论文、催化剂、风险、**证伪条件**、决策日志）与 `/scorecard`（财报前/后对照）都是围着这一点做的。
2. **它对"不可信"的处理很一致**：缺数据 → 标 `missing` + 大师 `abstain`；数据不够 → 直接显示 `unavailable` **和原因**（README 明确写"**不填假内容**"）；财报日期是估算的 → 标 `estimated`，**绝不标成"已确认"**。**这种'宁可说不知道'的做派，本身就是它的核心差异点。**
3. **18 位大师是"规则化模拟"，且它自己承认局限**：覆盖经典价值（Buffett / Graham / Munger / Fisher）、成长创新（Lynch / Wood / Thiel / Aschenbrenner）、宏观周期（Dalio / Soros / Marks / **ARPS** 加密黄金）、中国价值（**段永平 / 张磊 / 李录 / 但斌 / 大宇**）、特殊策略（Serenity，AI 算力供应链）。README 明确：**"大师观点是基于公开投资理念的规则化模拟，不代表本人立场"**；`augur eval personas` 还会**逐个去掉某位大师看共识 IC 变好还是变差**，并把"分数恒定、没有信息量"的大师单独列出；**并自曝**"远期收益逐日重叠，**置信区间偏乐观**，只能当筛查，不能直接当权重"。

**个人判断（未验证）**：它是"**AI 投研**"里少见的**把'可审计性'当第一性**的项目——不承诺替你赚钱，而是逼你把"依据、时点、分歧"记清楚。**但要注意**：它的所有分数、Kelly 仓位与估值都**依赖数据质量与模型假设**（README 免责声明原话）；大师是**规则化模拟**（不是真人观点）；它**不构成投资建议**。**我没有运行任何一次**，Dashboard 截图、"不填假内容"的行为、MCP 工具数都与回测指标**均照录 README，未独立验证**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [BruceLanLan/augur](https://github.com/BruceLanLan/augur) |
| 描述 | "🦉 Augur — 本地优先的投资研究记忆系统：18 位投资大师独立分析 + 加权共识，证据带时间语义、每次运行可复查，CLI / Dashboard / MCP。A local-first research memory system with 18 investor personas." |
| 归属 | 个人账号 `BruceLanLan`（ungh `orgs` 查询非组织）；`isFork: false`、`isArchived: false` |
| 许可证 | **MIT**（`LICENSE` 1,068 字符，`Copyright (c) 2026 BruceLanLan`）——**注意：这只是代码许可；它引用的行情/财报数据与（若用）模型服务另有条款** |
| 主语言 | **Python 75.1%**（shields.io，2026-11-13）；其余为前端（Dashboard）等 |
| 技术栈 | Python 3.10+ · **yfinance**（实时行情，`.[data]` extra）· **SEC EDGAR / XBRL**（财报与 10-K）· **MCP**（`mcp` 1.x 与 2.x 都经真实 stdio 会话验证；14 工具 / 7 prompt / 5 只读资源）· 本地 Dashboard（`augur serve`，默认 `127.0.0.1:8000`）· CLI（`augur ...`）· 可选 PDF 导出（`.[export]`） |
| 关注度 | **685 Star / 115 Fork / 4 Watcher**（**页面值**，2026-11-13）；开放 Issue **3**、贡献者 **3**（shields.io，2026-11-13）。**ungh 镜像 681 / 115 / 4，基本一致** |
| 维护状态 | **2026-04-07 创建**；ungh 报 `pushedAt` **2026-10-29**（距核验日约 2 周）、页面 `updatedAt` 2026-11-05；当前 **v13.0.0（"Earnings Loop"）**，页面报 **1 Release / 4 tags** |
| 默认分支 | `main` |
| 版本 | **1 Release / 4 tags**（README 徽章标 **v13.0.0**；`CHANGELOG.md` 记录 v10→v11→v12→v13 的演进） |
| 话题标签 | `ai-agents` · `investment-analysis` · `multi-agent` · `stock-analysis` · `value-investing` |
| 体积 | `main` 分支 **804 个条目**（ungh.cc files API）；顶层以 `src`（317）、`docs`（230）、`tests`（187）、`hermes-agents`（19）、`scripts`（15）、`scanner`（6）、`feedback`（6）、`personas`（3）为主 |

## 能做什么（README 自述）

- **研究闭环（CLI）**：`augur analyze AAPL`（18 位大师同时分析 + 加权共识 + Kelly 仓位）· `augur workflow`（带追踪、存 RunBundle、末尾打印 Run ID）· `augur report from-run`（共识、分歧图、来源）· `augur export`（md/json/evidence-pack/pdf）· `augur committee`（预设委员会提问）· `augur valuation`（两阶段 DCF，自动取 FCF/股本/净负债并注明来源）· `augur risk-review`（对比两份 10-K 风险因素章节的新增/删除/措辞升级）· `augur backtest`（离线合成数据可 `--demo`）。
- **财报周期（v13 新）**：`augur watchlist add` → `augur earnings --backfill`（**从 SEC 8-K Item 2.02 回填历史财报日期**，再按发布节奏**估算**下一次并标 `estimated`；手写的日历条目不被覆盖）→ `augur cycle`（对未来 N 天要发财报的自选股给出就绪状态、缺口与下一步；**只读本地状态、不联网**）。
- **内置研究 Skill**：`augur skill run earnings-prep AAPL`（财报前 dossier，**分歧逐条引用证据 ID**，无证据支撑的分歧单列成 "evidence gap"）· `filing-delta`（自动取最近两份 10-K 的 XBRL，列出变化 ≥5% 的实质性项）· `debt-covenant-review` · `insider-cluster-review`。**执行前，执行器会先检查每步要用的能力、网络域名与本地资源是否都在清单里声明过，任一不通过就不开始。**
- **Dashboard**（`augur serve --open`，`⌘K` 命令面板）：首页（行情 + 大师概览）· `/stocks` · `/committee` `/debate` `/compare`（预设委员会、多轮辩论、五维雷达）· `/history`（运行历史与热力图）· `/thesis`（投资论文/证伪条件/决策日志）· `/earnings` `/scorecard`（财报队列、事后记分卡）· `/inbox` · `/valuation`（DCF 表单 + WACC × 永续增长率敏感度网格）· `/portfolio` `/watchlist` `/settings`。
- **接入 Agent**：`augur-mcp` 提供 14 个工具（含 `augur_run_skill`）、7 个研究 prompt、5 类只读资源（`augur://evidence/{id}`、`augur://runs/{id}`、`augur://thesis/{id}`、`augur://decisions/{id}`、`augur://ledger/{ticker}/{quarter}`）。
- **信任相关的默认值**（README 记录的历史变更）：`augur serve` / `augur api` **默认只监听 `127.0.0.1`**；**学习权重与 rolling-IC 默认不混入共识**（数据太少会把噪声当能力，需 `AUGUR_FORCE_LEARNED=1` / `AUGUR_FORCE_RIC=1` 显式开启）。

## 使用前需要注意

- **不构成投资建议**（README 原文警示）：**"仅供学习研究"**；**"大师观点是基于公开投资理念的规则化模拟，不代表本人立场"**；**所有分数、Kelly 仓位与估值都依赖数据质量与模型假设**。
- **回测结论不要当真金**：README 自己写 `augur eval personas` 的"远期收益逐日重叠、**置信区间偏乐观**，只能当筛查，不能直接当权重"。**别把它当可交易的证据。**
- **数据质量决定上限**：行情走 yfinance、财报走 SEC EDGAR/XBRL。README 记录了历史修正（如 **SEC 市值单位曾误用美元原值**，导致 v11 前基于回放生成的因子/IC 含误差）。**任何结论都要回到原始来源核对。**
- **本地优先 ≠ 完全不联网**：数据落本地、**无遥测**，但 `augur earnings --backfill`、`filing-delta` 等**需要联网**访问 EDGAR（并要求你提供**真实联系邮箱**）。
- **MCP / Dashboard 的暴露面**：默认只听 `127.0.0.1`；**若要对外暴露，README 建议先设 `AUGUR_API_TOKEN` 再加 `--host 0.0.0.0`**——即"**一旦对外开放就是有风险的**"，别裸奔。
- **非常小的团队**：**3 位贡献者、3 个开放 Issue**；v11→v13 有过**破坏性 import 变更**（模块移到 `augur.experimental.*`、删掉兼容转发模块），**升级前应读 `CHANGELOG.md`**。
- **我只读了文档**：**未安装、未运行、未接 MCP、未联网取数据、未复核任何分数/回测/Dashboard 截图**；"18 位大师"、"14 个 MCP 工具"、`abstain`/`missing` 行为**均为 README 自述**。

## 什么时候会再看

- 想要一套**跑在自己机器上、可审计可复查**的投研工作流（而不是又一个"给结论"的 AI），需要 18 位大师视角、时间语义证据、RunBundle 复查时。
- 想参考"**怎么把'不确定性'正经地建模进产品**"：`missing` → `abstain`、`estimated` vs `confirmed`、`unavailable` + 原因、RunBundle 可复查，都是可迁移的设计。
- 想对照"**AI 价值投资研究框架**"的另一条路线：[ai-berkshire](ai-berkshire-AI时代的伯克希尔价值投资研究框架.md)（Claude Code/Codex 上的四大师 Skill 合集）。
- 想研究"**本地优先 + MCP 暴露**"如何设计默认安全值（默认 `127.0.0.1`、学习权重默认关闭、要 token 才对外）。
- 想找"**本地优先的个人金融工具**"的其他样本：[wealthfolio](wealthfolio-本地优先个人财务追踪器.md)（个人财务追踪）· [Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)（投研工作台）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **几乎同题、路线不同的另一篇**：[ai-berkshire：AI 时代的伯克希尔——价值投资研究框架](ai-berkshire-AI时代的伯克希尔价值投资研究框架.md)——两篇都在做"**AI + 价值投资研究**"，但**形态相反**：那篇是挂在 **Claude Code / Codex 上的 Skill 合集**（四位大师、多 Agent 并行、22 个 Skill、3 层架构），本篇是**独立 CLI/库 + Dashboard + MCP**（18 位大师、**强调时间语义与 RunBundle 复查**）。**一个把大师限定在四位并把'反偏见机制'写进流程，一个把大师扩到十八位并把'证据时点与可复查'写进数据模型。**要选型建议两篇对着读。
- **同为"本地优先的个人金融 / 投研工具"**：[wealthfolio：本地优先个人财务追踪器](wealthfolio-本地优先个人财务追踪器.md)——那是**记账与资产追踪**（本地优先），本篇是**研究记忆**（本地优先）。可对照"**本地优先**"在两类金融工具里的不同落法。
- **A 股/美股/港股投研工作台的近邻**：[Vibe-Research：A股美股港股投研工作台](Vibe-Research-A股美股港股投研工作台.md)——可对照"**投研工作台**"的两种形态：一个是**多大师研判 + 记忆**，一个是**工作台式数据与研究组织**。

## 参考来源

- 仓库主页与 README（19,655 字符 / 458 行，中文为主，另有 `README_EN.md`；核验日期 2026-11-13）：https://github.com/BruceLanLan/augur
- 许可证：`LICENSE`（MIT，1,068 字符，`Copyright (c) 2026 BruceLanLan`）
- 变更与升级说明：`CHANGELOG.md`、README 中 v10/v11/v12/v13 的升级段落
- Skill 指南与回放说明：`docs/skills-guide.md`、`docs/RELEASE_NOTES_v11.md`（README 指向）
- 完整文件树（804 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档 / topics）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**基本一致**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号经 ungh `orgs` 接口确认为个人账号

以上信息核验于 2026-11-13。**未 clone、未安装、未运行任何命令、未接 MCP、未联网取数据、未复核任何分数或回测**；定位与差异（时间语义证据、`abstain`/`missing`、RunBundle、18 位大师、MCP 工具数、Dashboard 页面）均来自 README，"不构成投资建议"的标注为 README 原文。
