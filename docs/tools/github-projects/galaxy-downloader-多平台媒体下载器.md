# galaxy-downloader：多平台媒体下载器

> 记录这个 Web 形态的多平台媒体下载器——把链接粘进网页就能解析下载 **Bilibili / 抖音 / 小红书 / TikTok / X / 微博 / SoundCloud 等 23 个平台** 的视频、音频与图文。第一句先记清三件事：**许可证不是开源许可**（自定义"个人使用及免费公开服务许可证"，含商业限制、禁止发布修改版）；**解析要靠后端 API**（默认指向作者自建服务）；README 宣称的"下载桌面版"在本仓库**找不到对应发行物**。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README、**逐条核对 LICENSE 全文**、`package.json`、仓库目录树、GitHub API 元数据与默认分支 `main` 的 commits 接口；未访问线上站点、未解析下载任何链接、未审计源码）
- 适用范围：浏览器访问的 Web 应用；也可自托管（前端 Next.js / 后端 Cloudflare Worker）

## 核心结论

它解决的是"**不想装软件、把链接粘贴到网页就能下载各平台媒体**"这个问题。前身从仓库名看是 B 站的音频下载器，后来扩成了 23 个平台的通用下载器。

三点必须先看清：

**① 许可证不是开源许可，这是本笔记里最需要留意的一条。** GitHub API 返回 `NOASSERTION`，我**逐条读了 `LICENSE` 全文**：它采用作者自定义的《galaxy-downloader 个人使用及免费公开服务许可证 v1.0》，README 自己写明"**这是源码可用许可证，包含商业使用限制，不属于 OSI 定义的开源许可证**"。要点：

- 允许**个人非商业**获取、运行、学习、修改、二次开发；
- 允许**个人免费向公众提供非商业下载服务**，且**不强制公开你自己的修改源码**；
- 但**除"免费公开服务"外，修改版仅限本人使用**——**不得向他人发布修改版的源码、安装包、容器镜像或独立部署包**；
- **收费、广告变现、付费会员、集成进商业产品、用于企业经营/内部业务，均需事先取得书面商业授权**；
- "免费"不等于非商业：免费但服务于商业目的的使用同样需要授权。

也就是说：**自用和"我搭个免费给大家用"是允许的；但想二次分发、或用来赚钱/给公司用，都要另谈。** 这与本仓库里 MIT（videdown）或 AGPL（go-music-dl）的取向完全不同，判断"能不能用、怎么用"时务必以这条为准。

**② 解析能力在后端，不在前端。** README 的部署说明里有 `NEXT_PUBLIC_API_BASE_URL`（生产默认指向 `https://downloader-api.bhwa233.com`）——**前端只负责界面，真正解析各平台链接的是独立的后端 API**。这带来两个直接后果：用作者的线上站点时，**你粘贴的链接会发到作者的服务器**；想完全自托管，就得连后端一起自己部署（仓库里确实带了 `worker/` 与相关脚本）。

