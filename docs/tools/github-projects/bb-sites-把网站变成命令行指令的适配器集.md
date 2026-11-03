# bb-sites：把网站变成命令行指令的社区适配器集

> 记录它的核心机制（脚本跑在你已登录的浏览器里，不碰 API Key 也不绕反爬），以及由此带来的合规与账号风险。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README、中文版说明与 GitHub API 元数据，未安装 bb-browser、未运行任何适配器）
- 适用范围：依赖上游 [bb-browser](https://github.com/epiral/bb-browser)（JavaScript）；适配器在你的浏览器会话内执行

## 核心结论

[bb-browser](https://github.com/epiral/bb-browser) 的**社区站点适配器集**，一句话概括它的机制：**把网站变成 CLI 命令**。

关键实现方式是——**每个适配器就是一个 JS 函数，通过 `bb-browser eval` 跑在你自己的浏览器里**。README 用三个否定短语说明了它为什么"简单"：

> The browser is already logged in — **no API keys, no cookie extraction, no anti-bot bypass**.
> （浏览器本来就已经登录了——不需要 API Key，不需要提取 Cookie，不需要绕过反爬。）

这个思路的巧妙之处在于：**登录态、验证码、指纹、反爬对抗这些麻烦事都不需要处理，因为你用的就是人本来在用的那个浏览器会话。** 代价也很直接：**它本质上是把你人工浏览的行为自动化**，所以合规定位、账号风险、以及站点改版导致的失效，全都得自己承担。

README 自述规模：**102 个适配器、覆盖 36 个平台**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [epiral/bb-sites](https://github.com/epiral/bb-sites) |
| 上游依赖 | [epiral/bb-browser](https://github.com/epiral/bb-browser)（本仓库只是适配器集合） |
| 许可证 | **API 未检测到（`/license` 返回 404，根目录无 LICENSE 文件）** |
| 语言 | JavaScript |
| 关注度 | 636 Star、128 Fork、**2 关注**、49 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2026-03-14 创建，最近推送 2026-08-18（核验时约两个半月未更新） |
| 体积 | 约 379 KB（`size` 字段） |
| 文档 | README（英）、README.zh-CN.md（中）、SKILL.md、DESIGN.md |

## 快速开始

```bash
bb-browser site update                     # 安装/更新适配器
bb-browser site list                       # 列出可用命令
bb-browser site reddit/me                  # 运行
bb-browser site reddit/thread <url>        # 带参数运行
```

命令命名规则是 `平台/动作`，例如 `github/repo epiral/bb-browser`、`bilibili/search 编程`、`youtube/transcript dQw4w9WgXcQ`、`douban/top250`、`yahoo-finance/quote AAPL`。

## 覆盖范围（README 自述分类）

| 分类 | 代表平台与命令 |
| --- | --- |
| 搜索引擎 | Google / 百度 / Bing / DuckDuckGo 搜索；搜狗微信文章搜索（`sogou/weixin`） |
| 新闻媒体 | BBC（RSS 或搜索）、路透、今日头条（搜索 + 热榜）、东方财富财经新闻 |
| 社交媒体 | Twitter/X（用户、推文串、搜索、时间线、**通知**）、Reddit（用户、帖子、讨论树、评论链）、微博（7 个适配器，含手机版 7 个）、虎扑热帖 |
| 技术开发 | GitHub（用户、仓库、Issue、**开 Issue**、**提 PR**、fork）、Hacker News、Stack Overflow、CSDN、博客园、npm、PyPI、arXiv、Dev.to、V2EX |
| 影音娱乐 | YouTube（含 **transcript**、评论、频道、feed）、B 站 9 个适配器、IMDb、Genius、豆瓣（搜索/详情/排行榜/Top250/影评）、起点中文网 |
| 招聘求职 | BOSS 直聘（搜索 + JD 详情）、LinkedIn（主页 + 帖子搜索） |
| 金融 | 东方财富（**个股行情** + 新闻）、Yahoo Finance 报价 |
| 数码产品 | GSMArena 搜索、Product Hunt 今日榜 |
| 知识参考 | Wikipedia、知乎（用户/热榜/问答/搜索）、Open Library 图书搜索 |
| 生活出行 | 有道翻译、携程目的地搜索 |
| 社交应用 | 即刻（推荐流 + 搜索）、小红书（主页/feed/搜索/笔记/评论/用户帖子） |

### 与 README 清单不一致的地方

抓取仓库根目录时看到**平台目录数量明显多于 README 表格里列出的 36 个**——除了表格中的平台，还出现了 `36kr`、`instagram`、`linuxdo`、`openrouter`、`smzdm`、`sohu`、`xiaoyuzhoufm`、`xueqiu` 等目录（另有 `fixtures`、`test` 等辅助目录）。

**这说明 README 的覆盖清单很可能滞后于实际适配器**。判断依据是目录名本身，未逐个打开确认可运行；引用"支持哪些站点"时**应以 `bb-browser site list` 的实际输出为准**。

## 适配器长什么样

每个适配器是一个带 `@meta` 注释块的 JS 函数：

```javascript
/* @meta
{
  "name": "platform/command",
  "description": "What this adapter does",
  "domain": "www.example.com",
  "args": {
    "query": {"required": true, "description": "Search query"}
  },
  "readOnly": true,
  "example": "bb-browser site platform/command value1"
}
*/

async function(args) {
  if (!args.query) return {error: 'Missing argument: query'};
  const resp = await fetch('/api/search?q=' + encodeURIComponent(args.query), {credentials: 'include'});
  if (!resp.ok) return {error: 'HTTP ' + resp.status, hint: 'Not logged in?'};
  return await resp.json();
}
```

三个细节值得注意：`readOnly` 是**元数据里显式声明的字段**（说明存在非只读的适配器，见上表 `github/issue-create`、`github/pr-create`、`github/fork`）；`credentials: 'include'` 直接复用浏览器会话 Cookie；出错时会返回 `hint`（例如 `Not logged in?`）——**这是很实用的设计，把"为什么失败"一起返回**。

**私有适配器会覆盖社区同名适配器**，放在 `~/.bb-browser/sites/`：

```
~/.bb-browser/
├── sites/          # 你的私有适配器（优先级更高）
└── bb-sites/       # 本仓库（bb-browser site update 拉取）
```

## 站点适配的脆弱性（以小红书为例）

README 专门给小红书写了一节，恰好说明了这类适配器为什么容易失效：

- `xiaohongshu/me` **读取当前用户 store，而不是假设 `/user/me` 一定会被调用**；
- `xiaohongshu/search` 跳到真实搜索路由并等待当前 `search/notes` 响应，支持站点原生排序（`latest` / `likes` / `comments` / `collects`）；
- `xiaohongshu/note` 与 `comments` **需要有效的 `xsec_token`**：要么传完整笔记 URL，要么先调用 `feed` / `search` / `user_posts`，让当前会话把 token 缓存下来；
- `xiaohongshu/user_posts` 改为解析个人页 SSR 状态，"不再依赖旧的请求假设"。

README 还明说：小红书适配器现在混用 Pinia store 状态、页面内路由和 SSR 状态解析，**就是为了避开"已经不再稳定触发"的旧 XHR 路径**。

> 这段说明的普遍意义：**依赖站点前端内部实现的抓取，会和站点前端一起烂掉。** 49 个开放 Issue 对一个 636 Star 的仓库来说不算少，这与"适配器持续失效"是同一件事的两面。

## 使用前需要注意

- **合规与账号风险是首要问题**：适配器的本质是**在你已登录的会话里自动调用站点接口、执行站点操作**。这接近自动浏览/自动操作，**是否违反各站点服务条款需要逐个判断**；招聘类（BOSS 直聘、LinkedIn）、社交类（微博、小红书、Twitter/X）平台的自动化访问通常有更明确的限制。**账号被风控或封禁的后果由使用者承担**——README 没有给出合规声明，这一点与 [global-stock-data](global-stock-data-美股与全球市场数据工具包.md) 那种逐条标注条款的做法形成鲜明对比。
- **没有许可证文件**：`/license` 返回 404，根目录无 LICENSE，**默认按"保留所有权利"处理**，商用前需自行确认授权。
- **存在写操作适配器**：`github/issue-create`、`github/pr-create`、`github/fork` 会**真实产生副作用**。运行前先确认适配器的 `readOnly` 元数据；对写操作类命令，先在测试仓库上验证。
- **强依赖上游 bb-browser**：本仓库只是适配器集合，`site update` 拉取与执行都靠上游；上游接口变化会整体失效，而**本仓库自身已约两个半月未更新**。
- **登录态即是作用域**：脚本跑在你已登录的浏览器里，意味着**它能做的事和你自己手动能做的事一样多**。不要在不该登录的浏览器配置文件里执行未知来源的适配器，第三方适配器本质上是可以操作你账号的代码。
- **X（Twitter）等平台的登录态会过期**，`hint: 'Not logged in?'` 这类返回是排查起点。
- **我只读了 README 与目录，未安装上游、未运行任何适配器**：适配器数量、可用性、返回结构均未验证。

## 什么时候会再看

- 想让 **AI 助手/脚本读取某个网站的公开信息**，却不想处理登录与反爬时：先看这里有没有现成适配器。
- 想参考**"复用用户浏览器会话"这一思路的完整实现**时：`@meta` 元数据格式、`credentials: 'include'`、`readOnly` 声明、私有适配器覆盖机制，是这套方案的关键设计点。
- 想参考**"站点前端改版后适配器怎么改"**时：小红书那一节记录了从"依赖 XHR 路径"转向"Pinia store + 路由 + SSR 状态"的迁移过程。
- 需要**批量取中文平台的公开数据**（B 站、豆瓣、微博、知乎、V2EX）时：覆盖度是它相对同类项目的优势。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **两条取数路线的对照**：[a-stock-data](a-stock-data-A股全栈数据工具包.md) 与 [global-stock-data](global-stock-data-美股与全球市场数据工具包.md) 走的是"直连 HTTP 接口 + 自己管理限流与降级"，本篇走的是"在已登录浏览器里执行"——前者要处理封 IP，后者要处理站点改版与账号风险。三者的适配器/数据源覆盖也有重叠（东方财富、Yahoo Finance）。
- 同为多平台内容操作：[AiToEarn](AiToEarn-AI内容变现与多平台发布.md)——方向相反，那是**多平台发布**（写出去），本篇是**多平台读取**（读回来）。
- **同在"向平台取内容"，一句对照**：[hongguo-drama](hongguo-drama-红果短剧桌面版.md) 是**桌面应用直接对接来源平台接口**（下载视频、扫码登录读观看记录），本篇是**在你已登录的浏览器会话里跑适配器**——两条路都躲不开站点改版与账号/条款风险，只是"借用的登录态"来源不同。
- **同为"取平台内容的下载类工具"，登录态来源不同**：[videdown](videdown-开源视频下载工具.md) 是**独立桌面应用 + 导入浏览器 Cookie**（下载引擎为 yt-dlp），本篇是**复用现成浏览器登录会话**——都在处理"需要登录的内容"，差别在于"登录态从哪来、交给谁"。
- **同属"取站点内容"，路线不同**：[newsnow](newsnow-实时热点新闻聚合阅读器.md) 是**服务端抓各平台热榜并加缓存**（30 分钟缓存 + 自适应间隔），本篇是**在你已登录的浏览器会话里执行适配器**——前者集中、后者贴身，平台条款风险同样自担。
- **同属"让程序操作浏览器"，控制权归属不同**：[playwright-mcp](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md) 是**让 LLM 直接接管浏览器**（自起浏览器或经 CDP 连上去），本篇是**在你已登录的会话里跑适配器**——一个拿到控制权、一个只借用登录态。
- 上游项目（尚未单独收录）：[epiral/bb-browser](https://github.com/epiral/bb-browser)。

## 参考来源

- 项目仓库与 README：https://github.com/epiral/bb-sites
- 中文 README：https://github.com/epiral/bb-sites/blob/main/README.zh-CN.md
- 适配器开发说明（SKILL.md / DESIGN.md）：https://github.com/epiral/bb-sites/blob/main/SKILL.md
- 上游 bb-browser：https://github.com/epiral/bb-browser
- 元数据（Star / Fork / 时间 / 许可证状态）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未安装上游、未运行任何适配器，适配器数量、可用性与实际返回内容均未验证；合规性判断也未咨询任何平台条款原文。
