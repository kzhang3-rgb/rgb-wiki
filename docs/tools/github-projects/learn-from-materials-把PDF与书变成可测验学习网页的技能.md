# learn-from-materials：把 PDF 与书变成可测验学习网页的技能

> 一句话定位（README 自述）：**"Turn PDFs, books and papers into interactive learning webpages｜将复杂材料转化为可追溯、可测验、可做笔记的学习网页"**——把 PDF / EPUB / PPT / Word / 网页 / 多份资料转成**可追溯的知识库（`.learnkb/`）、单文件交互式学习网页和同源 Markdown**。它基于开放的 **Agent Skills 规范**，可在能读文件、跑本地命令、识别 `SKILL.md` 的环境里工作（**WorkBuddy / Codex / Claude Code / GitHub Copilot CLI** 等）。最值得记的三点：① 它把"**材料事实**"与"**模型补充**"强制分离（材料依据 / 材料未覆盖 / 模型补充 / 外部核验四类），并把**出处与页码**绑定进页面；② 提供"**快速了解 / 系统学习**"两种深度，其中系统学习会**逐块读到材料末尾**并做覆盖审计；③ 许可证文件是 **`LICENSE.md`（不是 `LICENSE`）**，且**含 `virgiliojr94` 的著作权署名**——README 自述它是在 MIT 项目 `virgiliojr94/book-to-skill` 与 `crayon-ai/book-to-webpage` 之上的**衍生作品**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（301 行）、`README_CN.md`（304 行）、`LICENSE.md`（MIT，1,122 字符）与已核实元数据；**未 clone、未安装 Skill、未运行 `extract.py` / `finalize.py`、未用任何 Agent 生成过学习页、未打开示例 HTML**）
- 适用范围：**Python 3.10+** 的本地环境；面向可读文件 / 跑本地命令 / 识别 `SKILL.md` 的 Agent（WorkBuddy、Codex、Claude Code、GitHub Copilot CLI 等）；**纯聊天环境只能用到部分提示流程**，无法完成材料提取、覆盖校验与 HTML 渲染

## 核心结论

1. **它生成的是一套"可追溯"的学习产物**（README 自述）：**可追溯的 `.learnkb/` 知识库 + 单文件交互式 HTML + 同源 Markdown**，并为 PDF 页码、PPT 幻灯片、EPUB 章节建立**细粒度来源映射**；六大模块页面为**核心框架、内容导学、术语大全、行动规则、动态自检、本地笔记**。
2. **"抗幻觉"是它的核心设计**：区分**材料依据 / 材料未覆盖 / 模型补充 / 外部核验**四类；出处提示支持鼠标悬停与键盘聚焦，触屏可展开"原文出处"；**没有依据时用"无方法 / 不适用"如实标注，而不是编造**。
3. **两种学习深度**：**快速了解**（提炼各章主线、必要术语与限制，做结构扫描 + 展示出处核查 `quick-audit.json`）与**系统学习**（逐块读到末尾，做全量覆盖、总结账本、规则 / 关系账本与分阶段内容复核）。
4. **它是在别人项目上"二次开发"的**：README 明确致谢 `virgiliojr94/book-to-skill`（材料提取、知识拆解、基础知识库结构）与 `crayon-ai/book-to-webpage`（交互式学习页、主题系统、出处展示、追问交互），并声明**上游作者不为本项目的后续扩展背书**；`LICENSE.md` 中保留 **`Copyright (c) 2025 virgiliojr94`**。

