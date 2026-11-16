# NodeGraph：把研究论文变成可验证知识图谱的 VS Code 扩展

> 一句话定位（README 自述）：**"Turn research papers into verifiable knowledge graphs with AI."**——让 AI Agent（Claude Code / Codex / Cursor / Antigravity）读一篇 PDF，把论文**建成一整张节点图**，并且"**每条论断都能连回它出自的原句**"；不想用 Agent 也可以手建一个空白 `.nodegraph.json`，用**同一块交互画布**自己画。最值得记的三点：① 它的核心不是"总结"，而是 **Original 溯源标签**——点一下原文引用就跳进源 PDF **高亮对应句子**，"一键把 AI 的说法对回论文"；② 它赌的是"**读论文天然是图、不是线性文档**"（论文写作先发散再收敛，拆解就是反向展开）；③ 存储是**磁盘上的纯 JSON**（`.nodegraph.json` + 旁挂图片目录），**无账号、无遥测**，且每张图能**导出为单个自包含 HTML**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（470 行）、`LICENSE`（1,069 字符）与已核实元数据；**未安装扩展、未在 VS Code 中打开任何 `.nodegraph.json`、未跑 `verify-nodegraph.js`、未让任何 Agent 生成图、未导出 HTML、未打开示例 `demo/ex1`–`ex5`**）
- 适用范围：**VS Code 扩展**（Custom Editor API）；UI 为 **React + TypeScript**，渲染依赖 **KaTeX** 与自研 Markdown-lite 渲染器、SVG 走线（A*）、**pdfjs-dist**；构建用 esbuild。标注版本 **2.4.0**

## 核心结论

1. **"结构优先"的读论文方式**（README 的 Motivation）：论文是**先发散再收敛**的产物，真正读懂要把它**拆回发散的想法**——"**那是节点图，不是线性文档**"。所以它不做文档，做**心智图式的结构**。
2. **防幻觉靠"引用可核对"**：Agent 写笔记最大的顾虑是幻觉，**Original 标签**就是对策——每条论断可携带它**依据的原文引用**，右键引用可**打开源 PDF、跳到对应页、高亮匹配句**（`p.N` 按 PDF 自身页码从 1 计）。
3. **同一流程可映射三种对象**（README）：**论文 PDF**（`paper` 提示）、**讲义幻灯片**（`lecture`，按讲义的章节结构当骨架、大量抽图、逐字引用定义）、**代码库**（`code`，节点用 `code` 类型链接指向 `文件:起始-结束`，导出 HTML 后可解析为 GitHub blob URL）。
4. **机器可读规范 + 校验器**：扩展自带 `.agent/nodegraph/SPEC.md`（完整 JSON schema、ID 约定、KaTeX/Markdown 语法规则、生成工作流）与 `.agent/nodegraph/ENVIRONMENT.md`（本机已装的 Python 库与 CLI 工具）；`tools/verify-nodegraph.js` 会**重读每条引用的行区间或 PDF 页**、检查 `internal` 链接目标存在与扇出上限，**失败即非零退出**，"agent 无法谎称自己检查过"。

