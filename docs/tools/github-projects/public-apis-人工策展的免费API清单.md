# public-apis：人工策展的免费 API 清单

> 一句话值得记住的取舍：它是 GitHub 上 Star 数最高的仓库之一（近 50 万），内容却"只是一份 Markdown 清单"——**主体是 `README.md` 里按分类排列的 API 表格**（列固定为 `API | Description | Auth | HTTPS | CORS | Call this API`），靠社区人工策展、只收"完全免费或至少有免费层、且不需要先买设备/服务"的条目；配套的 Python 脚本只做两件事：**校验条目格式、校验链接（含查重）**，并用 GitHub Actions 在 PR 上跑。想找某个方向的免费 API 时来这里按分类翻；想参考"**一份巨型清单怎么用 CI 守住格式与链接**"时，`scripts/validate/` 值得读。需要注意：**README 顶部是大篇幅赞助方 APILayer 的商业推广**，且清单指向的每个 API 各有各的条款与随时会变的免费额度。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README（2,923 行）开头与结构、`CONTRIBUTING.md`、`LICENSE`、`scripts/README.md`、完整文件树与元数据；**未 clone、未运行任何校验脚本、未逐条访问清单中的 API、未统计条目总数**）
- 适用范围：开发前期"找个免费数据源"的场景；想借鉴"清单类仓库的格式约束 + 链接 CI"的做法时

## 核心结论

**它是一份"单文件为主"的策展清单，仓库本体只有 23 个文件。** 真正的内容集中在 `README.md`（2,923 行），其余是校验脚本、测试、模板与 3 个工作流。也就是说：**"几十万 Star"给的是一份文档，不是一个软件。**

**它现在有两套条目格式**（这是近期的一个明显变化）：

| 板块 | 列 | 说明 |
| --- | --- | --- |
| **REST API**（主体） | `API \| Description \| Auth \| HTTPS \| CORS \| Call this API` | 正文按分类排列；`Call this API` 给 Postman 的 Run 按钮 |
| **MCP Servers**（新增，且与 REST 分开） | `Name \| Description \| Auth \| Transport \| Install` | 因为 MCP server 是**装进客户端**而不是从你的代码里发 HTTP，所以 `HTTPS` / `CORS` 这两列对它不适用，改为记录 `Transport` 与"在哪些市场有上架" |

README 里还有一个显眼的 **`## APILayer APIs`** 表格（列出赞助方的 11 个产品，每行配 Postman 按钮），排在 MCP 板块之前。

**收录规则写得很硬**（`CONTRIBUTING.md`）：这是社区工具、**不是营销位**——专门用来推广付费 API 的 PR 会被拒；"API 免费但必须先买设备"的也拒（原文例子：控制智能插座的 API）。`Auth` 与 `CORS` 等字段**只接受枚举值白名单**（如 `Auth` 仅允许 `OAuth` / `apiKey` / `X-Mashape-Key` / `No` / `User-Agent`），并要求**每个 PR 只加一个链接**、按字母序插入、描述不超过 100 字符。

