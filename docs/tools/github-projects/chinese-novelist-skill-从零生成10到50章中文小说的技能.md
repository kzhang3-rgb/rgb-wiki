# chinese-novelist-skill：让 AI 从零写出一部完整中文小说的 Agent 技能

> 一句话定位（README 自述）：**"让 AI 为你写一部完整的中文小说"**、面向 **Coding Agent / Personal Agent 等各类 Agent** 的技能包——先定人物和大纲，再**逐章写到完稿**。最值得记的三点：① 它的骨架是**四阶段流水线**（Phase 0 初始化 → Phase 1 **三层递进式问答** → Phase 2 **规划 + 确认** → Phase 3 **疯狂创作** → Phase 4 **自动校验与修复**），并配**创作记忆**、**中断续写**、**悬念钩子**与**去 AI 味**；② 写作模式三选一——**串行 / 子 Agent 并行 / Agent Teams**；③ README 顶部挂着**赞助商 Infistar.cc**（"注册即送 $5"），属于**明确的商业引流位**，与项目本身的功能要分开看。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（268 行）、`LICENSE`（1,070 字符）与已核实元数据；**未安装该 Skill、未在 Claude Code / 其它 Agent 中调用、未生成任何一章小说、未运行字数脚本、未访问赞助商站点**）
- 适用范围：**适配主流 coding agent**（README 以 **Claude Code Skill** 徽章标注，安装走 `npx skills add` 的 skills 安装器）；无需本地构建，产出为**本地文件目录**（`user-preferences.json` 与逐章 Markdown）

## 核心结论

1. **面向"从零到完稿"的编排，不是生成器**：README 自述目标是**生成 10–50 章完整中文小说**，"长篇网文连载皆宜"。它把一次创作拆成**先问答定调 → 出大纲 / 人物档案 / 写作计划 JSON → 连续写章 → 章节校验与修复**，并强调**章节衔接**（记录人物处境与事件后果、承接未解悬念）。
2. **流程里有三个"稳住长篇"的机制**：**偏好记忆**（`user-preferences.json`，跨项目共享题材 / 视角 / 文风，减少重复问答）、**中断续写**（检测未完成项目并从断点继续）、**三种写作模式**（串行 = 默认稳妥；子 Agent 并行 = 求速度；Agent Teams = Claude Code 多 Agent 协作 / 大型长篇）。
3. **每章是固定节拍**（README 的 Phase 3）：**写前分析 → 撰写（3,000–5,000 字）→ 润色去 AI 味 → 字数检查 → 更新摘要**；Phase 4 再做**文件与字数检查，不合格章节最多修复 3 轮**。
4. **产出是一个结构化目录**：`user-preferences.json` 与 `<时间戳-书名>/` 下的 `01-大纲.md`、`00-人物档案.md`、`02-写作计划.json`、`03-文风基准.md`（文风指纹 / 基准段落 / AI 味负面清单）、`99-章节摘要.md` 与 `第NN章-*.md`。

