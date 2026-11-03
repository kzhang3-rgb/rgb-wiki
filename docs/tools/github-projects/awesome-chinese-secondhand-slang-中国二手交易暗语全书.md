# awesome-chinese-secondhand-slang：中国二手交易暗语全书

> 记录这个仓库到底是什么、能查到什么，以及它的几处口径不一致。它把闲鱼 / 转转 / 小红书上流通的交易黑话整理成结构化词条，同时给出卖家潜台词与买家避坑解读。第一句先记清三件事：**MIT 许可**；**仓库极新（2026-10-17 创建，核验时约 17 天）且已约两周没有新提交**；**首页指向一个商业站点，最新提交是为该站点做 SEO**。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README 全文、`data/slang-dictionary.json` 结构、提交记录、目录树、Releases 与 GitHub API 元数据；未逐一核实任何词条的真实性，未访问其商业站点）
- 适用范围：面向中国大陆的二手交易场景（README 主列闲鱼、转转、小红书）；仓库本身只有文档与一个 JSON 数据文件，在线阅读即可

## 核心结论

这是一个"行话词典"型仓库，不是软件项目。它做的事是把中文二手平台上流通的**黑话 / 暗语 / 缩写**整理成一份可查询的资料：README 里是分类速查表和拼音缩写表，机器可读的完整数据放在 `data/slang-dictionary.json`。

它和一般的"黑话列表"不同的地方在于**每条都做了双向解读**，并且带状态字段：

- **卖家潜台词**（`sellerIntent`）：这个词背后卖家真正想说什么；
- **买家避坑建议**（`explanation`）；
- **存活状态**：README 用 🟢 现行可用 / 🟡 抽检限流 / 🔴 平台拦截 三档标注——也就是说，它记录的不只是词义，还有**这个词在当前平台风控下还能不能用**；
- **替代暗语**（`successorSlang`）与**趋势**（`activeTrend`）：某个词被封后换成了什么。

换句话说，它的隐含前提是"这些暗语的存在意义是绕过平台审核"。这一点决定了它的使用边界，值得先想清楚再决定要不要参考。

第二件要先看清的是**口径不一致**。这个仓库的自述数字与实际文件对不上，而且不止一处：

| 指标 | README 各处说法 | 实际（核验时实测） |
| --- | --- | --- |
| 变体总数 | badge 标签 `5,082,569+`；badge alt 与正文 `4,569+`；仓库描述 `4,476+` | **5,082**（JSON 内 `slangVariants` 求和） |
| 品类数 | badge 标签 `149 核心品类`；badge alt `110 Topics`；正文 `114 大核心品类` | **27 个 category**；**149** 是词条（entries）数 |
| JSON 结构 | 声称含 `stats` / `categories` / `abbreviations` / `entries` 四个键 | **顶层是一个扁平数组**（149 个词条对象），无上述包装键 |

其中"149"这个数字在两个指标里各出现一次（一处指品类、一处实际是词条数），说明这几个数字大概率是标签与正文各改各的、没有对齐。**遇到这类自述数字，一律以实际文件为准。**

