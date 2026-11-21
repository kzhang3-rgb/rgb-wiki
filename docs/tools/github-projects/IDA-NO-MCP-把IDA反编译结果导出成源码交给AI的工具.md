# IDA NO MCP：把 IDA 反编译结果导出成源码文件交给 AI 的免 MCP 工具

> 一句话定位（README 自述）：**"告别 IDA MCP 复杂、冗长、卡顿的交互模式"**、**"AI 逆向，无需额外配置"**（Simple · Fast · Intelligent · Low Cost）。第一句先记清三件事：① 它是**"反 MCP"思路**——不做实时工具调用（tool call），而是把整个 IDA 反编译结果**一次性导出成源码文件 + 一份 `AGENTS.md` 导航上下文**，再丢进任意 AI IDE（Cursor / Claude Code…），靠 IDE 自带的索引、并行、切片能力去分析；② **双轨产物**——**Python 插件**（`oldpython/INP.py`，跑在 IDA 进程内、GUI 用）与 **Rust 独立二进制**（`src/`，产物 `inp`，跑在 IDA 进程外、大文件 / 批量 / 脚本化用），两者**互补而非新旧替代**；③ **硬依赖付费商业软件 IDA Pro 9.x**（二进制运行时链接 IDA 自带的 `libida`/`libidalib`，按平台分发、**不可 redistributable**），且 `idalib` 与 IDA 小版本**物理上硬绑定**。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（280 行中文版）/ `README_EN.md` / `Cargo.toml`、文件清单（18 项）与 ungh 镜像 + shields 元数据；**未 clone、未安装 IDA 插件、未构建 Rust 二进制、未安装 / 运行 IDA Pro、未导出任何二进制、未复现任何性能数字、未审计源码**）
- 适用范围：**需要本机已安装对应平台的 IDA Pro 9.x**（Python 插件版还需 `idat`/IDAPython 环境）；构建 Rust 版另需 LLVM/Clang（bindgen）。面向本机单人逆向分析工作流。

## 核心结论

**事实：它把"AI 逆向"从"实时调工具"改成"先导出成工作区、再让 AI 读源码"。** README 的理念原话是"Text、Source Code、Shell 是 LLM 原生语言……把 IDA 反编译结果导出为源码文件，直接丢进任意 AI IDE（Cursor / Claude Code / ...），天然适配索引、并行、切片（反编译超大函数）等优化"。因此它的产物是一整套**静态文件目录**（`decompile/`、`disassembly/`、`strings.txt`、`imports.txt`、`exports.txt`、`memory/`、`AGENTS.md` 等），而不是一个对外暴露工具的 MCP 服务器。

**事实：双轨设计是"物理限制导致的"，不是叠床架屋。** README 明确解释道：**IDA 插件必须是共享库格式（在 IDA 进程内运行）**，而**绕过大文件分析卡死必须在 IDA 进程外做**（独立二进制 + `--skip-analysis`），"两者无法合一"。两个版本**共享相同的输出格式**（元数据头、`decompiled.c`/`function_index.txt`、`AGENTS.md`），所以 AI 分析时无差别。

**事实：它主要解决的是"大文件"场景。** README 直指原版两个痛点——**内存爆炸**（"原版在 270 万函数时 RAM 用到 140G"）与 **token 爆炸**（"100M 二进制导出近 1G、两万多个文件，AI 根本喂不进去"），因此引入 **`auto` / `legacy` / `consolidated` 三档导出模式**：大文件（默认 > 20000 函数）自动切 `consolidated`，改成**单文件 `decompiled.c`（追加写、常数内存）+ 采样 `callgraph.txt`（N 跳 BFS）+ 跳过 `memory/` + 短串过滤**。

