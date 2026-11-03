# AvPlay：成人视频聚合播放 App 的发布仓库

> 记录一个"把发行渠道做成 GitHub 仓库"的样本：仓库里只有 README 与预览素材，真正的产品是 Release 里的四端安装包。只记录其分发结构、无许可证状态与"内容 + 推广"的双重属性，不构成使用建议。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README、Release 说明与产物列表、提交记录、目录树与 GitHub API 元数据；未下载安装包、未安装、未运行）
- 适用范围：Android（APK）/ iOS（IPA）/ macOS（DMG）/ Windows（ZIP）四端客户端，仅通过 GitHub Release、官网与 Telegram 分发

## 核心结论

这是**一个分发仓库，不是一个源码仓库**。默认分支 `main` 上只有 5 个条目：`.gitignore`（16 B）、`README.md`（3,106 B）、`resources/logo.png`（19 KB）、`resources/preview.mp4`（3.66 MB）、`resources/preview.png`（380 KB）——没有一行应用代码。产品本体是 Release 里打包好的四端安装包，所以既看不到源码，也没有构建方式可核对。

它的内容是**成人视频（AV）聚合与在线播放**：README 自述覆盖"日本、中文、欧美、动漫各类资源"，提供在线观看与内置播放器；从 Release 说明看，它深度整合了 **JavDB**（成人影片资料库）的"影片预览 / 想看 / 看过"，并新增了"短剧排行榜""黄果AI短剧"这类聚合入口。

