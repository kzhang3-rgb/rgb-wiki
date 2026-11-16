# awesome-deepseek-integration：DeepSeek 官方的"实用集成"清单

> DeepSeek 官方（`deepseek-ai` 组织）维护的第三方集成索引：把"哪些软件已经能接 DeepSeek API"按应用 / Agent 框架 / RAG / 浏览器插件 / IDE 插件等分类列出来，条目统一是"图标 + 名称链接 + 一句话说明"。想知道某个软件怎么接 DeepSeek、或想知道某个项目是不是官方认可时值得再看。

- 状态：草稿
- 最近核验：2026-11-05
- 适用范围：只想找"现成能接 DeepSeek 的软件"或评估某条目可信度时；本笔记未逐条点开清单中的项目。

## 核心结论

- **这是官方一手入口**：仓库属于 `deepseek-ai`（DeepSeek 官方组织），描述为 "Integrate the DeepSeek API into popular software"，中文标题"DeepSeek 实用集成"。与同组织的 [deepseek-harness](deepseek-harness-DeepSeek开源Agent框架.md) 一样，属官方维护。
- **本质是一份文档索引，不是软件**：仓库无代码（GitHub 语言统计为 `none`），主体是 `README.md` 的多语言版本 + 每个集成在 `docs/<名称>/` 下的一页说明与图标。
- **官方自己会为部分集成写说明页**：清单里约 15 条（中文版）指向仓库内的 `docs/<简称>/README_cn.md`，例如 Chatbox 与 Cherry Studio 都有官方写的接入说明，而不是只给外链。
- **已经停更**：提交记录显示 `main` 上最新提交停在 **2026-02-23**，此后约 8.5 个月没有新提交；页面的 `updated_at`（2026-11-05）是 Star 刷新所致，不代表内容有更新。
- **多语言版本并不同步**：实测中文版约 169 行条目、英文版约 218 行条目，**中文版覆盖落后于英文版**（英文版多出 Visual Studio / Discord Bots / Emacs / Providers 等分类）。
- **许可是 CC0-1.0**：公共领域奉献，对本仓库自身内容几乎没有使用限制；但**它列出的那些项目各自的许可与版权归各自所有者**，不影响本项目内容。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | https://github.com/deepseek-ai/awesome-deepseek-integration |
| 归属 | **DeepSeek 官方组织 `deepseek-ai`**（`isOrg: true`） |
| 一句话用途 | 把 DeepSeek API 接入各类流行软件的官方集成清单 |
| 许可 | **CC0-1.0**（Creative Commons Zero v1.0 Universal，`LICENSE` 约 7.0 KB，公共领域奉献） |
| 主语言 | **无**（纯文档，语言统计为 `none`） |
| 关注度（2026-11-05） | **39,734 Star / 4,317 Fork / 389 关注 / 44 开放 Issue**（页面值） |
| 创建 / 最近推送 | **2024-01-11 创建；`pushed_at` 2026-02-23**（提交记录确认已停更约 8.5 个月） |
| 默认分支 | `main` |
| Release / Tag | **无任何 Release、无 tag** |
| 贡献者 | 30 位以上（api 返回前 30 名，最高 `mowentian` 137 次、`Bin788` 43 次、`DillionApple` 37 次） |
| topics | `awesome` / `deepseek` |
| 多语言版本 | `README.md`（英）/ `README_cn.md`（简中）/ `README_zh_tw.md` / `README_ja.md` / `README_es.md` |
| 根目录 `CONTRIBUTING.md` | **不存在（404）** |

> 口径说明：数据来自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章**，核验日 GitHub API 匿名配额已用尽，未取到 `size`。ungh 与页面数值基本一致（Star 39,721 vs 39,734，Fork 4,315 vs 4,317），以页面值为准。"已停更"由 `commits/main.atom` 交叉核验：最新 20 条提交全部落在 2026-02-23。

## 内容结构

中文版（`README_cn.md`）分为 **16 个分类**，条目用 HTML `<table>` 排版（图标 + 名称链接 + 一句话说明）：

| 分类 | 说明 |
| --- | --- |
| 应用程序 | 各类客户端 / 桌面 / 移动 App |
| AI Agent 框架 | Agent 编排与运行框架 |
| AI 数据应用框架 | 数据侧应用框架 |
| RAG 框架 | 检索增强生成相关 |
| FHE (全同态加密) frameworks | 隐私计算方向 |
| Solana 框架 | 链上 / 加密生态 |
| 综合数据管理 | 数据治理类 |
| 即时通讯插件 | IM 机器人 / 插件 |
| Office 插件 | Office Add-in |
| 浏览器插件 | 浏览器扩展 |
| VS Code 插件 | VS Code 扩展 |
| neovim 插件 | Neovim 扩展 |
| JetBrains 插件 | JetBrains 系列 IDE 扩展 |
| AI Code 编辑器 | 原生 AI 代码编辑器 |
| 安全 | 安全类工具 |
| 其它 | 未归类的集成 |

**英文版（`README.md`）另多出 4 个分类**：Visual Studio Extensions、Discord Bots、Emacs、Providers。

