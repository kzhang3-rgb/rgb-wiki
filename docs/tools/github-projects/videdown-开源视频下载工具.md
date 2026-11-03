# videdown：开源视频下载工具

> 记录这个 Electron 视频下载工具的实际形态——它本质是**给 yt-dlp 套了一层现代 UI**（下载引擎不是自研），支持抖音/小红书/B站/YouTube/Instagram 等平台、可导入 Cookie 下载需登录的内容。第一句先记清三件事：**MIT 许可**；**活跃度已陈旧（`pushed_at` 2026-08-22，核验时约两个半月未提交）**；取平台视频内容的版权与平台条款风险由使用者自担。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README 全文与 GitHub API 元数据，并用默认分支 `main` 的 commits 接口复核活跃度；未下载安装包、未运行、未导入任何 Cookie、未审计源码）
- 适用范围：Windows（正文以 `Setup x.x.x.exe` 为主线）与 macOS / Linux（源码构建需自备 yt-dlp 与 ffmpeg）

## 核心结论

videdown 的定位是"现代化的开源视频下载工具"，但拆开看，**它的下载能力来自 yt-dlp，不是自己实现的**：README 明确致谢 yt-dlp 与 FFmpeg，技术栈里也把 YT-DLP、FFmpeg 与 Electron/Vue 并列。所以它真正的价值在**外壳**——把命令行工具包进一个带下载队列、实时进度、多平台预设的桌面客户端，让不习惯敲命令的人也能用。

这里要分清两件事：

- **支持 1000+ 网站**这句话，实际能力边界取决于 **yt-dlp 的 extractor 覆盖**，而不是 videdown 自己维护了多少个平台适配。yt-dlp 支持多少，它就（理论上）支持多少；yt-dlp 没覆盖或失效的站点，它同样会失败。
- README 里单独列出了国内四家（抖音、B站、小红书、快手）与国外若干（YouTube、Instagram、TikTok、Twitter/X、Facebook），这只是"常用平台摘录"，不代表 1000+ 是逐站验证过的。

