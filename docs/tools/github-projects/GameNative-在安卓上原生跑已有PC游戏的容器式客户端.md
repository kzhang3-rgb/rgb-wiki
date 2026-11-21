# GameNative：在安卓上原生跑自己已有的 PC 游戏

> 记录一个把**你已拥有的 PC 游戏**（Steam / Epic / GOG / Amazon）**在安卓设备上原生运行**的容器式客户端（`utkarshdalal/GameNative`，描述 `Native PC gaming with Steam, Epic, GOG and Amazon integrations on Android`）：**不是串流**——它在安卓上通过 **Wine / Proton + Box64 / FEX** 把 Windows 游戏跑起来，并把**存档同步到云端**，PC 上停下、手机上接着玩。三个值得记的点：**① 走"社区配置自动应用"路线**——每晚用真实使用数据生成"跑不起来的游戏列表"，多人验证过的配置在启动时自动套用；**② 商业与开源的边界写在明处**——本体 GPL-3.0，但 Optimize / AI 排障 / 抢先版是 ko-fi 会员付费特性，且 `THIRD_PARTY_NOTICES` 明确提到**捆绑了部分专有组件**；**③ 默认开启 PostHog 遥测**——游戏启动 / 关闭 / 安装等事件**始终收集**（可选事件才可关）。**未 clone、未安装 APK、未在任何安卓设备上跑过游戏、未编译、未审计源码。**

- 状态：草稿
- 最近核验：2026-11-19（阅读 `master` 分支 `README.md`（138 行）、`ROADMAP.md`（3,227 B）、`LICENSE`（GPL-3.0，35,123 B）首部、`.gitmodules`、文件清单（2,764 项）、ungh.cc 镜像元数据与 releases、shields.io 徽章；**未 clone、未安装 APK、未装任何游戏、未在任何安卓设备上运行、未编译、未访问 Discord 或 gamenative.app、未审计源码**）
- 适用范围：**想在自己已有的安卓手机 / 平板 / 掌机上跑已购 PC 游戏的个人**；想参考"**把 PC 游戏运行时搬进安卓**"的技术栈与"**众包兼容配置自动应用**"机制的项目作者。设备为 **Android**（GPU 驱动相关，见下），构建侧是标准 **Android Studio 工程（Kotlin + Gradle）**，依赖若干 git submodule

## 核心结论

**① 定位：不串流的"原生 PC 游戏"。** README 一句话是 "Play the PC games you already own — from Steam, Epic and GOG — on your Android device, with cloud saves." 它把 Windows 游戏在安卓本地跑起来（**Wine / Proton + Box64 / FEX** 一类兼容层），**存档同步到云端**，并能登录 Steam / Epic / GOG / Amazon 四家商店。

**② ⭐ "社区配置自动应用"是它的核心体验路径。** README 承认"**还在早期，不是每个游戏都能跑，有些要调教**"，但选择用**众包**解决：**每晚用真实使用数据生成"跑不起来的游戏列表"**（发布在 `GameNative/gamenative-compat` 仓库，每个坏掉的游戏一条 issue，带**多少玩家遇到、报了什么问题、游戏窗口是否曾渲染过**等数据），社区把能跑通的配置沉淀下来，**在启动时自动套用**——所以"很多游戏开箱即用"。在线查询某个游戏是否被跑通：`gamenative.app/compatibility`。

