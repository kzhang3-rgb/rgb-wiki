# ghidra：NSA 开源的软件逆向工程框架

> 想弄清 Ghidra 是什么、装了以后怎么启动、能不能脱离 GUI 批量分析，以及要不要把它接进自己的自动化流程时，先看这篇。

- 状态：草稿
- 最近核验：2026-11-09（读取了 `README.md` 全文、`LICENSE` 首部，以及 ungh.cc 镜像返回的仓库元数据；未下载发行版、未安装 JDK/Gradle、未运行 `ghidraRun`，也未做任何反编译）
- 适用范围：软件逆向 / SRE（Software Reverse Engineering）。官方发行版跨 Windows、macOS、Linux；**README 明写安装官方预编译版需要 JDK 25 64 位**。属于通用分析平台，不针对某一类文件格式或某一处理器。

## 核心结论

Ghidra 是美国国家安全局（NSA）研究理事会创建并维护的一套软件逆向工程框架（事实，来自 README 首段与仓库元数据的描述字段）。它包含一整套分析工具，可以对 Windows、macOS、Linux 上编译出来的代码做**反汇编、汇编、反编译、图形化、脚本化**等操作，支持多种处理器指令集与可执行文件格式，既能**交互式**使用，也能**自动化**模式运行；使用者可以用 **Java 或 Python** 编写自定义扩展组件与脚本（以上均为 README 明述的事实）。

判断：它更接近一个"平台/框架"而非单一命令行工具——README 自述其定位是解决大型 SRE 任务的**规模化与团队协作**问题，并提供可定制、可扩展的研究平台。对个人使用者而言，最直接的入口是带 GUI 的客户端；对自动化场景，则要关注它的 headless 与 PyGhidra 路径（README 里 `ghidraRun` 与 `support/pyghidraRun` 两种启动方式并列列出）。

