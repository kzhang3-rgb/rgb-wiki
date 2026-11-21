# Ponytail：让 AI 编码像"最懒的资深开发"一样思考的技能

> 记录一个**给 AI 编码助手注入"能不做就不做"策略**的提示词 / 技能包（`DietrichGebert/ponytail`，描述 "Makes your AI agent think like the laziest senior dev in the room. The best code is the code you never wrote."）：核心是一条"**代码阶梯**"——先看能不能不做，再看代码库有没有、标准库有没有、平台原生有没有、依赖有没有、能不能一行，最后才写"最小可行 + 一处小测试"；配套 `/ponytail-review`、`/ponytail-audit`、`/ponytail-debt`、`/ponytail-gain` 等命令。三条先记住：**它本体是一个 prompt（`skills/ponytail/SKILL.md` + `AGENTS.md`），其余都是加载器**；**官方声明只在 GitHub 与 npm `@dietrichgebert/ponytail` 发布、永不发布 `.exe` / `.dll`**；**它宣传的是"更少代码、更少成本"，且自报"98% 有风险的逻辑仍带测试"**。未安装、未运行、未复现其基准。

- 状态：草稿
- 最近核验：2026-11-17（阅读 `main` 分支 README 全文（231 行）、`LICENSE`、文件清单（216 项）、Release 列表（30 条）与 ungh 镜像 / shields 徽章；**未安装、未在 Claude Code / Codex 等宿主中运行、未复现其基准测试、未审计源码**）
- 适用范围：**提示词 / 技能包**——README 称可装进约 20 种 Agent（Claude Code、Codex、Cursor、Copilot、OpenCode、Gemini、Windsurf、Cline、Kiro 等）；本体是 `skills/ponytail/SKILL.md`，给"只读规则文件"的 Agent 用 `AGENTS.md`

## 核心结论

**一句话定位（API 描述）**：`Makes your AI agent think like the laziest senior dev in the room. The best code is the code you never wrote.` 中文语境就是"**房间里最懒的资深开发**"：你给他五十行，他看一眼，什么也不说，换成一行。

**① 它的本体就是一个 prompt。** README 原文："Ponytail is one prompt: `skills/ponytail/SKILL.md`. The compact version, for agents that read a rules file, is `AGENTS.md`. Everything else in this repo loads that prompt into different agents."——**仓库里其余文件都是"把这条 prompt 装进不同宿主"的加载器**（Claude Code 插件、Codex 插件、`gemini-extension.json`、`opencode.json`、`plugin.yaml` 等）。

**② 核心是"代码阶梯"（the ladder）。** 写代码前**停在第一个成立的台阶**：① 这东西需要存在吗 → ② 代码库里已经有了吗 → ③ 标准库能做吗 → ④ 平台原生功能能做吗 → ⑤ 已装的依赖能做吗 → ⑥ 能一行吗 → ⑦ 才写"能跑的最小实现"，**有逻辑就配一处小测试**。README 强调阶梯**在"读懂问题之后"才跑**："它先读改动涉及的代码、追真实的数据流，再挑台阶。**对方案懒，但对读代码从不懒。**"

**③ 它强调"懒而不失职"。** README："Lazy, not negligent"—**信任边界校验、数据丢失处理、安全、可访问性从不进入削减范围**；"有分支 / 循环 / 解析器 / 涉及金钱或安全的逻辑要留一处小测试"；**每次回复末尾要写"跳过了什么 / 没检查什么 / 有哪些风险"**。

**④ 一套命令（README 原表）。** `/ponytail [lite|full|ultra|off]`（强度开关）、`/ponytail band [on|off]` 与 `/ponytail band import`（Claude Code 专属的"成本 / token / 时间条"）、`/ponytail-review`（像"会被半夜叫起来修 bug 的资深开发"那样审当前 diff：bug / 安全 / 真实负载 / 无测试的风险路径 / 慢路径 / 可删项）、`/ponytail-audit`（对整仓跑同样检查、按优先级排序）、`/ponytail-debt`（把 `shortcut:` 注释收成账本）、`/ponytail-gain`（展示基准里的影响记分板）、`/ponytail-help`。

