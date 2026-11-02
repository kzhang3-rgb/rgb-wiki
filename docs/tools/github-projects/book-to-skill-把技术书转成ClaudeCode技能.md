# book-to-skill：把一本技术书编译成 Claude Code 技能

> 一句话值得记住的取舍：它**不在查询时检索（那还是 RAG），而在"编译时"把书拆成结构化的技能**——SKILL.md 放核心心智模型，各章拆成按需加载的文件，让书变成"随叫随到、但不常驻上下文"的能力。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、仓库页与 GitHub API 元数据，未安装、未跑过提取脚本）
- 适用范围：已在使用 Claude Code skills 的人；想把自己手头的技术书/文档变成可调用技能的场景

## 核心结论

它瞄准的痛点很朴素：**技术书买来读一遍，三个月后连第 7 章存在过都忘了**。常见替代方案都不行——搜 PDF 只得到页码、直接问模型会幻觉、自己记笔记最后变成一份不打开的 200 行文档。

`book-to-skill` 的做法是把书**编译成一个技能**，写到 `~/.claude/skills/<slug>/`：

| 产物 | 内容 | 大致规模 |
| --- | --- | --- |
| `SKILL.md` | 核心心智模型 + 章节索引 | ~4,000 tokens |
| `chapters/ch01-*.md` … | 每章一个文件，**按需加载** | 每章 ~1,000 tokens |
| `glossary.md` | 全部关键术语，按字母序，带章节引用 | ~1,500 tokens |
| `patterns.md` | 技术、算法与设计模式 | ~2,000 tokens |
| `cheatsheet.md` | 决策表与速查规则 | ~1,000 tokens |

关键点是**章文件按需加载**——你不问到那一章，它就不占技能预算。之后就能像普通技能一样用：`/my-book`、`/my-book replication`、`/my-book ch05`。

