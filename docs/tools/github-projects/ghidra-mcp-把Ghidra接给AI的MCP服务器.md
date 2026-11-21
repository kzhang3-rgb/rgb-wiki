# ghidra-mcp：把 Ghidra 接给 AI 的 MCP 服务器

> 记录一个把 NSA 开源逆向框架 Ghidra 暴露成 MCP 工具、让 AI 直接读改反汇编结果的服务器：自述 219 个 agent 可见工具、GUI 插件与无头服务两种形态，README 明确写了"默认只监听 localhost、默认关闭脚本执行"的安全取舍。第一句先记清三件事：**它是 Ghidra 的"外挂"（宿主必须是 Ghidra 12.2）**；**默认分支是 `dev`（不是 main）**；**Apache-2.0，但衍生自 LaurieWired/GhidraMCP**。

- 状态：草稿
- 最近核验：2026-11-09（阅读默认分支 `dev` 上的 README（约 8.8 万字符，**只读前若干节与章节标题列表，未通读全文**）、README 内的 License 说明与元数据；**未 clone、未安装 Ghidra 扩展、未构建、未运行、未接任何 MCP 客户端**）
- 适用范围：需要 **Ghidra 12.2**、**Java 25 LTS**（从源码构建另需 Maven 3.9+）、Python 3.10+（推荐 uv）；扩展"只能在它所构建的确切 Ghidra 版本里加载"；面向本机单人工作站。

## 核心结论

**事实：它是一个 MCP 服务器，把 Ghidra 的逆向能力包装成工具给 AI 用。** README 自述"**219 MCP tools**"（其 Production Status 表又写 220 条目录项 / 219 个 agent 可见工具），覆盖反编译、调用图、交叉引用、结构体/枚举创建、字符串提取、P-code 模拟、以及（v5.4.0 起）实机调试器。形态分两种：**随 Ghidra 一起跑的 GUI 插件**，与**不开 Ghidra 界面的无头服务器**（可 Docker、面向 CI/CD）。

**事实：默认是"只给本机、默认不放行脚本"。** README 的 Security 一节明确写 "GhidraMCP is designed for **localhost-only development**"，默认绑定 `127.0.0.1`、无鉴权；若要暴露到回环之外必须先配 `GHIDRA_MCP_AUTH_TOKEN`（**服务器拒绝在没有 token 时绑定非回环地址**）；`/run_script_inline`、`/run_ghidra_script` 这类"在 Ghidra 进程里执行任意 Java"的端点**自 v5.4.1 起默认关闭**（需 `GHIDRA_MCP_ALLOW_SCRIPTS=1` 才开）。

**判断：它的差异化在"工具数量与写入/动态能力"，不是"最轻量"。** 作者自称 219 个工具约是竞品的 3 倍，并提供批处理（自称减少 93% 调用）、P-code 模拟、TraceRmi 实机调试等写/动态操作；代价是依赖链更重（Ghidra 12.2 + Java 25 + 一个 Python 桥接 wheel），且 README 体量庞大、迭代很快。**这些能力是否名副其实，本笔记未运行验证。**

