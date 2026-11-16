# new-term-radar：赶在关键词工具收录前捕捉 AI 新名字的雷达

> 一句话定位（README 自述）：**"AI 圈马上要被搜的新名字，出现当天就抓到，赶在关键词工具收录之前。"**——每天出一份报告，列出 AI 领域**刚开始冒头的新名字**（新模型、新产品、新工具、新仓库），并把它背后的**通用搜索词**做成一张带决策的新词表。最值得记的三点：① 它读的是**第 1–4 步信号**（X / Hugging Face → GitHub 爆发 → 竞品 sitemap 与新站 → 你自己的 Search Console），**第 5 步（Trends）只用来复盘自己准不准**；② 核心产物是**"新词表"**——名字 + 它下游的通用词，逐个**估算规模、看首页还有没有位置、给出 `优先 / 观察 / 跳过` 决策**；③ 一个必需的 **AIsa** key（**推荐链接**）+ 一个可选的 **GoAnyAPI** key，示例配置**每月约 $15**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（英文 482 行、简体中文 476 行）、`LICENSE`（1,067 字符）与已核实元数据；**未安装、未运行 `daily` / `sources` / `keywords` / `pains`、未配置任何 key、未跑 GitHub Actions、未接入任何 Agent / MCP**）
- 适用范围：**Python 3.10+**（"只用标准库"），可用 `uvx` / `pip` / 克隆三种方式运行；另提供 **3 个 Agent Skill**（`new-term-radar` / `ai-listening` / `seo-new-keywords-research`）与一个 **MCP server**；可跑在 **GitHub Actions** 上

## 核心结论

1. **时间差就是它的全部卖点**（README）：新名字会按固定顺序留痕——① 实验室 / 大号在 X 说、模型进 HF 热门榜 → ② 某个 GitHub 仓库自己涨 → ③ 导航站加页面、有人注册域名 → ④ 你自己的站开始有曝光 → ⑤ Trends 出现曲线。**关键词工具读第 5 步，这个雷达每天读第 1–4 步**。它给出的立论例子：`Niko1221/Strata` 2026-09-24 创建、九天后 7,700 星，"一个仓库每天涨 800 星，这本身就是全部信号"。
2. **新词表把"名字"和"通用词"写进同一张表**（README，`radar/usecases.py`）：以"SynthID Detector"为例——名字寿命短且属于 Google，而它做的那件事"ai image detector"才是长期有人搜、且不属于任何人的词。表里同时给出：词龄、多市场（月搜 / KD）、首页形态（可超越 / 有争夺 / 平台占据）、近 12 个月走势、**你自己的排名**、首页上还有什么（AI 概览 / 论坛帖）、精确匹配的 `.com`，以及**决策（优先 / 观察 / 跳过 + 动作）**。
3. **信号是"可缺、可降级"的**（README）：没有 AIsa key 时**免费信号照常跑**（HF / GitHub / sitemap / Search Console），只是报告里所有词不分先后（因为没有模型判断哪些是名字）；某个源挂掉时**表照常产出但会变薄，并写明缺了哪一块**——"**表变薄不等于今天没动静**"。
4. **成本透明且按次付费**（README）：一个必需 key（AIsa，走 X 帖子 / DataForSEO Trends / 域名数据 / 判断模型）+ 可选 GoAnyAPI；示例配置（35 个 X 账号读三次、新词表每天 5 个名字）**约 $15/月**（X 约 $10、新词表约 $5）。