**⑤ 它主打的数字（自报，未复现）。** README 称 Ponytail 5 相对"无技能基线"：**代码 -53% · 时间 -41% · 成本 -26% · token -45%**，且**"98% 有风险的逻辑带测试"（无技能时 68%）**；在 Claude Code 里以同一 Agent 开 / 关技能各跑（含一个真实 FastAPI + React 仓库、39 个任务、Opus 5.5、每个 5 次运行）得出。**这些是项目自报的基准数字，本笔记未复现、未核验其方法学。**

**⑥ 发布渠道与"防伪"声明。** README 明确：**只从 GitHub `DietrichGebert/ponytail` 或 npm `@dietrichgebert/ponytail` 安装**；"**它从不发布 `.exe` 或 `.dll` 文件；一个发布这些的副本不是我的**"。仓库根目录含 `package.json`（npm 包）、`plugin.json` / `plugin.yaml`（插件清单）、`gemini-extension.json`、`opencode.json`、`INSTALL.md`（15,774 B）等。

**⑦ 关注度极高、且集中。** **193,913 Star / 10,912 Fork / 459 关注**（ungh 快照，2026-11-17；shields 同期 194k / 11k）；**114 个开放 Issue、贡献者 132 人**（shields）；**2026-06-12 创建**、pushed **2026-10-29**；`main`；**30 个 Release**（分页上限；最新 `v5.3.3`，2026-10-29）；**216 个文件**。（含义：**一个"提示词包"在几个月里拿到近 20 万 Star**——热度本身是一个值得记录的现象。）

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) |
| 描述（API） | "Makes your AI agent think like the laziest senior dev in the room. The best code is the code you never wrote." |
| 许可证 | **MIT**（`LICENSE` 1,071 字节；README："MIT. The shortest license that works."） |
| 主语言 | shields 顶层语言 **49.1%** |
| 关注度 | **193,913 Star / 10,912 Fork / 459 关注**（ungh，2026-11-17；shields 同期 194k / 11k）；**114 个开放 Issue、贡献者 132 人**（shields） |
| 创建 / 最近推送 | **2026-06-12 创建**；`pushedAt` **2026-10-29**、`updatedAt` 2026-11-18 |
| 默认分支 / Release / 文件数 | `main`；**30 个 Release**（分页上限；最新 `v5.3.3`，2026-10-29）；**216 个文件** |
| 根目录可见文件 | `README.md`（14,837 B）、`AGENTS.md`（2,721 B）、`INSTALL.md`（15,774 B）、`LICENSE`（1,071 B）、`__init__.py`（8,936 B）、`after-install.md`、`package.json`、`plugin.json`、`plugin.yaml`、`gemini-extension.json`、`opencode.json`、`.gitignore` |
| 本体 / 适配 | 本体：`skills/ponytail/SKILL.md`（完整 prompt）+ `AGENTS.md`（精简规则版）；其余为各宿主加载器与文档 |
| 发布渠道（README） | **只从 GitHub `DietrichGebert/ponytail` 或 npm `@dietrichgebert/ponytail`**；官网 `ponytail.dev` |

## 使用前需要注意

- **① 它只是一个 prompt，效果取决于宿主与模型。** 本体是 `SKILL.md` / `AGENTS.md`；**README 自己也把基准数据限定在"Claude Code + Opus 5.5"**。换宿主 / 换模型 / 换任务，**"更少代码、更少成本"未必成立**；README 也承认"命令需要支持技能的宿主"、部分适配器只加载 always-on 规则而无命令。
- **② 基准数字是"自报"，本笔记未复现。** **-53% 代码 / -41% 时间 / -26% 成本 / -45% token / 98% 风险逻辑带测试**均为 README 与 `benchmarks/` 自述，**未见第三方独立复现**；引用时请标注为"项目自报"。
- **③ "懒"有边界，README 也画了线。** 校验 / 数据丢失 / 安全 / 可访问性**不在削减范围**，且要求每次回复报"跳过了什么、没检查什么、风险是什么"。**但边界是否被真正遵守，取决于实际运行结果——本笔记未验证。**
- **④ 装包要认渠道。** README 强调**只从官方 GitHub / npm 安装**，并声明"**从不发布 `.exe` / `.dll`，发布这些的副本不是我的**"。**下载 / 安装前请核对官方渠道**（对照同类现象的仿冒提醒，如 [HGhubX](HGhubX-从HGhub分叉出的跨平台短剧客户端.md) 的 README）。
- **⑤ 极新 + 极高 Star（现象）。** **创建约 5 个月、Star 近 20 万、30 个 Release、132 位贡献者**。**热度高不等于适配你的栈**；其"band / board"面板还涉及**把历史 Claude Code 会话的成本 / token / 时间导入本地页面**（默认留在本机 `~/.config/ponytail/band/`），**数据边界 README 有说明但值得自己再确认**。
- **⑥ 我未做任何验证。** 仅阅读 README 全文、`LICENSE`、文件清单、Release 列表与元数据；**未安装、未在任一宿主运行、未启用电平 / 面板、未导入任何会话、未复现基准、未审计源码**；命令行为、安装步骤与数字均为 README 自述。

