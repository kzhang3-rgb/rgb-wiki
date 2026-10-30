# global-stock-data：美股与全球市场数据工具包

> 记录它的"官方源优先"策略，以及把数据源按合规等级分级的做法——"官方"不等于"免费用"。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README、合规分级表与 GitHub API 元数据，未安装运行、未实际取数）
- 适用范围：Python 3.9+；面向 Claude Code / Codex / OpenClaw 等 AI 编程助手；部分数据源仅适合个人研究（见合规分级）

## 核心结论

`a-stock-data` 的姊妹项目，同一个作者，把美股与全球市场数据整合成单文件 Skill。README 头部标注的形态是 **16 层架构 · 50+ 个端点 · 17 个数据源 · 零鉴权**，依赖只有 `requests`。

它和姊妹项目最大的区别不在数据种类，而在**把"合规"变成了一等公民**：每个数据源都标了等级（S/B/C/D），并把来源条款**原文摘录**出来，反复强调一句话——**"Official" does not mean "free to use."**（官方来源不等于可以免费用。）

最能体现态度的一件事：作者**已经写好了一个可用的港交所（HKEX）持股层，然后删掉了**——因为其条款禁止机器人/爬虫抓取，且"无论是否牟利"都适用。作者的理由是：发一个违反来源条款的代码，就违背了给数据源分级的初衷。