中文版的链接分布（实测）：外部 `http(s)` 链接约 182 条（其中指向 `github.com` 的约 106 条），指向仓库内 `docs/` 的约 15 条。清单末尾附 Star History 图表（`api.star-history.com`）。

## 用法要点

- **按分类找入口**：想给某个软件接 DeepSeek，先在 README 里按分类定位；官方已经写了说明页的条目，直接看 `docs/<名称>/README_cn.md`。
- **API key 走官方开放平台**：README 开篇即指向 <https://platform.deepseek.com/>。
- **多语言按需切换**：英 / 简中 / 繁中 / 日 / 西五个版本，同一仓库不同文件。
- **本仓库自身不写代码**：它给的是"去哪儿找"的索引，接入细节在各自项目的仓库或 `docs/` 说明页里。

## 使用前需要注意

- **已经停更约 8.5 个月**：`pushed_at` 与提交记录都停在 **2026-02-23**，页面上 2026-11-05 的 `updated_at` 是 Star / 关注刷新，不要当成"仍在更新"。这意味着新出现的集成不会进入这份清单。
- **中文版落后于英文版**：中文版 16 类、英文版 20 类；只看中文版会漏掉 Visual Studio / Discord Bots / Emacs / Providers 相关条目。
- **"被收录"不等于"官方背书质量"**：清单是索引性质，条目多为第三方项目，**不标注被收录项目的活跃度、许可或安全状况**——要用之前仍得回到各自仓库核验（本 wiki 中 [Chatbox](chatbox-桌面AI客户端社区版.md) 与 [cherry-studio](cherry-studio-多模型Agent与知识库的桌面工作台.md) 就是被收录的第三方客户端，它们的许可与维护状态都得单独看）。
- **CC0-1.0 的边界**：CC0 覆盖的是本仓库自身内容；列出的项目名称、logo、描述文本版权归各自所有者，引用时仍要注意。部分图标是外链（含 `github.com/user-attachments/...` 与第三方图床），存在失效风险。
- **HTML 表格排版**：条目用 `<table>` 而非 Markdown 表格，纯文本 / 终端类渲染器里可读性较差，也更容易出现链接断行。
- 本笔记**只读了中英文 README、LICENSE、元数据与提交记录**，未逐条点开清单中的项目、未核验条目链接是否仍有效。

## 什么时候会再看

- 需要给某个软件（客户端 / 插件 / IDE / 框架）接 DeepSeek，想先看有没有现成方案时。
- 想确认某个项目"是不是官方认可 / 收录"时——先查这份清单，避免把第三方自述当官方。
- 想参考"**官方如何维护一份多语言、图文表格排版的 awesome 清单**"时（包括它停更后的样子）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为 `deepseek-ai` 官方组织的仓库**：[deepseek-harness：DeepSeek 开源的 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)——那是官方开源的插件化 Agent 框架（`dsh`），本篇是官方维护的第三方集成清单。一个"官方自己做的工具"，一个"官方收录别人做的集成"。
- **被本清单收录的桌面 AI 客户端**：[chatbox：桌面 AI 客户端社区版](chatbox-桌面AI客户端社区版.md)、[cherry-studio：把模型、Agent 与知识库收进一个桌面工作台](cherry-studio-多模型Agent与知识库的桌面工作台.md)——两者在清单里都有官方写的 `docs/chatbox/README_cn.md` 与 `docs/cherrystudio/README_cn.md`。可对照"被官方清单收录"与"项目自身的许可 / 维护状态"这两件独立的事。
- **同为 awesome 清单，但治理与商业色彩不同**：[public-apis：人工策展的免费 API 清单](public-apis-人工策展的免费API清单.md)——本篇由**厂商官方**维护、许可 **CC0-1.0**、已停更；那篇由**社区组织**维护、**MIT**、仍在更新，且**顶部带赞助方推广**。可对照"官方清单"与"社区清单"在许可、更新节奏与商业内容上的差别。
- **都涉及"把多家服务接起来"，产物不同**：[aimux：用 Rust 写的统一 LLM 接入层](aimux-用Rust写的统一LLM接入层.md)——本篇是**官方文档索引**（列"哪些软件能接 DeepSeek"，本身不是代码），aimux 是**可安装的代码库**（自己就是接入层，覆盖多家 provider）。可对照"**清单**"与"**实现**"。

## 参考来源

- 项目仓库与中文 README：https://github.com/deepseek-ai/awesome-deepseek-integration/blob/main/README_cn.md
- 英文 README：https://github.com/deepseek-ai/awesome-deepseek-integration/blob/main/README.md
- 许可证：https://github.com/deepseek-ai/awesome-deepseek-integration/blob/main/LICENSE（CC0-1.0）
- 官方开放平台（API key）：https://platform.deepseek.com/
- 元数据来源（2026-11-05）：ungh.cc 镜像、GitHub 页面内嵌 JSON、shields.io 徽章；停更判断来自 `commits/main.atom`（最新提交 2026-02-23）。GitHub API 匿名配额用尽，未取到仓库体积。