另需留意：README 顶部有一节 **Security Warning**，明说"某些版本的 Ghidra 存在已知安全漏洞"，要求使用前先阅读其 Security Advisories —— 这是项目自己给出的风险提示（事实）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [NationalSecurityAgency/ghidra](https://github.com/NationalSecurityAgency/ghidra) |
| 描述定位 | "Ghidra is a software reverse engineering (SRE) framework" |
| 许可证 | Apache-2.0（根目录 `LICENSE`，约 11,357 字符；开头为标准 Apache License 2.0 正文） |
| 主语言 | Java 86.1% |
| 关注度 | 98,593 Star / 1,206 关注 / 11,186 Fork（ungh.cc 镜像，核验日 2026-11-09） |
| 维护状态 | 2019-03-01 创建；最近推送 2026-11-06；开放 Issue 约 1.6k |
| 发行版 | 57 个 Release / 60 个 tag |
| 话题标签 | disassembler, reverse-engineering, software-analysis |

（Star/Fork/Issue/推送时间等为易变信息，以上取 2026-11-09 的 ungh.cc 镜像值；GitHub API 匿名配额已用尽，未取到 `size`。）

## 用法要点

以下均来自 README，只整理其明述内容：

- **安装官方预编译版**：先装 **JDK 25 64 位**，再从 Releases 下载多平台发行包（README 特别提醒：文件名形如 `ghidra_<version>_<release>_<date>.zip`，要去 Assets 里找，**不要**下成 "Source Code"）；解压（**不要**覆盖到已有安装上）；用 `./ghidraRun`（Windows 用 `ghidraRun.bat`）启动，或用 `./support/pyghidraRun` 启动 **PyGhidra**。更多安装与排错见安装目录根部的 Getting Started 文档。
- **从源码构建**：需要 JDK 25 64 位、**Gradle 9.1.0+**（或仓库自带 wrapper）、**Python3（3.9 到 3.15）**含 pip；Linux/macOS 需要 GCC 或 Clang 与 make；Windows 需要 Visual Studio 2017+ 或 Microsoft C++ Build Tools（含 MSVC、Windows SDK、C++ ATL 组件）。流程为下载/克隆源码 → `gradle -I gradle/support/fetchDependencies.gradle` → `gradle buildGhidra`，产物在 `build/dist/`。
- **二次开发**：写用户脚本与扩展可通过 Eclipse 的 **GhidraDev** 插件（在发行包的 `Extensions/Eclipse/GhidraDev/` 下），或在 Script Manager 里点 VS Code 图标、从 CodeBrowser 的 *Tools -> Create VSCode Module project* 生成 VS Code 工程。README 提醒：GhidraDev 与 VS Code 集成**只支持针对已完整构建的 Ghidra 安装**开发。
- **开发 Ghidra 本体**：README 强烈推荐用 Eclipse，流程为 `gradle prepdev eclipse buildNatives`，再以 "Existing Projects into Workspace" 导入（勾选搜索嵌套项目）用内置 run 配置启动调试。

## 使用前需要注意

- **安全提示**：README 明确警告某些版本存在已知安全漏洞，并指向其 Security Advisories，使用前应自行核对版本。
- **工具链版本较新**：官方安装即要求 JDK 25，构建还要求 Gradle 9.1.0+ 与 Python 3.9–3.15；环境不匹配会导致装不上或构建失败（事实，README 要求）。
- **导出合规**：该项目由 NSA 开源，具备高级反汇编/反编译能力；用于逆向分析他方软件时，需自行确认当地法律与授权范围，本笔记不构成法律意见（判断）。
- **未验证项**：我没有实际安装或运行，README 中的启动命令、构建步骤、依赖版本均按文档转述，未在真实环境复核是否照做即通。

## 什么时候会再看

- 需要一套可扩展、能写脚本插件的反汇编/反编译平台，而现有工具不够用或不便自动化时。
- 想在无 GUI 的服务器上做批量分析（headless / PyGhidra 方向），先回来核对启动方式与依赖。
- 要写 Ghidra 扩展或脚本，回顾 Java/Python 脚本入口与开发环境搭建路径时。

## 相关笔记

- **被 AI 接驳的用法**：[ghidra-mcp](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)——把 Ghidra 的分析能力通过 MCP 暴露给 AI，是"人类用 GUI、AI 用接口"的一种组合方式。
- **同属逆向链路的 Android 侧**：[Apktool](Apktool-AndroidAPK反编译与打包工具.md)——面向 APK 资源与 smali 的反编译/回编译，与 Ghidra 针对二进制代码的分析互补。
- **可执行文件格式的解析与改写**：[LIEF](LIEF-可执行文件格式解析与改写库.md)——当需要以库的方式解析/修改 ELF、PE 等格式而非交互式逆向时更合适。
- **先识别文件类型与加壳**：[Detect-It-Easy](Detect-It-Easy-文件类型与加壳识别工具.md)——在动 Ghidra 之前判断样本类型与是否加壳。
- **调试环节的补充**：[pwndbg](pwndbg-为GDB与LLDB打造的调试与逆向插件.md)——Ghidra 偏静态分析，动态调试常配合 GDB/LLDB 类工具进行。
- **把 Ghidra 当作其中一层的上层外壳**：[craft-anything：看清任意软件怎么工作，再让 AI 重建一个自己的版本](craft-anything-看清软件如何工作再用AI重建自己的版本.md)——那是"逆向理解 + AI 重建"的流水线，其 Deep 模式在检测到本机装了 Ghidra 时会调用它；本篇是被调用的那一层。
- **把它当作外部引擎调用的上层**：[REA：用 Agent 把软件从行为逆向到原生二进制](rea-用Agent逆向软件行为与原生二进制.md)——那篇在需要深度原生分析时**外挂** Hopper / Ghidra / IDA / Kuna，Ghidra 是可选 provider 之一；本篇是引擎本身。可对照"**把引擎当工具的编排层**"与"**引擎本体**"。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/NationalSecurityAgency/ghidra/blob/master/README.md
- 许可证文件：https://github.com/NationalSecurityAgency/ghidra/blob/master/LICENSE
- 仓库元数据（Star/Fork/创建与推送时间/默认分支）：https://ungh.cc/repos/NationalSecurityAgency/ghidra
- 安全公告：https://github.com/NationalSecurityAgency/ghidra/security/advisories

以上信息核验于 2026-11-09。
