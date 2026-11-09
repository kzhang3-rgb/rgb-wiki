# radare2-mcp：给 radare2 接 AI 的 MCP 服务器

> 记录一个用 C 原生接入 radare2 的 MCP 服务器：既能当命令行 MCP 服务，也能注册成 r2 核心插件、在现有 r2 会话里开 HTTP 服务；README 重点讲清了只读模式、令牌鉴权、"yolo/approve"这类危险开关与逐工具配置。第一句先记清三点：**它由 radare2 官方组织 radareorg 维护**（非第三方魔改）；**核心是 C（70.0%）**、MIT；**默认分支 `main`、最新 Release 追踪 r2 6.2.4**。

- 状态：草稿
- 最近核验：2026-11-09（阅读 `main` 分支 README 全文（英文 262 行）、元数据；**未 clone、未编译、未以 r2pm 安装、未运行、未接任何 MCP 客户端**）
- 适用范围：需先有可用的 **radare2**（README 顶部徽章标注 r2 **6.2.4**；部分功能要求 **ABI 91+**）；宿主可以是本地 r2、远程 r2/iaito 会话，或 Docker；客户端支持 stdio 或 HTTP。

## 核心结论

**事实：它是 radare2 官方的 MCP 服务器，用 C 直接调 r2 原生 API。** README 描述为 "MCP stdio server for radare2"，并自述"**Fully written in C using the native r2 APIs**"，目标是让 OpenCode、Mai、VSCode、Claude、CLION 等 AI 客户端直接用 radare2 做二进制分析。

**事实：它有三种运行形态，覆盖面比"单纯 stdio 服务器"更宽。** ① 命令行/带外 MCP 服务（stdio，也支持 `-H` 的 HTTP 模式）；② **r2 核心插件**——在已有的 r2 会话里用 `r2mcp start` 复用当前 `RCore`，让 MCP 工具直接操作该会话已加载的文件与分析状态；③ 通过 **r2pipe** 连接本地或远程的 r2/iaito 会话。

**事实：安全相关开关相当细。** README 列出的能力里包含 **readonly 模式、sandbox lock、限制工具（restrict tools）、逐工具细粒度配置**；HTTP 模式有 Bearer 令牌（`-a` / `-A` 随机生成，或 `R2MCP_AUTH_TOKEN`），插件侧还有 `r2mcp.approve`（主管审批）与 `r2mcp.yolo`（**关闭审批并暴露危险 run 工具**）。

