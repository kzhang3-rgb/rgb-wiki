# ai-berkshire：AI 时代的价值投资研究框架（Claude Code / Codex 技能合集）

> 一句话定位（README 自述）："**一个人 + Claude Code / Codex = 一个投研团队。**" 它把**巴菲特、芒格、段永平、李录**四位大师的方法论**系统化、结构化**成一套**同时兼容 Claude Code 与 Codex 的 Skill 合集**（22 个），用**多 Agent 并行**做专业级投研。**最值得记的三点**：① 它不是"用巴菲特视角分析一下"——**四位大师会给出真实的矛盾与张力**（README 用"巴菲特说真便宜、李录说不确定就不买"作例），并且**强制给结论**（通过 / 不通过 / 灰色地带 + 价格区间），不写"一方面…另一方面…"；② 它把**反偏见机制写进流程**——**信息丰富度 A/B/C 评级、芒格式逆向检验、8 条红线快速否决、反共识检查、留白原则**；③ **仓库里躺着 2,282 份、覆盖 114 家公司、29 个专题的报告**（`reports/` 占 3,656 个文件），并有独立阅读站 **xbtlin.github.io/ai-berkshire**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（760 行，另有 `README_EN.md` / `README_JA.md` 存在性核对）、`LICENSE`、完整文件树与元数据；**未 clone、未安装 Skill、未在 Claude Code / Codex 中运行、未读任何一份报告原文、未联网取任何数据、未复核任何实盘业绩数字**）
- 适用范围：**Claude Code** 或 **Codex**（Skill / 斜杠命令形态）。README 称"仓库是全量框架，公众号是精选"。仓库语言为 **HTML**（大量成品报告）。

## 核心结论

**一句话定位（README 自述）**：**"AI 时代的伯克希尔"**——一套**同时兼容 Claude Code 与 Codex** 的投资研究 Skill 合集，将四位价值投资大师（**巴菲特、芒格、段永平、李录**）的方法论系统化，通过 AI Agent 实现专业级投研。

**三个最值得记的点：**

1. **"强制给结论 + 四大师真实对抗"是它对着普通 AI 打的差异点**（README 自述）：
   - **强制输出**：**通过 / 不通过 / 灰色地带**，带**具体价格区间**与**分层建议**（激进 / 稳健 / 保守），并附"**镜子测试**：5 句话说不完整 = 不买，没有例外"。
   - **四位大师各给评分并允许冲突**（README 以拼多多为例）：段永平（商业模式）**3.7** / 巴菲特（财务估值）**4.4** / 芒格（逆向思考）**3.5** / 李录（长期确定性）**2.0**——**"巴菲特说真便宜，李录说'不确定就不买'"**。
   - **多 Agent 并行**：`/investment-team` **同时**启动 4 个独立 Agent 研究同一家公司，各自搜网络、交叉验证、独立结论，最后 Team Lead 综合（**不是把一个 prompt 拆成四段**）。
2. **结构化反偏见机制**（README 自述表）：
   | 机制 | 解决什么 |
   | --- | --- |
   | **信息丰富度评级（A/B/C）** | 防"资料多 = 确定性高"的幻觉（如泡泡玛特评为 B 级，推算指标标置信度） |
   | **芒格式逆向检验** | 强制想失败场景（"什么情况下会死？"→ 列情景与概率） |
   | **快速否决清单** | **8 条红线一票否决**（如管理层诚信污点直接否决，不管多便宜） |
   | **反共识检查** | "聪明人为什么在做空？" |
   | **留白原则** | 数据不足就标"灰色地带"，**不用推测伪装确定性** |
3. **"金融数据精确性"是它第二类硬约束**：README 举例——**不同来源市值单位可能是"港币亿"或"人民币亿"**，它用 `tools/financial_rigor.py verify-market-cap` **手算校验**（股价 × 总股本 vs 报告值），**所有计算用 Python `decimal.Decimal`（不用 `float`）**，**关键数据至少 2 个独立来源交叉验证**。

**它的三层架构（README 自述）**：**Skill 层**（22 个明确入口：深度研究 / 财报分析 / 行业筛选 / 持仓管理 / 思维工具）→ **Agent 层**（团队型 Skill 由 **Team Lead 并行调度 4 个大师视角 Agent**，各自独立搜索、独立判断、互相挑战，最后综合；轻量 Skill 不经此层）→ **工具层**（精确计算、实时检索、报告抽检）。