它和 RAG 的差别，README 说得比较清楚（**属作者观点**）：**RAG 在查询时**切片、嵌入、找相似向量，回答的是"这几段和你的问题接近"；**book-to-skill 在编译时**做一次深度分析，把作者真正的框架、命名、适用场景、反模式提炼出来，回答的是"这个作者建了这 12 个框架，可以直接拿来推理"。作者自己的结论是：**跨 50+ 本书搜索选 RAG；深挖一本书、把框架嵌进工作流选 skill**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [virgiliojr94/book-to-skill](https://github.com/virgiliojr94/book-to-skill) |
| 描述 | "Turn any technical book PDF into a Claude Code skill — ready to study, reference, and use while you work." |
| 许可证 | MIT（仓库含 `LICENSE.md`） |
| 语言 | Python 100%（核心是 `scripts/extract.py`） |
| 关注度 | **18,017 Star、1,926 Fork、96 关注、19 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-05-01 创建；GitHub API 显示最后推送 **2026-08-05**；仓库页显示最新提交 2026-05-20、约 18 次提交 |
| 规模 | 体积约 1,577 KB（约 1.5 MB）；**1 分支、0 Tag、无 Release**（仓库页） |

> **数据一致性提示**：仓库页显示的是 **3k Star / 390 Fork / 25 关注**，与 API 的 **18,017 Star / 1,926 Fork / 96 关注** 差距很大。仓库页显示的最新提交停在 2026-05-20，而 API 的最后推送是 2026-08-05，**判断仓库页拿到的是较早的快照**。按本项目约定**以 API 值为准**，两者不当同一份数据使用。

## 用法要点

```bash
/book-to-skill <文档路径> [技能名 slug]

# 示例
/book-to-skill ~/Downloads/designing-data-intensive-applications.pdf
/book-to-skill ~/books/clean-code.epub clean-code
```

支持的格式很全：**PDF、EPUB、DOCX、TXT、Markdown、reStructuredText、AsciiDoc、HTML、RTF、MOBI/AZW/AZW3**。

提取工具**按格式依次尝试、用第一个可用的**；纯文本/Markdown/rST/AsciiDoc 不需要额外依赖：

- **PDF 文字型（散文为主）**：`pdftotext`（poppler）→ 回退 `PyPDF2` → `pdfminer.six`，都是秒级；
- **PDF 技术型（含代码/表格/公式）**：用 `docling`（`pip3 install docling`，约 1.5s/页），**能保留 markdown 表格与代码块**；
- **EPUB**：`ebooklib`+`beautifulsoup4`（最佳）→ 回退内置 `zipfile`；
- **DOCX / HTML / RTF**：`python-docx` / `beautifulsoup4` / `striprtf`，各有标准库回退；
- **MOBI/AZW/AZW3**：Calibre 的 `ebook-convert`（外部程序，非 pip）。

**开始提取前它会先问你这本书是"技术书"还是"文字书"**，据此自动选工具。

安装就是把两个文件放进技能目录：

```bash
mkdir -p ~/.claude/skills/book-to-skill/scripts
curl -o ~/.claude/skills/book-to-skill/SKILL.md        https://raw.githubusercontent.com/virgiliojr94/book-to-skill/master/SKILL.md
curl -o ~/.claude/skills/book-to-skill/scripts/extract.py https://raw.githubusercontent.com/virgiliojr94/book-to-skill/master/scripts/extract.py
```

## 使用前需要注意

- **版权与使用边界（最重要）**：把一本书转成技能，产出物**本质上是这本书的结构化衍生内容**。个人自用与"把生成结果分发/共享/商用"是两回事——**后者很可能侵权**。笔记只记录方法，不涉及任何书籍内容。
- **性能数据是作者自述**：README 给的基准是（103 页技术书、仅 CPU）`pdftotext` 0.1s / 27K tokens / 0 表格 / 0 代码块，对比 `docling` 164s / 27K（+1.2%）/ 48 表格 / 36 代码块。**我未独立验证**，但它清楚地说明了取舍：**要表格和代码块，就得接受约 1000 倍的时间**。
- **"绝不幻觉""密度优先"是设计目标，不等于必然结果**：摘要质量取决于所用模型、"技术/文字"判定是否正确、以及书本身的排版。README 的"chapter 号有据可循、不会编造章节标题"是**其设计意图与卖点，我未验证**。
- **依赖外部工具**：多数格式要额外装东西；缺工具时脚本会提示要跑哪条命令，但**你得自己装**（尤其 MOBI 系列要装 Calibre 桌面程序）。
- **它和 NotebookLM 不是替代关系**：作者自己说，要跨 80 本书搜索用 NotebookLM 更合适，book-to-skill 面向"深挖一本、嵌进工作流"。
- **我只读 README、仓库页与元数据**：未安装技能、未运行 `extract.py`、未生成过任何技能。

## 什么时候会再看

- 想**把手边一本技术书变成"工作时随时能问"的技能**时——这是它最直接的用途。
- 想参考**"编译期提炼 vs 查询期检索"**这条与 RAG 的路线对照时。
- 想参考**"按需加载的上下文预算设计"**：SKILL.md 前置、章节按需，正好对应"压缩只保留前若干 token"的约束。
- 需要**一份多格式文档提取的选型表**时（哪种格式用哪个库、质量与速度对比）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **"把外部内容变成模型可用素材"的同族**：[markitdown](markitdown-文件转Markdown工具.md)（文档转 Markdown）、[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md)（网页转 LLM 数据）。
- **给编码助手喂能力的技能层**：[agents.md](agents.md-给编码助手用的AGENTS文件格式.md)、[zframes](zframes-让Agent用对话生成行情看板.md)、[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)、[dsh-plugins-store](dsh-plugins-store-DSH插件市场.md)。
- **"把整本书译出来"的对照做法**：[translate-book](translate-book-用并行子代理翻译整本书.md)——那篇用并行子代理逐块翻译整本书并保留全文；本篇是**编译期提炼**（章节按需加载、只留能力不留原文），两者正好是"全量转换"与"提炼压缩"的两条路。
- **书稿/教材类内容项目**：[everyone-can-use-english](everyone-can-use-english-人人都能用英语.md)、[up](up-人生进阶指南.md)、[Foundations-of-LLMs](Foundations-of-LLMs-大模型基础教材.md)——可对照"**内容本身**"与"**把内容变成能力**"两类做法。

## 参考来源

- 项目仓库与 README：https://github.com/virgiliojr94/book-to-skill
- 技能定义文件：https://github.com/virgiliojr94/book-to-skill/blob/master/SKILL.md
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README、仓库页与元数据；未安装、未运行。提取基准、摘要质量与"不幻觉"等说法均来自作者自述，未独立验证；本笔记不涉及任何具体书籍内容，转换他人作品时请自行判断版权与使用范围。