另外它明确声明 **"distributes code, not market data"**（分发的是代码，不是行情数据），数据由使用者按各来源自身的条款去取；商用只建议依赖 S 级来源。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [simonlin1212/global-stock-data](https://github.com/simonlin1212/global-stock-data) |
| 作者 | Simon Lin（[X @linsizhen](https://x.com/linsizhen)，simonlin0423@gmail.com） |
| 许可证 | Apache-2.0 |
| 语言 / 依赖 | Python 3.9+；仅 `requests`（Windows 另需 `tzdata`） |
| 关注度 | 1,819 Star、266 Fork、10 关注、0 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2026-05-20 创建，最近推送 2026-10-28 |
| 版本 | README 标注已到 **V2.2**（含 V2.0 官方源优先、V2.1 全球利率与外汇、V2.2 波动率/空头持仓/台湾/能源） |
| 兼容 | Claude Code、Codex、OpenClaw；SKILL.md 是 Markdown + 内嵌 Python，可直接拷走 |

## 合规分级（这是最值得看的部分）

作者声明分级来自**逐条读各来源的实际服务条款**，并标注了阅读日期（2026-07-24 初读，2026-10-11 / 10-12 复核部分来源），条款原文照引：

| 等级 | 商用 | 再分发 | 代表来源 | 依据（原文摘要） |
| --- | --- | --- | --- | --- |
| S | ✅ | ✅ | SEC EDGAR、美国财政部、CFTC、美联储、纽约联储、BIS、ECB、EIA（库存）、台湾证交所、TPEx | EDGAR："Anyone can access and download this information for free"、"We allow scripted access"（硬上限 10 req/s，需 User-Agent）；美联储："public domain"；BIS："unrestricted"；ECB："reused free of charge" |
| B | ⚠️ 需先核实 | ❌ | FINRA、EIA 现货价 | FINRA 禁止"data mining, scraping or harvesting tools (including robots)"，且限 **non-commercial use**；EIA 的 WTI/Brent/Henry Hub 现货价来自 Refinitiv（LSEG） |
| C | ❌ 需授权 | ❌ | CBOE、东财、新浪、腾讯 | CBOE 期权链与波动率指数需 **approval in advance + 签署许可协议**；东财/新浪/腾讯是网站前端接口，仅个人研究 |
| D | ❌ | ❌ | Yahoo、Nasdaq | 条款禁止未经许可的自动化访问；**仅个人学习研究，风险自负** |
| ⛔ 已排除 | — | — | HKEX（CCASS） | 禁止 robot/bot/spider/scraper，且"whether or not for gain"→ **本项目不提供该抓取器** |

作者还解释了一个很实在的矛盾：**为什么还留着 D 级的 Yahoo 和 Nasdaq？** 因为它们的条款性质其实和 HKEX 差不多，但没有替代品——Yahoo 是港股 K 线与美/港以外市场（日本、台湾、韩国、印度、欧洲）的唯一来源，Nasdaq 是唯一免 Key 的财报日历；而 HKEX 持股层属于"锦上添花"，所以删掉。作者的处理方式是：**代码保留、条款全文引用、用不用由你自己判断。**

> 这种"把风险写清楚、把选择权交给使用者、但不替使用者掩盖风险"的写法，比直接说"本项目合规"要有用得多。

## 数据覆盖（摘要）

- **行情与 K 线**：Sina / 腾讯 / 东财报价（25–78 字段）；Sina 与 Yahoo K 线（美股可回溯到 1984，Yahoo 的标普 500 可到 1927），支持日/周/月/分钟，日本 / 台湾 / 韩国 / 印度 / 欧洲通过代码后缀访问。
- **技术指标**：MA/EMA、MACD、RSI、KDJ、布林带，纯 Python 本地计算，零额外依赖。
- **基本面**：东财三表 + 关键指标、Yahoo quoteSummary（23 个模块）、SEC XBRL（503 个 GAAP 指标）。
- **期权与持仓 ⭐**：CBOE 官方期权全链（含 **IV 与 delta/gamma/vega/theta/rho**、**0DTE** 过滤、异动流检测）；FINRA 全市场每日卖空量（12,112 只）；FINRA 双月空头持仓（22,595 只，含回补天数）。
- **申报与事件 ⭐**：EDGAR 当日索引（当天的 Form 4 / 8-K / 13F）、2001 年起的全文检索、Form 4 内部人逐笔交易（含是否勾选 10b5-1）、13F 季度机构持仓。
- **筛选器与宏观 ⭐**：EDGAR frames（任意 XBRL 标签跨全市场筛选，免费）、美债收益率曲线（1M~30Y）、CFTC COT、财报日历。
- **全球利率与外汇 ⭐（V2.1）**：BIS 一次调用取 38 家央行政策利率、ECB 汇率与关键利率与欧元区曲线、纽约联储 SOFR/EFFR、美联储 H.10/H.6、财政部拍卖与联邦债务。
- **波动率 ⭐（V2.2）**：CBOE 波动率指数（VIX 自 1990 起）与 **VIX 期限结构**（contango / backwardation）、VVIX/SKEW/OVX/GVZ。
- **台湾 ⭐（V2.2）**：TWSE + TPEx 官方 OpenAPI（最新交易日行情、PE、月营收、融资融券，商用例已核实）。
- **能源 ⭐（V2.2）**：EIA 周度原油/汽油/馏分油/天然气库存；WTI（1986 起）/ Brent / Henry Hub 现货价（**B 级**）。

## 使用前需要注意

- **必须先设 `SEC_CONTACT`**：SEC 要求声明 User-Agent，不声明会被当作 *Undeclared Automated Tool* 拒绝；作者说忘设时代码会直接报错提醒。SEC 请求内置限流压到 8 req/s（官方上限 10）。
- **"零鉴权"不等于"零成本"**：Yahoo 的 cookie + crumb 由代码自动管理并会过期刷新；各来源的条款限制才是真正要看的成本。
- **两条数据不是一回事**：FINRA 的"每日卖空量"（当天卖空成交）与"空头持仓"（未平仓空头，半月一次）是不同数据集，作者在 FAQ 里专门澄清。
- **时间新鲜度不一**：ECB 与纽约联储每工作日更新；美联储 H.10 每周一发布上周数据；BIS 视国家滞后几天到约三周。**每行数据都带日期，需要当天精确值时应回到央行本身核对。**
- **地域限制**：中国大陆服务器直连 Yahoo / SEC 可能不稳定，作者建议走代理或依赖东财 / 新浪 / 腾讯。
- **期权层仅限美股**：港股期权不在 Yahoo 覆盖范围内，需要 HKEX 的付费数据源。
- **我只读了文档，没有实际取数**：端点能力、字段口径、限流实现均未验证；合规分级也是**作者基于条款的判断**（作者自己在 TPEx、EIA 现货价两处也标注了"our judgment"）。
- **免责声明**：只提供数据获取工具，不构成投资建议。

## 什么时候会再看

- 需要在 AI 助手里做**美股 / 港股取数**时：与 [a-stock-data](a-stock-data-A股全栈数据工具包.md) 配对安装。
- 想参考**"第三方数据源合规分级"**怎么写时：这是目前见过的、把"数据源条款 → 风险等级 → 是否提供抓取器"这条链路写得最完整的开源范例，尤其是"删掉一个已写好的功能"那个决定。
- 想了解**0DTE、VIX 期限结构、Form 4 / 13F、卖空数据**这类美股特色数据的口径与免费来源时。
- 想参考**"没有替代品的违规来源怎么办"**的处理方式时：保留代码 + 原文引用条款 + 明确责任归属。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 姊妹项目：[a-stock-data](a-stock-data-A股全栈数据工具包.md)——覆盖 A 股，两者可同时安装、互不冲突；本篇的合规分级写法更完整，可反向对照 A 股侧"没有分级"的现状。
- 同为美股方向的工具：[US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md)（风险监测面板，强调数据缺失如实标注）、[chinese-buy-us-stock-guide](chinese-buy-us-stock-guide-中国人投资美股指南.md)（开户、税务与合规流程）。
- 可作为数据层的助手端：[openclaw](openclaw-自托管个人AI助手.md)。

## 参考来源

- 项目仓库与 README：https://github.com/simonlin1212/global-stock-data
- 中文版 README：https://github.com/simonlin1212/global-stock-data/blob/main/README_zh.md
- 更新日志：https://github.com/simonlin1212/global-stock-data/blob/main/CHANGELOG.md
- 元数据（Star / Fork / 时间）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未安装运行，端点可用性与合规分级判断均未独立复核。
