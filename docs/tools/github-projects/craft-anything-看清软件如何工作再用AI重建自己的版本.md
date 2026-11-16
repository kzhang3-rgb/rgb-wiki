# Craft Anything：看清任意软件怎么工作，再让 AI 重建一个自己的版本

> 一句话值得记住的取舍：它把"**研究一个软件**"与"**照着它写一个新版本**"串成一条流水线——先用自己的工具（自带的解码器、反汇编器、反编译器，Deep 模式下还会调用 Ghidra）把目标网站 / 桌面程序 / 手机应用 / 游戏 / 固件**看清**，再让你本机已登录的编码 Agent（首选 Claude Code）在**你指定的技术栈**里重建，然后**逐项与原件对比**并反复收敛；默认的 **Clean room 模式干脆不读目标内部**（不读代码、不读文件、不读页面源码），只按"访客能看到的东西"重建。想研究"逆向理解 + AI 重建"这条链路怎么落地、或想知道它怎么给"看得见 / 看不见"划线时值得看；但它是 **early alpha**，且 **Clean room 是规则而不是操作系统沙箱**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`LICENSE-MIT` / `LICENSE-APACHE`、`docs/licences.md`、仓库根目录文件清单与元数据；**未 clone、未构建、未安装、未运行任何一次 rebuild**）
- 适用范围：想借用 AI 编码 Agent 复刻一个界面 / 网站 / 小游戏 / 工具的场景；构建需要 Rust **1.97 或更新**，以及**本机已登录的编码 Agent**（见下文）

## 核心结论

**它是一条"四步流水线"，而不是一个单点工具：**

| 步骤 | 做什么 |
| --- | --- |
| **Study（研究）** | 用自带工具检视原件：各屏宽下的页面、代码、文件、运行方式（Clean room 下只做"可见部分"） |
| **Build（构建）** | 编码 Agent 按你要求点名（或默认）的语言 / 框架 / 引擎写出一个新项目 |
| **Check（比对）** | 把新构建与原件逐项比较，**列出还不一致的地方**（网站按三档屏宽比对，游戏跑它自己的自检，应用跑起来看） |
| **Ready（可用）** | 打开 / 试玩 / 预览结果，或继续提要求让它改 |

**引擎本身是"纯 Rust 的逆向工程引擎"，含 320 个工具**（其中 300 个在引擎里，其余在 AI 与 setup 层），覆盖原生程序、.NET、Java、Android、Apple、Electron / JavaScript、网站、固件、抓包、数据库与游戏文件；**自带解码器、反汇编器与反编译器，不依赖任何外部程序即可工作**。Deep 模式会用上全部工具，装了 Ghidra 时也会调用它。同一个引擎驱动桌面端（egui）、CLI 与 MCP server，**其中一部分还能以 WebAssembly 跑在浏览器里**。完整工具清单在 `docs/tools.md`。

**"Clean room / Deep"是这篇最该记清的一条设计：**

- **Clean room（默认）**：**不读原件内部**——不读代码、不读文件、不读页面源码。它只用"你能看到的东西"：你提供的图片、网站访客所见（截图、可见文本、量出来的布局 / 颜色 / 字体 / 间距、展开过的菜单），以及（在你允许时）应用窗口的截图。**凡是会"看进去"的工具都会被拒绝，并在拒绝时告诉你怎么切到 Deep 模式。**
- **Deep**：所有工具都能用，供你私人使用。程序代码与文件交给自带引擎读取（装了 Ghidra 就用 Ghidra），网站会读它的 JavaScript 并观察其起始页，构建方也能用上这些。

