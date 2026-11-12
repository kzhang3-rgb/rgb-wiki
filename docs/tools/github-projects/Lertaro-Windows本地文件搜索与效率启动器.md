# Lertaro：Windows 本地文件搜索与效率启动器

> 记录一个用 **.NET 10 + C# WPF** 写的 Windows 全局搜索/启动器：直接读 NTFS 的 **USN Change Journal 与 $MFT** 做实时索引，对标的正是 **Listary** 与 **Everything**。第一句先说清三件事：**仓库与账号同名（`Lertaro/Lertaro`）但它不是个人资料仓库，而是一个成品软件仓库**；**它对标 Everything/Listary 但并非其分支，仓库里也没有专门记那两个项目的笔记**；**项目非常新（2026-08-06 创建，核验时约 3 个月）却已有上千 Star**——关注度与成熟度是两回事。

- 状态：草稿
- 最近核验：2026-11-12（阅读 README 全文（英文主 README + 目录结构）、`LICENSE`、`main` 分支完整文件树（2,950 个文件）与元数据、页面内嵌 sidebarAbout、shields.io 语言占比；**未 clone、未编译、未安装、未运行、未装插件、未审计源码**）
- 适用范围：**Windows 10/11（x64 与 ARM64）**；从源码构建需 **.NET 10 SDK + Visual Studio 2022 或 JetBrains Rider**，要打安装包还需 **Inno Setup 7（64 位版）**；面向"用键盘快速找本地文件 / 启动程序"的日常场景

## 核心结论

**一句话定位（README 自述）**：Lertaro 是"an ultra-lightweight, high-performance, extensible global search and productivity launcher for Windows, built on **.NET 10 (WPF)**"，并自称"a modern, open-source alternative to **Listary** and **Everything**"。仓库描述同样写"local file search and productivity tool for Windows… alternative to Everything and Listary… featuring instant NTFS MFT parsing, real-time USN monitoring, and plugin support"。

**它不是个人主页仓库**：仓库与账号同名（`Lertaro/Lertaro`）容易被误判为 profile 仓库，但实测 `main` 分支有 **2,950 个文件**，含 `App/`、`Core/`、`Service/`、`Plugins/`、`PluginSdk/`、`Cli/`、`Installer/`、`Site/`、`Tests/`、`dev-docs/` 等目录与两个解决方案文件（`Lertaro.slnx`、`Lertaro.Plugins.slnx`）——这是一个完整的 Windows 桌面软件工程。

**三个最值得记的点：**