**事实：版本通道分三档。** README 明说：**打 tag 的 Release（如 7.0.0、7.1.0）是稳定版**，Release Candidate 与 `main` 是 beta（"shapes can still change"），**`dev` 是实验分支，工具会被自由合并/改名/重构，"build nothing on its API"**——而仓库的默认分支恰好就是 `dev`。（判断：想用稳定形态应取 Release，而不是直接跟默认分支。）

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [bethington/ghidra-mcp](https://github.com/bethington/ghidra-mcp) |
| 描述定位 | Ghidra MCP Server——200+（自述 219）个面向 AI 逆向的 MCP 工具；GUI 插件 + 无头服务器、惰性工具加载、命名约定强制、批处理、Ghidra Server 集成与 Docker 部署 |
| 许可证 | **Apache-2.0**（README "📄 License" 一节明写 Apache License 2.0；`LICENSE` 11,442 字符）；**衍生自 [LaurieWired/GhidraMCP](https://github.com/LaurieWired/GhidraMCP)**（2025 年 8 月），版权归属见仓库 `NOTICE` |
| 主语言 | Java 69.4%（shields.io top 语言） |
| 关注度 | **6,166 Star、382 Fork、25 关注、18 个开放 Issue**（2026-11-09 核验；ungh.cc 镜像同为 6,166/382） |
| 维护状态 | **2025-08-30 创建**；`pushed_at` 2026-11-08（活跃）；**默认分支 `dev`**（不是 main） |
| 发行版 | **40 个 Release / 54 个 tag**；README 示例使用 v7.0.0（扩展 `GhidraMCP-<version>.zip` + 桥接 wheel `ghidra_mcp_bridge-<version>-py3-none-any.whl`） |
| 话题标签 | ai、binary-analysis、ghidra、ghidra-extension、java、mcp、mcp-server、model-context-protocol、python、reverse-engineering、static-analysis |

## 用法要点

**（1）两条安装路线：装 Release，或从源码构建。**

- **装现成 Release（无需 clone、无需构建）**：在 Ghidra 项目窗口 **File > Install Extensions** 选下到的 `GhidraMCP-<version>.zip` 并重启；在 **File > Configure > Utility > Configure** 勾选 **GhidraMCPPlugin**；再用 `uv tool install <发布的 wheel 链接>` 装 Python 桥接。装完可用 `curl http://127.0.0.1:8089/check_connection` 自检，预期返回类似 `{"status":"ok","server_kind":"gui","version":"7.0.0"}`。
- **从源码构建**：前置为 **Java 25 LTS、Maven 3.9+、Ghidra 12.2、Python 3.10+（uv）**；用 `python -m tools.setup preflight / ensure-prereqs / build / deploy` 一条龙（`deploy` 会重启 Ghidra 并跑 schema 冒烟检查）。

**（2）MCP 客户端接入（stdio，README 给出的最小配置）。** 关键点：**`command` 必须写绝对路径**（README 明确警告：用裸命令名会因 GUI/服务进程看不到 shell PATH 而 `spawn ENOENT`）：

```json
{
  "mcpServers": {
    "ghidra-mcp": { "command": "/home/<you>/.local/bin/bridge-mcp-ghidra" }
  }
}
```

Windows 写作 `"command": "C:\\Users\\<you>\\.local\\bin\\bridge-mcp-ghidra.exe"`（用 `where.exe bridge-mcp-ghidra` 查路径）。桥接还支持 `--transport streamable-http`（web 客户端）等形态，旧的 `sse` 传输已标记为废弃。

**（3）惰性工具加载是默认（README 标为 issue #440）。** 一次性广播全部 ~220 个端点会超出某主流厂商的 schema 限制（README 点名 Gemini 直接返回 `400 INVALID_ARGUMENT`）。因此桥接默认只在连接时加载 `listing,function,program` 三组（约 70 个端点 + 8 个静态工具），其余由模型通过 `search_tools`/`load_tool_group` 按需拉取；**若客户端不处理 `tools/list_changed`，需用 `--no-lazy` 或 `GHIDRA_MCP_LAZY=0` 关闭惰性加载**。

**（4）可写与动态能力。** README 列出：函数重命名/改类型/加注释、结构体与枚举创建、Ghidra 脚本执行（**默认关闭**，见安全项）、P-code 模拟（v5.4.0）、实机调试器集成（16 个 `/debugger/*` 端点，走 TraceRmi，**仅 GUI 插件**；Windows PE 用 dbgeng、其它用 gdb/lldb），以及把 22 个 `debugger_*` 工具代理到外部调试器服务器（需显式开启）。

**（5）命名"约定强制"是产品特性。** v5.0 起把命名/类型规范做进工具层，分 **Auto-fix（静默改） / Warn（放行并告警） / Reject（直接拒绝）** 三档，可用 Ghidra 的 **Edit > Tool Options > GhidraMCP HTTP Server > Strict Naming Enforcement** 关闭——README 说明该开关仅在服务器启动/重启时读取。

**（6）无头 / Docker。** 提供 `mvn clean package -P headless` 构建的无头服务器与 `docker/` 下的 compose；README 记 GUI 端点约 216 个、无头端点约 201 个。注意其 jar 不含 Ghidra，需把 Ghidra 的 Framework/Features/Processors jar 一并放进 classpath。

## 使用前需要注意

- **默认安全面并不大，但要看清前提**：默认只监听 `127.0.0.1` 且无鉴权（适合可信单人机）；**只要绑定到非回环地址，服务器就要求先配 `GHIDRA_MCP_AUTH_TOKEN`**，否则拒绝启动。把端口暴露出去前，务必同时评估 `GHIDRA_MCP_FILE_ROOT`（限制文件路径端点，防目录穿越）。
- **脚本执行端点默认关闭是有意为之的破坏性变更（v5.4.1）**：`/run_script_inline`、`/run_ghidra_script` 会在 Ghidra 进程里跑任意 Java；旧版本曾默认开启（README 称"prior default was unsafe"）。需要时再显式 `GHIDRA_MCP_ALLOW_SCRIPTS=1`。
- **版本/分支耦合**：扩展"只能在它所构建的确切 Ghidra 版本里加载"；README 提示 Ghidra 12.2 提升了项目数据库版本，用 12.2 存过的程序可能无法在更早的 Ghidra 里打开，升级共享部署前应先读 Ghidra 的 `WhatsNew` 并备份。
- **默认分支是 `dev`（实验）**：README 原话是"build nothing on its API"；要稳定形态请取打过 tag 的 Release，而不是直接跟 `dev`。
- **许可与来源**：Apache-2.0，但衍生自 `LaurieWired/GhidraMCP`，使用/分发时留意 `NOTICE` 的归属要求。
- 本笔记**只读 README、许可说明与元数据，未 clone、未构建、未安装扩展、未运行、未接入任何 MCP 客户端**；文中工具数、版本号、性能数字均为 README 自述。

## 什么时候会再看

- 想让 AI 直接操作 Ghidra（读反汇编、批量改名/加注释、跑 P-code 模拟或对接实机调试）时；
- 想参考"**给逆向工具接 MCP 时如何做安全默认**"（默认 localhost、非回环必须带 token、脚本执行默认关、路径根限制）的做法时；
- 想比较 **Ghidra / radare2 / WinDbg** 三种宿主各自的 MCP 封装差异时。

## 相关笔记

- **它接入的宿主**：[ghidra：NSA 开源的软件逆向工程框架](ghidra-NSA开源的软件逆向工程框架.md)——本篇是其 MCP 外挂，宿主必须先装 Ghidra 12.2。
- **同类"给逆向工具接 AI"**：[radare2-mcp：给 radare2 接 AI 的 MCP 服务器](radare2-mcp-给radare2接AI的MCP服务器.md)——那是 C 写的 radare2 版 MCP；本篇是 Java 写的 Ghidra 版，宿主与工具面不同。
- **同类"给调试器接 AI"**：[mcp-windbg：给 WinDbg 接 AI 的 MCP 服务器](mcp-windbg-给WinDbg接AI的MCP服务器.md)——那是面向 Windows 崩溃转储/内核调试的 MCP；本篇侧重静态反汇编与（可选）实机调试。
- **同域的上层封装**：[reverse-skill：逆向与渗透技能路由包](reverse-skill-逆向与渗透技能路由包.md)——技能层负责"该用哪个工具"，本篇是被调用的底层能力之一。
- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"把逆向能力交给 AI Agent"，粒度不同**：[universal-modder：把改任何 PC 游戏做成一整套 Agent 技能](universal-modder-把改任何PC游戏做成一整套Agent技能.md)——它把逆向**编进 Agent 技能与引擎手册**、按需调用 Ghidra；本篇是**把 Ghidra 单独接成 MCP**。一个偏"**用技能编排整个工作流**"，一个偏"**暴露单个工具给任意客户端**"。
- **同为"把逆向能力接给 Agent"，接的深度不同**：[REA：用 Agent 把软件从行为逆向到原生二进制](rea-用Agent逆向软件行为与原生二进制.md)——那篇自带本地 MCP，把 Ghidra 当作众多 provider 之一（还含 IDA / Kuna / Hopper 与大量内置静态解析）；本篇只服务 Ghidra 一个宿主。可对照"**单工具深接**"与"**多引擎总闸**"。
- **路线对照（MCP 的替代方案）**：[IDA-NO-MCP：把 IDA 反编译结果导出成源码文件交给 AI 的免 MCP 工具](IDA-NO-MCP-把IDA反编译结果导出成源码交给AI的工具.md)——那是**反 MCP** 思路：不做实时工具调用，而是把 IDA 反编译结果**一次性导出成源码工作区**（含 `AGENTS.md` 导航上下文）让 AI IDE 自己去索引；本篇是把 Ghidra **实时接成 MCP**（自述 219 个工具）。可对照"**实时工具调用**"与"**一次性导出 + IDE 索引**"两种交付形态，以及各自对宿主（Ghidra vs IDA）与平台构建的依赖。

## 参考来源

- 项目仓库与 README（默认分支 `dev`）：https://github.com/bethington/ghidra-mcp
- 安全说明（README "🔒 Security" 一节）与 License 一节：同仓库 README
- 元数据（Star / Fork / 创建与推送时间 / 默认分支）：GitHub 页面内嵌 JSON 与 ungh.cc 镜像 https://ungh.cc/repos/bethington/ghidra-mcp

以上信息核验于 2026-11-09（GitHub API 匿名配额用尽，未取到 `size`）。