**个人判断（未验证）**：它的**方法论包装（强制结论 + 大师对抗 + 反偏见清单 + 精确计算）确实比"直接问 AI"更像一份投研纪律**，而 `reports/` 里 2,282 份成品报告是它"能持续产出"的实证。**但风险同样清楚**：① **实盘业绩（2024 +69.29%、2025 +66.38%）是作者自述、用富途截图佐证，未经审计**，README 自己也标"历史收益不代表未来"；② **它是 Skill/Prompt 形态**，最终质量**取决于所接的模型、检索到的数据与源数据质量**；③ README **专门警告有人冒名**（`ai-berkshire.com`、`aiberkshire.com` 等域名由他人注册，与本项目无关），**说明这个项目名已有被冒用的现实**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [xbtlin/ai-berkshire](https://github.com/xbtlin/ai-berkshire) |
| 描述 | "AI 时代的伯克希尔：基于 Claude Code / Codex 的价值投资研究框架。巴菲特·芒格·段永平·李录四大师方法论 + 多Agent并行研究。｜ AI-era Berkshire: a value investing research framework built for Claude Code / Codex. 4 masters' methodologies + multi-agent adversarial analysis." |
| 归属 | 个人账号 `xbtlin`（ungh `orgs` 查询非组织）；`isFork: false`、`isArchived: false` |
| 许可证 | **MIT**（`LICENSE` 1,063 字符，`Copyright (c) 2026 xbtlin`）——**注意：只覆盖仓库内容；`reports/` 里的报告是对第三方公司的研究，其数据来自外部来源；实盘业绩截图归作者** |
| 主语言 | **HTML 82.0%**（shields.io，2026-11-13）——**因为 `reports/` 下是大量成品报告**；其余为 Python（`tools/`）等 |
| 技术栈 | **Claude Code / Codex 的 Skill 与斜杠命令**（`skills/` 21、`codex-skills/` 23、`codex-prompts/` 21）；Python 工具（`tools/` 18，含 `financial_rigor.py`、`reports_index.py`）；`reports/` 为 HTML 报告；有 **MCP** 相关 topic；阅读站为 GitHub Pages |
| 关注度 | **17,108 Star / 2,516 Fork / 85 Watcher**（**页面值**，2026-11-13）；开放 Issue **27**、贡献者 **15**（shields.io，2026-11-13）。**ungh 镜像 17,099 / 2,515 / 85，基本一致** |
| 维护状态 | **2026-04-07 创建**；ungh 报 `pushedAt` **2026-11-04**、页面 `updatedAt` 2026-11-12（**活跃**）；页面报 **1 Release / 1 tag** |
| 默认分支 | `main` |
| 版本 | **1 Release / 1 tag**；README 徽章指向 `trendshift.io` 的 trending 榜 |
| 话题标签 | `ai` · `ai-agent` · `anthropic` · `berkshire-hathaway` · `charlie-munger` · `china-stock` · `claude` · `claude-code` · `financial-analysis` · `fintech` · `fundamental-analysis` · `investment` · `investment-research` · `llm` · `mcp` · `portfolio-management` · `stock-analysis` · `stock-market` · `value-investing` · `warren-buffett` |
| 体积 | `main` 分支 **3,951 个条目**（ungh.cc files API）；顶层以 **`reports`（3,656）**、`assets`（63）、`投资炼丹`（62）、`codex-skills`（23）、`skills`（21）、`筛选公司`（21）、`research_notes`（21）、`codex-prompts`（21）、`tools`（18）、`scripts`（8）、`data`（5）为主 |
| 官网 / 文档 | 阅读站 <https://xbtlin.github.io/ai-berkshire/> · 研究报告索引 `reports/README.md` |

## 内容与规模（README 自述）

- **报告库**：README 自动更新的横幅称"**全部 2,282 份报告（含底稿）· 114 家公司 · 29 个专题**，更新至 **2026-11-12**"，完整目录见 `reports/README.md`；阅读站支持按公司 / 专题 / 时间整理、可搜索、记住阅读进度。
- **22 个 Skill**：分深度研究、财报分析、行业筛选、持仓管理、思维工具等类；团队型 Skill 走 Agent 层。
- **多渠道文档**：`README.md`（中文）、`README_EN.md`（英文，34,878 字符）、`README_JA.md`（日文）。
- **官方渠道只有三个**（README 明确）：**本仓库、阅读站、微信公众号「复利炼丹炉」**；**不收费、不荐股、不带单、不建投资群、不以任何形式收款**；并**警示冒名**：`ai-berkshire.com`、`aiberkshire.com` 等域名**由他人注册、与本项目无关**——"凡以 AI Berkshire 名义拉群、收费、推荐个股的都不是本人"。

## 使用前需要注意

- **"实盘业绩"是作者自述，未经审计**：README 列 **2024 全年 +69.29%、2025 全年 +66.38%**，与恒生 / 标普 / 沪深 300 / 纳斯达克对比，称"两年累计超 146 万元、截图来自富途真实账户"。**这些数字未经第三方核实**，README 自己也标 **"历史收益不代表未来表现"**。**不要把过往收益当作能力证明。**
- **它面向投研，不是投资建议**：README 明说**不收费、不荐股、不带单**；产出的是"研究框架与报告"。**任何结论都需你自己判断，且数据准确性取决于外部来源与模型。**
- **存在被冒名的现实**：README 专门列出"**他人注册的近似域名**"与"**冒名拉群/收费/荐股**"的警告。**看到以 AI Berkshire 名义收费或带单的，按 README 口径都是假的。**
- **它是 Skill / Prompt 形态，质量强绑定运行环境**：要跑在 Claude Code / Codex 上，**实际效果取决于你用的模型、检索能力与源数据质量**；README 提到的"实时检索、报告抽检"等工具层能力**本笔记未验证**。
- **仓库很重**：`reports/` 一项就 **3,656 个文件**（整仓 3,951 个），**clone 与浏览成本都不低**（改用阅读站更省事）。
- **报告里的公司结论不等于事实**：`reports/` 是对第三方公司的研究输出，**可能过期、可能受模型与数据源影响**；**应以原始财报/公告为准**。
- **我只读了文档与元数据**：**未 clone、未安装 Skill、未运行、未读任何报告原文、未复核任何业绩数字**；22 个 Skill、三层架构、反偏见机制、报告数量与业绩数字**均来自 README**。

## 什么时候会再看

- 想在 **Claude Code / Codex** 上搭一套**有纪律的多 Agent 投研流程**（强制结论、大师对抗、反偏见清单、精确计算）时，来查它的 Skill 清单与流程设计。
- 想参考"**怎么把'反偏见'写成流程约束**"（A/B/C 信息丰富度、逆向检验、红线否决、反共识、留白）——这套机制**可迁移到任何"让 AI 做判断"的场景**。
- 想对照"**AI + 价值投资研究**"的另一条路线：[augur](augur-本地优先的投资研究记忆系统.md)（**本地优先的研究记忆系统**：18 位大师、时间语义证据、RunBundle 复查）。
- 想研究"**开源项目如何应对冒名与收费骗局**"的实际做法（README 的官方渠道声明与域名警示）。
- 想对照"**多 Agent 投研**"的其它实现：[easy_investment_Agent_crewai](easy_investment_Agent_crewai-A股多Agent分析平台.md)（CrewAI 的 A 股多 Agent 平台）· [Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)（A股/美股/港股投研工作台）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **几乎同题、形态相反的另一篇**：[augur：本地优先的投资研究记忆系统](augur-本地优先的投资研究记忆系统.md)——两篇都做"**AI + 价值投资研究**"，但**路子相反**：本篇是**挂在 Claude Code / Codex 上的 Skill 合集**（**四位**大师、多 Agent 并行、22 个 Skill、三层架构、反偏见清单），那篇是**独立 CLI / 库 + Dashboard + MCP**（**十八位**大师、**强调证据时间语义与不可变 RunBundle 复查**、缺数据 `abstain`）。**一个把"决策纪律"写进流程，一个把"证据可审计"写进数据模型。**选型建议两篇对着读。
- **A 股多 Agent 分析的近邻**：[easy_investment_Agent_crewai：A股多 Agent 分析平台](easy_investment_Agent_crewai-A股多Agent分析平台.md)——那篇用 **CrewAI** 编排 A 股分析 Agent；本篇是**大师方法论 + 多 Agent 对抗**的价值投资框架。可对照"**多 Agent 投研**"的两种编排方式与目标市场。
- **投研工作台的近邻**：[Vibe-Research：A股美股港股投研工作台](Vibe-Research-A股美股港股投研工作台.md)——可对照"**投研工具**"的两种交付形态：一个是**提示词/Skill 框架**（挂在你已有的编码 Agent 上），一个是**工作台**（自成一体的研究组织界面）。
- **A 股 AI 投研工作台的近邻（许可反而更紧）**：[easy-stock：A 股 AI 智能投研工作台](easy-stock-A股AI智能投研工作台.md)——那篇是 **Go + React/TS + Electron 的完整工作台**（行情 / 题材 / 四级研究 / 持仓巡检四维评分 / 持仓优化），但许可是 **PolyForm Noncommercial 1.0.0（源码可用、非商业）**；本篇是 **MIT + Skill 框架**（挂在你已有的编码 Agent 上）。两者可对照"**开源授权**"与"**源码可用**"的实际差别。

## 参考来源

- 仓库主页与 README（20,705 字符 / 760 行，中文为主，另有 `README_EN.md` 34,878 字符与 `README_JA.md`；核验日期 2026-11-13）：https://github.com/xbtlin/ai-berkshire
- 许可证：`LICENSE`（MIT，1,063 字符，`Copyright (c) 2026 xbtlin`）
- 报告索引：`reports/README.md`（README 指向）· 阅读站 <https://xbtlin.github.io/ai-berkshire/>
- 关于盈亏与精确性：README 的 `## Real Track Record` 与 `## 为什么不能直接问 AI` 段（含 `tools/financial_rigor.py` 示例）
- 官方渠道与冒名警示：README 中"官方渠道只有三个"与近似域名说明
- 完整文件树（3,951 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档 / topics）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**基本一致**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号经 ungh `orgs` 接口确认为个人账号

以上信息核验于 2026-11-13。**未 clone、未安装 Skill、未运行、未读任何报告原文、未复核任何实盘业绩数字**；定位、四大师与反偏见机制、22 个 Skill、三层架构、报告数量与业绩数字均来自 README，**业绩与收益类数字未经第三方审计**。