1. **索引走 Windows 底层而非目录遍历**：直接读 NTFS/ReFS 的 **USN Change Journal 与 $MFT**，由轻量后台服务保持索引实时同步，另含 FAT32/exFAT 监控与网络共享缓存（README 自述）。这是它自称"低资源、近即时"的技术根据。
2. **三进程隔离 + 100% 本地**：SYSTEM 后台服务 `Lertaro.Service`、用户态 WPF 界面 `Lertaro.App`、以及一个**绕过 UIPI** 的 hook 辅助进程 `Lertaro.Service --hook` 三者严格隔离；README 称"100% local with zero telemetry"。
3. **开放插件 SDK + 桥接社区生态**：提供 .NET 10 C# SDK 契约（自定义搜索提供者 / 别名 / 操作 / 列 / 预览），并**兼容 Flow Launcher 社区插件**；仓库 `Plugins/` 下已内置约 **31 个**插件目录。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Lertaro/Lertaro](https://github.com/Lertaro/Lertaro) |
| 描述 | A modern, high-performance local file search and productivity tool for Windows. A sleek, customizable alternative to Everything and Listary built with C# WPF and NT Services, featuring instant NTFS MFT parsing, real-time USN monitoring, and plugin support.（仓库自述，原文） |
| 归属 | 账号 `Lertaro`；页面数据 `isOrg: true`（组织账号）、`isFork: false`、`isArchived: false`、Public。**注：同页举报链接标注为 `(user)`，账号性质口径在页面上并不完全一致，我未进一步核实** |
| 许可证 | **MIT**（`LICENSE` 1,056 字节，标准 MIT 文本；**版权行为 `Copyright (c) 2026`，未写版权人姓名**） |
| 主语言 | **C# 98.7%**（shields.io `languages/top`，2026-11-12） |
| 关注度 | **1,442 Star / 72 Fork / 4 关注**（GitHub 页面内嵌数据，2026-11-12）。**镜像 ungh.cc 同日报 1,378 Star / 64 Fork / 4 关注**——两值不一致，此处记录页面值；Fork 与 Star 两处均不同、watchers 一致 |
| 维护状态 | **2026-08-06 创建**；页面 `updatedAt` **2026-11-05**、ungh 报 `pushedAt` **2026-11-04**（距核验日约 1 周）→ **非常活跃** |
| 版本发布 | 页面侧栏报 **172 Release / 204 tags**（2026-11-12）——**3 个月内发布密度极高** |
| 默认分支 | `main` |
| 官网 | https://lertaro.github.io （VitePress 站点，源码在仓库 `Site/`） |
| 话题标签 | `application-launcher` · `desktop` · `efficiency` · **`everyting`（原文即此拼写）** · `keyboard-launcher` · `launcher` · `listary` · `plugins` · `productivity` · `productivity-tools` · `search` · `windows` · `windows-search` |
| 仓库体积 | ungh 镜像未返回 `size` 字段，GitHub API 已限流未取，**未获得仓库体积数字**；仅知 `main` 文件树约 **2,950 个文件**（未计历史提交与大二进制） |
| 仓库结构 | `Tests` 854 · `Plugins` 663 · `App` 601 · `Core` 383 · `Site` 279 · `PluginSdk` 100 · `dev-docs` 14 · `Cli` 14 · `Installer` 10 · `Service` 6 · `Readmes` 6（+ `.github` 2）。根目录含 `Lertaro.slnx`、`Lertaro.Plugins.slnx`、`make.bat`、`build_and_run.bat`、`DEVELOPMENT_GUIDE.md`、`AGENTS.md`、`logo.svg`、`.gitlab-ci.yml`、`bump_lertaro_version.exe` 等 |

## 它能做什么（均据 README 自述）

- **索引**：读取 NTFS/ReFS 的 **USN Change Journal 与 $MFT**，不再逐目录遍历；后台服务实时同步索引；支持 FAT32/exFAT 监控与**网络共享缓存**。
- **搜索语法**：fzf 风格模糊匹配、多关键词跳跃匹配、目录路径 token，支持**前缀 / 后缀 / 精确 / 排除**操作符，并支持**非 ASCII 拼音别名转写**（拼音相关细节仓库另有 `dev-docs/pinyin-matching.md`）。
- **三种窗口模式 + 深度停靠**：快速弹窗、完整主窗口，以及一条会**自动停靠进原生"打开/保存"对话框与主流文件管理器**（File Explorer、Total Commander、Directory Opus、OneCommander）的内联栏。
- **操作菜单与预览**：`Ctrl+O` 打开操作菜单（接入**原生 Shell 右键**能力），`Alt+P` 调起 QuickLook 文件预览。
- **磁盘空间 Treemap**：无需重扫即可在实时 Treemap 中查看已索引磁盘占用、下钻大文件夹并执行清理操作。
- **插件**：开放 .NET 10 C# 插件 SDK（自定义搜索提供者 / 别名 / 操作 / 列 / 预览），并**兼容 Flow Launcher 社区插件**；`Plugins/` 目录已内置一批（详见下节）。
- **架构与隐私**：三进程隔离——SYSTEM 后台服务 `Lertaro.Service`、用户态 `Lertaro.App`、绕过 UIPI 的 `Lertaro.Service --hook`；README 称"100% local with zero telemetry"。

## 内置插件（据 `Plugins/` 目录，部分）

`Plugins/` 下约 31 个插件目录，覆盖：**文件管理器/压缩/办公集成**——`DirectoryOpus`、`OneCommander`、`TotalCommander`、`Xyplorer`、`Xplorer2`、`WinRAR`、`Bandizip`、`WPS`、`AutoCAD`；**搜索与筛选**——`ContentSearch`（44 文件）、`FileFilters`、`Files`、`FolderCascader`、`PinyinAlias`、`SpanishAlias`；**效率与系统**——`Calendar`、`ProcessManager`、`WindowSwitcher`、`SystemSettings`、`FileUnlocker`、`CustomActions`、`CustomCommands`、`AudioDeviceSelector`、`Translator`、`WebSearch`；**外观/桥接**——`CuratedThemes`、`AnimeThemes`、`CoreExtensions`（99 文件）、`FlowLauncherBridge`（67 文件）、`QuickLookBridge`、`BrowserData`。
（以上为目录清单归纳；各插件实际能力与可用性**我未逐一核验**。）

