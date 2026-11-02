# llm_wiki：会自己长大的个人知识库桌面应用

> 一句话记住它：它把"个人 Wiki"当成**要维护的产物**而不是**要检索的语料**——LLM 增量地把你的文档编译成一份可持续更新、可溯源、Obsidian 兼容的 wiki，而不是每次提问都从零检索。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、LICENSE 文件与 GitHub API 元数据，未安装未运行）
- 适用范围：想把大量文档沉淀成结构化知识库的个人用户；需自备模型 API（含 OpenAI 兼容接口）

## 核心结论

llm_wiki 明确对标 Karpathy 的 [llm-wiki 方法论](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)，把它从"一份可以贴给 LLM 的抽象模式文档"做成了**跨平台桌面应用**。

它最核心的取舍，是把传统 RAG 反转过来：

| | 传统 RAG | llm_wiki |
| --- | --- | --- |
| 知识形态 | 每次查询临时检索→拼答案 | **一次性编译成持久 wiki，再持续更新** |
| 产物 | 一次性的回答 | 可阅读、可链接、可版本化的 Markdown 页面 |
| 分工 | 检索器 + 生成器 | **人负责策展，LLM 负责维护** |

保留自原型的部分（README 自述"忠实沿用"）：三层架构 **Raw Sources（不可变）→ Wiki（LLM 生成）→ Schema（规则与配置）**；三个核心操作 **Ingest / Query / Lint**；`index.md` 作内容目录与导航入口；`log.md` 作可解析的操作记录；`[[wikilink]]` 交叉引用；每页带 YAML frontmatter；**wiki 目录可直接当 Obsidian vault**。

在此之上它加了不少工程化扩展，README 列出的重点包括：

