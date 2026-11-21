# GestaltX：给 iOS 改 MobileGestalt（设备配置缓存）的编辑器

> 一句话定位（README 自述）：**"A MobileGestalt editor for iPhone and iPad"**，GitHub 描述为 **"An MobileGestalt editor for iOS 27.x, featuring region-code tweaks, device presets, backups."** 三件事先讲清：① 它改的是 iOS 的 **MobileGestalt 系统配置缓存**（主要是 `CacheExtra` 字段），用"能力标志 + 预设"来开关 / 改写设备功能（分 **Display / Device / iPad / Intelligence / Advanced** 五类）；② **最被关注的用途是"在（中国大陆）设备上尝试启用 Apple Intelligence"**——通过**改 Region Code**（如选 United States 会写入 `LL`）、若干 AI 能力标志与 Siri 模式来试；③ **⚠️ 底层走的是私有 / 未公开机制**（自述由 `worse_query` 提供设备端系统缓存访问、`NeoSpring` 负责 respring），**README 自己就记录了"硬件伪装曾弄坏 Face ID / 听写"的情况并给出恢复流程**——改 MobileGestalt 有**系统不稳定 / 需要恢复设备**的风险。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `README.md`（英文主文件）、`THIRD_PARTY_NOTICES.md`、文件清单（370 项）与 ungh 镜像 + shields 元数据；**未 clone、未编译、未安装 IPA、未在任何 iOS 设备上运行、未修改任何设备配置、未审计源码**）
- 适用范围：**iOS / iPadOS 27.x** 的 iPhone / iPad；**仅限你自有或获授权管理的设备**；构建侧需 **Xcode**（含 Rust 工具链以编 `Native/DeviceConsole` 的 XCFramework）；分发走**侧载**（SideStore / AltStore）

## 核心结论

**事实：它的对象是 MobileGestalt——iOS 的私有"设备配置存储"。** 工具围绕其 `CacheExtra` 字段做**读 / 搜 / 增 / 改 / 删**，并内置一批"能力标志"预设，分为五类（见下）。README 强调：**预设改的是"能力标志"，实际效果取决于具体机型与系统版本**——"标志置位"不等于"功能真的可用"。

**事实：最受关注的用途是"在中国大陆设备上尝试启用 Apple Intelligence"。** README 专设一节说明：**通常只改 Region Code 就够了**——选 United States 会把 `RegionCode` 设为 `LL` 并保留销售地区后缀；也可选中国大陆 / 香港 / 台湾 / 日本 / 英国。**Sales Region**（销售地区后缀）可与 Region Code 组合使用。⚠️ 但 README 明确写了"**这些是实验性标志，不保证能下载到资源或让 Apple Intelligence 真正工作**"，能否生效还取决于机型、系统版本与 Apple 服务。

**事实：它自述借鉴了同类项目的"不伪装硬件"路线。** README 引述 **YumEdit 1.2.1** 的做法——**不伪造 product / board / CPU**，只把生成式模型标志置 `1`（Apple Intelligence Capability）并把 `SiriMode` 置 `2`（Siri AI Mode），**两者都不改硬件身份**。README 声明 **GestaltX 不实现临时硬件伪装**。

