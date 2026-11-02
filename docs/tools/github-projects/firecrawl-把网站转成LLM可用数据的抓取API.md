# firecrawl：把网站转成 LLM 可用数据的抓取 API

> 一句话值得记住的取舍：它把"给 AI 喂网页"拆成**一组明确的端点**（Search / Scrape / Interact / Agent / Crawl / Map / Batch），输出统一是 **Markdown、结构化 JSON 或截图**——但主仓库是 **AGPL-3.0**，自建或二次分发前必须看清许可。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、仓库页与 GitHub API 元数据，未注册、未调用 API）
- 适用范围：要把网页内容喂给 LLM / Agent；有公开核心 + 托管服务的开源核心（open-core）形态

## 核心结论

firecrawl 的定位是 **"给 AI 用的网页数据接口"**：不自己写爬虫、不处理反爬、不处理 JS 渲染，直接拿到干净可喂模型的文本或结构化数据。它同时提供**开源仓库**与**托管服务**，README 第一句就写明 "Open source and available as a hosted service"。

它把能力收敛成几个端点，比"一个大而全的爬虫"更好判断用哪个：

| 端点 | 一句话职责 |
| --- | --- |
| **Search** | 搜网页并直接返回结果的完整正文（含 markdown） |
| **Scrape** | 把任意 URL 转成 markdown / HTML / 截图 / 结构化 JSON |
| **Interact** | 先 scrape 拿到 `scrape_id`，再用自然语言或代码操作该页面（搜索、点击、导航） |
| **Agent** | 只描述你要什么、**不必给 URL**，由它自己去找；README 说它是 `/extract` 的演进 |
| **Crawl** | 一次请求抓整站（异步 job，需轮询；SDK 会自动轮询） |
| **Map** | 秒级列出站点所有 URL，可按关键词排序 |
| **Batch Scrape** | 异步批量抓成千上万个 URL |

其中 **Agent 支持 schema 结构化输出**（如 Pydantic 模型），并可选两个模型：自称默认的 `spark-1-mini`（"便宜 60%"）与 `spark-1-pro`（复杂研究、跨站对比）。

接入方式对 Agent 友好：既能用 **MCP**（`npx -y firecrawl-mcp`，配 `FIRECRAWL_API_KEY`），也能一键装 **Skill / CLI**（`npx -y firecrawl-cli@latest init --all --browser`，README 称支持 Claude Code、Antigravity、OpenCode 等）。SDK 有 Python（`firecrawl-py`）、Node（`@mendable/firecrawl-js`）、Java。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) |
| 描述 | "🔥 The API to search, scrape, and interact with the web for AI" |
| 许可证 | **AGPL-3.0**（GitHub API 与根 `LICENSE` 文件一致）；**SDK 单独为 MIT**（`LICENSE` 文件提交信息为 "Update SDKs to MIT license"），两者不同，二次使用需分别确认 |
| 语言 | TypeScript |
| 关注度 | **119,634 Star、7,369 Fork、354 关注、320 个开放 Issue**（GitHub API 口径，`open_issues_count` 含开放 PR，2026-11-02） |
| 维护状态 | 2024-04-15 创建；GitHub API 显示最后推送 **2026-05-14**；仓库页显示最新提交 2026-05-06、约 5,410 次提交 |
| 规模 | 页面显示 **1,565 分支、39 Tag**；体积约 89,156 KB（约 87 MB） |
| 官网 / 托管 | https://firecrawl.dev （playground 可试用） |

## 用法要点

官方主推托管服务：先到 firecrawl.dev 注册拿 API key，再用 SDK 或 cURL。

```python
from firecrawl import Firecrawl

app = Firecrawl(api_key="fc-YOUR_API_KEY")

result = app.scrape("firecrawl.dev", formats=["markdown"])   # 单页转 markdown
docs   = app.crawl("https://docs.firecrawl.dev", limit=50)   # 整站抓取（SDK 自动等待）
hits   = app.search("firecrawl", limit=5)                    # 搜索并返回正文
found  = app.agent(prompt="Find the founders of Stripe")      # 不给 URL，自主查找
```

