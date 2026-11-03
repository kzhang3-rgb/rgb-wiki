# playwright-mcp：给 LLM 用的浏览器自动化 MCP 服务器

> 记录微软官方出的这个 MCP 服务器——让 LLM 通过 Playwright 驱动浏览器，靠**无障碍树快照**（不是截图）与页面交互，因此不需要视觉模型。第一句先记清两件事：**它自己的 README 建议编码 Agent 别用它**（改用 CLI+SKILLS，更省 token）；**核心源码已经搬进 Playwright 主仓库**，本仓库现在更像 `@playwright/mcp` 这个 npm 包的发布页与文档入口。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README（经 jsDelivr 取 `@main`）、`CONTRIBUTING.md`、Release 说明、目录树与 GitHub API 元数据；**未安装、未接入任何 MCP 客户端、未驱动浏览器、未审计源码**）
- 适用范围：Node.js 18+；任意 MCP 客户端（VS Code、Cursor、Claude Code、Codex、Copilot、Windsurf、Goose、Grok、Junie 等 20 余种）

## 核心结论

一点背景：它把 Playwright 的浏览器自动化能力包装成一个 **MCP server**，装法就是 `npx @playwright/mcp@latest`。LLM 通过无障碍树快照"看"页面并操作，绕开了截图加视觉模型那条路。

三点必须先看清：

**① 它自己的文档建议编码 Agent 不要用它——这是本条目最值得记的一点。** README 专门写了一节 "Playwright MCP vs Playwright CLI"：对**编码 Agent**而言，**CLI + SKILLS** 更省 token，因为 CLI 调用不必把庞大的工具 schema 和啰嗦的无障碍树塞进上下文；而 **MCP 仍适合**需要持久状态、需要反复迭代推理页面结构的场景——探索式自动化、自愈测试、长跑自主流程。**项目自己把取舍写进了首页，这在同类工具里很少见**，也说明"选 MCP 还是选 CLI"要按用途定，不是越新越好。

**② 源码已经搬走了。** `CONTRIBUTING.md` 开头就是一条警告："**Playwright MCP 的核心已移至 Playwright 主仓库**"。我核验了目录树：本仓库 `src/` 下**只剩一个 281 B 的 README**，真正代码与测试都在 `microsoft/playwright` 里（`packages/playwright-core/src/tools/mcp`、`tests/mcp`）。也就是说，**这个仓库现在主要承担 npm 包发布、文档、Issue 入口与 CI**——想读源码或提 PR，要去主仓库。

**③ 抓的是无障碍树，不是像素。** Key Features 三条：快而轻、对 LLM 友好（不需要视觉模型）、工具调用确定性高（避开截图方案的歧义）。这决定了它适合结构化操作，而不是"看图理解"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) |
| 描述 | "Playwright MCP server" |
| 许可证 | **Apache-2.0**（`LICENSE` 11,552 B） |
| 主语言 | TypeScript |
| 关注度 | **39,106 Star、3,372 Fork、149 关注、1 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2025-03-21 创建；`pushed_at` **2026-11-01**；默认分支 `main` 最新提交 `ec572ee`「devops(bots): publish the agent's GitHub writes as typed calls (#1817)」为 **2026-11-01**（Pavel Feldman，**PGP 签名验证通过**），次新提交由 `microsoft-playwright-automation[bot]` 于 10-31 提交 → **极高活跃**（近一周内多次提交） |
| 体积 | 约 1.9 MB（API `size` 1,963 KB） |
| npm 包 | https://www.npmjs.com/package/@playwright/mcp （README 所填 homepage） |
| Topics | 仅 `mcp`、`playwright` |
| 版本节奏 | 最新 **v0.0.84**（2026-10-31，**作者是个人账号 `pavelfeldman`**，非 bot）；上一版 v0.0.83（2026-09-28） |
| AI 协作痕迹 | 根目录有 **`.claude/` 与 `CLAUDE.md`（3,935 B）**；CI 含 `microsoft-playwright-automation[bot]`；最新提交信息本身就是在讲"把 agent 的 GitHub 写操作改成 typed calls 再发布"→ 项目自身大量使用 Agent |
| 其他文件 | `.azure-pipelines/`、`.devcontainer/`、`SECURITY.md`、`server.json`（MCP 服务器清单）、`Dockerfile`、`cli.js`、`config.d.ts` |

> **关于"只有 1 个开放 Issue"**：39,106 Star 的仓库只剩 1 个开放 Issue，并不代表没人提问——Playwright 系列的 Issue 大多**集中到主仓库 `microsoft/playwright`** 管理与关闭，`CONTRIBUTING.md` 也写明贡献需先在那边发 Issue 并认领。看到这个数字别误判成"这项目没人管"。

## 它能做什么

