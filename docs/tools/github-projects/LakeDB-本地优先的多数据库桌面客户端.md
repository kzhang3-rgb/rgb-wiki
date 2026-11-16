# LakeDB：本地优先的多数据库桌面客户端（专有许可，非开源）

> 记录一个"**免费可用、但不是开源**"的多数据库桌面客户端（`DavLagoHern/LakeDB`）：一套工作区同时管 **MySQL / MariaDB / PostgreSQL / SQLite**，带"**先复核再执行**"的 AI（QuerIA）与结构对比 / 迁移工具。三件必须先看清的事：① **专有许可**——README 与 `LICENSE` 都明写"**免费使用，但不是开源软件**"，公开仓库只放**官方构建、文档与 issue**；② **凭据留在本地、行数据与查询结果不发给 AI**；③ **AI 不自动执行任何东西**——生成的 SQL 必须**显式本地执行**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（214 行）、`LICENSE` 首行与文件清单及元数据；**未下载安装包、未安装、未连任何数据库、未使用 QuerIA、未核对 `docs/PRIVACY.md` / `docs/TERMS.md` / `docs/COMPATIBILITY.md` 正文**）
- 适用范围：**macOS Apple Silicon**（Homebrew cask）、**Windows x64**、**Linux x64**；界面语言 **英文 + 西班牙语**；可管 MySQL、MariaDB、PostgreSQL、SQLite，另**实验性**支持 Amazon Redshift

## 核心结论

**一句话定位（README 自述）**：`Your databases. Your next move.`——一个**免费、本地优先**的 MySQL / MariaDB / PostgreSQL / SQLite 桌面客户端；在**各自独立的连接工作区**里浏览、查询、编辑，可**比较结构、复核迁移**，并用**可见 SQL 的可选 AI**。

**① 许可证是它最容易被误解的地方。** README 正文与 `LICENSE` 第一行都写着"**LakeDB is free to use, but it is not open source.**"；`LICENSE` 首行是 **`LakeDB Proprietary License`**（Copyright (c) 2026 David Lago），**应用源码不以开源方式授权**。这个公开仓库承载的是**官方构建产物、文档、issue 与社区反馈**。所以：**能免费用，不能当开源项目去 fork 改**——引用它时必须写清这一点。

**② "review-first" 是它的 AI 立场。** QuerIA 可以**生成 SQL、帮忙解释或纠正查询错误**，但 **nothing runs automatically**——**没有任何语句会自动执行**，必须**显式"在本地执行"**。README 反复强调两点：**凭据留在本地**，且**数据库的行数据与查询结果不会发给 AI 服务**。还有一个细节：**AI 修正只是"可选"的**，本地也有语法诊断与安全 quick fix。

**③ 它是"正经 SQL 客户端 + 可选 AI"，不是"AI 玩具"。** README 明确"**AI joins the existing LakeDB workflow; it does not replace it**"：连接管理、Monaco 编辑器（方言感知补全、schema 补全、别名 / 列 / 主键索引提示、本地语法诊断）、结果表格（本地过滤、拖动列、虚拟化、分页、排序、带冲突检查与回滚的类型化安全编辑）、备份 / 还原、结构对比与事务性表复制、权限管理（PostgreSQL 角色 / MySQL 账号与授权）都在 AI 之外独立存在。

**④ 商业化是"会员加 AI 额度"，不是锁功能。** README 写：**完整本地 SQL 客户端免费用**；可选的 **Patreon 会员**帮助资助开发，**包含更高的 QuerIA AI 用量上限**；**核心本地功能不锁在会员后面**。

