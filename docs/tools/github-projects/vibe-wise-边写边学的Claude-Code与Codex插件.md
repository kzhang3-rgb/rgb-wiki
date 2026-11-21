# vibe-wise：边写边学的 Claude Code 与 Codex 插件

> 一句话定位（README 自述）：**"You build. AI writes."**——一个**同时支持 Claude Code 与 Codex** 的插件：**AI 先问你的思路**，帮你看权衡、解释陌生概念；**你定设计、你决定何时可以实现**；AI 写完代码后再解释改了什么、为什么。它最值得记的是三件事：① **它把"学习"放进编码流程**——不是"AI 直接写完给你"，而是**在 Build / Design / Implementation 三级检查点上让你先推理**；② **检查点不是三个必经站**——你直接要代码时，Implementation 检查点**会连带确认设计**（跳过单独的 Design 检查点），每个确认都带 **Discuss** 选项；③ **无额外账号 / 后端 / 遥测**——偏好与学习笔记存在项目里的 `.vibe-wise/`（Git 仓库下即仓库根），**并明确提示你把它加进 `.gitignore`**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（297 行 / 10,798 字符）、`LICENSE`（MIT）、完整文件树与元数据；**未安装、未在 Claude Code / Codex 中运行、未跑任何一次学习会话、未审计插件源码**）
- 适用范围：需要**较新版本的 Claude Code 或 Codex** + **Python 3**（Claude Code 用 Python 恢复学习上下文与重置笔记；Codex 只有重置需要它；**无额外 Python 包依赖**）

## 核心结论

**一句话定位（README 自述）**：**面向 Claude Code 与 Codex 的"边写边学"插件**——**AI 先问你的方法**，你定设计并决定何时实现，AI 写代码后解释改动与理由。

**三个最值得记的点：**

1. **它把"学习"编进编码流程，而不是事后补课**（README 自述）：
   - 面向"**边做边学**"的人——包括**新人、初级开发者、以及探索陌生技术栈的有经验工程师**。
   - 起手式：`/vibe-wise:learn`（Codex 用 `$vibe-wise:learn`）→ **一次问一个问题**（可用方向键与 Enter 选择，选 **Use defaults** 可跳过偏好设置）→ 然后让 AI 开工。**新项目或陌生仓库都行**（已有仓库时 Claude 会先看代码、画一张小型系统地图）。
   - **你不需要先知道答案**：AI 会解释陌生概念、勾出相关部分、帮你把问题拆小；用自然语言回答即可，想跳过就说 "skip"。
2. **三级检查点**（README 自述）：
   | 检查点 | 发生什么 |
   | --- | --- |
   | **Build** | 你和 AI **一起推理**该怎么处理问题 |
   | **Design** | 审设计。**Confirm and continue** 记录该设计并继续规划，**还不写代码** |
   | **Implementation** | 审**具体的代码改动**。**Implement this step** 授权 AI 动手 |
   - **不是三个必经站**：**准备写代码时，Implementation 检查点会同时确认设计**，跳过单独的 Design 检查点。
   - **两个确认都提供 Discuss**：可以提问、澄清、或探索替代方案后再决定。
   - **实现后**：AI 简述改了什么、关键代码怎么工作、为什么符合你的决定、加了 / 改了哪些测试及覆盖什么、跑了哪些检查及结果。
   - **当 AI 想加实现细节**时，会**把这些"它加的"和"你定的"分开列**（短列表或表格，说明每项加了什么、为什么重要），**你可以逐项质疑或改**。
3. **本地优先、无账号无遥测**（README 自述）：
   - **无额外账号、后端或遥测**；笔记存放在项目里的 `.vibe-wise/`（**Git 仓库下即仓库根**，插件从任意子目录都能找到；**无 Git 时是你启动时的文件夹，所以要从项目顶层启动**）。
   - 已保存笔记**会进入 AI 上下文**，所以**仍适用你平时 Claude Code / Codex 的数据设置**。
   - **建议把 `.vibe-wise/` 加进 `.gitignore`**（**插件不会静默改你的 `.gitignore`**）。
   - `/vibe-wise:reset` 会先显示项目并询问 **Cancel / Reset learning**；确认后把 profile、进度、项目地图**备份到笔记目录的 `backups/`**，再重启 onboarding——**源码与其它项目不受影响**。

