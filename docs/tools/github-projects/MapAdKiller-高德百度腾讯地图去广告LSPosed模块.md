# MapAdKiller：给高德 / 百度 / 腾讯地图去广告的 LSPosed 模块

> 一个安卓侧的地图"净化"模块：高德、百度、腾讯三家地图的广告精简与界面定制，同时提供**依赖 Xposed 框架的模块版**和**免 root 的内置重签名版**两条安装路径。它最值得记的不是"能去多少广告"，而是它如何把**适配版本基线**、**实测边界**和**两套分发形态的取舍**写清楚——这正是这类 hook 模块能不能长期可用的关键。

- 状态：草稿
- 最近核验：2026-11-13（阅读 GitHub 页面内嵌数据、ungh.cc 元数据与 `master` 文件树、README 全文、`LICENSE`；**未 clone、未编译、未安装任何 APK、未在 LSPosed / LSPatch 或真机上运行、未核对其去广告与省电的实际效果**）
- 适用范围：**Android**（模块最低 Android 8.0；免 root 内置版走 LSPatch，需 Android 9.0 或更高，且不低于宿主自身要求）；需要支持 **libxposed API 102** 的框架（如 LSPosed），或直接安装重新签名的内置版 APK。适配基线为高德国内版 17.02.0.2010、高德 Play 版 17.02.2.2014、百度地图 22.1.20、腾讯地图 11.7.0（6013），**其它地图版本不保证锚点一致**。

## 核心结论

**一句话定位（README 自述）**："高德、百度、腾讯地图的广告精简与界面定制。"功能分两块：**去广告**（开屏、标注、POI、搜索、我的页等）与**界面自定义**（首页 / 我的页 / 搜索页 UI、工具宫格、底栏、导航栏缩放、地图标注分类等）。

**三个最值得记的点：**

1. **同一份功能，两种分发，二选一**：**模块版**（`MapClean-lsp-*.apk`，约 5.4 MiB）配合 LSPosed / API 102 框架注入，**不重新签名官方地图**；**免 root 内置版**（每个地图各一个重签名 APK）**不需要框架**，但体积明显更大，且与官方签名不同、不能覆盖安装。README 明确"**模块版与内置版选择一种**""不要对内置地图重复启用外部模块"——这是取舍，不是可叠加。
2. **适配以"地图版本"为锚点，版本一变就可能失效**：README 直接给出每家的"本轮适配基线"，并声明"其它地图版本不保证锚点一致""地图更新可能改变配置和锚点，遇到异常先关闭相关选项"。**这类模块的可用性完全绑定上游 App 版本**，是它最大的不确定性来源。
3. **把"验证边界"写在明面上**：README 单列"**已知限制与验证边界**"，逐条说明哪些**已实测**（如开屏拦截在 Android 16 / 内置版实测冷启动 1005 ms / 1032 ms、无白屏无 ANR）、哪些**尚未实机验证**（灵动岛在 HyperOS 上的系统卡片、百度打车支付、真实道路与车机导航、POCO F3 ARM 机型安装、Play 整包逐项实测）。**"测过什么 / 没测什么"写得越清楚，越适合作为同类项目的工程范本参考。**

