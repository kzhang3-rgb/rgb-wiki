# pwntools：CTF 与漏洞利用开发的 Python 框架

> 打 CTF 的 Pwn 题、写 exploit 时几乎必备的 Python 库；当你想知道它到底提供什么、装在哪、许可是什么时，再回看这篇。

- 状态：草稿
- 最近核验：2026-11-09（阅读了 `Gallopsled/pwntools` 默认分支 `dev` 的 `README.md` 与根目录 `LICENSE-pwntools.txt`，以及元数据；未安装、未运行、未 clone）
- 适用范围：CTF/漏洞利用开发，主要面向 Linux（README 称最佳支持 64 位 Ubuntu LTS 22.04/24.04），大多数功能在类 POSIX 系统可用；需 Python 3.10+（5.0.0 起），旧环境用 4.x

## 核心结论

pwntools 是一个用 Python 写的 **CTF 框架与漏洞利用开发库**，目标是让 exploit 编写尽量简单，面向快速原型与开发。README 给的典型用法是：`from pwn import *`，设置 `context(arch='i386', os='linux')`，`remote(...)` 连远程，`asm(shellcraft.sh())` 生成并发送 shellcode，最后 `r.interactive()` 交互。

**事实层面**：它自称 "CTF framework and exploit development library"，文档在 docs.pwntools.com，并提供配套的教程仓库（pwntools-tutorial）与往届 CTF 题解仓库（pwntools-write-ups）。README 明确指出：核心功能大多自包含、纯 Python，但**汇编/反汇编非本机架构等特性需要非 Python 依赖**。**判断层面**：它是 Pwn 方向的"基础设施型"工具库，涵盖远程连接、ELF 解析、汇编/反汇编、shellcode 生成等常用能力；本笔记只据 README 与元数据描述，未实测其各模块在具体环境下的可用性。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Gallopsled/pwntools](https://github.com/Gallopsled/pwntools) |
| 描述定位 | "CTF framework and exploit development library"——CTF 框架与漏洞利用开发库 |
| 许可证 | **特殊**：根目录**没有 `LICENSE`**（GitHub 报 `NOASSERTION` / "Other"）；实际许可写在根目录 `LICENSE-pwntools.txt`（1,645 字符），为自制混合文案——"大部分是 MIT，但少数部分为 GPL 或 BSD 2-clause"（详见下文核实） |
| 主语言 | Python 46.4%（top；另有 C、汇编等） |
| 关注度 | 13,833 Star / 272 关注 / 1,876 Fork；开放 Issue 87（核验 2026-11-09） |
| 维护状态 | 2013-04-29 创建；最近推送 2026-11-02；默认分支 **`dev`**（不是 `main`） |
| 发行版 | 80 个 Release / 125 个 tag |
| 话题标签 | assembly, capture-the-flag, ctf, exploit, pwnable, rop, shellcode, shellcode-development, python 等（数量很多） |

## 用法要点

来自 README 的真实内容：

- **最小示例**：`from pwn import *` → `context(arch='i386', os='linux')` → `r = remote('host', port)` → `r.send(asm(shellcraft.sh()))` → `r.interactive()`。
- **文档与教程**：主文档 <https://docs.pwntools.com/>；教程在 `Gallopsled/pwntools-tutorial`；往届题解在 `Gallopsled/pwntools-write-ups`。
- **安装（README 给的命令）**：
  ```sh
  sudo apt-get update
  sudo apt-get install python3 python3-pip python3-dev git libssl-dev libffi-dev build-essential
  python3 -m pip install --upgrade pip
  python3 -m pip install --upgrade pwntools
  ```
- **支持范围**：最佳支持 64 位 Ubuntu LTS（22.04、24.04）；多数功能可在任何类 POSIX 发行版（Debian、Arch、FreeBSD、OSX 等）工作。5.0.0 起支持 Python 3.10+，更旧版本/Python 2.7 用 4.x。
- **非 Python 依赖**：汇编/反汇编"外部架构"等功能需要额外非 Python 依赖，详见官方安装文档。

## 使用前需要注意

- **许可证需自行读原文**：GitHub 页面/API 对根目录许可报 `NOASSERTION`，因为**根目录没有标准 `LICENSE`**，真正的许可文本在 `LICENSE-pwntools.txt`。该文件自述：**项目整体开源，大部分为 MIT，但少数部分为 GPL 或 BSD 2-clause**；这些例外代码集中在目录 `pwnlib/constants/` 与 `pwnlib/data/`，其许可在文件头或该目录下 `LICENSE.txt` 中单独说明。若你要把 pwntools 或其片段用于闭源/商业集成，**务必逐目录核对**，勿仅凭 README 里的 MIT 徽章下结论。
- **默认分支是 `dev`**：不是 `main`；引用源码链接时注意分支名。
- **依赖与平台**：完整功能不是"纯 pip 装完即用"，跨架构汇编/反汇编需额外系统依赖；Windows 不在"最佳支持"之列。
- **未验证项**：本笔记仅阅读 README 与许可文件及元数据，**未安装、未运行、未 clone**，未实测任何模块行为。
- **易变信息**：Star/Fork/Issue/Release/tag 及推送时间为 2026-11-09 快照。

## 什么时候会再看

- 开打 Pwn 题、需要快速搭 exploit 脚本骨架时。
- 需要确认某个版本的 Python 兼容性、或首次在非 Ubuntu 环境安装踩坑时。
- 需要判断把 pwntools 用于非 CTF/商业场景的许可证边界时（回看 `LICENSE-pwntools.txt`）。
- 想找官方教程或往届题解练手时。

## 相关笔记

- **调试搭档**：[pwndbg](pwndbg-为GDB与LLDB打造的调试与逆向插件.md)——搭配 GDB/LLDB 做动态调试，常与 pwntools 脚本配合定位漏洞。
- **解析目标二进制**：[LIEF](LIEF-可执行文件格式解析与改写库.md)——用 Python 解析/改写 ELF/PE 等格式，可与 pwntools 的 ELF 能力相互补充。
- **逆向与渗透技能路由**：[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)——组织逆向/渗透相关技能与工具时的入口。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：<https://github.com/Gallopsled/pwntools>（README 取自 `raw.githubusercontent.com/Gallopsled/pwntools/dev/README.md`）
- 许可证文件：<https://raw.githubusercontent.com/Gallopsled/pwntools/dev/LICENSE-pwntools.txt>
- 官方文档：<https://docs.pwntools.com/>
- 元数据（Star/Fork/Issue/时间等，2026-11-09 核验）：ungh.cc 镜像 `https://ungh.cc/repos/Gallopsled/pwntools` 与 shields.io

以上信息核验于 2026-11-09。
