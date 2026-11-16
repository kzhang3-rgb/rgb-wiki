# Supervisor-Skills：把博导的科研经验炼化成 AI 副导师技能

> 一句话定位（README 自述）：**"将博导十年科研经验，炼化为你的 AI 副导师"**——把作者在数据科学与 AI 顶会（SIGMOD / VLDB / ICML / NeurIPS 等）的发表与审稿经验，蒸馏成 **11~12 个结构化 AI 技能**（覆盖选题、Idea 评估、论文规划、正文写作、润色、作图、投稿前自查、Rebuttal），配一套**六章中英双语教程（`handbook/` + `handbook-en/`）**。最值得记的是三点：① 它是**"方法论 + 可执行技能"双轨制**——指南讲"为什么"，`skills/` 里每个目录一个 `SKILL.md` 直接可装进助手；② **许可证是 CC BY-NC-SA 4.0（非商业 + 相同方式共享）**，这与仓库里常见的 MIT / Apache 完全不同，商用前必须看清；③ 作者是香港科技大学（广州）助理教授骆昱宇，项目已**与百度库库AI、豆包办公模式合作落地**，是"个人科研方法论产品化"的样本。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（227 行）、`README.en.md` 的摘要、`LICENSE`、`llms.txt`、完整文件树与元数据；**未安装任何技能、未在 Claude Code / Codex 中运行、未跑任何一次评估或写作、未读 `handbook/` 正文、未审计各 `SKILL.md` 全文**）
- 适用范围：使用 Claude Code / Cursor / Codex 等助手的科研工作者（面向 NeurIPS / ICML / ICLR / VLDB / SIGMOD / KDD / ACL / EMNLP / CVPR 等顶会投稿）；**技能文本本身与助手无关**，也可直接复制进 Claude / DeepSeek / Kimi 使用；仓库主体是 **Markdown + Python**（Python 100%，实为文档工程）

## 核心结论

**① 它是什么。** 一个**"科研方法论 + 可执行技能"的双轨仓库**：`handbook/` 是系统性理论教程（六章，中文），`handbook-en/` 是英文镜像，`skills/` 是把理论蒸馏成的 Agent Skills。README 的原话是"只有理解了『道』，才能更好地使用『器』"——**指南负责讲清楚，技能负责跑起来**。

**② 它怎么做。** 每个技能就是一个含 `SKILL.md` 的目录，README 给出的安装方式是一条自然语言 Prompt：

```
Help me install every skill under the skills/ directory from https://github.com/HKUSTDial/Supervisor-Skills. Treat each directory containing SKILL.md as a separate skill; do not install the repository root as one skill.
```

`llms.txt` 里也说明了同样是**逐目录安装、不要把仓库根当成一个技能**（Codex 用户用 `$skill-installer`）；想只装 Draw.io 重建技能就单独指定 `.../tree/main/skills/drawio-reconstruction`。

**③ 最值得记的取舍：它把"防幻觉"当成写作技能的核心约束。** 几个技能反复出现同一套价值观——**每个事实性声明都要可溯源**：`paper-writer` 自述"证据门控（evidence-gated）……每个声明可溯源到你的材料或核验过的文献，引用经独立核查，**绝不编造**"；`intro-drafter` 强调"引用经**真实检索**核验"；`deep-research` 是"逐条引用核验 + MECE 分类综合"；`paper-polish` 则**把可能改变含义的改动逐条标出请你确认**。**这是"让 AI 写论文"这个高危场景里少见的、把"不许编"写进技能定义的做法。**