**判断：这属于 iOS 侧"改系统配置以解锁被地区 / 机型门控能力"的一类工具**，与 Nugget / MGKeys / mond / GestaltEdit 属同一谱系（README 把它们列为**参考**、未打包其代码）。它的价值与风险都集中在一处：**改的是系统级私有配置——既能开功能，也能把设备改坏。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [huami1314/GestaltX](https://github.com/huami1314/GestaltX) |
| 描述（原文） | "An MobileGestalt editor for iOS 27.x, featuring region-code tweaks, device presets, backups." |
| 归属 | 账号 `huami1314`；**⚠️ README 内的徽章与版权署名指向 `huamidev/GestaltX`**（`Copyright © 2026 huamidev`），疑似同一作者的**账号改名 / 别名**，未核实 |
| 许可证 | **GPL-3.0**（`LICENSE` 35,149 B）+ **`THIRD_PARTY_NOTICES.md`**（2,706 B，逐条列出捆绑组件与参考对象） |
| 主语言 | Swift 80.8%（shields 顶层语言）；另含 **ObjC**（`GXCustomizationAccess.m` 等）与 **Rust**（`Native/DeviceConsole`） |
| 关注度 | **8,441 Star / 23 关注 / 2,734 Fork**（ungh，2026-11-19；shields 同期 8.4k / 2.7k）、**100 开放 Issue、贡献者 16** |
| 维护状态 | **2026-11-09 创建**（核验时约 10 天）；ungh `pushedAt` **2026-11-17**、`updatedAt` 2026-11-19；默认分支 **`main`** |
| 发行 | **8 个 Release**（十天内连发：v1.0.1 `2026-11-09` → **v1.1.1 `2026-11-13`**，最新含 3 个资产约 6.8 MB；早期 1 个资产约 2.4 MB） |
| 体积 | **370 文件**：`Native/` 188 · `GestaltX/` 88 · `tests/` 60 · `localization/` 9 · `ShareExtension/` 5 · `GestaltX.xcodeproj/` 3 · `.github/` 2 · `scripts/` 2 · `Shared/` 2 · `Configuration/` 1；根文件含 5 种语言的 README 与中英双份 CHANGELOG |

## 功能（README 预设表摘要）

| 分类 | 预设 / 工具（择要） |
| --- | --- |
| **Display** | 灵动岛能力与布局、常亮显示（AOD）、AOD Vibrancy、高亮度 AOD、AOD 翻页画册、HDR 照片显示、壁纸视差、Liquid Glass 低功耗渲染、实验性 PWM 与 DisplayPort 标志 |
| **Device** | 机型名、开 / 关机音效、充电上限、轻点唤醒、相机控制设置、Action Button 设置、碰撞 SOS、Audio Mix、空间 FaceTime 音频 |
| **iPad** | Apple Pencil 设置、台前调度（Stage Manager）能力、iPad 应用兼容性 |
| **Intelligence** | **独立区域码选项**、**Apple Intelligence 能力标志**、设备端 Siri、Audio Intelligence、Personal Translator、Photos Clean Up、手写合成、常听 Hey Siri、面向**中国大陆设备激活尝试**的销售地区选项 |
| **Advanced** | Apple 内部功能标志、全系统 `InternalBuild` 标志（**需显式确认**）、内部设置 bundle、Security Research Device 标志 |
| **字段编辑器** | 读 / 搜 / 增 / 改 / 删 `CacheExtra` 字段；本地 plist 导入 / 导出；支持 String / Integer / Real / Boolean / Data / Date / Array / Dictionary |
| **变更与备份** | 自动读取 + 手动重读、暂存前后对比、验证写入、按预设还原；**编辑前自动首次备份、每次写入前再备份、记录原值**；备份可打开 / 导出 / 还原 / 删除 |
| **日志与 respring** | 实际"exploit 阶段"日志、手动 **NeoSpring** respring |

**工程结构（仅列骨架）**：`GestaltX/`（SwiftUI 应用 88 文件，如 `ContentView.swift` 50,155 B、`Capabilities.swift` 14,869 B，及 ObjC 桥接 `GXCustomizationAccess.m` 31,288 B）+ `ShareExtension/`（分享扩展，经共享 App Group 收 plist）+ `Shared/`（App Group 与 plist 收件箱）+ **`Native/DeviceConsole/`（188 文件：Rust crate，vendoring `idevice-ffi` 包装 libimobiledevice 的 afc / amfi / core_device / debug_proxy / diagnostics 等服务，编成 XCFramework）** + `tests/`（60 个测试）+ `localization/`（9 份 JSON，含 zh-Hant-TW / zh-Hant-HK）。

## 使用与分发要点

- **获取**：从 [Releases](https://github.com/huami1314/GestaltX/releases/latest) 下载 IPA。README 提示：部分用户在 **iOS 27 的 Safari** 下载会报"文件损坏 / 无法解压"，需**重试多次或换浏览器**（原因未确认）。
- **签名**：**侧载分发**——发布的 IPA **带 ad-hoc 签名与 entitlements**，供 **SideStore / AltStore 重签名**检测；**非开发者证书签名**。自行签名需**证书 + provisioning profile**，且要求**至少一个可用的 App Group**，**App 与内嵌分享扩展须共用同一个 App Group**。
- **兼容性**：支持 **iOS / iPadOS 27.x**；**26.x 不支持设备端 MobileGestalt 访问**；底层"Powered by **worse_query**"、**SystemGroup 访问需 iOS 27**；也支持**本地 plist 编辑**（无需设备端访问）。
- **导入 plist**：在"文件"等应用里选中 MobileGestalt plist → 分享 → 选 GestaltX；应用会校验**设备绑定证明**（或至少两个来自本机的唯一标识）后要求确认；**无法验证的文件被拒绝**；导入过的文件**不会**被当作设备身份参照。
- **恢复**：若此前"硬件伪装"弄坏了 Face ID / 听写，可在 **Backups → Restore Hardware Identity** 或 **Intelligence → Restore Hardware Identity** 还原**原始硬件身份**（仅还原 product / board / CPU 与原始监管型号，保留当前 AI / Siri / 区域等设置）；**当前仅识别 iPhone 11 ~ iPhone 15/15 Plus（含 SE 第 2 / 3 代）**，其它机型**直接拒绝、不猜芯片**。恢复后需**完整重启**再测 Face ID / 听写 / Apple Intelligence。

## 使用前需要注意

- **⚠️ 能改坏系统**：README 自述"**修改 MobileGestalt 可能导致系统不稳定或需要设备恢复**"，并**专门写了"此前硬件伪装弄坏 Face ID / 听写"**的恢复流程——**这类操作是有真实翻车先例的**，且恢复**只覆盖特定机型**。
- **⚠️ 涉及绕过地区 / 机型门控**：核心用途之一是**绕开 Apple 对 Apple Intelligence 的地区限制**（改 Region Code / Sales Region，含"面向中国大陆设备的激活尝试"）；这可能**违反 Apple 服务条款**，且 README 自己声明"**效果不保证**"。
- **⚠️ 依赖私有 / 未公开机制与设备端写入**：由 `worse_query`（GPL-3.0-or-later，其"escape 实现"原样保留）与 `NeoSpring` / WebKit respring 支撑，**均非公开 API**，随 iOS 版本变化**易失效**。
- **⚠️ 侧载 + 需自备签名**：发布的 IPA 是 **ad-hoc 签名**，靠 SideStore / AltStore 重签名安装，**非 App Store**；自行签名要证书与**含可用 App Group 的 provisioning profile**。
- **⚠️ 极新 + 迭代极快**：**2026-11-09 创建**（约 10 天），**十天内连发 8 个 Release**、**100 个开放 Issue**、16 贡献者——更新快但**稳定性与问题积压都未知**。
- **⚠️ 仅供自有 / 获授权设备**：README 免责声明原文——"**For security research and educational purposes only. Use only on devices you own or are authorized to manage.**"，并要求改前备份数据、自担后果。
- 本笔记**只读 README、`THIRD_PARTY_NOTICES.md`、文件清单与元数据；未 clone、未编译、未安装、未在设备上运行、未改任何配置、未审计源码**。

## 什么时候会再看

- 想了解"**iOS 的 MobileGestalt / CacheExtra 是什么**、这类"能力标志"编辑在**结构上**怎么组织"时（仅概念与结构层面）。
- 想参考"**改系统级配置的工具如何做变更管控**"（写前备份 / 原值记录 / 暂存对比 / 验证写入 / 按预设还原 / 设备绑定导入校验）时。
- 想对照"**在设备识别层绕开地区限制**"的两条路（Android 改 SIM 国家码 vs iOS 改 MobileGestalt 区域码，见下）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"改设备地区识别以绕地区限制"，平台相对**：[Nrfr：免 Root 修改 SIM 卡国家码](Nrfr-免Root修改SIM卡国家码.md)——那是 **Android** 侧改 SIM / 运营商国家码（为 TikTok、Samsung Health 等按地区限制的服务）；本篇是 **iOS** 侧改 MobileGestalt 的 **Region Code / Sales Region**（为 Apple Intelligence）。两者都是"**改设备识别层**"的绕行手段，可对照平台差异与风险提示的写法。
- **同为"iOS 设备端工具"，方向不同**：[fairplay-decrypt：离线解密 App Store IPA 的 FairPlay 工具](fairplay-decrypt-离线解密App-Store-IPA的FairPlay工具.md)——那篇是 iOS 侧**解密 IPA**（移除 DRM 保护）；本篇是 iOS 侧**改设备配置**。两者都涉及**绕过 Apple 的某种限制**，使用前都需确认授权与合法性边界。
- **同为"免 root 的移动端特殊改动"**：[MapAdKiller：高德百度腾讯地图去广告 LSPosed 模块](MapAdKiller-高德百度腾讯地图去广告LSPosed模块.md)——那是 **Android** 侧改宿主机应用行为（含免 root 重签名路线）；本篇是 **iOS** 侧侧载改系统配置。可对照两端"**改系统 / 改应用**"与"**免 root 路径**"的取舍。

## 参考来源

- 项目仓库与 README：https://github.com/huami1314/GestaltX
- `THIRD_PARTY_NOTICES.md`（worse_query / NeoSpring / 参考项目 / GitHub mark 许可）：同仓库 `main`
- README 引述的同类项目：YumEdit、MGKeys、Nugget、mond、GestaltEdit、worse_query、NeoSpring（见其 Credits）
- 元数据（Star / Fork / 创建与推送时间 / Release / 默认分支）：ungh.cc 镜像 https://ungh.cc/repos/huami1314/GestaltX 与 shields.io 徽章

以上信息核验于 2026-11-19（仓库创建约 10 天，更新极快）。仅阅读 README、`THIRD_PARTY_NOTICES.md`、文件清单与元数据；未 clone、未编译、未安装、未在设备上运行、未修改任何设备配置、未审计源码。**本笔记只作信息记录，不构成任何操作指引或背书。**
