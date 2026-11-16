# qiaomu-seo：覆盖 Google、Bing 与 AI 搜索的 SEO 技能

> README 自述：这是一个**专业 SEO Agent Skill**——**"不是检查 title 字符数、H1 数量和关键词密度的打分器"**，而是按**真实搜索链路**工作：**爬虫访问与 URL 发现 → 抓取与 JavaScript 渲染 → 可索引性（indexability）→ canonical / hreflang → 页面相关性与搜索意图 → 内链与信息架构 → 搜索表现测量**。最值得记的是三点：① 它**把"证据分级"写进交付纪律**——每个结论都标明是**"直接观察、合理推断还是缺少证据"**，**不编造搜索量、关键词难度、排名、流量或竞品数据，也不承诺收录、富结果或 AI 引用**；② 它覆盖 **AI 搜索可见性**，但**分别检查** Google AI Overviews / AI Mode、ChatGPT Search、Copilot 与 Perplexity 的**基础可访问性与供应商边界**，**"不把传统 SEO、模型训练和 AI 引用混为一谈"**；③ **默认只读**——"审计 / 诊断 / 比较"不碰代码与线上数据，**"修复 / 实现"只在明确授权后执行**，并保留修改前证据、测试结果、回滚信息与监控窗口。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（135 行）、`LICENSE`（1,064 字符）与已核实元数据；**未安装、未运行任何校验脚本、未对任何站点做审计、未访问 README 中引用的数据/报告文件**）
- 适用范围：面向**支持 Agent Skills 的客户端**（README 点名 Codex、Claude Code、Cursor 等），**前置条件**为已装 **Node.js**（`node --version`）与可用的 **npm / npx**（`npx --version`），且客户端**允许读取本机 skills 目录**

## 核心结论

1. **它按"搜索链路"分层排查，而不是按"页面元素"打勾**（README）：定位"**页面不收录或收录异常**"时，会依次检查 robots、状态码、渲染、索引指令、canonical、sitemap、内链发现与 Search Console 证据，**先找出阻塞发生在哪一层**。
2. **输入 → 交付物是明确的**（README 表格）：

   | 输入 | 交付物 |
   | --- | --- |
   | 网站 URL、代码、渲染页面、sitemap 或 crawl 文件 | 按**影响、置信度、工作量和依赖**排序的技术 SEO 问题清单 |
   | Search Console、Bing Webmaster Tools、分析数据或服务器日志 | **分段后**的流量/索引诊断、候选原因、**缺失证据**与下一步验证 |
   | 产品、受众、市场和关键词数据 | 搜索意图分析、**keyword-to-page map**、内容 brief 与站点架构建议 |
   | 迁移计划或新旧 URL inventory | URL 映射、redirect / canonical / hreflang / sitemap 检查与监控方案 |
   | **明确授权修改**的网站代码 | 最小化代码修复、变更记录、测试结果、回滚信息与复查时间点 |

3. **它懂 SEO 的"边界话术"**（README "专业边界"）：**robots.txt 控制抓取访问**；**robots meta / X-Robots-Tag 控制受支持的索引与展示行为**；**canonical 是偏好信号**；**sitemap 与 IndexNow 是发现/变更通知**——**"它们都不保证收录"**。并且明确区分三层不同层级：**Schema.org 语法有效、Google 搜索功能资格、实际出现富结果**；也区分**实验室数据（Lighthouse）与现场数据（Core Web Vitals）**。

