# mcp-windbg：给 WinDbg 接 AI 的 MCP 服务器

> 记录一个把 Windows 调试器（CDB/KD）与 AI 接起来的 MCP 服务器：用 `cdb.exe`/`kd.exe` 无界面地分析崩溃转储、附加用户态/内核目标，并提供 `--filter-script` 在上传前脱敏。第一句先记清三点：**它驱动的其实是 `cdb.exe`/`kd.exe`（不依赖 WinDbg 图形界面）**；**微软已自带官方 WinDbg MCP，本项目是独立社区替代**；**默认分支是 `develop`（不是 main）**。

- 状态：草稿
- 最近核验：2026-11-09（阅读 `develop` 分支 README 全文（英文 243 行）、工具与场景表、安装方式、文档索引与元数据；**未安装、未接任何客户端、未跑任何转储/调试会话**）
- 适用范围：**Windows 平台**，需装 **Debugging Tools for Windows** 或 Microsoft Store 版 WinDbg（提供 `cdb.exe` / `kd.exe`，会自动探测）；客户端需支持 MCP（Claude Code、GitHub Copilot、Claude Desktop、Cursor、Windsurf、Cline 等）。

## 核心结论

**事实：它把 AI 接到 Windows 调试器上，底座是 CDB 与 KD 两个命令行调试器。** README 明说：用户态（转储、`-remote`）走 **CDB**，内核目标（`-k`）走 **KD**；它是一个"包在 `cdb.exe`/`kd.exe` 外面的 Python 外壳"，让 LLM 跑真实调试命令并解读输出。**README 也直说"它不是魔法自动修复"（not a magical auto-fix）。**

**事实：微软已发布官方 WinDbg MCP，本项目自定位为独立社区替代。** README 顶部 TIP 提示：**从 WinDbg 1.2610.1001.0 起微软官方附带 WinDbg MCP**（在 WinDbg 的 MCP 服务设置里开启，用 VS Code + GitHub Copilot 或 Copilot CLI 对话）。本项目**独立于微软**，卖点是：**无头运行 `cdb`/`kd`**，因此适合"用别的 MCP 客户端""从另一台机器经 HTTP 驱动调试器""不打开 WinDbg UI、批量脚本化分诊大量转储"。

**事实：工具面是一条"打开会话 → 跑命令 → 关闭"的会话模型。** README 的 Tools 表列出 11 个工具：`list_dumps`、`open_cdb_dump`、`open_cdb_remote`、`open_kd_session`、`open_kd_dump`、`run_cdb_command`、`run_kd_command`、`close_cdb_session`、`close_kd_session`、`send_ctrl_break`、`wait_for_break`。每次 `open_*` 返回一个不透明的 `session_id`（如 `cdb-1a2b3c4d`），可同时开多个会话并分别寻址。

