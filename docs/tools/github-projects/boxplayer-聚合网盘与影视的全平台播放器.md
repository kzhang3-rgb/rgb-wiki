# boxplayer：聚合网盘与影视的全平台播放器

> 记录这个"把网盘、媒体服务器、音乐、阅读和 AI 塞进一个客户端"的项目，以及它分散在多个域名与多个客户端之间的边界。第一句先记清三件事：**GPL-3.0**；**活跃（最新提交与 Release 均在 2026-11-02）**；**本仓库只维护 Electron 桌面版，Apple 与 Android 端是官网 / App Store 上的另一个客户端**。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README 全文、LICENSE 首行、Release 列表与 GitHub API 元数据，并用默认分支 `main` 的 commits 接口复核活跃度；未下载安装包、未安装、未登录任何云盘或媒体服务器、未审计源码）
- 适用范围：本仓库为 Electron 桌面版（Windows / macOS / Linux）；手机、平板、电视端需去官网或 App Store，**与本仓库不是同一个客户端**

## 核心结论

这是一个**个人媒体资产的总入口**：把多家网盘、本地目录和 Jellyfin / Emby / Plex 的内容放在同一个界面里，再用一个播放器覆盖影视、音乐和电子书，另外挂一个 AI 工作台和一套给 Agent 用的 CLI/MCP。

它值得记录的地方有三处：

**① 它不是一个客户端，而是"一个仓库 + 多个客户端"。** README 明确写：*"本仓库主要维护 Electron 桌面版；各平台客户端的界面、功能与发布节奏可能不同"*、*"Apple 平台 App Store 版与本仓库的 Electron 桌面版是不同客户端"*。但**仓库的描述（description）却写"支持 Windows Linux iOS macOS tvOS Android"**——初次接触的人很容易以为这个仓库就是全套。**要记住：这里的代码主要对应桌面端。**

**② 它有两条"给机器用"的出口。** 除了图形界面，仓库还带 `clouddrive-cli`：提供文件遍历、搜索、媒体整理计划、**dry-run 预览**与操作记录，并能作为 **MCP Server** 供 AI 客户端调用。这一点比多数网盘客户端更进一步——**它把"云盘能力"抽象成了可被 Agent 调用的接口**，而且难能可贵地内置了"先预览再执行"的机制。

**③ "Pro 付费 + 自带 AI"与"可自配模型"并存。** README 说核心的文件管理、播放、音乐、阅读和 CLI 可直接用；**Pro 主要覆盖需要服务端成本的内置 AI、资源搜索额度与优先支持**，同时**允许配置自己的 OpenAI 兼容服务或本地模型**（第三方 API 费用由对应服务商收取）。也就是说，**付费点不在功能而在"托管式 AI"**——这是一个相对克制的商业化设计，记录在此以供参考。

