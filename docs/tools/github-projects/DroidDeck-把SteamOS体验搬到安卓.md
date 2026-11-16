# DroidDeck：把 SteamOS 体验搬到安卓

> 一句话定位（README 自述）：**"DroidDeck brings the SteamOS experience to Android"**——把 SteamOS 的**游戏模式（Steam Gaming Mode）、完整 Linux 桌面、GOG / Epic / Xbox 等商店集成、Steam VR** 搬进安卓手持设备。首页三句自我标榜：**"100% open source. No ads. No telemetry. No proprietary components. No source withheld."**（100% 开源、无广告、无遥测、无专有组件、无隐藏源码）。最值得记的三点：① 它**不是"模拟器前端"**，而是要装一整套**独立的 Linux 运行时**（运行时约 3 GB，桌面与模拟器另约 1.1 GB）；② 硬件门槛写得很实——**Android 9+、Adreno 730 或任意 8xx、无需 root**，而 **Adreno 6xx / Mali / Xclipse / PowerVR 属实验性、Adreno 710 明确不支持**；③ 许可证是 **GPL-3.0**，且**项目没有官网**——README 提醒"只能从本仓库的 Releases 下载"。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（163 行）、`LICENSE`（GPL-3.0，35,136 字符）与已核实元数据；**未 clone、未安装 APK、未安装 Linux 运行时、未在任何安卓设备上运行、未编译、未访问 Discord**）
- 适用范围：**安卓设备**（Android 9+，GPU 门槛见下）与安卓游戏掌机；构建侧需 **Docker、Java 17、Android SDK / NDK、Rust（rustup）、GitHub CLI、Python 3、`zstd`**，Windows 另有 `tools/run_windows.ps1` 与 `tools/prepare_windows_runtime.py`

## 核心结论

1. **定位是"把整套 SteamOS 体验搬到安卓"**（README 自述）：目标不止游戏——包含 **Steam 游戏模式、完整 KDE Linux 桌面、GOG / Epic Games / Xbox 等商店集成、Steam VR**；README 里"从 Steam 切到桌面"的 GIF 演示的正是手持机上启动游戏、再切到桌面的闭环。
2. **"无专有组件"是明确卖点，但依靠大量第三方开源组件拼装**：README 用大量 `<details>` 折叠块逐条列出**运行时与 Wine / Proton、商店、音视频与 HUD、桌面、智能眼镜、VR / Joy-Con**各层的来源与许可证（FEX、Wine / Proton、Mesa、MangoHud、FusionHUD、Syncthing、Flatpak、xodus-nexus 等），并声明"本项目不隶属于其中任何一方"。
3. **交付物 = 一个 APK + 一份独立的 Linux 运行时**：装完 APK 后再"安装 Linux 运行时"，然后按 **Play** 登录 Steam。Android 14+ 需在开发者选项打开 *"Disable child process restrictions"*；Android 12 / 13 首次启动 Steam 会弹 *"Fix it for me"* 引导。
4. **许可证为 GPL-3.0**（`LICENSE` 35,136 字符；正文含 "DroidDeck brings the SteamOS experience to Android / Copyright (C) 2026 DroidDeck contributors"）。