**判断：它更偏"运维式排障/批量分诊"而非交互式调试。** 因为它驱动的是无头调试器、面向"一次调用拿到 `!analyze -v` 等结果"和"扫一目录的转储"，对"想用 AI 帮忙看崩溃/内核 bugcheck 的开发与支持场景"更贴；**实际稳定性与命令覆盖未在本笔记验证。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [svnscha/mcp-windbg](https://github.com/svnscha/mcp-windbg) |
| 描述定位 | 面向 WinDbg 的 MCP 服务器；无头驱动 `cdb.exe`/`kd.exe`，做崩溃转储分析、用户态远程调试与内核调试 |
| 许可证 | **MIT**（`LICENSE` 1,073 字符）；作者 **Sven Scharmentke**（发布的可执行文件由 Liasoft GmbH 提供代码签名证书） |
| 主语言 | Python 93.8%（shields.io top 语言） |
| 关注度 | **1,656 Star、167 Fork、22 关注、0 个开放 Issue**（2026-11-09 核验；ungh.cc 镜像当时给 1,665/168，镜像与页面略有差异） |
| 维护状态 | **2025-05-02 创建**；`pushed_at` 2026-10-28（活跃）；**默认分支 `develop`**（不是 main） |
| 发行版 | **17 个 Release / 23 个 tag**；每个 Release 附单文件 `mcp-windbg.exe`（自带解释器，代码签名）与 `SHA256SUMS` |
| 话题标签 | copilot、copilot-chat、crash-dump、crash-reports、mcp、mcp-server、windbg、windbg-extension |

## 用法要点

**（1）能做什么（README 的 Use cases 表）。** 想对崩溃/蓝屏 `.dmp` 定根因、想中断挂起的用户态进程（经 `cdb -server`）、想调内核/驱动/`bugcheck`（KDNET/命名管道/串口）、想批量分诊一目录转储、想在别的机器上经 HTTP 驱动调试、想在输出离开本机前抹掉秘密——各自对应一篇场景文档。

**（2）安装到 Claude Code：三种插件。** README 明确区分：`mcp-windbg-uvx`（**服务器**，插件用 uvx 自动拉起）、`mcp-windbg-skills`（**四个技能**：`analyze-dump` / `debug-remote` / `kernel-debug` / `windbg-doctor`）、`mcp-windbg-agents`（**一个 `crash-analyst` 子代理**）。最短路：

```
/plugin marketplace add svnscha/mcp-windbg
/plugin install mcp-windbg-uvx@mcp-windbg
```

uvx 需要 [uv](https://docs.astral.sh/uv/)（`winget install astral-sh.uv`）。**企业注意**：README 警告 Claude Code 受管设置若定义了 `allowedMcpServers`，插件自带的 MCP 服务器可能被静默跳过（指向 claude-code issue #32882），作者建议**手动注册**。

**（3）手动注册 / 其它客户端（需要 Python 3.10+）。**

```bash
pip install mcp-windbg
claude mcp add mcp-windbg -s user -e _NT_SYMBOL_PATH="SRV*C:\Symbols*https://msdl.microsoft.com/download/symbols" -- python -m mcp_windbg
```

VS Code / GitHub Copilot 用 `F1` 选 **MCP: Open User Configuration**，在 `servers` 下配 `"command": "python"`、`"args": ["-m", "mcp_windbg"]` 并设 `_NT_SYMBOL_PATH`。

**（4）免 Python 安装。** 每个 Release 附单文件 `mcp-windbg.exe`（自带解释器与依赖、已代码签名），客户端 `command` 指向它、**不填 `args`**；`cdb.exe` 仍必需。Release 还附 `SHA256SUMS`，可用 `Get-FileHash` 与 `Get-AuthenticodeSignature` 校验（`Status` 应为 `Valid`）。

**（5）运行形态与选项。** 支持 **stdio**（本地）或 **streamable-HTTP**（从另一台机器驱动）。README 提到的主要选项：`--cdb-path`、`--kd-path`、`--symbols-path`、`--filter-script`（**在参数与输出离开本机前做 PII/密钥脱敏**）、`--transport`。文档站：**svnscha.github.io/mcp-windbg**（含工具、提示词、CLI、客户端配置参考与排障）。

## 使用前需要注意

- **平台限定 Windows**：依赖 `cdb.exe`/`kd.exe`，非 Windows 环境不适用。
- **"把调试能力交给 AI"的风险**：它能对活动会话执行**任意 WinDbg/KD 命令**（`!process 0 0`、`!heap`、`lm` 等）；内核会话的 `close_kd_session` 会**恢复目标机运行**——操作前应清楚它会中断/放行目标。
- **数据外泄面**：调试输出可能含内存内容、路径、PII；README 为此提供 `--filter-script`，**但是否启用、规则是否周全由使用者负责**——把转储交给云端模型前建议开启脱敏。
- **与官方微软 WinDbg MCP 的关系**：若你本就用 WinDbg 1.2610.1001.0+ 且想要"内建 AI 调试"，README 建议优先看官方方案；本项目在"别的客户端 / 远程 / 无头批量"场景才有差异。
- **默认分支是 `develop`**：默认分支非 `main`，跟默认分支取得的可能是开发中状态；要稳定形态应取 Release。
- **核实程度**：本笔记**只读 README 与元数据，未安装、未接任何客户端、未跑任何转储或调试会话**；Star/Fork/Issue 与时间均以 2026-11-09 为准，增长很快。

## 什么时候会再看

- 手上有一堆 Windows 崩溃转储（`.dmp`/`.mdmp`/`MEMORY.DMP`）或要调内核/驱动，想让 AI 帮忙批量化分诊、说清 `bugcheck` 时；
- 想用**非微软官方**的途径把 WinDbg 调试接进 Claude Code / Copilot / Cursor 等客户端，或需要**从另一台机器经 HTTP 驱动**时；
- 想参考"**工具输出在离开本机前脱敏**（`--filter-script`）"这类做法的实现时。

## 相关笔记

- **同类"给逆向/调试工具接 AI"**：[ghidra-mcp：把 Ghidra 接给 AI 的 MCP 服务器](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)——那是 Ghidra 侧（静态反汇编为主）的 MCP；本篇面向 Windows 调试与崩溃分析。
- **同类"给逆向工具接 AI"**：[radare2-mcp：给 radare2 接 AI 的 MCP 服务器](radare2-mcp-给radare2接AI的MCP服务器.md)——那是 C 写的 radare2 版 MCP；本篇是 Python 写的 WinDbg 版。
- **同为 GDB/调试生态**：[pwndbg：为 GDB 与 LLDB 打造的调试与逆向插件](pwndbg-为GDB与LLDB打造的调试与逆向插件.md)——那是 GDB/LLDB 侧的调试增强插件；本篇把 WinDbg/CDB/KD 接给 AI，一个在 Linux 调试栈、一个在 Windows 调试栈。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README（默认分支 `develop`）：https://github.com/svnscha/mcp-windbg
- 官方文档站：https://svnscha.github.io/mcp-windbg/
- 微软官方 WinDbg MCP 说明（README 引用）：https://learn.microsoft.com/en-us/windows-hardware/drivers/debuggercmds/windbg-mcp-overview
- 元数据（Star / Fork / 创建与推送时间 / 默认分支 / 发行版）：GitHub 页面内嵌 JSON 与 ungh.cc 镜像 https://ungh.cc/repos/svnscha/mcp-windbg

以上信息核验于 2026-11-09（GitHub API 匿名配额用尽，未取到 `size`）。
