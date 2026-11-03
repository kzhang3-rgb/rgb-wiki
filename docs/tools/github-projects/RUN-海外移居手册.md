# RUN：海外移居手册

> 记录一本"用结构化数据生成的手册式仓库"——它本身不是工具，而是把 36 个欧洲国家 + 13 个欧洲以外目的地的移居路线整理成可查询的 Markdown；同时记下它的分许可结构、以及 badge 数字与正文数字对不上的一处口径问题。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README 全文 305 行、DISCLAIMER、CONTRIBUTING、LICENSE / LICENSE-CODE、`data/programmes.csv` 全量统计、目录树 149 个文件与 GitHub API 元数据；**未逐条核对任何一条路线数据与官方来源**）
- 适用范围：面向中国大陆申请人；直接在 GitHub 上在线阅读即可，无需安装；数据自述核查时间为 2026 年 7–10 月

## 核心结论

这是一个**知识库型仓库，不是软件**。它把海外移居整理成三层：`src/`（源数据）→ `data/`（结构化数据）→ `docs/`（生成的国家页与路线页），用 `scripts/build_docs.py` 把前者产出后者。这就是为什么一个纯内容仓库的主语言是 **Python**。

**第一件要写清的事是许可分持**：内容用 **CC BY-SA 4.0**（`LICENSE`，20,138 字节），代码另持 **MIT**（`LICENSE-CODE`，22 行，`Copyright (c) 2026 RUN contributors`）。README 末尾也明确写了"文档内容采用 CC BY-SA 4.0，脚本采用 MIT"。**转载或改写它的国家页要按 CC BY-SA 署名并以相同协议共享**，不能按 MIT 理解。

**第二件是内容性质**：它**不是一手核实**。`DISCLAIMER.md` 与 README 都写明"欧洲篇内容改写自 [LetsMigrate.org](https://letsmigrate.org/zh-hans/countries/) 公开资料及其引用的官方来源"，全球篇是公开检索整理，并声明"不构成任何移民、法律、税务、投资、金融、就业或搬迁建议""任何决定前请以各国政府官方页面为准"。**欧洲以外部分作者自陈"核查深度低于欧洲篇"。**

**第三件是一处可验证的口径不一致**：README 顶部 badge 写「移民路线 **110**」，而正文"实用指南"表里把 `data/programmes.csv` 描述成"**99 条**路线的结构化数据"。实测这份 CSV 是 **110 行数据**（去掉表头），与 badge 一致——正文那个 99 才是陈旧的；README 末尾的致谢段给出了拆解："**99 条**来自 LetsMigrate.org…另有 **11 条 RUN 补充路线**"（99 + 11 = 110）。

