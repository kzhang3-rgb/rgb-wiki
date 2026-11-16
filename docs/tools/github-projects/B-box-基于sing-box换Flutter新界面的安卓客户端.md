# B-box：基于 sing-box-for-android 换 Flutter 新界面的非官方安卓客户端

> 记录一个"**保留 sing-box 内核、把 Android 界面换成 Flutter**"的非官方客户端：后端仍是 SagerNet 的 sing-box-for-android（SFA），界面改用 FlClash 与 prism_ui 的 Flutter 组件，并新增了一套面向"连接与请求调试"的工具页。三件使用前必须看清的事：**它是独立维护的非官方分支、不代表上游发布**；**`LICENSE` 文件只含 GPL 声明段落、不含 GPL 全文（GitHub 识别为 "not identifiable"）**；**默认分支是 `newui`，且 README 自己写明"已有 Release 不等同于当前源码"**。未下载 APK、未安装、未运行、未审计源码。

- 状态：草稿
- 最近核验：2026-11-16（阅读 `newui` 分支的 README、`AGENTS.md`、`LICENSE`、`version.properties` 等根文件与文件清单（694 文件）；`main` 分支 README / LICENSE；Release 列表、`commits/newui.atom`、`commits/main.atom` 与 ungh 镜像 / GitHub 页面内嵌元数据 / shields 徽章。**未 clone、未编译、未下载 APK（约 48 MB）、未安装、未运行、未审计 Flutter 与 Go 源码**）
- 适用范围：**Android 7.0+ / ARM64**（README 明写下载入口为 "Android 7.0+，ARM64"）；开发需 Flutter + Android 构建链 + Go（构建与测试走 GitHub Actions，本机仅做静态检查）

## 核心结论

**一句话定位（描述）**：`B-box: unofficial Android client based on SagerNet/sing-box-for-android with dashboard UI customizations.`（README 自述：**"基于 SagerNet/sing-box-for-android 的非官方 Android 客户端"**，界面用 FlClash 与 prism_ui 的 Flutter 组件，**后端保持 sing-box**，"项目独立维护，不代表上游的官方发布"）。

**① 它是什么。** 一个把上游 SFA（sing-box 的 Android 客户端）的**界面层整体更换为 Flutter** 的分支：Flutter 负责全部页面与导航，代理内核与平台能力仍走上游的 `libbox` 与 Android 桥接。**保留完整 sing-box 协议**，并保留 VPN、分应用代理、Root/Shizuku 应用查询、远程控制、连接管理与日志等原能力。

**② 界面来源是三段式，不能混为一谈。** `AGENTS.md` 明确"**三个上游边界分别维护**"：Android 后端 = sing-box-for-android；Flutter 界面组件 = FlClash v0.9.3 及其 `prism_ui`；核心 = sing-box / libbox。文档分工也写死：`libbox.md` 管核心差异、`flclash.md` 管 UI 来源与适配、`app.md` 管应用差异与构建发布——**更新其中一个上游不自动改动另两个基线**。

**③ 最"新"的一块是"连接与请求调试"。** README 列出的工具页"连接与请求"支持连接管理、**HTTP 抓取、HTTPS MITM、域名筛选、请求详情及 HAR / cURL 导出**；JSON 配置支持 **MITM、Map Remote、Header/URL/Body Rewrite、Map Local / API Mock 和 JavaScript 请求/响应改写**。这类"把抓包与改写做进代理客户端配置"的做法，比单纯的节点管理走得更深（**本笔记未验证其实现，仅据 README 与文档记录**）。

