# BiliSail（哔帆）：Flutter 写的跨平台 Bilibili 第三方客户端

> 一个用 Flutter / Dart 写的哔哩哔哩第三方客户端，覆盖 Windows、Android、macOS、iOS、Linux，主打双／多标签浏览、原生播放、弹幕、云端观看历史与本地续播、下载队列与离线播放。值得记的不是它能看 B 站，而是三点：**它把功能边界、分包结构和"哪些平台验证过、哪些还没验"都写进了 README 与 `docs/`**；**它目前没有开源许可证**；以及**作为需要登录的第三方客户端，账号与合规风险需要自己先想清楚**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`THIRD_PARTY_NOTICES.md` 开头、`master` 分支文件树、GitHub 页面内嵌元数据与 ungh 镜像；并**核实仓库根目录没有 `LICENSE` 文件**；**未 clone、未构建、未运行、未登录任何 B 站账号、未审计源码**）
- 适用范围：Flutter **3.47.6 stable / Dart 3.13.5** 构建；目标平台 Windows x64 / ARM64、Android arm64（API 24+）、macOS 13.0+、iOS 15.0+、Linux x64；文档与示例命令以 **PowerShell** 为主

## 核心结论

**一句话定位（README 自述）**：BiliSail（哔帆）是"专注观看体验的跨平台 Bilibili 第三方客户端"，以**视频、影视和直播观看**为核心，提供多标签浏览、原生播放、弹幕、云端观看历史与本地续播、下载与离线播放。GitHub 描述为："A cross-platform Bilibili third-party client focused on viewing experience"。

**三个最值得记的点：**

1. **工程组织按"根应用 + 四个独立包"切开，边界写得很清楚（README 自述）**：`packages/bili_api`（纯 Dart 协议与 API）、`bili_player`（播放器契约 + media_kit 适配）、`bili_danmaku`（**无网络**的有界弹幕调度与绘制）、`bili_mux`（独立精简原生组件，**仅**处理本地 MP4 无损封装）。把"联网取数""播放""弹幕""本地封装"拆成互不越界的包，是它文档里反复强调的架构取舍。
2. **它对"验证到什么程度"说得比较实诚**：README 的《平台与开发状态》表逐平台标注了当前是"已经跑通"还是"待验"。例如 Windows 有本机 MSIX 安装运行记录；而 macOS / iOS / Linux / Android 多数写着"首次远端构建／实机验收**待验**"。**这与"能构建"不等于"能用"是两回事**，引用时别把 CI 通过当成实机验证。
3. **它标注了账号与合规前提，但风险仍在使用者**：README 有《使用声明》（第三方客户端，与官方无隶属关系，仅供学习交流）；账号能力（扫码登录、内嵌官方网页密码／短信登录）**凭据存进系统安全存储**，遥测默认关闭，空降助手默认关闭且**不携带账号凭据**。但**用第三方客户端登录 B 站账号本身的合规与风控风险，需要使用者自担**（我未做任何登录验证）。