- **Two-Step Chain-of-Thought Ingest**：先"分析"再"生成"两次独立 LLM 调用（第一步抽实体/概念/矛盾点与结构建议，第二步才写文件），并用 **SHA256 增量缓存**跳过未改动的源文件。
- **持久化摄入队列**：串行处理、落盘可恢复、失败自动重试至多 3 次、带进度可视化。
- **4 信号相关度模型**（知识图谱）：直接链接 ×3.0、同源重叠 ×4.0、Adamic-Adar ×1.5、类型亲和 ×1.0。
- **Louvain 社区检测**：自动发现知识簇，用内部边密度算"内聚度"，低于 0.15 标警告。
- **Graph Insights**：自动提示"意外连接"（跨社区/跨类型/边缘↔中枢）与"知识缺口"（孤立页、稀疏社区、桥接节点），缺口可一键触发 Deep Research。
- **可选向量检索**：走任意 OpenAI 兼容 `/v1/embeddings`，存在 **LanceDB**（Rust 后端）里做 ANN。
- **Rust 后端 Chat Agent**：可用 wiki/源/图谱/网络检索、工作区文件工具、受审批的 shell 命令、技能文件读取；支持 `SKILL.md` 技能与 `/skill` 选择。
- **本地 HTTP API + MCP Server + Agent Skill**：内置 `127.0.0.1:19828` JSON API 与 MCP server，另有一个独立仓库的技能可一键装进 Claude Code / Codex。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [nashsu/llm_wiki](https://github.com/nashsu/llm_wiki) |
| 描述 | 把文档自动整理成互联知识库的跨平台桌面应用；不采用一次性 RAG，而是让 LLM 增量构建并维护一份持久 wiki。 |
| 许可证 | **GPL-3.0**（`LICENSE` 为 GNU GPL v3 全文，Copyright (C) 2024-2026 Yong Su）；**但 GitHub API 的 `license` 字段返回 `NOASSERTION`**，两者不一致 |
| 语言 / 技术栈 | TypeScript（前端）+ Rust 后端；sigma.js / graphology / ForceAtlas2 / LanceDB |
| 关注度 | **17,277 Star、2,045 Fork、78 关注、232 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-04-08 创建，最近推送 2026-08-25；`main` 分支 |
| 体积 | 约 40,354 KB（`size` 字段，约 39 MB） |

## 用法要点

README 给出的定位是"**a personal knowledge base that builds itself**"。使用时会创建一个项目，项目内大致是这样一棵树：

```
raw/sources/     # 不可变原始文档（PDF、Office、EPUB/MOBI、图片、网页剪藏、批量 URL）
wiki/            # LLM 生成的页面（index.md、log.md、overview.md、实体页、概念页……）
schema.md        # 结构规则
purpose.md       # 目标、关键问题、研究范围（原方法论没有，本项目的增补）
.llm-wiki/       # 会话、配置等运行数据
```

摄入时支持 PDF / Office / EPUB/MOBI / Org mode / 图片 / 媒体 / 网页剪藏 / 批量 URL，PDF 解析可选内置、云端或本地 **MinerU**。每一步生成的页面都会在 frontmatter 写 `sources: []` 做**来源溯源**，索引页 `index.md` 与总览 `overview.md` 每次摄入后自动更新。

查询侧是一个多阶段管线：分词检索 →（可选）向量检索 → 图谱扩展（含 2 跳衰减）→ **预算控制**（上下文窗口 4K→1M，按 60%/20%/5%/15% 分配给 wiki 页 / 聊天历史 / 索引 / 系统）→ 组装带编号的页面并让 LLM 按 `[1][2]` 引用。

## 使用前需要注意

- **许可证是 GPL-3.0，且 API 直接报错**：`LICENSE` 文件是 GNU GPL v3 全文（Copyright 2024-2026 Yong Su），而 GitHub API 返回 `NOASSERTION`（未识别）。**以 LICENSE 文件为准，按 GPL-3.0 对待**。这意味着它**有传染性**——与同页多数 MIT 工具不同，集成进闭源产品或商用分发需要认真评估义务。同时这个 API 与文件不符的情况，也说明"只信 API 的 license 字段"会出错。
- **232 个开放 Issue 是个不小的数字**：以 1.7 万 Star 的体量看，积压量偏多，使用前建议先扫一遍高频问题。
- **README 的性能结论是项目自述，未验证**：例如"启用向量检索后整体 recall 从 58.2% 提升到 71.4%"，属于作者给出的基准，**没有独立的复现验证**。
- **推送时间与创建时间差**：最近推送 2026-08-25，距核验日（2026-11-02）已 2 个多月，活跃度需自行判断。
- **摄入要花 LLM 额度**：两步 CoT 摄入 = 每个源文件两次模型调用；它用 SHA256 缓存来省重复摄入，但**首次全量摄入的 token 成本要预估**。
- **数据发往第三方模型**：摄入与问答都会把文档内容发给配置的模型端点，**敏感文档需先评估**；`Read Sources Only` 模式只是限制"只从原始素材作答"，不等于数据不外发。

## 什么时候会再看

- 想给大量个人文档建一个**长期维护、可读可链接**的知识库，而不是一个只能问答的黑箱；
- 需要参考"**编译期提炼 vs 查询期检索**"这个架构取舍的具体落地（与 [book-to-skill](book-to-skill-把技术书转成ClaudeCode技能.md) 是同一思路的两种实现）；
- 想对照"知识图谱 + 社区检测 + 缺口提示"如何做成产品的可视化功能时；
- 需要给自制工具接 MCP / 本地 HTTP API 时可参考它的接口设计。

## 相关笔记

- [book-to-skill：把技术书转成 Claude Code 技能](book-to-skill-把技术书转成ClaudeCode技能.md)：同样把"检索"前置为"编译"，但产物是技能而非 wiki。
- [memory-brain：AstrBot 记忆与生活层](memory-brain-AstrBot记忆与生活层.md)：另一种"给助手补长期记忆"的实现路线。
- [markitdown：文件转 Markdown 工具](markitdown-文件转Markdown工具.md)：文档转 Markdown 的通用工具，与本文档摄入环节同源问题。
- [Foundations-of-LLMs：大模型基础教材](Foundations-of-LLMs-大模型基础教材.md)：理解"检索、Embedding、图"这些基础概念的背景资料。
- [agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)：它内置的 `SKILL.md` 属于同一类"给 AI 的说明文件"约定。

## 参考来源

- 仓库主页：<https://github.com/nashsu/llm_wiki>
- README（`main` 分支）：<https://raw.githubusercontent.com/nashsu/llm_wiki/main/README.md>
- LICENSE（`main` 分支，GNU GPL v3 全文）：<https://raw.githubusercontent.com/nashsu/llm_wiki/main/LICENSE>
- 上游方法论（Andrej Karpathy）：<https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
- GitHub API 元数据：<https://api.github.com/repos/nashsu/llm_wiki>（核验日期 2026-11-02）
