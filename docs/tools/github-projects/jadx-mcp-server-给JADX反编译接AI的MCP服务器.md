# jadx-mcp-server：给 JADX 反编译接 AI 的 MCP 服务器

> 当你想让 Claude、Codex 等 LLM 直接读取 JADX 里反编译出来的安卓代码、查类/方法/清单、甚至改注释时，看这篇；它讲的是这对「JADX 插件 + MCP 服务器」组合里负责对外讲 MCP 的那一半。

- 状态：草稿
- 最近核验：2026-11-09（阅读了 `main` 分支 README.md 全文，并核对仓库元数据；**未安装、未运行、未 clone**）
- 适用范围：Python 侧 MCP 服务器（README 标注 Python 3.10+），需与配套的 JADX 插件 `jadx-ai-mcp` 一起用；面向在**你被授权测试**的应用上做安卓逆向/静态分析（SAST/VAPT）的场景

## 核心结论

**事实**：`jadx-mcp-server` 是一个独立的 Python MCP 服务器，它通过 MCP（Model Context Protocol）对外提供工具，再经由本地 HTTP 请求去驱动一个被改造过的 `jadx-gui`（即配套的 [`jadx-ai-mcp`](jadx-ai-mcp-JADX的AI反编译插件.md) 插件），从而让 LLM 与"反编译后的安卓应用上下文"实时交互（README 原话大意）。README 里给出了一张时序图：LLM 客户端 → MCP Server → JADX 插件 → JADX-GUI，再原路返回。