**个人判断（未验证）**：它的热度与更新频率都不低（见项目信息），说明"地图去广告"确有需求；但**它本质上是在别人的闭源 App 上做 hook**，因此"能不能用"永远取决于目标地图当前版本，README 也反复提示按版本判断——这是使用前必须接受的现实。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [ldxm666/MapAdKiller](https://github.com/ldxm666/MapAdKiller) |
| 描述 | "高德/百度/腾讯地图去广告 LSPosed 模块（libxposed API 102）+ 高德首页/我的页/搜索页 UI 自定义；v1.1.0 新增：关闭搜索框热词轮播、摘除首页打车浮窗"（**注意：仓库描述仍写 v1.1.0，而 README 当前版本已是 2.3.5.step2，描述滞后于正文**） |
| 归属 | 账号 `ldxm666`（**个人用户**，页面 `isOrg: false`）；`isFork: true`（fork 自 `LSPosed/LSPosed.github.io`）、`isArchived: false`、`isPrivate: false`、`isTemplate: false` |
| 许可证 | **GPL-3.0**（页面 `spdxId: GPL-3.0`；`LICENSE` 文件为 GPLv3 全文；README 徽章写 "GPL-3.0-or-later"——**页面标注 GPL-3.0，README 标注 GPL-3.0-or-later，以 LICENSE 文件为 GPLv3 正文**） |
| 主语言 | **Kotlin 83.4%**（shields.io，2026-11-13） |
| 技术栈 | **Android 原生（Kotlin 1.9.23 + 必要 Java 编译桩）**；`app/src/` 下分 `io.github.ldxm666.mapadkiller/`（高德）与 `io.github.ldxm666.bmapclean/`（百度）两个包，另有 `app/native/`（C/C++ 原生补丁，**vendor 了完整 zstd 库源码**）、`app/META-INF/xposed/`（`module.prop`、`java_init.list`、`native_init.list`、`scope.list`），构建脚本 `app/build.ps1` |
| 关注度 | **526 Star / 20 Fork / 3 Watcher / 6 开放 Issue**（**页面值**，2026-11-13；与 ungh.cc 镜像一致，均 526 Star / 20 Fork / 3 Watcher。Issue 数取自 shields.io） |
| 维护状态 | **2026-09-03 创建**；页面 `updatedAt` 与 ungh `pushedAt` 同为 **2026-11-12**（距核验日 1 天，**活跃**）；已累计 **36 Release / 40 tags** |
| 默认分支 | `master` |
| 版本 | 当前 **2.3.5.step2（versionCode 23052，2026-11-13）**；发布 GitHub Release，并发布到模块仓库 `Xposed-Modules-Repo/io.github.ldxm666.mapadkiller` |
| 话题标签 | 无（页面 `topics` 为空） |
| 体积 | `master` 文件树约 **449 个文件**（ungh.cc files API） |
| 仓库结构 | 顶层：`app/`（源码、原生、资源、xposed 元数据）、`docs/`（更新说明、验证记录、开屏分析）；`app/libs/` 含**二进制依赖**（`api-102.0.0.jar`、`interface-102.0.0.jar`、`service-classes.jar`、`kotlin-stdlib-1.9.23.jar`）与**预编译 `.so`**（`arm64-v8a` / `armeabi-v7a` / `x86` / `x86_64` 的 `libmapclean_labels.so`、`libamapbldpatch.so`）；源码主体是高德侧约 40 个 `Amap*.kt` 与百度侧约 25 个 `Baidu*.kt` |
| 官网/分发 | 模块页 <https://modules.lsposed.org/module/io.github.ldxm666.mapadkiller/> |

## 支持的地图与适配基线（README 自述）

| 地图 | 包名 | 本轮适配基线 |
| --- | --- | --- |
| 高德国内版 | `com.autonavi.minimap` | 17.02.0.2010 |
| 高德 Google Play 版 | `com.autonavi.minimap` | 17.02.2.2014 |
| 百度地图 | `com.baidu.BaiduMap` | 22.1.20 |
| 腾讯地图 | `com.tencent.map` | 11.7.0（6013） |

三家独立配置、可 JSON 备份与恢复。高德国内版与 Play 版**包名相同，只能装一个**；Play ZIP 解压后四个 APK 需一起安装（可用 `adb install-multiple`）。

## 主要能力（README 自述）

- **开屏广告拦截**：覆盖广告请求、缓存判断、素材下载、启动阶段实时请求、图片/视频广告视图、本地已存素材等多条路径，并清理 `files/splash/` 下已下载素材；由"拦截开屏广告"开关控制（默认开启，关闭后恢复宿主原始行为）。
- **界面自定义**：高德首页 / 我的页 / 搜索页 UI、工具宫格（隐藏项让位、底栏全关后工具靠近底部）、横屏导航栏缩放、巡航速度与持续时间设置。
- **地图标注分类**：高德、腾讯各含餐饮、购物、医疗、学校与科教、交通、住宿、景点与休闲、金融、生活服务等类别开关（需先开"兴趣点"总开关）。
- **省电与后台**：推送长连接、消息处理、后台任务、埋点、桌面小组件、第三方标注过滤等**独立开关**（拦截可能影响行程提醒、家人地图通知、桌面卡片等宿主功能）。

## 使用前需要注意

- **⚠️ 许可证是 GPL-3.0**：项目代码按 GPL-3.0 发布（README 写 "GPL-3.0-or-later"），**具有传染性**；内置包另附 LSPatch 许可与来源说明。地图商标、数据、素材与原应用代码**属于各自权利人**，模块只是对官方 App 的修改，账号、支付等服务仍受原应用与服务条款约束。
- **⚠️ 仓库含二进制与构建产物**：`app/libs/` 下有**预编译 `.jar` 依赖**（含 `api-102.0.0.jar`、`service-classes.jar` 等）和**预编译 `.so`**（多 ABI）；`app/res/drawable/` 含图片（含 `donate_qr.jpg` 捐赠二维码）。**它不是纯源码仓库**，构建依赖见 `BUILD.md`。
- **⚠️ 需要特权 / 特殊环境**：模块版需**支持 API 102 的 Xposed 框架**（如 LSPosed）并勾选地图作用域；免 root 内置版走 **LSPatch 重签名**（Android 9.0+）。**免 root 不等于普通安装**——重签名后可能影响系统校验、支付 / 账号联动、系统卡片或厂商服务，README 自身也提示不要重复启用外部模块。
- **⚠️ 联网情况**：README 未宣称模块需要联网；但**地图本身联网**，且工具宫格 / 首页卡片 / 底栏使用官方下发配置，需"首次抓取官方数据"（可手动重抓）。核验时未验证其是否会引入额外上报。
- **⚠️ 自述与仓库的实际一致性**：**仓库描述写 v1.1.0，README 写 2.3.5.step2**——不是功能不符，而是**描述字段明显滞后**，引用版本时以 README / Release 为准。
- **⚠️ 功能与关闭有副作用**：拦截推送 / 小组件刷新可能影响行程提醒、家人地图通知、桌面卡片；"关闭/拦截"类开关开启表示阻止对应行为。第三方标注过滤及建筑 / 帧率选项**具有版本和性能边界，不适用于所有机型**；README 明确"本版本不作零耗电、零掉帧或安全软件绝不误报的承诺"。
- **⚠️ 部分能力尚未实机验证**：灵动岛（HyperOS 系统卡片）、百度打车支付、真实道路与车机导航、POCO F3 ARM 机型安装、Play 整包逐项测试等，README 自己标为未完成 / 待验证。
- **我未做任何运行验证**：**未 clone、未编译、未安装 APK、未接入任何框架、未在真机运行**；上述能力、命令、适配基线、验证边界**均来自 README、`LICENSE`、文件树与元数据自述**。

## 什么时候会再看

- 需要在**高德 / 百度 / 腾讯地图**上做去广告或界面精简，且能接受"绑定地图版本"的维护方式时。
- 想参考"**一份功能、两种分发（Xposed 模块版 + 免 root 内置重签名版）**"如何在一个仓库里并存，并把安装差异写清楚。
- 想借鉴它**把"适配版本基线 + 已知限制与验证边界 + 实测数据"写进 README** 的做法——这是 hook / 逆向类项目少见但很有用的诚实写法。
- 研究 Android 侧 **libxposed API 102 模块工程结构**（`META-INF/xposed/` 的 `module.prop`、`scope.list`、`native_init.list`）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同类"Android 应用 / 系统改造"工具，但改的是 APK 打包与反编译**：[Apktool：Android APK 反编译与打包工具](Apktool-AndroidAPK反编译与打包工具.md)——MapAdKiller 免 root 内置版本质是重签名宿主 APK，两者在做"改包"这件事上互为上下游。
- **同为"免 root 的特殊改动"安卓工具**：[Nrfr：免 Root 修改 SIM 卡国家码](Nrfr-免Root修改SIM卡国家码.md)——都在不 root 的前提下改动宿主行为，可对照其"免 root 路径"与风险提示的写法。
- **同为"改设备 / 系统自报值绕地区门控"，平台相对**：[GestaltX：给 iOS 改 MobileGestalt 设备配置的编辑器](GestaltX-给iOS改MobileGestalt设备配置的编辑器.md)——那是 **iOS** 侧改 **MobileGestalt 设备配置缓存**（含 Region Code）以尝试启用地区受限功能；本篇是 **Android** 侧 hook 宿主 App 去广告。两者都把"**可能改坏系统**"写进 README 并给出还原路径，可对照两平台的改动层面与风险提示写法。

## 参考来源

- 项目仓库与 README：https://github.com/ldxm666/MapAdKiller
- 当前 Release：https://github.com/ldxm666/MapAdKiller/releases/tag/v2.3.5.step2
- 许可（GPL-3.0）：https://github.com/ldxm666/MapAdKiller/blob/master/LICENSE
- 模块分发页：https://modules.lsposed.org/module/io.github.ldxm666.mapadkiller/
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 语言 / 默认分支 / Release 数 / 文件名）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未编译、未安装任何 APK、未在框架或真机上运行**；能力、适配基线与验证边界均来自 README、`LICENSE`、文件树与元数据自述。