第二件要先看清的是**活跃度**。仓库 `updated_at` 是 2026-11-03（核验当天），看着很新；但默认分支 `main` 的最新提交停在 **2026-08-22**，两者相差两个半月。这与 `updated_at` 会被 star、fork 等边缘活动刷新的机制一致——**"`updated_at` 新"不等于"主线在维护"**。此类下载工具高度依赖 yt-dlp 跟进站点改版，长期不更新意味着即插即用的成功率会随时间下降。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [cshuangyy/videdown](https://github.com/cshuangyy/videdown) |
| 描述 | "Videdown 是一款现代化的开源视频下载工具，让你可以从抖音 小红书 B站 YT Instagram等网站下载无水印高清视频" |
| 许可证 | **MIT**（GitHub API `license.spdx_id` = `MIT`；README 末尾附 MIT 全文，Copyright (c) 2024 Videdown） |
| 主语言 | TypeScript（Electron + Vue 3 + Tailwind CSS） |
| 下载引擎 | **yt-dlp**（外部依赖）+ **FFmpeg**（合并/转码）；并非自研下载器 |
| 关注度 | **2,127 Star、231 Fork、8 关注、72 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2026-04-14 创建；`pushed_at` **2026-08-22**；默认分支 `main` 最新提交 `0c620c1`「修复错误 youtube下载」为 **2026-08-22**（PGP 签名 verified）→ **真陈旧**，非抓取缓存假象；`updated_at` 2026-11-03 系边缘活动刷新 |
| 体积 | 约 0.87 MB（API `size` 890 KB） |
| 主页 | 未填（`homepage: null`）；`has_pages`、`has_discussions`、`has_downloads` 均为 false，但已开启 Wiki 与 Projects |
| 话题标签 | 无（`topics` 为空） |

> 补充：72 个开放 Issue 相对 2,127 Star 不算低，与"约两个半月无提交"叠加看，**提了问题不一定有人跟**，实际使用前建议翻一遍 Issue 列表。

## 它能做什么

按 README 的功能说明，能力大致分三块：

**下载**

- 通过 yt-dlp 下载，README 称支持 1000+ 网站；常用平台列出抖音、B站、小红书、快手、西瓜视频、YouTube、Instagram、TikTok、Twitter/X、Facebook。
- 支持多格式与**仅音频**下载；可选画质。
- **Cookie 支持**：可导入浏览器 Cookie，用于下载需要登录才能访问的内容（README 以 YouTube 需登录视频为例，推荐用 Chrome 扩展 "Get cookies.txt" 导出再导入）。

**下载管理**

- 下载队列；实时进度显示（速度、剩余时间）。
- 暂停 / 恢复 / 重试。

**界面**

- 现代 UI（Vue 3 + Tailwind CSS）。

## 安装与构建

**普通使用**：从 Releases 页下载 `Videdown Setup x.x.x.exe`（Windows）安装即可。发行页二进制是否附带 yt-dlp / ffmpeg 未在 README 说明，若运行时提示缺依赖，需按下方"源码构建"部分自行准备。

**源码构建**（README 给出）：

1. 先装 **yt-dlp** 与 **ffmpeg**：
   - Windows（PowerShell）：README 给了对应的 `winget` 安装命令；
   - macOS / Linux：分别给了 `brew` / `apt` / `pacman` 命令。
2. 再 `pnpm install`，然后 `pnpm dev`（开发）或 `pnpm build`（打包）。

**技术栈**：Electron、Vue 3、TypeScript、Tailwind CSS、yt-dlp、FFmpeg。

**项目结构（按 README）**：`electron/`（`main.ts`、`preload.ts`）、`src/`（`components/`、`assets/`、`App.vue`）、`public/`、`package.json`。

## 使用前需要注意

**① 下载引擎是第三方 yt-dlp，能力边界跟着 yt-dlp 走。** 站点支持数、能否成功解析，最终取决于 yt-dlp 的版本与 extractor 状况，而不是 videdown 的 UI。若某个平台失效，往往需要等 yt-dlp 更新（或本地升级 yt-dlp），再等 videdown 跟上。

**② 活跃度已陈旧，不等于"不能用"，但要有心理预期。** 默认分支最新提交停在 2026-08-22。这类工具面对站点改版和平台反爬本就脆弱，**约两个半月的空窗**意味着现成版本可能已经对部分平台失效，且 72 个开放 Issue 不一定有人处理。使用前建议先看 Issue 区与 Releases 是否有新版本。

**③ Cookie 导入=把登录态交给工具。** 导入浏览器 Cookie 是"下载需登录内容"的常见做法，但 Cookie 等同于你的账号凭据。README 只是描述了导出/导入流程，并未说明 Cookie 如何存储（是否加密、存哪里）。**要不要导入、导入哪个账号的 Cookie，请自行权衡**；不愿交出登录态就不要用这条路径。

**④ 下载平台视频涉及版权与平台条款。** 这类工具取的是平台内容，**内容版权与平台服务条款的边界由使用者自行判断**。本笔记只作信息记录，不构成使用建议，也不对合规性作定性。

**⑤ README 含作者收款二维码。** README 末尾附有"支持作者"的微信支付 / 支付宝收款码，属于赞助入口——记录在此，供你判断项目性质时参考。

**⑥ 未安装、未审计代码。** 以上全部来自 README 与 API 元数据的自述，我没有下载安装包、没有运行、没有导入 Cookie、没有审计源码。

## 什么时候会再看

- 需要一个**带图形界面的 yt-dlp 前台**（队列、进度、多平台预设），而不是自己敲命令行时。
- 想参考"**把成熟命令行工具封装成 Electron 桌面客户端**"的做法，以及"UI 层与下载引擎分层"的结构时。
- 想复盘"**`updated_at` 新 ≠ 主线在维护**"这个判断陷阱时，可以拿它当例（与 [squoosh](squoosh-浏览器内的图片压缩工具.md)、[the-book-of-secret-knowledge](the-book-of-secret-knowledge-信息安全与运维清单集.md) 归为同类情形）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"取平台视频内容"，路线不同**：[hongguo-drama](hongguo-drama-红果短剧桌面版.md) 是**针对单一平台的专用客户端**（自己对接红果来源接口、含播放与 NAS 落库），本篇是**通用下载器套壳 yt-dlp**（跨平台、只负责下载）——前者深、后者广，账号与条款风险都要自担。
- **同为"向平台取内容"，机制不同**：[bb-sites](bb-sites-把网站变成命令行指令的适配器集.md) 是**在你已登录的浏览器会话里执行适配器**（复用现成登录态），本篇是**独立桌面应用 + 导入 Cookie**——都在处理"需要登录的内容"，只是"登录态从哪来"不同。
- **同为"下载工具"，形态不同**：[go-music-dl](go-music-dl-多平台音乐搜索与下载工具.md) 是**自托管服务 + 多端前端**（音频、多平台、含音频解密/会员整曲签名），本篇是**桌面端套壳 yt-dlp**（视频、通用）——前者是"服务"，后者是"壳"。
- **同为"下载工具"，内核来源不同**：[galaxy-downloader](galaxy-downloader-多平台媒体下载器.md) 是**网页应用 + 作者自建解析后端**（解析能力不在本仓库），本篇是**桌面端套壳 yt-dlp（MIT）**——一个把解析交给远端服务，一个用成熟命令行引擎，且前者为自定义非开源许可。
- **同为"视频下载器"，实现路线与许可都不同**：[videodl](videodl-纯Python的轻量级多平台视频下载器.md) 是**纯 Python 自研多平台客户端**（一个平台一个适配器、无 GUI、**非商业许可**），本篇是**桌面端套壳 yt-dlp（MIT）**——一个自己写解析层、一个借成熟引擎，许可也从 MIT 换成了 PolyForm Noncommercial。

## 参考来源

- 项目仓库与 README：https://github.com/cshuangyy/videdown
- 发行页：https://github.com/cshuangyy/videdown/releases
- 许可证全文：仓库 `LICENSE`（MIT）
- 元数据与活跃度：GitHub REST API（`/repos/cshuangyy/videdown` 与 `/commits?sha=main`），2026-11-03

以上信息核验于 2026-11-03（元数据取自 GitHub API，活跃度经默认分支 `main` 的 commits 接口复核）。未下载安装包、未运行、未导入 Cookie、未审计源码。