**配套校验脚本只做两端**（`scripts/validate/`）：`format.py` 校验 README 表格格式，`links.py` 校验链接——其中 `-odlc`（`--only_duplicate_links_checker`）可以**只查重复链接而不做耗时的全量可达性检查**。另有 `scripts/tests/` 下的 `test_validate_format.py` 与 `test_validate_links.py`，用 `python -m unittest discover tests/ --verbose` 跑。三个工作流（`.github/workflows/`）分别是：`test_of_validate_package.yml`（跑校验包测试）、`test_of_push_and_pull.yml`、`validate_links.yml`（链接校验）。**PR 会触发一次全量链接检查，通过才可能被合。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [public-apis/public-apis](https://github.com/public-apis/public-apis) |
| 描述 | A collective list of free APIs |
| 官网字段 | `https://APILayer.com/?utm_source=Github&utm_medium=Referral&utm_campaign=Public-apis-repo`（**指向赞助方并带 UTM 参数**） |
| 归属 | 组织 `public-apis`（页面 `isOrg: true`） |
| 许可证 | **MIT**（`LICENSE` 1,068 字符，`Copyright (c) 2022 public-apis`）——**只覆盖清单本身** |
| 主语言 | Python 96.8%（占比来自校验脚本，**仓库主体其实是 Markdown**） |
| 关注度 | **498,556 Star / 55,816 Fork / 4,758 关注**（页面值，2026-11-13；ungh.cc 为 498,545 / 55,814 / 4,758，**Star 差 11、Fork 差 2，基本一致**） |
| 开放 Issue | 67（shields.io，2026-11-13） |
| 贡献者 | 415（shields.io，2026-11-13） |
| 维护状态 | **2016-03-20 创建（近 10 年）**；ungh 报 pushed **2026-11-11**；页面 `updatedAt` 2026-11-13 |
| 默认分支 | `master` |
| 版本 | **0 Release**（页面 `sections.releases: false`），不按版本发布 |
| 话题标签 | 14 个：api / apis / dataset / development / free / list / lists / open-source / public / public-api / public-apis / resources / software |
| 仓库规模 | 文件树 **23 个文件**；`README.md` **2,923 行** |
| 文档 / 社区 | 无独立站点（官网字段即赞助方链接）；README 给出 Discord 邀请链接 |

## 结构：内容在 README 里，脚本只管守规矩

文件树只有 23 项，去掉模板与资源后可归为三类：

```text
README.md                    ← 真正的"数据"：按分类排列的 API 表格 + MCP Servers 板块 + APILayer 板块
CONTRIBUTING.md              ← 收录与格式规范（含 Auth / CORS / Transport 的枚举白名单）
LICENSE                      ← MIT
.github/ISSUE_TEMPLATE.md
.github/PULL_REQUEST_TEMPLATE.md
.github/workflows/           ← test_of_validate_package.yml · test_of_push_and_pull.yml · validate_links.yml
scripts/README.md            ← 校验脚本怎么跑
scripts/requirements.txt
scripts/github_pull_request.sh
scripts/validate/format.py   ← 校验 README 条目格式
scripts/validate/links.py    ← 校验链接（-odlc 只查重复）
scripts/tests/               ← test_validate_format.py · test_validate_links.py
```

跑校验的方式（在仓库根目录）：

```bash
python -m pip install -r scripts/requirements.txt
python scripts/validate/format.py README.md      # 格式
python scripts/validate/links.py README.md       # 链接（较慢）
python scripts/validate/links.py README.md -odlc # 只查重复链接
```

## 使用前需要注意

- **别把"社区策展"当成"中立推荐"**：README 顶部**整段是赞助方 APILayer 的统一套件推广**（含 banner、"APILayer APIs" 产品表与 Postman 按钮），仓库的官网字段也直接指到 `apilayer.com`（带 UTM 参数）。`CONTRIBUTING.md` 明确说"**这个清单不是营销工具**"、营销性 PR 会被拒——**两者并存，读的时候要自己分层**：分类表格是社区内容，顶部与 APILayer 板块是赞助方内容。
- **MIT 只覆盖清单本身**：清单里指向的每个 API 有各自的服务条款、隐私政策与用量限制；**"在清单里"不等于"可以随意使用"**，尤其涉及个人数据、金融与地图的条目。
- **"链接有效"≠"API 免费可用"**：CI 只检查链接活着与格式正确，**免费额度、是否需要信用卡、是否已停服都随时间变化**，用前必须到对方的官方文档核实当前条款。
- **清单很长但也很杂**：`README.md` 近 3,000 行、条目按分类堆在一个文件里，**没有机器可读的数据文件（JSON / YAML）**——想程序化消费只能自己解析 Markdown 表格，或去看社区维护的镜像（本笔记未核验任何镜像的准确性）。
- **MCP 板块的定位**：它把 MCP server 与 REST API 分开列，`Install` 列只链 **Cursor / Anthropic / Glama** 三个市场，并要求"只链真实存在的上架页，404 的 PR 会被关"。这个板块比 REST 板块新，**条目数也少得多**（本笔记未逐条统计）。
- **默认分支是 `master` 不是 `main`**：写脚本、拉 raw 文件时注意（raw 链接用 `master`）。
- **我只读了文档与文件树，没有跑脚本、没有点开任何一条 API**：条目的真实性、链接的当前有效性、各 API 的免费层情况、以及校验脚本的实际行为**均未独立验证**。

## 什么时候会再看

- 做新产品 / 小工具时想**先找有没有现成的免费数据源**，按分类快速扫一遍（天气、金融、地理、社交、开发工具等）。
- 想参考**"巨型 Markdown 清单如何用 CI 守住格式与链接"**时：`scripts/validate/format.py` + `links.py` + 三个 workflow 是一套小而完整的样本，`-odlc` 那种"把耗时检查拆成可选"的做法尤其值得抄。
- 想给某个清单类项目写**字段枚举白名单**（`Auth` / `CORS` / `Transport` 只允许固定取值）时，`CONTRIBUTING.md` 是一份现成的约束范式。
- 想观察一个**近 10 年、近 50 万 Star 的策展仓库现在长什么样**（赞助方推广与社区内容如何共处）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"清单仓库"，维护方式不同**：[free-voice-clone：本地免费语音克隆与音乐生成模型清单](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)——那篇是**本地语音模型清单**（**无 CI、无校验、静态快照**）；本篇有 **`format.py` / `links.py` + GitHub Actions** 守格式与链接。可对照"**清单仓库有没有自动化维护**"对长期可用性的影响。
- **同属"围绕 MCP 的清单 / 入口"，但目的不同**：[servers：MCP 官方参考服务器合集](servers-MCP官方参考服务器合集.md)——那一份是**官方参考实现的少量样本**（现约 7 个），用来照着学怎么写 MCP；本篇的 MCP 板块是**跨厂商的目录**（记录 transport 与上架位置），用来海选现成的装。文风与用途都可对照。
- **同属"聚合清单 + Actions 校验流水线"**：[everything-ai-coding：聚合编程 AI 扩展资源的索引与一键安装库](everything-ai-coding-聚合编程AI扩展资源的索引与一键安装库.md)——那篇是**多上游采集 + LLM 评分 + 一键安装**的重型流水线（还带安全提示组件）；本篇是**人工策展 + 只校验格式与链接**的轻量做法。两者对照"自动聚合"与"人工把关"两条路线，以及"校验到什么程度"。
- **同属"策展型清单"**：[the-book-of-secret-knowledge：信息安全与运维清单集](the-book-of-secret-knowledge-信息安全与运维清单集.md)——那篇把"策展型仓库怎么写"（统一条目格式、收录原则、失效链接的保守处理）当成可复用的写法样本，可与本篇的字段枚举白名单 + CI 校验对照。
- **同属 awesome 清单，但许可与商业色彩不同**：[awesome-deepseek-integration：DeepSeek 官方实用集成清单](awesome-deepseek-integration-DeepSeek官方实用集成清单.md)——那篇由厂商官方维护、许可是 **CC0-1.0**、且已停更；本篇由社区组织维护、**MIT**、**顶部带赞助方推广**。可对照"官方清单"与"社区清单"在治理、许可与商业内容上的差别。
- **它就是本篇的展示层：把这份清单搬上网的 React 静态站**：[Free-APIs.github.io：把免费 API 清单做成一个 React 站点](Free-APIs.github.io-把免费API清单做成React站点.md)——那一篇是**网页版外壳**（README 自述 "derived from Public APIs"），而且**仓库里不放数据**、页面打开时**运行时从第三方服务 `api.publicapis.org` 拉**（`/entries`、`/categories`）。两篇对照可看"**原始清单**"与"**展示层**"的分离，以及"**数据到底放哪**"的三种选择（编进仓库 / 跑 CI 校验 / 运行时现拉）。

## 参考来源

- 仓库主页与 README（2,923 行，核验日期 2026-11-13）：https://github.com/public-apis/public-apis
- 收录与格式规范：`CONTRIBUTING.md`（含 `Auth` / `CORS` / `Transport` 枚举白名单、MCP 条目格式、PR 规则）
- 许可证：`LICENSE`（MIT，1,068 字符，`Copyright (c) 2022 public-apis`）
- 校验脚本说明：`scripts/README.md`；脚本与测试：`scripts/validate/format.py`、`scripts/validate/links.py`、`scripts/tests/*`
- 工作流：`.github/workflows/test_of_validate_package.yml`、`test_of_push_and_pull.yml`、`validate_links.yml`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日）；Issue 数与贡献者数取自 shields.io（2026-11-13）

以上信息核验于 2026-11-13，Star 数与清单内容可能变化。**未 clone、未安装依赖、未运行 `format.py` / `links.py`、未跑任何测试、未逐条访问清单中的 API、未统计条目总数**；条目格式、枚举白名单、校验范围与工作流用途均来自 README、`CONTRIBUTING.md` 与 `scripts/README.md` 自述。