**个人判断（未验证）**：它把"AI 进数据库工具"最危险的两点——**自动执行**与**数据外流**——用"显式执行 + 行数据不发 AI"挡在前面，方向是对的；但它**闭源**，这些承诺**只能靠 README / 隐私文档的表述与成品行为判断，无法审计源码**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [DavLagoHern/LakeDB](https://github.com/DavLagoHern/LakeDB)（描述：`A free, local-first desktop client for MySQL, MariaDB, PostgreSQL and SQLite.`） |
| 归属 | 个人开发者 `David Lago`（`DavLagoHern`）；**contributors 2 人**（shields） |
| 许可证 | **专有（非开源）**：`LICENSE` 首行 `LakeDB Proprietary License`，Copyright (c) 2026 David Lago；README 明写"free to use, but it is not open source"；shields license 报 `not identifiable by github` |
| 主语言 | PowerShell 69.4%（**注意**：这是仓库里**打包 / Homebrew / WinGet 脚本**的语言统计，**不代表应用实现语言**，应用本体源码不在仓库内） |
| 关注度（2026-11-13） | **17 Star / 0 关注 / 2 Fork**（页面与 ungh 一致）；shields：**0 个开放 Issue** |
| 创建 / 最近推送 | **2026-07-19 创建**；`pushed_at` **2026-11-13**（**核验当天仍有推送，项目活跃**）；默认分支 `main` |
| Release / 文件数 | **25 个 Release**（最新 **`v1.0.0`**，2026-11-03；其下为 `v1.0.0-beta.9.0`、`beta.8.0` … 一路到 `v0.11.3-beta.2`（2026-07-24））；125 个文件 |
| 官网 / 文档站 | **`https://davlagohern.github.io/LakeDB/`**（GitHub Pages；仓库页内嵌元数据给出 `pagesUrl`），另在 MariaDB Ecosystem Hub 有收录条目 |
| 关键文档文件 | `docs/PRIVACY.md`（4,263）、`docs/TERMS.md`（1,081）、`docs/COMPATIBILITY.md`（5,922）、`docs/ROADMAP.md`（12,216）、`docs/VERSION-HISTORY.md`（32,624）、`docs/REDSHIFT.md`（2,938）、`docs/LakeDB-Engineering-Enterprise.pdf`（约 2.1 MB） |
| 打包脚本 | `packaging/homebrew/Casks/lakedb.rb`、`packaging/homebrew/update-cask.mjs`、`packaging/winget/**`、`.github/workflows/download-stats.yml`、`.github/workflows/package-managers.yml` |

> 口径说明：数据取自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章**（核验日未直接调用 api.github.com）。**主语言 PowerShell 是打包脚本的统计**，不要据此判断应用技术栈。

## 主要能力（README 表格）

| 区域 | 现在可用 |
| --- | --- |
| 连接 | 多个 MySQL / MariaDB / PostgreSQL / SQLite 连接并存、分组、颜色、只读模式、诊断与搜索；MySQL / MariaDB / PostgreSQL 支持直连、SSH 与 TLS |
| 工作区 | 每个连接独立的 SQL / QuerIA / 表标签页，恢复编辑器内容、已选 schema 与布局 |
| Schema 上下文 | PostgreSQL 的库、schema、`search_path`、表、视图、物化视图、函数、过程、序列、类型；MySQL / MariaDB 系统 schema 与可视化关系图 |
| SQL 编辑器 | **Monaco Editor**、方言感知关键字补全、主动 schema 补全、别名 / 列 / 主键索引提示、本地语法诊断、安全 quick fix、可选 AI 纠正、格式化、Explain、事务、历史与流式导出 |
| QuerIA | **每连接可复用的业务上下文**、Normal 与 Agentic 生成、跨库关系、索引检查、`EXPLAIN` 复核、**可见 SQL 与显式本地执行** |
| 结果与数据 | 本地结果过滤、可拖动列、虚拟化表格、分页、搜索、排序、**带冲突检查与回滚的类型化安全编辑** |
| 数据库工具 | PostgreSQL schema 备份 / 还原、对比与事务性表复制；MySQL / MariaDB 备份、还原、对比与迁移；引擎不支持的能力**保持禁用** |
| 权限管理 | 查看 PostgreSQL 角色 / MySQL 账号与授权，**应用前先复核 SQL** |
| 安全 | **本地加密凭据**、生产环境确认、渲染进程沙箱、只读强制；**数据库流量由本机直达所配主机** |
| 韧性 | 稳定设备标识、崩溃恢复、会话恢复、校验更新、配置备份与诊断 |

**"对比与迁移"的口径（README 特别澄清）**：`Model`（默认，比结构，**不读行**）、`Exact row counts`（可选，比行数，**行数相等不代表值相等**）、`Content / checksums`（可选，比内容校验和，**读表数据、大表昂贵**）、`Merge`（插入 / 更新并保留目标独有行，**不是逐行差异下载**）、`Replace data`（**破坏性**，先删目标行再复制）。README 提示：**在生产库上先从 model 对比与小范围开始**，迁移前复核 SQL 计划、目标与依赖。

## 安装与平台

- **macOS Apple Silicon（Homebrew）**：`brew tap DavLagoHern/lakedb` → `brew trust DavLagoHern/lakedb` → `brew install --cask lakedb`；官方 tap **跟踪已校验的 Release 与 SHA-256 校验和**。
- **直下**：macOS `.dmg` / `.zip`、Windows `setup.exe` / `portable.exe`、Linux `.AppImage` / `.deb`。
- **签名状态（README 明确区分）**：**macOS Apple Silicon 包经 Apple Developer ID 签名并公证**；**Windows 包尚未用受信任证书签名**。每个包在 **`v1.0.0` Release 页有对应 SHA-256 文件**。
- **从 beta 迁移**：`1.0.0` 是首个去掉 beta 标签的版本；若用过历史的 `1.0.0-beta.*`，**需手动安装**（旧更新通道可能不提供过渡），**保留现有应用数据**；早期 `v0.9.0` 源码归档在 `legacy/v0.9.0-20260721`。
- **安装更新**会保留本地配置，**不会迁移已连接的数据库**；**数据库变更需要显式用户操作**。

## 使用前需要注意

- **专有许可，不可审计（最重要）**：README 与 `LICENSE` 都写明**不是开源软件**。**"凭据本地加密""行数据不发 AI""本地执行"这些安全承诺，无法通过看源码核实**——只能依据官方文档与成品行为判断。要用于生产库前请自行评估。
- **Windows 包未受信任签名**：README 自己写了这一点；Windows 用户需接受未签名安装（**仅在官方 LakeDB 仓库下载**并**核对 SHA-256**）。
- **Redshift 仍是实验性**：README 标注"**official AWS Python connector 或 AWS Data API；live AWS validation remains pending**"——**未在真实 AWS 上验证**。
- **AI 用量与会员挂钩**：核心本地功能免费，但**更高的 QuerIA AI 用量**通过 Patreon 会员提供；**免费额度与限额的具体数值我未核实**。
- **"对比 / 迁移"可能很贵甚至危险**：`Content / checksums` 会**真读表数据**、大表昂贵；`Replace data` 是**破坏性**操作；README 自己建议**生产库从小范围开始**。**这些操作的风险由使用者承担**。
- **关注度很低**：**17 Star / 2 Fork**、contributors 2 人——**社区验证样本极少**，出问题时能参考的第三方经验有限。
- **主语言的统计有误导性**：仓库只含**打包脚本与文档**，语言统计到 PowerShell，**不能推断应用实现技术**。
- **我只读了 README、`LICENSE` 首行与文件清单及元数据**：**未下载安装包、未安装、未连库、未用 QuerIA、未读 `PRIVACY.md` / `TERMS.md` 正文**；安全与隐私表述均来自 README 自述。

## 什么时候会再看

- 需要**一个客户端同时连 MySQL / MariaDB / PostgreSQL / SQLite**、又想要**可选 AI 但默认不自动执行**时。
- 想参考"**AI 进数据库工具时如何限制它**"的做法：**显式执行、可见 SQL、只读连接、生产环境确认、行数据不发 AI**。
- 想研究"**免费专有 + 赞助会员**"这种**非开源的独立软件**如何用公开仓库做分发、文档、issue 与社区（可与本 wiki 里其它"源码可见 / 专有"许可样本对照）。
- 想对比"**本地优先客户端如何把凭据与数据留在本机**"这条设计取向时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"本地优先、数据敏感的桌面应用"，许可相反**：[wealthfolio：本地优先的个人财务追踪器](wealthfolio-本地优先个人财务追踪器.md)（**AGPL-3.0，开源可审计**）、[augur：本地优先的投资研究记忆系统](augur-本地优先的投资研究记忆系统.md)——两篇都是"数据留本机"的开源样本；**本篇同为本地优先，却是专有许可、不可审计**。可对照"**本地优先**"与"**是否开源**"其实是两个独立维度。
- **同属"非 OSI 开源许可"的样本**：[baocut：带可编辑时间轴的 AI 视频 Agent](baocut-带可编辑时间轴的AI视频Agent.md)（`BaoCut Community License`，源码可见）、[PriceAI：AI 订阅卡网渠道比价工具](PriceAI-AI订阅卡网渠道比价工具.md)（`Source Available License`）、[qwen-image-local：在 8 GB 显卡上本地跑 Qwen-Image 的桌面工具](qwen-image-local-在8GB显卡上本地跑Qwen-Image的桌面工具.md)（MIT 代码 + **非商用权重**）——本篇是**完全不公开源码的专有许可**。几篇放一起看"**免费 / 可读源码 / 可商用**"三者如何被切分。
- **同属"面向开发者 / 运维的桌面与自托管工具"**：[postcat：终端里的键盘优先 HTTP 调试 TUI](postcat-终端里的键盘优先HTTP调试TUI.md)（API 调试）、[LeePanel：靠本地 SSH 管理服务器的开源 Linux 面板桌面端](LeePanel-靠本地SSH管理服务器的开源Linux面板桌面端.md)（服务器面板）——都是"把开发/运维操作收进一个界面"的工具，可对照许可与本地化程度。

## 参考来源

- 仓库主页与 README（214 行；核验日期 2026-11-13）：https://github.com/DavLagoHern/LakeDB
- 许可证：`LICENSE`（`LakeDB Proprietary License`，1,553 字节，首行已核验；README 明写"not open source"；核验日期 2026-11-13）
- 官网 / 文档站：https://davlagohern.github.io/LakeDB/
- Release 列表（最新 `v1.0.0`，2026-11-03；共 25 条）：https://github.com/DavLagoHern/LakeDB/releases
- 相关文档（README 指向，未打开正文）：`docs/PRIVACY.md`、`docs/TERMS.md`、`docs/COMPATIBILITY.md`、`docs/ROADMAP.md`、`docs/VERSION-HISTORY.md`、`docs/REDSHIFT.md`
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 贡献者）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13）