**个人判断（未验证）**：这个仓库的价值在"**换壳但不换核**"——它没有另起内核，而是把界面层重做并补上请求调试能力，同时用 `AGENTS.md` 把"哪些上游边界不许混"写死。但它对**平台与许可**都不宽松：**只发 ARM64**、`LICENSE` 只有声明段没有 GPL 全文、且有"不得暗示与上游关联"的附加条款。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [giturass/B-box](https://github.com/giturass/B-box)（描述："B-box: unofficial Android client based on SagerNet/sing-box-for-android with dashboard UI customizations."） |
| 归属 | 个人开发者 `giturass`；基于 [SagerNet/sing-box-for-android](https://github.com/SagerNet/sing-box-for-android)，界面参考 [FlClash v0.9.3](https://github.com/chen08209/FlClash) 与其 [prism_ui](https://github.com/chen08209/prism_ui) |
| 许可 | README 与 `LICENSE` 声明 **GPL-3.0**（Copyright (C) 2022 nekohasekai），**并含附加条款**："no derivative work may use the name or imply association with this application without prior consent"，另注明"fork 不得以原名称上架 F-Droid 等应用商店"。**但 `LICENSE` 文件（791 字节）只有声明段落、不含 GPL 全文**，故 GitHub / shields 识别为 **"not identifiable by github"** |
| 主语言 | Dart 57.5%（实为 Flutter + Kotlin + Go 多语言工程） |
| 关注度（2026-11-16） | **89 Star / 0 关注 / 1 Fork**（页面内嵌值与 ungh 镜像、shields 均一致）；shields：**0 开放 Issue、12 贡献者** |
| 创建 / 最近推送 | **2026-09-30 创建**；`pushed_at` **2026-11-16**（`newui` 最新提交 `16aad5de` "docs: record shared node use and profile regression validation"，2026-11-16T02:07:46Z，作者 `giturass`） |
| 默认分支 | **`newui`**（**不是** main / master）；`main` 保留上游 SFA 内容，最新提交停在 **2026-09-28**（作者为上游 `nekohasekai`）——**看本项目代码要看 `newui`** |
| Release / 文件数 | **1 个 Release**（`1.15.0-alpha.13`，prerelease，2026-11-09；签名 ARM64 APK 约 48 MB，附 `B-box-version-metadata.json`，由 Actions 从 `da777a0` 构建）；`newui` 共 **694 个文件** |
| 文档站 | <https://b-box-docs.vercel.app>（托管在 Vercel）；应用包名 `io.ericlee.bfa`，源码 namespace 与 AIDL 描述符仍保留 `io.nekohasekai.sfa` |

> 口径说明：数据来自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON（`newui` 分支）+ shields.io 徽章 + `commits/newui.atom`**（核验日未直接调用 api.github.com）。

## 功能与界面

**README 自述的能力范围**：

- **界面全 Flutter**：Flutter 负责全部页面与导航，"启动器和 Android 分享入口均进入新界面"；界面布局与组件来源见 `flclash.md`。
- **配置与内核**：配置使用 sing-box JSON；**代理组、可选节点、测速与路由模式来自 libbox**，自动组由内核选择。
- **保留的原能力**：VPN、分应用代理、Root / Shizuku 应用查询、远程控制、连接管理、日志。
- **工具 · 连接与请求**：连接管理、HTTP 抓取、**HTTPS MITM**、域名筛选、请求详情、**HAR / cURL 导出**（支持范围与限制见 `libbox.md`）。
- **JSON 配置中的 HTTP 改写**：MITM、Map Remote、Header / URL / Body Rewrite、Map Local / API Mock、JavaScript 请求 / 响应改写（说明与示例见 `libbox.md`）。
- **经新页面接入的既有后端**：Tailscale、Taildrop、SSH、OpenConnect / OpenVPN 认证、USB/IP、网络诊断。
- **数据兼容**：支持配置与设置备份；**B-box 既有包名、数据库及配置保持兼容**。
- **已移除**：旧 Compose 页面 / 导航 / 编辑器 / 更新下载与静默安装链路；**Xposed / LSPosed 特权增强**；**Android 5 / 6 支持**（兼容说明见 `app.md`）。
- **文档与约束**：项目约束见 `AGENTS.md`；核心差异 `libbox.md`；FlClash UI 使用与来源 `flclash.md`；Android 应用差异与构建发布 `app.md`；组件许可证在 `flutter/assets/licenses/`。

**工程约定（`AGENTS.md` 摘录）**：以 `verify_newui.py` 做本机静态约束检查、文档改动还需查链接；测试走 `.github/workflows/newui.yml`（Go 扩展、Flutter 分析 / 交互 / 布局、JVM、Android smoke 矩阵），签名 Release 走手动触发的 `build-release.yml`；**不提交密钥 / 签名 / AAR / 生成的 APK**；上游同步固定比较 SHA，"**仅凭版本相同不能直接替换官方 AAR**"。

## 使用前需要注意

- **非官方分支**：README 明写"项目独立维护，**不代表上游的官方发布**"，并把"界面参考版本"和"核心 / 应用比较基线"分开——不要拿它当 SFA 官方版本。
- **许可的两处边界**：① `LICENSE` 只有声明段、**不含 GPL 全文**，所以自动化工具识别不出许可证；② 附加条款**限制派生作品使用本应用名称或暗示关联**，并注明 fork 不得以原名上架应用商店。
- **"已有 Release ≠ 当前源码"**：README 原文——"`newui` 的新界面与功能仍按提交验证；**已有 Release 不等同于当前源码**"。目前也只有 **1 个 Release 且为 alpha**。
- **默认分支是 `newui`**：`main` 上是上游内容，克隆 / 阅读代码时若停在 `main` 会看到完全不同的东西。
- **平台限制**：**仅 ARM64、Android 7.0+**；Xposed / LSPosed 与 Android 5 / 6 支持已被移除。
- **代理类工具的法律与合规边界由使用者自担**：本项目属网络代理客户端，如何配置与使用涉及所在地法规与服务条款，**本笔记只记录项目信息，不构成任何使用建议，也不对其合规性作判断**。
- **文档站托管在第三方（Vercel）**：`b-box-docs.vercel.app` 不是 GitHub Pages，可用性依赖该域名。
- 本笔记**只读了 README、`AGENTS.md`、`LICENSE`、文件清单与元数据**，未下载 APK、未安装、未运行、未审计源码。

## 什么时候会再看

- 想在 **Android** 上用"**sing-box 内核 + 全新 Flutter 界面**"的非官方客户端时。
- 想参考"**以 Flutter 替换 Android 原生界面、同时保留原后端 / 原包名兼容**"的工程做法（含"三个上游边界分别维护"的约束写法）时。
- 想对照**不同平台的代理客户端**：macOS 的 mihomo 客户端、iOS 的小火箭规则、Android 的 sing-box 客户端。
- 想了解"**把抓包 / 改写（MITM、Rewrite、Map Local）做进代理客户端配置**"这一路做法时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"代理客户端"，平台与内核不同**：[bowel：macOS 上不用 WebView 的 mihomo 客户端](bowel-macOS上原生Go渲染的mihomo客户端.md)——那是 **macOS + mihomo + Go 原生渲染**（PolyForm 非商用许可）；本篇是 **Android + sing-box + Flutter 界面**（GPL-3.0 附加名称条款）。可对照"**客户端 GUI 的技术路线**"与"**许可形态**"两条差异线。
- **消费侧客户端 ↔ 服务端节点面板**：[mistgate：自建小规模 VPN 车队面板](mistgate-自建小规模VPN车队面板.md)——那是在**服务端**管节点车队（Hysteria2 / VLESS / REALITY，AGPL-3.0）；本篇是**消费侧客户端**，两者常配套使用，可对照"**建节点**"与"**用节点**"两端。
- **同为"客户端侧的分流 / 规则"**：[Shadowrocket-Rules：小火箭分流规则配置](Shadowrocket-Rules-小火箭分流规则配置.md)——那是 iOS 小火箭的分流规则集（另有 Clash 版本）；本篇是 Android 上带完整界面的 sing-box 客户端，一个管"**规则怎么写**"，一个管"**客户端怎么用**"。
- **"订阅产物 ↔ 读取客户端"的上下游**：[FreeSubsCheck：自动爬取并校验的免费节点订阅仓库](FreeSubsCheck-自动爬取并校验的免费节点订阅仓库.md)——它产出的 Clash / mihomo 订阅与节点格式是同族客户端的常见输入；**注意：该订阅来源为第三方公开池，本库不对其来源与合规性背书**，且 sing-box JSON 与 Clash YAML 并不通用（见上文 `AGENTS.md` 约束）。
- **同属"网络出入口"的不同思路**：[cloudflared：Cloudflare 隧道客户端](cloudflared-Cloudflare隧道客户端.md)——那是借助 Cloudflare 网络的隧道客户端；本篇是本地代理客户端，可对照"**托管网络隧道**"与"**本地代理**"两类打通方式。

## 参考来源

- 项目仓库与 README（`newui` 分支，中文；核验日期 2026-11-16）：https://github.com/giturass/B-box
- 项目约束文档：https://github.com/giturass/B-box/blob/newui/AGENTS.md
- 许可证：https://github.com/giturass/B-box/blob/newui/LICENSE
- 文档站（第三方托管）：https://b-box-docs.vercel.app
- 上游与界面来源：https://github.com/SagerNet/sing-box-for-android ；https://github.com/chen08209/FlClash ；https://github.com/chen08209/prism_ui
