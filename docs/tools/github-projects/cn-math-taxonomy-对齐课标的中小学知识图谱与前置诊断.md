# cn-math-taxonomy：对齐课标的中小学知识图谱与前置诊断

> 记录一个把中小学课标拆成"知识点 + 先修边"的开放数据集，以及它附带的静态站点与自适应前置诊断。仓库名还叫数学（`cn-math-taxonomy`），内容已经从数学扩到语文、科学、英语、物理、历史、地理、生物、道德与法治八个学科。未部署、未导入数据、未运行任何脚本。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README（572 行）、`LICENSE`、`package.json`、`CHANGELOG.md` 存在性、目录树、提交与 Release 元数据，并下载 `data/topics.json`、`data/dependencies.json`、`data/questions.json` 实测计数；**未 clone、未启动静态服务器、未运行 `validate.py` / `verify_questions.py`、未逐条核对任何知识点或题目**）
- 适用范围：纯静态站点（浏览器 `fetch` 读 `data/*.json`，需本地起一个静态服务器）；数据在浏览器侧只写 `localStorage`、不上传；校验脚本仅需 Python 标准库

## 核心结论

**① 它的产物是数据，不是软件。** 仓库的主体是三个 JSON：`data/topics.json`（知识点节点）、`data/dependencies.json`（先修边）、`data/questions.json`（题库），另加 `data/cn-curriculum-standards.json`（课标条目）。我实测（2026-11-04）：

| 文件 | 实测总量 | 分学科（按 id 前缀） |
| --- | ---: | --- |
| `topics.json` | **3063 个知识点** | 数学 `cnm` 870、英语 `cne` 791、物理 `cnp` 269、科学 `cns` 222、语文 `cnc` 493、道德与法治 `cnd` 159、历史 `cnh` 134、地理 `cng` 59、生物 `cnb` 66 |
| `dependencies.json` | **4545 条先修边**（hard 2421 / soft 2124） | 数学 1459、英语 1028、物理 637、科学 288、语文 656、道法 171、历史 141、地理 76、生物 89 |
| `questions.json` | **12399 道题** | 数学 3627、英语 3164、物理 1076、科学 888、语文 1972、道法 636、历史 536、地理 236、生物 264 |

**② README 的分学科数字经得起核，但"总数"和仓库描述都滞后。** README 表格里逐学科写的（数学 870 知识点 / 1459 边 / 3627 题；英语 791 / 1028 / 3164；物理 269 / 637 / 1076）与实测**完全一致**；但同一份 README 的「当前统计」区块（标注日期 **2026-10-23**）仍写"知识点总数约 2615""全图合计约 3003 条"，而**仓库 description 更是停在"1644 个知识点、2403 条先修边、6723 道题"**——三处数字分别是 3063 / 4545 / 12399 的早期快照。原因是语文、科学、道法、历史、地理、生物都是 10 月下旬之后陆续加进来的，`description` 与统计区块没有跟着回填。

**③ "AI 生成 + 未经教师审核"被写进了正文，并配了三重自查。** README 明写"先修关系、hard/soft 强度、诊断题难度与区分度都没有经过教师或学生数据检验""**题目与答案全部由 AI 生成、未经教师审核**"，还专门点出一条**自指的核验漏洞**："高中题和英语题没有做盲解，`verify` 和答案来自同一条表达式"。与之配套的是三段式校验：`scripts/validate.py`（JSON 合法性、id 唯一、**先修图无环**、课标 id 引用存在）、`verify_questions.py`（受限表达式求值，自述 auto_verified 2823 / mismatch 0 / unverifiable 532）、`second_pass.py`（初中 1401 题盲解复核，原始不一致 3 处且全是解题者算错）。