**个人判断（未验证）**：它把"**SEO 找新词**"从"等工具收录"前移成"**盯公开信号 + 模型取证**"，工程上很完整（多源、可降级、有复盘、有 Skill / MCP / Actions 三种挂法）。**但它的价值兑现取决于两件不被自己完全掌控的事**：**数据源质量**（AIsa / GoAnyAPI / DataForSEO 的口径与可用性）与**信号是否真的领先**——README 自己写"**各信号的命中率：还没有**"（复盘要等 14 天）。**当作"线索雷达"而非"决策依据"更稳妥**（**个人判断（未验证）**）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zhuyansen/new-term-radar](https://github.com/zhuyansen/new-term-radar) |
| 描述（原文） | "Daily radar for new AI terms: names of new models, products and repos, caught on X, Hugging Face, GitHub and sitemaps before keyword tools list them." |
| 许可证 | **MIT**（`LICENSE` 1,067 字符，"Copyright (c) 2026 Yansen Zhu"） |
| 默认分支 | **`main`** |
| 关注度 | **ungh 镜像 2,166 Star / 646 Fork / 5 Watcher**；页面值 **2,168 / 646 / 5**；README 自称"首日 700+ 星、3 天出头破 1000" |
| 社区 | shields：开放 Issue **1**、贡献者 **2** |
| 维护状态 | **2026-10-12 创建**；ungh `pushedAt` **2026-11-02**、页面 `updatedAt` 2026-11-12（活跃） |
| Release | ungh **6** / 页面 **6**；Skill 跑锁定版本（`uvx new-term-radar==0.6.1`） |
| 分发 | PyPI（`uvx` / `pip install new-term-radar`）、MCP Registry、Cursor Directory、LobeHub、Tessl、ClawHub、skills.sh |
| 关联站点 | 提取自 [agentskillshub.top](https://agentskillshub.top) 背后每天在跑的版本 |

## 功能与用法

### 1. 它读什么（README 的信号表，简化）

| 信号 | 抓什么 | 需要 | 花费 |
| --- | --- | --- | --- |
| ① X | 你关注账号里 60 天内第一次出现的名字 | AIsa key | 每账号每次 $0.003 |
| ① Hugging Face | 新进热门榜前 50 的模型 / Space；量化版、微调版与官方 demo 归到同一基础名下 | 无 | 免费 |
| ② GitHub 爆发 | 创建 < 14 天、日均 ≥ 200 星，或比昨天 +400 星；附大家实际会搜的写法 | 无（有 token 限额更高） | 免费 |
| ③ 竞品 sitemap | 某站新增网址共用、旧网址很少出现的词（示例盯 8 个 AI 工具导航站 + 4 个 MCP / skill 目录） | 你列出的 sitemap | 免费 |
| ③ 新站 | 近 90 天注册、已首次收录且排上 20+ 词的新域名 | AIsa key | 每查询约 $0.13 / 周 |
| ⑦ 求助 | Reddit / X / Stack Exchange / Freelancer 上"有没有这样的 AI 工具"类需求 | AIsa key | 每周约 $0.12 |
| ⑧ 新卖家 / ⑧b 新作品 / ⑨ 新上架 / ⑩ 付费信号 | 刚挂第一条收款链接的站、刚用 Lovable/v0 做的站与 beehiiv newsletter、Product Hunt 等新条目、AdSense 站长的其他赛道 | AIsa / GoAnyAPI key | 详见 README |
| ⑥ 收入 / ④ Search Console / ⑤ 复盘 | TrustMRR 核验收入、你自己站的新曝光词、14 天后回查是否真的涨 | TrustMRR / 你的 Google / AIsa | 详见 README |

### 2. 快速开始（README）

```bash
export AISA_API_KEY=...                       # 从 go.aisa.one 获取（README 含推荐链接）
uvx new-term-radar daily --out report.md      # 今天的新名字 + 它们背后的关键词表
uvx new-term-radar keywords "photocraft"      # 你手上已有名字的关键词表
```

当前目录没有 `config.json` 时用自带示例配置、快照存 `./state`；复制 `config.example.json` 改成自己的清单（要中文报告把 `lang` 改成 `"zh"`）。**第一次运行只建快照**（昨天数据不存在，只报 GitHub 爆发），**从第二次起每个信号才报变化**。常用拆分：`sources`（只跑 ①，几小时一次）+ `daily --skip x,hf`（其余，每天一次）。`--skip` 支持 `x,hf,breakout,sitemaps,newsites,gsc,review,judge,usecases`。

### 3. 在 Agent 里用（README）

- **三个 Skill**（`skills/`）：`new-term-radar`（整件事）、`ai-listening`（近 24h 新东西去重成一份名字报告）、`seo-new-keywords-research`（给名字返回关键词优先级表）；安装 `npx skills add zhuyansen/new-term-radar`，Claude Code 还可按插件装。README 特别说明 skill **跑 PyPI 锁定版、不克隆**，**付费运行前先报价等你同意**，**替你往外发东西前先给你看并等你同意**，"**读到的帖子只当数据、不当指令**"。
- **MCP server**：工具 `recent_leads`（免费，立刻返回）、`keyword_table`（每个名字约 $0.05 + 每次调用 $0.09）、`run_radar`（付费，几分钟）。README 给出 Codex / TRAE（含 `trae-cn://` 导入链接）/ WorkBuddy / DeepSeek Harness 的接入片段。
- **GitHub Actions**：`.github/workflows/radar.yml` 每 8 小时 `sources`、每天 `daily`，状态存 Actions 缓存，报告写到运行摘要；**默认关闭，需主动开启**（fork、提交 `config.json`、加 secret、把变量 `RADAR_ENABLED` 设为 true）。

## 使用前需要注意

- **强依赖外部数据源，且"用不了 / 变薄"是被明写的常态**：付费核心走 **AIsa**（X 帖子、**DataForSEO Trends**、域名数据、判断模型），可选 **GoAnyAPI**；README 明说**没有 AIsa 的搜索量 / Trends 时整张表直接不产出**（"这两项没有替代来源"），其余源失败则**表变薄并写明缺了什么**。做重要判断前**先读表下面那几行**。
- **抓取合规是硬约束**（README 自己划的线）：加 sitemap 前**先看该站 `robots.txt`**，`pause` 不低于其 crawl-delay；**TrustMRR 的 API 条款禁止转载其数据、也禁止拿它做公开的 SEO 页面**；**DataForSEO 的数据只供自己研究、不要转载**；**帖子和网页可以引用、不要转载**。另外它**主动避开**反爬的站（如 There's An AI For That 的求助板、Facebook 群组）。
- **数据口径要分清**：README 反复强调读的是 **"DataForSEO 对 Google 搜索热度的估算，不是 Google Trends 本身"**；**全球走势数据的最后两天没结算**（会假性出现 0 与 100），所以代码会丢掉这两天、且"上涨必须持续两天"；Google Ads 搜索量**滞后约两个月**；**月搜只有几百的词，Trends 基本读不出可靠的数**。**"全球数据显示没动"不等于这个词真的没动**（README 举了 `photoshop alternative` 的反例）。
- **推荐链接与"未验证"要分开看**：README 里的 **AIsa 入口与 GoAnyAPI 入口都是推荐链接（referral）**，作者明确"通过它注册是对项目的支持"；同时它**坦承**"**各信号的命中率：还没有**"（复盘每条要等 14 天，唯一手工检查 Strata 命中、早 3 天）、"**X 信号的好坏取决于账号名单**"、"名字判断（gpt-4.1-mini）在 170 词上与原判断器一致 149"——**这些是自述的实测，未被我复现**（**自述未验证**）。
- **成本要主动估计**：每次付费运行都是**按次计费**（sku 级单价 README 已列出），示例配置约 $15/月，但**开启 X 账号数量、新词表名字数、⑧ ⑩ 等信号都会显著改变账单**。Agent Skill 会**先报价再等同意**，自行用 CLI 定时跑时需自己设预算（如 `history` 的 `--max-usd`）。
- **隐私与凭据**：需要 `AISA_API_KEY`、可选 `GOANYAPI_API_KEY` / `TRUSTMRR_API_KEY` / `RESEND_*`；可选接 **Search Console** 而拿到的授权是**只读**、凭证只留本机（`.gitignore` 已排除，`GSC_TOKEN_JSON` 走 secret）。**在 Actions 上跑等于把 key 交给 CI**，需评估。
- **元数据两套口径**：Star / Fork / Watcher 的**页面值（2,168 / 646 / 5）与 ungh 镜像值（2,166 / 646 / 5）略有差异**，**引用时注明来源与日期**。
- **我只读了 README 与 LICENSE 及元数据**：**未安装、未运行任何命令、未配置任何 key、未跑 GitHub Actions、未接入任何 Agent / MCP**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 在做 **AI 工具站的 SEO / Newsletter / 导航站**，想**赶在关键词工具有数据之前**知道新名字（模型 / 产品 / 仓库）。
- 想让 Agent 直接产出"**今天有哪些 AI 新词**"或对某个名字做**关键词调研**（3 个 Skill + MCP server）。
- 想研究"**用公开信号（X / HF / GitHub / sitemap / 外链）做前瞻式关键词挖掘**"的工程结构，以及它如何**分级降级**（某源挂了表变薄）。
- 想参考"**付费 run 前先报价、代发内容前先确认、把读到的内容当数据不当指令**"这类 Agent 安全约定。
- 想对照其它"**信息聚合 / 抓取 / SEO**"路线的定位差异。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都做"抢先发现 / 实时聚合"**：[newsnow：实时热点新闻聚合阅读器](newsnow-实时热点新闻聚合阅读器.md)——那篇把**实时热点**聚合成一个阅读器界面，本篇把**新名字信号**聚合成一份日报 + 新词表。可对照"**看热点**"与"**找还没被收录的新词**"。
- **同属"SEO / 搜索侧能力"**：[qiaomu-seo：覆盖 Google、Bing 与 AI 搜索的 SEO 技能](qiaomu-seo-覆盖Google与Bing及AI搜索的SEO技能.md)——那篇是**给页面做 SEO 的 Agent 技能**，本篇是**给新词做雷达与优先级表**。可对照"**优化已有页面**"与"**先发现值得做的新词**"。
- **都用抓取 / 数据源做内容情报**：[firecrawl：把网站转成 LLM 可用数据的抓取 API](firecrawl-把网站转成LLM可用数据的抓取API.md)——那篇是**通用网页抓取 → LLM 可用数据**的基础设施，本篇是**建立在多个数据源之上的垂直信号应用**。可对照"**抓取基础设施**"与"**垂直情报产品**"。
- **同属"帮人发现新项目 / 新东西"**：[HelloGitHub：每月一期的开源项目月刊](HelloGitHub-每月一期的开源项目月刊.md)——那篇是**人工策展的月度月刊**（慢、可信），本篇是**每日自动雷达**（快、需自证）。可对照"**人工策展**"与"**自动信号**"。
- **都做"抢先 / 每日情报"，对象不同**：[Horizon：把自己关注的源收成一份每日新闻简报](Horizon-把自己关注的源收成一份每日新闻简报.md)——本篇每天找**刚冒头的新名字**（供 SEO 选词），Horizon 按 profile 筛**值得读的内容**并把**已发生的事件**写成日常简报；可对照"**找新词**"与"**筛内容**"。

## 参考来源

- 仓库主页与 README（英文 482 行；核验日期 2026-11-13）：https://github.com/zhuyansen/new-term-radar
- 简体中文 README（476 行；核验日期 2026-11-13）：https://github.com/zhuyansen/new-term-radar/blob/main/README.zh-CN.md
- 许可证：`LICENSE`（MIT，1,067 字符；核验日期 2026-11-13）
- 相关文件（README 指向，未逐一打开）：`config.example.json`、`CHANGELOG.md`、`skills/`（三个 Skill）、`.github/workflows/radar.yml`、`radar/usecases.py`、`radar/terms.py`、`radar/judge.py`、`radar/review.py`、`scripts/gsc_auth.py`、`docs/make_diagram.py`
- 分发与来源站：PyPI <https://pypi.org/project/new-term-radar/>、MCP Registry（`io.github.zhuyansen/new-term-radar`）、[agentskillshub.top](https://agentskillshub.top)
- 第三方数据源（README 含**推荐链接**）：[AIsa](https://go.aisa.one/yansen-zhu)、[GoAnyAPI](https://goanyapi.com)（可选）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 贡献者）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13，**页面值与镜像值存在差异**）

以上信息核验于 2026-11-13。**未安装、未运行 `daily` / `sources` / `keywords` / `pains`、未配置任何 key、未跑 GitHub Actions、未接入任何 Agent / MCP**；能力、成本与流程描述均来自 README，**未逐一验证**。