**两个必须在收录时就写清楚的事实**：一是**仓库没有任何 LICENSE**（`license: null`，README 中也没有许可证段落），按默认即"保留所有权利"，属闭源、不可审计的分发；二是 README 里带**明确的推广位**——机场 WgetCloud（带邀请码 `7kMz`）与网盘 PikPak（带邀请码 `29636995`），说明它不只是作品，也承担引流。

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [anonymousjav/AvPlayRelease](https://github.com/anonymousjav/AvPlayRelease) |
| 归属 | 个人开发者 GitHub 账号 `anonymousjav`；git 提交者显示为 `0xmf`（同一账号的 noreply 邮箱 `108519290+anonymousjav@users.noreply.github.com`） |
| 许可证 | **无（未提供 `LICENSE`，API 返回 `license: null`）**，即默认保留所有权利 |
| 主语言 | `null`——仓库本身不含代码，GitHub 无法识别语言 |
| 关注度 | 1,254 Star、68 Fork、**8 订阅者**（`subscribers_count`；`watchers_count` 与 Star 数相同，属 GitHub 历史遗留，不宜当两个指标）；**3 个开放 Issue** |
| 活跃度 | 2022-07-28 创建；`pushed_at` **2026-10-23**；默认分支 `main` 最新提交 **2026-10-22**；`updated_at` 2026-11-03（可能由 star 等边缘活动刷新，不代表代码更新） |
| 仓库体积 | API 报约 **246 MB**（`size` 251,760 KB），但当前 `main` 树上的 5 个文件合计仅约 **3.9 MB**——差距说明 git 历史中曾提交过大体积二进制（很可能是安装包），后被移除但仍留在历史里 |
| 官网 | `https://www.javrocket.top`（API `homepage` 为 `https://javrocket.top`） |
| 其他元数据 | 无 description、无 topics、`has_wiki: false`、`has_discussions: false`、`has_pages: false`、`has_downloads: false`；默认分支 `main` |
| 目录结构 | `.gitignore`、`README.md`、`resources/logo.png`、`resources/preview.mp4`、`resources/preview.png` |

### 版本与产物

| 版本 | 发布时间 | 产物 | 单个体积 | 累计下载量 |
| --- | --- | --- | --- | --- |
| v2.2.0 | 2026-10-23 | `AvPlay-2.2.0.apk` | ≈ 36.8 MB | 21,639 |
| v2.2.0 | 2026-10-23 | `AvPlay-2.2.0.ipa` | ≈ 22.7 MB | 3,592 |
| v2.2.0 | 2026-10-23 | `AvPlay-2.2.0.dmg` | ≈ 148.8 MB | 1,002 |
| v2.2.0 | 2026-10-23 | `AvPlay-2.2.0.zip` | ≈ 140.9 MB | 2,909 |
| v2.1.0 | 2026-10-21（草稿创建于 2026-01-15） | 同上四类 | — | APK 4,420、DMG 103（仅读到这两个） |

v2.2.0 Release 说明原文摘要：适配 iPhone Duo；优化 JavDB 图片加载；新增短剧排行榜；新增黄果AI短剧；新增 JavDB 的"影片预览""想看""看过"；并提示**从 v2.1.0 起 iOS 包 Bundle ID 已变更，旧版本数据需通过导出或云备份迁移**。

## 它能做什么

- **四端覆盖**：Android（APK）、iOS（IPA）、macOS（DMG）、Windows x64（ZIP），同版本号一并发布。
- **聚合与播放**：按 README 自述聚合"日本、中文、欧美、动漫"各类资源，提供在线观看与内置播放器，README 强调"响应式设计、大屏观看也优雅"。
- **与 JavDB 联动**：Release 说明显示支持影片预览、"想看"、"看过"等资料库侧功能。
- **短剧入口**：新增短剧排行榜与"黄果AI短剧"，属于把非 AV 的短剧内容也纳入同一客户端。
- **分发渠道**：GitHub Release 为主，README 另给官网、Issue 反馈入口与 Telegram 群组。

## 使用前需要注意

- **无许可证，不可审计**：没有 `LICENSE`，也没有源码，无法核验其网络请求、数据采集与权限使用；只能靠成品安装包的黑箱行为判断。
- **内容与版权来源不明**：聚合成人内容与 JavDB 资料，"资源从何而来、是否获得授权"均未说明；**本笔记只记录该项目存在与结构，不构成任何使用建议，也不对其合规性作判断**。
- **声明与实际存在张力**：README 写"仅供研究与学习交流使用，请勿用于非法用途"，但产品定位是面向成人内容的消费客户端，且带推广返利——"研究学习"的措辞与用途描述之间并不自洽。
- **安装需要绕过系统安全机制**：README 直接给出 macOS 侧命令 `sudo xattr -dr com.apple.quarantine /Applications/AvPlay.app`（即去掉隔离属性以绕开 Gatekeeper），iOS 则需 Sideloadly / AltStore **侧载**。这意味着安装包未经 Apple 公证/审核，跳过系统保护属于使用者自担风险。
- **README 内含推广返利链接**：机场（WgetCloud，邀请码 `7kMz`）与私人网盘（PikPak，邀请码 `29636995`），属于典型的联盟引流，其推荐理由需自行打折看待。
- **数据流向未说明**：内置播放器与第三方资料站抓取都涉及浏览/登录数据，仓库未提供任何隐私说明。
- **分发链路的稳定性依赖第三方**：官网 `javrocket.top` 与 Telegram 属于站点/平台可控渠道，可用性、域名存续与被墙情况都无法保证。
- **体积落差**：API 报约 246 MB、当前树仅约 3.9 MB，说明历史里存在已删除的大体积产物；引用"仓库大小"时不应只看 API 数字。

## 什么时候会再看

- 想做"**把 GitHub Release 当作软件分发渠道**"的案例研究（不公开源码、只发安装包、README 做落地页 + 推广位）时。
- 复盘"**内容型客户端如何绕开应用商店**"这条路径（侧载、去隔离属性、Telegram 分发）及其合规边界时。
- 需要对照"**无许可证 + 敏感内容**"这类仓库在收录时应当如何只记元数据、不镜像内容时。

## 相关笔记

- **同为"第三方内容聚合客户端"，来源结构不同**：[hongguo-drama](hongguo-drama-红果短剧桌面版.md) 是围绕**单一平台**的社区客户端，本篇是**多源成人内容的聚合客户端**；可对照两者在"来源授权、账号凭据、分发方式"上的风险差异。
- **同属"聚合型媒体工具"，版权边界同类**：[galaxy-downloader](galaxy-downloader-多平台媒体下载器.md) 是跨平台网页下载器（解析依赖作者自建后端），本篇是四端安装包客户端——两者都把内容来源与版权合规留给使用者自担。
- **同属"不便镜像正文、只做结构记录"的仓库**：[awesome-chinese-secondhand-slang](awesome-chinese-secondhand-slang-中国二手交易暗语全书.md)——那本记录了平台风控相关的黑话数据，本篇是成人内容客户端；收录方式一致：只记元数据与边界，不复制正文。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 仓库主页：<https://github.com/anonymousjav/AvPlayRelease>
- Releases：<https://github.com/anonymousjav/AvPlayRelease/releases>
- 官网：<https://www.javrocket.top>
- 核验时间：2026-11-03（GitHub API 元数据、`git/trees/main`、`commits?sha=main`、`releases?per_page=5`，README 经 jsDelivr 通道读取）