**④ 数据来源是分级标注的，不是一律"已对齐课标"。** 官方已核（国家中小学智慧教育平台 unit 目录）、第三方目录待核（dzkbw.com、xuexili.com、dzskb.com）、"非官方、待核"（九下与多册小学目录）三类标得很清楚；`docs/standards-mapping.md`、`docs/moe2025-alignment.md`、`docs/questions.md` 分别交代映射规则、与 2025 修订版的差异、题库方法与局限。

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [xiaoyang111222/cn-math-taxonomy](https://github.com/xiaoyang111222/cn-math-taxonomy) |
| 描述 | "数学（小学到高中）、英语（三年级到高中）、初中物理的知识图谱：1644 个知识点、2403 条先修边、6723 道题…"（**数字已明显滞后，见上**） |
| 站点 | 生产站点 https://math.pansgo.com/ （Cloudflare Pages）；GitHub Pages 备份 `https://xiaoyang111222.github.io/cn-math-taxonomy/` |
| 许可证 | **CC BY-NC 4.0**（`LICENSE` 19,347 字节 / 409 行，即 Attribution-NonCommercial 4.0 全文）——署名 + **非商业**；`assets/vendor/` 内 Cytoscape 3.30.4、dagre 0.8.5、cytoscape-dagre 2.5.0、qrcode-generator 2.0.4 仍各自 **MIT**（见 `assets/vendor/VENDOR.md`） |
| 主语言 | Python（但仓库主体其实是 JSON 数据 + 静态 HTML/JS；Python 只用于 `scripts/` 下的生成与校验） |
| 关注度 | **6,464 Star / 1,674 Fork / 订阅者仅 26 / 20 个开放 Issue**（GitHub API，2026-11-04） |
| 活跃度 | **2026-10-18 创建**（核验时仅 17 天）；`pushed_at` **2026-11-04**；单分支 `main`；提交密集（所有者 118 次 + 5 位外部贡献者），10-29 至 11-04 几乎每天多次提交 |
| 发行版 | **无 Release**（以仓库 + 静态页分发） |
| 体积 | API `size` 39,294 KB ≈ **38 MB**；默认分支树 377 个文件 / 合计 **26,624 KB ≈ 26 MB**；最大单文件 `data/questions.json` **8.28 MB**（另有 `topics.json` 2.67 MB、`dependencies.json` 897 KB、`CHANGELOG.md` 102 KB、README 118,697 字节 / 572 行 / 56,972 字符）；`screenshots/` 58 张 |
| 数据格式 | 声明参考 [Marble os-taxonomy](https://github.com/withmarbleapp/os-taxonomy) 的 `topics` + `dependencies` 两文件结构（并注明其中文分支 `os-taxonomy-beijing` 只读了 README、**未解析其数据文件**） |
| 商业化 | README 顶部即放**商业授权说明**：个人学习 / 教学 / 研究免费，商业使用需另行授权（X 私信 `@hassanyehiaa`、邮箱 `499749204@qq.com`、或仓库 Issue） |

## 它能做什么

**数据层：一张按学段分色、带 hard/soft 权重的先修图。**

- 节点字段含 `id` / `name` / `domain`（数与代数、图形与几何、统计与概率、综合与实践）/ `grade` / `semester` / `chapter` / `type`（conceptual、procedural、reasoning、application、modeling）/ `description` / `mastery_criteria` / `diagnostic_question` / `standards`（课标映射）/ `root_reason`。
- 边为 `{topicId, prerequisiteId, strength, reason}`，`hard` 表示"不会前置基本学不了"，`soft` 表示"有帮助但非硬门槛"；做过**传递约简**，只保留直接先修。数学图的根是 `cnm_p001`（1 到 5 各数的认识），英语图的根是 `cne_p001`（字母表），**数学与英语互不连边**。
- 旧的小学外部节点 `cnm_ext_001`–`018` 已删除，`data/id-aliases.json` 保留旧 id → 新小学知识点的跳转，避免历史链接失效。

**站点层：三个纯静态页面 + PWA。**

- 「知识图谱」（`explore/`）用 Cytoscape + dagre 只画**当前知识点的局部图**（默认上溯 2 层先修、下探 1 层；硬边实线、软边虚线；学段分色），不默认铺开全图；超过 40 点先按年级收起。
- 「诊断」（`diagnose/`）是一个可离线的**自适应前置诊断**：选目标知识点或整章，沿先修边出题（同一知识点连续答对默认 3 题判定掌握并跳过祖先；答错再出一道更简单的确认题，确认题也错才算薄弱并回溯）；输出薄弱根因、不稳知识点、**按年级从低到高排列的学习路径**、相关课标原文，以及一张 Cytoscape 路径图。进度存 `localStorage`，可导出 JSON、打印、生成 1080×1920 分享图。
- 首页统计卡不写死，打开时按当前学科从数据文件实时计数；**不加载任何外网 CDN**（图形库与二维码库都放在 `assets/vendor/`）；统计钩子默认不发任何请求。
- 另有 `children/`（孩子档案、盲区与历史，仅本机 `localStorage`）与 PWA（`sw.js` / `manifest.webmanifest`，可离线）。

**校验层：三个脚本 + 一份变更日志。**

```bash
python3 scripts/validate.py          # 结构与先修图校验（应输出 VALIDATION PASSED）
python3 scripts/verify_questions.py  # 题库答案自动核验（受限表达式求值）
python3 -m http.server 8000          # 本地打开站点
```

## 使用前需要注意

- **内容未过人工审核，这是作者自己第一条写的局限。** "审核缺位""题库很小（每点 4–5 题，诊断时最多问 2 题）""粒度不统一""部分先修边反映的是教材编排而非严格逻辑依赖"。**不能当权威测评或教学依据使用**。
- **`hard` / `soft` 的划分是 AI 判定的。** README 原话："这个区分是 AI 的主观判断，未经审核。"点开数据之前先知道这一点。
- **"已验证"的边界很窄。** `verify_questions.py` 用受限表达式重算答案，所以能通过的题只是"表达式自洽"；高中题和英语题**没有盲解**，`verify` 与答案出自同一条表达式——这是作者主动承认的循环校验问题。真正做过独立第二遍盲解的是初中那 1401 题。
- **教材目录还有多处标着"待核"。** 九下、多册小学目录来自第三方站点（dzkbw.com / xuexili.com / dzskb.com），非官方逐页核对；英语的 PEP / Go for it! / 高中七册单元目录同样"没有逐页核对"。
- **许可与 `package.json` 口径不一致。** `LICENSE` 是 **CC BY-NC 4.0**，而 `package.json` 里写的是 `"license": "ISC"`、`"test"` 还是 `echo "Error: no test specified"`——`package.json` 整体像样板残留（版本停在 1.0.0），**以 `LICENSE` 为准**。非商业限制比 CC BY-SA 4.0 更强：**不得用于付费课程、付费 App、机构收费服务、售卖题库**，商业使用须先取得单独授权。
- **仓库名与内容已明显不符。** 名字叫 `cn-math-taxonomy`，实际已覆盖八个学科；`description` 的统计数字也停在早期（1644 / 2403 / 6723），读之前不要被 description 误导。
- **改动节奏很快，本笔记的数字是快照。** 2026-10-30 加入初中历史、10-31 加入初中地理、11-03 加入初中生物，11-02 还发生过一次"去掉『AI 生成初稿、未经教师审核』状态声明"的提交（**正文与局限章节里的同类披露仍在**）。引用具体数量前请重新计数。
- **我只做了数据计数与文档阅读。** 未 clone、未起服务、未运行脚本、未打开站点，因此 `VALIDATION PASSED` 之类只是转述作者自述，**我未复现**。

## 什么时候会再看

- 需要一份**可离线、可自托管的 K12 知识点 / 题库 JSON**（含课标映射与先修关系）做二次开发或做检索实验时；
- 想参考"**课标对齐 → 知识图谱建模（DAG + hard/soft 先修 + 传递约简）→ 自适应前置诊断 → 多重自查脚本**"这一整套做法时；
- 想看"**AI 大批量生成内容如何写诚实披露**"——局限、待核、自指漏洞都摆出来——的写法样本时；
- 想对比"结构化数据 → 脚本生成的静态内容站"这类仓库的许可策略（本篇主体 CC BY-NC + 第三方依赖保持 MIT）时。

## 相关笔记

- **同为"结构化数据 → 脚本生成静态内容站"**：[RUN：海外移居手册](RUN-海外移居手册.md)——那边是 `src/` → `data/` → `docs/`、由 `scripts/build_docs.py` 生成，内容是改写公开资料、非一手核实；本篇是 `data/*.json` → 静态页面、由 `scripts/validate.py` 校验。两篇都出现过"宣称数字与实际文件不一致"，也都把内容与第三方依赖的许可分开处理。
- **另一条"降低英语学习门槛"的路线**：[everyone-can-use-english：人人都能用英语](everyone-can-use-english-人人都能用英语.md)——那是给成人的学习方法与"一千小时"训练体系；本篇是三年级到高中的**英语知识图谱 + 题库 + 前置诊断**，管"哪里不会、该补哪一课"，不管"怎么坚持学"。
- **同属中文结构化教学资源**：[Foundations-of-LLMs：大模型基础教材](Foundations-of-LLMs-大模型基础教材.md)——那是浙江大学团队按章组织的大模型教材（PDF 分发）；本篇是 K12 课标对齐的知识图谱（JSON + 静态站）。一个是"读一套书"，一个是"查一张图"。
- **同为"中文 + 面向学生 + 内容为整理"，许可策略不同**：[Ac-Wiki：大学生的百科全书](Ac-Wiki-大学生的百科全书.md)——那边是 `docs/` 下的**文档内容**（正文 CC BY 4.0、源码 MIT，**均可商用**），本篇是 `data/*.json` + 静态站的**结构化内容**（主体 **CC BY-NC 4.0**，**含非商业限制**）。两篇都要面对"整理内容的口径与时效"问题，可对照许可分层与限制程度的差别。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/xiaoyang111222/cn-math-taxonomy
- 生产站点：https://math.pansgo.com/ ；GitHub Pages 备份：https://xiaoyang111222.github.io/cn-math-taxonomy/
- 许可原文：https://github.com/xiaoyang111222/cn-math-taxonomy/blob/main/LICENSE （CC BY-NC 4.0）
- 数据与文档：`data/topics.json`、`data/dependencies.json`、`data/questions.json`、`data/cn-curriculum-standards.json`、`docs/standards-mapping.md`、`docs/questions.md`、`docs/moe2025-alignment.md`
- 校验脚本：`scripts/validate.py`、`scripts/verify_questions.py`、`scripts/second_pass.py`

以上信息核验于 2026-11-04（Star 数、学科范围与各文件数量都会继续变化）。**未 clone、未运行、未部署、未导入任何数据，未逐条核对知识点与题目**；知识点 / 先修边 / 题目数量为当日下载 `data/*.json` 后实测计数，其余为阅读文档与元数据所得。