**判断：它更像"给 r2 用户就地补一层 MCP"，而非大而全的工具集。** README 很简短（英文 262 行）、topics 为空、Release 仅十余个；对习惯 radare2 命令行、又想让 AI 参与分析的人来说接入成本较低（一条 `r2pm -Uci r2mcp`）。但"到底暴露了多少工具、每个工具做什么"在 README 里着墨不多，需要读者自己去跑 `search_tools` 或读源码确认——**本笔记未运行，无法给出工具清单。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [radareorg/radare2-mcp](https://github.com/radareorg/radare2-mcp) |
| 描述定位 | 面向 radare2 的 MCP stdio 服务器；C 原生实现，可作 CLI、r2 插件或 HTTP MCP 服务 |
| 许可证 | **MIT**（`LICENSE` 1,063 字符） |
| 主语言 | C（shields.io top 语言 70.0%） |
| 关注度 | **358 Star、43 Fork、2 关注、8 个开放 Issue**（2026-11-09 核验；ungh.cc 镜像同为 358/43） |
| 维护状态 | **2025-03-22 创建**；`pushed_at` 2026-10-15；**默认分支 `main`**；由 radare2 官方组织 `radareorg` 维护 |
| 发行版 | **18 个 Release / 19 个 tag**；README 徽章标注面向 radare2 **6.2.4** |
| 话题标签 | 空（未设置 topics） |

## 用法要点

**（1）最简安装：走 r2pm。**

```bash
r2pm -Uci r2mcp
```

README 说明：`r2mcp` 可执行文件会被装到用户家目录的 r2pm bindir，但**不应直接在 shell 里执行**，它只应由所选语言模型的 MCP handler 拉起（要手动验证可 `r2pm -r r2mcp`）。

**（2）MCP 客户端接入（stdio，通用配置）。**

```json
{
  "mcpServers": {
    "radare2": {
      "command": "r2pm",
      "args": ["-r", "r2mcp"]
    }
  }
}
```

README 另给了 **Claude Desktop**（`claude_desktop_config.json`）、**VS Code / GitHub Copilot Chat**（`servers` + `"type": "stdio"`）、**Zed**（`context_servers`，`"source": "custom"`）三种客户端的具体写法；Codex 用户可执行 `make codex-plugin-install`。

**（3）HTTP 模式与鉴权。** 用 `-H [address:]port` 开 HTTP MCP 服务：**裸端口只绑 `127.0.0.1`**，要对外需显式 `0.0.0.0:port`；对外绑定务必配令牌（`-a <token>`，或 `-A` / `-a random` 启动时随机生成并**只向 stderr 打印一次**，也可用环境变量 `R2MCP_AUTH_TOKEN`）；客户端每个 GET/POST 都要带 `Authorization: Bearer <token>`。**逐会话 HTTP 状态**用 `-X max[:idle_seconds]` 开启，由 `X-Session-ID` 请求头路由——但 `-X` 与 Bearer 鉴权都**要求 radare2 ABI 91 或更新**（旧 ABI 下 `-H` 仍可作单一共享服务器，`-X` 被忽略）。

**（4）r2 核心插件模式。** 在既有 r2 会话里直接开 HTTP MCP：

```bash
r2 -e r2mcp.port=8765 -c 'r2mcp start' /bin/ls
```

子命令：`r2mcp start|http|stop|restart|status`、`r2mcp logs on|off` / `logs file <path>`、`r2mcp approve on|off` / `approve url <url>`、`r2mcp yolo on|off`（**关闭审批并暴露危险 run 工具**）、`r2mcp config`。它以 eval key 形式暴露与服务端同名选项，包括 `r2mcp.port`、`r2mcp.auth`、`r2mcp.approve`、`r2mcp.yolo`、`r2mcp.readonly`、`r2mcp.sandbox`、`r2mcp.sessions*`、`r2mcp.decompiler` 等。注意：**手动用 `-i` 加载插件时，要等插件加载后再设这些 key**（如 `-c 'e r2mcp.port=8765'`），因为 r2 会先处理 `-e` 再初始化插件。

**（5）可选：Docker、原始命令、SQL。** Docker：`docker build -t r2mcp .`，客户端 `command` 用 `docker`、`args` 用 `["run","--rm","-i","-v","/tmp/data:/data","r2mcp"]`。原始能力：可选暴露"直接跑 r2 命令或 r2js 脚本"。SQL：装同版本 radare2 的 [r2xsql](https://github.com/radareorg/r2xsql) 核心插件后，`sql` 工具可用（走一条语句直传 SQL 插件、**支持写操作，因此被排除在只读模式 `-R` 之外**，且远程 r2 HTTP 客户端模式 `-u` 不暴露它）。

**（6）从源码构建。** Linux/macOS 用 `make install`（二进制装到 `/usr/local/bin`）；Windows 按 CI 的方式用 meson + ninja（`meson b` 后 `ninja -C b`）。

## 使用前需要注意

- **把逆向/任意命令能力交给 AI 的前提**：README 提供 `r2mcp.yolo on`（关闭审批、暴露危险 run 工具）——这是"方便"与"风险"的直接权衡点，**默认是否开启、审批如何工作未在本笔记验证**，接入前应自行读源码/配置确认。
- **对外暴露需要令牌**：裸端口只绑本地；一旦用 `0.0.0.0` 就必须配 `-a`/`-A` 令牌。**别在无鉴权情况下把 HTTP MCP 端口开到网络里。**
- **版本/ABI 耦合**：逐会话 HTTP（`-X`）与 Bearer 鉴权要求 **radare2 ABI ≥ 91**；普通 `-H` 在旧 ABI 下退化。使用前需确认 r2 版本与 ABI。
- **只读与写能力要分清**：存在 `-R` / `readonly` 只读模式，但 `sql`、脚本、原始命令等属于写/危险子集，不能指望"只读"覆盖它们。
- **信息密度有限**：README 未列出完整工具清单，topics 为空，**工具面需自行实测**；本笔记**只读 README 与元数据，未 clone、未编译、未运行、未接入任何客户端**。
- **许可**：MIT，属宽松许可；但 radare2 本体及其它插件各自许可需另查。

## 什么时候会再看

- 已经习惯 radare2（尤其 iaito / r2pm 生态）、想让 AI 直接调用现成 r2 会话做分析时；
- 想参考"**MCP 服务器如何同时支持 stdio 与 HTTP、并做逐会话与令牌鉴权**"的实现时；
- 想对比 **radare2 / Ghidra / WinDbg** 三种宿主各自的 MCP 封装（工具面、鉴权默认、运行形态）时。

## 相关笔记

- **同类"给逆向工具接 AI"**：[ghidra-mcp：把 Ghidra 接给 AI 的 MCP 服务器](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)——那是 Java 写的 Ghidra 版 MCP（工具面大得多）；本篇是 C 写的 radare2 版，更轻、更贴近命令行。
- **同类"给调试器接 AI"**：[mcp-windbg：给 WinDbg 接 AI 的 MCP 服务器](mcp-windbg-给WinDbg接AI的MCP服务器.md)——面向 Windows 崩溃转储/内核调试；本篇面向 r2 的静态与动态分析。
- **同层的宿主框架**：[ghidra：NSA 开源的软件逆向工程框架](ghidra-NSA开源的软件逆向工程框架.md)——它是 Ghidra 侧的宿主；radare2 是与之并列的另一套逆向框架，本笔记是其 MCP 接入。
- **同域的上层封装**：[reverse-skill：逆向与渗透技能路由包](reverse-skill-逆向与渗透技能路由包.md)——技能层负责"该用哪个工具"，本篇是被调用的底层能力之一。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README（默认分支 `main`）：https://github.com/radareorg/radare2-mcp
- radare2 本体：https://github.com/radareorg/radare2 ；SQL 扩展插件 r2xsql：https://github.com/radareorg/r2xsql
- 元数据（Star / Fork / 创建与推送时间 / 默认分支 / 发行版）：GitHub 页面内嵌 JSON 与 ungh.cc 镜像 https://ungh.cc/repos/radareorg/radare2-mcp

以上信息核验于 2026-11-09（GitHub API 匿名配额用尽，未取到 `size`）。