**判断：它的自我定位是"MCP 的替代方案"而非"MCP 的封装"。** 与把 IDA/Ghidra 接成 MCP 服务器的路线相反，它主张"工具应该保持简单"，用"导出文件 + 让 IDE 的通用能力去索引"替代"给模型暴露一堆专用工具"。代价是**没有实时交互**（改一次就要重导出 / 重分析），换来的是**对超大函数、超大工程的吞吐与内存可控**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [P4nda0s/IDA-NO-MCP](https://github.com/P4nda0s/IDA-NO-MCP) |
| 描述（原文） | "Say goodbye to the complex, verbose, and laggy interaction mode of IDA Pro MCP" |
| 许可证 | **⚠️ 仓库内无 `LICENSE` 文件**（`main` / `master` 均 404，shields `not specified`）；但 `Cargo.toml` 里声明 `license = "MIT"`——**声明与文件不一致，复用 / 分发前须向作者确认授权** |
| 主语言 | Python 73.5%（shields 顶层语言；`oldpython/INP.py` 121,512 B 为最大文件） |
| 关注度 | **2,078 Star / 8 关注 / 273 Fork**（ungh，2026-11-19；shields 同期 2.1k / 273）、**8 开放 Issue、贡献者 9** |
| 维护状态 | **2025-12-05 创建**；ungh `pushedAt` **2026-07-26**（⚠️ 约四个月无代码推送）、`updatedAt` 2026-11-15；默认分支 **`main`** |
| 发行 | **0 Release**（仓库含 `.github/workflows/release.yml`（3,324 B）用于构建发布，但尚无发布产物） |
| 体积 | **18 文件**：`src/` 10（Rust）、`oldpython/` 1（`INP.py`）、`.github/` 1、根 6（README ×2、Cargo.toml ×2、build.rs、.gitignore） |

## 双轨形态与输出格式

README 用一张表讲清两个版本的**适用/不适用**：

| 版本 | 形态 | 适用场景 | 不适用 |
| --- | --- | --- | --- |
| **Python 插件**（`oldpython/INP.py`） | IDA 插件（`.py`），GUI 里 `Ctrl-Shift-E` 触发 | IDA GUI 内交互导出**中小文件**；分析时随时导出当前进度 | 100MB+ 大文件（IDA 进程内分析会卡死） |
| **Rust 独立二进制**（`src/`，产物 `inp`） | 命令行可执行文件 | **大文件 / 批量 / 脚本化**；`--skip-analysis` 绕过 IDA 自动分析卡死 | 不能作为 IDA 插件加载（IDA 只加载 `.dylib`/`.so`/`.dll`，不加载可执行文件） |

**导出内容（README 表格摘要）**：`decompiled.c`（consolidated 合并反编译）/ `decompile/`（legacy 每函数一个 `.c`，含 callers/callees）/ `disassembly/`（反编译失败时回退的 `.asm`）/ `function_list.txt` / `function_index.txt` / `callgraph.txt`（采样调用图）/ **`AGENTS.md`（AI 导航上下文，始终生成）** / `disassembly_fallback.txt` / `decompile_failed.txt` / `decompile_skipped.txt` / `strings.txt` / `imports.txt` / `exports.txt` / `memory/`（1MB 分片的 hexdump，consolidated 模式跳过）。每个函数带统一元数据头（`func-name` / `func-address` / `export-type` / `callers` / `callees`）。

README 另建议在 IDB 目录下补充 `apk/`（APK 反编译目录）、`docs/`（逆向报告）、`codes/`（exp / Frida 脚本）等上下文，让 AI 拿到完整视角。

## 使用与构建要点

- **Python 插件**：把 `oldpython/INP.py` 复制或软链到 IDA 插件目录（Windows `%APPDATA%\Hex-Rays\IDA Pro\plugins\`；Linux/macOS `~/.idapro/plugins/`），重启 IDA 后用 `Ctrl-Shift-E` 或 `Edit → Plugins → Export for AI`。另有 headless 批处理：`idat -A -S"INP.py <export_dir> <skip_analysis> <export_mode>" <target.i64>`。
- **Rust 二进制**：需**本机已装对应平台的 IDA Pro 9.x** + LLVM/Clang（bindgen）；设 `IDADIR` 指向 IDA 安装目录后 `cargo build --release`，产物 `target/release/inp`（Windows 为 `inp.exe`）。**⚠️ 不能跨平台交叉编译**（二进制通过 rpath 链接 IDA 的商业库 `libida.dylib`/`libida.dll`/`libida.so`，只随对应平台 IDA 提供；用 SDK stub 库能链接但会运行时崩溃）。
- **运行**：macOS/Linux 需 `DYLD_LIBRARY_PATH`/`LD_LIBRARY_PATH` 指向 `IDADIR`；选项 `-o <dir>`、`--mode auto|legacy|consolidated`、`--skip-analysis`/`-a`、`--force`。
- **⚠️ 版本硬绑定**：`idalib` 底层硬编码了 IDA 内部类的虚函数表偏移，**不同 IDA 大版本物理上不能混用**（否则在 `idalib_check_license` 处 `EXC_BAD_ACCESS` 崩溃）。README 给出对照表：v9.3sp1 → `idalib 0.9.0`、v9.3 → `0.8.1`、v9.2 → `0.7.2`、v9.1 → `0.6.1`、v9.0sp1 → `0.4.1`、v9.0 → `0.3.0`；据此确认或修改 `Cargo.toml`。

## 使用前需要注意

- **⚠️ 无许可证文件**：仓库内查无 `LICENSE`（`main` / `master` 均 404，shields `not specified`），只有 `Cargo.toml` 的 `license = "MIT"` 元数据声明。**声明 ≠ 授权文件**，复用 / 再分发前务必向作者确认。
- **⚠️ 依赖付费商业软件 IDA Pro 9.x**：Rust 版构建与运行、Python 插件运行都离不开本机已授权的 IDA；IDA 的库**不可 redistributable**，不能打包分发。无 IDA 授权则整套工具不可用。
- **⚠️ 四个月无推送**：ungh `pushedAt` 停在 **2026-07-26**（`updatedAt` 为 2026-11-15）；Star 在涨 ≠ 代码在更新。README 自承索引逻辑"部分情况下存在bug🥹🥹🥹🥹"。
- **⚠️ 性能数字为 README 自报**：对照样例 **reqable App.framework（24MB，54,937 函数）**，吞吐 Python 优化后 112 函数/秒 → Rust **133–159 函数/秒（+19%）**，峰值 RSS 1,289 MB → 1,460 MB（判为"持平"）。**单机单样例、本笔记未复现**。
- **⚠️ 0 Release / 需自行构建**：仓库有 release 工作流文件但无发布产物；Rust 版须自行在目标平台构建。
- **⚠️ 导出物本身高度敏感**：产物是**完整反编译源码 + 字符串表 + 内存 hexdump**（含完整逻辑与数据），本笔记只记录工具与其元数据，**不涉及任何具体二进制或内容的获取方式**；使用者在保管、分享导出目录时应自行评估保密与合规边界，并确保仅在**合法授权范围内**做逆向分析。
- 本笔记**只读 README / `README_EN.md` / `Cargo.toml` / 文件清单与元数据，未 clone、未安装、未构建、未运行、未导出任何文件、未审计源码**；文中构建步骤、选项、性能与模式阈值均为 README 自述。

## 什么时候会再看

- 想把 **IDA 的反编译结果一次性交给 AI IDE**（而不是接一套 MCP 服务器）、或面对**超大二进制 / 大工程**需要控制内存与 token 时；
- 想参考"**把工具拆成'进程内插件 + 进程外独立二进制'两条轨道、但共享同一套输出格式**"这种因宿主物理限制而做的设计取舍时；
- 想研究"**大文件导出如何省内存 / 省 token**"（流式 append、单文件合并、采样调用图、按长度过滤字符串、跳过 raw memory）的具体做法时；
- 想对照"**给逆向工具接 AI**"的几条路线（见下）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **最直接的路线对照（MCP vs 免 MCP）**：[ghidra-mcp：把 Ghidra 接给 AI 的 MCP 服务器](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)——那是把逆向能力**实时暴露成 MCP 工具**（自述 219 个工具）的路线；本篇是**反其道而行**，把结果**导出成源码工作区**让 AI IDE 去读。可对照"**实时工具调用**"与"**一次性导出 + IDE 索引**"两种交付形态。
- **同为反编译器接 AI，宿主不同**：[jadx-mcp-server：给 JADX 反编译接 AI 的 MCP 服务器](jadx-mcp-server-给JADX反编译接AI的MCP服务器.md)——那是 JADX（安卓）的 MCP 服务器；本篇面向 IDA（原生二进制）且走导出路线。可对照"**不同宿主 + 不同交付形态**"的组合。
- **上层技能路由**：[reverse-skill：逆向与渗透技能路由包](reverse-skill-逆向与渗透技能路由包.md)——那是回答"该用哪个工具"的路由层；本篇是被它可能调用的底层能力之一（导出结果供 AI 分析）。
- **同为"把逆向能力交给 AI Agent"**：[REA：用 Agent 把软件从行为逆向到原生二进制](rea-用Agent逆向软件行为与原生二进制.md)——那篇自带本地 MCP、把 Ghidra / IDA / Hopper 当众多 provider 之一；本篇只服务 IDA 一个宿主且走导出路线。可对照"**多引擎总闸**"与"**单宿主导出**"。

## 参考来源

- 项目仓库与 README（默认分支 `main`）：https://github.com/P4nda0s/IDA-NO-MCP
- `Cargo.toml`（`license = "MIT"` 声明与依赖 `idalib = "0.9"`）、`README_EN.md`：同仓库 `main` 分支
- `idalib` 版本对照与 issue：https://github.com/idalib-rs/idalib
- 元数据（Star / Fork / 创建与推送时间 / 默认分支）：ungh.cc 镜像 https://ungh.cc/repos/P4nda0s/IDA-NO-MCP 与 shields.io 徽章

以上信息核验于 2026-11-19。仅阅读 README、`README_EN.md`、`Cargo.toml`、文件清单与元数据；未 clone、未构建、未运行、未导出，未审计源码。