**个人判断（未验证）**：这个技能的核心竞争力不在"查什么"，而在"**怎么把不确定说清楚**"——**影响与置信度分开表达**、**缺数据时标 `unknown`**、**证据分级**。对做 SEO 的人来说，这比再多一条 checklist 更有用，因为 SEO 的多数错误正是**把推断当事实**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [joeseesun/qiaomu-seo](https://github.com/joeseesun/qiaomu-seo) |
| 描述 | GitHub 描述**末尾被截断**："Audit, diagnose, research, plan, implement, experiment on, and verify website SEO across Google, Bing, and AI-search surfaces. Use for technical SEO, …"；**完整定位以 README 为准**：**"用于 Google、Bing 与 AI Search 的专业 SEO Agent Skill：技术 SEO 审计、关键词—页面策略、自然流量诊断、站点迁移、代码修复与效果验证。"** |
| 许可证 | **MIT**（`LICENSE` 1,064 字符，Copyright (c) 向阳乔木；与 GitHub 页面 `spdxId` = MIT 一致） |
| 作者 | 向阳乔木（乔向阳 / Joe）；X [@vista8](https://x.com/vista8)；站点 <https://qiaomu.ai> · 博客 <https://blog.qiaomu.ai>；微信公众号"向阳乔木推荐看" |
| 关注度 | **页面 474 Star / 2 Watcher / 61 Fork**；ungh 镜像 **471 / 2 / 60**；shields stars 474、forks 61、**开放 Issue 0**、**contributors 1**（2026-11-13） |
| 维护状态 | **2026-08-03 创建**；**ungh `pushedAt` 2026-08-03（与创建同日，此后无代码推送）**；页面 `updatedAt` **2026-11-05**——**须区分：页面 `updated` 不代表代码更新**；默认分支 `main`；**Release 0**；**topics 为空** |
| 形态 | 一个 **Agent Skill**。README 提到 `scripts/validate_skill.py`、`scripts/validate_knowledge.py`、`scripts/validate_audit.py`、`data/seo-source-registry.json`、`reports/creation-handoff.md`、`evals/trigger_cases.json` 等路径，但**我未核对仓库文件树，这些文件是否存在、内容如何未核实** |

## 功能与用法

### 1. 它能对付哪些 SEO 问题（README）

- **页面不收录或收录异常**：查 robots、状态码、渲染、索引指令、canonical、sitemap、内链发现与 Search Console 证据，**定位阻塞在哪一层**。
- **自然搜索流量下降**：按**页面、查询、国家、设备、搜索类型和时间**切片，**区分**技术故障、需求变化、内容衰退、迁移影响与**数据口径问题**。
- **关键词没有对应落地页**：研究搜索意图与主题关系，产出 **keyword-to-page map**、内容 brief、页面新建/合并/改进建议，**避免关键词互相竞争**。
- **技术 SEO 落地**：审计并修复 canonical、redirect、sitemap、robots、metadata、JSON-LD、hreflang、分页、内链与 JavaScript 渲染问题。
- **网站改版或迁移**：建 URL inventory、重定向映射、canonical/hreflang/sitemap 一致性检查、上线监控与回滚边界。
- **电商、国际化与大型站点**：检查商品变体、分类/筛选页、程序化页面、多语言/多地区信号，**按模板抽样说明实际覆盖率**。
- **AI 搜索可见性**：分别检查 Google AI Overviews / AI Mode、ChatGPT Search、Copilot 与 Perplexity 的**基础可访问性和供应商边界**。

### 2. 安装与前置（README "安装" / "前置条件"）

```bash
npx skills add joeseesun/qiaomu-seo
# 确认 skill 已被发现：
npx skills add joeseesun/qiaomu-seo --list
```

- 前置：`node --version` 已装、`npx --version` 可用（随 Node.js 一同安装）、客户端支持 Agent Skills 且允许读取本机 skills 目录。
- README "你可以直接这样说" 给了示例说法，如"**抓取并渲染这个网站，找出为什么产品页没有进入 Google 索引**"、"**审计多语言电商站的商品变体、faceted navigation、分页和 hreflang**"、"**分别检查 Google AI Overviews、ChatGPT Search 和 Perplexity 的抓取与可见性基础，不承诺引用**"。

### 3. 1.2 版的专业能力升级（README "1.2 专业能力升级"）

- **登记并定期复查 33 个官方来源**（Google、Bing、IndexNow、Schema.org、web.dev、OpenAI、Microsoft、Perplexity）；
- 对平台规则**记录来源、复查日期与功能生命周期**，**自动拦截已知陈旧断言**；
- **分离** Schema.org 语法 / 搜索功能资格 / 实际展示，也**分离** IndexNow 接收 / 抓取 / 索引结果；
- 增加国际 SEO、电商、图片/视频搜索、程序化 SEO、内容治理、性能测量与 SEO 实验模块；
- **区分 OAI-SearchBot、GPTBot、ChatGPT-User 与其他 AI crawler 的用途和控制边界**。

### 4. 验证命令（README "验证"）

```bash
python3 scripts/validate_skill.py .
python3 scripts/validate_knowledge.py . --strict-stale
python3 scripts/validate_audit.py path/to/audit.json
# 另两条走外部 meta-skill：
python3 ~/.agents/skills/qiaomu-meta-skill/scripts/export_skill_ir.py . --output reports/skill-ir.json
python3 ~/.agents/skills/qiaomu-meta-skill/scripts/trigger_eval.py . --cases evals/trigger_cases.json --output reports/trigger-eval.json
```

## 使用前需要注意

- **差不多三个月没推送过代码**：创建与最后一次 `pushedAt` **同为 2026-08-03**，而页面 `updatedAt` 是 2026-11-05。**"页面显示更新"可能是元数据/描述或 README 的被动刷新，不等于功能迭代**——引用"活跃度"时务必区分这两者。**Release 0、开放 Issue 0、贡献者 1**，属**个人项目**。
- **它自己声明"不承诺效果"**：**不编造搜索量、关键词难度、排名、流量或竞品数据，不承诺排名、收录、富结果或 AI 引用**。若你想要的是"保证某某词上首页"，**这个技能不提供，且刻意不做**。
- **修复类操作需要你显式授权**：**审计 / 诊断 / 比较默认只读**；**修复 / 实现只在明确授权后执行**，并保留修改前证据、测试结果、回滚信息与监控窗口。**想只审计不改站，就用"审计 / 诊断 / 比较"的措辞。**
- **它依赖外部数据才有"证据"**：没有 Search Console 或关键词工具时，README 明说**只做定性研究，并把搜索量、KD、覆盖率标为 `unknown`**。**它的结论质量上限，取决于你给的数据。**
- **"33 个官方来源 + 自动拦截陈旧断言"是 README 自述**：README 指向 `data/seo-source-registry.json` 与 `validate_knowledge.py`，但**我未核对文件树、未运行脚本**，**该机制是否真实存在未验证**。
- **验证脚本有外部依赖**：后两条验证命令走 `~/.agents/skills/qiaomu-meta-skill/` 下的脚本，**需要你另外装那个 meta-skill**；且全部验证命令是 **Python 3** 脚本（`python3`），但 README 的前置条件**只列了 Node.js**——**未列 Python**，属**文档遗漏/不一致**。
- **安装命令写法与同批另一技能不同**：本 README 用 `npx skills add`（**无** `@latest`），而 `ip-as-logo-skill` 用 `npx skills@latest add`；**两者均为各自 README 自述，我未实际执行对比**。
- **元数据口径**：页面 Star **474** 与 ungh 镜像 **471** 有约 0.6% 差异，**引用时注明来源与日期**。
- **我只读了 README 与 `LICENSE`**：**未安装、未运行任何校验脚本、未对任何站点做审计、未访问 README 引用的 `reports/`、`data/`、`evals/` 文件**；仓库是否真含这些文件、脚本能否跑通，**均未验证**。

## 什么时候会再看

- 需要做**技术 SEO 审计 / 自然流量下降诊断 / 站点迁移**，并希望助手**明确区分观察、推断与证据缺口**时。
- 想参考"**把 AI 搜索（AI Overviews / ChatGPT Search / Copilot / Perplexity）与传统 SEO 分开处理**"的写法时。
- 想参考"**默认只读、改代码前留证据与回滚点**"这种**授权式**助手行为设计时。
- 想参考"**能力/规则带复查日期 + 自动拦截陈旧断言**"这类知识保鲜机制时（README 自述，未验证）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"把网站变成可分析对象"**：[firecrawl：把网站转成 LLM 可用数据的抓取 API](firecrawl-把网站转成LLM可用数据的抓取API.md)——那篇提供**抓取/渲染的底层能力**，本篇在其之上做**索引性、相关性与可见性判断**。可对照"**抓取工具**"与"**SEO 判断层**"。
- **同属"给 Agent 用的研究/检索能力"**：[argo：给 Agent 用的搜索与深度研究工具](argo-给Agent用的搜索与深度研究工具.md)——那篇做**通用搜索与深度研究**，本篇把检索聚焦到**搜索链路与 SEO 证据**上。可对照"**泛研究**"与"**垂直诊断**"。
- **同属"Agent Skill 的聚合与索引"**：[everything-ai-coding：聚合编程 AI 扩展资源的索引与一键安装库](everything-ai-coding-聚合编程AI扩展资源的索引与一键安装库.md)——那篇是**技能/扩展的索引与安装入口**，本篇是**可直接装入的一个具体技能**。可对照"**资源目录**"与"**单体技能**"。
- **同属"SEO / 搜索侧能力"，阶段不同**：[new-term-radar：赶在关键词工具收录前捕捉 AI 新名字的雷达](new-term-radar-每天抢先捕捉新AI名词的雷达.md)——那篇是**给新词做雷达与优先级表**（赶在关键词工具收录前发现值得做的新词）；本篇是**给已有页面做 SEO 的 Agent 技能**。可对照"**先发现值得做的新词**"与"**优化已有页面**"。

## 参考来源

- 仓库主页与 README（135 行；核验日期 2026-11-13）：https://github.com/joeseesun/qiaomu-seo
- 许可证：`LICENSE`（MIT，1,064 字符，Copyright (c) 向阳乔木；本地副本 `C:\Users\Administrator\AppData\Local\Temp\rgw2\joeseesun__qiaomu-seo.LICENSE`）
- 本地 README 副本：`C:\Users\Administrator\AppData\Local\Temp\rgw2\joeseesun__qiaomu-seo.README.md`
- README 内引用但**未核实**的文件：`reports/creation-handoff.md`、`data/seo-source-registry.json`、`scripts/validate_skill.py`、`scripts/validate_knowledge.py`、`scripts/validate_audit.py`、`evals/trigger_cases.json`，以及外部 `~/.agents/skills/qiaomu-meta-skill/scripts/` 下的两个脚本
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release / 开放 Issue / 贡献者）：GitHub 页面内嵌数据 + ungh.cc + shields.io（2026-11-13）

以上信息核验于 2026-11-13。**未安装、未运行任何校验脚本、未对任何站点做审计、未访问 README 引用的 `reports/`、`data/`、`evals/` 文件**；能力与用法描述均来自 README，**未逐一验证**。
