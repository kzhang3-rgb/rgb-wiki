# Android DEX：把安卓应用搬进桌面窗口的闭源镜像工具（"通用版三星 DeX"）

> 一个**闭源**的桌面程序（Windows / Linux / macOS），自称"**所有安卓设备都能用的三星 DeX 替代**"：用 **ADB + scrcpy** 把安卓应用投到电脑上，做成**可缩放窗口**，支持多应用同开、屏幕镜像、逐应用音频、通知管理、媒体控制，并用键鼠原生玩手游。README 首屏就标了 **"Closed Source"** 徽章。**最需要看清的是它的 "Gaming Mode（No Emulator Detection）" 一节**——它明确把"**反作弊系统会把你识别成真手机**"当卖点，并配了**连点/连发（turbo）、D 键摇杆、FPS 鼠标锁定 + 陀螺仪模拟、脚本与组合宏**等一整套"手游宏"能力；**这在多数手游的用户协议下属于作弊工具，可能直接导致封号**。另外**这个 GitHub 仓库里没有应用源码**——它只有**官网页面 + 文档 + Release 里的可执行包**（共 49 个文件）。**未下载安装包、未安装、未运行、未连接任何设备**。

- 状态：草稿
- 最近核验：2026-11-16（阅读 `main` 分支 README 全文（158 行）、`LICENSE` 前 40 行、文件清单（49 条目）与元数据；Release 列表、`commits/main.atom`、ungh 镜像、GitHub 页面内嵌 JSON 与 shields 徽章。**未 clone、未下载安装包、未安装、未运行、未连接任何安卓设备、未开启 ADB、未审计二进制**）
- 适用范围：**桌面端**（Windows / Linux / macOS）；被控端为**安卓设备**（需开启 ADB 调试，USB 或无线配对）

## 核心结论

**一句话定位（描述）**：`Universal Samsung DeX alternative for all Android devices. Run Android apps on Windows, Linux & macOS with resizable windows, advanced FPS gaming controls, and high-performance wireless ADB mirroring.`

**① 它是什么。** 一个**闭源、免费（非商用）的桌面端"安卓窗口化"工具**：借 **ADB** 与设备通信、用 **scrcpy** 做低延迟画面镜像与远程输入，界面用 **Flutter** 写，被控端另有一个 **Kotlin** 写的伴侣服务（媒体监听、设置界面）。目标是把手机上的应用变成桌面上**可缩放、可多开**的窗口，并把**音频、通知、媒体播放**都收进来。

**② 关键前提：它要你把手机的 ADB 调试打开。** ADB 是安卓的**高权限调试通道**；再叠加"无线配对"，等于**在你家网络里留了一条能控制手机的通道**。README 承诺"**100% 本地处理 / 零数据收集 / 无云端、完全离线 / 直连本地 ADB**"——这些**在闭源前提下无法被外部验证**。

**③ 仓库不是源码仓库。** `main` 分支共 **49 个文件**：官网页面（`index.html` 27,685 字节、`download.html` 43,825 字节、`style.css` 44,938 字节）、文档（`doc/` 下 8 篇 Markdown + `web/` 下同名 HTML 镜像）、`README` / `LICENSE` / `NOTICE` / `SECURITY.md`、`Data/`（13 个截图与一段 29.7 MB 演示视频）、`.github/`、`.idea/`（**IDE 配置被提交进来了**）与一个 **Google Search Console 验证文件**（`google27bfd0b0515c9981.html`）。**想读源码要看别的仓库——这里没有。**

**④ 最重的风险是 "Gaming Mode"。** README"Gaming Mode (No Emulator Detection)"一节原话大意：**因为游戏直接跑在你的真机上、触摸事件通过底层原生协议下达，反作弊系统会把你的设备当成真手机，从而"不受模拟器封禁或限制"**。它随之提供的控制项包括 **Tap Spot（单点 / turbo 连发）**、**D-Pad / 摇杆**、**FPS 鼠标锁定与陀螺仪瞄准**、**右键开镜（ADS）**、**滑动 / 技能触发**、**脚本与组合宏**、**按游戏保存的控制档**。**个人判断：这就是一套面向手游的宏 / 作弊工具，且以"绕过反作弊识别"为卖点**——与本库其他工具不同，它**不是"更高效地干正事"，而是"在规则之外获得优势"**，务必按这个定性来评估。

