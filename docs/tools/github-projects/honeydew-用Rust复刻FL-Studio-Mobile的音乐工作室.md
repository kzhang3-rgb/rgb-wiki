# honeydew：用纯 Rust 从零复刻 FL Studio Mobile 的免费音乐工作室

> 它想回答的问题是"能不能用纯 Rust 重写一个像 FL Studio Mobile 的手机音乐工作站"：底层音频引擎、界面、跨平台外壳全部自研，跑在 Windows / Linux / macOS / Android 和浏览器（WASM）上。**它最值得记的不是功能清单，而是"clean-room 复刻 + GPL 借用插件宿主"这套拼法**——怎么在不碰原版代码的前提下做到能开 `.flm` 甚至读写桌面版 `.flp`。适合关心 Rust 跨端音频、音乐软件架构、以及"复刻某商业软件该怎样划版权边界"的人。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`LICENSE`、仓库文件树与 GitHub 页面内嵌元数据；**未 clone、未 `cargo build`、未运行应用、未试听音色、未验证 .flm/.flp 兼容性**）
- 适用范围：桌面端发行 **Windows / Linux / macOS / Android / Web**（iOS 为实验性）。**Linux 需 glibc 2.35+**（Ubuntu 22.04、Debian 12 起），Alpine 需另装 `libstdc++`、`alsa-lib`；从源码构建需 **Rust + `cargo`**，Web 目标需 `wasm32-unknown-unknown` 与 `trunk`。仓库语言为 **Rust**。

## 核心结论

**一句话定位（README 自述）**："A free, open-source music studio modelled on FL Studio Mobile, written in Rust."——免费开源、仿 FL Studio Mobile、纯 Rust，运行于 Windows / Linux / macOS / Android 与浏览器。

**三个最值得记的点：**

