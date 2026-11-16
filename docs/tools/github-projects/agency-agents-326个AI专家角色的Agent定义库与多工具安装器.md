# agency-agents：326 个 AI 专家角色的 Agent 定义库与多工具安装器

> 一句话定位（README 自述）：**"The Agency: AI Specialists Ready to Transform Your Workflow"**——自称一整个"**AI 代理公司**"，用**每个角色一个 `.md` 定义**的方式提供 **326 个专家**（从"前端开发"到"Reddit 社区运营"），并配**跨约 15 种工具的转换 / 安装脚本**（Claude Code / Copilot / Cursor / Codex / Gemini / OpenCode / Windsurf / Aider / Qwen / Kimi / Osaurus / OpenClaw / Hermes / DeepSeek Harness 等），另有桌面应用与站点 `agencyagents.app`。最值得记的三点：① **内容主体是"人设 + 流程 + 交付物"的角色定义文件**，不是可直接运行的代码；② **分发方式多样**（原生 `.md`、转换脚本、图形化 App、社区翻译）；③ **体量与热度极高（★ 超 16 万），但 README 自报的 "230+ agents" 与实际统计的 326 个文件口径不一致**。

- 状态：草稿
- 最近核验：2026-11-13（**分段读完全部 README（1,185 行）**、`LICENSE`（MIT 全文）、顶层目录清单与元数据；**未 clone、未安装、未运行任何安装 / 转换脚本、未把任何 agent 装入任何工具、未逐条审计 326 个 agent 定义**）
- 适用范围：使用 **Claude Code / GitHub Copilot / Cursor / Codex / Gemini CLI / OpenCode / OpenClaw / Windsurf / Aider / Qwen / Kimi / Osaurus / Hermes / DeepSeek Harness** 等编码助手的人；本仓库以 **Markdown 文件 + shell 脚本** 分发，跨平台

## 核心结论

1. **本质是"角色定义库"**（README 自述）：**"a growing collection of meticulously crafted AI agent personalities"**——每个 agent 是一个 `.md` 文件，含**身份与人设（Identity）、核心使命（Core Mission）、领域规则（Critical Rules）、交付物（Technical Deliverables，含代码示例）、工作流（Workflow）、成功指标（Success Metrics）**；README 明确"**不是通用提示词模板**"。
2. **规模（已核实元数据）**：**326 个 agent 的 `.md` 定义**，分布在 20 个分部；`main` 上 **384 个条目**；README 正文的 **"230+ Specialized Agents"** 是早期口径（见"使用前需要注意"）。
3. **多工具分发是其最大卖点**：原生 `.md` 直接给 **Claude Code / GitHub Copilot**；其余工具走 `scripts/convert.sh` + `scripts/install.sh`（支持 `--tool` / `--division` / `--agent` / `--agents-file` / `--dry-run` / `--parallel` / `--no-interactive`）转换为各家格式（Cursor 的 `.mdc`、Codex 的 TOML、Kimi 的 YAML、OpenClaw 的 `SOUL.md` + `AGENTS.md` + `IDENTITY.md`、DSH 的 `SKILL.md`、Aider 的 `CONVENTIONS.md`、Windsurf 的 `.windsurfrules` 等）。
4. **另有图形化 App 与站点**：README 顶部推广 **Agency Agents 桌面应用**（macOS / Linux / Windows，安装进 Claude Code / Cursor / Codex / Gemini / Osaurus 等，一键且自动更新），下载在**另一个仓库** `msitarzewski/agency-agents-app`，站点 <https://agencyagents.app>；**本仓库本身 0 Release**。
5. **许可 = MIT**（`LICENSE` 全文）；README 写 "use freely, commercially or personally"。

