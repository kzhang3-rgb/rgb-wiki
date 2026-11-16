# Horizon：把自己关注的源收成一份每日新闻简报

> 一句话定位（README 自述）：**"Enjoy the News itself. Leave others to Horizon"**——用 AI 把你订阅的源（RSS / Hacker News / Reddit / Telegram / X / GitHub / 金融新闻等）**收集、过滤、去重**，再按 **profile**（一套可复用的编辑规则）决定"什么属于这里、什么值得留、写什么"，产出**中英双语日报**，可发布到 GitHub Pages、发邮件、推 Webhook 或微信。最值得记的三点：① 核心抽象是 **profile**——它把"编辑口味"写成配置（Markdown 提示词 + JSON 块定义），**改规则通常不用动 Python**；② 它同时是**给人跑的 CLI 流水线**和**给 AI 助手用的东西**（MCP server + Agent Skill，`npx skills add Thysrael/Horizon --skill horizon -g`）；③ **MIT**、Python 99.7%、README 里赞助位与引流链接较多（须与技术内容分开看）。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README（英文 398 行）、`LICENSE`（MIT，1,065 字节）与已核实元数据；**未 clone、未安装 `uv`、未跑 `horizon` / `horizon-wizard`、未配置任何模型 key、未跑 GitHub Actions、未打开 Live Demo**）
- 适用范围：**Python**（README 用 `uv sync` 安装，另有 `pip install -e .`）或 **Docker**；需要一个 AI 提供商的 key（README 徽章列出 Claude / GPT / Gemini / DeepSeek / 豆包 / MiniMax / Ollama / OpenClaw 等）；自动化另需 GitHub Actions；Agent Skill 需 **Node.js 22.20+**

## 核心结论

1. **它解决"好内容散落在各处、注意力有限"**（README "Why Horizon?"）：把 feeds / 论坛 / 时间线里的内容**收集 → 过滤 → 去重**，再连同**背景与社区讨论**一起送到你面前。
2. **Profile 是它最核心的抽象**（README）：一个 profile = **一套可复用的编辑规则**——"什么属于这里、什么值得留、写什么"。内置三个示例：

   | 你关注什么 | Profile | 你得到什么 |
   | --- | --- | --- |
   | 科技新闻 | `tech-news` | 事件与背景，必要时附影响与社区讨论 |
   | 工程深潜 | `tech-blog` | 背景、方案与可复用要点 |
   | AI 创作素材 | `ai-creator` | 摘要，必要时附时效钩子与创作角度 |

   一个源可以**显式绑定** profile，也可设 `"auto"` 让 AI 匹配（或给数组把匹配限定在几个 profile 内）；**改 profile 通常不用改 Python**（README 指向 `docs/profiles.md`）。
3. **流水线顺序固定**（README）：每个条目**路由到一个 profile** → **分析、过滤、去重先于增强（enrichment）** → 选中的条目**按 profile 分组**成为简报。全流程可走 CLI，也可让 **AI 助手通过 MCP 调用各阶段**（README 指向 `src/mcp/README.md`）。
4. **输入面很宽**（README 支持源表）：Hacker News（可带 top N 评论）、任意 RSS/Atom、Reddit（+ 评论）、Telegram 公开频道、Twitter/X（经 Apify，+ 回复）、GitHub 用户事件与仓库 Release、OpenBB 金融新闻（可选 extra）、OSS Insight、GDELT、Google News。
5. **输出面也宽**（README 投递表）：**GitHub Pages 日报站**（把生成的 Markdown 拷进 `docs/` 发布）、**邮件订阅**（SMTP/IMAP，含订阅与退订）、**Webhook**（飞书 / Lark、钉钉、Slack、Discord 或自定义端点）、**微信**（经 iLink Bot 扫码登录后发送，有微信侧回复限制）。
6. **有"均衡简报"机制**（README `digest` 段）：用 `max_items` 限制总条数、用 `category_groups` 给各类别设上限，**避免某个热门话题挤掉其它类别**；组限在 **profile 过滤之后、增强之前**生效；三项都不写则不施加均衡限制。

