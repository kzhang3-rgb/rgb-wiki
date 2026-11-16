# cs249r_book：ML Systems 开源教材四卷与配套工具

> 一句话定位（README 自述）：哈佛 CS249r 课程衍生的**机器学习系统（ML Systems）开源教材系列**——"**关于机器学习系统如何被构建、以及为什么会这样构建**"的四卷教材，外加**可动手构建 / 测量 / 授课的配套工具**：`TinyTorch`（从零写 ML 框架）、`MLSys·im`（系统建模模拟器）、`StaffML`（9000+ 系统设计题）、`Labs`（Marimo 交互笔记本）、`Kits`（真硬件部署）、`Slides` / `Instructor Hub`（授课材料）。最值得记的三点：① **默认分支是 `dev` 不是 `main`**（`main` 只放已上线到 mlsysbook.ai 的内容，发布流程按 Release 把 `dev` 提升到 `main`）；② 这是一个**单仓库内多种许可证并存**的项目，教材是 **CC BY-NC-ND**、很多配套却是 MIT / Apache / AGPL——**逐组件授权**，不看目录层级极易误读；③ 作者是 **Vijay Janapa Reddi**（哈佛），Vol I 已由 **MIT Press 出版在即**，Vol III / IV 仍是**明文标注的开放草稿**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 `README.md` 全文（1,089 行，含四卷表、配套组件表、逐组件许可表、FAQ 与贡献者名单）、根目录 `LICENSE.md`（631 B，CC BY-NC-SA 4.0 + 说明）与许可表原文；核对元数据；**未 clone、未构建、未运行 TinyTorch / MLSys·im / Labs / StaffML、未阅读任何一卷正文 PDF、未访问 mlsysbook.ai**）
- 适用范围：想系统学习"**模型之外**"的机器学习系统（内存 / 带宽 / 算力 / 网络 / 能耗 / 失败率等约束）的自学者、转 ML 基础设施的软件工程师、硬件架构师、设计课程的教师；README 自述**只需会 Python + 基础 ML 概念**，无需先修计算机体系结构 / 网络 / 分布式系统

## 核心结论

1. **事实：这是"四卷教材 + 一整套配套工具"的单仓课程项目。** 教材四卷分别是 Vol I《Introduction to Machine Learning Systems》（**已发布**，MIT Press 出版在即）、Vol II《Scaling Machine Learning Systems》（**预览，活跃修订中**）、Vol III《Agentic Machine Learning Systems》（**开发中，改动频繁**）、Vol IV《Physical AI》（**开发中**）；四卷**只扩大系统边界，不降低深度**。
2. **事实：默认分支是 `dev`，不是 `main`（这点很关键）。** README 明写"开发发生在 `dev`（即本页所示），`main` 保存 [mlsysbook.ai](https://mlsysbook.ai) 上已上线的内容，发布流程按 Release 把 `dev` 提升到 `main`"。**clone / 引用 / 提交前先确认目标分支**，直接看 `main` 可能拿不到最新材料。
3. **事实：单仓库内多种许可证并存（逐组件授权）。** 根目录 `LICENSE.md` 是 **CC BY-NC-SA 4.0**，但 README 明确"**每个组件按自己的许可证发布，各目录内的许可证文件（如 `tinytorch/LICENSE`、`staffml/app/LICENSE`）才是权威**"；shields 对此报 **`not identifiable by github`**。**教材是 CC BY-NC-ND（禁止演绎），而不少配套是 MIT / Apache-2.0 / AGPL-3.0**——见下方逐组件许可表。
4. **事实：配套工具覆盖"读—建—测—教"闭环。** `TinyTorch`（20 个渐进模块从张量写到 transformer）、`MLSys·im`（教材所有数值的**唯一来源**，规格追溯到引用来源）、`StaffML`（9000+ 系统设计题含模拟面试）、`Labs`（Marimo 浏览器笔记本）、`Kits`（Arduino / Seeed / Grove / Raspberry Pi 部署）、`Slides`（每章 Beamer）、`Instructor Hub`（多门课大纲）。另有更早期实验 `SocratiQ`（AI 导师）与 `MLPerf EDU`（教学基准套件）。
5. **事实：作者公开承认广泛使用 AI 工具，并声明责任归属。** FAQ 专列"Do you use AI tools?"——作者称用生成式 AI **写代码、综述文献、起草段落、产出图表、审计一致性**，但**"每个数字都由代码计算"、有自动检查、由同行与学生复核**，且**决定与错误都归作者自己**；Vol III / IV 未经课堂与同行评审故标为草稿。每卷含一份完整的"Note on AI Assistance"。

