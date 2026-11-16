# REA：用 Agent 把软件从行为逆向到原生二进制

> 一句话定位（README 自述）：**"One MCP for reverse engineering across binaries, applications, and runtime behavior."**——把**二进制、应用与运行时行为的检查能力做成一个本地 MCP**，接给你的编码 Agent；典型用法是"**看到一个你喜欢的功能 → 让 Agent 查明它怎么实现（细到二进制级）→ 展示证据 → 为你的项目写一个版本**"。最值得记的三点：① 它是**面向 Agent 的"逆向工具总闸"**（MCP + CLI 同一套工作流），深度原生分析再借用 Hopper / Ghidra / IDA / Kuna，静态 JS 与 .NET 则**不需要任何原生引擎**；② 覆盖面极宽——README 列了**约四十类目标**（原生二进制、Go/Rust 二进制、IL2CPP、Godot PCK、Android APK、.NET、Electron、固件、抓包、SQLite……）；③ 关注度高得反常（页面约 45.6 万 Star），但它同时是**动商业逆向工具、需自行持有合法授权**的项目，README 专门写了"官方下载只有三处"的警告。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（492 行）、`LICENSE`（1,064 字符），并交叉核对已给定元数据；**未 clone、未安装 `rea-agents`、未运行 `setup` / 任何 CLI 或 MCP、未接任何 Agent、未验证任何 showcase，也未审计源码**）
- 适用范围：**Node.js 22.x（>=22.23.4）/ 24.x（>=24.22.1）/ 26.12.1+ 与 npm**；面向**任何支持本地 MCP 的 Agent**（setup 自带 Claude Code、Codex、Cursor、Gemini CLI、Grok Build 等）；深度原生分析需自备 **Hopper / Ghidra / IDA / Kuna**（静态 JS/.NET 不需要）；固件、抓包、pwntools 类路径多在 **Linux** 侧

## 核心结论

1. **事实（README 自述）：它是一个 MCP 服务器 + CLI，把"逆向检查"包装成 Agent 可调用的工具。** 工作流是——**Agent 通过 MCP 调用 REA → REA 检查目标、追踪相关代码 → 返回 findings 与其 evidence / limitations → Agent 据此追问、解释行为，或写下并测试一个实现**；CLI 命令走同一套工作流。
2. **事实：深原生分析要外挂引擎，静态 JS/.NET 不用。** README 的 FAQ 明写深度原生分析依赖 **Hopper、Ghidra、IDA 或 Kuna**（视目标与操作而定），**静态 JavaScript 与 .NET 检查无需原生分析引擎**；setup 经批准后可选装 Hopper，Ghidra / IDA / Kuna 用你自己已有的安装。
3. **事实：分析在本地跑，结论带证据与局限。** README 反复强调"Analysis runs locally, and results include the evidence and limitations behind each conclusion"；对"是否上传我的应用"的回答是"REA 在本地分析目标，你的 Agent 会收到工具结果，而它的模型供应商有自己的数据政策"。
4. **事实：覆盖面是它的差异化。** README 的表列了**约四十类目标**（见下表），从原生二进制、Go/Rust 二进制、GPU code objects、模型文件，到 IL2CPP、Godot PCK、Android APK/设备、.NET、Electron、固件、网络/数据包/信号抓取、SQLite、进程行为等；此外还能**把录制的 HAR 变成 RL 环境**。
5. **关注度高得异常。** 页面 **stargazerCount 456,153**、ungh 镜像 453,977；而 README 自己写的却是"**🎉 400,000 GitHub stars**"——**三个数字口径不一，引用时务必注明来源与日期**。