**个人判断（未验证）**：它的价值在于把"写长篇"这件**长周期、易断线**的事做成了**有状态的工作流**（偏好 + 断点 + 摘要 + 校验），这比"一次生成一章"更贴合"网文连载"。但**成品质量高度依赖底层模型与提示执行**，且 README 自己承认"**连贯性和文字质量由 Agent 审读，仍需作者把关**"——**别把它当"自动化爽文工厂"**（**个人判断（未验证）**）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [PenglongHuang/chinese-novelist-skill](https://github.com/PenglongHuang/chinese-novelist-skill) |
| 描述（原文） | "🎭 AI 写小说：从零生成 10-50 章完整中文小说，三层问答 · 创作记忆 · 悬念钩子 · 自动校验，长篇网文连载皆宜｜开源免费，适配主流 coding agent｜🤝 Infistar.cc 赞助 · 注册送 $5｜AI novel writing skill" |
| 许可证 | **MIT**（`LICENSE` 1,070 字符，"Copyright (c) 2026 PenglongHuang"） |
| 默认分支 | **`master`**（注意：**不是 `main`**） |
| 关注度 | **ungh 镜像 4,810 Star / 653 Fork / 12 Watcher**；页面值 **4,855 / 664 / 13** |
| 社区 | shields：开放 Issue **14**、贡献者 **2**；另有微信交流群、GitHub Discussions |
| 维护状态 | **2026-01-25 创建**；ungh `pushedAt` **2026-11-01**、页面 `updatedAt` 2026-11-12（活跃） |
| Release | ungh **5** / 页面 **5**；标注版本 **2.3.2**（v2.0 为重构成版本） |
| 安装 | `npx skills add PenglongHuang/chinese-novelist-skill` |

## 功能与用法

### 1. 创作流程（README 的流程图）

```
Phase 0 初始化      ·加载偏好 user-preferences.json
                    ·检测中断
Phase 1 三层问答    L1 核心定位（必答 3 问）：题材创意 / 主角设定 / 核心冲突
                    L2 深度定制（可跳过）：世界观 / 视角 / 主题 / 读者定位 / 章节数量
Phase 2 规划+确认   ·大纲 ·人物档案 ·写作计划 JSON
Phase 2.5 模式选择  串行 / 子 Agent 并行 / Agent Teams
Phase 3 疯狂创作    逐章：写前分析 → 撰写(3000-5000字) → 润色去AI味 → 字数检查 → 更新摘要
Phase 4 自动校验    文件与字数检查 → 不合格章节修复（最多 3 轮）
```

问答每问都支持**随机生成**，也可直接说"跳过 / 都用默认"。Phase 2 会把"基本信息 + 章节规划（前 5 章）+ 主要角色"展示给你**确认**，回复"确认"后再选模式开始。

### 2. 三种写作模式（README）

| 模式 | 说明 | 适用场景 |
| --- | --- | --- |
| **串行** | 主 Agent 逐章写，稳定可靠 | 默认推荐 |
| **子 Agent 并行** | 多个子 Agent 分批并行写 | 追求速度 |
| **Agent Teams** | Claude Code 多 Agent 协作 | 大型长篇 |

### 3. 内置参考资料（README 文件树）

- **流程文档 `references/flows/`**：`phase0-initialization.md`、`phase1-layer1-core.md`、`phase1-layer2-customize.md`、`phase2-planning.md`、`phase3-writing.md`、`phase4-validation.md`、`shared-infrastructure.md`。
- **写作指南 `references/guides/`**：`chapter-guide.md`（含开头技巧 / 中文文学技法 / 连贯性 / 质量检查）、`hook-techniques.md`（**13 种结尾钩子**）、`character-building.md`、`dialogue-writing.md`、`plot-structures.md`、`content-expansion.md`、以及大纲 / 人物 / 章节三个模板。

## 使用前需要注意

- **默认分支是 `master`，不是 `main`**：任何拼接默认分支的链接、raw 文件地址、CI 触发或 clone 脚本都要按 `master` 处理，**别按习惯写 `main`**（这是本仓库与多数新仓库最容易踩的差异）。
- **README 顶部含商业引流，需与功能分开看**：赞助商 **[Infistar.cc](https://www.infistar.cc/register?aff=29SS8B9E&ref_source=link)** 的推广文案宣称"零配置接入 / 超低成本 / 全模型任意切 / 国内顺畅使用"，并给出 **"通过专属通道注册即送 $5 体验额度"**。**该链接带推广参数（`aff=`）**，属**推广 / 返利性质的引流**；`README` 另列有"爱发电赞助"。**这些是第三方服务宣传，不代表本项目功能或质量**（**个人判断（未验证）**）。
- **"自动校验 / 去 AI 味"不等于"成稿即可发"**：README 明确写"**连贯性和文字质量由 Agent 审读，仍需作者把关**"，字数检查也只是"脚本检查字数，不达标时修复"。**把这些当作辅助而非保证**（**个人判断（未验证）**），**自述能力未验证**。
- **偏好文件跨项目共享**：`user-preferences.json` 会记录你的题材 / 视角 / 文风偏好并在项目间复用。好处是省问答，**代价是这类写作偏好数据会在本地长期留存**——**注意存放位置与是否进入版本库**（**个人判断（未验证）**）。
- **依赖 Agent 的 skills 安装器与文件 / 命令能力**：README 面向"能读写的各类 Agent"；真正跑起来需要 Agent 能执行脚本（字数检查）并**在长任务里保持状态**（中断续写 / 子 Agent 并行）。**并行 / Teams 模式的具体行为、是否互相覆盖文件，README 未展开**（**个人判断（未验证）**）。
- **元数据两套口径**：Star / Fork / Watcher 的**页面值（4,855 / 664 / 13）与 ungh 镜像值（4,810 / 653 / 12）不同**，**引用时注明来源与日期**。
- **我只读了 README 与 LICENSE 及元数据**：**未安装 Skill、未在 Claude Code / 其它 Agent 中调用、未生成任何章节、未运行字数脚本、未访问赞助商站点**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想用 Agent **从零写一部长篇中文小说 / 网文连载**，并希望有**大纲、人物档案、文风基准与章节摘要**这类中间产物。
- 想看"**把长周期创作做成有状态工作流**"的技能设计（三层问答 → 规划 → 逐章 → 自动校验；偏好记忆 + 中断续写）。
- 想在**串行 / 子 Agent 并行 / Agent Teams** 之间权衡长文生成的速度与稳定性。
- 想参考"**一个 Skill 同时面向多种 Agent**"的写法（`skills add` + 各 Agent 兼容说明）。
- 想对照其它"AI 写作 / 内容改写"路线的定位差异。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"AI 写中文长文 / 网文"**：[easy-writing：易创网文写作软件](easy-writing-易创网文写作软件.md)——那篇是**独立写作软件**（界面 + 工程化管理），本篇是**交给 Agent 的技能包**（命令行安装 + 文件目录产出）。可对照"**写作软件**"与"**Agent 技能**"。
- **都关心"去掉 AI 腔"**：[no-ai-slop：去掉写作里"AI 腔"的技能](no-ai-slop-去掉写作里的AI腔技能.md)——那篇专门做**去 AI 痕迹**，本篇把"润色去 AI 味"作为**每章固定的一步**并配 `03-文风基准.md`（含 AI 味负面清单）。可对照"**专项去 AI 腔**"与"**长篇流水线里的去味环节**"。
- **都是"把长文本编译成可复用资产"**：[book-to-skill：把一本技术书编译成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇把**技术书**编译成技能，本篇把**一个念头**编译成完整小说目录。可对照"**输入是既有书稿**"与"**输入是零起点问答**"。
- **同属"内容加工流水线 + 质量自检"**：[podcast-to-article：把播客逐字稿整理成可发长文的技能](podcast-to-article-把播客逐字稿整理成可发长文的技能.md)——那篇把**逐字稿**整理成可发长文并做**成稿自检**，本篇从零**生成**长篇并做**逐章校验**。可对照"**加工既有素材**"与"**从零生成**"。

## 参考来源

- 仓库主页与 README（268 行；核验日期 2026-11-13）：https://github.com/PenglongHuang/chinese-novelist-skill
- 许可证：`LICENSE`（MIT，1,070 字符；核验日期 2026-11-13）
- 相关文件（README 指向，未逐一打开）：`references/flows/*`、`references/guides/*`、`ROADMAP.md`、`README` 中提及的 v1.0 → v2.0 升级说明 PR #10
- 赞助 / 引流（**第三方商业内容，非项目功能**）：Infistar.cc 专属通道（含 `aff=` 推广参数）、爱发电赞助页
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 `master` / Release 数 / 贡献者）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13，**页面值与镜像值存在差异**）

以上信息核验于 2026-11-13。**未安装该 Skill、未在 Claude Code / 其它 Agent 中调用、未生成任何章节、未运行字数脚本、未访问赞助商站点**；能力与流程描述均来自 README，**未逐一验证**。