## 下载、构建与文档

- **下载**（README 给直链）：x64 有 Installer（`Lertaro-Setup.exe`，推荐、支持后台服务）与 Portable（`Lertaro-Portable.zip`）；ARM64（Snapdragon / Windows on ARM）另有对应 Installer 与 Portable。发布走 [GitHub Releases](https://github.com/Lertaro/Lertaro/releases)。
- **从源码构建**：要求 Windows 10/11、**.NET 10 SDK**、Visual Studio 2022 或 Rider，打安装包需 **Inno Setup 7（64 位）**；`build_and_run.bat` 本地重建 App/Core/Service/plugins 并重启，`make.bat` 产出 x64 与 ARM64 的 Release 到 `dist/`。
- **文档**：主 README 有 7 种语言（English / 简体中文 / 繁體中文(香港/台灣) / 日本語 / 한국어 / Español，译文在 `Readmes/`）；线上文档分 [User Manual](https://lertaro.github.io/user-guide/) 与 [Developer Manual](https://lertaro.github.io/dev-guide/)，`Site/` 下有 **8 种语言**的 `user-guide` 与 `dev-guide`；仓库内另有 `DEVELOPMENT_GUIDE.md` 与 `dev-docs/`（含 `search-grammar.md`、`search-ranking.md`、`index-rules.md`、`pinyin-matching.md`、`pitfalls.md`、`windows-integration.md`、`settings-permissions.md`、`window-rendering.md` 等）。
- **CI/发布**：仓库有 `.github/workflows/release.yml`、`deploy-docs.yml`，另有 `.gitlab-ci.yml`（约 11.8 KB）与 `dev-docs/gitlab-self-hosted-runner.md`，表明同时使用 **自托管 GitLab CI runner**（推断，未逐行核实）。
- **赞助**：README 提供 **USDT（TRC20）** 捐赠地址。

## 使用前需要注意

- **⚠️ 项目极新，关注度≠成熟度（事实 + 判断）**：**2026-08-06 创建**，核验时约 3 个月，却已有 **1,442 Star / 72 Fork**、**172 Release / 204 tags**。**事实**是它活跃、曝光高；**判断**是历史短、缺少长期第三方验证，"上千 Star"不能当作"久经检验"。
- **⚠️ 发布节奏极快，需接受版本变动**：3 个月 172 个 Release（可能含自动/夜间发布，未核实）意味着迭代频繁，行为与配置可能快速变化；把重要配置留档、留意更新说明更稳妥。
- **⚠️ README 顶部带明确 CAUTION**：作者提示"只从官方仓库、官网与 GitHub Releases 下载，不要运行来源不明的文件"——**这通常意味着已存在仿冒分发**，下载务必核对来源（供应链风险）。
- **⚠️ 仓库根目录直接提交了一个编译产物 `bump_lertaro_version.exe`（约 290 KB）**：运行它等于信任一个无法从源码审计的二进制；即便项目整体开源，这个文件本身**来源与内容我未核验**。
- **⚠️ 涉及较敏感的权限模型**：后台服务以 **SYSTEM** 运行，另有一个**绕过 UIPI** 的 hook 辅助进程用于在受限窗口（如部分文件对话框）中响应热键。这是它"深度停靠"能力的技术前提，但也意味着**安装后拥有较强的系统级能力**；README 未逐条解释权限边界，**我未审计源码**，装前建议先读 `dev-docs/settings-permissions.md`、`windows-integration.md`。
- **"零遥测 / 100% 本地"是作者自述，我未独立验证**：没有独立抓包或审计确认。
- **许可证形式瑕疵（非实质问题）**：`LICENSE` 为**标准 MIT**，但版权行为 `Copyright (c) 2026`，**未写版权人姓名**。这**不影响 MIT 授权条款**，但如果要按 MIT 要求"保留版权声明"，缺少明确署名主体。
- **对标的 Everything / Listary 都不要搞混**：Lertaro 自称其替代品，但**它不是 Everything（voidtools，闭源）或 Listary（闭源）的分支**；本仓库里也**没有**专门记录这两个项目的笔记。此外合集里已有一篇**同名撞车**的 [everything](everything-重建全世界软件的开源单仓.md)，那是完全不同的项目（见"相关笔记"）。
- **我未做任何验证**：**未 clone、未编译、未安装、未运行、未装插件、未运行 `bump_lertaro_version.exe`、未审计源码**；上述功能、索引机制、三进程隔离、内置插件清单、构建与下载方式均来自 **README、LICENSE、文件树与元数据**。Star/Fork 页面值与镜像值不一致、账号 `isOrg` 与举报链接口径不一致，均已如实标注来源。

## 什么时候会再看

- 想在 **Windows 上换掉 Everything / Listary**，要求开源、可深度定制、插件可扩展，且希望搜索栏能**内联停靠进原生"打开/保存"对话框**与常见文件管理器。
- 想参考"**用 USN Journal / $MFT 做实时索引**"的 Windows 原生实现（对照目录遍历式索引的性能取舍）。
- 想参考"**.NET/WPF 三进程隔离**"的落地写法：SYSTEM 后台服务 + 用户态 UI + 绕过 UIPI 的 hook 辅助进程，各管一段。
- 想参考"**开放插件 SDK + 桥接既有社区插件生态（Flow Launcher）**"的组织方式。
- 想持续观察它的发行节奏与稳定性，判断是否适合作为长期日常工具。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同名撞车、务必区分**：[everything：把"全世界的软件"重写一遍的开源单仓](everything-重建全世界软件的开源单仓.md)——那篇的 "Everything" 是 **Puter 团队用 Web 重写各类软件（Office/Slack/Photoshop…）的 monorepo**，与本篇对标的**本地文件搜索工具 Everything（voidtools，闭源）完全是两回事**；两篇名字都带 "Everything" 相关字眼，最容易混，故互链消歧。**注意：仓库里目前没有专门记录 voidtools Everything 或 Listary 的笔记**。
- **桌面应用 + 开放插件生态的对照**：[NonsPlayer：多音源跨平台音乐播放器与插件系统](NonsPlayer-多音源跨平台音乐播放器与插件系统.md)——两者都是**桌面应用 + 开放插件 SDK**，且都**桥接/兼容既有社区插件生态**（Lertaro 兼容 Flow Launcher 插件）；对照"插件契约怎么定、第三方插件如何桥接"这一层。
- **同属 Windows 生态、且 Lertaro 主动适配 WSL**：[WSL：Windows 上的 Linux 子系统](WSL-Windows上的Linux子系统.md)——Lertaro 文件树里有 `Core/WslPath.cs`、`App/.../WslSettingsItem.cs`，说明它把 WSL 路径纳入了索引/跳转范围；想了解"Windows 本地工具怎么处理 WSL 路径"时可对照。

## 参考来源

- 项目仓库与主 README：https://github.com/Lertaro/Lertaro
- 简体中文 README：https://github.com/Lertaro/Lertaro/blob/main/Readmes/zh-CN.md
- 许可（MIT）：https://github.com/Lertaro/Lertaro/blob/main/LICENSE
- 开发者指南（仓库内）：https://github.com/Lertaro/Lertaro/blob/main/DEVELOPMENT_GUIDE.md
- 在线文档（用户手册 / 开发者手册）：https://lertaro.github.io/ ・ https://lertaro.github.io/user-guide/ ・ https://lertaro.github.io/dev-guide/
- 语言占比（C# 98.7%）：https://img.shields.io/github/languages/top/Lertaro/Lertaro.json
- 元数据（Star / Fork / 时间 / 默认分支 / Release 数）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**未 clone、未编译、未安装、未运行、未装插件、未审计源码**；功能、索引机制、三进程隔离、插件清单与构建方式均来自 README、LICENSE、文件树与元数据自述。**关注度页面值（1,442 Star / 72 Fork）与镜像值（1,378 Star / 64 Fork）不一致，已按页面值记录并注明。**