**个人判断（未验证）**：这个仓库的价值有两层——**方法论层**（六章 handbook 是可直接读的科研写作教材）与**产品化层**（"把个人隐性经验拆成一组可安装技能"的样本）。要真正用起来，需先接受**非商用许可**，其次要理解**这些技能只在"你有真实材料"时才安全**——它们的设计前提就是"证据门控"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [HKUSTDial/Supervisor-Skills](https://github.com/HKUSTDial/Supervisor-Skills) |
| 描述 | "将博导十年科研经验，炼化为你的 AI 副导师"（作者 [骆昱宇](https://luoyuyu.vip/)，香港科技大学（广州）助理教授） |
| 许可证 | **CC BY-NC-SA 4.0**（`LICENSE` 1,018 字符）——**含 NonCommercial 限制**，非商业分享与改编需**注明出处**并按**相同方式共享**。GitHub 与 shields.io 均**识别不出**该许可，须以 `LICENSE` 文件为准 |
| 主语言 | **Python 100%**（shields.io，2026-11-13；**实为 Markdown 文档工程**） |
| 关注度 | **12,426 Star / 737 Fork / 30 Watcher**（**页面值**，2026-11-13）；开放 Issue **1**、贡献者 **5**（shields.io，同日）。**ungh 镜像 12,401 / 737 / 30** |
| 维护状态 | **2026-04-19 创建**（README 记为"项目发布：七个锚点技能"）；ungh `pushedAt` **2026-11-05**、页面 `updatedAt` 2026-11-13；默认分支 **`main`**；**0 条 Release**；当前版本 **v2.1.0**（写入 `llms.txt`） |
| 体积 | `main` 分支 **186 个条目**：`skills` 108、`handbook` 30、`handbook-en` 18，其余为 `assets` 等；根目录另有 `README.md` / `README.en.md` / `CHANGELOG.md` / `CONTRIBUTING.md` / `LICENSE` / `llms.txt` |
| 相关项目 | [HKUSTDial/DataMagic](https://github.com/HKUSTDial/DataMagic)（动态图表与数据故事）· [HKUSTDial/DeepEye](https://github.com/HKUSTDial/DeepEye)（数据分析智能体）· 续作 [Ducktor](https://github.com/yinwu01/Ducktor) / [DeepSeek-Ducktor](https://github.com/yinwu01/DeepSeek-Ducktor) |
| 联系 | Yuyu Luo（`yuyuluo [AT] hkust-gz.edu.cn`）；微信交流群需加管理员 `xyp619` |

## 能力结构

### 1. 技能清单（`skills/`）

README 表格列出 **12 个技能**，`llms.txt` 则写 **"eleven anchor skills"** 并列出 **11 个**——两份文件的口径不一致（**README 多出的一个是 `rebuttal-guidance`**，对应 TODO 里"如何写好 Rebuttal"那章）。以仓库实际目录为准：

| 技能 | 功能（README / `llms.txt` 摘要） |
| --- | --- |
| `idea-evaluator` | 用"更高 / 更快 / 更强 / 更省 / 更广"五维框架 + 生命周期能力匹配给 Idea 打分，含**致命缺陷审计**与**范式转移探测**；非 STEM 的 Idea 走替代框架 |
| `deep-research` | 综述级文献调研：多视角检索、逐条引用核验、MECE 分类综合与交叉对比，产出**逐条回答研究问题**的 survey 报告 |
| `vibe-research-workflow` | Vibe Coding / Figure / Writing 的工具选择，以及 AI 辅助科研的**六条行为准则** |
| `tech-paper-template` | 技术类论文思考模板（含**四项自洽检查**与"Technique vs New Problem"定位） |
| `intro-drafter` | 按六段式 Flowchart 产出 Introduction 正文（**引用经真实检索核验**），也可只出大纲 |
| `paper-writer` | **证据门控**的论文正文写作：从单段到整篇，每个事实性声明可溯源，引用过独立核查阶梯 |
| `benchmark-paper-template` | 面向 Benchmark / Evaluation 类论文的五柱审计、六段 Intro 逻辑链、Section 2–7 骨架与投稿前清单 |
| `paper-polish` | 忠于原意的语言润色、去 AI 腔、措辞分寸、中文稿改写为投稿级英文；**可能动到含义的改动逐条标出请你确认** |
| `figure-designer` | 动机图 / 总览图 / 实验图的**范式选择与质量审计** |
| `drawio-reconstruction` | 把参考图、论文图、架构图、幻灯片图或 UI 截图**重建为可编辑 `.drawio`**，并导出 PNG 预览与视觉审计 |
| `pre-submission-reviewer` | 顶会审稿人视角自查：宏观逻辑、写作细节、语法、LaTeX 格式、图质量，含**基于检索的新颖性与引用完整性检查** |
| `rebuttal-guidance` | 基于条件说服模型：识别审稿人心态（X）、匹配 rebuttal 策略（Y），为每条 Weakness / Question 生成回应指导（concern 分类、语气建议、证据指针、完整性检查） |

### 2. 教程（`handbook/` 六章）

| 章节 | 内容 |
| --- | --- |
| 01 Preliminary | 从审稿人视角看论文质量（Novel Problem / Novel Method / Nice Story / Nice Presentation）；附可下载的《博士生科研入门辅导》PDF |
| 02 Idea Generation | Idea 的生命周期与能力匹配、"更高更快更强"5 维思路、颠覆式创新 |
| 03 Paper Writing | 科研论文全流程、Introduction 思考模型、技术类 / Benchmark 类论文模板、写作 Checklist |
| 04 Scientific Plotting | 动机图 / 总览图 / 实验图的设计范式与绘图 Checklist |
| 05 Vibe Research | Vibe Research / Coding 入门与实战经验分享 |
| 06 Case Studies | Alpha-SQL（ICML'25）、AFlow（ICLR'25）、LEAD（VLDB'26）的写作剖析 |

`handbook-en/` 是同样的六章英文镜像。

### 3. 时间线与外部合作（README News）

- **2026-04-19** 项目发布：七个锚点技能 + handbook；**2026-05-21** Star 突破 1,000（发布第 32 天）。
- **2026-07-03** 新增 `drawio-reconstruction`；受邀在 **2026 中国智能体大会** Skill 会场分享。
- **2026-07-10** **v2.1**：新增 `paper-writer` / `paper-polish` / `deep-research`，`intro-drafter` 升级为正文输出。
- **2026-07-15** 与**豆包**合作，作为**豆包·办公模式**的核心科研技能，驱动"科研评估"与"学术创作"两大官方能力。
- **2026-09-21** 与**百度**合作，作为专家嵌入**库库AI**平台，以"计算机科研专家 骆教授"形象呈现。
- **2026-11-05** 续作 **Ducktor** / **DeepSeek-Ducktor** 发布。

## 使用前需要注意

- **许可证是非商业的**：**CC BY-NC-SA 4.0** 含 **NonCommercial** 限制。**商业用途需另行获得授权**；分享与改编需**注明出处**并采用**相同方式共享**。GitHub 页面与 shields.io 都**识别不出**该许可（渲染为 `not identifiable by github`），**必须以 `LICENSE` 文件为准**——这类"识别失败"是引用许可证时最容易出错的地方。
- **不要把这个仓库装成一个技能**：README 与 `llms.txt` 都强调**逐目录安装**（"Treat each directory containing SKILL.md as a separate skill"），仓库根目录不是技能。
- **技能清单有两套口径**：README 表格 **12 个**（含 `rebuttal-guidance`），`llms.txt` 写 **11 个**。**引用数量时注明来源**。
- **它是"方法论"而非"保证"**：仓库自述项目**仍处早期阶段**（"这个项目仍处在早期阶段"）；技能承诺的是**流程约束**（证据门控、引用核验、逐条标出改动），**不能替你完成判断**——README 本身就写"**卓越的科研成果，无法仅靠『万能 Prompt』催生**"。
- **教程里的观点是作者的**：`handbook/` 是作者个人的科研经验总结（含合作者与学员的实战分享），属**经验性内容**，不等于通用规范。
- **维护状态**：0 Release，**用即跟 `main`**；`pushedAt` 2026-11-05（约一周前，仍在更新）；贡献者仅 5 人，开放 Issue 1 条；**Star 与 ungh 镜像略有差异**，引用数字须注明来源与日期。
- **我只读了 README、`llms.txt`、`LICENSE` 与文件树**：**未安装任何技能、未在助手内运行、未读 `handbook/` 正文、未审计各 `SKILL.md` 全文**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 需要一套**从选题到 Rebuttal 的科研写作流程框架**时（先读 `handbook/` 建框架，再用 `skills/` 落地）。
- 想让 AI 帮忙**写论文正文 / 润色 / 自查**，且**要求"不许编造、每个声明可溯源"**时——参考它的"证据门控"设计。
- 想参考"**把个人领域经验产品化成一整套 Agent 技能 + 配套教程**"的做法时（含 `llms.txt` 这种给 Agent 读的入口文件）。
- 想研究"**社区方法论项目如何与商业平台合作落地**"（豆包办公模式 / 百度库库AI）时。
- 想对比其它"技能形态"的项目时：[book-to-skill](book-to-skill-把技术书转成ClaudeCode技能.md)（把技术书编译成技能）· [argo](argo-给Agent用的搜索与深度研究工具.md)（给 Agent 的搜索与深度研究后端）· [github-skill-forge](github-skill-forge-把GitHub仓库转成Trae技能.md)（把任意仓库转成技能）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都是"把知识编译成技能"，知识源不同**：[book-to-skill：把一本技术书编译成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇把**技术书**拆成"SKILL.md 核心模型 + 按需加载的章节文件"；本篇把**作者的科研经验**拆成一组并列技能 + 一套教程。可对照"**知识源是书**"与"**知识源是人的经验**"。
- **都服务于"深度研究"，形态不同**：[argo：给 Agent 用的搜索与深度研究工具](argo-给Agent用的搜索与深度研究工具.md)——那篇是**搜索与证据评分后端**（200+ 源、RRF 融合、证据快评）；本篇的 `deep-research` 技能是**流程与写作规范**（多视角检索 + 逐条核验 + MECE 综合）。可对照"**提供证据**"与"**约束怎么用证据**"。
- **都是"造技能的技能"的邻居**：[github-skill-forge：把 GitHub 仓库转成 Trae 技能](github-skill-forge-把GitHub仓库转成Trae技能.md)——那篇解决"**怎么把外部仓库变成技能**"，本篇是"**人工把方法论写成技能**"。一个是机器搬运，一个是人工蒸馏。
- **同属"把科研流程做成技能"，组织方式不同**：[nature-skills：面向科研的 Nature 技能库](nature-skills-面向科研的Nature技能库.md)——那篇是 **23 个 Nature 风格技能**（`skills/nature-<topic>/SKILL.md`，**README 夹带商业推广，须与技术内容分开**）；本篇是"**六章教程 + 12 个技能**"的双轨制（强调"防幻觉"写进技能定义）。可对照"**技能数量堆叠**"与"**方法论文档 + 少量技能**"两种做法。
- **同属"面向科研 / 学习场景的 Skill"**：[learn-from-materials：把 PDF 与书变成可测验学习网页的技能](learn-from-materials-把PDF与书变成可测验学习网页的技能.md)——本篇偏**科研 / 导师经验**（六章教程 + 12 个技能、证据门控），那篇偏**把一份材料读透并结构化**（可追溯知识库 + 学习网页 + 覆盖审计）。可对照"**方法论 + 导师经验**"与"**单份材料精读**"。
- **都是"给科研加一套 Agent 技能 / 工具"**：[NodeGraph：把研究论文变成可验证知识图谱的 VS Code 扩展](NodeGraph-把研究论文变成可验证知识图谱.md)——本篇把**科研指导经验**炼成技能（方法论技能），那篇把**读论文本身**变成图谱工作流（阅读与结构化工具）。可对照"**方法论技能**"与"**阅读与结构化工具**"。
- **都服务"深度研究"，一个管流程、一个管溯源到原文**：[imozuru：芋づる式地把未知领域挖到原文的 Claude Code 技能](imozuru-逐条溯源到原文的ClaudeCode技能.md)——本篇的 `deep-research` 技能给的是**流程与写作规范**（多视角检索 + 逐条核验 + MECE 综合）；imozuru 给的是"**不摘要、只标原文位置**"的溯源方法（图书馆情报学方法论 + 引用逐字机械核对）。两条都强调"结论必须挂回证据"，可对照"**流程约束**"与"**溯源到原文**"。

## 参考来源

- 仓库主页与 README（227 行；核验日期 2026-11-13）：https://github.com/HKUSTDial/Supervisor-Skills
- 英文摘要与技能清单：`llms.txt`（4,452 字符，标注 **v2.1.0**）· `README.en.md`
- 许可证：`LICENSE`（**CC BY-NC-SA 4.0**，1,018 字符；**GitHub 与 shields.io 均识别不出**）
- 技能目录：`skills/` 108 个条目（12 个 `SKILL.md`）；教程：`handbook/` 30 个条目 + `handbook-en/` 18 个条目
- 变更记录：`CHANGELOG.md`；README "News" 节记录 2026-04-19 至 2026-11-05 的时间线
- 作者主页：https://luoyuyu.vip/
- 完整文件树（186 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**略有差异**）；Issue 数 / 贡献者数 / 主语言取自 shields.io（同日）

以上信息核验于 2026-11-13。**未安装任何技能、未在 Claude Code / Codex 中运行、未跑任何一次评估或写作、未读 `handbook/` 正文、未审计各 `SKILL.md` 全文**；能力描述来自 README 与 `llms.txt`，**未逐一验证**。**许可证为非商业（CC BY-NC-SA 4.0），商用前请确认授权。**