**个人判断（未验证）**：DroidDeck 更像一个"**把 PC 游戏与桌面生态硬塞进安卓手持机**"的系统层工程；其价值与风险都集中在**设备兼容性**——README 自己把 GPU 支持分成"支持 / 实验性 / 不支持"三档，说明体验对芯片型号高度敏感。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Droid-Deck/DroidDeck](https://github.com/Droid-Deck/DroidDeck) |
| 描述（原文） | "DroidDeck brings the SteamOS experience to Android" |
| 许可证 | **GPL-3.0**（`LICENSE` 35,136 字符）；README 另列 FEX(MIT)、Wine/Proton(LGPL)、Mesa、MangoHud(MIT)、FusionHUD(GPL-3.0 + 署名条款)、ReSkate(GPL-3.0) 等大量第三方组件 |
| 关注度 | **页面 Star 19,768 / Watcher 114 / Fork 1,002**；**ungh 镜像 19,703 / 114 / 998**（2026-11-13）；shields：开放 Issue **437**、贡献者 **91**（同日） |
| 维护状态 | **2026-09-24 创建**；ungh `pushedAt` **2026-11-13**、页面 `updatedAt` **2026-11-13**（当日推送，非常活跃）；默认分支 **`main`** |
| Release | **ungh 22 条 / 页面 23 条**（2026-11-13）；README 顶部直接链 **0.7.1 APK** 下载 |
| 分发 | **仅 GitHub Releases**（README 强调"没有官网，只从本仓库下载"）；文档在 `docs/`（含 `smart-glasses-support.md`、`docs/releases/`） |
| 社区 | Discord <https://discord.gg/JRGAvawjsm>（预览版、设备报告、`#bug-reports`） |
| 工程入口 | 构建脚本 `tools/build_local.sh`、`tools/deploy_local.sh`、`tools/run_windows.ps1`、`tools/prepare_windows_runtime.py`；产物在 `app/build/outputs/apk/release/` |

## 功能与用法

### 安装（README 四步）

1. 设备要求：**Android 9+**，GPU **Adreno 730 或更新，或任意 8xx**；**Adreno 6xx / Mali / Xclipse / PowerVR 为实验性**，**Adreno 710 暂不支持**；**无需 root**。
2. 空间：运行时约 **3 GB**，桌面与模拟器另需 **约 1.1 GB**。
3. 装 **APK → 装 Linux 运行时 → 按 Play → 登录 Steam**。
4. Android 14+ 打开开发者选项的 *"Disable child process restrictions"*；Android 12 / 13 首次启动 Steam 用 *"Fix it for me"* 引导。

### 构建（README）

需要 **Docker、Java 17、Android SDK 与 NDK、Rust（rustup）、GitHub CLI、Python 3、`zstd`**：

```bash
tools/build_local.sh          # APK 落在 app/build/outputs/apk/release/
# 用 tools/deploy_local.sh 安装；首次后可用 ./gradlew :app:assembleRelease 复用产物
./gradlew -PskipRust=true :app:testDebugUnitTest   # 单元测试
bash tools/test_local.sh                            # helper + preload 测试
```

Windows 见 `tools/run_windows.ps1` 与 `tools/prepare_windows_runtime.py`。

### 能力范围（README 摘要）

- **游戏侧**：Steam 游戏模式、Proton 运行 Windows 游戏、多手柄分屏、串流（Remote Play）。
- **桌面侧**：完整 KDE (Kubuntu) 桌面、pacman 仓库、Flatpak 商店、Syncthing 文件夹同步。
- **商店侧**：GOG、Epic Games、Xbox Cloud Gaming、Battle.net、EA、itch.io 等（多数靠第三方开源客户端适配）。
- **外设侧**：智能眼镜（VITURE / RayNeo / XREAL / Rokid / REDMAGIC）、Steam VR、Joy-Con 与摄像头手部追踪。
- **限制（Limits）**：兼容性与性能**因设备而异**；**桌面合成是软件渲染**；**Firefox 在 proot 下沙箱被削弱**。

## 使用前需要注意

- **GPL-3.0 的传染性**：DroidDeck 本体 GPL-3.0，若你要**分发修改版或把它嵌进自己的产品**，需按 GPL-3.0 公开对应源码；README 逐条列出的第三方组件各有各的许可证（含 MIT / LGPL / GPL-3.0 / BSD 等），**混用前需逐个确认**。
- **设备兼容性是最大不确定性**：README 明写"**兼容性与性能因设备而异**"；GPU 支持分档（8xx 支持、6xx 等实验、710 不支持）意味着**同一 APK 在不同机型上体验可能天差地别**，建议先看 Discord 的设备报告（**个人判断（未验证）**）。
- **桌面是软件渲染 + 沙箱被削弱**：README 的 Limits 指出"**Desktop compositing is software rendered**"，且"**Firefox sandboxing is reduced under proot**"——这既是**性能限制**也是**安全边界**，别把它当成日常办公桌面用。
- **依赖安卓的开发者开关**：Android 14+ 必须手动打开 *"Disable child process restrictions"*，**这不是普通用户默认会做的设置**。
- **要额外装 Linux 运行时**：占用数 GB，且运行时是**独立于 APK**安装的映像，首次使用需另外下载 / 安装，**离线不可用**（**个人判断（未验证）**）。
- **分发渠道单一**：无官网，README 强调"只从本仓库 Releases 下载"——**第三方镜像 / 二次打包有风险**，请核对 Release 校验信息。
- **我只读了 README 与 LICENSE**：**未 clone、未安装 APK、未装运行时、未在任何设备上运行、未编译**；上述能力均来自 README 与许可证文本，**未逐一验证**。

## 什么时候会再看

- 想在**安卓掌机 / 手机**上跑 Steam 与 Windows 游戏（而不是只在 PC 上）时。
- 想了解"**把 Linux 桌面与 PC 游戏栈搬进安卓**"要处理哪些层（运行时、Wine / Proton、商店、HUD、外设）时。
- 想参考"**大量第三方开源组件如何在一个项目里被逐条署名与合规**"的写法（README 的 `<details>` 折叠说明非常完整）时。
- 想对照"**把桌面级 / 复古体验移植到现代设备**"的其它路线：[ra2（浏览器跑红警 2）](ra2-浏览器运行红色警戒2.md) · [aerial-tv（给国产电视的 tvOS 风格桌面）](aerial-tv-给国产电视的tvOS风格桌面.md)。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都是"把 PC 级体验搬进非 PC 设备"**：[ra2：浏览器运行红色警戒 2](ra2-浏览器运行红色警戒2.md)——那篇用 **WebAssembly + 浏览器**复刻一款 PC 老游戏；本篇用**安卓 + Linux 运行时**复刻整套 SteamOS 体验。可对照"**浏览器内的单机复刻**"与"**系统层的生态移植**"。
- **都属"给大屏 / 电视设备做桌面式体验"**：[aerial-tv：给国产电视的 tvOS 风格桌面](aerial-tv-给国产电视的tvOS风格桌面.md)——那篇把 **tvOS 风格桌面装到电视**；本篇把 **SteamOS 游戏模式与桌面装到安卓掌机**。一个偏"客厅大屏"，一个偏"手持便携"。
- **同为"第三方独立实现"**：[BiliSail：跨平台 Bilibili 第三方客户端](BiliSail-跨平台Bilibili第三方客户端.md) · [NonsPlayer：多音源跨平台音乐播放器与插件系统](NonsPlayer-多音源跨平台音乐播放器与插件系统.md)——都需在**非官方环境里重实现大量能力**，可对照各家如何处理**兼容性与合规**。
- **同属"把花活塞进小设备"**：[screenplink：把显示器当靶子的激光干射射击游戏](screenplink-把显示器当靶子的激光干射射击游戏.md)——一个偏"硬件创意"，一个偏"安卓上的完整游戏生态"。
- **同为"安卓与桌面体验互搬"，方向与许可相反**：[Android DEX：把安卓应用搬进桌面窗口的闭源镜像工具](Android-Dex-把安卓应用投到桌面的闭源镜像工具.md)——本篇是**把 SteamOS / Linux 桌面搬进安卓**（GPL-3.0、开放源码、首页自称无广告 / 无遥测 / 无专有组件），那篇是**把安卓应用搬到桌面**（闭源专有、ADB + scrcpy）；一个方向是"桌面 → 安卓"，一个是"安卓 → 桌面"，且许可与可审计性截然相反，可对照。

- **同为"把 PC 级体验搬进非 PC 环境"**：[nfsu2-web：在浏览器里跑《极品飞车：地下狂飙 2》](nfsu2-web-在浏览器里跑极品飞车地下狂飙2.md)——那篇用浏览器 + WASM 复刻一款 PC 老游戏，本篇用安卓 + Linux 运行时复刻整套 SteamOS 体验；同属"跨设备复刻体验"，技术栈差异很大。

## 参考来源

- 仓库主页与 README（163 行；核验日期 2026-11-13）：https://github.com/Droid-Deck/DroidDeck
- 许可证：`LICENSE`（GPL-3.0，35,136 字符；核验日期 2026-11-13）
- 文档：`docs/smart-glasses-support.md`、`docs/releases/`（各版本截图与说明）
- 社区：Discord <https://discord.gg/JRGAvawjsm>
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc 交叉核对（2026-11-13，**页面值与镜像值略有差异**）；开放 Issue 数 / 贡献者数取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未安装 APK、未安装 Linux 运行时、未在任何安卓设备上运行、未编译、未访问 Discord**；能力与流程描述均来自 README，**未逐一验证**。