**判断**：它的定位是这套两件套里的"翻译层/网关"——不自己反编译，只把 JADX-GUI 的能力包装成 MCP 工具暴露给 AI 客户端。要真正跑通，插件与服务器缺一不可。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zinja-coder/jadx-mcp-server](https://github.com/zinja-coder/jadx-mcp-server) |
| 描述定位 | "MCP server for JADX-AI Plugin"，即 JADX-AI 插件的 MCP 服务器 |
| 许可证 | Apache-2.0（仓库 `LICENSE` 文件，约 11,357 字符） |
| 主语言 | Python 91.6% |
| 关注度 | 838 Star / 5 关注 / 136 Fork（核验日 2026-11-09，来源 ungh.cc 镜像与 GitHub 页面内嵌 JSON） |
| 维护状态 | 2025-04-08 创建；最近 push 2026-10-19；默认分支 `main`；开放 Issue 2 个 |
| 发行版 | **0 个 Release / 0 tags**（无正式发行版；README 中的下载链接实际指向配套仓库 jadx-ai-mcp 的 releases） |
| 话题标签 | ai, android-hacking, android-reverse-engineering, application-security, llm, mcp, mcp-server, mcp-servers, mobile-security, model-context-protocol, model-context-protocol-server, python, reverse-engineering, vapt |

## 功能面

README 用 `## Current MCP Tools` 一节列出了服务器对外暴露的 MCP 工具（节选，均为 README 原文所述）：

- 读取类：`fetch_current_class`、`get_selected_text`、`get_all_classes`、`get_class_source`、`get_method_by_name`、`get_smali_of_class`。
- 搜索类：`search_method_by_name`、`search_classes_by_keyword`（支持分页）。
- 结构探查：`get_methods_of_class`、`get_fields_of_class`。
- 清单与资源：`get_android_manifest`、`get_manifest_component`、`get_main_activity_class`、`get_main_application_classes_code` / `get_main_application_classes_names`、`get_strings`、`get_all_resource_file_names`、`get_resource_file`。
- 引用分析（交叉引用，支持分页）：`xrefs_to_class`、`xrefs_to_method`、`xrefs_to_field`。
- 修改类：`rename_variable`、`add_comment`、`list_comments`。
- 调试器类：`debug_get_stack_frames`、`debug_get_threads`、`debug_get_variables`。

**判断/注意**：README 提到，返回反编译源码的工具默认会剥离 Kotlin 编译器元数据（`@Metadata` / `@DebugMetadata`，README 称这类内容常占 Kotlin 转储的 25%–50%），传 `slim=False` 可获得原始转储；剥离不会改变行号，所以 `with_line_numbers=True` 与 `add_comment(line=...)` 仍可用。

此外，README 与本仓库 README 的工具清单略有不一致：本仓库这里只列到 `rename_variable`，而配套插件仓库的 README 还列出了 `rename_class` / `rename_method` / `rename_field` / `rename_package`。这属于两处文档更新不同步，实际以你运行时的版本为准（**此点仅据两篇 README 对比，未运行验证**）。

## 怎么用

以下步骤均来自 README，**我未实际执行**：

1. **安装插件**（在配套仓库那边完成）：一条命令 `jadx plugins --install "github:zinja-coder:jadx-ai-mcp"` 直接装最新插件，或用 JADX-GUI 图形界面安装。
2. **接入 MCP 客户端**（Claude Desktop 示例）——编辑 `claude_desktop_config.json`（Windows 在 `%APPDATA%\Claude\`，macOS 在 `~/Library/Application Support/Claude/`），加入：

```json
{
  "mcpServers": {
    "jadx-mcp-server": {
      "command": "/<path>/<to>/uv",
      "args": ["--directory", "</PATH/TO/>jadx-mcp-server/", "run", "jadx_mcp_server.py"]
    }
  }
}
```

   也可用 `uv tool install git+https://github.com/zinja-coder/jadx-mcp-server` 安装成可执行文件，再把 `command` 直接写成 `jadx_mcp_server`。

3. **其它 LLM 客户端**：README 另给了 Cherry Studio（stdio，command 用 `uv`）与 LM Studio（配置 mcp.json，附视频）。
4. **HTTP 模式**：`uv run jadx_mcp_server.py --http`，可加 `--port`。README 特别提醒：stdio 模式下横幅/健康检查等人类可读输出写到 stderr，stdout 留给 MCP JSON-RPC，以兼容 Codex、Claude Desktop 等 stdio 客户端。
5. **两套连接要分清**（README 强调）：一条是"MCP 服务器监听 LLM 客户端"的连接（`--host` / `--port`，默认 `127.0.0.1:8651`），另一条是"MCP 服务器找 JADX 插件"的连接（`--jadx-host` / `--jadx-port`，默认 `127.0.0.1:8650`）。远端部署时可分别指定。
6. **非 MCP 的 OpenAI 兼容模型**：仓库提供 `examples/openai_compatible_client.py`，把 MCP 工具列表转成 OpenAI function-calling 工具，跑"调用→执行→回灌"循环；支持 `--list-tools` 只打印工具 schema 不调模型。

## 使用前需要注意

- **远程绑定风险（README 明确的 CAUTION）**：使用 `--host 0.0.0.0` 等非 localhost 地址时，服务器会以**纯 HTTP、无鉴权**绑定所有网卡，README 指出同网段任何人可调用全部工具、无 TLS、流量可被截获。缓解建议：仅在可信隔离网络绑定、用防火墙限制端口、或改用 SSH 隧道。`--jadx-host` 指向非本机时同理，会发出未鉴权的 HTTP 请求。
- **法律/伦理边界的自我约束**：README 的 Legal Warning 明确本工具仅用于教育、研究与**经授权的**安全评估，且需在你拥有或有明确许可的应用上使用；未授权逆向、侵权等滥用被明令禁止。工具本身不设技术护栏，合规责任在使用者。
- **反编译能力暴露给 AI 的注意点**：这套工具让 LLM 能读取反编译源码、重命名类/方法/变量、加注释、访问调试信息。把这类能力接入 AI 客户端时，等于把逆向操作面交给了模型与提示词，应确保只在受控环境、对授权样本使用。
- **未验证项**：README 标注"Tested on Claude Desktop"，其它 LLM 支持"might be tested in future"（原文）。文档中"支持 apktool"在 Roadmap 里已勾选，但**我未验证**其具体实现方式与成熟度。
- **无正式发行版**：本仓库 0 Release / 0 tag（核验于 2026-11-09），获取途径主要通过源码运行或配套仓库的 release 包。

## 什么时候会再看

- 想在自己项目里把"本地某 GUI 工具的能力"包成 MCP 工具给 AI 用时，参考它的两层连接设计与 stdio/HTTP 双模式。
- 需要在多机（本机 MCP + 远程 JADX，或 Docker/WSL）之间部署时，回来查 `--host/--port/--jadx-host/--jadx-port` 的取值。
- 想给非 MCP 的 OpenAI 兼容模型接 MCP 工具时，看它的 `examples/openai_compatible_client.py` 适配思路。

## 相关笔记

- **配套的 JADX 插件（必须一起用）**：[jadx-ai-mcp](jadx-ai-mcp-JADX的AI反编译插件.md)——本服务器负责对外讲 MCP，插件负责在 JADX-GUI 内执行反编译操作，两者通过本地 HTTP 串联。
- **同领域的安卓打包/反编译工具**：[Apktool](Apktool-AndroidAPK反编译与打包工具.md)——JADX 侧重反编译读代码，Apktool 侧重资源解码与重打包，逆向 Android 时常常互补。
- **另一条"把反编译器接给 AI"的路线**：[ghidra-mcp](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)——同样是 MCP 服务器，但对接的是 Ghidra，可对比不同反编译器暴露给 AI 的工具设计。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/zinja-coder/jadx-mcp-server （`main` 分支的 README.md）
- 许可证文件：https://github.com/zinja-coder/jadx-mcp-server/blob/main/LICENSE
- 配套插件仓库：https://github.com/zinja-coder/jadx-ai-mcp
- 上游 JADX 反编译器：https://github.com/skylot/jadx
- 元数据兜底：ungh.cc 镜像（`https://ungh.cc/repos/zinja-coder/jadx-mcp-server`）与 GitHub 页面内嵌 JSON、shields.io 徽章

以上信息核验于 2026-11-09。
