# agents.md：给编码助手用的 AGENTS.md 文件格式

> 它不是工具，而是一份"给 AI 编码助手的 README"约定：在项目里放一个 `AGENTS.md`，把构建、测试、提交规范写进去，让编码助手不用每次重新摸索。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文与 GitHub API 元数据，未克隆、未运行站点）
- 适用范围：面向支持 AGENTS.md 约定的 AI 编码助手（Codex 等）；格式本身与语言、框架无关

## 核心结论

这个仓库主张一件事：**给 AI 编码助手也准备一份 README**。

它的定位是"AGENTS.md 之于编码助手，相当于 README 之于人类"——一个**固定、可预期的位置**，用来放项目上下文与协作指令。README 用一句话概括格式，然后给出一个最小示例。

值得注意的是，**这个仓库的 README 本身很短（约 2,000 字符）**：真正的内容是**格式约定 + 一个 Next.js 官网**，而不是一套工具或库。仓库里能读到的东西，基本就是"格式长什么样"和"怎么把官网跑起来"两件事。

README 给出的最小示例把内容分成三类：

| 章节 | 放什么 |
| --- | --- |
| `Dev environment tips` | 开发环境与仓库导航技巧（示例里是 pnpm / turbo 的用法） |
| `Testing instructions` | 怎么跑 CI、怎么跑测试、改动后要补什么 |
| `PR instructions` | 提交标题格式、提交前要跑哪些检查 |

示例里的内容都是**具体到命令**的（例如 `pnpm turbo run test --filter <project_name>`），而不是"记得写测试"这类空话——这提示了该格式真正想解决的问题：**把口口相传的仓库规范，变成助手每次都能读到的显式约束**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [agentsmd/agents.md](https://github.com/agentsmd/agents.md) |
| 描述 | AGENTS.md — a simple, open format for guiding coding agents |
| 许可证 | MIT |
| 语言 | TypeScript（官网为 Next.js 站点） |
| 关注度 | **25,262 Star、1,934 Fork、184 关注、187 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2025-08-19 创建，最近推送 2026-09-10；**无 Tag、无 Release** |
| 体积 | 约 2,131 KB（`size` 字段） |

## 用法要点

README 只交代了官网怎么本地跑：

```bash
pnpm install
pnpm run dev
# 打开 http://localhost:3000
```

格式本身的用法是"在项目根目录放一个 `AGENTS.md`"——**这一条是约定，不是代码**。README 没有在本仓库里给出格式的完整规范文档（如字段清单、嵌套规则、多目录覆盖规则等），需要的话只能去官网或仓库其他文件里找。

## 使用前需要注意

- **它是一份"约定/标准"，不是一个软件**：Star 数很高，但仓库里几乎不含可运行的库，Star 高度并不代表代码质量或可用性——**不要把它当成"装了就能用的工具"**。
- **README 极短，信息量主要不在这里**：约 2,000 字符，只有格式示例与官网运行说明。**格式的完整规则、以及它和 Codex 等具体实现的对接细节，本 README 未给出**，我没有进一步核对官网正文。
- **187 个开放 Issue 值得留意**：对一个"文档 + 官网"型仓库来说这个数字偏高，通常意味着大量讨论集中在"格式该怎么定/和各家实现怎么对齐"上。**但 GitHub API 的 `open_issues_count` 会把开放的 Pull Request 一并计入**，所以它**不等于**纯 Issue 标签页的数量，不能直接当作"未解决的分歧有多少"。
- **无 Tag、无 Release**：说明这个格式**没有版本化的正式发布**，随时可能调整；如果要长期依赖某个版本的写法，需要自己保存当时的快照。
- **我只读了 README 与元数据，没有克隆、没有跑官网、也没有在真实项目里用 AGENTS.md 验证过助手是否真的读取它**。

## 什么时候会再看

- 需要**给 AI 编码助手交代项目规范**（装依赖、跑测试、提 PR 的固定动作）时：直接照这个结构写一份 `AGENTS.md`，比散落在贡献文档里的说明更容易被助手读到。
- 想参考**"一个格式约定类项目怎么组织仓库"**时：极简 README + 独立官网的分工，可以对照其他标准类仓库的做法。
- 需要**确认某个助手到底读不读、从哪里读 `AGENTS.md`** 时：本仓库不回答这个问题，得去对应助手的文档。**Vibe-Research 的 README 提到 Codex 会读用户全局的 `$CODEX_HOME/AGENTS.md`**，可作一个旁证（那条来自另一个项目，未独立验证）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **AI 助手生态的其他记录**：[deepseek-harness](deepseek-harness-DeepSeek开源Agent框架.md)（插件化 Agent 框架）、[openclaw](openclaw-自托管个人AI助手.md)（自托管助手）、[dsh-plugins-store](dsh-plugins-store-DSH插件市场.md)（插件市场）、[AiToEarn](AiToEarn-AI内容变现与多平台发布.md)——可对照"给助手提供上下文/能力"的不同层次：AGENTS.md 提供**项目级指令**，MCP 提供**工具能力**，插件市场提供**分发渠道**。
- **MCP 体系三连**：[servers](servers-MCP官方参考服务器合集.md)、[java-sdk](java-sdk-MCP官方JavaSDK.md)、[markitdown](markitdown-文件转Markdown工具.md)——那一组解决的是"助手能调什么工具"，本篇解决的是"助手要遵守什么指令"，两者常一起出现。
- **同时提到 AGENTS.md 的取数工作台**：[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)——其 README 把 `AGENTS.md` 用作部署与维护说明，并特别提醒 Codex 会读取用户全局的 `AGENTS.md`，说明这类文件的影响范围可能超出单个项目。

## 参考来源

- 项目仓库与 README：https://github.com/agentsmd/agents.md
- 官网：https://agents.md/
- 元数据（Star / Fork / 时间 / 许可证）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README 与元数据；未克隆仓库、未运行站点、未在真实项目中验证助手读取行为。格式的完整规范不在本 README 内，未进一步核对。