需要先看清的边界：**它是二次开发项目**（README 自述"基于 [liupan1890/aliyunpan](https://github.com/liupan1890/aliyunpan) 继续开发"），且**列出的 12 家网盘服务各有限速、权限与 API 能力差异**，README 也承认"第三方云盘按其 API 能力展示操作"。**能用哪些功能，最终取决于对应服务开放了什么。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [gaozhangmin/boxplayer](https://github.com/gaozhangmin/boxplayer) |
| 描述 | "BoxPlayer - 聚合网盘管理+影视聚合 支持 Windows Linux iOS macOS tvOS Android" |
| 许可证 | **GPL-3.0**（GitHub API `license.spdx_id` = `GPL-3.0`；仓库 `LICENSE` 为完整的 GNU GPL v3 文本，35,149 字节，已核验首行一致） |
| 主语言 | TypeScript（Electron + Vue 3） |
| 关注度 | **7,510 Star、449 Fork、44 关注、0 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2023-02-23 创建；`pushed_at` **2026-11-02**；默认分支 `main` 最新提交 `a354105`「fix: preserve custom series members in media details」为 **2026-11-02**（未签名），前一日还有 `ci: allow independent Quark credential updates`、`feat: include local music audio engine and library updates in v5.0.38` → **活跃，迭代密集** |
| 体积 | 约 **329 MB**（API `size` 336,598 KB）——远大于常规 Electron 项目 |
| 主页（API 字段） | `https://www.boxplayer.cc` |
| 官网（README 指向） | **`https://xbyvideohub.com/`**（README 的"官网及全平台下载"，App Store 版也在这一体系内） |
| 话题标签 | `electron-app`、`linux`、`macos`、`typescript`、`vue3`、`windows` |
| 版本 | 公开 Release **v5.0.38**（2026-11-02，作者 `gaozhangmin`）；核验时 `releases` 接口只返回这 1 个公开版本，**其余为草稿**（README 亦写明"GitHub 草稿 Release 尚不能作为公开下载版本"） |
| Release 产物 | **只有 Linux 包**（`.deb` / `.pacman` / `.AppImage`，含 x64 与 arm64/aarch64）；**Windows 与 macOS 安装包需从官网下载** |
| 仓库开关 | 开启了 Projects；**关闭了 Pull Requests**、未开 Wiki、无 Discussions |
| 目录结构（README） | `electron/`（主进程与系统集成）、`src/`（Vue 界面、云盘与媒体功能）、`shared/`、`clouddrive-cli/`（CLI 与 MCP Server）、`scripts/`、`screenshot/` |

> 两处需要留意：**①** API 的 `homepage` 是 `www.boxplayer.cc`，而 README 的"官网"是 `xbyvideohub.com`，**两个域名都自称官方，本项目未说明二者关系**；**②** 仓库关闭了 PR、开放 Issue 数为 0，但 README 又写"问题与建议：GitHub Issues"——**建议渠道与实际开关不一致**，提交反馈前先确认能不能提。

## 它能做什么

**能力总览（README 表格）**

| 场景 | 主要能力 |
| --- | --- |
| 文件管理 | 多账号云盘浏览、搜索、上传下载、复制移动、分享与回收站 |
| 影视媒体库 | 扫描云盘与本地目录，补全海报与元数据；统一浏览电影、剧集、季集、演员 |
| 媒体服务器 | 连接 Jellyfin、Emby、Plex，浏览媒体库、继续观看、搜索并播放 |
| 视频播放 | 内置播放器与 **MPV / IINA** 外部播放；音轨、字幕、**ASS/SSA**、倍速、连续播放、进度恢复 |
| 音乐 | 歌曲/歌手/专辑/自建歌单与队列、**粒子播放器**、歌词、音效、桌面歌词 |
| 阅读 | EPUB / PDF / TXT；书签、笔记、高亮与 AI 阅读辅助 |
| AI 工作台 | 搜索与分析内容、规划文件整理、文档问答；**执行写入操作前提供确认** |
| 自动化 | `clouddrive-cli` 与 **MCP Server**，供终端与 AI Agent 调用云盘能力 |

**内容来源**

- **云盘（12 家）**：阿里云盘 / Alipan、百度网盘、123 网盘、115、夸克、PikPak、OneDrive、Dropbox、Box、中国移动云盘 139、天翼云盘 189、光鸭云盘。
- **媒体与目录**：Jellyfin、Emby、Plex、WebDAV、AList、本地文件夹。
- README 明确提示：**各服务的官方能力、账号权限与限速不同，App 只展示当前来源支持的操作**。

**开发与构建（README）**

```bash
# 要求 Node.js >= 22.12.0，使用 pnpm
pnpm install
pnpm dev

CI=true pnpm exec vue-tsc --noEmit   # 只做类型检查
pnpm run test
pnpm run build                        # 会自动递增 patch 版本，再类型检查与打包
```

- 私有凭据放 `.env.local`（已忽略提交），用 `pnpm run secrets:generate` 生成 `src/secrets.generated.ts`；README 明确"**不要提交真实凭据**"。
- 版本说明记在 `docs/releases/v5.0.37.md` 之类文件中。

## 使用前需要注意

**① 别把"仓库支持全平台"当成事实。** 描述里写了 iOS / tvOS / Android，但 README 说本仓库主要维护 Electron 桌面版，Apple 与 Android 端是另一套客户端。**下载与报问题时要先分清自己在用哪个客户端。**

**② 两个"官网"域名并存。** API 主页指向 `www.boxplayer.cc`，README 指向 `xbyvideohub.com`。**下载安装包时应只走其中一个并自行核对**；README 也专门提醒 macOS 遇到安全提示时"**先确认下载来源，不要对来源不明的安装包解除隔离**"——这条提示本身值得照做。

**③ GitHub Release 只提供 Linux 包，且公开版本只有 1 个。** Windows / macOS 安装包在官网，**GitHub 上的不是完整下载入口**；其余版本是草稿，README 说草稿版不能当公开下载版本用。

**④ 关闭了 PR、Issue 数为 0。** 不接受外部代码贡献（`has_pull_requests: false`），建议渠道与 README 表述不一致。**遇到问题大概率只能靠自己排查。**

**⑤ 涉及账号凭据与第三方服务条款。** 客户端要登录你的各家网盘（多账号）、媒体服务器，可能还配 AI 服务的 key。**这些凭据如何存储、是否加密、AI 会把哪些内容发往何处，README 没有展开说明**；同时 README 结尾声明"第三方服务产生的额度消耗、费用与账号风险由使用者自行承担"。要不要把多份网盘登录态交给一个客户端，请自行权衡。

**⑥ GPL-3.0 是强传染许可证，且这是二次开发项目。** 仓库基于 `liupan1890/aliyunpan` 继续开发、以 GPL-3.0 发布——**如要再分发修改版，需遵守 GPL-3.0 的传染性要求**；另需注意上游项目自身的许可证状态（本次未核验）。

**⑦ 体积约 329 MB。** 仓库明显包含大量非代码资源，clone 成本不低。

**⑧ 只作信息记录。** 我未下载、未安装、未登录任何服务、未审计源码；上文全部来自 README、LICENSE 首行、Release 与 API 元数据的自述。**内置 AI 的实际数据流向、云盘接口的合规性，均未验证。**

## 什么时候会再看

- 想把散在多个网盘的影视/音乐/电子书收进一个客户端，或需要同时接 Jellyfin / Emby / Plex 时。
- 想参考"**桌面客户端如何把自身能力再抽象成 CLI + MCP Server 供 Agent 调用**"（尤其是 `dry-run` 预览与操作记录这两个设计）时。
- 想复盘"仓库描述写全平台、但代码只管一个平台"与"两个域名都自称官网"这两类核验点，或参考"付费点放在托管式 AI、核心功能保持免费 + 可自配模型"的商业化取舍时。

## 相关笔记

- **同为"客厅 / 电视场景的客户端"**：[aerial-tv：给国产电视的 tvOS 风格桌面](aerial-tv-给国产电视的tvOS风格桌面.md)——那是改造电视桌面本身，本项目的 Android TV 端是"播放器装在电视上"；可对照两类"电视端改造"的切入方式。
- **同为"Electron 跨平台桌面客户端"**：[hongguo-drama：红果短剧桌面版](hongguo-drama-红果短剧桌面版.md)——那类是把单一内容源包成桌面客户端，本项目是把**多个异构来源**统一进一个客户端；可对照两者在"来源聚合"与"账号凭据"上的复杂度差异。
- **MCP 生态参照**：[servers：MCP 官方参考服务器合集](servers-MCP官方参考服务器合集.md)——本项目的 `clouddrive-cli` 自带 MCP Server，可用那份清单作对照，理解"应用自带 MCP"在整体生态里的位置。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/gaozhangmin/boxplayer
- 官网（README 指向，未访问）：https://xbyvideohub.com/
- 许可证：https://github.com/gaozhangmin/boxplayer/blob/main/LICENSE
- CLI / MCP 文档：https://github.com/gaozhangmin/boxplayer/blob/main/clouddrive-cli/README.md
- 上游项目：https://github.com/liupan1890/aliyunpan

以上信息核验于 2026-11-03：Star / Fork / Release 与客户端边界都可能变化。未下载安装包、未运行、未登录任何云盘或媒体服务器、未审计源码、未核验上游项目许可证。
