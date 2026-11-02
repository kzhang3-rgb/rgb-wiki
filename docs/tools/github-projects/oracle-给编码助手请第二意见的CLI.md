# oracle：给编码助手请一个"第二意见"的 CLI

> 一句话记住它：把你要评审的问题 + 选定的项目文件打成一份带行号的上下文，交给另一个模型去评——**关键是它允许你先把"要发出去的东西"完整打印出来核对**。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文与 GitHub API 元数据，未安装未运行）
- 适用范围：需要在项目真实上下文里请"第二个模型"复核的开发者；需自备模型凭证或已登录的浏览器会话

## 核心结论

oracle 的自我定位很克制：**"Bring a second brain, not a second briefing"**。它不是又一个聊天客户端，而是解决一个具体动作——

> 我卡住了，我想让**另一个模型**基于**我这个项目真实文件**给意见。

它的做法是：打包一个 prompt + 你选的文件 → 通过 API 或已登录浏览器发给模型 → 结果存成一次 **session**（可后台跑完、可回看、可追问）。

它把"怎么发"拆成三条路径，这是最值得记的设计：

| 路径 | 什么时候用 | 需要什么 |
| --- | --- | --- |
| **API** | 要可靠自动化，或一次跑多个模型 | 对应厂商的 API Key（OpenAI / Azure / Anthropic / Gemini / xAI / OpenRouter / 兼容端点） |
| **Browser** | 想借用已登录的 ChatGPT 或 Gemini 会话 | 装 Chrome 并完成一次登录 |
| **Render** | 只想自己看 / 复制 / 粘贴这份上下文 | **不需要任何账号或 Key** |

其中 **Render 模式是它最好的安全设计**：`--render` 会把"将要发送的完整 prompt + 编号后的文件内容"本地打印出来，**不联系任何模型**。对"我要把公司代码发给外部模型"这种事，能先看清要发什么再决定发不发，是很实际的护栏。

其它几个工程细节也值得记：

- **上下文可控**：`--file` 接受文件、目录、glob，以及 `!` 排除（`--file "src/**/*.ts" --file "!**/*.test.ts"`）。
- **发送前先预览**：`--dry-run summary --files-report` 打印解析出的文件清单与 token 估算。
- **答案可定位**：生成的文本 bundle 带**稳定行号**，所以回复可以引用 `path:line`。
- **多模型面板**：`--models` 一次跑多个模型，并在一个 session 里记录每个模型的用量、成本、输出与"部分失败"。
- **不泄凭证的体检**：`oracle doctor --providers` 检查各模型是否就绪，而**不打印凭证**。
- **会话可续**：`oracle status --hours 72` 看近期记录，`oracle session` 重连、`oracle restart` 重跑、`--followup` 带更多上下文继续（受具体厂商能力限制）。
- **Agent 集成**：提供 `oracle-mcp` stdio server，也可从仓库安装 Oracle skill，接入 Claude Code / Codex / Cursor 等 MCP 客户端。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [steipete/oracle](https://github.com/steipete/oracle) |
| 描述 | Ask the oracle when you're stuck. Invoke GPT-5 Pro with a custom context and files. |
| 许可证 | MIT（README 与仓库 `LICENSE` 一致） |
| 语言 / 运行环境 | TypeScript；**要求 Node.js 24 或更新** |
| 关注度 | **4,060 Star、428 Fork、10 关注、12 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2025-11-14 创建，最近推送 2026-10-30（很活跃）；`main` 分支 |
| 体积 | 约 91,016 KB（`size` 字段，约 89 MB） |
| 主页 | <https://askoracle.sh> |
| 主题标签 | agents、ai、anthropic、gemini-pro、gpt-5-pro、openai、opus、sonnet |

## 用法要点

安装有两条常见路径：

```bash
# 方式一：Homebrew（macOS / Linux）
brew install steipete/tap/oracle

# 方式二：npm 全局安装
npm install -g @steipete/oracle

# 不安装直接试
npx -y @steipete/oracle --help
```

最推荐的**第一步是先不连模型，只看它会发什么**：

```bash
oracle --render \
  -p "Review the package metadata for release risks" \
  --file package.json
```

确认无误后，去掉 `--render` 就是真正提问（有 OpenAI Key 时默认走 API，否则走浏览器；也可用 `--engine api` / `--engine browser` 显式指定）：

```bash
oracle \
  -p "Audit the model runner for race conditions" \
  --file "src/oracle/**/*.ts" \
  --file "!**/*.test.ts"
```

浏览器模式下有个细节：**单个**文本/源码文件会保持原样上传；**多个**文本/源码文件会打包成一份（纯文本时为扁平文本，含原始文件时或显式 `--browser-bundle-format zip` 时为 ZIP）；图片和文档尽量保持直接附件。

## 使用前需要注意

- **核心风险是"数据出境"**：oracle 存在的意义就是把项目文件发给外部模型。**把私有代码、密钥、内部文档交给第三方模型前，必须确认合规与授权**。建议固定习惯：**先 `--render` 或 `--dry-run` 看清内容再发送**，并用 `!` 排除敏感目录（如 `.env`、凭据、专有算法目录）。
- **浏览器模式是"借用登录态"**：它靠 Chrome 自动化驱动已登录的 ChatGPT / 用 Cookie 的 Gemini 客户端。这类方式的**稳定性与合规性都不由你掌控**（账号条款、界面改版都可能影响），README 也把细节放在专门的 `browser-mode.md` 里，说明有边界。
- **Node.js 24+** 的版本要求较高，老环境需先升级。
- **要自己备凭证**：API 模式需要 OpenAI / Azure / Anthropic / Gemini / xAI / OpenRouter 等任一 Key。
- **会话落在本机**：运行记录存在 `~/.oracle/sessions`。这既是可回看的好处，也意味着**本机磁盘上会留有你发出去的上下文**——共享机器上需注意清理。
- **"第二个模型的意见"不等于正确答案**：它是第二视角，不是权威裁决；多模型面板也会如实记录"部分失败"，说明这不是全绿保证。

## 什么时候会再看

- 需要一个**能在提交前先打印将发送内容**的"请外部模型评审"工具时（这是它相对同类最实用的点）；
- 想给 Claude Code / Codex / Cursor 加一个"请第二个模型复核"的 MCP 能力时；
- 需要参考"一次跑多个模型并记录各自成本/失败"的多模型编排实现时；
- 研究"API 模式 vs 浏览器登录态"两种取数路径取舍时。

## 相关笔记

- [prompt-optimizer：提示词优化器](prompt-optimizer-提示词优化器.md)：同样提供 CLI/服务形态与 MCP，侧重优化与评测 prompt 本身。
- [agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)：oracle 的 agent 集成面向同类"给助手读的说明文件"。
- [zframes：让 Agent 用对话生成行情看板](zframes-让Agent用对话生成行情看板.md)：另一个"通过 skill 接入助手"的形态，可对照接线方式。

## 参考来源

- 仓库主页：<https://github.com/steipete/oracle>
- README（`main` 分支）：<https://raw.githubusercontent.com/steipete/oracle/main/README.md>
- 官方文档站：<https://askoracle.sh>
- GitHub API 元数据：<https://api.github.com/repos/steipete/oracle>（核验日期 2026-11-02）