**③ ⭐ 开源本体 + 明确的付费层 + 明确提到专有组件。** 本体 **GPL-3.0**（`LICENSE` 35,123 B）。但 README 把"**Optimize（按设备调优）**、**AI 排障（找并修阻止游戏启动的问题）**、**抢先版**"标为 [ko-fi 会员](https://ko-fi.com/gamenative) 付费特性。`LICENSE` 之外还有 **`THIRD_PARTY_NOTICES`（27,488 B）**，README 说它包含"**第三方与专有组件的署名、copyleft 源码提供声明，以及随应用捆绑的专有组件通知**"——即**仓库 GPL-3.0 ≠ 产物全部可自由再分发**，复用前要看这一份。

**④ 默认开启的匿名遥测（PostHog）。** README 的 "Analytics & privacy" 节写明：**始终收集**（用于改进兼容性）**游戏启动 / 关闭 / 退出事件**（游戏名、商店、会话时长、平均 FPS、容器配置）与**安装 / 取消 / 卸载事件**；**可选**项（功能使用、登录成功 / 失败、推荐交互、应用前后台、云同步事件）可在 *Settings → Info → Usage Analytics* 关闭。README 声明**不收集个人身份信息**（无姓名 / 邮箱 / IP / 设备标识）——**这是项目自述，本笔记未抓包验证**。

**⑤ 支持四家商店 + 云存档 + 主流外设。** README "What you get"：Steam / Epic / GOG / Amazon **四家**；**PC ↔ 手机之间接续的云存档**；自动应用已知配置；**手柄与触屏支持**（自定义控制编辑器 + 屏幕 HUD）；**Steam DLC / 创意工坊 / 分支**支持；Discord（自述 35k+ 成员）求助。

**⑥ 支持渠道很特殊：GitHub issue 被关闭。** README 明确"**请不要在 GitHub 开 issue，会被自动关闭**"，求助走 Discord。因此 shields 显示 **0 个开放 issue**——**不代表没有问题，而是官方把 issue 通道关掉了**。

**⑦ 关注度与状态（ungh 快照 / shields，2026-11-19）。** **11,848 Star / 68 关注 / 505 Fork**（ungh；shields 同期 12k / 509）；**0 开放 issue、贡献者 90 人**（shields）；**2025-04-21 创建**、`pushedAt` **2026-11-14**、`updatedAt` 2026-11-14（活跃）；**默认分支 `master`**；**30 个 Release**（最新 **v1.4.0**，2026-11-05）；**2,764 个文件**；**GPL-3.0**（`LICENSE` 35,123 B）；shields 顶层语言 **Kotlin 70.1%**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [utkarshdalal/GameNative](https://github.com/utkarshdalal/GameNative) |
| 描述（GitHub） | `Native PC gaming with Steam, Epic, GOG and Amazon integrations on Android` |
| 归属 | 个人账号 **utkarshdalal**；相关组织仓库 `GameNative/proton-wine`、`GameNative/FEX`、`GameNative/gamenative-compat`、`GameNative/leveldb`、`GameNative/snappy-java`、`GameNative/lsfg-vk-android` |
| 官网 / 兼容查询 | <https://gamenative.app>（兼容性列表 <https://gamenative.app/compatibility>） |
| 许可证 | **GPL-3.0**（`LICENSE` 35,123 B）；另有 **`THIRD_PARTY_NOTICES`（27,488 B）**，README 明说含"**专有组件通知**"与 copyleft 源码提供声明 |
| 主语言 | shields 顶层 **Kotlin 70.1%**（Android 工程，Gradle 构建） |
| 关注度 | **11,848 Star / 68 关注 / 505 Fork**（ungh，2026-11-19；shields 同期 12k / 509）；**0 开放 issue、贡献者 90**（shields，**issue 被官方自动关闭**） |
| 创建 / 最近推送 | **2025-04-21 创建**；`pushedAt` **2026-11-14**、`updatedAt` 2026-11-14 |
| 默认分支 / Release / 文件数 | **`master`**；**30 个 Release**（最新 **v1.4.0**，2026-11-05）；**2,764 个文件** |
| 分发 | **GitHub Releases** 的 APK（另有官方下载域 `downloads.gamenative.app`）；构建产物是 **Android 工程** |
| 仓库结构 | `app/`（2688，其中 `app/src` 2658 · `app/schemas` 23）· `tools/`（19）· `keyvalues/`（13）· `.github/`（10）· `ubuntufs/`（5）· `docs/`（4）· `gradle/`（3）· `.run/`（2）· `PrivacyPolicy/`（1） |
| 根目录可见文件 | `README.md`（7,790 B）、`ROADMAP.md`（3,227 B）、`CONTRIBUTING.md`（4,019 B）、`LICENSE`、`THIRD_PARTY_NOTICES`（27,488 B）、`manifest.json`（33,326 B）、`recommendations.json`（1,576 B）、`prod-debug.lineage`、`build.gradle.kts`、`settings.gradle.kts`、`gradlew`、`img.png`（1.78 MB）、**`keystore`（2,516 B，疑为调试签名）**、`.gitmodules`、`PrivacyPolicy/README.md`（5,215 B） |
| git submodule | `adrenotools`（Pipetto-crypto/libadrenotools）· `lsfg-vk-android`（GameNative/lsfg-vk-android）· `leveldb`（GameNative/leveldb，分支 `gamenative`）· `snappy-java`（GameNative/snappy-java，分支 `gamenative`） |

## 它能做什么

- **跑已购 PC 游戏**：Steam / Epic / GOG / Amazon 四家商店**无需串流**地在安卓本地运行（Wine / Proton 兼容层路线）。
- **云存档接续**：README 主打 PC 与手机之间"停下—接着玩"的**云端存档同步**。
- **社区配置自动应用**：社区验证过的 per-game 配置在启动时自动套用；仓库 `keyvalues/` 里可见 13 份游戏配置（如 `Factorio.txt`、`Hades.txt`、`Noita.txt`、`Sea of Stars.txt`、`Baba Is You.txt` 等）。坏掉的游戏由 `GameNative/gamenative-compat` **每晚生成公开列表**。
- **输入与外设**：手柄 + 触屏，含**自定义控制编辑器**与屏幕 HUD；发布物另有 **XR 版本**（面向 Quest 头显，见 Release 资产 `gamenative-v1.4.0-legacy-xr.apk`）。
- **商店细节**：Steam 支持 **DLC / 创意工坊 / 分支**；Roadmap 里列出 Epic 的 **EOS DRM**、GOG 的 **Galaxy 服务（经 Comet）** 等正在推进。
- **排障与捐助线**：`docs/` 里放了 **Amazon / Epic / GOG 的 API 规格 JSON**（`AMAZON_API_SPEC.json` 36,787 B 等）与 `posthog-recipes.md`；`tools/` 为构建脚本（`build-comet.sh`、`build-fossilize.sh`、`build-wine-xr-bridge.sh`、`provision-build-arm64x-wine-bridge.sh`、`verify-*.ps1` 等）。

## 使用前需要注意

- **⚠️ 许可要分层看**：**本仓库 GPL-3.0**（传染性强），但 README 指向的 **`THIRD_PARTY_NOTICES` 明确点名"捆绑的专有组件"**——即**产物并非全部自由可再分发**。若要分发修改版或打包进自己的产品，须逐条核对这份文件与各第三方组件许可，**不能只看 `LICENSE` 就按 GPL-3.0 整体复用**。
- **⚠️ 默认开启的遥测**：**游戏启动 / 关闭 / 退出事件**与**安装 / 取消 / 卸载事件**是**始终收集**的（README 称不含个人身份信息）；只有"可选"一类能在设置里关。介意的话需在下一次启动前于 *Settings → Info → Usage Analytics* 检查。
- **⚠️ 没有公开 issue 通道**：GitHub issue **被官方自动关闭**，求助只走 Discord——**shields 的"0 open issue"是通道关闭的结果，不是"零问题"**；历史问题不可检索，排查只能依赖 Discord 与 `gamenative-compat`。
- **⚠️ 设备兼容性高度依赖 GPU 驱动**：这类"安卓跑 PC 游戏"方案对**芯片 / 驱动**很敏感（Release note 里出现 **Turnip / Adreno / Mali** 等驱动与 DXVK、vkd3d-proton 的版本号）。**同一 APK 在不同机型上体验可能差别很大**，买前建议先查兼容列表与 Discord。
- **⚠️ APK 体积巨大**：最新 Release 的 `gamenative-v1.4.0.apk` 约 **545 MB**（571,251,660 B）、XR 版约 **547 MB**（573,542,118 B）——因为它**捆绑了 Wine / Proton / FEX 等整套运行时**，并非普通轻量 App。
- **⚠️ 付费层与项目方向**：Optimize、AI 排障、抢先版是 **ko-fi 会员**特性；Roadmap 的目标包含 **Google Play 上架**、**在线多人**、**把 GameNative 做成安卓上的 Steam Deck 替代**——**这些均为路线图自述，尚未落地，本笔记未验证**。
- **⚠️ 版权边界**：README 结尾 Disclaimer 写明"**只用于运行你合法拥有的游戏，不得用于盗版或其它非法用途**"；本笔记仅记录技术形态，不涉及任何游戏内容的获取方式。
- **我只读了文档与元数据**：**未 clone、未安装 APK、未装任何游戏、未在任何安卓设备上运行、未编译、未访问 Discord / gamenative.app、未审计源码**；上述机制、性能与兼容性均来自 README / ROADMAP / Release 说明，**未逐一验证**。所有版本号（DXVK 3.1.1、vkd3d-proton 3.1、Turnip Gen8 V38、Proton 11.0-2 等）取自 Release 说明，会随版本变化。

## 什么时候会再看

- 想在自己已有的**安卓手机 / 平板 / 掌机**上跑**已购的 Steam / Epic / GOG / Amazon 游戏**、又不想串流时；
- 想研究"**把 Windows 游戏运行时（Wine / Proton + Box64 / FEX + DXVK / vkd3d + 定制 Turnip 驱动）搬进安卓**"要处理哪些层与依赖时；
- 想参考"**众包兼容配置自动应用 + 每晚公开坏游戏列表**"这套把"兼容性"工程化的做法时；
- 想对照"**把 PC 游戏生态搬进安卓**"的另一条路线：[DroidDeck](DroidDeck-把SteamOS体验搬到安卓.md) 是**整台 SteamOS（游戏模式 + Linux 桌面 + 商店 + VR）**；本篇是**聚焦"跑游戏 + 云存档"的客户端**。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都是"把 PC 游戏搬进安卓"，但产品形态与合规姿态相反**：[DroidDeck：把 SteamOS 体验搬到安卓](DroidDeck-把SteamOS体验搬到安卓.md)——那篇要装**整套独立 Linux 运行时**（游戏模式 + 完整 KDE 桌面 + 商店 + Steam VR），首页自称"**无遥测 / 无专有组件**"、只从 Releases 分发；本篇是**聚焦"跑已购游戏 + 云存档 + 自动应用社区配置"的客户端**，**默认开启 PostHog 遥测**、`THIRD_PARTY_NOTICES` 坦承**捆绑专有组件**、并有 ko-fi 付费层。两者同为 **GPL-3.0**，正好对照"**把体验做全**"与"**把合规写清**"两种取舍。
- **同属"安卓与桌面体验互搬"，方向与本篇相反**：[Android DEX：把安卓应用搬进桌面窗口的闭源镜像工具](Android-Dex-把安卓应用投到桌面的闭源镜像工具.md)——本篇是**把 PC 游戏搬进安卓**，那篇是**把安卓应用投到桌面**（闭源专有、ADB + scrcpy）；一个方向是"桌面 → 安卓"，一个是"安卓 → 桌面"，可对照两种互搬在**许可与可审计性**上的差别。

## 参考来源

- 仓库主页：<https://github.com/utkarshdalal/GameNative>
- README（138 行）：<https://github.com/utkarshdalal/GameNative/blob/master/README.md>
- 路线图：<https://github.com/utkarshdalal/GameNative/blob/master/ROADMAP.md>
- 第三方与专有组件声明：<https://github.com/utkarshdalal/GameNative/blob/master/THIRD_PARTY_NOTICES>
- 隐私政策：<https://github.com/utkarshdalal/GameNative/blob/master/PrivacyPolicy/README.md>
- 兼容性列表（由真实使用数据生成）：<https://github.com/GameNative/gamenative-compat>
- 兼容性在线查询：<https://gamenative.app/compatibility>
- 许可证（GPL-3.0）：<https://github.com/utkarshdalal/GameNative/blob/master/LICENSE>
- 核验时间：2026-11-19（ungh.cc 镜像的 repo / releases / files(master) 元数据、shields.io 徽章，以及 raw 通道的 `README.md`、`ROADMAP.md`、`LICENSE`、`.gitmodules`）