**个人判断（未验证）**：这个仓库对"**AI 工程（AI engineering）**"这一提法是当作独立学科来写的——README 反复强调"**约束驱动架构（constraints drive architecture）**"，并用"每个数字都由代码算出"来对抗 LLM 时代教材容易出现的"看起来对其实错"。**但四卷只有 Vol I 相对稳定，Vol III / IV 明文草稿**；引用教材内容前**必须逐组件看 LICENSE、并确认卷别状态**，否则很容易踩到"非商业 / 禁止演绎"或"内容仍在改"的坑。以下内容均来自 README 自述，**本笔记未阅读任何正文、未运行任何工具**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [harvard-edge/cs249r_book](https://github.com/harvard-edge/cs249r_book) |
| 描述 | 自述为开源教材系列："关于机器学习系统如何被构建、以及为什么会这样构建"，并配套"用于构建、测量与教学"的工具；作者 Vijay Janapa Reddi（哈佛） |
| 许可证 | **逐组件授权（单仓库内多种许可并存）**：根目录 `LICENSE.md` = CC BY-NC-SA 4.0；各组件以**各自目录内的许可证文件为准**。shields 报 `not identifiable by github`（详情见下表） |
| 主语言 | **多语言混合**：教材为 LaTeX / Markdown（`books/`），配套为 Python（`tinytorch` / `mlsysim` / `staffml`）、Jupyter / Marimo 笔记本、Beamer 幻灯片等；**本笔记未取到单一主语言占比** |
| 关注度 | **30,171 Star / 3,741 Fork / 212 Watcher**（**页面值**，2026-11-13）；开放 Issue **5**、贡献者 **115**（同日） |
| 维护状态 | **2023-09-06 创建**；页面 `updatedAt` 2026-11-12、ungh `pushedAt` **2026-11-12**（**活跃**）；**默认分支 `dev`**（非 `main`）；**30 条 Release** |
| 体积 | `dev` 分支 **24,545 个条目**（教材 + 配套 + 贡献者数据 + CI 等的超大仓库）；另有 `README/` 多语言版（`README_zh.md` / `README_ja.md` / `README_ko.md`，README 自述其翻译**跟进的是较早版本**） |
| 站点 / 课程 | 教材与工具入口 <https://mlsysbook.ai>；课程自 **2023 年哈佛 CS249r** 开设，README 自述已被 UC Berkeley、Columbia、Georgia Tech、IIT Bombay 等多校采用；目标"2030 年服务一百万学习者" |

## 教材与配套结构

### 1. 四卷（README 自述）

| 卷 | 主题 | 工作单位 | 核心系统问题 | 状态 |
| --- | --- | --- | --- | --- |
| Vol I | Introduction to Machine Learning Systems | 单机上的模型 | 智能如何在**单台机器**上高效执行？ | **已发布**，MIT Press 出版在即 |
| Vol II | Scaling Machine Learning Systems | 机群（fleet） | 智能如何跨**分布式集群 / 数据中心**扩展？ | **预览**，活跃修订中 |
| Vol III | Agentic Machine Learning Systems | 轨迹（trajectory） | 智能在**长时程自主行动**时如何被治理？ | **开发中**，改动频繁 |
| Vol IV | Physical AI: ML Systems That Sense and Act | 物理动作 | 智能如何**安全地作用于物质与物理系统**？ | **开发中**，改动频繁 |

README 明写 Vol III / IV 是开放草稿，"**请暂不要引用或据此授课**"。

### 2. 配套组件（README 自述）

| 组件 | 做什么 | 状态 |
| --- | --- | --- |
| **Textbook** | 四卷教材，建立从单机到机群、Agent、物理系统的心智模型 | Vol I 已发布 |
| **Tiny🔥Torch** | **从零构建 ML 框架**，20 个渐进模块（张量 → transformer） | 已发布 |
| **MLSys·im** | 对课堂无法实跑的大规模系统**建模内存 / 算力 / 网络 / 能耗**；教材所有计算都由它承载 | 已发布 |
| **Hardware Kits** | 把模型部署到 **Arduino / Seeed / Grove / Raspberry Pi**，带真实内存 / 功耗 / 延迟约束 | 已发布 |
| **Labs** | **Marimo** 交互笔记本，浏览器内运行：先预测、再改参数、看哪里崩 | 早期发布，公开构建 |
| **StaffML** | **9000+ 系统设计题**（云 / 边缘 / 移动 / TinyML），含练习与模拟面试 | 早期发布 |
| **Instructor Hub** | 每门课一份大纲 + 每周阅读与幻灯片（introductory / scaling / TinyML / Edge AI / Physical AI(草案) / Agentic AI(筹备中)） | 已发布 |
| **Lecture Slides** | 每章一套 **Beamer** 幻灯片，统一主题、易改样式 | 已发布 |
| **Newsletter** | 新章节 / 发布 / 社区动态 | 持续 |

另有更早期实验：**`SocratiQ`**（`socratiq/`，给静态学习站点加引导阅读 / 测验 / 间隔重复的 AI 导师）与 **`MLPerf EDU`**（`mlperf-edu/`，对齐 MLCommons MLPerf 的教学基准套件）。

### 3. 逐组件许可表（README 原文，核验日期 2026-11-13）

README 明说"**这是多组件仓库，每个组件按自己的许可证发布；各目录内的许可证文件（如 `tinytorch/LICENSE`、`staffml/app/LICENSE`）才是权威**"。

| 组件 | 路径 | 许可证 | 含义（README 摘要） |
| --- | --- | --- | --- |
| 教材 Textbook | `books/` | **CC BY-NC-ND 4.0** | MIT Press 版所用许可。**署名 + 非商业**分享；**修改版（含翻译）需获授权** |
| Labs / Kits / Slides / Instructor Hub | `labs/` · `kits/` · `slides/` · `instructors/` | **CC BY-NC-SA 4.0** | **非商业**分享与改编，**署名 + 相同方式共享** |
| TinyTorch | `tinytorch/` | **MIT** | 宽松许可，可**商用**、修改、再分发 |
| MLSys·im | `mlsysim/` | **Apache 2.0** | 宽松许可，含**显式专利授权** |
| StaffML | `staffml/app/` | **AGPL v3** | 强 copyleft，**部署服务的修改须公开**；**可向作者获取商业许可** |
| StaffML 题库 | `staffml/vault/questions/` | **CC BY-NC 4.0** | 研究 / 教育用途，**商业使用需授权** |
| TinyDigits 数据集 | `tinytorch/`（README 链 `tinytorch/LICENSE`） | **BSD 3-Clause** | 宽松许可（与 sklearn 血缘一致） |
| TinyTalks 数据集 | `tinytorch/`（README 链 `tinytorch/LICENSE`） | **CC BY 4.0** | **署名 + 允许商业使用** |

根目录 `LICENSE.md` 正文补充：`books/` 的教材正文由 MIT Press 按 **CC BY-NC-ND 4.0** 发布（各卷版权页有说明），**其余课程材料**由该文件覆盖（即 **CC BY-NC-SA 4.0**）。读者版摘要见 <https://mlsysbook.ai/about/license.html>。

### 4. 分支与发布（README）

- **开发在 `dev`**（README 页面所示即此分支）；`main` 保存已上线到 mlsysbook.ai 的内容；**发布流程按 Release 把 `dev` 提升到 `main`**。

## 使用前需要注意

- **默认分支是 `dev`（重要）**：直接 clone `main` 可能与 README / 站点所示不一致；引用或提交前**先确认分支**。
- **许可证千万别"一刀切"（重要）**：这是**单仓库多许可并存**的项目——**教材 `books/` 是 CC BY-NC-ND**（**禁止演绎，翻译也需授权**），而 `TinyTorch` 是 MIT、`MLSys·im` 是 Apache-2.0、`StaffML` 是 AGPL-3.0。**不能因为"仓库是开源的"就默认整套可自由商用 / 改编**；**逐组件以其目录内许可证文件为准**，shields 也因多许可而报 `not identifiable by github`。
- **只有 Vol I 相对稳定**：Vol II 仍在活跃修订，**Vol III / IV 是明文草稿**（README 请求"暂勿引用或据此授课"）；**本笔记未阅读任何一卷正文**，卷别状态以 README 为准。
- **"数字由代码计算"是作者承诺，未独立验证**：README 称教材每个数值都来自随文发布的 Python 代码、`MLSys·im` 是唯一数值来源、每次提交跑测试校验单位 / 交叉引用 / 记法 / 引用；**这些说法本笔记均未运行、未核验**。
- **AI 使用透明度较高**：FAQ 公开承认用生成式 AI 辅助写代码、综述、起草与绘图，并说明用**不依赖工具的方式复核**（代码算数、自动检查、同行与学生复核）；StaffML **每题记录"是否机器起草、是否经人复核"**。这属作者自述。
- **配套工具的运行边界**：`Kits` 需真实硬件（Arduino / Seeed / Grove / Raspberry Pi），`Labs` 是 Marimo 笔记本，`StaffML` / `MLSys·im` 是软件——**均需自行搭环境，本笔记未安装、未运行**。
- **多语言 README 可能滞后**：`README/` 下的中文 / 日文 / 韩文页面**跟进的是较早版本**（README 自述），信息以英文 README 为准。
- **贡献需相对专业**：项目由 115 位贡献者协作，README 给出各组件贡献指南（教材 / TinyTorch / Kits / Labs·MLSys·im / StaffML / SocratiQ）；门槛高于普通文档仓库。
- **我只读了 README 与根 `LICENSE.md`**：**未 clone、未构建、未运行 TinyTorch / MLSys·im / Labs / StaffML、未阅读正文 PDF、未访问 MLSysBook 站点**。

## 什么时候会再看

- 想系统学"**模型之外**"的 ML 系统知识（内存墙、量化、KV cache、加速器、分布式训练、服务、能耗与碳足迹）时——从这里找**四卷教材**的入口与章节结构。
- 需要"**边读边动手**"的教材时：对照 `TinyTorch`（从零写框架）与 `Labs` / `MLSys·im`（交互与建模）。
- 想参考"**把教材、工具、题目、幻灯片、课程大纲组织进同一个仓库**"这种"教科书即平台"的工程化写法时。
- 需要"**单仓库多许可证并存**"的范例，或想学习"**逐组件授权如何在 README 与各目录 LICENSE 里交代清楚**"时。
- 研究"**如何在 LLM 时代写一本可被引用的教材**"（数值由代码计算、AI 使用的透明声明）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"大模型 / ML 开源教材"**：[Foundations-of-LLMs：大模型基础教材](Foundations-of-LLMs-大模型基础教材.md)——那篇是浙大团队**从模型侧**讲 LLM 基础（含微调、模型编辑、RAG）；本篇是哈佛**从系统侧**讲"模型如何跑起来"（内存、带宽、集群、能耗）。可对照"**讲模型**"与"**讲系统**"。
- **同为"系统设计"方向**：[SystemDesign：系统设计面试内幕指南中译](SystemDesign-系统设计面试内幕指南中译.md)——那篇是**通用系统设计面试**材料；本篇的 `StaffML` 是**面向 ML 系统的 9000+ 设计题**，可对照两者的题目口径。
- **同为"从零手写"的教学项目**：[minimind：从零训练 64M 小 LLM 的中文项目](minimind-从零训练64M小LLM的中文项目.md) 与 [NamiAgent：从零手写 Agent 的中文可运行教程](NamiAgent-从零手写Agent的中文可运行教程.md)——前者从零训一个小 LLM，后者从零手写 Agent；**`TinyTorch` 则从零写 ML 框架**，三者都是"**building from scratch**"路线。
- **同为"复现 / 实验手册"型学习资料**：[agentic-rl-lab：复现 LLM Agent 的强化学习算法实验手册](agentic-rl-lab-复现LLMAgent的强化学习算法实验手册.md)——可对照"实验驱动"与"教材 + 配套工具驱动"两种学习组织方式。

## 参考来源

- 仓库主页与 `README.md`（1,089 行；核验日期 2026-11-13）：https://github.com/harvard-edge/cs249r_book
- 四卷与配套组件表：README《The Four Volumes》《What's in the Repository》（2026-11-13）
- **逐组件许可表与说明**：README《License》节原文 + 根目录 `LICENSE.md`（631 B，CC BY-NC-SA 4.0，并注明 `books/` 为 CC BY-NC-ND 4.0）；读者摘要 <https://mlsysbook.ai/about/license.html>
- 分支说明：README《Branches》（`dev` 为开发分支，`main` 为已上线内容，发布流程按 Release 提升）
- AI 使用说明：README FAQ《Do you use AI tools?》（作者自述）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 `dev`）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**页面与 ungh 均记为 2026-11-12 推送**）；开放 Issue 5、贡献者 115、Release 30（同日）
- 站点入口：<https://mlsysbook.ai>（教材与各配套工具）

以上信息核验于 2026-11-13。**仅阅读 `README.md`、根 `LICENSE.md` 与许可表原文，并核对文件树与元数据**；**未 clone、未构建、未运行 TinyTorch / MLSys·im / Labs / StaffML、未阅读任何一卷正文 PDF、未访问 MLSysBook 站点**。**许可证以各组件目录内的许可证文件为准；本仓库为"单仓库内多种许可并存"，不可按整体一刀切理解。**