`Interact` 的用法是"先抓后操作"——同一次抓取产出的 `scrape_id` 复用，再逐步发指令（如"搜索 mechanical keyboard"→"点第一个结果"）。

README 里**自托管**也存在：仓库含 `SELF_HOST.md` 与 `docker-compose.yaml`，也就是可以脱离托管服务自己部署。

## 使用前需要注意

- **许可证是最大的判断点（事实）**：主仓库为 **AGPL-3.0**。这是强传染性许可——如果你**修改后通过网络对外提供服务**，通常要按 AGPL 开放对应源码。而 **SDK 是 MIT**（据 `LICENSE` 提交信息），两者不同，别把"SDK 是 MIT"当成"整个项目可随意用"。
- **它是 open-core**：托管服务是商业主体，公开仓库只是核心部分。我在核验时正好观察到一处需要留意的现象——**GitHub API 显示最后推送为 2026-05-14，距核验日（2026-11-02）已近 6 个月**，而仓库页显示最新提交停在 2026-05-06。**这是"公开仓库推送节奏与托管服务不同步"的一种可能解释（个人判断，未验证）**；遇到需要判断"是否还在活跃维护"时，应交叉看官网/文档/Release，而不是只看这个仓库的推送时间。
- **两处 Issue 数字差距很大，不要混用**：仓库页显示 **Issues 76**（纯 Issue 口径），而 GitHub API 的 `open_issues_count` 为 **320**——后者把**开放 PR 也算进去**，且两次抓取时间不同。按本项目约定，**以 API 值为准并标明口径**，两者不当同一份数据使用。
- **性能与覆盖率是作者口径**：README 称"覆盖 96% 的网页""P95 延迟 3.4s"。这是**作者自述的营销数据，我未独立验证**。
- **抓取的合规边界由使用者负责**：轮换代理、绕过 JS 阻塞等能力意味着"技术上能抓"不等于"被允许抓"；目标站点的 robots、服务条款与所在地法律仍需自行遵守。
- **我只读了 README、仓库页与元数据**：未注册账号、未获取 key、未实际调用任何端点。

## 什么时候会再看

- 需要**给 Agent 接一个"读网页"的稳定入口**时：Search / Scrape / Crawl / Map 的分工比自建爬虫省事，输出直接是 markdown。
- 需要**"只描述需求、自动找数据"**（Agent 端点）而不想先知道 URL 时。
- 评估**open-core + AGPL 的取舍**时：这是"想用托管省事 vs 想自建但受许可约束"的典型案例。
- 需要参考**给 AI 用的数据接口怎么设计端点边界**时（把"搜/抓/操作/整站/批量"拆成独立名词）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **"把内容转成 LLM 可用格式"的同族对照**：[markitdown](markitdown-文件转Markdown工具.md)（本地文件转 Markdown）、[bb-sites](bb-sites-把网站变成命令行指令的适配器集.md)（在已登录浏览器里读站点）。
- **Agent / MCP 接入生态**：[servers](servers-MCP官方参考服务器合集.md)、[java-sdk](java-sdk-MCP官方JavaSDK.md)、[agents.md](agents.md-给编码助手用的AGENTS文件格式.md)。
- **许可与开源形态对照**：[indicator](indicator-Go技术指标与回测框架.md)（AGPLv3 + 商业双许可）、[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)（MIT 主体 + GPLv3/AGPL-3.0 子模块）。

## 参考来源

- 项目仓库与 README：https://github.com/firecrawl/firecrawl
- 托管服务与 playground：https://firecrawl.dev
- MCP 服务器：https://github.com/firecrawl/firecrawl-mcp-server
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README、仓库页与元数据；未注册、未调用 API。"96% 覆盖率""P95 3.4s"等为作者自述，未验证；公开仓库最后推送时间（2026-05-14）与官网服务是否同步，未独立验证。