**个人判断（未验证）**：这是"**Agent 人设库**"路线的头部项目——用**极其细致的角色设定**换取"专业化输出"的观感。它的价值可能在**写作范式（一份好的角色定义长什么样）**和**多工具适配目录**，而不在"拿来即用"；**326 个角色是否都名副其实、质量是否参差，需要抽样自评**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [msitarzewski/agency-agents](https://github.com/msitarzewski/agency-agents) |
| 描述 | "The Agency"——按分部组织的 AI 专家角色定义集合，附跨工具安装脚本 |
| 许可证 | **MIT**（`LICENSE` 全文；**注意文件里版权署名是 "AgentLand Contributors"，与仓库所有者 `msitarzewski` 不同名**，见"使用前需要注意"） |
| 主语言 | 未取到（未核验；主体为 Markdown + shell 脚本） |
| 关注度 | **★167,773 / Fork 27,216 / Watcher 1,178**（GitHub 页面值，2026-11-13）；开放 Issue **37**、贡献者 **104** |
| 维护状态 | **2025-10-13 创建**；页面 `updatedAt` **2026-11-11**、ungh `pushedAt` **2026-11-09**；默认分支 `main`；**0 Release** |
| 体积 | `main` 分支 **384 个条目**；其中 agent 定义 `.md` **326 个**（按分部逐目录统计，合计见下表） |
| 站点 / App | <https://agencyagents.app>；桌面应用在另一个仓库 `msitarzewski/agency-agents-app` |
| 元文件 | `divisions.json`、`tools.json`、`strategy/runbooks.json`（runbook 花名册）、`CONTRIBUTING.md` / `CONTRIBUTING_zh-CN.md`、`SECURITY.md` |

## 能力结构

### 1. 分部与 agent 数（逐目录统计，合计 326）

| 分部 | 数量 | 分部 | 数量 |
| --- | --- | --- | --- |
| engineering | 66 | specialized | 59 |
| marketing | 37 | game-development | 21 |
| integrations | 19 | strategy | 16 |
| gis | 13 | security | 12 |
| design | 10 | testing | 9 |
| sales | 9 | project-management | 7 |
| paid-media | 7 | support | 6 |
| spatial-computing | 6 | examples | 6 |
| academic | 6 | product | 6 |
| finance | 5 | healthcare | 3 |
| research | 1 | scripts | 1 |
| .github | 1 | **合计** | **326** |

README 另按"Engineering / Design / Paid Media / Sales / Marketing / Product / Project Management / Testing / Security / Support / Spatial Computing / Specialized / Finance / Game Development / Academic / GIS / Healthcare / Research"等小节列出；`game-development` 下再分 Unity / Unreal / Godot / Blender / Roblox 等子目录。

### 2. Agent 文件的结构（README "Add a New Agent"）

每个 `.md` 含：**frontmatter（name / description / color）**、**Identity & Memory**、**Core Mission**、**Critical Rules**、**Technical Deliverables（带代码示例）**、**Workflow Process**、**Success Metrics**。README 称"每个 agent 都有独特的声音、沟通风格与方法"。

### 3. 分发与集成（README）

- **原生 `.md`（无需转换）**：**Claude Code** → `~/.claude/agents/`；**GitHub Copilot** → `~/.github/agents/` + `~/.copilot/agents/`。
- **需转换 / 安装脚本**：Antigravity（每 agent 一个 `SKILL.md` → `~/.gemini/config/skills/`）、Gemini CLI、OpenCode（`.opencode/agents/`）、Cursor（`.mdc` → `.cursor/rules/`）、Aider（`CONVENTIONS.md` 只放花名册索引）、Windsurf（`.windsurfrules`）、OpenClaw（每 agent 一个工作区 `SOUL.md` / `AGENTS.md` / `IDENTITY.md`）、Qwen Code、Kimi Code（YAML）、Codex（TOML）、Osaurus（`SKILL.md`）、Hermes（lazy-router 插件）、DeepSeek Harness（`SKILL.md` 技能）。
- **选择粒度**：`--division`、`--agent`、`--agents-file`（可用 runbook 花名册批量装）；`--dry-run` 预演；`--parallel` / `--jobs N` 并行。
- **桌面应用**（另一仓库）：一键浏览与安装、自动更新。

### 4. 示例工作流与翻译

- `examples/` 含多 agent 协作示例（如 `examples/nexus-spatial-discovery.md`：8 个 agent 并行产出一份跨职能产品蓝图）。
- README 列了**社区翻译 / 本地化**：简体中文（2 个独立仓库）、葡语、俄语、印尼语、阿语、韩语、日语、越南语（各由不同维护者维护，版本覆盖不一）。

## 使用前需要注意

- **体量极大、质量未逐条审计**：**326 个 agent 定义**、跨 20 个分部，**我只统计了文件数，没有逐条审阅内容**；README 的 "Production-Ready / Battle-tested" 等评价**属自述**，需按需抽样自评。
- **口径不一致**：README 正文与致谢都写 **"230+ agents"**，而按目录统计为 **326 个 `.md`**（README 未随增长更新计数）；另一处写 **"10,000+ lines"**（未核验）。**引用数字时以目录统计 + 核验日期为准**。
- **第三方工具限制已有前兆**：README 明写 **OpenCode 运行时当前只注册约 119 个 agent、其余会被静默丢弃**（附上游 bug 链接），并建议用 `--division` 限装；**这说明"一次装全部"在其他工具上也可能有上限**。
- **App 与站点在另一仓库 / 站外**：桌面应用在 `msitarzewski/agency-agents-app`（本仓库 **0 Release**），站点 `agencyagents.app` 为站外入口；**长期可用性与条款自判**。
- **LICENSE 署名与仓库所有者不同名**：`LICENSE` 版权行为 **"AgentLand Contributors"**，仓库归 `msitarzewski`——**署名差异不代表许可冲突，但引用出处时注意**。
- **安装脚本会写你的助手目录**：`install.sh` 会把文件写进 `~/.claude/agents/`、`.cursor/rules/` 等；**建议先看 `--dry-run`，并按需只用 `--division`**。
- **我只读了 README、`LICENSE` 与目录**：**未 clone、未运行脚本、未安装任何 agent、未审计源码与内容**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想找**某个具体角色的"人设 + 流程 + 交付物"写法**做参考（如工程、测试、安全、营销、GIS 等分部）时。
- 想把**同一套 agent 定义批量分发到多个编码工具**，想抄 `convert.sh` / `install.sh` 的适配目录（各工具落盘路径与文件格式）时。
- 想研究"**角色定义长什么样才算专业**"（Identity / Mission / Rules / Deliverables / Workflow / Metrics 六段式）时。
- 想对照其它"技能 / 角色集合"项目的组织与体量时（见下）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"聚合 + 一键安装"**：[everything-ai-coding：聚合编程 AI 扩展资源的索引与一键安装库](everything-ai-coding-聚合编程AI扩展资源的索引与一键安装库.md)——那篇是**把多种编程 AI 扩展做成索引 + 一键安装**，本篇是**把 326 个角色定义一键装进多种工具**；可对照"索引库"与"内容库 + 安装器"。
- **同为"给编码助手的定义文件"**：[agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——那篇讲 **`AGENTS.md` 这类约定文件本身**，本篇是**用 `.md` 承载角色人设**，且会向 OpenClaw / DSH 等输出 `AGENTS.md` / `SKILL.md`。
- **同为"把经验炼成技能包"**：[Supervisor-Skills：把博导经验炼化成 AI 科研副导师技能](Supervisor-Skills-把博导经验炼化成AI科研副导师技能.md)——那篇是**单一领域、少量技能**，本篇是**泛领域、数百角色**；可对照"专精"与"广度"两种 skill 组织方式。
- **同为"从仓库 / 书稿生成 Claude Code 技能"**：[book-to-skill：把技术书转成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇的输入是**一本书**，本篇的输入是**已写好的角色定义**；可对照两者"转换管线"的差异。
- **同属"给一堆 agent 分配角色 / 岗位"，形态不同**：[dontbemichael：用现有订阅跑一屋子 Agent 的本地多 Agent 框架](dontbemichael-用现有订阅跑一屋子Agent的本地多Agent框架.md)——本篇是**可安装到多种工具的角色定义库**（偏"角色提示词资产"），那篇是**带办公室界面、邮箱与记忆的本地应用**（偏"跑起来的组织"）；可对照"角色定义"与"角色 + 运行时"。

## 参考来源

- 仓库主页与 README（1,185 行；核验日期 2026-11-13）：https://github.com/msitarzewski/agency-agents
- 许可证：`LICENSE`（MIT 全文，署名 "AgentLand Contributors"）
- 集成说明：`integrations/*/README.md`、`scripts/convert.sh`、`scripts/install.sh`、`strategy/runbooks.json`
- 站点与应用：<https://agencyagents.app>；`msitarzewski/agency-agents-app`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 文件数 / 贡献者与 Issue）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13）
- 分部 agent 数：按 `main` 分支逐目录统计（2026-11-13）

以上信息核验于 2026-11-13。**未 clone、未安装、未运行任何安装 / 转换脚本、未把任何 agent 装入任何工具、未逐条审计 326 个 agent 定义**；能力与流程描述均来自 README 与 `LICENSE`，**未逐一验证**。
