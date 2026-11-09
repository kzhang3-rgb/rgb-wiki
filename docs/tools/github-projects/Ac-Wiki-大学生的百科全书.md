# Ac-Wiki：大学生的百科全书（MkDocs 公益知识站）

> 记录一个"内容即产品"的中文公益知识库：1,898 Star、53 位贡献者，覆盖从入学前到就业的全生命周期；正文 CC BY 4.0、源码 MIT，内容与代码分许可。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README、`mkdocs.yml`、许可文件、目录树与 GitHub API 元数据，未 clone、未构建站点、未逐篇阅读正文）
- 适用范围：中文大学生活 / 学术 / 求职类内容；站点为 MkDocs Material 静态站

## 核心结论

Ac-Wiki 是**一个面向大学生的中文知识共享站**，自述定位为"高等教育学社基础知识开源建设工程"、副标题"青年大学习"，站点在 <https://ac-wiki.org>。

它值得记的地方不在技术，而在**组织方式**：

- **内容按"学生生命周期"而非学科组织**：导航依次是「入学前 → 在校学习 → 校园生活 → 成长通道 → 博客 → 友情链接」，首页标题直接写"大学生的百科全书"。
- **内容与源代码分许可**：**正文（`docs/` 下的 Markdown 与图片）走 CC BY 4.0**，**源码（`src/` 等）走 MIT**——这是内容型仓库里比较少见的清晰切分，值得借鉴。
- **它是 MkDocs 工程**：**仓库根目录没有 `README.md`**，首页实际是 `docs/README.md`（GitHub 会检索 `docs/` 目录作为首页，MkDocs 的 `nav` 也把"首页"指向 `README.md`）。
- **内容"由志愿者驱动"**：README 明确写"由志愿者驱动的公益项目"，单篇内容同样需要按"非一手核实"对待。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Ac-Wiki/Ac-Wiki](https://github.com/Ac-Wiki/Ac-Wiki) |
| 描述 | 高等教育学社基础知识开源建设工程｜青年大学习 |
| 站点 | <https://ac-wiki.org>（`docs/CNAME` 配自定义域名） |
| 许可证 | **内容 `docs/`：CC BY 4.0**（`docs/LICENSE-CC-BY-4.0.md`）；**源码 `src/`：MIT**（根 `LICENSE`，Copyright (c) 2024-2025 Ac-Wiki） |
| 主要语言 | HTML（实为 MkDocs 静态站，正文是 Markdown） |
| 关注度 | **1,898 Star、108 Fork、6 关注、3 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2024-10-19 创建**，最近推送 **2026-10-28**；默认分支 `main`；**有 Discussions** |
| 发行版 | **无任何 Release**（站点由 CI 直接构建发布到 `gh-pages`） |
| 体积 | API `size` 54,685 KB（约 53 MB）；默认分支树 **162 个 blob，实测约 30.3 MB** |
| 分支 | `main`、`gh-pages`（部署产物）、`docusaurus-site`（一条未合并的 Docusaurus 重构线），另有若干 `*-patch` 分支 |
| 贡献者 | **53 人**：`NekoMirra`(92)、`github-actions[bot]`(45)、`penn201500`(33)、`cscnk52`(28)…… 其中含 `github-actions[bot]` / `dependabot[bot]` / `actions-user` 三个机器人账号 |
| topics | `ac-wiki`、`mkdocs`、`mkdocs-material`、`students`、`wiki` |

## 站点结构

内容主体在 `docs/`，按生命周期分区：

| 分区 | 大致内容 |
| --- | --- |
| `campus-life/` | 开学第 0 课、课程与学分、签到考勤、转专业、辅修/第二学位、学生邮箱、图书馆、人际关系、心理健康、医保、奖助学金、助学贷款…… |
| `academic-resources/` | 学术研究与写作、关键概念、毕业论文（理学）、网络资源获取、有用的开源项目、数学学习资源 |
| `general-skills/` | 搜索引擎、工具推荐、**计算机基础（`computer-basic/SurfingTutorial/`，分章）**、考试与竞赛、学生优惠、密码管理、隐私保护、加密通讯、生活技巧 |
| `general-skills/qi-ji-yin-qiao/` | "校园跑""刷课"等**灰色技巧**（只记存在，不展开） |
| `growth-path/` | 就业准备、企业文化、银行账户与信用卡、铁路与航空出行 |
| `community-hub/` | `about-wiki.md`（同类 Wiki 索引）、`contact-us.md`、`CONTRIBUTING.md` |
| `blog/` | 博客（`index.md` + 6 篇 `posts/` + `.authors.yml`） |
| 其他 | `friendly-links.md`（高校课程资源与同类站点互链）、`assets/`、`robots.txt` |

**工程构成**：`mkdocs.yml`（约 238 行，Material 主题）、`requirements.txt`（锁定版本，含 `mkdocs-material==9.6.14`、`jieba` 中文分词、`mkdocs-git-authors-plugin` / `-committers-` / `-revision-date-localized-` / `-glightbox` / `-rss-plugin`）；`src/overrides/`（404 页、`partials/`、自定义 CSS 与 JS）；`.github/` 里有 **Issue 模板**（`Docs-request.yml` / `Website-bug.yml`）与 **5 条 workflow**（`ci.yaml`、`pr-preview.yml` + `pr-preview-cleanup.yml`、`autocorrect-check.yml`、`sync.yml`）。

## 使用前需要注意

- **内容是"整理"，不是"一手核实"**：这是志愿者共建的百科，单条信息（政策、数字、流程）**可能过时或因地而异**；涉及奖助学金、医保、贷款、转专业等**与你切身利益相关的流程，务必以本校教务处/官方文件为准**。这一点与 [cn-math-taxonomy](cn-math-taxonomy-对齐课标的中小学知识图谱与前置诊断.md)、[RUN：海外移居手册](RUN-海外移居手册.md) 属同一类"整理型内容"风险。
- **许可要分清对象**：**引用、改编正文请按 CC BY 4.0 署名**（可商用，但须署名并注明是否修改）；**复用代码按 MIT**。**不要笼统说"这个项目是 MIT"**——那会漏掉正文的 CC BY 4.0。
- **仓库含少量 PDF 二进制**：`docs/general-skills/study/assets/` 下有考研数学 / 英语真题 PDF（如 `2025年考研英语一.pdf`）。量不大，但属于"内容仓库"的常见形态；**这些第三方真题的版权状态本仓库未单独声明**，转载需谨慎。
- **存在"灰色技巧"类目**：`general-skills/qi-ji-yin-qiao/` 收录"校园跑""刷课"等规避校规的做法。**本笔记只记录该目录存在，不复述其做法、不提供使用建议**——这类内容涉及校纪校规，风险自负。
- **不要照搬根目录找 `README.md`**：本项目首页在 `docs/README.md`，`main` 根目录没有 README；写脚本或做抓取时容易扑空。
- **有一条未合并的重构线**：分支 `docusaurus-site` 说明曾尝试迁到 Docusaurus；**技术栈未来可能变**，引用 `mkdocs.yml` 等路径时以你拉到的版本为准。
- **`docs/superpowers/` 是"隐藏"的 AI 协作产物**：目录里有 `plans/` 与 `specs/`（如 `2026-06-18-mkdocs-layout-restructure.md`，967 行，开头即 "For agentic workers: REQUIRED SUB-SKILL..."），但 `mkdocs.yml` 用 `exclude_docs: superpowers/` **把它们排除在站点之外**——**内容留在 Git、不发布**，是"用 Agent 做重构但把计划文档留档"的一种实践。
- 我**未 clone、未构建站点、未逐篇阅读正文**：以上结构来自 `mkdocs.yml`、目录树与元数据；内容的准确性与时效性未验证。

## 什么时候会再看

- 需要**中文的、按生命周期组织**的大学生活 / 学术 / 求职知识入口时。
- 想参考"**内容型开源项目如何做许可分层**"（正文 CC BY 4.0 + 代码 MIT）时。
- 想抄一套**MkDocs Material 的中文站点配置**：`jieba` 分词搜索、博客 / RSS / 标签插件、CI 构建 + PR 预览 + 自动排版检查（Autocorrect）时。
- 想找**同类中文知识站**时：`docs/community-hub/about-wiki.md` 与 `friendly-links.md` 本身就是一份索引（colleges.chat、csdiy.wiki、oi-wiki、ctf-wiki、missing-semester-cn、USTC《Linux 101/201》、高校课程资料仓库等）。
- 想观察"**AI Agent 参与重构，但计划文档只留档不发布**"这种做法时（`docs/superpowers/`）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"中文 + 面向学生 + 内容为整理"的知识项目**：[cn-math-taxonomy：对齐课标的中小学知识图谱与前置诊断](cn-math-taxonomy-对齐课标的中小学知识图谱与前置诊断.md)——那边是 `data/*.json` + 静态站的**结构化内容**（主体 CC BY-NC 4.0、依赖 MIT，**非商业限制**），本篇是 `docs/` 下的**文档内容**（CC BY 4.0 + 代码 MIT，**可商用**）。两篇可对照"内容型项目的许可策略"与"整理内容的核实口径"。
- **同为"整理型内容仓库"，都需按非一手核实对待**：[RUN：海外移居手册](RUN-海外移居手册.md)——一个讲移居流程、一个讲大学生活，都是"把散落经验整理成文"的公益项目，共同风险是**信息过时与地域差异**。
- **同类"开源人生 / 生活指南"**：[HowToLiveBetter：高性价比人生指南](HowToLiveBetter-高性价比人生指南.md)、[up：人生进阶指南](up-人生进阶指南.md)——那些偏"选择与决策"，本篇偏"在校生活与学业办事流程"，同属"把经验写成可检索文档"的路线。
- **"给 AI 助手的元文件"话题的另一种做法**：[agents.md：给编码助手用的 AGENTS.md 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——本项目**不放 `AGENTS.md`**，但把 Agent 生成的 `plans/` 与 `specs/` 留档在 `docs/superpowers/` 并排除出站点，是"协作产物如何落库"的一个对照样本。

## 参考来源

- 项目仓库：<https://github.com/Ac-Wiki/Ac-Wiki>；站点：<https://ac-wiki.org>
- 首页 README：`docs/README.md`；贡献指南：`docs/community-hub/CONTRIBUTING.md`
- 许可：根 `LICENSE`（MIT，源码）+ `docs/LICENSE-CC-BY-4.0.md`（正文 CC BY 4.0）
- 站点配置：`mkdocs.yml`、`requirements.txt`、`src/overrides/`
- 元数据（Star / Fork / 体积 / 分支 / 贡献者 / 时间）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读 README、`mkdocs.yml`、许可文件、目录树与元数据；未 clone、未构建站点、未逐篇阅读正文，未核验任何单条内容的准确性与时效性。
