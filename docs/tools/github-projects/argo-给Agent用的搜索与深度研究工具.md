# argo：给 Agent 用的搜索与深度研究工具

> 记录一个"专门为 Agent 打造的搜索 / 抓取 / 证据核验后端"：**自述 200+ 搜索源（README 展开为 284 个源、其中 240 个免密钥）、99 个业务域、18 种语言**，既是 **DSH 插件**、也是 **MCP 服务器**、也提供 **CLI 与四个子技能**。第一句先记清三件事：**它的核心不是"又一个搜索引擎"，而是"证据管线 + 证据评分"**；**许可证在 v2.9.7 由 MIT 换成 Apache-2.0**；**唯一硬依赖是 `pyyaml`**。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README 章节与标题结构、`SKILL.md`、`server.json`、`pyproject.toml`、`LICENSE` 首段、目录树、Release 与元数据；**未安装、未运行、未调用任何引擎、未接入 MCP 客户端、未审计源码**）
- 适用范围：Python ≥ 3.9（`pyproject.toml` `requires-python = ">=3.9"`）；唯一硬依赖 `pyyaml>=6.0`，可选 `curl_cffi` / `rapidfuzz`；以 `argo-search` 包名发布，含 MCP 与 DSH 插件两种接入形态

## 核心结论

**① 它把自己定位成"给 Agent 的搜索后端"，而不是给人用的搜索页。** 仓库 topics 直接写着 `ai-agent`、`agent-tools`、`metasearch`、`evidence-verification`、`mcp-server`、`dsh-plugin`。README 有一节专门对比"它和模型自带搜索"。设计目标是让 Agent 拿到的不是一坨网页，而是**带着可信度标注的证据**。

**② 真正的差异点是"证据管线 + 证据评分"，而不是源的数量。** 流程大致是：语言检测 → 按业务域路由 → 多引擎召回 → RRF 融合去重 → 证据快评。README 给出评分公式：**综合分 ≈ 0.40·selection + 0.35·absorption + 0.15·freshness + 0.10·引擎分**——即更看重"选得准、吸收得进去"，新鲜度与引擎权重只占小头。

**③ 一套能力，三种调用通道。** 同一个内核通过三条路暴露给 Agent：**原生一等工具**（宿主里直接当工具用）、**原生 web seam**（替换宿主的网页抓取）、**MCP 按需**（作为 MCP 服务器被调用）。DSH 用户按宿主能力选通道，不是三选一互斥。