README 把边界写得很清楚：**Clean room 是"Craft Anything 自己提供 / 拒绝什么"的规则，不是操作系统沙箱**——它不隔离你的电脑。你运行的编码 Agent 依然以你的身份行事，能读 / 写 / 运行什么取决于它自己的权限。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [storytold/craft-anything](https://github.com/storytold/craft-anything) |
| 描述 | Craft Anything: understand how any software works, and craft your own. |
| 官网 / 应用页 | https://getartcraft.com/ · https://getartcraft.com/apps/craft-anything |
| 归属 | 组织 `storytold`（页面 `isOrg: true`；署名 "ArtCraft Team and the Craft Anything contributors"） |
| 许可证 | **MIT OR Apache-2.0 双许可**（`LICENSE-MIT` 1,106 字符 + `LICENSE-APACHE` 10,174 字符，任选其一）；**品牌资产另有条款**，见下节 |
| 主语言 | Rust 98.9%（shields.io；README 徽章自称 "100% Rust"） |
| 关注度 | **30,012 Star / 6,111 Fork / 134 关注**（页面值，2026-11-13；ungh.cc 镜像为 29,109 / 5,921 / 131，**镜像偏低，不一致**） |
| 开放 Issue | 188（shields.io，2026-11-13） |
| 维护状态 | **2026-10-31 创建**（很新）；ungh 报 pushed **2026-11-12**；页面 `updatedAt` 同日 |
| 默认分支 | `main` |
| 版本 | **7 Release / 7 Tag**，最新 **v0.6.1（2026-11-12）**，最早 v0.1.0（2026-10-31）——**约 12 天发了 7 个版本**，节奏极快 |
| 话题标签 | 空 |
| 仓库规模 | 文件树 **1,062 个文件**（含 vendored 的 `vendor/wgpu-hal/`） |
| 关联套件 | 同组织下的 ArtCraft "Craft App Suite"（PhotoCraft / VectorCraft / FilmCraft / WordCraft / PdfCraft / FigCraft 等十余个应用），README 表格逐项列出 |

## 名字与品牌：不在开源许可里

README 末尾单独声明：**ArtCraft 的名称、字标与 logo（放在 `docs/brand/`）是 ArtCraft Team 的商标，不在本项目许可范围内**；只能在**不修改**的前提下、且仅作为本仓库与 Craft Anything 的一部分使用（另见 `docs/brand/LICENSE-brand.txt`）。**分支与修改版必须移除这些品牌资产。**

对应地，GitHub 页面 license 字段只显示 **`Apache-2.0`**（双许可的两支只显示了一支）——**这里必须读 `LICENSE-MIT` / `LICENSE-APACHE` 才能看清是 `MIT OR Apache-2.0`**，别把页面标签当成完整口径。

## 依赖策略：只允许宽松许可

`docs/licences.md` 把它的依赖红线写得很具体，值得单独记（**这是一份可以直接抄的"许可白名单"范式**）：

- **链接或随附的代码必须是宽松许可**：MIT、Apache-2.0（含 WITH LLVM-exception）、BSD-2/3-Clause、ISC、Zlib、0BSD、Unicode-3.0、BSL-1.0、CDLA-Permissive-2.0、OFL-1.1，字体可用 Ubuntu Font Licence。
- **MPL-2.0 只按"名字"允许**（文件级 copyleft，只能逐 crate 允许；目前只有 `option-ext`，且未改动）。
- **绝不链接或捆绑 copyleft 代码**（GPL、LGPL、AGPL、SSPL、EUPL、CC-BY-SA 等）。
- **"只运行、不链接"的外部程序可以用任意许可**：把 Ghidra、rizin、tshark、Frida、JADX、Apktool、rtl_433、adb、浏览器、Agent CLI 等作为**独立进程**启动；由使用者自行安装，或按需下载上游发行版，**从不把它们的代码拷进自己的仓库**。
- **避开有专利负担的编解码器**：不加 H.264/AVC、HEVC/H.265、AAC 编码器，优先 AV1 / VP9 / Opus / FLAC / PNG / WebP，解码这些格式时用操作系统自带的编解码器。
- 已标注的"已知坑"：`dssim`（图像相似度）是 **AGPL-3.0**，不用；**Hunyuan3D 模型许可排除欧盟、英国与韩国**；**FLUX.1 [dev] 权重为非商用许可**。

检查靠 `cargo deny`（`cargo xtask licenses` / `cargo deny check`），新增许可或 crate 例外必须在 `deny.toml` 与这份文档里各写一行理由；发行版用 `cargo-about` 生成第三方许可文件。

## AI：用它必须自备一个"已登录的编码 Agent"

构建环节**需要一个装在你电脑上、且已登录的编码 Agent**：

- **Claude Code 是首选**：Craft Anything 运行**你自己的、未经修改的 `claude` 程序**，用你自己的登录态；**它看不到也不经手你的账号**，你的使用受你与 Anthropic 之间的协议约束（详见 `docs/claude-code.md`）。
- **Codex 适配器已存在，但尚未在真实安装上验证过。**
- **GitHub Copilot** 可经 ACP 用它已安装的 Copilot CLI（需另行 `copilot login`）。
- **Grok Build** 可经 ACP 用你已安装的 `grok`（`grok agent stdio`，需另行 `grok login`）。
- 其它支持 ACP 的 Agent（Gemini CLI、Qwen Code、opencode、goose、Hermes、Maki、Devin）可逐个后端切换；其中 **Maki 与 Devin 已实机跑过**（Devin 用后端存储的 key 鉴权，它自己的 CLI 登录在 ACP 下不算数）。
- **问答与分析**可用 Anthropic、OpenAI、OpenRouter、xAI（设置了 `XAI_API_KEY` 时）以及本地 OpenAI 兼容服务器（Settings → AI）。

## 两种用法与两种模式

- **Simple（默认）**：回答"你想做什么？"——选网站 / 应用 / 游戏，描述，按 Start。项目页展示四个步骤、Agent 当前在做什么（用白话）、提问与授权卡片、结果相似度，以及打开结果 / 所在文件夹的按钮；底部输入框提改动。"Just look inside" 只研究不构建（Deep 模式下）。
- **Advanced**：完整工作台——全部工具（Ctrl/Cmd+K）、函数列表、字符串、伪 C、反汇编、hex、调用图、evidence 台账、unknowns、jobs、日志与 Agent 控制台。
- 两种用法驱动**同一个引擎**；Clean room / Deep 可在标题栏或设置里切、用 `settings_set deep_mode true`、或单次 CLI 运行加 `--deep`（或 `--clean-room`）。

## 上手与项目结构

需要 Rust **1.97 或更新**（某个依赖要求），Debian / Ubuntu 上桌面端还需 `libxkbcommon-dev libwayland-dev libx11-dev libxrandr-dev libxi-dev libgl1-mesa-dev libgtk-3-dev`。

```sh
git clone https://github.com/storytold/craft-anything && cd craft-anything
cargo run --release -p craft-anything                # 桌面端
cargo install --path apps/craft-anything-cli         # CLI，命令名 craft-anything-cli
craft-anything-cli doctor                            # 检查安装、编码 Agent 与引擎
```

命令行重建（问题在终端里问）：

```sh
craft-anything-cli rebuild https://example.com "Recreate this website"
craft-anything-cli rebuild - "A snake game in Rust" --rounds 1
craft-anything-cli rebuild https://example.com "Make the header blue" --continue-in "<项目文件夹>"
craft-anything-cli rebuild - "Add a contact form" --project-folder "<你的文件夹>" --existing edit
```

新项目默认落在 `Documents/Craft Anything`，每个项目目录含：`craft.json`（请求、目标、状态与历史）、`capture/`（从目标记录到的东西）、`app/`（生成的项目）、`checks/`（每次与原始对比）、`investigation.cany/`（证据）、`inbox/`（待解问题）。

## 面向 Agent：每个工具都是 CLI 子命令 + MCP 工具

```sh
craft-anything-cli commands                                   # 按 toolset 列出全部工具
craft-anything-cli mcp                                        # 以 MCP 形式提供给任意 MCP 客户端
craft-anything-cli rebuild https://example.com "Recreate this website"
```

结果以**证据记录**返回，带置信度、覆盖率与局限；大结果先回摘要，再用 `core_evidence_browse` 分页。`craft-anything-cli setup` 可把它接到编码 Agent 应用上。**检视本身不需要 AI。**

## 发行版覆盖

- **Windows**：msi（x64 / arm64 / x86）+ portable zip，安装器与可执行文件**有代码签名**。
- **macOS**：universal dmg（**已签名并公证**，README 给了自检命令）；CLI 另有单独 zip（0.2.0 之后 CLI 已并入 app）。README 说明了浏览器下载 CLI 可能被 Apple 拦截、以及用 `curl` 下载规避的做法。
- **Linux**：AppImage（内置 CLI，`<AppImage> --cli <command>`）、Flatpak、deb、rpm、tar.gz，覆盖 x86_64 / aarch64，另有 riscv64 tar.gz。**Flatpak 是沙箱，无法启动浏览器，因而无法用它研究网站**（README 明确建议改用其它包）。
- **FreeBSD**：x86_64 tar.gz。
- **Web（WebAssembly）**：浏览器查看器，用来判断"某个文件是什么"，**文件不离开浏览器**，可托管在任意静态服务器上。

## 使用前需要注意

- **它是 early alpha，而且作者自己把"能用的"与"半成品"分得很清楚**：README 的 Status 明列——**已可用**：桌面端（Simple/Advanced）、CLI、MCP server；以 Claude Code 为构建方的网站 / 应用 / 游戏四步流程；网站三档屏宽检查；游戏重建（Unity / Unreal / Godot）；含 Ghidra 的 Deep 模式；生成图片与 3D 模型（**opt-in，需自带 fal.ai key**）；插件（MCP server、`wasm-plugins` 选项下的 WASM 组件）。**"部分就绪"**：**用 API key 与其它编码 Agent 构建（已写出来，但还没实机跑过）**、应用与游戏的行为检查、少数只对"替身"测试过的引擎工具。**下一步**是托管（managed）层。
- **Clean room 不是安全边界**：它只是引擎"提供什么 / 拒绝什么"的规则，**不隔离你的电脑**；你运行的编码 Agent 仍以你的身份行动，能做什么取决于它自己的权限；而且**不同 Agent 能被 Craft Anything 拒绝的自带工具并不一致**。
- **合规要求写进了 README**：只对**你拥有、或你有权检视**的软件使用；**它从不绕过 DRM、登录或反作弊**；重建只依据证据复原布局、样式与行为，并明确告诉 Agent **不要复制 logo、照片或文字**，除非你确认拥有或有授权。
- **安装依赖会执行代码**：构建方安装项目依赖时，命令在你电脑上运行，包在安装过程中也可能执行代码（npm 默认跑每个包的 install 脚本，Rust build script 与 Python 源码包会跑自己的 setup 代码）。Craft Anything 对**这个项目里没见过的命令类型**会先问（`npm`、`cargo`…）；**一旦对 `npm` 选"总是允许"，之后所有 npm 命令都不再询问**——只对你信任其依赖的项目这么做，或用 `npm install --ignore-scripts`。
- **双许可但品牌不在许可内**：代码可 `MIT OR Apache-2.0` 任选，但 `docs/brand/` 的 ArtCraft 名称 / 字标 / logo 是商标，**分支与修改版必须移除**。
- **仓库里有 vendored 代码**：文件树含 `vendor/wgpu-hal/`（带 `LICENSE.APACHE` 与 `LICENSE.MIT`），统计文件数（1,062）时别把它当自研代码。
- **发布节奏快也意味着口径会变**：12 天 7 个版本，README 与 `docs/` 的结构、支持矩阵、状态划分都可能随时改写；引用前建议按 README 里的 `docs/STATUS.md`、`docs/tool-status.md`、`docs/ROADMAP.md` 现查。
- **我只读了文档与元数据，没有装、没有跑**：四步流程的实际效果、Check 的判定质量、各引擎工具对真实目标的准确性、以及 Clean room "拒绝"是否会漏，**均未独立验证**。

## 什么时候会再看

- 想设计或实现**"逆向理解 + AI 重建 + 自动比对收敛"**这类流水线，需要一份把四步、证据记录、授权卡片与"部分就绪"声明都写清楚的结构参考时。
- 需要给一个工具补上**"Clean room 与 Deep 两级"**的可见性开关，或想知道怎么为"不读源码也能重建"划一条自洽的线时。
- 需要一份**可抄的第三方许可策略**（宽松白名单 + 只运行不链接 + 专利编解码器回避 + 已知坑清单）时：`docs/licences.md` 与 `deny.toml` 是少见的完整实例。
- 想参考**"320 个工具同时暴露为 CLI 子命令与 MCP 工具"**的工程做法，或它其余十余个 ArtCraft 桌面应用（同组织 `storytold` 下）的复用方式时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"用 AI 逆向 + 重做软件"，clean-room 边界正好相反**：[universal-modder：把改任何 PC 游戏做成一整套 Agent 技能](universal-modder-把改任何PC游戏做成一整套Agent技能.md)——本篇默认 **Clean room 不读目标内部**（只用可见信息），那篇**会读真实代码 / 资源**去改游戏。两篇对照着看，最能说明"**合规路线怎么选**"。
- **同为"AI 编码 Agent 当主力施工队"**：[dioramas：用 AI 生成素材做电影感 3D 网站](dioramas-用AI生成素材做电影感3D网站.md)——那篇是 AI **从 brief 搭新页面**（对着自己的引擎迭代），本篇是 AI **重建已有软件**（对着原件比对收敛）。可对照"**把 AI 当施工队**"的两类任务。
- **同属"重建既有软件"这条大方向，但路子不同**：[everything：重建全世界软件的开源单仓](everything-重建全世界软件的开源单仓.md)——那篇是用 Web 技术把一堆办公 / 创作软件各重写一个开源版本、铺开几十个应用；本篇是"**先逆向看清、再让 AI 重建**"的单条流水线。两篇可对照"广铺功能"与"逐个复刻"两种打法。
- **Deep 模式依赖的外部逆向引擎**：[ghidra：NSA 开源的软件逆向工程框架](ghidra-NSA开源的软件逆向工程框架.md)——本篇装了 Ghidra 时会调用它做 Deep 分析；那篇是引擎本身。另见把它接给 AI 的那条线：[ghidra-mcp](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)。
- **同为"用新语言 / 新栈重做一个既有软件"**：[honeydew：用 Rust 复刻 FL Studio Mobile 的音乐工作室](honeydew-用Rust复刻FL-Studio-Mobile的音乐工作室.md)——那篇是**人工规格先行**的 clean-room 复刻（纯 Rust，不含原版代码 / 采样 / 预设 / 美术）；本篇把"复刻"过程交给编码 Agent，且给出 Clean room / Deep 两级可见性。对照看"谁来读原件、读到哪一层"。
- **同类"重写既有商业软件"的另一个样本**：[ConcordRTS：用 C++20 重写红警 2 引擎](ConcordRTS-用C++20重写红警2引擎.md)——那是把一个具体游戏引擎逐步重写并接入 D3D11；两者都涉及"原件形态 → 新实现"的对应关系，但一个手写、一个 AI 生成。
- **同为"看清软件 → 用 AI 重建"这条链路，引擎来源不同**：[REA：用 Agent 把软件从行为逆向到原生二进制](rea-用Agent逆向软件行为与原生二进制.md)——那篇是**自带本地 MCP + CLI**，把深度原生分析**外挂**回 Hopper / Ghidra / IDA / Kuna；本篇把 Study/Build/Check/Ready 串成**自带动引擎的流水线**并划 Clean room 边界。可对照"**自带引擎**"与"**编排外部引擎**"两种取舍。

## 参考来源

- 仓库主页与 README：https://github.com/storytold/craft-anything （核验日期 2026-11-13）
- 许可：`LICENSE-MIT`（1,106 字符）、`LICENSE-APACHE`（10,174 字符）、`NOTICE`、`ATTRIBUTION.md`、`docs/brand/LICENSE-brand.txt`
- 依赖许可策略：`docs/licences.md`、`deny.toml`、`about.toml` / `about.hbs`（核验日期 2026-11-13）
- 工具与状态：`docs/tools.md`、`docs/tool-status.md`、`docs/STATUS.md`、`docs/ROADMAP.md`（据 README 指引，未逐一读完）
- 发行：Releases 页与 ungh.cc `releases`（最新 v0.6.1，2026-11-12）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 与 Tag 数 / 许可证字段 / 是否 fork / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；Star / Fork / Watcher 的镜像对照取自 ungh.cc（同日，数值偏低，已在正文注明）；Issue 数取自 shields.io（2026-11-13）

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未构建、未安装、未试跑任何一次 rebuild**；四步流程效果、Check 判定质量、工具准确性与 Clean room 边界均来自 README 与 `docs/` 自述。