**个人判断（未验证）**：把"每日简报"做成"**源 → profile 规则 → 分级过滤 → 多通道分发**"的完整闭环，且规则用提示词 + JSON 配置表达、改规则基本不写代码——这套"**把编辑口味写成配置**"的设计是本条目的主要价值。**但产出质量取决于两个外生条件**：你选的源，以及你配的模型与 key（README 把模型 provider 做成可换的选项，说明质量与成本会随 provider 而变）。**当作"信息采集与筛分的脚手架"而非"内容质量保证"更稳妥**（**个人判断（未验证）**）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Thysrael/Horizon](https://github.com/Thysrael/Horizon) |
| 描述 | "📡 Your own AI-powered news radar. Generates daily briefings in English & Chinese. \| 用 AI 构建你专属的新闻雷达" |
| 许可证 | **MIT**（`LICENSE` 1,065 字节，Copyright (c) 2026 Thysrael） |
| 主语言 | **Python 99.7%**（shields `languages/top`，2026-11-13） |
| 关注度 | **页面 14,024 Star / 34 关注 / 2,010 Fork**（2026-11-13）；ungh 镜像 **13,865 / 34 / 1,968**（**两套口径差异约 1%，引用时注明来源**）；shields 另报**开放 Issue 2、贡献者 45** |
| 维护状态 | **2026-02-20 创建**；pushed **2026-11-12**（活跃）；默认分支 `main`；**0 Release**（README "Project Status" 明写 "Publish releases on GitHub" 与 "Publish the package to PyPI" 仍属计划中） |
| 体积 | **213 个文件**（ungh `files/main`） |
| 站点 | 官网 https://www.horizon1123.top/ ；Live Demo 与配置指南（GitHub Pages）https://thysrael.github.io/Horizon/ |
| 安装 | `uv sync`（推荐，另可用 `uv sync --extra dev` / `--extra openbb`）或 `pip install -e .`；也可用仓库自带 `docker compose` 构建运行 |
| 社区 | QQ 群 1106121909；投稿源经 https://horizon1123.top |

## 功能与用法

### 1. 安装（README 三选一）

```bash
# 本地安装
git clone https://github.com/Thysrael/Horizon.git
cd Horizon
uv sync                       # 推荐（pip 也可：pip install -e .）
uv sync --extra dev           # 需要 pytest 等开发依赖时

# Docker
docker compose build --build-arg EXTRAS=openbb horizon
```

- `trafilatura` 全文抽取已含在基础安装里；`twitter` extra 还需要 Playwright 浏览器与系统包，**当前 Dockerfile 并未安装**。
- 可选 `openbb` extra 无可用 wheel 时，README 给了只装二进制的方式：`uv pip install --only-binary=:all: openbb openbb-benzinga`。

### 2. 配置（README）

- **交互式向导（推荐）**：`uv run horizon-wizard`——问你的兴趣（如 "LLM inference"、"嵌入式"、"web security"）后**自动生成 `data/config.json`**。符合条件的 ChatGPT 订阅用户可选 `openai-chatgpt`，**通过 Sign in with ChatGPT 用订阅额度而不需要 OpenAI API key**。
- **手工配置**：`cp .env.example .env` 放真实密钥，`cp data/config.example.json data/config.json` 改源清单。最小配置示例（README）：

```jsonc
{
  "ai": { "provider": "openai", "model": "gpt-4", "api_key_env": "OPENAI_API_KEY" },
  "sources": {
    "rss": [
      { "name": "Simon Willison", "url": "https://simonwillison.net/atom/everything/", "profile": "tech-news" }
    ]
  },
  "processing": {
    "profiles_dir": "profiles",
    "default_profile": "tech-news",
    "profile_settings": { "tech-news": { "threshold": 7.0, "topic_dedup": true } }
  }
}
```

- **要点**：`api_key_env` 必须是**环境变量名**而非密钥本身；`data/config.json` 里任何字符串都可用 `${VAR_NAME}` 引用环境变量（适合 `ai.base_url`、私有 RSS 地址、Webhook 端点、自定义 header）；**每个 profile 的分数阈值、话题去重等用户偏好写进 `processing.profile_settings`，而不是 profile 文件**。Gemini 用 `GOOGLE_API_KEY`。

### 3. 运行（README）

```bash
uv run horizon [OPTIONS]                          # 本地
docker compose run --rm horizon [OPTIONS]         # Docker
```

| 选项 | 默认 | 说明 |
| --- | --- | --- |
| `--hours N` | 24 | 取最近 N 小时 |
| `-d` / `--data-dir PATH` | `data` | 数据目录（连带影响摘要、订阅者与默认配置位置） |
| `-c` / `--config PATH` | `<data-dir>/config.json` | 仅改配置文件路径 |
| `-l` / `--log-level LEVEL` | `WARNING` | DEBUG / INFO / WARNING / ERROR / CRITICAL |

报告写到 `data/summaries/`（设了 `--data-dir` 则在其下）。

### 4. 自动化与给 Agent 用（README）

- **GitHub Actions**：仓库里带一份 **被禁用**的 `daily-summary.yml.disabled` 模板，配置好你的部署后**重命名成 `daily-summary.yml`** 才启用。
- **MCP server**：工具清单见 `src/mcp/README.md`，客户端接入见 `src/mcp/integration.md`。
- **Agent Skill**：`npx skills add Thysrael/Horizon --skill horizon -g`（需 Node.js 22.20+），装完后可直接让助手配置源、改 profile、运行 Horizon；另有 Skill ZIP 下载（`https://thysrael.github.io/Horizon/downloads/horizon-skill.zip`）。

### 5. 文档目录（README 表格）

`docs/configuration.md`（AI 提供商 / 源 / profile / 过滤 / 邮件 / Webhook / 微信 / Pages / MCP）、`docs/profiles.md`（profile 路由、提示词、运行时过滤偏好、增强块与工具）、`docs/scoring.md`（如何评估与排序）、`docs/scrapers.md`（源抓取器与扩展）、`docs/extractors.md`（RSS 全文抽取）、`src/mcp/README.md`、`docs/agent-skill.md`。

## 使用前需要注意

- **需要自备模型 key，且成本与质量随 provider 变化。** README 把"用哪家模型"做成选项（含 Ollama 这类本地模型与 Sign in with ChatGPT 这条免 API key 的路），**这意味着产出质量与花费并非项目固定，而由你的配置决定**。
- **邮件订阅要你自己的 SMTP/IMAP 凭据，微信经第三方 iLink Bot（有回复限制），X 源经 Apify（第三方付费服务）。** 这些依赖链在 README 里都只指向配置文档，**未说明各服务自身的条款与计费**。
- **README 未讨论各数据源的抓取条款与频控风险。** 它重点在配置、部署与投递；至于"抓这些源的公开内容是否合规、会不会触发频控"，**文档里没有对应章节**，属使用者需自担的判断（**这是我的核对观察，非项目声明**）。
- **0 Release，只能跟 `main`。** README 自己把"发布 Release"和"上 PyPI"列在计划中，**当前没有版本化发行物可锁**。
- **README 内嵌多个赞助位与引流链接**（Modelflare / Compshare / OfoxAI / AstraFlow / APIXO，均带来源参数）。**这些是商业推广，与技术内容须分开，不构成第三方背书。**
- **我只读了 README 与 LICENSE 及元数据**：**未 clone、未安装 `uv`、未跑 `horizon` / `horizon-wizard`、未配置任何模型 key、未跑 GitHub Actions、未打开 Live Demo / 官网**；上述能力、流程与选项均来自 README，**未逐一验证**。

## 什么时候会再看

- 想搭一个"**只看我关心的事**"的每日简报（而非通用热榜），并希望**用配置表达编辑口味**时。
- 想给 AI 助手接一个"**读我的源 → 排序去重 → 出简报**"的能力（MCP / Skill）时。
- 想参考"**多条源 → profile 规则 → 分级过滤（阈值 / 去重 / 均衡）→ 多通道分发（Pages / 邮件 / Webhook / 微信）**"这条完整流水线的组织方式时。
- 想对照"**环简报到 Agent**"的两种接法（CLI 流水线 vs MCP + Skill）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"把信息聚成一份可读产物"，取向不同**：[newsnow：实时热点新闻聚合阅读器](newsnow-实时热点新闻聚合阅读器.md)——那篇把**各平台实时热榜**聚成一个界面（给人读的实时聚合），本篇按**你的 profile 规则**把**你选的源**筛成**每日双语简报**；两者都自带 MCP 出口，可对照"**看全网热点**"与"**按口味筛订阅源**"。
- **都做"每日情报"，对象不同**：[new-term-radar：赶在关键词工具收录前捕捉 AI 新名字的雷达](new-term-radar-每天抢先捕捉新AI名词的雷达.md)——那篇每天找**刚冒头的新名字**（供 SEO 选词），本篇按 profile 筛**值得读的内容**并把**已发生的事件**写成简报。
- **同属"把外部内容变成可用数据"，层级不同**：[firecrawl：把网站转成 LLM 可用数据的抓取 API](firecrawl-把网站转成LLM可用数据的抓取API.md) 是**通用抓取基础设施**（任意网页 → LLM 可用数据），本篇是**建立在多种源之上的简报应用**（含过滤、去重、排序与投递）。
- **都做"给 Agent 读的内容入口"**：[lexfridman-cn：Lex Fridman 播客中文站](lexfridman-cn-LexFridman播客中文站.md)——那边是**一个站点**把内容结构化（`llms.txt` / 全文 RSS / JSON-LD + 自建 MCP）供 Agent 取用，本篇是**一条流水线**把多源内容加工后经 MCP / Skill 交给 Agent。
- **同属"信息汇聚 + 研判/筛选"，深度不同**：[BettaFish：多 Agent 舆情分析助手微舆](BettaFish-多Agent舆情分析助手微舆.md)——那篇是**多 Agent 做舆情分析**（研判），本篇是**单条流水线做个人简报**（筛选与摘要）。
- **都叫"个人 AI"，但一个是助手本体、一个是单一用途流水线**：[talorys：部署在自己 Cloudflare 账号里的个人 AI 助手](talorys-部署在自己Cloudflare账号里的个人AI助手.md)——talorys 把聊天 / 记忆 / 任务 / 笔记 / 提醒整套装进一个应用，Horizon 只把"**新闻简报**"这一件事做到闭环。

## 参考来源

- 项目仓库与 README（英文 398 行；核验日期 2026-11-13）：https://github.com/Thysrael/Horizon
- 许可证：`LICENSE`（MIT，1,065 字节；核验日期 2026-11-13）
- 官网：https://www.horizon1123.top/ ；Live Demo / 配置指南：https://thysrael.github.io/Horizon/
- 相关文件（README 指向，未逐一打开）：`docs/configuration.md`、`docs/profiles.md`、`docs/scoring.md`、`docs/scrapers.md`、`docs/extractors.md`、`docs/agent-skill.md`、`src/mcp/README.md`、`src/mcp/integration.md`、`.github/workflows/daily-summary.yml.disabled`、`CONTRIBUTING.md`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 主语言 / 文件数 / 贡献者）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13，**页面值与镜像值存在差异**）

以上信息核验于 2026-11-13。**未 clone、未安装 `uv`、未跑 `horizon` / `horizon-wizard`、未配置任何模型 key、未跑 GitHub Actions、未打开 Live Demo / 官网**；能力、选项与流程描述均来自 README，**未逐一验证**。