**装法**：标准配置就一段 JSON——

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}
```

README 为 20 多个客户端分别给了装法（含 `claude mcp add playwright npx @playwright/mcp@latest`、`codex mcp add`、`/mcp add`、VS Code CLI `code --add-mcp`，以及 Cursor / Goose / LM Studio / Kiro 的一键 deep link）。

**能力概览**

- 用 `browser_snapshot` 取无障碍树，再配合点击、填表、标签页、cookie、网络请求、控制台消息、等待等 `browser_*` 工具族与页面交互。
- 可选能力用 `--caps` 打开：`vision`、`pdf`、`devtools`；另有 `network`（路由）、`storage`（cookie/存储）等另需显式开启。
- **v0.0.84 的新东西**：**一个浏览器里跑多个独立会话**（`browser_tabs` 的 `isolatedContext`，用来隔离多账号的 cookie 与 storage）；**URL 运行时参数脱敏**（`--redact-url-params=access_token,code`）；`browser_find` 可限制返回条数；`browser_network_requests` 支持 `all: true` 跨导航查看历史请求；元素 `target` 现在也接受 JavaScript / Python / Java / C# 定位器。
- 也支持连到**已在运行的浏览器**（`--extension` 需装 Playwright 扩展，或 `--cdp-endpoint` / `--endpoint`）。

## 使用前需要注意

**① 它说的"脱敏"不是安全边界。** v0.0.84 的 `--redact-url-params` 与既有的 `secrets` 机制，README **原文写明"是便利功能，不是安全特性"（a convenience, not a security feature）**——别拿它当护栏用。

**② 默认只绑 localhost，但源站过滤也不构成边界。** `--allowed-hosts` 默认只允许 localhost、回环 IP 与 `--host` 指定的主机；文档同时提醒 `--allowed-origins` / `--blocked-origins` **不构成安全边界、也不影响重定向**。要绑 `0.0.0.0` 之前先想清楚暴露面。

**③ 文件访问默认受限，但一个开关就全放开。** 默认只能访问工作区根目录（无 roots 时为 cwd），且**禁止 `file://` 导航**；而 `--allow-unrestricted-file-access` 会**同时**放开"工作区外的文件"与"`file://` URL"。

**④ 它等于把浏览器交给模型。** 工具族里有 `browser_run_code_unsafe`（可执行代码），加浏览器的登录态、Cookie 与网络请求——**接给 Agent 时应按"把浏览器控制权交给模型"来评估风险**，不要只看它"不需要视觉模型"这一点。

**⑤ 选它之前先想清楚是不是该用 CLI。** 按它自己的说法：编码 Agent 场景官方更推荐 [microsoft/playwright-cli](https://github.com/microsoft/playwright-cli)（CLI + SKILLS）；MCP 留给需要持久浏览器上下文的探索式任务。

**⑥ 未安装、未接入客户端、未驱动浏览器、未审计源码**——且源码也不在本仓库。

## 什么时候会再看

- 要给 Agent 接浏览器自动化，尤其是需要**持久浏览器上下文、探索式操作或自愈测试**时。
- 需要对照"**MCP vs CLI + SKILLS**"该怎么选、按什么标准权衡 token 成本时。
- 想参考"官方项目如何把核心搬进主仓库、把本仓库收敛为发布 + 文档 + Issue 入口"这种仓库分层做法时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **MCP 生态的位置**：[servers](servers-MCP官方参考服务器合集.md) 是**官方参考服务器清单**（清单与样例），本篇是**微软官方出品的浏览器自动化服务器**（实际落地的厂商服务器）——两者可对照"参考实现"与"生产可用"。
- **同为"让程序操作浏览器 / 读站点"，路线不同**：[bb-sites](bb-sites-把网站变成命令行指令的适配器集.md) 是**在你已登录的浏览器会话里执行适配器**（不碰 Cookie、不绕反爬），本篇是**让 LLM 直接接管浏览器**（自起浏览器或经 CDP 连上去）——一个"复用登录态"，一个"拿到控制权"。
- **同为"提供 MCP server"，暴露的东西不同**：[newsnow](newsnow-实时热点新闻聚合阅读器.md) 通过 MCP 暴露**热榜数据**，本篇通过 MCP 暴露**浏览器自动化能力**——可对照"给 Agent 送数据"与"给 Agent 一双会点鼠标的手"。
- **形态对照（MCP vs SKILL）**：[reverse-skill](reverse-skill-逆向与渗透技能路由包.md) 是把能力打包成 **SKILL 技能路由包**；本篇 README 亲自讨论了 MCP 与 CLI+SKILLS 的取舍，两篇合看能看清"同一份能力用哪种形态交付"。

## 参考来源

- 项目仓库与 README：https://github.com/microsoft/playwright-mcp
- npm 包：https://www.npmjs.com/package/@playwright/mcp
- 官方推荐的编码 Agent 替代路径（CLI + SKILLS）：https://github.com/microsoft/playwright-cli
- 源码所在地（Playwright 主仓库）：https://github.com/microsoft/playwright
- 官网与 MCP 客户端安装指南：https://playwright.dev 、https://modelcontextprotocol.io
- 元数据（Star / Fork / 时间 / 许可证 / 体积 / Release / 提交）：GitHub REST API，2026-11-03