1. **"clean-room 复刻"是它的核心方法（README 自述事实）**：把 FL Studio Mobile 的**行为、文件格式、外观**先写成一份规格，代码只依据规格编写，**仓库不含原版任何代码、采样、预设或美术**（连图标都是代码里从零画的）。它明确声明与 Image-Line 无隶属、未获背书。**这是"想复刻但要躲开版权"的标准做法**，值得参照。
2. **它靠"借用 GPL 代码"补齐了手机版没有的桌面能力（README 自述事实）**：通过引入 [Lemon Studio](https://github.com/ItsAlphaNeon/Lemon-Studio)（作者 ItsAlphaNeon，GPL-3.0-or-later）的代码，Honeydew 获得了 **VST3 / VST2 / CLAP 插件宿主**、**FL Studio 桌面工程 `.flp` 的读写**，以及一批内置合成器/效果器（Lemon Reverb、Lemon Limiter、Lemon Trio、Lemon Sampler、Citrus、Lemon Drum Sampler、Lemon Slicer、Tesla 等）。**这让它从"手机版复刻"越界到"能碰桌面工程"**——代价是整体被 GPL 传染（见"使用前需要注意"）。
3. **跨端策略是"一套 Rust 内核 + 多外壳"**：桌面/移动端为原生构建，**Web 版编译为 WASM 静态站点**（Trunk 构建，产物预 gzip、单文件不超过 25 MiB，可托管在任意静态空间）。音色库独立成版本、单独发布（`sounds-<version>`），**仓库里按 90 MB 分片存放以绕开 GitHub 单文件限制**——这决定了它 clone 下来体积不小。

**个人判断（未验证）**：它的功能面（多轨、钢琴卷帘、鼓机、混音器、自动化、和弦编辑、音频编辑、插件宿主、FLP 导入导出）对着 FL Studio Mobile 用户是熟悉的路子；**但它是单人项目、非常新（2026-10-14 创建，核验日约 1 个月、已 36 个 release）**，"复刻得像不像、插件/FLP 兼容到什么程度"这类问题**我无法在不运行的情况下判断**，需自行实测。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [daijro/honeydew](https://github.com/daijro/honeydew) |
| 描述 | "An open-source, clean-room reimplementation of FL Studio Mobile, rebuilt in pure Rust." |
| 归属 | 账号 `daijro`（**个人**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false` |
| 许可证 | **GPL-3.0**（页面 `spdxId: GPL-3.0`，名为 "GNU General Public License v3.0"；**直读 `LICENSE` 文件**为 GPLv3 全文，35,149 字节）。**注意：README 的许可段写的是 `GPL-3.0-or-later`（或更高版本），与页面显示的 `GPL-3.0` 略有口径差异** |
| 主语言 | **Rust 99.2%**（shields.io，2026-11-13） |
| 技术栈 | 纯 Rust 应用 + `cargo` 工作区（`crates/`、`src/`）；Web 用 WASM（Trunk）；Android 有 Gradle 外壳与 Java 桥接，iOS 有 Xcode 脚本；**`vendor/lemon-studio/` 为借用的 Lemon Studio 插件宿主与效果器代码**；`content/recipes/` 为大量音色配方（TOML） |
| 关注度 | **132 Star / 28 Fork / 3 Watcher**（**页面值**，2026-11-13）；开放 Issue **32**（shields.io，2026-11-13）。**页面值与 ungh.cc 镜像一致（镜像同为 132 / 28 / 3）** |
| 维护状态 | **2026-10-14 创建**；页面 `updatedAt` **2026-11-12**、ungh 报 `pushedAt` **2026-11-10**（距核验日约 1–3 天，**活跃**）；已累计 **36 Release / 36 tags**（页面值） |
| 默认分支 | `main` |
| 版本 | **36 Release / 36 tags**；页面 `packages: true`（发布产物），`usedBy: false` |
| 话题标签 | 无（页面 `topics` 为空） |
| 体积 | `main` 文件树约 **1,427 个条目**（ungh.cc files API）；顶层以 `vendor`（568）、`src`（436）、`content`（147）、`crates`（114）、`assets`（45）、`tools`（43）、`tests`（26）、`sounds`（18）、`android`（12）为主 |
| 官网 | <https://playground.daijro.dev/honeydew>（在线试玩 Web 版） |

## 能做什么（README 自述）

- **多平台**：Windows（含 ARM）、macOS（Apple Silicon）、Linux（`x86_64` / `aarch64`，含 musl 版）、FreeBSD、Android（64/32 位 ARM，Android 8+）、iOS（实验性、未签名）、Web（静态站点）。下载后解压运行 `honeydew-native`；AppImage 为单文件。
- **工程格式**：可开可存 **FL Studio Mobile 工程 `.flm` 与 `.zip` 工程包**、**MIDI**、**FL Studio 桌面工程 `.flp`**；音频（WAV/FLAC/OGG/MP3）可导出不可导入。开 `/存 `.flp` 时缺失的插件与采样会用内置音色替代，并列出被替换项；存 FLP 还能选"包含渲染音频"以便在 FL Studio 里一致回放。
- **界面与功能**：播放列表、钢琴卷帘、鼓机编辑器、混音器、效果器机架、乐器选择、歌曲菜单、自动化编辑器、和弦编辑器、音频编辑器（README 的截图逐项对应）。
- **插件（桌面端）**：可加载 **VST3 / VST2 / CLAP** 乐器与效果器，插件设置随工程保存、控制项可自动化；Windows 插件在 Linux 上可通过 **yabridge / vstbridge + Wine** 桥接。
- **多核处理**：Linux/macOS/Windows 桌面端可开关"多核处理"，把独立通道机架分到不同 CPU 核心；**默认关闭**，短片段/单核/无独立通道时自动回退顺序处理，**不会跨核拆分单个插件实例**。
- **本地音乐助手控制（实验性）**：桌面端可选开启一个本地控制桥，用 **MCP 助手**编辑当前打开的歌曲（复用已配置插件、或新建内置合成器/鼓轨并加音符）。

## 使用自己的 FL Studio Mobile 音色（README 自述）

Honeydew **不包含任何 FL Studio Mobile 内容**。若你拥有该 App，可**从你自己的副本**导入其音色、预设与示例歌曲：把 App 的 `.apk` / `.obb` 放进 "Original Content" 文件夹（各平台路径见 README），再在设置里点 Import；或用命令行 `cargo run --release --bin honeydew-import -- path/to/your.apk`。README 明确提醒：**这些内容仍归你的 FL Studio Mobile 许可约束，不要分享**。

## 使用前需要注意

- **⚠️ 许可证是 GPL-3.0（copyleft），且 README 写"or-later"（事实）**：页面显示 `GPL-3.0`，README 许可段写 `GPL-3.0-or-later`；代码版权归 "The Honeydew contributors"。**GPL 意味着二次分发/衍生受传染性约束**，若你要把它并入闭源产品需先处理合规。**本仓库还含来自 Lemon Studio（本就 GPL-3.0-or-later）的代码**，各文件保留原始版权与许可头。
- **⚠️ "不含原版内容"是它的核心声明，但需要你自证合规（事实 + 提示）**：README 声称 clean-room、不含 FLM 代码/采样/预设/美术；**这是作者声明，我无法验证**。它同时说明用户导入的自有 FLM 内容**仍受原许可约束、不得再发布**。
- **⚠️ 仓库含大体积二进制 / 构建产物（按文件树判断）**：`sounds/` 下为**按 90 MB 分片的音色库 zip**（18 个条目），`android/gradle/wrapper/gradle-wrapper.jar` 为二进制，`assets/fonts/` 有 TTF 字体。**clone 后体积不小**；音色库另有独立发布（`sounds-<version>`，基础 APK / `-no-sounds` 构建约 **740 MB** 需一次性下载）。
- **⚠️ 平台与硬件前提**：Linux 需 **glibc 2.35+**；Alpine 需补 `libstdc++` / `alsa-lib`；Android 上**卸载 App 会删除 Honeydew 文件夹及其中的歌曲**；iOS 版为**未签名 IPA**，需自签工具安装。
- **⚠️ 联网与更新（README 自述）**：桌面端会检查更新（默认最多每 6 小时一次，可关）；基础 APK 与 `-no-sounds` 构建需联网下载音色库。**未提及需要特殊系统权限**。
- **⚠️ 它很新、迭代很快（事实）**：**2026-10-14 创建**，核验日已 36 Release、`pushedAt` 约 1–3 天内；**单人项目**，README 与实现可能快速变动。
- **⚠️ README 自述与仓库实际**：README 描述的目录（`src/`、`vendor/lemon-studio/`、`content/recipes/`、`crates/`、`tests/`、`tools/`、`android/`、`sounds/` 等）**在文件树中均存在**，**未发现明显不符**（仅按文件树判断）。
- **我未做任何运行验证**：**未 clone、未构建、未运行、未试听、未验证兼容性**；上述能力、格式支持、许可与平台约束**均来自 README、`LICENSE` 与仓库文件自述**。

## 什么时候会再看

- 想参考**"用纯 Rust 写一个跨端（含 WASM）音乐工作站"**的工程组织（`cargo` 工作区 + `crates/` + Web 分发）。
- 想研究**"clean-room 复刻一个商业 App"该怎样划规格与版权边界**（规格先行、仓库不含原版素材、用户自备内容的免责写法）。
- 想抄**"自研应用 + 借用 GPL 插件宿主"**来获得 VST3/VST2/CLAP 与桌面工程（`.flp`）兼容的做法与代价。
- 想搭一套**"音色库独立版本、单独发布、分片存放"**的素材分发机制（GitHub 单文件限制的绕法）。
- 需要一个**开源、能跑在浏览器里的 FL Studio Mobile 风格**工作站做对照研究。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"重写/复刻一个既有商业软件"**：[ConcordRTS：用 C++20 重写红警2引擎](ConcordRTS-用C++20重写红警2引擎.md)——一个用现代 C++ 重写游戏引擎、一个用纯 Rust 复刻音频工作站，可对照看**"用新语言重做老作品"**在架构与版权上的两种处理。
- **同样做 clean-room 复刻，但改写的是"谁来读原件"**：[craft-anything：看清任意软件怎么工作，再让 AI 重建一个自己的版本](craft-anything-看清软件如何工作再用AI重建自己的版本.md)——本篇是人先把行为 / 格式 / 外观写成规格再照规格写代码；那篇把复刻过程交给本机已登录的编码 Agent，并给出 **Clean room / Deep** 两级"能看多深"的开关，可对照"读原件读到哪一层"的两种划法。
- **同属"带插件系统的音乐/音频应用"**：[NonsPlayer：多音源跨平台音乐播放器与插件系统](NonsPlayer-多音源跨平台音乐播放器与插件系统.md)——一个偏**音乐播放与插件扩展**、一个偏**音乐制作与插件宿主（VST3/CLAP）**，关注点不同，但"应用如何做插件化"可互相参照。
- **同属"桌面端音频应用，且引擎可插拔"**：[VoiceStudio：全本地运行的 AI 语音工作室（AGPL 的 ElevenLabs 替代）](VoiceStudio-全本地运行的AI语音工作室与ElevenLabs替代.md)——那篇是**语音生成与处理**（多引擎可插拔，Electron + Python，AGPL-3.0）；本篇是**音乐制作**（插件宿主 VST3 / VST2 / CLAP，纯 Rust，GPL-3.0）。两者可对照"**多引擎 / 插件化管理**"与**许可选择**（GPL vs AGPL）上的取舍。

## 参考来源

- 项目仓库与 README：https://github.com/daijro/honeydew
- 在线试玩（Web 版）：https://playground.daijro.dev/honeydew
- 许可（GPL-3.0）：https://github.com/daijro/honeydew/blob/main/LICENSE
- 第三方声明：https://github.com/daijro/honeydew/blob/main/THIRD_PARTY_NOTICES.md
- 借用的 Lemon Studio：https://github.com/ItsAlphaNeon/Lemon-Studio
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 与 Tag 数 / 许可证）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未构建、未运行、未试听、未验证兼容性**；功能、格式支持、许可与平台约束均来自 README、`LICENSE` 与仓库文件自述。