第三件是**来源与推广**。仓库主页填的是 `https://anyu.luckhub.cc`（README 自称"官方线上中枢"），最新一条提交的说明就是 `docs: optimize SEO anchor text and exact match keywords for anyu.luckhub.cc`——即最近一次改动是为了给这个商业站点做 SEO。仓库 `updated_at` 是 2026-11-03，但默认分支 `main` 的最新提交停在 **2026-10-19**，此后的时间戳刷新来自 star 等边缘活动，**不代表内容还在更新**。同时它 **660 Star 但订阅者（watchers）只有 1**，且贡献者单一，规模感需要打折看。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Domainsun/awesome-chinese-secondhand-slang](https://github.com/Domainsun/awesome-chinese-secondhand-slang) |
| 描述 | "🐟 中国二手交易暗语全书与避坑合规指南 (闲鱼 / 转转 / 小红书 4,476+ 条实战暗号变体、卖家潜台词透视与买家验货防割指南)" |
| 许可证 | **MIT**（GitHub API `license.key` = `mit`；仓库内 `LICENSE` 1,102 B） |
| 主语言 | 无（`language: null`）——仓库只有文档与一个 JSON 数据文件，没有程序代码 |
| 内容规模 | README 9,549 B；`data/slang-dictionary.json` **303,619 B**；实测 **149 个词条 / 5,082 条变体 / 27 个分类** |
| 关注度 | **660 Star、41 Fork、1 关注、0 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2026-10-17 创建；`pushed_at` **2026-10-19**；默认分支 `main` 最新提交 `84970837`「docs: optimize SEO anchor text and exact match keywords for anyu.luckhub.cc」为 **2026-10-19**（作者 domainsun，**未签名**），次新三条均在 2026-10-18 → **约两周无新提交**；`updated_at` 2026-11-03 系边缘活动刷新 |
| 体积 | 约 217 KB（API `size` 217 KB） |
| 主页 | **`https://anyu.luckhub.cc`**（README 称其为"官方线上中枢"） |
| 话题标签 | `antifraud`、`awesome`、`awesome-list`、`chinese-slang`、`ecommerce`、`goofish`、`risk-control`、`secondhand`、`xianyu` |
| Releases | **无任何 Release**（`releases` 接口返回空数组） |
| 仓库开关 | 开启了 Projects 与 Wiki；`has_downloads`、`has_pages`、`has_discussions` 均为 false |
| 目录结构 | `.gitignore`(29 B)、`CONTRIBUTING.md`(1,664 B)、`LICENSE`(1,102 B)、`README.md`(9,549 B)、`data/`（仅 `slang-dictionary.json`） |

> 关于关注度：`watchers_count` 与 `stargazers_count` 恒等是 GitHub 的历史遗留，真正的关注者是 `subscribers_count`。此处 660 Star 对应 **1 个订阅者**，说明"收藏"很多但真正持续跟踪的人极少——**热心收藏者多、长期关注者少**，对一个靠持续跟进平台风控的专业资料型仓库来说不太乐观。

## 它能做什么

**README 部分（人读）**

- 开头即写"收录……4,569+ 条实战暗号变体 · 114 大核心品类主题"，并按五大类列速查表：
  - AI 账号与数码极客
  - 手机成色与配件排雷
  - 潮牌穿搭与轻奢古着
  - 二次元潮玩与谷圈周边
  - 交易套路与买卖行话
- 速查表每条含：**暗语 / 真实含义 / 存活状态（🟢 现行可用、🟡 抽检限流、🔴 平台拦截）/ 卖家潜台词 / 买家避坑建议**。
- 另有一张**拼音首字母缩写 A–Z 表**（A-Z 逐字母的缩写对照）。
- 末尾有免责声明两条，明确反对**制假售假、电信诈骗、违规违禁品交易**。

**JSON 部分（机器读）**

- 入口：`data/slang-dictionary.json`（README 里给了一段 JSON API 说明，但结构描述与实际不符，见上文）。
- 实测顶层是**扁平数组**，每个元素是一条词条，字段包括：

```json
{
  "id": "ai-services-cld-sub",
  "standardName": "Claude 官方会员订阅与代阅 (Pro / Team / API)",
  "category": "ai-services",
  "slangVariants": ["...41 条变体..."],
  "platforms": ["闲鱼", "转转", "小红书"],
  "riskLevel": "danger",
  "liveness": "killed",
  "successorSlang": ["..."],
  "activeTrend": "...",
  "explanation": "...",
  "sellerIntent": "..."
}
```

其中 `riskLevel`（如 `danger`）、`liveness`（如 `killed`）是这套数据的价值所在——**它记录的不只是"这个词什么意思"，还有"这个词现在还能不能用、风险多大"**。

**实测的 27 个分类**（`category` 字段取值）：

```text
academic-services, ai-services, anime-fandom, anime-goods, beauty-cosmetics,
camera-optics, condition-slang, digital-assets, digital-hardware, digital-phone,
evasion-contact, game-accounts, general-slang, hardware-mods, luxury-fashion,
network-tools, pc-hardware, pets-plants, shoes-sneakers, social-traffic,
streetwear-luxury, ticket-voucher, trading-terms, traffic-growth,
virtual-resources, watches-jewelry, xiaohongshu-matrix
```

按分类名判断，覆盖面从二手数码、潮牌球鞋、谷圈周边，延伸到 AI 账号、虚拟资源、网络工具、流量增长等**明显偏向"灰色/规避类"的方向**（如 `evasion-contact`、`digital-assets`、`traffic-growth`、`xiaohongshu-matrix`）。这说明它并不是一份中性的"二手交易词典"，**有相当一部分内容在讨论如何绕开平台规则成交**。

## 使用前需要注意

**① 自述数字口径混乱，别引用。** 变体数在四处给了四个值（`5,082,569+` / `4,569+` / `4,476+` / 实际 5,082），品类数在三处给了三个值（`149` / `110` / `114` / 实际 27）。**要引用规模，就用自己数出来的数**；README 上的数字不能直接转述。

**② 仓库极新且已停更约两周，规模感要打折。** 2026-10-17 创建（核验时约 17 天）、`pushed_at` 2026-10-19、**无任何 Release**、贡献者单一。660 Star 但订阅者只有 1。这类资料的价值高度依赖"持续跟进平台风控"，一旦停更，`liveness` 字段很快会失真——**核验日期之后的存活状态不可信**。

**③ 首页指向商业站点，最近一次提交就是为它做 SEO。** `homepage` = `https://anyu.luckhub.cc`，README 称其为"官方线上中枢"，最新提交说明为 `docs: optimize SEO anchor text and exact match keywords for anyu.luckhub.cc`。也就是说，这个仓库同时承担**导流入口**的角色。我没有访问该站点，其内容、收费与数据使用方式均未验证。

**④ 内容性质：记录的是"绕过平台审核的暗语"。** `liveness` 字段的分档依据是**该词有没有被平台拦截**，README 也把"存活状态"作为核心卖点。也就是说，这份资料的服务对象是**想在平台风控下继续成交的人**。其中 `ai-services` 一类还涉及 AI 账号代充/代阅（示例条目 `Claude 官方会员订阅与代阅` 的 `liveness` 就是 `killed`）。

**⑤ 数据来源包含真实用户搜索词。** 一条提交说明写明 `feat: add shebao, codex, and animal adoption slang variants **from real user search queries**`——部分词条来自真实搜索行为数据。**这类数据是怎么采集的、是否脱敏，仓库未说明**。

**⑥ 免责声明与内容之间存在张力。** README 明确反对制假售假、电诈与违禁品交易，但同一份数据里包含 `evasion-contact`（规避联系）等分类与"平台拦截/替代暗语"字段。**声明的边界不等于内容的边界**，两者要分开看。

**⑦ 只作信息记录，不构成使用建议。** 我没有逐条核实任何词条的真实含义、也未验证其存活状态是否与当前平台一致。上文所有内容均为"仓库自述 + 文件实测"的记录，**不代表我对其中任何交易做法或规避手段的认可**。二手交易涉诈、涉仿、涉违禁品的边界由使用者自行判断，必要时以平台规则与法律为准。

## 什么时候会再看

- 想了解中文二手平台上"黑话/暗语"这类语言现象的**结构**时（分类方式、双向解读、状态字段的设计）。
- 做**平台风控或反诈**方向的研究，需要一份"规避用语样本集"时。
- 想参考"把散落的行话整理成**带状态字段的结构化 JSON**，再由它生成 README 速查表"这种数据组织方式时。
- 想复盘"README 自述数字与实际文件对不上"这个核验点时（本仓库是相当典型的一例）。

## 相关笔记

- **同属"平台风控 / 账号"主题**：[Claude-Guardian：Claude 账号防封与环境准备 Skill](Claude-Guardian-Claude账号防封与环境准备Skill.md)——那边是从**技术侧**准备"干净"的账号环境以规避风控，这边的 `ai-services` 分类记录的正是"账号类服务在二手平台上的暗语与存活状态"；可对照一个从**使用方**、一个从**交易方**看同一件事。
- **同属"灰色内容清单"的记录方式**：[jailbreaks：大模型越狱提示词集合](jailbreaks-大模型越狱提示词集合.md)——两者都是不便镜像正文、只做元数据与结构记录的清单型仓库；可对照"收录什么、不收录什么"的边界划法。
- **同为面向中国大陆的实用避坑指南**：[HowToLiveBetter：高性价比人生指南](HowToLiveBetter-高性价比人生指南.md)——同样处理"普通人容易踩的坑"，但那本逐条标注证据等级与原始出处，本仓库则无任何来源标注；可对照两种"避坑内容"在**可核实性**上的差距。
- **同属"敏感内容、只做元数据记录"的仓库**：[AvPlay](AvPlay-成人视频聚合播放客户端.md) 是成人内容聚合客户端（无许可证、只发安装包），本仓库是平台暗语数据——收录方式一致：只记存在与结构、把边界写清楚，不复制正文。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库：https://github.com/Domainsun/awesome-chinese-secondhand-slang
- 数据文件：https://github.com/Domainsun/awesome-chinese-secondhand-slang/blob/main/data/slang-dictionary.json
- 商业站点（README 自称"官方线上中枢"，未访问）：https://anyu.luckhub.cc

以上信息核验于 2026-11-03：Star / Fork / Issue 数、提交时间、文件体积与 JSON 规模均可能变化；`liveness`、`activeTrend` 这类字段随时间失真尤快。**未逐一核实任何词条的真实性与当前有效性，未访问其商业站点，未安装或运行任何东西（本仓库也不含可执行内容）。**