**个人判断（未验证）**：它切的是"**用 AI 写代码，但不想把思考外包掉**"的人——**把"先想清楚再做"变成插件里的固定交互**。**代价是它要求你真的参与推理**（否则只是变成一个多问几句的编码助手）；**收益是把"AI 代劳"改成"AI 陪练"**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [nykooi1/vibe-wise](https://github.com/nykooi1/vibe-wise) |
| 描述 | "You build. AI writes."——面向 Claude Code 与 Codex 的"边写边学"插件 |
| 归属 | 用户账号 `nykooi1`；`isFork: false`、`isArchived: false` |
| 许可证 | **MIT**（`LICENSE` 1,065 字符）——README 明说"**可商用、可修改、可分享；分发时保留许可证声明；无担保**" |
| 主语言 | **Python 100.0%**（shields.io，2026-11-13）——主要用在钩子 / 脚本（恢复学习上下文、重置笔记） |
| 技术栈 | **Claude Code 插件（`.claude-plugin/`）+ Codex 插件（`.codex-plugin/`）+ Skills（`skills/`）+ Hooks（`hooks/`）**；需 **Python 3** |
| 关注度 | **4,345 Star / 174 Fork / 15 Watcher**（**页面值**，2026-11-13）；开放 Issue **7**、贡献者 **1**（shields.io，2026-11-13）。**ungh 镜像 4,337 / 15 / 173，略有差异** |
| 维护状态 | **2026-09-29 创建**；ungh 报 `pushedAt` **2026-10-09**、页面 `updatedAt` 2026-11-12——**代码推送约 1 个月前，近期更新多为元数据 / 徽章刷新**；页面报 **0 Release**（**通过插件市场分发，不发 GitHub Release**） |
| 默认分支 | **`main`** |
| 体积 | `main` 分支 **24 个条目**（ungh.cc files API）；以 `skills`（10）为主，另有 `.claude-plugin`（3）、`docs`（2）、`hooks`（2）、`tests`（2）、`.agents`（1）、`.codex-plugin`（1）、`assets`（1）、`LICENSE`、`README.md` |

## 安装与使用（README 自述）

**Claude Code**（内置 Anthropic 目录）：

```text
/plugin install vibe-wise@anthropic-plugin-directory
```

> 若你的 Claude Code 版本没有 Anthropic 目录，可走 GitHub 市场：`/plugin marketplace add nykooi1/vibe-wise` → `/plugin install vibe-wise@vibe-wise`（**第三方市场默认关闭自动更新**，可在 `/plugin` → Marketplaces 里开启）。**两种方式选一种即可。**

**Codex**（终端，逐条运行）：

```sh
codex plugin marketplace add nykooi1/vibe-wise
codex plugin add vibe-wise@vibe-wise
```

启动后运行 `$vibe-wise:learn`（**每个 Codex 会话开始时都跑一次**，或当 Codex 像是丢了学习模式时再跑；会**从已保存笔记恢复、不重复 setup**）。**Claude Code 与 Codex 共享同一份 `.vibe-wise/` 笔记。**

**命令对照**：Claude Code 用 `/`，Codex 用 `$`——`/vibe-wise:learn` ↔ `$vibe-wise:learn`；重置用 `/vibe-wise:reset` / `$vibe-wise:reset`。

**自定义（Make it yours）**：

| 经验级别 | 教学方式 |
| --- | --- |
| Beginner | 解释陌生部分、用图、问更小的推理问题 |
| Intermediate | 减少入门性背景，探讨交互与权衡 |
| Advanced | 追问难约束、失败模式与设计假设 |

- **所有级别都"先推理"**；AI 会根据你表现与对技术栈的熟悉度自适应。
- **检查点频率（Light / Normal / Frequent）与级别独立可调**。
- 可直接对 AI 说："少用几个检查点" / "聚焦后端架构" / "用多选题" / "这一个直接实现" / "暂停学习"（再用 `/vibe-wise:learn` 恢复）。

**更新**：Claude Code 里 `/plugin` → Installed → Update now；或 `claude plugin update vibe-wise@anthropic-plugin-directory`（GitHub 市场安装的用 `claude plugin marketplace update vibe-wise` + `claude plugin update vibe-wise@vibe-wise`）。Codex 用 `codex plugin marketplace upgrade vibe-wise` + `codex plugin add vibe-wise@vibe-wise`，然后开新会话。**更新不影响你已有的学习笔记，无需重置。**

## 使用前需要注意

- **它要求你真的参与推理**：设计是"AI 先问你"——若你一路跳过 / 直接要代码，**它就退化成一个普通编码助手**（价值主要来自你愿意走检查点）。
- **笔记会进 AI 上下文**：`.vibe-wise/` 里的偏好、学习笔记、项目地图**会随对话进入模型上下文**，**因此仍受你平时 Claude Code / Codex 的数据设置约束**（README 明说"无额外后端 / 遥测"只指插件自身）。
- **要把 `.vibe-wise/` 加进 `.gitignore`**：README 提示但**不会替你改**——**否则学习笔记与项目地图可能被提交进仓库**。
- **无 Git 时要从项目顶层启动**：笔记目录 = 你启动时的文件夹；**从子目录启动会导致插件把你的子目录当项目根**。
- **安装依赖插件市场**：Claude Code 走 **Anthropic 目录或 GitHub 市场**，Codex 走 **Codex 插件市场**；**具体命令与可用性随客户端版本变化**，以官方文档为准。
- **代码推送约在 1 个月前**（`pushedAt` 2026-10-09），**0 Release**（走市场分发），**贡献者 1 人**——**迭代与长期维护需自行评估**。
- **我只读了 README、许可与文件树**：**未安装、未在 Claude Code / Codex 中运行、未跑任何一次学习会话、未审计 `hooks/` / `skills/` 源码**；交互示例与检查点机制均来自 README，**未逐一验证**。

## 什么时候会再看

- 想在自己用 AI 编码时**"边做边学"**（强迫自己先想清楚设计、再看 AI 写），或想给团队成员 / 初学者装一个**陪练式**的编码助手时。
- 想参考"**把教学 / 检查点做成 Agent 插件的固定交互**"（Build / Design / Implementation 三级检查点 + Discuss 选项 + 实现后解释）的交互设计时。
- 想研究"**同一套笔记如何让 Claude Code 与 Codex 共享**"（`.vibe-wise/` 目录约定 + 本地优先、无账号无遥测）的跨客户端做法时。
- 想对照其它 Claude Code / Codex 插件与技能：[jobflow-for-codex](jobflow-for-codex-面向Codex的本地求职工作流.md)（求职工作流）· [svg-diagram](svg-diagram-手写SVG图表的Agent技能与linter.md)（手写 SVG 技能 + linter）· [book-to-skill](book-to-skill-把技术书转成ClaudeCode技能.md)（把技术书转成技能）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"给 Claude Code / Codex 装能力"的插件与技能**：[jobflow-for-codex：面向 Codex 的本地求职工作流](jobflow-for-codex-面向Codex的本地求职工作流.md)（把求职流程做成技能 + 台账）· [svg-diagram：手写 SVG 图表的 Agent 技能与 linter](svg-diagram-手写SVG图表的Agent技能与linter.md)（house style + 零依赖 linter）。几篇都是"**把一类流程 / 规则固化进 Agent 插件**"，可对照它们的**技能组织、检查点与边界写法**。
- **同属"用 AI 学习 / 提升"的取向**：[book-to-skill：把技术书转成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇把**技术书**转成可被 Agent 调用的技能；本篇在**真实编码过程中**陪你推理。一个偏"**把知识装进技能**"，一个偏"**在动手中学习**"，可对照。
- **同为"给 Claude Code / Codex 装能力"的插件 / 技能**：[Ponytail：让 AI 编码像"最懒的资深开发"一样思考的技能](ponytail-让AI编码像最懒的资深开发一样思考的技能.md)——那篇把**"懒得写代码"**做成 always-on 规则（本体是一份 `SKILL.md` / `AGENTS.md`）；本篇把**教学检查点**做成固定交互。一个偏"**在动手中削减**"，一个偏"**在动手中学习**"。

## 参考来源

- 仓库主页与 README（297 行 / 10,798 字符；核验日期 2026-11-13）：https://github.com/nykooi1/vibe-wise
- 许可证：`LICENSE`（MIT，1,065 字符）
- 官方文档：Claude Code 插件文档 <https://code.claude.com/docs/en/discover-plugins>（README 引用）· Codex <https://developers.openai.com/codex>
- 完整文件树（24 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）

以上信息核验于 2026-11-13。**未安装、未在 Claude Code / Codex 中运行、未跑任何一次学习会话、未审计插件源码**；交互示例、检查点机制与偏好设置均来自 README，**未逐一验证**。