**个人判断（未验证）**：从 `docs/` 的密度看（大量 `validation/*.md`、`decisions.md`、`AGENTS.md`、`implementation-plan.md`），作者是把它当"长期可维护的产品"在做，文档纪律好于多数同类个人客户端；**但它很新（2026-10-06 创建）且无许可证**，长期可用性、以及"能不能合法再分发/二次开发"都是变量。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [CKopoer/BiliSail](https://github.com/CKopoer/BiliSail) |
| 描述 | "A cross-platform Bilibili third-party client focused on viewing experience" |
| 归属 | 账号 `CKopoer`（**个人账号**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false`（页面值） |
| 许可证 | **无**（**页面 `sidebarAbout` 无 license 字段；`master` 文件树根目录没有 `LICENSE` 文件**；README《许可与第三方资源》原文："**本项目尚未确定开源许可证**。第三方依赖与素材保留各自许可和权利范围，公开源码不代表所有资源均已取得再分发授权"）。仓库内只有第三方许可文档：`THIRD_PARTY_NOTICES.md`、`assets/branding/bilibili_LICENSE.md`、`assets/fonts/*/LICENSE.txt`、`windows/licenses/*`、`packages/bili_mux/LICENSE-FFmpeg.txt` 等 |
| 主语言 | **Dart 89.8%**（shields.io `languages/top`，2026-11-13） |
| 技术栈 | **Flutter 3.47.6 / Dart 3.13.5**；状态与依赖注入用 **Riverpod**、路由 **go_router**、网络 **Dio**、结构化存储 **Drift**、安全凭据 **flutter_secure_storage**；播放统一用 **media_kit**（内置 **mpv** / **FFmpeg**）；自带 4 个本地包（`bili_api` / `bili_player` / `bili_danmaku` / `bili_mux`） |
| 关注度 | **640 Star / 30 Fork / 0 Watcher / 3 开放 Issue**（**Star·Fork·Watcher 为 GitHub 页面内嵌值，Issue 数取自 shields.io**，2026-11-13。**ungh.cc 镜像报 640 Star / 30 Fork / 0 Watcher，与页面一致**） |
| 维护状态 | **2026-10-06 创建**；页面 `updatedAt` **2026-11-13**、ungh 报 `pushedAt` **2026-11-12**（**活跃，创建仅一个多月**）；已累计 **15 Release / 59 tags**（页面值） |
| 默认分支 | `master` |
| 版本 | **15 Release / 59 tags**；页面 `packages: true`（发布包）；最新正式版 README 标为 **v1.2.9+5** |
| 话题标签 | 无（页面 `topics: []`） |
| 体积 | `master` 文件树约 **2,002 个文件**（ungh.cc files API） |
| 仓库结构 | 顶层：`lib/`（根应用，按 `app` / `core` / `domain` / `features` / `shared` 分层）、`packages/`（`bili_api` / `bili_player` / `bili_danmaku` / `bili_mux`）、`docs/`（设计、决策与验证记录，含大量 `docs/validation/*.md`）、`tool/`（检查、原生测试与打包脚本）、`android/` / `apple/` / `windows/` / `linux/`（各平台工程） |
| 官网 | 无独立官网（README 用 GitHub Releases 分发安装包） |

## 它是一个什么样的客户端（README 自述）

功能谱系覆盖：内容浏览（推荐、分区、热门、动态、番剧、放映厅、直播）、视频卡片悬停预览、七类搜索、**DASH 音视频分轨播放**、分 P／合集／选集与合集订阅、DLNA 投屏、直播（HLS/FLV、实时聊天、Super Chat）、弹幕与字幕（滚动/顶部/底部、样式与本地过滤、cue 显示）、账号与个人空间（扫码登录、关注分组、收藏夹、追番、消息/私信）、评论与动态互动、多标签导航与独立播放窗、云端观看历史与续播、下载与离线（队列、断点恢复、文件校验、本地分轨）、主题与快捷键等。

- 桌面端（Windows / macOS）**双标签共用一个独立播放窗**；Android 默认单标签；根标签固定，最多 16 个标签，**标签状态仅保留到本次运行结束**。
- macOS 当前用 **ad-hoc 签名**，安装后需在"隐私与安全性"里手动允许运行；Windows 的 MSIX 需**先信任附带的证书**（`.cer`）才能装。
- **运行应用无需另装 FFmpeg**（README 自述）；测试媒体生成才用本机 FFmpeg。

## 平台与开发状态（README 自述的验证程度，重点看"待验"两字）

| 平台 | 仓库自述的验证状态（摘录） |
| --- | --- |
| Windows x64 | 本地与 CI 构建、MSIX 打包／测试签名通过；游客播放等有原生验证，本机有 MSIX 安装运行记录；**新版安装、卸载和升级待完整回归** |
| Windows ARM64 | 已加入独立 CI 构建与打包；远端首次应用构建及设备验收见 CI/CD 文档 |
| Android arm64 | 本地与 CI 构建、固定 release 签名与单 ABI 校验通过；**真机运行、播放、离线下载和覆盖升级待验** |
| macOS | CI 的 arm64 构建与 DMG 通过；Intel x64 首次远端构建待验；**设备启动、原生播放、安装及 Gatekeeper 验收待验** |
| iOS | 已接入工程及 CI 未签名 IPA；**首次远端构建、签名安装、账号与播放实机验收待完成** |
| Linux | 已接入 GTK 工程及 CI Flatpak／AppImage；**首次远端构建及桌面运行待验** |

**判断（据上表）**：这份表把"CI 能构建"和"实机能用"分得很清。**除 Windows 外，多数平台在当前时点仍是"待验"状态**——想在某平台日用，请先自己装包实测。

## 账号、登录与数据边界（README 自述）

- **需要登录才能用完整功能**（用户主页、收藏夹、追番、消息等）；登录方式为**扫码登录**或**内嵌官方网页的密码／短信登录**，Windows 的密码／短信登录需 **WebView2 Runtime**。
- **凭据存进系统安全存储**（`flutter_secure_storage`），README 称账号隔离与错误处理见 `docs/api-design.md`。
- **默认不添加自动遥测**；**空降助手（BilibiliSponsorBlock 兼容）默认关闭**，启用后会**查询独立第三方服务，不携带 B 站账号凭据、也不自动上报**（README 自述）。
- **播放诊断日志存本地**应用数据目录的 `logs`，限制容量并**过滤账号、请求头和媒体 URL，不自动上传**（README 自述）。
- 仓库含一份 `docs/validation/api-risk-control-2026-11-11.md`（**接口风控相关验证记录，我没有读它的内容**）——说明作者已把"接口风控"当作要处理的问题，但这**不改变第三方客户端的固有风险**。

## 工程结构（README 自述的分包与职责）

```text
lib/
├── app/        # 组合根、路由、主题与工作区
├── core/       # 存储、安全凭据、取消与平台能力
├── domain/     # 共享领域类型
├── features/   # 按 domain / data / application / presentation 分层
└── shared/     # 共用数据映射与 ui 组件
packages/
├── bili_api/     # 纯 Dart 协议与 API 包
├── bili_player/  # 通用播放器契约、media_kit 适配与 VideoSurface
├── bili_danmaku/ # 无网络的有界弹幕调度与绘制
└── bili_mux/     # 独立精简原生组件，仅处理本地 MP4 无损封装
docs/            # 设计、决策与验证记录
tool/            # 检查、原生测试与构建打包脚本
```

- 根应用与四个包统一用 `.\tool\check.ps1` 做格式、静态分析与**离线测试**；正常测试用 fake transport / repository / player 与人工生成媒体，**无需真实账号**。
- 播放器原生库在构建时从固定来源下载（首次构建需能访问 GitHub）；`packages/bili_mux` 处理本地 MP4 无损封装，README 提示移动／分发便携构建时要保留整个 `Release` 目录（含原生 DLL 与 `data`）。
- 项目**原名 Bili Lite，现工程名 `bilisail`**；更名后的安装与数据标识变化见 `docs/validation/project-renaming.md`。

## 使用前需要注意

- **⚠️ 没有开源许可证（最关键的一条）**：**仓库根目录无 `LICENSE`，README 明说"尚未确定开源许可证"**。这意味着默认**保留所有权利**——**可以先阅读、学习，但不等于获得了复制、修改、再分发的授权**；公开源码也不代表仓库内的字体、图标、原生二进制都已获再分发授权（见 `THIRD_PARTY_NOTICES.md`）。**要用在衍生项目或再分发前，请先联系作者确认授权。**
- **⚠️ 第三方客户端的账号风险，README 不能替你兜底**：它是一款需要**登录 B 站账号**的非官方客户端。用非官方客户端登录本身存在**账号协议与风控风险**（README 的《使用声明》也只说"仅供学习交流""与官方无隶属关系"）。**公众号/常用账号请谨慎，最好用小号先试**（我未做任何登录验证）。
- **⚠️ 仓库含二进制素材与构建产物痕迹**：`master` 文件树里有字体（`.ttf`）、图标（`.png` / `.icns`）、应用图标等二进制文件；播放器原生库走构建期下载；仓库还带 `windows/patches/*`、`android/patches/*`（对 `media_kit`、`audio_service` 的补丁）。**仓库里有二进制不等于"开箱即用的已编译产物"——安装包只走 Releases。**
- **⚠️ 多数平台是"待验"而非"可用"**：除 Windows 外，README 明确 macOS / iOS / Linux / Android 的实机验收**尚未完成**。**请以 Releases 里实际有附件的平台为准**，并自行验证播放、登录、升级。
- **⚠️ 桌面播放窗与标签行为有具体记忆规则**：桌面双标签共用一个播放窗、`Ctrl+T`/`Ctrl+W` 开关标签、最多 16 个标签、标签状态**只在本次运行内保留**；macOS ad-hoc 签名、Windows MSIX 需先信任证书——**这些都会影响首次安装体验**，用前先读 README 的《下载与安装》《使用说明》。
- **⚠️ 很新，迭代快**：**2026-10-06 创建**，核验日已 15 Release / 59 tags、`pushedAt` 就在当天前后。**高活跃也意味着接口、行为、目录结构仍在快速变动**，README／文档可能滞后于代码。
- **我未做任何运行验证**：**未 clone、未构建、未运行、未登录任何账号、未审计源码**；上述能力、平台状态、隐私与登录边界**均来自 README、`THIRD_PARTY_NOTICES.md`、文件树与页面元数据自述**。

## 什么时候会再看

- 想找一款**Flutter 写的、覆盖全平台的 Bilibili 第三方客户端**，或想参考它"根应用 + `bili_api` / `bili_player` / `bili_danmaku` / `bili_mux` 四包分层"的工程切法。
- 想研究**弹幕渲染怎么做到"无网络、有界调度"**（`bili_danmaku` 的定位），以及 **DASH 分轨播放 + media_kit 适配**的落地写法。
- 想借鉴它**把"平台验证状态"逐平台列成表、区分"CI 通过"与"实机验收"**的文档习惯——这在个人客户端项目里少见。
- 想评估"**第三方客户端登录自有账号**"的合规与风控边界（配合仓库里的 `api-risk-control` 验证记录一起看）。
- 若将来作者**补上开源许可证**（或明确授权），则"能否二次开发／再分发"的结论需要重估。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"内容平台的非官方第三方客户端，且登录涉及账号风险"**：[hongguo-drama：红果短剧桌面版](hongguo-drama-红果短剧桌面版.md)——两者都是非官方客户端（一个看 B 站、一个看红果短剧），都需**扫码/账号登录**、都对**安装包签名**有要求（BiliSail 的 MSIX 需信任证书、ad-hoc 签名；红果 EXE 未签名），可对照看"第三方客户端的合规与账号风险"该怎么记。
- **同属"跨平台全功能播放客户端"**：[boxplayer：聚合网盘与影视的全平台播放器](boxplayer-聚合网盘与影视的全平台播放器.md)——都用"一个客户端 + 一个播放内核"把内容聚合起来（BiliSail 面向 B 站，boxplayer 面向网盘/媒体服务器），可对照两者的**技术栈选择（Flutter vs Electron）与许可证态度（无证 vs GPL-3.0）**。

## 参考来源

- 项目仓库与 README：https://github.com/CKopoer/BiliSail
- 第三方来源与许可检查：https://github.com/CKopoer/BiliSail/blob/master/THIRD_PARTY_NOTICES.md
- 下载与安装说明（含 CI/CD、签名）：https://github.com/CKopoer/BiliSail/blob/master/docs/validation/ci-cd.md
- 整体架构：https://github.com/CKopoer/BiliSail/blob/master/docs/architecture.md ・ 开发约定：https://github.com/CKopoer/BiliSail/blob/master/AGENTS.md
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 许可证字段缺失 / topics）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未构建、未运行、未登录任何账号、未审计源码**；功能、平台状态、隐私与登录边界均来自 README、第三方说明、文件树与页面元数据自述；**"无许可证"已通过"根目录无 `LICENSE` 文件 + README 明文"双重确认**。