**个人判断（未验证）**：REA 的定位像"**AI 逆向的工具总线**"——它不自己实现所有分析（听 README 口径，是把 Hopper/Ghidra/IDA/JEB/Apktool/Binwalk 等一堆工具编排起来，外加大量内置的静态格式解析），而是**统一成 MCP 工具 + CLI + 证据三件套**。这种"总闸"式设计对 Agent 友好，但代价是**依赖面很宽**：装一个 REA 不等于装好了它调用的那些引擎，且**商业引擎的授权要你自己解决**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [morluto/rea](https://github.com/morluto/rea) |
| 描述（原文） | "Reverse engineer anything with agents, from app behavior down to native binaries." |
| 归属 | **个人账号 `morluto`**（非组织）；`isFork=false`、`isArchived=false` |
| 许可证 | **MIT**（`LICENSE` 1,064 字符，`Copyright (c) 2026 morluto`）；**第三方材料保留各自许可**，见 `THIRD_PARTY_NOTICES.md` |
| 主语言 | **未核实**（本次元数据未含主语言字段）；代码以 npm 包 `rea-agents` 分发并需 Node.js |
| 关注度 | **页面 456,153 Star / 1,727 Watcher / 112,007 Fork**；**ungh 镜像 453,977 / 1,713 / 111,565**（两者差异 <0.5%，属镜像延迟）。README 自述"400,000 stars" |
| 维护状态 | **2026-04-14 创建**；ungh `pushedAt` **2026-11-12**、页面 `updatedAt` 同日（**活跃**，距今约 7 个月） |
| 默认分支 | `main` |
| Release | 页面 **40 个 Release / 41 个 tag**（ungh releases 接口单页最多返回 30 条） |
| 话题标签 | `agent-skills`、`ai`、`android`、`claude-code`、`cli`、`cpp`、`dsh-plugin`、`html`、`java`、`javascript`、`linux`、`mcp`、`nodejs`、`python`、`rea`、`react`、`rust`、`security`、`touhou`、`typescript` |
| 站点 / 分发 | 官网 <https://rea.tools/> · GitHub Pages <https://morluto.github.io/rea/> · npm 包 <https://www.npmjs.com/package/rea-agents> |

## 工作方式与安装用法

**（1）给 Agent 装。** 在装有 Node.js 与 npm 的机器上运行：

```bash
npx rea-agents setup
```

README 说明：setup 会**选择你的 Agent、展示将要做的改动、经你批准后**注册 REA 的 MCP server 并安装配套 workflow 指令（**对现有配置做备份**），之后需**重启 Agent**；它支持 **Claude Code、Codex、Cursor、Gemini CLI、Grok Build 及其他 Agent**（README 指向安装文档的 supported-agents 一节），其他客户端可走**手动 MCP 注册**。README 另提醒：若通过 package runner 获取，要**保留其缓存条目**；**从目标项目之外的受信目录获取包**。

**（2）直接问 Agent。**

```text
Understand how search works in the Notes app, show me the evidence, and build a
similar feature for my project.
```

**（3）用终端。** 检查一个已解包的 JavaScript/Electron 目录或 ASAR：

```bash
npx -y rea-agents@latest analyze-javascript-application /absolute/path/to/app --json
```

README 举例说明 Windows 下换成类似 `"D:/apps/example"` 的路径；结果为**模块、导入、Electron 边界及其证据**。要长期用可 `npm install --global rea-agents` 后使用 `rea --help`；原生分析需先配置 provider。

**（4）更新。** `rea update`（`rea --update` 是别名）；基于 `npx` 的 Agent setup 用 `npx rea-agents@latest setup` 刷新；Bun 全局更新需 **Bun 1.2.17+**。

**（5）MCP 上下文取舍。** README 说明：**输入 schema 默认 compact**，需要完整字段指引时设 `REA_MCP_INPUT_SCHEMA_PROFILE=full`；**输出 schema 默认完整**，客户端有 frame 上限时设 `REA_MCP_OUTPUT_SCHEMA_PROFILE=compact` 或 `off`（两者都保留服务端校验与完整结构化结果）。

**（6）Linux 前置项。** Ghidra、JADX、固件工具启动以及静态 JavaScript/Electron 分析，需要 REA 的 `PATH` 上有一个 **procps 兼容的 `ps`**，用于进程归属检查。

## 能分析什么（README 目标表摘要）

> 以下均为 **README 自述**、**未在本机验证**；"依赖"列即 README 标注的前置条件。

| 目标类别 | REA 返回（摘要） | 依赖 |
| --- | --- | --- |
| 桌面安装 | 运行时候选、支持文件、组件标记与下一步探测 | 内置（有界目录/头检查） |
| **原生二进制** | 伪代码、汇编、字符串、符号、调用与引用 | **Hopper / Ghidra / IDA / Kuna** |
| Go 二进制 / Rust 二进制 | 编译器版本、模块、依赖边；cargo-auditable crate 名与版本 | 内置静态 ELF/PE/thin Mach-O；**无需装 Go/Rust** |
| GPU code objects | CUDA/HIP 目标、kernel 参数与资源、保留 PTX | 内置静态 |
| 模型文件 | GGUF / safetensors 元数据、张量声明与派生合计 | 内置头检查；可选全文件哈希 |
| IL2CPP / Godot PCK | Unity 全局元数据（版本 24/27/29/31/39）；Godot 归档清单与提取 | 内置静态，**无需装 Unity/Godot** |
| PE 证书 / PE 导入 | Authenticode `WIN_CERTIFICATE` 与签名者字段；DLL 名、序号、IAT RVA、pefile import hash | 内置静态 PE |
| Windows 快捷方式 / ELF notes | LNK 目标与 ExtraData；PT_NOTE/SHT_NOTE 归属、build-id、ABI-tag | 内置静态 |
| 二进制加固 | PIE、NX、RELRO、CFG 等头标志（ELF/PE/Mach-O） | 内置静态 |
| Apple IPS / Linux 内核模块 | 崩溃元数据、线程、镜像、寄存器；`.modinfo`、`__versions` CRC、签名尾 | 内置静态 |
| Erlang BEAM / WASM / EVM | IFF chunks、原子、导入导出；WASM 校验与解码 WAT；EVM 选择器与推断参数 | 内置；**WASM 需 WABT 1.0.42**；**EVM 需 Linux x64** |
| **JavaScript / Electron** | 模块、导入、source map、路由、IPC、native add-ons、`rea audit-release-exposure` | Node.js + npm；Linux 需 procps `ps` |
| 网站 | 页面结构、脚本、网络观察与请求的截图 | Chrome 系浏览器 |
| 抓包（HAR / SAZ） | 请求、响应、暴露载荷与来源位置 | HAR / Fiddler Everywhere SAZ；原生 mitmproxy 需 Linux |
| .NET 程序集 | 元数据、CIL 指令、原生依赖声明、构建比较 | **无（纯静态）** |
| **Android APK / 签名 / 设备 / 资源 / Flutter** | Manifest、类、反编译方法与引用；v1/v2/v3/v3.1 签名与密钥轮换；设备日志与截图；资源解码；Dart snapshot 哈希与各 ABI 摘要 | 无头 **JADX + 完整 JDK**；设备需 **adb**；资源需 **Apktool**；签名/Flutter 无需额外 |
| 固件 | 区域、提取结果与原生分析交接 | Linux 上 **Binwalk / Unblob** |
| 数据包 / pcap / 逻辑信号 | 字段、重复值、解码规则；端点、DNS、TLS SNI、协议分布、HTTP 请求；解码注解与采样范围 | Linux **TShark 4.x + prlimit**；**sigrok-cli 0.7.2** |
| 进程行为 | 终端输出、交互、退出与文件系统观察、运行比较 | Linux/macOS + 原生 PTY |
| 离线 ELF / 崩溃记录 | section/segment、原始符号与重定位；线程寄存器/信号与映射候选 | **调用方提供 pwntools**（Linux x64），可选 GDB/pwndbg |
| PyInstaller / Deno compile | CArchive 成员、Python 版本、入口脚本与字节码；虚拟文件、入口点、记录选项 | 无（纯静态） |
| SQLite / JEB 项目 | 表、列、索引、视图、触发器与部分行；JEB 项目单元与伪代码 | SQLite 需 Node 24.x/26+ 原生 SQLite 限制；JEB 需 `REA_JEB_MCP_URL` |

README 另注：**静态 JavaScript 与 .NET 检查只读文件、不运行应用**；**运行时捕获会以你的用户权限运行或交互目标**（各 runtime 指南描述其副作用）。离线原生检查还支持**调用方提供的 GNU objdump 与 Rizin**（**REA 不安装也不更新这两者**）。provider 选择与格式/宿主支持差异，见 README 指向的 Hopper/Ghidra 安装、IDA、Kuna，以及"实验性 Windows Ghidra 支持"等文档。

## Showcases 与 RL 环境（README 自述）

| Showcase | README 说法 |
| --- | --- |
| **DX-Ball：重建一个声音 pan 计算** | 顺声音调用找到 position-to-pan 辅助函数，检视指令，把不完整伪代码变成 C；**通过 3,205 个原始 x86 用例、复现全部 63 个编译函数字节** |
| **Notion：追踪 Electron 剪贴板桥** | 找 renderer 的 clipboard API，穿过 preload 与 IPC 进入 main 进程，检查富剪贴板格式 |
| **TH04：恢复 DOS 弹幕环计算** | 检视原 PC-98 游戏的 16 位指令，恢复固定与瞄准角度计算，**与历史编译器输出对比** |

RL 环境：README 说明可**把录制的 API 请求/响应变成 Agent 可调用的工具**，导出可运行环境（命令行例：`rea environment-build ./session.har ./my-env --name my-env --json`，运行需要 **AgentEnv 与 Docker**）；另有"**桌面应用环境**"路线（让 Agent 用截图+鼠标键盘操作 Linux 桌面应用），构建机需要 **Linux amd64/arm64 + Docker + 指定版本 agentenv**。

## 使用前需要注意

- **关注度数字三个口径且差异巨大**：README 写 "🎉 400,000 GitHub stars"，GitHub 页面内嵌数据为 **456,153**，ungh 镜像 **453,977**。**引用时务必注明来源与日期**（本页按已给定元数据记录页面与镜像两值）。
- **README 自己写了防假冒警告**：`> [!IMPORTANT]` 明确"从 [morluto/rea]、[rea.tools] 或 [rea-agents npm 包] 获取 REA；**仅从这些官方来源跟随安装与发布链接**；**相似的仓库名或复制的 README 不代表下载是官方的**"。这件事在**关注度极高的项目**上尤其重要——而我**未安装该 npm 包、未核验其发布者身份**。
- **深度能力要靠你自己合法持有的商业/外部工具**：README 的 License 一节明写"**你必须合法获得并获授权使用商业工具**，包括 IDA Pro / Hex-Rays、Hopper 与 JEB；**REA 不授予任何工具许可、密钥或激活权益**；试用与 demo 受厂商条款（用户、功能、自动化、输出共享限制）约束；**破解副本、盗钥、非法共享密钥、绕过授权限制均不被授权**"。即装 REA ≠ 装好了 IDA/Hopper。
- **运行时捕获有副作用**：README 说这类操作"**以你的用户权限运行或交互目标**"。用在生产或他人的系统前，请先读各 runtime 指南与 `DISCLAIMER.md`。
- **依赖链宽且分平台**：静态 JS/.NET 最轻，只要 Node.js；但原生分析、Android（JADX + JDK）、固件（Binwalk/Unblob）、数据包（TShark）、信号（sigrok-cli）各自有平台与外部工具要求，**README 中多处标注 Linux 专用或"Linux 已验证"**。
- **Node.js 版本门槛较新**：需 22.x（>=22.23.4）、24.x（>=24.22.1）或 26.12.1+，配 npm。
- **我只读了 README 与 LICENSE**：**未 clone、未安装 `rea-agents`、未跑 `setup`/CLI/MCP、未验证任何 showcase 的复现数字、未审计源码、未核实 npm 包与站点**；上表能力、流程、依赖与 showcase 结论**全部转述自 README 自述**，与"已独立验证"应严格区分。

## 什么时候会再看

- 想让**本机已登录的编码 Agent**（Claude Code / Codex / Cursor 等）具备"**看到一个 App 的功能 → 查明实现 → 照着重写**"的完整链路时。
- 想给 Agent 接一个**统一的逆向 MCP**，并在内部**切换 Hopper / Ghidra / IDA / Kuna** 这类原生引擎时。
- 想参考"**从录制的 HAR/抓包生成可运行 RL 环境**"或"**桌面应用环境**"这条把逆向产物转成训练资产的思路时。
- 想对照别的"**AI 看软件**"路线——尤其是"**只做证据与解释**"与"**顺手把版本重建出来**"这两种目标的分野时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同样是"看清软件 → 用 AI 重建"这条链路**：[Craft Anything：看清任意软件怎么工作，再让 AI 重建一个自己的版本](craft-anything-看清软件如何工作再用AI重建自己的版本.md)——那篇把 Study/Build/Check/Ready 串成一条**自带动引擎的流水线**并划了 Clean room 边界；本篇是**把逆向能力做成 MCP 工具交给你的 Agent**，深度原生分析再外挂引擎。可对照"**自带引擎**"与"**编排外部引擎**"两种取舍。
- **都是"把逆向工具接给 AI 的 MCP 服务器"**：[ghidra-mcp：把 Ghidra 接给 AI 的 MCP 服务器](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)——那篇是**只服务 Ghidra** 的 200+ 工具服务器；本篇是**再套一层总闸**，把 Ghidra 当作 provider 之一（还含 IDA/Kuna/Hopper 与大量内置静态解析）。
- **都强调"授权与边界"**：[reverse-skill：逆向/渗透/安全研究的技能路由包](reverse-skill-逆向与渗透技能路由包.md)——那篇是**技能路由 + 动手前的范围闸门**（不自己实现工具）；本篇是**MCP 工具实现 + 证据**，并在 License 里把商业工具授权与滥用红线写死。可对照"**路由**"与"**执行**"的分工。
- **被它当作 provider 的底层框架**：[Ghidra：NSA 开源的软件逆向工程框架](ghidra-NSA开源的软件逆向工程框架.md)——理解 REA 的"深度原生分析"实际上退回给谁执行。

## 参考来源

- 仓库主页与 README（**492 行**；核验日期 2026-11-13）：https://github.com/morluto/rea
- 许可证：`LICENSE`（**MIT**，1,064 字符，`Copyright (c) 2026 morluto`）；第三方材料见 `THIRD_PARTY_NOTICES.md`，负责任使用见 `DISCLAIMER.md`
- 站点与分发：<https://rea.tools/> · <https://morluto.github.io/rea/> · npm 包 <https://www.npmjs.com/package/rea-agents>
- 元数据（Star / Watcher / Fork / 创建与推送时间 / 默认分支 / Release 与 tag 数 / topics）：GitHub 页面内嵌 JSON + shields.io + ungh.cc 镜像（**2026-11-13 交叉核对**；README 自述 star 数与页面/镜像不一致，已在正文标注）

以上信息核验于 2026-11-13。**未 clone、未安装 `rea-agents`、未运行 `setup` / 任何 CLI 或 MCP、未接任何 Agent、未验证任何 showcase 的复现结果、未审计源码**；项目能力、工作流、依赖与 showcase 均转述自 README 自述，**未逐一验证**。
