# nautilus_trader：Rust 原生的多资产交易引擎

> 它想解决的是"研究用 Python 写一遍、上线再用编译语言重写一遍"这个断裂——同一份策略代码同时跑回测与实盘；但它自己也写明了实盘仍有一批仿真复现不了的行为，这一点比卖点更值得记。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、LICENSE 与 GitHub API 元数据，未安装、未编译、未跑回测、未接任何交易所）
- 适用范围：官方预编译 wheel 覆盖 Linux（x86_64 / ARM64）、macOS（ARM64）、Windows（x86_64），Python 3.13–3.14；装 wheel 不需要 Rust 工具链。接实盘需自备各交易所 / 券商的凭据与网络条件

## 核心结论

NautilusTrader 是一个有 8 年历史的开源交易引擎（2018-06 创建），核心已经用 Rust 重写，Python 退到"控制面"位置：

| 层 | 承担什么 |
| --- | --- |
| Rust 核心 | 确定性事件驱动运行时、行情与订单模型、网络（tokio）、内存分配（mimalloc） |
| Python 控制面 | 策略逻辑、配置、编排；通过 [PyO3](https://pyo3.rs) 绑定到 Rust 运行时 |

它对着的痛点很具体：**策略研究通常在 Python 里用向量化写法，而生产系统另写一套事件驱动实现**。它的主张是把这个分裂去掉——同一套架构、执行语义与时间模型同时用于回测和实盘，策略不必重写就能从研究挪到生产。也可以用纯 Rust 写整个系统。

**但真正值得记的是它主动划出的那条线**：README 在讲完"同一份策略代码"之后立刻补了一句——实盘仍会引入仿真不一定能复现的 venue、传输、时序、持久化、外部活动与对账行为，并直接给出文档链接（`docs/concepts/live.md#backtest-and-live-differences`）。**"回测和实盘一致"是所有交易引擎的营销话术，把不一致的地方写进文档的反而不多。**

其他值得记的点：

- **资产类别不绑定**：任何有 REST API 或 WebSocket 的场所都能通过适配器接进来；现有集成横跨加密 CEX/DEX、传统市场（外汇、股票、期货、期权）、体育博彩交易所与预测市场，README 表里 21 个条目（其中 `Kalshi` 标注 `building`，`CoinAPI`、`FinFeed` 标注 `beta`，其余 `stable`）。
- **回测粒度到纳秒**，支持行情逐笔 / 成交逐笔 / K 线 / 订单簿 / 自定义数据，可同时跑多场所、多品种、多策略。
- **订单能力齐**：TIF 支持 `IOC` / `FOK` / `GTC` / `GTD` / `DAY` / `AT_THE_OPEN` / `AT_THE_CLOSE`；执行指令含 `post-only`、`reduce-only`、冰山单；条件单含 `OCO` / `OUO` / `OTO`。
- **状态持久化可选 Redis**；官方定位里有"引擎快到能训 RL/ES 交易智能体"这一条，但**训练框架本身不在项目范围内**。
- 项目自称遵循 Rust 的 [Soundness Pledge](https://raphlinus.github.io/rust/2020/01/18/soundness-pledge.html)，并把"最低支持的 Rust 版本 ≈ 最新稳定版"当成既定事实写出来。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [nautechsystems/nautilus_trader](https://github.com/nautechsystems/nautilus_trader) |
| 组织 | nautechsystems（GitHub 组织账号） |
| 官网 / 文档 | <https://nautilustrader.io> ・ <https://nautilustrader.io/docs/> |
| 许可证 | **LGPL-3.0**（`spdx_id: LGPL-3.0`；LICENSE 为标准 LGPLv3 全文，未见 "or later" 声明） |
| 语言 | Rust（核心）+ Python（控制面） |
| 关注度 | **31,157 Star、4,201 Fork、229 关注、137 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2018-06-25 创建，最近推送 2026-11-02；已启用 Discussions |
| 版本 | 最新标签 **`v2.0.0rc8`**（2026-10-29，预发布）；v1 稳定线最新标签 `v1.231.0` |
| 默认分支 | **`develop`**（不是 `main` / `master`） |
| 体积 | 约 306,201 KB（≈ 299 MB，克隆前留意） |

## 版本、分支与安装（这一段最容易踩坑）

分支职责写得很清楚，但**默认分支是开发分支**，照着 GitHub 首页去用容易拿错版本：

| 分支 | 含义 |
| --- | --- |
| `master` | 最新发布版源码，README 明说推荐用于生产 |
| `nightly` | `develop` 的每日快照，14:00 UTC 合并，供提前测试 |
| `develop` | 活跃开发分支（也是仓库默认分支） |
| `develop_v1` | v1 只剩关键安全修复的维护分支 |

安装方面有两个细节值得单独记：

```bash
# 想用 v2（当前是 RC 线）：必须带 --pre
pip install -U nautilus_trader --pre

# 不加 --pre 会装到 v1 稳定版，而 v1 的 Python API 与 v2 文档不是一回事
pip install -U nautilus_trader
```

- 官方推荐用 `uv` + 原生 CPython；Conda 一类发行版"可能能用但不受官方支持"。
- **v2 仍是 release candidate**，README 明确写"不建议在控制真实资金的实盘环境使用 RC"，也提到 v2 目标是稳定 2.x 的 API，稳定之后才会引入正式的弃用流程（也就是说现在 breaking change 仍可能发生）。发布节奏目标为双周一次，但"实验性或较大特性可能延期"。
- **精度模式有两档，且不同安装方式默认值不同**：官方 Python wheel 默认 high-precision（128 位整数，最多 16 位小数）；纯 Rust crate 默认 standard-precision（64 位整数，9 位小数），要显式开 `high-precision` feature。涉及价格 / 数量比较时，这一点会实打实影响行为。

## 安全与供应链（比多数交易项目细）

README 用一整节列供应链控制，项目也确实在把它当工程问题处理。摘几类：

- **依赖来源**：Rust 依赖只从 crates.io 取，锁文件带校验和；新版本有发布"冷却期"才开始采用；Python 第三方包只装 wheel。
- **依赖审计**：`cargo-vet` 审来源，`cargo-deny` 强制一份**与 LGPL-3.0-only 兼容的许可证白名单**，`cargo-audit` / OSV Scanner / `pip-audit` 在相关 PR 与每日跑。
- **构建与发布**：Actions 固定到 commit SHA；CI runner 限制出网白名单；Python 产物带 SLSA 构建溯源；容器镜像用 Sigstore 签名并附 SPDX SBOM；PyPI / crates.io 走 OIDC 可信发布；发布用的 environment 不跑 PR 与 fork 代码；**发布 tag 不可变**。
- **代码与流程**：`CODEOWNERS` 要求关键基础设施、依赖清单与锁文件必须评审；受保护分支要求签名提交并通过 CI；`gitleaks` 与 `zizmor` 在 pre-commit 阶段查密钥与 Actions 配置。
- 漏洞上报走 GitHub Security Advisories 或 `security@nautechsystems.io`，承诺 48 小时内确认、严重漏洞 30 天内修复。

## 范围边界（README 自己划的）

这一节直接决定了"能不能拿它当完整平台用"：

- 开源项目聚焦**单机**回测与实盘，面向个人与小型团队的量化交易者。
- **明确不在范围内**：UI 仪表盘、分布式编排、内置 AI/ML 工具，以及未被列入开源范围的外部集成（数据库、云服务、监控工具）。
- 新增集成要先开 RFC issue 讨论是否合适，再提 PR。

也就是说：想要大屏、多机调度、自动调参或现成的模型训练流水线，都得自己在它之上补。

## 使用前需要注意

- **以上全部来自 README / LICENSE / 元数据，我没有实测**：适配器可用性、回测撮合的真实性、实盘对账行为、性能数字均未验证。
- **"同一份代码跑回测与实盘"是它的核心卖点，也是最该自己验的一条**：项目自己列举了实盘会引入的六类差异（venue、传输、时序、持久化、外部活动、对账），这类差异通常不体现在回测收益上。
- **别用 RC 接真钱**：README 自己这么说；且 v1 与 v2 的 Python API 不兼容，网上大量教程对应的是 v1，照抄会踩版本坑。
- **两个链接带推广参数**：CoinAPI 与 FinFeed 的仓库链接是 `...?ref=nautilus`，属项目方引流链接，不是中立推荐；其余适配器链接未带该类参数。
- **许可证是 LGPL-3.0，比 MIT / Apache 严、比 GPL 留了口子**：改动引擎本体再分发，需按 LGPL 分享修改并允许使用者重新链接（"以库的形式被调用"是 LGPL 相对 GPL 的例外）；其依赖白名单也按 "LGPL-3.0-only 兼容"来管。商用或再分发前建议自行确认条款。
- **仓库体积约 299 MB**，且是 Rust + Python 双栈，本地构建与 CI 的依赖面都不小；不想碰 Rust 工具链就只用 wheel。
- 我这次**没有核实其公司实体与商业服务**（官网指向 nautilustrader.io，README 未描述收费产品），因此不对"是否免费、是否有托管版"下判断。

## 什么时候会再看

- 需要在**同一套代码里同时做回测与实盘**、且不想自己写撮合与订单状态机时——这是它相对同类最省事的地方。
- 想参考**"确定性事件驱动 + Python 控制面"的双语言分工**怎么落地时。
- 想抄**交易类项目的供应链安全清单**时：这份 README 的 Security 一节可以直接当检查表用（锁文件校验和、依赖冷却期、许可证白名单、SLSA、Sigstore + SBOM、OIDC 发布、不可变 tag）。
- 想参考**"怎么有分寸地写自己的局限"**时：卖点之后紧跟差异清单，值得学。
- 要接**加密所、IBKR、期权 / 期货，或体育博彩与预测市场**这类非典型标的时，先来查它的适配器状态表。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为量化交易/研究平台**：[QuantDinger](QuantDinger-开源AI交易操作系统.md)（自托管 AI 交易 OS，明确会下真实订单）、[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)（投研工作台，强调查证）、[tick-stock-panel](tick-stock-panel-自托管A股量化工作台.md)、[ai_quant_trade](ai_quant_trade-AI量化交易操盘手.md)、[KHunter](KHunter-A股量化选股系统.md)——**本篇是这组里工程化程度与安全治理最深的一个，也是唯一把"实盘与回测差异"写进 README 的**。
- **入门读物对照**：[xquant-beginner](xquant-beginner-人人都是量化交易员开源书.md)——那本教"怎么把投资想法写成 spec 并验证"，本篇是"验证之后往哪跑"的引擎。
- **回测与指标实现对照**：[indicator](indicator-Go技术指标与回测框架.md)（Go 侧指标与回测；注意它 v2 起改为 AGPLv3 双许可，与本篇的 LGPL-3.0 属不同约束强度）。
- **数据层**：引擎本身不带行情数据，取数要靠外部——见 [a-stock-data](a-stock-data-A股全栈数据工具包.md)、[global-stock-data](global-stock-data-美股与全球市场数据工具包.md)（其后者的合规分级思路，与接实盘前的数据授权评估是同一件事）。
- **Agent 接入对照**：[servers](servers-MCP官方参考服务器合集.md)——本篇没有 MCP / Agent 层（AI 工具明确排除在开源范围外），可与 [QuantDinger](QuantDinger-开源AI交易操作系统.md) 的 Agent Gateway 对照。

## 参考来源

- 项目仓库与 README：https://github.com/nautechsystems/nautilus_trader
- 官方文档：https://nautilustrader.io/docs/
- v2 迁移指南（README 引用）：`MIGRATION_V2.md`
- 路线图（README 引用）：`ROADMAP.md`
- 发布线与版本号：README 中的 `master` / `nightly` / `develop` 徽章与 `version.json`
- 元数据（Star / Fork / Issue / 体积 / 时间）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。未安装、未编译、未运行，适配器可用性与实盘行为均未验证。