**④ 许可证换过。** **v2.9.7（2026-10-19）由 MIT 换成 Apache-2.0**（Release 标题直接写"License 转 Apache-2.0"）；现行 `LICENSE` 为 Apache-2.0（© 2026 taxueseek），`pyproject.toml` 亦写 `Apache-2.0`。**若你是在更早版本上做的二次分发，需要留意上游许可变更的时间点。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [taxueseek/argo](https://github.com/taxueseek/argo) |
| 描述 | "专门为 agent 打造的开源 agent 搜索工具，具备 200+的搜索源及多语言搜索能力，覆盖中文/英文/学术/代码/购物/金融/新闻/百科。" |
| 别名 | 中文名 **Argo 阿尔戈**（README 与 `SKILL.md` 均用此名） |
| 许可证 | **Apache-2.0**（`LICENSE`，© 2026 taxueseek）；**v2.9.7 起由 MIT 变更而来** |
| 主语言 | Python（`requires-python >= 3.9`） |
| 关注度 | **226 Star、16 Fork、0 关注、2 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-07-18 创建，仍在活跃更新**：`pushed_at` 2026-11-01；`main` 最近提交含 `docs(readme)`、`fix(ci)`、`feat(search)`（`--numverify` 数值印证）、`revert(engines)` 等，节奏密集；**共 31 个 tag，最新 `v2.10.1`**；Release atom 最新 `v2.10.1`（2026-10-29） |
| 体积 | API `size` 9,071 KB（约 9 MB）；目录树 1,017 项 / **963 个 blob / 约 11.4 MB**；`config.yaml` 单文件 **约 190 KB** |
| 话题标签 | `academic-search`、`agent-tools`、`ai-agent`、`deep-research`、`dsh-ecosystem`、`dsh-plugin`、`evidence-verification`、`mcp-server`、`metasearch`、`python`、`search-engine`、`web-search` |
| 包信息 | `pyproject.toml`：`name = "argo-search"`、`version = 2.10.1`、`license = Apache-2.0`；`server.json`：MCP registry `io.github.taxueseek/argo`，npm 包 `argo-search` 版本 `2.10.1` |
| 关键文件 | `README.md`（约 68 KB）、另有 en / es / ja / ko 四语 README（各约 6–7 万字符）、`SKILL.md`（`version: 2.10.1`）、`AGENT_INSTALL_GUIDE.md`、`config.yaml`、`cordis.patch.yml` |
| 目录结构 | `argo_search`、`backends`、`bin`、`data`、`docs`、`engines`、`mcp`、`packages`、`references`、`scripts`、`sub-skills`、`tests` |

## 它能做什么

**规模（README 自述）**：**284 个搜索源**（其中 **240 个免密钥**，即无需 API Key 即可用）、**99 个业务域**、**18 种语言**；覆盖中 / 英 / 学术 / 代码 / 购物 / 金融 / 新闻 / 百科等。**这些均为作者自述，我未逐个验证。**

**四个子技能（`sub-skills/`）**

| 子技能 | 做什么 |
| --- | --- |
| `local-search` | 本机文件搜索（自述 **32 个本地引擎**） |
| `local-seek` | 本地 **BM25 + 向量**混合检索 |
| `ego-search` | **登录态**搜索（默认关闭） |
| `js-run` | 运行一段 JS 取数 / 处理 |

**接入形态**

- **MCP 服务器**：自述暴露 **19 个 MCP 工具**；
- **DSH 插件**：三种通道（原生一等工具 / 原生 web seam / MCP 按需）；
- **CLI**：`argo` 命令行，带 `--envelope`、`--fields agent` 等输出控制；
- **技能**：`SKILL.md`（frontmatter `name: argo`、`version: 2.10.1`，一串中文触发词：搜索 / 查一下 / 核实 / 抓取 / 深度研究…）。

**性能与体积（作者自述）**

- **双层缓存**（内存 + SQLite），热查询约 **10 ms**；
- 面向 Agent 的"**agent 档**"单次输出约 **3.7 KB**（控制进上下文的体积）；
- `SKILL.md` 的 Gotchas 提醒：`--envelope` 与 `--fields agent` 不要同时给；`-n` 取值超过 10 无额外收益。

## 使用前需要注意

- **规模数字都是自述。** 284 源 / 240 免密钥 / 99 域 / 19 个 MCP 工具 / 32 个本地引擎，均未经我实测；其中"免密钥源"会随时间因站点改版或封禁而失效（这类多引擎工具的通病）。
- **"证据评分"是启发式排序，不是事实核查。** 公式给出的是排序权重，**不等于"内容为真"**；应把它当"挑证据的排序器"，而不是"真伪判定"（个人判断）。
- **搜索 / 抓取有合规与条款风险。** 它替 Agent 去抓公开网页，使用时应遵守目标站点条款与当地法律；`ego-search`（登录态）默认关闭是有意为之。
- **许可已在 v2.9.7 收紧为 Apache-2.0。** 早期 MIT 版本的成果若被再分发，注意版本与许可的对应关系；Apache-2.0 要求保留声明与变更说明。
- **本项目与 DSH（DeepSeek Harness）生态绑定。** topics 含 `dsh-ecosystem` / `dsh-plugin`，`cordis.patch.yml` 即为其 DSH 接线；DSH 尚处 developer preview，宿主变化可能影响其插件形态。
- **本笔记的核验边界**：只读了 README 章节结构、`SKILL.md`、`server.json`、`pyproject.toml`、`LICENSE` 首段、目录树与元数据；**未安装、未运行、未调用任何引擎、未做任何核验质量测试**。

## 什么时候会再看

- 想给 Agent（尤其本地 / 自托管、DSH 生态）接一个**免密钥、多引擎的检索与抓取后端**时；
- 想参考"**多引擎召回 → RRF 融合 → 证据快评**"这条证据管线的实现与评分公式时；
- 想抄"**一套能力、三条接入通道（原生工具 / web seam / MCP）**"的分层设计时；
- 想对照"搜索结果的**排序可信度**该怎么度量"这一命题时。

## 相关笔记

- **同为"给 LLM / Agent 的取数入口"**：[firecrawl：把网站转成 LLM 可用数据的抓取 API](firecrawl-把网站转成LLM可用数据的抓取API.md)——那是托管型抓取 API（把任意页面转成 LLM 可用数据），本篇是本地优先的多引擎检索 + 证据管线；一个偏"取任意页面"，一个偏"多源召回再排序"。
- **同为"把站点内容变成程序可用数据"**：[bb-sites：把网站变成命令行指令的适配器集](bb-sites-把网站变成命令行指令的适配器集.md)——那边复用**已登录浏览器会话**，本篇走**免密钥多引擎**；一个借登录态，一个不借。
- **都提供 MCP 出口，给出的东西不同**：[newsnow：实时热点新闻聚合阅读器](newsnow-实时热点新闻聚合阅读器.md) 通过 MCP 给**热榜数据**，本篇通过 MCP 给**检索与核验能力**——"给数据"与"给能力"两类 MCP 服务器。
- **它接入的宿主生态**：[deepseek-harness：DeepSeek 开源 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)——argo 自带 DSH 插件（topics 含 `dsh-ecosystem` / `dsh-plugin`），可对照"给宿主加一个搜索 / 研究能力"的接线方式。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/taxueseek/argo
- 技能定义：`SKILL.md`（`name: argo`、`version: 2.10.1`）
- MCP 注册信息：`server.json`（`io.github.taxueseek/argo`）
- 许可证：`LICENSE`（Apache-2.0）；变更点见 Release **v2.9.7**（2026-10-19，标题含"License 转 Apache-2.0"）
- 配套生态：https://github.com/taxueseek/taxue-invest
- 元数据（Star / Fork / 时间 / topics）：GitHub REST API；Release 与 tag 经 `releases.atom` 与 jsDelivr 核验，2026-11-04

以上信息核验于 2026-11-04。仅阅读 README 章节、`SKILL.md`、`server.json`、`pyproject.toml`、`LICENSE` 首段、目录树与元数据；未安装、未运行、未调用任何引擎，源数量与检索质量未验证。
