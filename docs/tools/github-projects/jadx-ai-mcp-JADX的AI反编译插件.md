# jadx-ai-mcp：JADX 的 AI 反编译插件

> 当你想给 JADX 反编译器装上"AI 助手"，让 Claude、Codex 等 LLM 实时读你正在看的类、查交叉引用、重命名混淆符号时，看这篇；它讲的是这对组合里跑在 JADX-GUI 内部、真正执行反编译操作的那个插件。

- 状态：草稿
- 最近核验：2026-11-09（阅读了 `jadx-ai` 分支 README.md 全文，并核对仓库元数据；**未安装、未运行、未 clone**）
- 适用范围：JADX-GUI 插件（README 标注 Java 11+），需与配套的 MCP 服务器 `jadx-mcp-server` 一起用；面向在**你被授权测试**的应用上做安卓逆向/静态分析（SAST/VAPT）的场景

## 核心结论

**事实**：`jadx-ai-mcp` 是 [JADX 反编译器](https://github.com/skylot/jadx) 的一个插件，直接集成 MCP（Model Context Protocol），为 LLM 提供"实时的逆向支持"。README 的定位口号是：反编译 → 上下文感知的代码评审 → AI 建议，全部实时。它由作者从 JADX 起家（README 注释提到 "Started as Fork"）。

**判断**：它和 [`jadx-mcp-server`](jadx-mcp-server-给JADX反编译接AI的MCP服务器.md) 是一对**配套项目**，同属作者的 "Zin MCP Suite"：插件在 JADX-GUI 内提供 HTTP 服务端并真正操作 IDE 内部模型；MCP 服务器把插件能力翻译成 MCP 工具暴露给 AI 客户端。单装任何一个都跑不起来。而它对 JADX 上游是"扩展"关系——核心反编译逻辑仍归 [@skylot](https://github.com/skylot) 的 JADX。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zinja-coder/jadx-ai-mcp](https://github.com/zinja-coder/jadx-ai-mcp) |
| 描述定位 | "Plugin for JADX to integrate MCP server"，即让 JADX 接入 MCP 服务器的插件 |
| 许可证 | Apache-2.0（仓库 `LICENSE` 文件，约 11,357 字符；README 称继承自上游 JADX 的 Apache 2.0） |
| 主语言 | Java 100.0% |
| 关注度 | 2,994 Star / 11 关注 / 280 Fork（核验日 2026-11-09，来源 ungh.cc 镜像与 GitHub 页面内嵌 JSON） |
| 维护状态 | 2025-04-07 创建；最近 push 2026-10-19；**默认分支 `jadx-ai`（不是 main）**；开放 Issue 3 个 |
| 发行版 | 30 个 Release / 31 tags（README 引导从 releases 下载 `.jar` 插件与 `.zip` 服务器包） |
| 话题标签 | ai, java, llm, mcp, mcp-server, mcp-servers, mobile, model-context-protocol, model-context-protocol-servers, pentesting, reverse-engineering, sast, vapt |

## 怎么用

以下步骤均来自 README，**我未实际执行**：

1. **下载**：从 https://github.com/zinja-coder/jadx-ai-mcp/releases 同时拿到 `jadx-ai-mcp-<version>.jar`（插件）和 `jadx-mcp-server-<version>.zip`（MCP 服务器）。README 明确提示"两个都要下"。
2. **安装插件**（二选一，来自 README 原文）：
   - 一条命令装最新版，无需手动下载 jar：
     `jadx plugins --install "github:zinja-coder:jadx-ai-mcp"`
   - 或用 JADX-GUI 图形界面安装（README 附了多张截图步骤）。
3. **准备 MCP 服务器**：解压 zip 进入 `jadx-mcp-server` 目录；项目用 [uv](https://github.com/astral-sh/uv) 而非 pip 管理依赖。可 `uv venv` 建环境，可选 `uv pip install httpx fastmcp`。
4. **接入 LLM 客户端**：README 给了 Claude Desktop 的配置（Windows `%APPDATA%\Claude\claude_desktop_config.json`、macOS `~/Library/Application Support/Claude/claude_desktop_config.json`），把 `jadx-mcp-server` 作为 MCP server 配进去（`command` 指向 `uv`，`args` 走 `--directory ... run jadx_mcp_server.py`）。另给了 Cherry Studio（stdio）与 LM Studio（mcp.json）两种配置方式。
5. **插件内配置**：可在插件界面里**配置监听端口**、恢复默认端口、强制重启服务器、查看服务器状态；连自定义端口时在服务器侧用 `--jadx-port` 指定（例如 `uv run jadx_mcp_server.py --jadx-port 8652`）。
6. **试用流程**（README "Give it a shot"）：打开 jadx-gui 载入 APK → 启动 Claude，应看到"锤子"符号（工具图标）→ 点击后运行类似 `fetch currently selected class and perform quick sast on it` 的提示词 → 弹窗授权 → 出结果。

README 称该插件可"完全控制 GUI 与内部项目模型"，支持：把选中类导出到 MCP、跑自动化 Claude 分析、把建议以内联方式回传。

## 使用前需要注意

- **必须与 MCP 服务器配套**：插件自身只在本机提供 HTTP 服务端，AI 客户端要接通仍需 `jadx-mcp-server`。只装插件没有可用的 MCP 入口。
- **默认分支特殊**：本仓库默认分支是 `jadx-ai` 而非 `main`，更新与查看文件时容易走错分支（核验日 2026-11-09）。
- **远程绑定风险**：README（服务器侧文档）明确 CAUTION——MCP 服务器用非 localhost 地址时以纯 HTTP、无鉴权暴露全部工具，同网段任何人可读反编译代码、重命名类/方法、访问调试信息，且无 TLS。跨机部署需靠可信隔离网络 / 防火墙 / SSH 隧道。
- **法律/伦理边界**：README 的 Legal Warning 写明本工具仅用于教育、研究与**经授权的**安全评估，需在你拥有或有明确许可的应用上使用；未授权逆向、侵权、恶意活动被明令禁止。工具不设技术护栏，合规自负。
- **反编译能力暴露给 AI 的注意点**：插件让 LLM 能读源码、重命名混淆符号、加注释、读调试栈帧/线程/变量。把它接进 AI 客户端即等于把逆向操作面交给模型与提示词，应只在受控环境、对授权样本使用。
- **测试范围有限**：README 多次标注"Tested on Claude Desktop Client, support for other AI will be tested soon"，即主要验证对象是 Claude Desktop，其它客户端支持程度未充分声明。
- **未验证项**：`rename_class` / `rename_method` / `rename_field` / `rename_package` 等工具只见于本仓库 README 列表，配套仓库 README 未同步列出；实际可用性**未运行验证**。

## 什么时候会再看

- 需要在 JADX-GUI 内一步一步完成"装插件 → 配端口 → 接 AI"时，回来照 README 的截图/命令走。
- 插件与 MCP 服务器版本对不上、端口冲突、或换客户端（Cherry Studio / LM Studio / stdin+http）时，回来查配置。
- 想对比"插件 + 服务器"这套两件套的分工设计，给别的 IDE/工具做类似 AI 集成时参考。

## 相关笔记

- **配套的 MCP 服务器（必须一起用）**：[jadx-mcp-server](jadx-mcp-server-给JADX反编译接AI的MCP服务器.md)——本插件在 JADX-GUI 内执行反编译操作，服务器负责对外讲 MCP 协议，两者通过本地 HTTP 串联。
- **同领域的安卓打包/反编译工具**：[Apktool](Apktool-AndroidAPK反编译与打包工具.md)——JADX 侧重反编译读代码，Apktool 侧重资源解码与重打包，逆向 Android 时常常互补。
- **同类逆向技能路由**：[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)——编排逆向与渗透技能的工具，可与本套 JADX 工作流配合使用。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/zinja-coder/jadx-ai-mcp （`jadx-ai` 分支的 README.md）
- 许可证文件：https://github.com/zinja-coder/jadx-ai-mcp/blob/jadx-ai/LICENSE
- 配套 MCP 服务器仓库：https://github.com/zinja-coder/jadx-mcp-server
- 上游 JADX 反编译器：https://github.com/skylot/jadx
- 文档站点：https://jadx-ai-mcp.readthedocs.io/en/latest/
- 元数据兜底：ungh.cc 镜像（`https://ungh.cc/repos/zinja-coder/jadx-ai-mcp`）与 GitHub 页面内嵌 JSON、shields.io 徽章

以上信息核验于 2026-11-09。
