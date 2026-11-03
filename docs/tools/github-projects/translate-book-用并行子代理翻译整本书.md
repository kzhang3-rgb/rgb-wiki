# translate-book：用并行子代理翻译整本书

> 一本几百页的书塞进一个会话必然上下文爆掉、还被截断——它把"分块 + 每个块给一个全新上下文的子代理"做成了一条有校验的可恢复流水线；真正值得抄的是它的分工原则：**脚本管账，LLM 只管语义判断**。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、LICENSE 与 GitHub API 元数据，未安装 skill、未跑过任何一本书）
- 适用范围：宿主需为 Codex / Claude Code / OpenClaw；依赖 **Calibre**（`ebook-convert`）与 **Pandoc**，Python 3 需装 `pypandoc`（`beautifulsoup4` 可选，用于更好的目录）；输入支持 PDF / DOCX / EPUB，输出 HTML（带浮动目录）/ DOCX / EPUB / PDF

## 核心结论

这是一个给编码助手用的**技能（skill）**，不是命令行程序：你让它"把某本书翻译成中文"，它自己走完转换 → 分块 → 并行翻译 → 校验 → 合并 → 生成多格式的整条链路。

它解决的问题是单会话翻译的物理上限：

- 整本书放进一个会话，上下文一路累积，越到后面越容易被截断；
- 长篇里同一个专有名词会被反复"重新决定"译法，出现前后不一致。