**个人判断（未验证）**：作为技术样本，它有意思的地方是"**用开源组件（ADB / scrcpy）拼出一个闭源产品**"这层组合；但作为"我要不要装"，**它是本库中同类项里风险定性最重的一个**：闭源不可审计 + 需要 ADB 高权限 + 主推规避反作弊的手游宏。**能不用就不用；要与不用，先想清楚封号与合规代价。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Shrey113/Android-Dex](https://github.com/Shrey113/Android-Dex)（README 顶部警示："**Official Distribution Only** —— 本仓库是 Android DEX 唯一官方且被授权来源"） |
| 描述 | "Universal Samsung DeX alternative for all Android devices. Run Android apps on Windows, Linux & macOS with resizable windows, advanced FPS gaming controls, and high-performance wireless ADB mirroring." |
| 归属 | 个人开发者 **Shrey113**（`LICENSE` 署 `Copyright (c) 2026 Shrey Patel`）；README 提供 "Buy Me a Coffee" 打赏入口 |
| 许可证 | **闭源 / 专有（Proprietary）**——`LICENSE` 4764 字节（标题 "ANDROID-DEX PROPRIETARY SOFTWARE LICENSE"）：**仅供个人 / 教育 / 研究 / 测试使用，不得商用**；**禁止再分发、修改、重打包、镜像站、打包进第三方安装器（含下载器 / 广告 / 工具栏）**；允许创作相关内容但**不得以此变现**（不得有广告 / 赞助 / 联盟 / 付费推广）；商用需**书面授权**。因是自定义许可证，**shields 与 GitHub 均识别为 `not identifiable by github`** |
| 技术栈 | **ADB**（设备通信、无线配对、命令执行）+ **scrcpy**（画面镜像与远程输入，GPL）+ **Flutter**（桌面客户端 UI）+ **Kotlin**（安卓伴侣服务 / 媒体监听 / 设置界面）；README 特别致谢 scrcpy 作者 `rom1v` |
| 主语言 | **HTML 62.4%**（shields，2026-11-16）——反映"仓库主体是官网页面，不是应用源码" |
| 关注度（2026-11-16） | **3,831 Star / 32 关注 / 265 Fork**（ungh 镜像）；**页面内嵌 3,893 / 32 / 270**（略高，属时间差）；shields 显示 **3.9k Star、270 Fork**；**开放 Issue 3 个、贡献者 1 位**（shields） |
| 创建 / 最近推送 | **2026-02-02 创建**；`pushed_at` **2026-11-07**；`commits/main.atom` 最新提交 **2026-10-31T14:50:32Z**（`add Buy me a coffe`，作者 `Shrey113`）→ `pushed_at` 与最新 commit 有约一周差（可能来自分支 / 标签推送） |
| 默认分支 | `main`；无 GitHub Pages（页面 `pagesUrl` 为空） |
| Release / 文件数 | **16 条 Release**（页面 `releaseCount`）；最新正式版 **`Android-Dex-v.1.5`**（非预发布，`publishedAt` **2026-10-31**，创建于 2026-09-25），发布说明提到"**Rust 驱动的原生核心**"、Windows ARM64 与 Flatpak 打包、无线配对与桌面定制；`main` 共 **49 个文件** |
| 分发 | GitHub Releases 提供 **`Android_Dex_Windows.zip` / `Android_Dex_Linux.zip` / `Android_Dex_macOS.zip`**（README 表格里的 `releases/latest/download/...` 固定链接）；README 强调**只从本仓库下载** |
| 站内文档 | `doc/`：`ARCHITECTURE.md`、`BOOT_FLOW.md`、`DATA_MODEL.md`、`DEVICE_MANAGER.md`、`ERROR_HANDLING.md`、`MODULES.md`、`RECONNECTION.md`、`scrcpy.md`；`web/` 为其 HTML 镜像；另有 `SECURITY.md`、`NOTICE` |

> 口径说明：数据来自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章 + `commits/main.atom`**（核验日未直接调用 api.github.com）。**shields 的 Issue / 贡献者数**为单独来源；Star / Fork 会随时间变化。

## 使用前需要注意

- **🔴 "No Emulator Detection" + 宏 / 连发 = 手游作弊工具**：README 把"**反作弊识别不出模拟器**"当卖点，并给 **turbo 连点、脚本与组合宏、FPS 自瞄（鼠标锁定 + 陀螺仪）** 等能力。**这很可能违反你所玩手游的用户协议**，一旦被判定为作弊，**封号 / 封设备后果由使用者自负**。**本笔记记录其存在，不为其用途背书。**
- **🔴 需要开启手机 ADB，且支持无线**：ADB 是高权限调试通道。README 声称"**100% 本地处理、零数据收集、无云端、完全离线、直连本地 ADB**"——但**应用是闭源二进制，这些承诺无法从外部验证**。请按"**我把高权限通道交给了一个不可审计的闭源程序**"来权衡。
- **⚠️ 源码不在这个仓库里**：仓库只有官网 + 文档 + Release 包（**HTML 占 62.4%**）。**"能读文档"不等于"能审代码"**；安全性完全依赖对作者（**1 位贡献者**）的信任。
- **⚠️ GPL 组件与闭源外壳的组合值得留意**：它捆绑 **scrcpy（GPL-3.0）**。README 在 License 末尾写"**第三方开源组件（含 scrcpy）仍受各自许可约束**"，`NOTICE`（417 字节）应为第三方声明。**闭源产品如何合规地使用 / 分发 GPL 组件，是一个需要发行方说明的问题**——此处只作标注，不作结论。
- **⚠️ 单一贡献者 = 单点依赖**：**贡献者 1 位**、开放 Issue 3 个；`pushed_at` 与最新 commit 存在时间差。**维护与安全都压在一个人身上**。
- **⚠️ 提交了 IDE 配置与验证文件**：文件清单里含 `.idea/`（JetBrains 工程配置）与 `google27bfd0b0515c9981.html`（Google Search Console 验证文件）——属工程卫生层面的小瑕疵，也再次说明**这是"发布站仓库"而非产品源码仓库**。
- **⚠️ 许可口径要看清**：它自称 "**free**"，但许可证实为**专有**——"免费"指**个人用途不收费**，**不是**"开源 / 可自由使用"；**禁商用、禁再分发、禁修改**。引用时别把它当自由软件。
- **本笔记只读了 README、`LICENSE`（前 40 行）、文件清单与元数据**：**未下载安装包、未安装、未运行、未连接任何安卓设备、未开启 ADB、未审计二进制**；功能与安全描述**均引自 README**，未独立验证。

## 什么时候会再看

- 想参考"**用 ADB + scrcpy 把安卓应用窗口化到桌面**"这类工具的产品形态与功能清单时（**仅作了解，评估风险后再决定是否使用**）。
- 想对照"**同是跨设备画面 / 输入搬运，方向与协议不同**"的几条路线时：见 [crossdesk](crossdesk-轻量跨平台远程桌面与MCP远控服务端.md)（桌面↔桌面远控）与 [DroidDeck](DroidDeck-把SteamOS体验搬到安卓.md)（把桌面体验搬进安卓，方向相反）。
- 想研究"**开源组件（scrcpy）如何被闭源产品外壳包住**"这一组合与其中的 GPL 合规问题时（**仅作观察样本**）。
- 想提醒自己"**闭源 + 高权限 + 规避反作弊**"三者叠加的风险定性时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"跨设备搬运画面与输入"，协议与方向不同**：[crossdesk：轻量跨平台远程桌面与 MCP 远控服务端](crossdesk-轻量跨平台远程桌面与MCP远控服务端.md)——那是**桌面↔桌面**的远程桌面（自有远控协议 + MCP，开源）；本篇是**安卓→桌面**的窗口化镜像（ADB + scrcpy，闭源）。两篇可对照"**远控一台完整桌面**"与"**把手机应用搬成桌面窗口**"。
- **方向相反的"把桌面体验搬进安卓"**：[DroidDeck：把 SteamOS 体验搬到安卓](DroidDeck-把SteamOS体验搬到安卓.md)——本篇是**把安卓应用搬到电脑**，那篇是**把 SteamOS / Linux 桌面搬进安卓**；两者都自制"DeX 式桌面"，但一个**开源（GPL-3.0）且主打无遥测**，另一个**闭源**。**并列看"开源 vs 闭源"的同题做法**很直观。
- **同属"给安卓设备换一套桌面体验"**：[aerial-tv：给国产电视的 tvOS 风格桌面](aerial-tv-给国产电视的tvOS风格桌面.md)——那篇在**安卓电视**上通过 ADB 安装第三方桌面、抢回主页键；本篇把**安卓手机的窗口搬上电脑**。两者都要**开 ADB**，可对照"改安卓的桌面"与"把安卓搬到桌面"。
- **同属"安卓 + ADB / 系统级权限"，风险面可对照**：[dsh-Go：DeepSeek Harness 的安卓客户端](dsh-Go-DeepSeek-Harness的安卓客户端.md)——那篇靠**无障碍 / Shizuku / ADB** 做手机操控，README 自己也提示"不在存有支付或银行信息的设备上解除限制"；本篇把 ADB 控制权交到桌面端。**两篇都该先看清"权限范围 + 数据流向"再决定装不装。**
- **方向相反的"把 PC 游戏搬进安卓"**：[GameNative：在安卓上原生跑自己已有的 PC 游戏](GameNative-在安卓上原生跑已有PC游戏的容器式客户端.md)——本篇是**把安卓应用搬到桌面**（闭源专有、ADB + scrcpy），那篇是**把 PC 游戏搬进安卓**（GPL-3.0、Wine / Proton 兼容层）；一个方向是"安卓 → 桌面"，一个是"桌面 → 安卓"。可对照两篇在**许可（专有 vs GPL-3.0）、可审计性与数据流向（闭源 vs 开源）**上的差别。

## 参考来源

- 仓库主页与 README（`main` 分支，158 行；核验日期 2026-11-16）：https://github.com/Shrey113/Android-Dex
- 许可证：`LICENSE`（ANDROID-DEX PROPRIETARY SOFTWARE LICENSE，4764 字节，`Copyright (c) 2026 Shrey Patel`）与 README "License" 一节；第三方声明见 `NOTICE`（417 字节）与 `SECURITY.md`
- 站内文档：`doc/ARCHITECTURE.md`、`doc/BOOT_FLOW.md`、`doc/DATA_MODEL.md`、`doc/DEVICE_MANAGER.md`、`doc/ERROR_HANDLING.md`、`doc/MODULES.md`、`doc/RECONNECTION.md`、`doc/scrcpy.md`（`web/` 下有同名 HTML 镜像）
- 下载（README 指定固定链接）：`releases/latest/download/Android_Dex_Windows.zip` / `Android_Dex_Linux.zip` / `Android_Dex_macOS.zip`
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / 文件清单）：ungh.cc 镜像、GitHub 页面内嵌 JSON 与 `commits/main.atom`，2026-11-16
- 开放 Issue 数与贡献者数、许可证识别、主语言占比：shields.io 徽章，2026-11-16
- Release 列表（16 条，最新 `Android-Dex-v.1.5`，2026-10-31）：ungh.cc releases API 与页面 `releaseCount`，2026-11-16

以上信息核验于 2026-11-16。Star / Fork、Issue 数与仓库内容随时可能变化；**未下载安装包、未安装、未运行、未连接任何安卓设备、未开启 ADB、未审计二进制**。功能与安全描述均引自 README，**未独立验证**。README 中"**No Emulator Detection**"及配套宏 / 连发能力仅作事实记录，**本笔记不为其用途背书，也不构成使用建议**。