## 什么时候会再看

- 想研究"**把一条编码策略（'能不做就不做'）固化成可被多种 Agent 加载的 prompt / 技能**"时——尤其是**同一份 `SKILL.md` 如何适配约 20 种宿主**（插件清单、规则文件、扩展清单）。
- 想参考"**代码阶梯**"这类"按序否决、最后才写最小实现"的决策结构，以及"**懒但不砍校验 / 安全 / 可访问性**""**回复末尾报风险**"这些边界写法。
- 想对照其它"给编码 Agent 装能力"的插件与技能（[no-ai-slop](no-ai-slop-去掉写作里的AI腔技能.md) 管文风、[vibe-wise](vibe-wise-边写边学的Claude-Code与Codex插件.md) 管学习方法）时；想复盘"**一个提示词包为何能在几个月冲到近 20 万 Star**"时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"给 AI 编码助手装的单点技能"，约束侧相反**：[no-ai-slop：去掉写作里的 AI 腔技能](no-ai-slop-去掉写作里的AI腔技能.md)——那篇从**输出侧**约束"文字写法"（去 AI 腔）；本篇从**方案侧**约束"要不要写、写多少"（代码阶梯）。两篇都是**一枚只管单一维度的技能**，可对照"**管文风**"与"**管体量**"两种把规则固化进技能的做法。
- **同为"让 Claude Code / Codex 变得更好用"的插件 / 技能**：[vibe-wise：边写边学的 Claude Code 与 Codex 插件](vibe-wise-边写边学的Claude-Code与Codex插件.md)——那篇把**教学检查点**做成固定交互（强迫你先想清设计再看 AI 写）；本篇把**"懒得写代码"**做成 always-on 规则。一个偏"**在动手中学习**"，一个偏"**在动手中削减**"，可对照两种插件如何改变编码过程。
- **都涉及"给编码助手注入规则"的载体**：[agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——本篇的**精简版本体就是一份 `AGENTS.md`**；那篇讲的是这个"规则文件格式"本身。想理解"prompt 如何落成跨工具可读的规则文件"，两篇是上下游。

## 参考来源

- 仓库主页与 README（核验日期 2026-11-17）：https://github.com/DietrichGebert/ponytail
- 本体 prompt 与精简规则：`skills/ponytail/SKILL.md` · `AGENTS.md`（仓库内）
- 安装说明：`INSTALL.md`（15,774 B）· 官网 <https://ponytail.dev>
- 许可证 `LICENSE`（MIT，1,071 字节）：https://github.com/DietrichGebert/ponytail/blob/main/LICENSE
- Release 列表（30 条，最新 `v5.3.3`，2026-10-29）：https://github.com/DietrichGebert/ponytail/releases
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-17

以上信息核验于 2026-11-17：Star / Fork 与版本变化很快（该仓库创建约 5 个月即近 20 万 Star）。**未安装、未在任一宿主运行、未复现基准、未审计源码**；**命令行为、安装步骤、平台列表与"更少代码 / 更少成本 / 98% 带测试"等数字均为项目自报，本笔记未独立验证**；**下载 / 安装请以 README 声明的官方渠道（GitHub 与 npm `@dietrichgebert/ponytail`）为准。**
