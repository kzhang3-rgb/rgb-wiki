# QuantOrb：Solana memecoin 的持仓扫描与风险评分

> 一句话值得记住的取舍：它给一个 Solana 代币地址打**可解释的风险分**（把前 20 名持仓聚类成"操作者"、模拟抛售对价格的冲击、列出 owner 还能改什么），并把**评分规则的全部权重与阈值写成公开文档 + 可运行代码**让第三方复算；但**这不是一个开源工具库**——仓库用**自定义专有许可（All rights reserved）**只开放约 1/10 的代码供"阅读与核验"，且项目方**自己发行了代币**。想研究"链上持仓聚类 + 可复算的风控评分"时值得看，想"拿来二次开发"则要先把许可读清楚。

- 状态：草稿
- 最近核验：2026-11-10（阅读 README 全文、`LICENSE`、仓库文件树与元数据；**未 clone、未运行 `npm test`、未打开官网 / Telegram bot、未核验其代币与主站行为**）
- 适用范围：对 Solana / pump.fun 上的 memecoin 做持仓与风险排查的场景；技术栈 Node 18+ / Python 3.11+，**零第三方依赖**（README 徽章标注 `dependencies-none`）

## 核心结论

**它不是"开源的扫描器"，而是"一个闭源产品的公开文档 + 约 1/10 的规则代码"。** README 自述"About a tenth of QuantumOrb's code is open here"——公开的只是**决定判分的规则**与**读 pump.fun / Solana 的解析层**；**站点、API、Radar 启动流、钱包链接发现、bot 的命令与数据库、各数据源的 key 与配额**都闭源（`docs/architecture.md` 划线）。

**它的核心产出是"一次扫描，三个答案"：**

| 问题 | 扫描读什么 |
| --- | --- |
| **谁真正持有它？** | 按"真实流通量"（剔除曲线、池子、锁仓与销毁地址，避免"池子被当成巨鲸"）算前 20 名钱包；**把同属一人的钱包合并成"操作者（operators）"** |
| **他们砸盘会怎样？** | 一个**抛售情景**：若这些关联 / 新钱包 / 受转账的钱包全部砸向当前流动性，价格会跌多少 |
| **还有什么能伤到你？** | owner 权限、谁能抽走流动性、前 20 名买家、以及**谁造的币**（Guard 对签名钱包的三项检查） |

每次扫描给出 **0–100 分**与四档结论（**CLEAN / OK / RISKY / DANGER**）；有些模式"太明确、不适合用加权和"，会**强制触发红旗**；当代币**太新或太成熟**、持仓模式没有意义时，扫描会**明说并拒绝给分**。

例（README 首页示例 `$GRUMPKIN`）：`20 wallets → 13 operators, biggest holds 41.1% of float, could move price −67% if sold` → `score 12/100 · DANGER`，并附多条 `RED FLAG` / `CAP` / `SCENARIO` 明细。

