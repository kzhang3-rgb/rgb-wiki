# nature-skills：面向科研的 Nature 风格技能库

> 一句话定位（README 自述）：一套**面向全球 AI 学者的可复用科研技能库**——按 `SKILL.md` 组织、把"读论文 / 润色 / 写作 / 审稿 / 返修 / 绘图 / 文献检索 / 专利"等科研流程**封装成 23 个可安装技能**，装到 Codex、Claude Code、Chatbox 等 Agent 里，用自然语言触发。最值得记的是三点：① 技能覆盖面**从读研到投稿再到返修**，还延伸到**专利草稿**与**社科写作**；② **README 里混有大量商业推广内容**（GPT / Claude 代充、知识星球、商务合作邮箱、自有网页版），与"技术内容"需要分开看；③ 作者在 README 里**自述其技能设计被谷歌 DeepMind 借鉴并催生了 Science Skills**——属**自述、未独立核实**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 `README.md` 全文（672 行，含技能索引表与安装说明）、`LICENSE`（Apache-2.0 全文）；核对元数据；**未 clone、未安装任何技能、未运行 `npx skills` / 安装脚本、未打开 `natureskills.cn` 或任一技能详情页、未访问抖音 / 知识星球 / 代充站点、未核实任何自述与效果**）
- 适用范围：需要 **Node.js 18+**（走 `npx skills`）或一个稳定 clone；可对接 **Codex · Claude Code · Chatbox · OpenClaw · OpenCode · Hermes** 等；部分技能另需 **Python / R / 浏览器 / MCP** 等运行依赖（需单独配置）

## 核心结论

1. **事实：这是一套 23 个可触发技能的组织化仓库。** 每个技能是 `skills/nature-<topic>/` 下的独立目录，靠 `SKILL.md` 的 frontmatter（`name`、`description`）+ 规则 + 工作流驱动；`skills/nature-shared/` 是**共享支持包**（被多个技能引用，但**不计入技能索引**，也不独立触发）。
2. **事实：覆盖面从"读"到"写"到"投"到"返修"，还外挂专利与社科。** 技能索引表把每个技能标了状态（`Stable` / `Beta` / `Draft`）：`Stable` 有 `nature-figure`、`nature-polishing`、`nature-ref-verifier`、`nature-literature-pipeline`；其余多为 `Beta` / `Draft`（`Draft` 含义为"规则已定义但未在真实案例上测试"）。
3. **事实：README 夹带商业推广，需与技术内容分开看。** 第 1 章"项目发起人与运营信息"直接放**自有 GPT / Claude 代充与成品号站点**、**知识星球**、**商务合作邮箱**，顶部还推**自有网页版 `natureskills.cn`**；这些属**运营 / 变现内容**，与技能本身是两回事。
4. **事实：许可证为 Apache-2.0。** 仓库根目录有完整 `LICENSE`（Apache License 2.0），允许商用与修改，但**需保留版权与许可声明**。
5. **自述（未独立核实）：作者称其技能设计被 DeepMind 借鉴。** README 第 3.1 节写"我设计的 Nature Skills 被谷歌 DeepMind 关注并借鉴……推出了 Science Skills"。这**属于作者单方陈述**，本笔记**未做任何独立核实**，不应作为事实引用。