**第四件是关注度结构**：核验日 **2,426 Star**，但 **`subscribers_count` 只有 7**，仓库 **2026-10-29 才创建**（核验时仅 5 天）。这是典型的"短期热度极高、长期关注尚未沉淀"的组合，**Star 数在这里不能当质量指标用**。

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [BryceYuuu/RUN](https://github.com/BryceYuuu/RUN) |
| 归属 | 个人账号 BryceYuuu |
| 定位 | 读作"润"，海外移居手册（欧洲 36 国 · 110 条路线 · 13 个主要移民地区） |
| 许可证 | **内容 CC BY-SA 4.0**（`LICENSE`，20,138 B）；**代码 MIT**（`LICENSE-CODE`，22 行） |
| 主语言 | Python（仅为 `scripts/build_docs.py` 生成管线，仓库中没有可运行的应用代码） |
| 关注度 | **2,426 Star / 171 Fork / 订阅者仅 7**（`subscribers_count`；`watchers_count` 与 Star 数恒等，是 GitHub 历史遗留）/ **1 个开放 Issue** |
| 活跃度 | **2026-10-29 创建**（核验时仅 5 天）；`pushed_at` **2026-11-03T07:23:28Z**，`main` 最新提交 **2026-11-03T07:22:53Z** |
| 仓库体积 | 约 **352 KB**（纯文本 + 5 张 SVG 图；无 PDF、无二进制大文件） |
| 仓库结构 | 149 个文件：根目录 7 个 + `assets/charts/`(5 SVG) + `data/`(5) + `docs/europe/`(37) + `docs/global/`(14) + `docs/routes/`(7) + `docs/practical/`(3) + `docs/resources.md` + `src/`(69) + `scripts/build_docs.py` |
| 默认分支 | `main` |
| 元数据补充 | `homepage` 为 `null`、`topics` 为空数组（未做仓库元数据整理）；`has_projects` / `has_wiki` 为 `true` |
| 赞助入口 | 爱发电（微信 / 支付宝）+ GitHub Sponsors；但 `CONTRIBUTING.md` 明确"不接受中介广告、引流链接、'保证获批'类内容" |

## 它能做什么

- **按身份找路线**：README 首页分六类入口——数字游民（15 条欧洲路线，月入 €330 起）、技术工作 / 欧盟蓝卡（50+ 条，最快 21 个月永居）、留学 → 工作 → 移民、创业 / 自雇、被动收入 / 退休（葡萄牙 D7 €920/月起）、投资居留 / 入籍。
- **按国家读"生活与身份档案"**：欧洲 36 国各一页（`docs/europe/<国家>.md`），除路线外还写生活成本、房租、安全、平均工资、个税、护照、永居与入籍年限、是否承认双重国籍、中国护照入境方式；`docs/europe/README.md`（37 KB，全仓最大文件）是横向对比表。
- **欧洲以外 13 个目的地**：美国（H-1B / NIW / EB-5）、加拿大、澳大利亚、新西兰、日本、韩国、新加坡、香港、菲律宾、泰国、马来西亚、阿联酋、拉美。
- **"2025–2026 必须知道的变化"表**：西班牙黄金签证 2025-04-03 关闭、马耳他投资入籍 2025-07 被欧盟法院判决后废止、日本经营管理签证资本金 500 万 → 3,000 万日元、葡萄牙入籍由 5 年改为一般 10 年、拉脱维亚 2026-09-15 关闭买房居留、美国 H-1B 抽签改为按工资加权等。
- **5 张门槛对比图**：数字游民收入门槛、欧盟蓝卡薪资门槛、被动收入居留门槛、投资居留最低投资额、生活成本指数（`assets/charts/*.svg`）。
- **实用指南**：我该走哪条路（四步判断）、留学转移民期限对比、生活与身份对比表、中国申请人文件准备（海牙认证 / 无犯罪 / 学历认证 / 拼音 / 资金合规）、税务居民入门（183 天规则）、落地前 90 天清单、资源导航。
- **可直接用的结构化数据**：`data/programmes.csv` 实测 **110 行数据**，列为 `country / country_slug / programme / route_type / status / family / pr / threshold / income_eur_month / verified / source / url`；路线类型分布 **work 55 · digital-nomad 15 · residence-by-investment 13 · self-employed 12 · startup 5 · citizenship 4 · passive 4 · other 2**，状态分布 **open 105 / closed 5**；每行带 `verified`（核实日期，如 2026-10-25）与 `source`。
- **可参考的工程做法**：`src/notes/`（36 份欧洲国家笔记）、`src/details/`（18）、`src/global_notes/`（13）、`src/global.json`、`src/extra_routes.json` 是源，`docs/` 是产物，`scripts/build_docs.py` 负责生成；README 用 `<!-- AUTO:countries -->`、`<!-- AUTO:changes -->` 标记包裹自动生成区块——**同类"内容站当代码仓库维护"的流水线，可以只读它的结构**。

## 使用前需要注意

- **移民信息时效性极强**：2025–2026 年大量路线关闭或改规则（README 自己强调"网上很多旧攻略已经失效"）。本手册自述核查时间为 2026 年 7–10 月，**每条数据的有效期都很短**，决定前必须回到各国官方页面。
- **不是一手核实，也不是专业建议**：内容改写自 LetsMigrate.org 等公开资料，`DISCLAIMER.md` 明确"不构成移民、法律、税务、投资、金融、就业或搬迁建议""使用本手册所产生的任何后果由使用者自行承担"，并声明与 LetsMigrate.org 及任何政府、中介机构**无关联**。
- **欧洲以外部分核查深度较低**：作者在 README 里主动说明全球篇依据 2026-10 的公开检索整理，"核查深度低于欧洲篇"。
- **CC BY-SA 4.0 有传染性**：改写或转载它的正文需要署名并以相同协议共享；若想拿它的内容做商业或闭源产物，先读许可条款。抓 `programmes.csv` 这类数据文件也一样受内容许可约束。
- **Star 数不代表质量**：2,426 Star / 7 订阅者 / 建仓 5 天，热度与沉淀严重脱节，**别用它判断这份手册是否可靠**。
- **仓库极新，维护持续性未知**：2026-10-29 创建，核验时只有 4 次提交（最近一次 2026-11-03，另有一次 2026-11-01 的 "Add sponsor section to README"）。路线数据的长期更新能力尚待观察。
- **README 数字需交叉核验**：badge 的「110 条路线」与正文表格的「99 条」不一致（实测 CSV 为 110 行），这类自述数字建议以数据文件为准。
- **本笔记只记录了结构与元数据**：未逐条核对任何一条路线的门槛、时限与来源是否与官方一致，**内容准确性未经我验证**；对移民法律与操作路径不作任何判断。

## 什么时候会再看

- 需要一个**按国家 / 按身份两条轴**快速检索移居路线的入口，或想批量拿一份结构化的路线清单（`programmes.csv`，带门槛、月收入要求、核实日期与来源链接）时。
- 想了解 **2025–2026 年哪些路线关闭或收紧**（黄金签证、投资入籍、日本经管签、葡萄牙入籍年限等）时，这份变化表可作为一个索引，再回官方页面确认。
- 想参考**"结构化数据 → 脚本生成静态内容站"的中文项目组织方式**，或"内容与代码分持两个许可（CC BY-SA + MIT）"该如何标注时。

## 相关笔记

- **同为"内容与代码分持两个许可"的仓库**：[up 人生进阶指南](up-人生进阶指南.md) 用 `LICENSE-CONTENT.md` + `LICENSE-CODE.md`，本篇用 `LICENSE`（CC BY-SA 4.0）+ `LICENSE-CODE`（MIT）——两本中文书稿都选择把正文与工具分开授权。
- **同为面向中国大陆的实用决策型中文仓库，但来源可核实性不同**：[HowToLiveBetter 高性价比人生指南](HowToLiveBetter-高性价比人生指南.md) 每一条都带证据等级与一手出处（DOI / 官方文件）；本篇是**改写第三方整理**并声明以官方页面为准——可对照"一手引用"与"二手整理"两种做法在可核查性上的差距。
- **同属"结构化数据 → 脚本批量生成静态内容"的管线**：[lexfridman-cn](lexfridman-cn-LexFridman播客中文站.md) 同样是内容随仓库入库、一条构建命令全量生成，并涉及"改写第三方内容"的版权边界划分——两者的许可处理方式不同（那边代码 MIT、精读不受 MIT；这边内容 CC BY-SA 4.0、代码 MIT）。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 仓库主页：<https://github.com/BryceYuuu/RUN>
- 免责声明：<https://github.com/BryceYuuu/RUN/blob/main/DISCLAIMER.md>
- 贡献指南（含"只接受可核查信息"的来源规则）：<https://github.com/BryceYuuu/RUN/blob/main/CONTRIBUTING.md>
- 路线结构化数据：<https://github.com/BryceYuuu/RUN/blob/main/data/programmes.csv>
- 内容许可（CC BY-SA 4.0）：<https://github.com/BryceYuuu/RUN/blob/main/LICENSE>
- 代码许可（MIT）：<https://github.com/BryceYuuu/RUN/blob/main/LICENSE-CODE>
- 主要二手来源（作者自述）：<https://letsmigrate.org/zh-hans/countries/>
- 作者参考的中文同类项目：`The-Run-Philosophy-Organization/run`、`Knowckx/awesome-run`、`lidangzzz/How-to-run`
- 核验时间：2026-11-03（GitHub API 元数据、`git/trees/main?recursive=1`、`commits?sha=main`、raw 通道的 `README.md` / `DISCLAIMER.md` / `CONTRIBUTING.md` / `LICENSE-CODE` / `data/programmes.csv`）