**个人判断（未验证）**：这个仓库的"想法密度"大于"采用度"。它把**采集（Agent 写图）+ 溯源（原文引用）+ 分发（自包含 HTML）**串成了一条完整闭环，**但采用度极低**（见项目信息），且核心卖点（自动建图、quote-jump、校验器）均**来自自述、未验证**。适合"一个人精读几篇论文"的用法，**是否适合团队协作、大图性能如何，README 未给出证据**（**个人判断（未验证）**）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Jeong-jin-Han/NodeGraph](https://github.com/Jeong-jin-Han/NodeGraph) |
| 描述（原文） | "Turn research papers into verifiable knowledge graphs with AI." |
| 许可证 | **MIT**（`LICENSE` 1,069 字符，"Copyright (c) 2026 Jeongjin Han"） |
| 默认分支 | **`main`** |
| 关注度 | **ungh 镜像 42 Star / 5 Fork / 0 Watcher**；页面值 **42 / 5 / 0**（两口径一致）；shields：开放 Issue **0**、贡献者 **1** |
| 维护状态 | **2026-07-02 创建**；ungh `pushedAt` **2026-11-12**、页面 `updatedAt` 2026-11-12（活跃，但**低关注度**） |
| Release | **0**（无 GitHub Release）；经 **VS Code Marketplace** 分发，仓库内另有打包 `.vsix` |
| 标注版本 | README 徽章 **version 2.4.0** |
| 技术栈 | VS Code 扩展（Custom Editor API）· React + TypeScript · KaTeX · pdfjs-dist · esbuild |
| 存储 | 磁盘纯 JSON（`.nodegraph.json`）+ 旁挂图片目录 `.<graphname>-imgs/`，**无账号、无外部服务** |

## 功能与用法

### 1. 四步上手（README）

1. 从 **VS Code Marketplace** 安装 **NodeGraph**；
2. 右键论文所在文件夹，运行 `NodeGraph: Copy Agent Spec to Workspace`；
3. 把生成的 `.prompt/nodegraph/paper/english.md`（或 `korean.md`）粘进你的 Agent，**填上 PDF 路径**；
4. 打开生成的 `.nodegraph.json`。

### 2. 主要特性（README 摘要）

| 特性 | 说明 |
| --- | --- |
| **富节点内容** | Markdown（GFM）表格、KaTeX 公式、内联图片、可折叠 toggle 段，同一个渲染器 |
| **PDF quote-jump** | 右键原文引用 → 打开源 PDF、跳页、高亮匹配句 |
| **Ctrl+F 搜索** | `Aa` 搜标题 / 内容 / 原文；`#` 按**节点编号**搜（接受 `17`、`17 19`、`17-20`） |
| **节点编号 / 层级折叠 / Levels** | 标题栏显示 `#17`；折叠后代有独立轴向（一层 / 本层 / 其下全部 / 全部）；`1 / 2 / All` 控制显示到第几层 |
| **Outline / Edit this node** | 左侧目录列当前节点的直接子节点；右键一次性把整卡变编辑态（**编辑只经此路径进入**） |
| **跨图链接** | `internal` 链接跳节点，可跨图（`other.nodegraph.json#node_017`），标签自动同步为 `#17 <标题>` |
| **Graph verifier** | `tools/verify-nodegraph.js` 重读引用、查 `internal` 目标与扇出上限，**失败非零退出** |
| **HTML 导出** | 单个自包含交互式查看器（含搜索、高亮、布局） |

### 3. Agent 工作流（README）

`NodeGraph: Copy Agent Spec to Workspace` 会把 `SPEC.md`、`ENVIRONMENT.md` 与多语言提示写入目标文件夹（**一次一文件夹、按点击位置写入**）；典型流程是：命令 → 让 Agent 读 `SPEC.md` + `ENVIRONMENT.md` → 读 / 建 `.nodegraph.json` → 直接改 JSON → 点工具栏 **Reload** 看图更新。**规范要点**：KaTeX 反斜杠在 JSON 里**必须双写**（`\\frac`）、货币美元须转义（`\$` / JSON 里 `\\$`）、骨架节点用 **kernel test**（作者独有的贡献，取 4–8 个）、任何节点**最多 4 个直接子节点**、标题要写成**可能被证伪的断言**而非"结论 / 方法"这类槽位名。仓库自带 5 张**全 Agent 生成**的示例图：`demo/ex1`（Attention Is All You Need）、`ex2`（Point Transformer）、`ex3`（Mooncake）、`ex4`（3D Gaussian Splatting）、`ex5`（一篇哲学论文）。

### 4. 内容语法与文件格式（README）

节点内容支持 GFM 表格、`$...$` / `$$...$$` LaTeX、`**粗体**`、转义字面量与图片 token `[[IMG:filename.png:400x300]]`；图为 `version / title / source / nodeTemplates / nodes / edges / viewport / canvasImages` 结构，节点含 `original`（原文引用 + 定位）与 `toggleItems`。安装方式：Marketplace 搜索 "NodeGraph"，或 `code --install-extension packages/nodegraph-<version>.vsix`，或自建（`npm install && node esbuild.js --production && npx vsce package`）。

## 使用前需要注意

- **极低关注度 + 单一维护者**：**42 Star / 0 Watcher / 5 Fork、贡献者 1 人**（shields）。**自述能力未验证**：README 的 Benchmark（Opus 5 读论文建图，"无需人工清理"）、quote-jump、校验器均**未被我复现**。引用这些能力时应写明"**README 自述**"。
- **Release 为 0，走 Marketplace / `.vsix`**：没有 GitHub Release 可对签名做交叉核对；安装渠道为 **VS Code Marketplace** 或仓库内打包的 `packages/nodegraph-<version>.vsix`（**未验证**其构建产物与源码的一致性）。引用版本时注意 README 徽章标注 **2.4.0**，可能与实际 Marketplace 最新版不同。
- **隐私有明确例外**：编辑器本身**不采集 / 不上传、不用 CDN、无遥测**（KaTeX 从扩展自带资源加载）；**但"用浏览器打开导出的 HTML"会从 CDN 加载 KaTeX**——README 自己写明了这个例外。介意离线时需注意。
- **强依赖 AI Agent 与一堆语法坑**：自动建图依赖 Claude Code / Codex / Cursor / Antigravity 等能读写文件并跑命令的 Agent；KaTeX 反斜杠双写、`$` 与 `|` 的转义规则很多，**与手写 JSON 相比容错低**（**个人判断（未验证）**）。
- **数据只有本地 JSON**：好处是透明可迁移；代价是**没有云同步、没有协作**，图片存在旁挂目录 `.<graphname>-imgs/`，移动 / 分享要注意把 JSON 与图片目录一起带走。
- **我只读了 README 与 LICENSE 及元数据**：**未安装扩展、未打开任何 `.nodegraph.json`、未运行校验器、未让 Agent 生成图、未导出 HTML、未打开示例 demo**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想**精读 / 拆解一篇论文**，并把"每条论断 → 原句"的对应关系固化下来（而非只留一份摘要）。
- 想让 **Agent 直接把 PDF 变成结构化图**，再用**校验器**给它上一道"不许说谎"的闸。
- 想把论文 / 讲义 / **代码库**用同一套节点画布与导出格式来表达（含代码 `行区间` 链接）。
- 想参考"**机器可读 SPEC + 提示词随扩展写入工作区**"这种"让 Agent 读懂扩展约定"的集成方式。
- 想对照其它"**把文本 / 知识做成图谱**"的路线。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"用 AI 把文本变成知识图谱"**：[shiji-kb：用 AI 把《史记》变成可探索的知识图谱](shiji-kb-用AI把史记变成可探索的知识图谱.md)——那篇面向**古籍 / 文言**，本篇面向**研究论文 PDF**；两者都把"线性文本 → 可探索结构"当目标。可对照"**领域语料建库**"与"**单篇论文图谱 + 原文溯源**"。
- **同属"知识图谱 + 结构化知识"**：[cn-math-taxonomy：对齐课标的中小学知识图谱与前置诊断](cn-math-taxonomy-对齐课标的中小学知识图谱与前置诊断.md)——那篇是**按课标对齐的知识点图谱 + 前置诊断**，本篇是**论文论断图谱 + 引用核对**。可对照"**知识点依赖图**"与"**论证依赖图**"。
- **都是"给科研加一套 Agent 技能 / 工具"**：[Supervisor-Skills：把博导的科研经验炼化成 AI 副导师技能](Supervisor-Skills-把博导经验炼化成AI科研副导师技能.md)——那篇把**科研指导经验**炼成技能，本篇把**读论文本身**变成图谱工作流。可对照"**方法论技能**"与"**阅读与结构化工具**"。
- **同属面向科研的 Skill / 工具集**：[nature-skills：面向科研的 Nature 风格技能库](nature-skills-面向科研的Nature技能库.md)——那篇是**写作 / 图谱 / 数据到发表**的技能库，本篇是**论文理解与可验证笔记**。两者都服务科研流程，可对照"**产出端**"与"**输入（理解）端**"。
- **同为"把科研结论钉回可核对来源"**：[imozuru：芋づる式地把未知领域挖到原文的 Claude Code 技能](imozuru-逐条溯源到原文的ClaudeCode技能.md)——那篇把**每条论断指回具体文件与行号**（顺引用顺藤摸瓜挖到原文）；本篇把**论文论断变成可验证图谱**。可对照"**逐条溯源到原文**"与"**图谱化验证**"两种做法。

## 参考来源

- 仓库主页与 README（470 行；核验日期 2026-11-13）：https://github.com/Jeong-jin-Han/NodeGraph
- VS Code Marketplace：https://marketplace.visualstudio.com/items?itemName=JeongjinHan.nodegraph
- 许可证：`LICENSE`（MIT，1,069 字符；核验日期 2026-11-13）
- 相关文件（README 指向，未逐一打开）：`docs/FEATURES.md`、`docs/ko/README.md`、`.agent/nodegraph/SPEC.md`、`.agent/nodegraph/ENVIRONMENT.md`、`tools/verify-nodegraph.js`、`packages/nodegraph-<version>.vsix`、`demo/ex1`–`ex5`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 贡献者）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13）

以上信息核验于 2026-11-13。**未安装扩展、未在 VS Code 中打开任何 `.nodegraph.json`、未运行 `verify-nodegraph.js`、未让任何 Agent 生成图、未导出 HTML、未打开示例 demo**；能力与流程描述均来自 README，**未逐一验证**。