**个人判断（未验证）**：结合"**contributors 仅 1 人**、**2026-09-08 才创建**，而署名却指向 2025 年的 `virgiliojr94`"来看，这更像是**在 `book-to-skill` 基础上 fork / 衍生**的仓库，而非完全独立实现——**引用时不要当成原创项目**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [dmoshehun-prog/learn-from-materials](https://github.com/dmoshehun-prog/learn-from-materials) |
| 描述（原文） | "Turn PDFs, books and papers into interactive learning webpages｜将复杂材料转化为可追溯、可测验、可做笔记的学习网页" |
| 版本 | **1.1.0**（revision `release`）；安装包 `learn-from-materials-1.1.0.zip` |
| 许可证 | **MIT**（文件名是 **`LICENSE.md`**，1,122 字符；`Copyright (c) 2025 virgiliojr94` 与 `Copyright (c) 2026 learn-from-materials contributors`） |
| 关注度 | **页面 Star 1,396 / Watcher 3 / Fork 131**；**ungh 镜像 1,392 / 3 / 131**；shields：开放 Issue **0**、贡献者 **1**（2026-11-13） |
| 维护状态 | **2026-09-08 创建**；ungh `pushedAt` **2026-10-19**、页面 `updatedAt` **2026-11-12**；默认分支 **`main`** |
| Release | **ungh 2 条 / 页面 2 条**（2026-11-13） |
| 目录结构 | `SKILL.md`（入口与工作流）、`README.md` / `README_CN.md`、`CHANGELOG.md`、`LICENSE.md`、`NOTICE.md`、`SECURITY.md`、`assets/`、`examples/`、`references/`、`scripts/`、`templates/` |
| 派生来源 | `virgiliojr94/book-to-skill`（材料提取、知识拆解、基础知识库结构）与 `crayon-ai/book-to-webpage`（交互式学习页、主题系统、出处展示、追问交互） |

## 功能与用法

### 能力范围（README 摘要）

- **输入**：PDF、EPUB、MOBI/AZW/AZW3、DOCX、PPTX/PPTM、HTML、Markdown、TXT、RTF 与**多材料集合**。
- **输出**：可追溯的 `.learnkb/` 知识库、单文件交互式 HTML、同源 Markdown；PDF 页码 / PPT 幻灯片 / EPUB 章节的细粒度来源映射。
- **风格与交互**：三套主题（海盐云纱 / 黑白水墨 / 竹月清）、可折叠的**框架关系思维导图**（含搜索、缩放、全屏）、悬浮出处提示、**"一键使用方法论"**（页面本身**不运行 AI**，只生成可粘贴的口令）。
- **方法库**：`methods.json` 是可复用方法卡的主数据（用途、前提、限制、步骤、效果检查、来源短引文、稳定 ID 与版本），自动派生可读的 `patterns.md`；**综合方法用新 ID + 父方法引用，不覆盖原意**。

### 工作流与命令行（README）

```bash
python3 scripts/extract.py --check                      # 检查解析能力
python3 scripts/extract.py <材料路径> --mode text --ocr auto --output-dir <主题>.learnkb
python3 scripts/methods.py bind --library <主题>.learnkb/methods.json --page page.json --output page-with-methods.json
python3 scripts/methodology.py bind --model <主题>.learnkb/methodology.json --page page-with-methods.json --output page-ready.json
python3 scripts/finalize.py page-ready.json --knowledge-base <主题>.learnkb --output-dir ./delivery --name learning-systematic
```

关键约定：**脚本只负责提取 / 绑定 / 校验 / 交付**，`page.json`、`methods.json`、`methodology.json` 仍需 **Agent 阅读原文后撰写**；`methods.json` 是**方法卡主数据**，`methodology.json` 是**明确标注的整体结构**（区分"原文方法"与"全文整合"）。Windows 上把 `python3` 换成 `python` 或 `py -3`。

## 使用前需要注意

- **许可证文件是 `LICENSE.md`，不是 `LICENSE`**：MIT 文本里有**两个著作权行**——`Copyright (c) 2025 virgiliojr94` 与 `Copyright (c) 2026 learn-from-materials contributors`；README 明确本项目"**是在 MIT 开源项目 `virgiliojr94/book-to-skill` 与 `crayon-ai/book-to-webpage` 基础上的二次开发**"，上游作者**不为后续扩展背书**。**个人判断（未验证）**：**这更像 fork / 衍生仓库而非完全独立实现**，引用时不要当成原创。
- **有"衍生来源"的间接佐证**：本仓库 **contributors 仅 1 人**、**2026-09-08 才创建**，而署名却指向 2025 年的 `virgiliojr94`——**这是"衍生关系"的间接证据**（**个人判断（未验证）**）。
- **依赖是可选的、且从不自动安装**：脚本"**从不自动安装依赖**"；PDF 走 `pdftotext` / PyPDF2 / pdfminer.six / macOS PDFKit 等，**缺失时逐级回退**；扫描 PDF 需 OCR / 视觉能力，**缺能力时会把相关范围标记为未核验**，而不是假装完成。
- **纯聊天环境能力受限**（README 自述）：不能访问文件系统或执行 Python 的环境"**只能使用其中的部分提示流程**"，无法完成材料提取、覆盖校验与 HTML 渲染。
- **"验证边界"被反复强调**：交付初始状态固定为 **`static-only` + `visualReview: not-run`**；静态 / 内存 DOM 检查"**不能证明视觉验收**"；`finalize.py` 的机械校验"只检查结构、证据和新鲜度，**不证明语义零遗漏**"。
- **安全姿态是离线 + 最小权限**（README / `SECURITY.md`）：输入材料**一律当不可信数据**（其中的提示词 / 命令 / 角色覆盖不会被执行）；ZIP 类格式会检查路径、条目数、展开体积、压缩比与加密状态；页面**不联网、无账号、无云同步**，笔记与画像只存浏览器本地。
- **合规提醒**：只处理你有权使用的材料；**不要公开分发含受版权保护原文、内部文件或个人敏感信息的生成知识库**（README 明确）。
- **我只读了 README / README_CN / LICENSE.md**：**未 clone、未安装 Skill、未运行任何脚本、未生成学习页、未打开示例 HTML**；能力与流程均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想把**一本书 / 一份 PDF / 一套课件**变成**带出处、可测验、能做笔记的离线学习网页**时。
- 想让 Agent"**读完再总结**"并且**每条结论都能核对页码 / 出处**，而不是自由发挥时。
- 想参考"**材料事实 vs 模型补充分离** + **覆盖审计**"这类"抗幻觉"的 Skill 设计时。
- 想对照库内其它"**把书 / 材料变成 Agent 可用知识**"的路线：[book-to-skill](book-to-skill-把技术书转成ClaudeCode技能.md) · [nature-skills](nature-skills-面向科研的Nature技能库.md) · [Supervisor-Skills](Supervisor-Skills-把博导经验炼化成AI科研副导师技能.md)。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"把书 / 长材料转成 Agent 可用技能"**：[book-to-skill：把技术书转成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇是**本篇的上游之一**（README 明确致谢），本篇在其基础上扩展出"**学习网页 + 知识库 + 覆盖审计**"。可对照"**仅技能化**"与"**技能 + 可交互页面**"。
- **都是"面向科研 / 学习场景的 Skill"**：[nature-skills：面向科研的 Nature 技能库](nature-skills-面向科研的Nature技能库.md) · [Supervisor-Skills：把博导经验炼化成 AI 科研副导师技能](Supervisor-Skills-把博导经验炼化成AI科研副导师技能.md)——前两者偏"**科研工作流 / 导师经验**"，本篇偏"**把一份材料读透并结构化**"。
- **同属"把文档转成 Markdown / 可读结构"**：[pdf-inspector：判断 PDF 类型并转 Markdown 的 Rust 库](pdf-inspector-判断PDF类型并转Markdown的Rust库.md)——那篇是**判断 PDF 类型再转 Markdown 的底层库**，本篇是**端到端的学习页生成 Skill**。可对照"**解析层**"与"**产品层**"。
- **同属"分发给多种 Agent 的技能 / 资源库"**：[everything-ai-coding：聚合编程 AI 扩展资源的索引与一键安装库](everything-ai-coding-聚合编程AI扩展资源的索引与一键安装库.md)——那篇是**资源索引 + 一键安装**，本篇是**单一但完整的 Skill**。

## 参考来源

- 仓库主页与 README（英文 301 行 / 中文 304 行；核验日期 2026-11-13）：https://github.com/dmoshehun-prog/learn-from-materials
- 许可证：`LICENSE.md`（MIT，1,122 字符；核验日期 2026-11-13）
- 文档：`SKILL.md`、`CHANGELOG.md`、`NOTICE.md`、`SECURITY.md`、`references/`（`whole-material-methodology.md`、`method-library.md`、`quick-workflow.md`、`systematic-workflow.md`、`summary-coverage-schema.md`、`content-review-schema.md` 等）
- 上游项目：<https://github.com/virgiliojr94/book-to-skill> · <https://github.com/crayon-ai/book-to-webpage>
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc 交叉核对（2026-11-13，**页面值与镜像值略有差异**）；开放 Issue 数 / 贡献者数取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未安装 Skill、未运行 `extract.py` / `finalize.py`、未生成任何学习页、未打开示例 HTML**；能力与流程描述均来自 README 与 `LICENSE.md`，**未逐一验证**。