**③ 平台的"支持列表"很长，但稳定性取决于上游。** 23 个平台里多数只标注"内容下载"，真正的解析规则要跟着各平台改版走；这类项目的能力边界随时会被上游接口变动重画。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [bhwa233/galaxy-downloader](https://github.com/bhwa233/galaxy-downloader) |
| 描述 | "Bilibili、抖音、小红书等平台下载视频和音频。" |
| 许可证 | **自定义《个人使用及免费公开服务许可证 v1.0》**（API 报 `NOASSERTION`，已手动核对 `LICENSE` 全文；**非 OSI 开源、含商业使用限制**） |
| 主语言 | TypeScript（Next.js 16 App Router 兼容层 + React 19） |
| 关注度 | **1,056 Star、193 Fork、5 关注、4 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2025-01-14 创建；`pushed_at` **2026-11-02**；默认分支 `main` 最新提交 `63c1747`「fix: deduplicate parsing and respect rate limits」为 **2026-11-02**（核验前一天），此前 10-27、10-10 各有提交 → **真实活跃** |
| 体积 | 约 6.8 MB（API `size` 6,929 KB；其中两个 lockfile 就占约 700 KB） |
| 线上站点 | https://downloader.bhwa233.com/ （README 所填 homepage，**作者自建服务，非官方站点**） |
| 社交功能 | 开启 Discussions；无 topics |
| 版本口径 | `package.json` 的 `name` 仍是旧名 **`bilibili-audio-downloader`**、`version` 为 **2.0.0**、`license` 写作 `SEE LICENSE IN LICENSE`；而提交信息提到 "v6.3.0 changelog" → **名称与版本口径不一致**（未进一步核实其版本编号规则） |

> **关于"下载桌面版"**：README 开头宣称页面顶部有"下载桌面版"入口，展示 Windows / macOS / Linux 最新正式版安装包。但本仓库 **Releases 为空、Tags 也为空**（API 均返回空数组），即**仓库内没有可下载的发行物**。安装包是否托管在他处、还是尚未发布，README 未说明——**这一点请以实际发行渠道为准，不要以为仓库里有现成包**。

## 它能做什么

**使用方式**

- 网页应用：复制媒体链接 → 粘贴到输入框 → 点解析 → 选择下载视频/音频/图片；自动识别平台链接。
- 本地开发：`pnpm install` → `pnpm dev`（默认 http://localhost:3010）。
- 自托管：`pnpm build` 后用 `vinext deploy` 部署到 Cloudflare Workers（仓库提供 `deploy` / `deploy:ci` / `deploy:preview` 脚本与 `wrangler.jsonc`；README 提示**不要直接用 `wrangler deploy`**，要交给 `vinext`）。

**平台覆盖（README 列表，共 23 个）**

Bilibili（视频/音频/分享口令）、Bilibili TV（仅音频）、抖音（视频/图文/分享口令）、Instagram（Reels/帖子/图文）、小红书（视频/图文）、TikTok、X、Telegram、Threads、**微信公众号**（文章视频/多视频）、微博（视频/图文/多视频）、Niconico、Vimeo、Dailymotion、Streamable、Reddit、Newgrounds、Tumblr、Pinterest、VK、OK.ru、Twitch、SoundCloud、Apple Podcasts。

**其他特性**

- **音频提取在浏览器端做**（FFmpeg.wasm），README 明确提示"受浏览器性能和文件大小影响"。
- 图文内容支持**分开下载或打包下载**（JSZip）。
- **本地下载历史**、多语言界面（简中 / 繁中 / 英 / 日 / 西 / 俄）。
- Apple Podcasts 支持节目主页搜索、选择单集并逐集下载；**README 声明仅支持公开可访问音频，不绕过付费、订阅、地区限制、登录或 DRM**，也不提供全集打包或音频提取——**这是它自己划的边界，值得记下**。

## 使用前需要注意

**① 上手前先读许可证。** 如上：个人非商业使用与"免费公开服务"可以；**二次分发修改版（源码/安装包/镜像/部署包）与任何商业用途都需要另行授权**。若你打算把它部署给公司内部或拿去变现，先取得书面授权。

**② 粘贴链接=把链接交给后端解析。** 线上站点默认把解析请求发往作者自建的 API（`downloader-api.bhwa233.com`）。**内容涉及敏感链接时，请先想清楚这一步**；要完全自主，就自建后端并改 `NEXT_PUBLIC_API_BASE_URL`。

**③ 取平台内容（含"无水印"）的版权与平台条款风险。** 抖音无水印、图文批量、跨平台下载等都属于对平台内容的再获取，**边界由使用者自行判断**。本笔记只作信息记录，不构成使用建议，也不对合规性作定性。

**④ "无水印 / 支持 XX 平台"这类表述会随上游失效。** 别把列表当承诺；解析失败往往要先看是不是平台改版或作者后端被限流（最新提交正是在改"去重解析 + 遵守限流"）。

**⑤ 仓库里有 AI Agent 协作痕迹。** 根目录含 `AGENTS.md`、`.claude/`、`skills-lock.json`、`.pr_agent.toml`，并带有 `tests/` 与 `vitest.config.ts`。不影响使用，但说明其代码有较大比例由 Agent 辅助产出。

**⑥ 未运行、未审计。** 以上全部来自 README、LICENSE、`package.json`、目录树与 API 元数据；我没有访问线上站点、没有解析下载任何链接、没有审计解析代码。

## 什么时候会再看

- 想**不装客户端、用网页快速取各平台视频/音频**（尤其是抖音无水印、小红书图文、微信公众号视频）时。
- 想参考"**前端 Web 应用 + 独立解析后端（Cloudflare Worker）**"的拆分方式，以及"用 FFmpeg.wasm 在浏览器里抽音频"的做法时。
- 想研究"**源码可用（source-available）而非开源**"的许可证怎么写——这份 LICENSE 的条款结构（个人使用 / 免费公开服务 / 商业授权 / 第三方材料）可以直接当范本看。
- 想复盘"**README 宣称的功能在仓库里找不到对应物**"这类核对点（与 [hongguo-drama](hongguo-drama-红果短剧桌面版.md) 的 README-vs-配置矛盾同类）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"下载工具"，形态与许可都不同**：[videdown](videdown-开源视频下载工具.md) 是**桌面端套壳 yt-dlp（MIT）**，本篇是**网页应用 + 独立解析后端（自定义非开源许可）**——一个靠成熟命令行引擎，一个靠自建解析服务。
- **同为"多平台聚合下载"，媒介与授权不同**：[go-music-dl](go-music-dl-多平台音乐搜索与下载工具.md) 是**音频、可 Docker 自托管、AGPL-3.0**，本篇是**视频/图文为主、前端托管于 Cloudflare、自定义许可**。
- **同为"取平台视频内容"，是否官方不同**：[hongguo-drama](hongguo-drama-红果短剧桌面版.md) 是**单一平台的社区客户端**，本篇是**跨平台网页下载器**——两者都把账号与版权边界留给使用者。
- **同属"聚合型媒体工具"，版权边界同类**：[AvPlay](AvPlay-成人视频聚合播放客户端.md) 是**四端安装包形式的成人内容聚合客户端（无许可证、闭源）**，本篇是**网页版下载器（自定义非开源许可、前端源码可见）**——都把内容来源与版权合规留给使用者自担，但可审计程度不同。
- **同属"多平台媒体工具"，解析在哪、许可如何都不同**：[videodl](videodl-纯Python的轻量级多平台视频下载器.md) 是**本地 Python 库/CLI（一个平台一个适配器、PolyForm Noncommercial）**，本篇是**网页应用 + 作者自建解析后端（自定义非开源许可）**——一个解析跑在你自己的机器上，一个解析跑在别人的服务器上。

## 参考来源

- 项目仓库与 README：https://github.com/bhwa233/galaxy-downloader
- README 镜像（内容一致）：https://cdn.jsdelivr.net/gh/bhwa233/galaxy-downloader@main/README.md
- 许可证全文：仓库 `LICENSE`（自定义《个人使用及免费公开服务许可证 v1.0》，2026 年版权）
- 依赖与脚本：仓库 `package.json`（含 `@ffmpeg/core 0.12.9` 等）
- 元数据与活跃度：GitHub REST API（`/repos`、`/commits?sha=main`、`/git/trees/main`、`/releases`、`/tags`），2026-11-03
- 线上站点：https://downloader.bhwa233.com/

以上信息核验于 2026-11-03（元数据取自 GitHub API，活跃度经默认分支 `main` 的 commits 接口复核，许可证经 `LICENSE` 全文人工核对）。未访问线上站点、未解析下载任何链接、未审计源码。
