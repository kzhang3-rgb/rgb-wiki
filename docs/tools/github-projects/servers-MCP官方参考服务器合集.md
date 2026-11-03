# servers：MCP 官方参考服务器合集

> 记录它已经"瘦身"成只留官方参考实现这件事——找 MCP 服务器清单应该去 Registry，不是这里。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README 与 GitHub API 元数据，未安装、未启动任何服务器）
- 适用范围：MCP 客户端（Claude Desktop 等）；TypeScript 服务器用 `npx`，Python 服务器用 `uvx` 或 `pip`

## 核心结论

Model Context Protocol（MCP）的**官方参考实现合集**，由 MCP steering group 维护、Anthropic 代管。定位是"演示 MCP 功能与官方 SDK 用法"，不是可直接投产的服务器集合。

**最重要的一条信息是"这个仓库已经不再是大清单了"**：README 顶部用 IMPORTANT 块明确说明——如果你要找 MCP 服务器列表，请去 **MCP Registry**（`registry.modelcontextprotocol.io`）；**本仓库只保留一小批官方维护的参考服务器**。大量网上教程引用的 `@modelcontextprotocol/server-github`、`server-postgres` 等，如今都已归档到 `servers-archived` 仓库。**这一条能直接避免"照着旧教程装服务器、结果装到已归档版本"的坑。**

当前保留的 7 个参考服务器：

| 服务器 | 用途 |
| --- | --- |
| **Everything** | 参考/测试服务器，同时含 prompts、resources、tools |
| **Fetch** | 抓取网页内容并转成便于 LLM 使用的格式 |
| **Filesystem** | 带可配置访问控制的安全文件操作 |
| **Git** | 读取、搜索、操作 Git 仓库 |
| **Memory** | 基于知识图谱的持久记忆系统 |
| **Sequential Thinking** | 通过思考序列做动态、反思式的问题拆解 |
| **Time** | 时间与时区转换 |

已归档（迁移至 [servers-archived](https://github.com/modelcontextprotocol/servers-archived)）的包括：AWS KB Retrieval、Brave Search（已由 Brave 官方服务器 [替代](https://github.com/brave/brave-search-mcp-server)）、EverArt、GitHub、GitLab、Google Drive、Google Maps、PostgreSQL、Puppeteer、Redis、Sentry、Slack（现由 Zencoder 维护）、SQLite。

README 还带一个 WARNING：**这些服务器是参考实现、是给开发者学习怎么自己写 MCP 服务器的教育示例，不是生产级方案**，使用者需要根据自己的威胁模型评估安全需求并自行加上防护。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) |
| 维护方 | MCP steering group 维护，Anthropic 代管，社区共建 |
| 许可证 | 新贡献 Apache-2.0，既有代码 MIT（双许可说明见 LICENSE） |
| 语言 | TypeScript 为主（Python 服务器以 `uvx` / `pip` 分发） |
| 关注度 | 91,740 Star、11,902 Fork、629 关注、38 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2024-11-19 创建，最近推送 2026-10-29 |
| 相关 SDK | C#、Go、Java、Kotlin、PHP、Python、Ruby、Rust、Swift、TypeScript 共 10 个官方 SDK |

> 两个数据口径说明：① API 的 license 返回 `NOASSERTION`，是因为 LICENSE 是"新贡献 Apache-2.0 + 既有代码 MIT"的双许可表述，**不是没有许可证**；② API 的 `open_issues_count` 会把开放 PR 计入，通常大于页面 Issues 标签页显示的条数。

## 用法要点

**TypeScript 服务器用 npx，Python 服务器用 uvx**（推荐）或 pip：

```sh
npx -y @modelcontextprotocol/server-memory
uvx mcp-server-git
pip install mcp-server-git && python -m mcp_server_git
```

**单独启动 stdio 服务器会"看起来卡住"，这是正常的**：README 专门解释了传输方式（transport）的区别——

| 传输方式 | 服务器怎么跑 | 客户端怎么连 |
| --- | --- | --- |
| **stdio** | 由客户端把它作为子进程拉起，通过 stdin/stdout 通信，**不开端口** | 在客户端配置里写 `command` + `args` |
| **Streamable HTTP** | 自己作为 HTTP 服务常驻，可服务多个客户端 | 填一个 URL，如 `http://localhost:3001/mcp` |

**本仓库所有服务器都提供 stdio，只有 Everything 同时提供 Streamable HTTP**（其 README 也覆盖了已废弃的 HTTP+SSE 传输）。所以在终端直接跑 `uvx mcp-server-git`，它不监听端口、最多打一行启动提示然后就像挂住了——那是在等客户端从 stdin 发消息。

Claude Desktop 配置示例（注意 **Windows 下要用 `cmd /c` 包一层 `npx`**，`uvx` 的条目不用改）：

