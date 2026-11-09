# pwndbg：为 GDB 与 LLDB 打造的调试与逆向增强插件

> 当原版 GDB/LLDB 让你在逆向与 exploit 开发中处处别扭时，pwndbg 是补足这些短板的插件；想知道它支持哪些调试器/版本、装之前有什么坑，看这篇。

- 状态：草稿
- 最近核验：2026-11-09（阅读了 `pwndbg/pwndbg` 默认分支 `dev` 的 `README.md` 与根目录 `LICENSE.md`，以及元数据；未安装、未运行、未 clone）
- 适用范围：低层软件开发、硬件 hacking、逆向与漏洞利用开发；支持 GDB 与 LLDB 两条路线，对版本有明确要求（见下表），LLDB 支持仍属早期阶段

## 核心结论

pwndbg（读作 /paʊnˈdiˌbʌɡ/）是一个 **GDB 与 LLDB 插件**，目标定位是"让调试少受点罪"，重点服务低层开发者、硬件黑客、逆向工程师与 exploit 开发者。README 解释其动机：原版 GDB/LLDB 用于逆向与漏洞利用开发时体验很差（比如要敲 `x/30gx $rsp`、LLDB 命令繁琐，且"到了 2026 年核心调试器仍缺少健壮的 hexdump 等易用功能"，WinDbg 用户切到 GDB/LLDB 会很不适应）。

**事实层面**：pwndbg 是一个 Python 模块，可加载进 GDB，也可作为 LLDB 的 REPL 接口运行，提供一系列工具与增强来填补原版调试器的空缺。README 明确列出对标的既有项目（gdbinit、PEDA、GEF、bata24/GEF），并指出这些工具"难以扩展、以单个大文件分发"（附了 103KB / 195KB / 423KB / 5.24MB 的体积），pwndbg 自称要做得更快、更干净、更健壮。**判断层面**：它属于"逆向/Pwn 日常调试环境"的常用增强件，与 pwntools 分工互补（一个偏调试观察、一个偏 exploit 编写）；本笔记只据 README 与元数据描述，未实测其功能。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [pwndbg/pwndbg](https://github.com/pwndbg/pwndbg) |
| 描述定位 | "Exploit Development and Reverse Engineering with GDB & LLDB Made Easy"——用 GDB 与 LLDB 做漏洞利用开发与逆向工程 |
| 许可证 | MIT（**根目录文件名是 `LICENSE.md`**，1,098 字符，版权 2026 Dominik 'Disconnect3d' Czarnota） |
| 主语言 | Python 96.6% |
| 关注度 | 11,164 Star / 127 关注 / 1,317 Fork；开放 Issue 184（核验 2026-11-09） |
| 维护状态 | 2015-02-22 创建；最近推送 2026-11-07；默认分支 **`dev`**（不是 `main`） |
| 发行版 | 29 个 Release / 32 个 tag |
| 话题标签 | gdb, lldb, debugging, disassembler, exploit-development, gef, hacking-tool, ida-pro, malware-analysis, pwnable, pwndbg, python, reverse-engineering 等 |

## 用法要点

来自 README 的真实内容：

- **两条路线**：作为 Python 模块加载进 GDB，或作为 LLDB 的 REPL 接口运行，两者都提供一致的体验。
- **该用 GDB 还是 LLDB**（README 给的对照表）：
  | 场景 | 支持调试器 |
  | --- | --- |
  | 调试 Linux 二进制或 ELF | GDB、LLDB |
  | 调试 macOS 上的 Mach-O | **LLDB** |
  | Linux 内核调试（qemu-system） | GDB、LLDB |
  | Linux 用户态模拟（qemu-user） | **GDB** |
  | 嵌入式调试（ARM Cortex M* 或 RISC-V/32） | GDB、LLDB |
- **兼容性要求**：
  | 组件 | 支持版本 | 备注 |
  | --- | --- | --- |
  | pwndbg-gdb | Python 3.10+、GDB 12.1+ | 在 Ubuntu 22.04/24.04 上久经考验 |
  | pwndbg-lldb | Python 3.12+、LLDB 19+ | 实验性/早期阶段 |
  | qemu-user | QEMU 8.1+ | vmmap 需要 vFile API |
  | qemu-system | QEMU 6.2+ | 自 Ubuntu 22.04 起支持 |
- **安装与文档**：安装步骤见 <https://pwndbg.re/stable/setup>；功能清单见 Features 页、另有可打印的 [CHEATSHEET](https://pwndbg.re/dev/CHEATSHEET.pdf)，文档在 <https://pwndbg.re>。

## 使用前需要注意

- **LLDB 支持尚不成熟**：README 明确声明 LLDB 实现仍处早期阶段，可能有 bug 或限制，已知问题集中在 GitHub 上 `LLDB Port` 标签的 Issue 里。
- **版本门槛**：GDB 路线要 Python 3.10+ 与 GDB 12.1+；LLDB 路线要 Python 3.12+ 与 LLDB 19+。版本不满足会影响使用。
- **默认分支是 `dev`**：不是 `main`；引用源码/安装脚本时注意分支。
- **许可证**：MIT，但**根目录文件名是 `LICENSE.md`（不是 `LICENSE`）**，版权署名在该文件内（2026 Dominik 'Disconnect3d' Czarnota）。
- **未验证项**：本笔记仅阅读 README、`LICENSE.md` 与元数据，**未安装、未运行、未 clone**，未实测在 GDB/LLDB 中的实际表现；README 中"更快、更干净、更健壮"属项目自述与相对比较，未在本笔记独立验证。
- **易变信息**：Star/Fork/Issue/Release/tag 及推送时间为 2026-11-09 快照。

## 什么时候会再看

- 准备给自己机器配一套逆向/Pwn 调试环境、纠结用 GDB 还是 LLDB 时。
- 遇到 GDB/LLDB 版本或 Python 版本不匹配导致的加载问题时。
- 需要随时查快捷命令 / 功能清单（CHEATSHEET）时。
- 想对比 pwndbg 与 GEF/PEDA 等同类插件时。

## 相关笔记

- **exploit 编写搭档**：[pwntools](pwntools-CTF与漏洞利用开发框架.md)——写 exploit 脚本常与 pwndbg 动态调试配合使用。
- **Windows 上的调试 AI 化**：[mcp-windbg](mcp-windbg-给WinDbg接AI的MCP服务器.md)——在 Windows/WinDbg 侧做调试增强，可与 pwndbg 的 GDB/LLDB 路线对照。
- **逆向框架**：[ghidra](ghidra-NSA开源的软件逆向工程框架.md)——静态反汇编/反编译，与 pwndbg 的动态调试互为补充。
- **逆向与渗透技能路由**：[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)——组织逆向/渗透相关技能与工具时的入口。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：<https://github.com/pwndbg/pwndbg>（README 取自 `raw.githubusercontent.com/pwndbg/pwndbg/dev/README.md`）
- 许可证文件：<https://raw.githubusercontent.com/pwndbg/pwndbg/dev/LICENSE.md>
- 官方文档站：<https://pwndbg.re>
- 元数据（Star/Fork/Issue/时间等，2026-11-09 核验）：ungh.cc 镜像 `https://ungh.cc/repos/pwndbg/pwndbg` 与 shields.io

以上信息核验于 2026-11-09。