它的做法是**每个块交给一个全新上下文的独立子代理**（默认 8 个并发，并按 API 限流分批），从而既避开上下文累积，也让"块"成为可重试、可恢复的最小单位。仓库自述这是受 [claude_translater](https://github.com/wizlijun/claude_translater) 启发后重写的独立项目（**明确说不是 fork**）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [deusyu/translate-book](https://github.com/deusyu/translate-book) |
| 名称 | README 标题为 **"Rainman Translate Book"**（与仓库名不一致，作者署名 Rainman） |
| 描述 | Agent skill for Codex, Claude Code, and OpenClaw that translates entire books (PDF/DOCX/EPUB) into any language using parallel subagents |
| 许可证 | MIT（LICENSE 为标准 MIT 全文，版权署名 `Copyright (c) 2025 Rainman`） |
| 语言 | Python（`scripts/` 下的流水线脚本 + SKILL.md 编排说明） |
| 关注度 | **2,172 Star、260 Fork、5 关注、1 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-03-15 创建，最近推送 2026-10-29；无 Release、无 Topics |
| 支持语言 | 中文、英、日、韩、法、德、西（自述可扩展） |

仓库根目录结构很干净：`SKILL.md`（技能定义与编排）+ `scripts/`（11 个脚本）+ `tests/baselines/`（签入的基线书稿）+ `README.md` / `README.zh-CN.md` / `AGENTS.md` / `CLAUDE.md`。

## 流水线：每一步都在防"静默出错"

```
PDF/DOCX/EPUB → Calibre HTMLZ → HTML → Markdown
   → 切成 ~6000 字符的 chunk（manifest.json 存哈希）
   → 8 个并发子代理，各读一块、各写一块
   → 校验（哈希比对 + 源块与产出块 1:1）
   → 合并 → Pandoc HTML（注入目录）→ Calibre 出 DOCX/EPUB/PDF
```

这条链路里最值得记的不是"用了子代理"，而是**几处刻意的防呆设计**：

- **`manifest.json` 存每个源块的 SHA-256**：合并前比对，防止把过期或被改坏的产出合进成品。
- **`source_fingerprint.json` 把临时目录绑到源文件字节**：源文件被换掉后再跑，会**直接中止**，而不是手里没变、悄悄复用旧块。
- **空块让整次合并中止**，而不是"安静地丢掉这一块的内容"。
- **1:1 校验**：每个源块必须有对应产出块，缺一个就报 `Missing source chunk`。
- **可恢复**：中断后重跑会跳过已有有效产出的块；失败块自动重试一次。

### 三份状态文件，三个不同职责

这是它架构上最清楚的一处：

| 文件 | 写的人 | 作用 |
| --- | --- | --- |
| `glossary.json` | **只有主代理写** | 术语表（唯一权威），子代理**只读** |
| `output_chunkNNNN.meta.json` | 各子代理自己写 | 每块的原始观察（术语、别名、性别、证据） |
| `run_state.json` | 只有主代理写 | 编排状态：哪些块译过、用了哪些术语 |

因为共享状态只有一个写者，**整套流程不需要加锁**。

### 术语表（针对"同一本书前后译名不一致"）

在翻译前先建术语表：抽 5 个块（首、尾、3 个均匀分布的中段）提取专有名词与高频领域词，定下规范译法，写进 `glossary.json`。之后每块翻译时，把相关的三列表（原文 / 别名 / 译文）作为**硬约束**注入该块的提示词。

细节上比多数同类项目严谨：

- 术语表 **v1 会自动升级到 v2**，升级时若发现同一个词面（源词或别名）挂在两个词条下，**升级中止并要求人工消歧**，而不是猜一个。
- `glossary.json` **从不被覆盖**，改译名要自己编辑，想重建就删掉。
- 改术语表后，`run_state.json` 记录过"哪些块用过哪些术语"，因此**只重译受影响的块**。
- 别名合并要求 LLM 判断，而不是单看字符串相似度。

## 设计原则（这一节最好抄）

README 用四句话把"哪些归脚本、哪些归模型"讲明白了：

1. **脚本管账，LLM 管语义合并**——状态、schema、去重、哈希、IO 全用确定性 Python；命名、性别归属、别名判断、冲突消解才交给 LLM。
2. **共享状态只有一个写者**——上面那三份文件的分工，换来"无需加锁"。
3. **保守合并**——新增实体要证据；别名合并要判断；性别从 `unknown` 起步、只有明确证据才升级；冲突时**不静默覆盖**已有规范值。
4. **状态分三层、放三个文件**，各自有明确读者。

其他工程细节：相邻块会给子代理注入**只读的 300 字前后文摘**（帮助代词与实体指代，不引入新的状态文件）；PDF 输出会**自动识别并去掉页码行**，同时保留 `1984` 这类年份、章节号与引用编号（识别不准时可显式加 `--strip-page-numbers`，但该参数要求先删掉已缓存的 `input.md` 与块文件才生效）；模板 / 标题 / 封面改过之后，必须删掉既有成品再重跑，否则不会重新生成。

## 使用前需要注意

- **我只读了 README 与仓库结构，没有安装、没有真跑一本书**：以上流程、校验行为与"8 并发"等均为文档自述，未验证。
- **版权问题需要你自己判断，README 里没有讨论**：这是一条"把整本书翻译成另一种语言并输出成 EPUB/PDF"的流水线，**成品是完整的衍生作品**。翻译他人有版权的书并发布，通常需要权利人授权——工具本身中立，但这条链路最容易越界。我只把它当工具收录，不代表建议用它翻任何受版权保护的书。（注意 MIT 只覆盖**这个项目的代码**，不覆盖你翻译出来的书稿。）
- **会把整本书内容发给你所配置的模型服务**：从 Calibre 转换到分块翻译，全书正文都会离开本机；涉及未公开稿件或商业资料时先想清楚这一点。
- **依赖链不轻**：Calibre + Pandoc 是两个大件，装不上或不在 PATH 里，第一步就过不去（排错表第一条就是 `Calibre ebook-convert not found`）。
- **临时目录是一次性的**：`{book_name}_temp/` 属于单次翻译运行，换标题 / 模板 / 图片要么换新目录，要么先删掉旧成品，否则你会看到"改了配置但输出没变"。
- **仓库基线测试要在 `tests/.artifacts/` 里跑**：因为 `convert.py` 会把临时目录写在**当前工作目录**下，作者的说明是"在 artifacts 里运行以免污染仓库根目录"。
- **贡献方式特殊**：作者明确希望**先开 issue 而不是先提 PR**，说这是维护者独占上下文的 AI 辅助流水线，PR 可能被关掉换成 issue——想改的话先看清楚这条。
- 仓库无 Topics、无 Release；判断它是否活跃建议直接看提交记录（API 的 `pushed_at` 可能是陈旧值，见 [HelloGitHub](HelloGitHub-每月一期的开源项目月刊.md) 那条同类教训）。

## 什么时候会再看

- 要**批量翻译长文档 / 书稿**、又不想自己写分块与重试逻辑时——这是它比"丢给模型整本翻"强的地方。
- 想参考**"长任务拆给多个子代理"的工程化写法**时：块级 manifest 哈希、源文件指纹、空块中止、单写者状态、按影响范围选择性重跑，这套组合可以搬到任何长链路 Agent 任务上。
- 想参考**"脚本管账、LLM 管判断"的分界线怎么划**时。
- 想参考**术语表 / 一致性治理**（v1→v2 自动升级、冲突不猜、只重译受影响部分）的做法时。

## 相关笔记

- **同为"AI 大规模改写文本"**：[lexfridman-cn：Lex Fridman 播客中文站](lexfridman-cn-LexFridman播客中文站.md)——那是把 506 期访谈改写成中文长文导读（改写幅度大于翻译），本篇是把整本书并行翻译；可对照"翻译"与"二次创作"在准确性与版权上的不同处理。
- 索引位置：[GitHub 项目收藏](README.md)。
- **最相近的"书 → 可用产物"技能**：[book-to-skill](book-to-skill-把技术书转成ClaudeCode技能.md)——那篇是把技术书**编译成按需加载的技能**（思考的是"怎么让模型少读"），本篇是把书**整本译出来**（思考的是"怎么把长任务拆开且不丢内容"）；两者可以并排对照"编译期提炼"与"全量转换"两种思路。
- **同族的文档转 Markdown 底层**：[markitdown](markitdown-文件转Markdown工具.md)——本项目的 PDF/DOCX/EPUB 转换走 Calibre，如果想换轻一点的解析路径，它是另一个答案。
- **运行宿主**：[openclaw](openclaw-自托管个人AI助手.md)——README 明确列出的三个宿主之一（另两个是 Codex 与 Claude Code）。
- **给编码助手的技能层对照**：[agents.md](agents.md-给编码助手用的AGENTS文件格式.md)、[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)、[dsh-plugins-store](dsh-plugins-store-DSH插件市场.md)。
- **长文档/内容类项目**：[everyone-can-use-english](everyone-can-use-english-人人都能用英语.md)、[up](up-人生进阶指南.md)——都是"书稿工程化"的例子，可对照它们在**构建与校验**上的取舍。

## 参考来源

- 项目仓库与 README：https://github.com/deusyu/translate-book
- 中文 README：https://github.com/deusyu/translate-book/blob/main/README.zh-CN.md
- 技能定义：`SKILL.md`；路线图跟踪 issue #7
- 灵感来源（README 声明）：https://github.com/wizlijun/claude_translater
- 元数据（Star / Fork / Issue / 体积 / 时间）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。未安装 skill、未运行流水线，翻译质量与各校验步骤的实际行为均未验证。