```json
{
  "mcpServers": {
    "memory": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "@modelcontextprotocol/server-memory"]
    },
    "git": {
      "command": "uvx",
      "args": ["mcp-server-git", "--repository", "path/to/git/repo"]
    }
  }
}
```

**不配客户端也能试服务器**：用 [MCP Inspector](https://github.com/modelcontextprotocol/inspector)——给 stdio 服务器的命令它会帮你拉起，给 URL 它连运行中的 Streamable HTTP 服务器：

```sh
npx @modelcontextprotocol/inspector uvx mcp-server-git
npx @modelcontextprotocol/server-everything streamableHttp
npx @modelcontextprotocol/inspector --transport http --server-url http://localhost:3001/mcp
```

## 使用前需要注意

- **别把它当服务器清单用**：清单去 MCP Registry；本仓库只有 7 个官方参考服务器。**找第三方服务器时也不要盲信旧文章里的包名**，先确认那个服务器是不是已被归档或被官方/厂商版本取代（Brave Search 与 Slack 就是两种典型：前者被官方替代，后者转由第三方维护）。
- **参考实现 ≠ 生产可用**：这是 README 自己的 WARNING。尤其 Filesystem 的"可配置访问控制"与各服务器的凭据处理，**必须按自己的场景重新评估**，不要直接把示例配置丢进生产。
- **stdio 服务器的"卡住"是设计如此**，不是故障；排查"服务器没反应"时先确认这个前提，再用 MCP Inspector 验证。
- **npm 供应链风险**：`npx -y` 会直接拉取并执行远端包，**不带 `-y` 时确认包名、或锁定版本更稳妥**；这类"一键执行"的调用方式在企业环境里通常需要单独审批。
- **许可证是双许可**：如果只看了 API 的 `NOASSERTION` 就以为"没有许可证"，会得出相反结论——**遇到 `NOASSERTION` / `Other` 时应打开 LICENSE 文件确认**（本站 `KHunter` 笔记遇到过同类误判）。
- **我只读了 README，没有安装或启动任何服务器**：可用性、各服务器实际行为、SDK 版本兼容性均未验证。

## 什么时候会再看

- 需要**给 MCP 客户端加一个基础能力**（文件、Git、抓网页、时间、记忆、思考链）时：先用官方参考实现验证，再决定是否换第三方。
- 想**自己写一个 MCP 服务器**时：这 7 个就是官方给出的 SDK 用法样本，其中 Everything 覆盖 prompts / resources / tools 三种能力，最适合当前对照。
- 需要**排查"服务器启动了但没反应"**时：先看 stdio 与 Streamable HTTP 的区别这张表。
- 想参考**一个超高关注度仓库如何主动"瘦身"并引导用户去新入口**时：README 顶部的 IMPORTANT 块与归档列表是很好的写法样本（用一次显式声明替代无边界的清单维护）。

## 相关笔记

- **应用自带 MCP Server 的例子**：[boxplayer：聚合网盘与影视的全平台播放器](boxplayer-聚合网盘与影视的全平台播放器.md)——其 `clouddrive-cli` 可作 MCP Server 供 AI 客户端调用，并内置 dry-run 预览与操作记录；可作为对照官方参考服务器看"业务型 MCP"长什么样。
- 索引位置：[GitHub 项目收藏](README.md)。
- 同属 AI 助手生态的笔记：[deepseek-harness](deepseek-harness-DeepSeek开源Agent框架.md)（Agent 框架）、[dsh-plugins-store](dsh-plugins-store-DSH插件市场.md)（插件市场，涉及插件校验逻辑）、[openclaw](openclaw-自托管个人AI助手.md)（自托管助手，涉及可替换的插件与执行器）。
- 涉及 MCP 接入的实践笔记：[AiToEarn](AiToEarn-AI内容变现与多平台发布.md)。
- **自带 MCP server 的具体应用**：[newsnow](newsnow-实时热点新闻聚合阅读器.md)（热榜聚合阅读器，对外提供 MCP server 当信息源）——与本篇"官方参考服务器清单"分别对应"清单/样例"与"实际落地的第三方服务器"。
- **厂商官方出品的生产级服务器**：[playwright-mcp](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md)（微软的浏览器自动化 MCP 服务器）——可对照本篇"参考实现"与"生产可用"的区别。

## 参考来源

- 项目仓库与 README：https://github.com/modelcontextprotocol/servers
- 已归档服务器：https://github.com/modelcontextprotocol/servers-archived
- 服务器清单（官方 Registry）：https://registry.modelcontextprotocol.io/
- 协议文档与传输层说明：https://modelcontextprotocol.io/
- MCP Inspector：https://github.com/modelcontextprotocol/inspector
- 元数据（Star / Fork / 时间 / 许可证状态）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未安装、未启动任何服务器，各服务器实际行为与安全性均未验证。