**个人判断（未验证）**：这个仓库真正的价值点是"**把科研工作流拆成可被 Agent 读的规则包**"这套**组织方式**（`SKILL.md` + 双语 README + 状态标签 + 贡献流程），技术内容本身（各技能效果）**未经我验证**；而 README 的推广密度较高，引用时**必须把"技能清单"与"运营信息"分开**，避免把商业推广当成项目背书。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Yuan1z0825/nature-skills](https://github.com/Yuan1z0825/nature-skills) |
| 描述 | 自述为"面向全球学者的科研 Skill 库"，收录可复用科研技能，强调真实问题、可验证工作流与可直接使用的产物（如 `.svg`、`.pptx`、`.docx`） |
| 许可证 | **Apache-2.0**（根目录 `LICENSE`，Apache License 2.0 全文；shields 报 `Apache-2.0`） |
| 主语言 | **文档型仓库**（`SKILL.md` / README / 规则与脚本混合）；**本笔记未取到单一主语言占比**，从结构看以 Markdown 指令包为主 |
| 关注度 | **53,404 Star / 2,774 Fork / 66 Watcher**（**页面值**，2026-11-13）；开放 Issue **1**、贡献者 **40**（同日） |
| 维护状态 | **2026-04-24 创建**；页面 `updatedAt` 2026-11-13、ungh `pushedAt` **2026-11-13**（**活跃**）；默认分支 `main`；**0 条 Release**（靠 clone / `npx skills` 分发，不走 Release） |
| 体积 | `main` 分支 **895 个条目**；核心为 `skills/` 下的 23 个技能目录 + `skills/nature-shared/`、`docs/`、`scripts/`、`assets/` 等 |
| 站点 / 社区 | 自有网页版 <https://natureskills.cn>（内置部分技能）、文档页 <https://yuan1z0825.github.io/nature-skills/>、英文 README（`README_EN.md`） |

## 23 个科研技能

以下为 README 技能索引表（核验日期 2026-11-13）。**状态**列取自 README 自标（`Stable` 已在真实学术内容上验证；`Beta` 已在示例上测试；`Draft` 规则已定义但未在真实案例上测试）。

| 技能 | 状态 | 用途（README 摘要） |
| --- | --- | --- |
| `nature-figure` | Stable | 投稿级 Python / R 科研图工作流：多面板证据架构、子图对齐门、PDF 文字/图形碰撞审计 |
| `nature-polishing` | Stable | 润色 / 重构 / 翻译为 Nature 风格英文，并扫描术语、单位、数值精度与声称漂移 |
| `nature-writing` | Draft | 起草 Nature 风格手稿章节并重建论文论证 |
| `nature-reviewer` | Draft | 审稿人视角模拟评审，输出三份互盲 reviewer reports 与分级意见 |
| `nature-citation` | Beta | 检索限定 Nature / CNS 系列支撑文献，导出 ENW / RIS / Zotero RDF |
| `nature-data` | Draft | 准备 Data Availability 声明、数据仓储方案与 FAIR 检查 |
| `nature-statistics` | Draft | 审查 / 改写统计报告：实验单位、重复数、p 值、多重比较、效应量、置信区间 |
| `nature-reader` | Beta | 生成带来源锚点、图文对应、公式渲染与中英对照的全文 Markdown reader |
| `nature-paper-xray` | Beta | 精读论文到能复述作者推理：还原出发点、区分承重设计与装饰、核对证据边界 |
| `nature-paper-trans` | Beta | 英文论文 PDF 逐页生图翻译，交付保留源页尺寸的中文图片型 PDF |
| `nature-paper-card` | Beta | 精读单篇论文生成 01–16 节 Paper Card，覆盖方法逻辑与证据链 |
| `nature-response` | Beta | 解析返修邮件，为互盲审稿人生成独立回复、cover letter、标红稿与 LaTeX 模板 |
| `nature-paper2ppt` | Beta | 从科研论文生成中文 PPTX 文献汇报 deck |
| `nature-paper2word` | Beta | 将 Markdown 编译为符合期刊 / 学校规范的 Word（可编辑公式、三线表、交叉引用） |
| `nature-image2ppt` | Beta | 将幻灯片图片 / 扫描 PDF / 图片型 PPTX 重建为对象级可编辑 PowerPoint |
| `nature-paper-to-patent` | Beta | 从论文 / 技术报告生成有证据约束的中国发明专利草稿，支持查新与技术交底书 |
| `nature-ref-verifier` | Stable | 参考文献多源交叉验证：逐字段对比，标记卷年冲突、作者编造、页码偏差 |
| `nature-academic-search` | Beta | 多源文献检索、引用核验、严格他引审计、引用者画像与参考文献管理 |
| `nature-downloader` | Beta | 通过图书馆入口、Chrome 登录态与开放获取路径获取学术全文 / PDF |
| `nature-literature-pipeline` | Stable | 自动化文献发现管线：多源检索、六维评分、精读推送与本地归档 |
| `nature-experiment-log` | Draft | 标准化记录实验材料，生成带 YAML frontmatter 的 Obsidian 实验日志 |
| `nature-proposal-writer` | Beta | proposal-first 科研写作状态机（frontmatter 名显示为 `researchwrite`） |
| `nature-social-science` | Draft | 社科学术写作与选题论证工作台：选题、文献版图、证据路线、新颖性审计 |

## 安装与集成

README 给出多条安装路径，**细节以 README 为准**，此处只记结构：

- **`npx skills`（推荐按需）**：需 Node.js 18+。`npx skills add Yuan1z0825/nature-skills --list` 列出可装技能，再按名字安装；单独使用 `nature-reader` / `nature-paper2ppt` / `nature-polishing` / `nature-writing` 时**需同时选择 `nature-shared`**。
- **Claude Code**：推荐保留一个稳定 clone，用 subagent wrapper 或 slash command 指向真实的 `skills/*/SKILL.md`，**不复制成碎片**；可选配 `SessionStart` 钩子 + `scripts/autoupdate-skills.sh` 自动更新（自带 1 小时节流、断网自动跳过）。
- **Codex**：用 `scripts/update-codex-skills.sh --pull` 同步整组技能并 `diff` 校验；`--check` 验证一致性；`--prune` 才会清理上游已删除的旧技能目录。
- **Chatbox**：图形界面内"从 GitHub 安装"，选技能（含 `nature-shared`）后启用；需支持工具调用的模型并开"智能体模式"。
- **其它 Agent**（OpenClaw / OpenCode / Hermes）：见仓库 `docs/open-source-agent-frameworks.md`。
- **可选运行依赖**：如 `nature-paper-to-patent`、`nature-academic-search` 的 Python / MCP 依赖需**单独 `pip install`**；启用国知局检索还要 `python -m playwright install chromium`；`nature-academic-search` 的 MCP 需配 `PUBMED_EMAIL`，Scopus / ScienceDirect 凭据**不应写进仓库**。

## 使用前需要注意

- **推广信息要与技术内容分开（重要）**：README 第 1 章含**自有 GPT / Claude 代充与成品号站点**、**知识星球**（"以加入知识星球代替打赏"）、**商务合作邮箱**，顶部与正文还推**自有网页版 `natureskills.cn`** 与**抖音视频教程 / 微信 / 交流群**。这些是**作者的运营与变现内容**，**与技能库本身无关**；引用时应明确区分，**不要把推广当作第三方认可或质量背书**。
- **"被 DeepMind 借鉴"是作者自述，未独立核实。** README 3.1 节称其技能被 DeepMind 关注并借鉴、促成了 Science Skills——**本笔记无法核实其真伪，请勿当作事实引用**。
- **技能状态参差不齐**：多数技能自标 `Beta` / `Draft`，作者也说明 `Draft` = 规则已定义但**未在真实案例上测试**；**本笔记未逐一验证任何技能的实际效果**。
- **按需安装、别漏 `nature-shared`**：单装 `nature-reader` / `nature-polishing` / `nature-writing` / `nature-response` / `nature-paper2ppt` 时**必须一并选 `nature-shared`**，否则可能缺依赖；技能名与目录名**不一定一致**（如 `nature-proposal-writer` 显示为 `researchwrite`）。
- **安装 ≠ 可用**：README 明说"安装的是**指令和配套文件**"，**Python / R、PDF/PPTX 工具、浏览器、MCP 服务等运行依赖仍需按各技能说明单独配置**；有的还依赖外部凭据或收费服务。
- **涉及第三方版权与合规**：`nature-downloader` 走"图书馆入口 / 登录态 / 开放获取路径"，`nature-paper-to-patent` 走专利检索——**使用前需自行确认授权与合规边界**。
- **Apache-2.0 的边界**：可商用、可修改、可再分发，但须**保留版权与许可声明、说明改动**；**许可证不覆盖第三方商标与 README 中推广内容所涉服务**。
- **我只读了 README 与 LICENSE**：**未 clone、未安装、未运行任何脚本、未访问其网页版与推广站点、未核实任何数字或效果声明**。

## 什么时候会再看

- 想给**科研工作流**找现成的 Agent 技能（读论文 / 润色 / 审稿 / 返修 / 绘图 / 文献检索）时。
- 想参考"**把一整套专业流程拆成多个 `SKILL.md` 包、并配套双语 README 与状态标签**"的组织写法时。
- 想学习"**同一批技能如何同时适配 Codex / Claude Code / Chatbox 等不同 Agent**"的安装与 wrapper 设计时。
- 想研究"**开源科研技能库的运营与变现如何与 README 共存**"，或需要一个"**如何把技术内容与推广信息分开阅读**"的实例时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"科研 / 学术"方向的 Agent 技能**：[Supervisor-Skills：把博导经验炼化成 AI 科研副导师技能](Supervisor-Skills-把博导经验炼化成AI科研副导师技能.md)——那篇把**博导经验**封装成科研副导师；本篇把**Nature 期刊工作流**封装成 23 个技能。一个偏"导师视角的方法论"，一个偏"期刊流程的工具箱"。
- **同为"把资料转成 Agent 技能"**：[book-to-skill：把技术书转成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇把**一本书**转成技能；本篇是**一整套技能库 + 安装框架**，可对照"单技能生成"与"多技能组织 + 跨客户端分发"。
- **同为"写作质量"方向**：[no-ai-slop：去掉写作里的 AI 腔技能](no-ai-slop-去掉写作里的AI腔技能.md)——那篇针对**中文写作的 AI 腔**；本篇的 `nature-polishing` 针对**学术英文与术语 / 数值一致性**，可对照两种"文本质量把关"的落法。
- **同类"技能安装 / 索引"基础设施**：[everything-ai-coding：聚合编程 AI 扩展资源的索引与一键安装库](everything-ai-coding-聚合编程AI扩展资源的索引与一键安装库.md)——可与本篇的 `npx skills` / 脚本安装方式对照。
- **同属"面向科研 / 学习场景的 Skill"**：[learn-from-materials：把 PDF 与书变成可测验学习网页的技能](learn-from-materials-把PDF与书变成可测验学习网页的技能.md)——本篇偏**科研工作流**（读 / 写 / 投 / 返修的 23 个技能），那篇偏**把一份材料读透并结构化**（可追溯知识库 + 学习网页 + 覆盖审计）。可对照"**科研流程工具箱**"与"**单份材料读透**"。
- **同属"面向科研的 Skill / 工具集"，一进一出**：[NodeGraph：把研究论文变成可验证知识图谱的 VS Code 扩展](NodeGraph-把研究论文变成可验证知识图谱.md)——本篇是**写作 / 图谱 / 数据到发表**的技能库（偏**产出端**），那篇是**论文理解与可验证笔记**（偏**输入 / 理解端**）。两者都服务科研流程，可对照这两个方向。

## 参考来源

- 仓库主页与 `README.md`（672 行；核验日期 2026-11-13）：https://github.com/Yuan1z0825/nature-skills
- 技能索引与状态标签：README 第 6 节（23 个技能表，2026-11-13）
- 安装说明：README 第 5 节（`npx skills` / Claude Code / Codex / Chatbox / 其它 Agent）
- 许可证：根目录 `LICENSE`（**Apache-2.0** 全文）；徽章 `<a href="LICENSE">license-Apache-2.0</a>`
- 推广 / 运营信息（**属作者自述，需与技术内容分开**）：README 第 1 章（GPT / Claude 代充站点、知识星球、商务合作邮箱）、自有网页版 <https://natureskills.cn>、抖音 / 微信 / 交流群
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 `main`）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13）；开放 Issue 1、贡献者 40（同日）

以上信息核验于 2026-11-13。**仅阅读 `README.md` 与 `LICENSE`，并核对文件树与元数据**；**未 clone、未安装任何技能、未运行 `npx skills` 或安装脚本、未访问其网页版与推广站点、未核实 README 中的数字、效果与"被 DeepMind 借鉴"等自述**。