**一个值得单独记的设计**：它要求**"没有来源的数字一律写 N/A，绝不写 $0"**（"a figure no source knows says N/A, never $0"）——这与本库另一篇投研工具的做法高度一致（见相关笔记）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [QuantumOrb/QuantOrb](https://github.com/QuantumOrb/QuantOrb) |
| 描述 | Memecoin holder scanner for Solana: see who really holds a token, how far the price falls if they sell, and what the owner can still change. |
| 官网 / Bot | https://quantumorb.app · Telegram [@QuantumORB_bot](https://t.me/QuantumORB_bot) |
| 归属 | 账号 `QuantumOrb`（页面 `isOrg: false`；README 署 © 2026 QuantumOrb） |
| 许可证 | **自定义专有许可（source-available，非开源）**——见下节 |
| 主语言 | JavaScript 72.5%（shields.io；另有 Python 的 `bot/`） |
| 关注度 | **86 Star / 25 Fork / 3 关注 / 0 开放 Issue**（页面值，2026-11-10；ungh.cc 镜像一致） |
| 维护状态 | **2026-10-13 创建**（很新）；ungh.cc 报 pushed **2026-11-05**；页面 `updated_at` 2026-11-10 |
| 默认分支 | `main` |
| 版本 | **无 Release / 无 tag** |
| 话题标签 | 空 |
| 运行环境 | Node 18+、Python 3.11+，**零第三方依赖** |

## 许可证：不是开源，是"可读可验证"的专有许可

这是本篇最需要记清的一点。`LICENSE`（1,119 字符）原文要点：

- 仓库内容**"published so that it can be read"**，允许你**查看、链接**，并**运行 `rules/`、`test/`、`examples/` 里的代码来核验 QuantumOrb 的判分**。
- **未经版权方书面许可，不得**：复制 / 修改 / 合并 / 发布 / 分发 / 再许可 / 出售本仓库任何部分，**或把其中的代码用在别的产品或服务里**；**不得**在其他产品或服务中使用 QuantumOrb 的**截图、名称或品牌**。
- **其余软件**（扫描器、站点、Telegram bot 及其基础设施）**不在本仓库、也不受本许可授权**。
- **免责**：按 "AS IS" 提供；**NOTHING HERE IS FINANCIAL ADVICE**。

对应地，GitHub 页面与 API 的 license 字段均为 **`NOASSERTION` / "Other"**——**这里必须查 `LICENSE` 文件**，不能按"好看的界面 + 公开仓库"默认它是开源。

## 仓库里公开了什么

```text
rules/      JavaScript  评分、操作者聚类、红旗、上限、抛售情景、Guard 三查、Shield 规则（含 cli.js 可打印判决）
chain/      JavaScript  服务端如何读 pump.fun / Solana：program id、派生地址、曲线 / 池子 / 元数据布局、交易读取器
market/     JavaScript  代币页的行情口径：哪些池子属于该币、以哪个定价、市值与 FDV 分开、N/A 而非 $0
bot/        Python      bot 每条关于代币的消息、Shield 规则、bot 自己的 pump.fun 曲线读取器
examples/               带期望判决的扫描输入（首页示例、服务端真实扫描产物、每个规则一个文件）
test/                   npm test：规则边界、示例对服务端结果、链读取器对真实地址、行情对真实数据
docs/                   架构（architecture.md）、逐条评分（scoring.md）、Guard/Shield（guard.md）
assets/                 banner 与截图
```

README 强调：每个文件都是"**服务端或 bot 自己的代码，不是重写**"，且测试跑的是**服务端自己的结果与真实链上地址**。

**自己复算分数**（README 给的三条命令）：

```bash
git clone https://github.com/QuantumOrb/QuantOrb && cd QuantOrb
npm test                                        # Node 18+，无依赖
npm run test:bot                                # Python 3.11+，仅标准库
node rules/cli.js examples/site-example.json    # 跑首页那个示例代币
```

**保持闭源的部分**（README 明列）：一次扫描要问哪些账户与交易、顺序与预算；**钱包之间链接的发现**；启动流；Radar；站点与其 API；bot 的命令 / 告警循环 / 数据库；**每个数据源的 key、限额与降级**；服务器。

## 配套能力（Radar / Shield / Guard / Bot）

- **Radar**：pump.fun 新币上线数秒内出现，保留最近 **1,000 个**（约一小时），筛选条件（市值、曲线进度、社媒、关键词）跑在全部上新上。
- **Shield**：在你看盘前先查**谁造的币**（用免费公开数据）——① **Creator history**：该 dev 钱包**每一个** pump.fun 发币记录（在其达到曲线的 10% 或 20 名买家后从链上读）；**≥10 次发币且毕业率 <10% 判为"连续发币者"**；② **Dev at launch**：dev 在发行时自购多少供应，**≥15% 判为"持有"**。
- **Guard**：在代币页与每次扫描里做三项更深检查（创作者历史、发行槽买入量、dev 当前持仓）。
- **Telegram bot `@QuantumORB_bot`**：`/alerts`（按你的筛选推新币）、`/watch`（跟踪毕业、市值变动、里程碑）、`/follow`（跟踪钱包的建仓与清仓）、`/dev`（dev 一有动作就通知 + Shield 记录）、`/token`、`/new`·`/list`；`/scan`（把完整扫描报告搬进聊天）**仍在路线图上（未完成）**。

**说明**：README 明确标注"**上线前只凭证据拦，且总是给出理由；读不到的检查会明说，绝不静默放行**"。这条"不静默降级"的表述值得记（但**未证实**，见下节）。

## 使用前需要注意

- **许可即最大风险（事实）**：**这是专有许可、不是开源**。可以"读 / 跑测试 / 核验判分"，**但不得复制、改动、分发，或把代码用于任何其他产品或服务**。想二次开发或嵌入自己的项目，**先取得书面许可**，否则属于侵权。
- **项目方自己发行代币（事实，需自行判断利益相关）**：README 公布了官网代币在 Solana / pump.fun 上的**唯一合约地址**（`EiN69j8…pump`）与 pump.fun / DexScreener / Solscan 链接，并警告"**任何其他自称 QuantumOrb 的合约都不是他们的**，请逐字符核对"。**读它的评分与结论时，应意识到项目方与代币存在利益关联。**
- **只公开约 1/10 的代码，核心不可验证**：**钱包链接的发现逻辑、扫描的账户 / 交易预算、Radar、站点与 API、bot 后端均闭源**。你能复算的是"**给定输入下的判分**"，**不能复算"它是否查全了链上数据、有没有漏报"**。
- **它是一个闭源在线服务，不是本地工具**：主站/机器人是服务，**每人每天 5 次免费扫描**（重开报告或扫描中刷新不计次）。仓库代码本身不构成可独立运行的扫描器。
- **它自己声明的边界**（README "Limits" 原意）：**只看公开链上数据，从不索要私钥 / 助记词、不碰资金**；**看到的是钱包不是人**（同一人的两个链上无关联钱包会被视为独立）；**CLEAN 不是承诺**（流动性、情绪、dev 的链下计划都在其读取范围之外）；**分数是"固定标准下的自动、主观评估"**，原始数据可能不完整。
- **明确免责**：不是投资建议，不是买卖或持有的号召。**加密资产（尤其 memecoin）风险极高。**
- **信息面薄、项目很新**：**0 开放 Issue、无 Release、无 tag**，2026-10-13 才创建；README 的截图来自**模拟链的开发构建**、bot 消息用的是**编造数据**（README 已注明，图上每个代币 / 钱包 / 数字都是假的）。
- **我未做任何验证**：未 clone、未跑 `npm test`、未打开官网或 bot、未核对其合约地址与链上行为——**以上功能与数字均来自 README 自述**。GitHub API 匿名配额用尽，未取到 `size` 与贡献者统计。

## 什么时候会再看

- 想研究"**链上持仓聚类（钱包 → 操作者）+ 抛售冲击模拟 + 可复算风控评分**"这条链路的规则设计时（`rules/` 与 `docs/scoring.md` 是公开的）。
- 想参考"**把判分规则的权重 / 阈值 / 红旗全部公开，并配可运行代码与示例让第三方复算**"这种"可验证但不开源"的发布方式时。
- 想复盘"**一个带代币的项目把 GitHub 仓库当成文档与信任背书**"时该注意什么（许可、开源边界、利益相关、闭源服务）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"交易方向、且都强调自己不是黑箱"**：[QuantDinger：开源的 AI 交易操作系统](QuantDinger-开源AI交易操作系统.md)——那篇是**自托管的交易执行栈**（策略、回测、实盘、风控都在自己手里，覆盖加密货币交易所与传统券商），本篇是**只看链上持仓与风险、不替你下单的扫描服务**；两者都写"不是黑箱信号服务"/"不是黑箱"，但**一个是全开源可自托管、一个是专有许可的闭源服务**，正好可对照"**公开程度**"这条线。
- **几乎相同的"数据口径"设计**：[Vibe-Research：A 股 / 美股 / 港股的本地投研工作台](Vibe-Research-A股美股港股投研工作台.md)——那篇要求"报告中找不到出处的数字会被替换成占位符"，本篇要求"**没有来源的数字写 N/A，绝不写 $0**"。**两处对"数据缺口不得伪装成 0"的处理几乎一模一样**，可对照这种"如实暴露缺口"的做法在不同投研工具里的落地。

## 参考来源

- 项目仓库与 README：https://github.com/QuantumOrb/QuantOrb
- 许可证：https://github.com/QuantumOrb/QuantOrb/blob/main/LICENSE（自定义专有许可，© 2026 QuantumOrb）
- 评分规则与架构：`docs/scoring.md`、`docs/guard.md`、`docs/architecture.md`
- 官网 / 规则说明：https://quantumorb.app · https://quantumorb.app/#how

以上信息核验于 2026-11-10，Star 数与仓库内容可能变化。**未 clone、未运行、未打开官网或 bot、未核验代币合约**；功能与数字均为 README 自述；GitHub API 匿名配额用尽，未取到 `size` 与贡献者统计；**不是投资建议**。
