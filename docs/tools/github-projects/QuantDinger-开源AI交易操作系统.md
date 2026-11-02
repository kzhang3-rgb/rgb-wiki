# QuantDinger：开源的 AI 交易操作系统（AI Trading OS）

> 值得记的有两点：① **架构纪律**——v5 明确把"HTTP API 不再持有长跑的交易/调度循环"当成设计分界线，交易、调度、异步任务、迁移各自独立进程；② **它会动真钱**——README 第一段就警告"在显式开启实盘时可提交**真实订单**"，这不是模拟玩具。

- 状态：草稿
- 最近核验：2026-11-02（阅读仓库 README、LICENSE 与 GitHub API 元数据，**未部署、未跑回测、未接入任何交易所或券商**）
- 适用范围：自托管（Docker Compose）；Python 3.12 + PostgreSQL 18 + Redis 8；覆盖加密货币交易所与传统券商

## 核心结论

一个**自托管的"AI 交易操作系统"**，把整条链路装进同一套栈：**AI 研究 → 策略代码 → 回测 → 模拟/实盘执行 → 监控**。

定位是"面向独立交易者、Python 策略作者和小团队"，卖点是 **local-first**：行情数据、策略代码、**券商凭据**与部署都在使用者自己手里。官方强调"**它不是黑箱信号服务**"——策略代码、风控设置、凭据、部署都由操作者掌控。

交易面很宽：加密侧支持 Binance / OKX / Bitget / Bybit / Gate / HTX / Coinbase Exchange / Kraken 及适配器扩展，传统券商走 **IBKR 与 Alpaca**。AI 侧可选 OpenRouter、OpenAI 兼容接口、Google、DeepSeek、Grok、MiniMax 或自定义端点。对外还提供 **Human API、Agent Gateway（`/api/agent/v1`）与 MCP server**（官方点名可用于 Cursor、Claude Code、Codex）。

**它同时是一家公司的商业产品**：README 写明 "QuantDinger is a product of Open Byte Inc."，官网 `quantdinger.com`，另有托管版 Live App `ai.quantdinger.com`。**开源自托管与商业云并存**，评估时要注意这条边界。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [OpenByteInc/QuantDinger](https://github.com/OpenByteInc/QuantDinger) |
| 组织 | [OpenByteInc](https://github.com/OpenByteInc)（**Organization**，公司产品 Open Byte Inc.） |
| 官网 / 托管版 | <https://www.quantdinger.com> / <https://ai.quantdinger.com> |
| 许可证 | **Apache-2.0**（`LICENSE` 为标准 Apache License 2.0 全文，未见附加条款） |
| 语言与技术栈 | Python 3.12；Flask + Gunicorn、PostgreSQL 18、**两个独立 Redis**（缓存 / 持久化任务队列）、Celery + beat、Docker Compose、Nginx 前端 |
| 版本 | v5（`VERSION` 文件声明源码版本；Git tag 形如 `v5.0.1`） |
| 关注度 | **10,776 Star、2,276 Fork、69 人关注、54 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2025-12-28 创建，最近推送 **2026-08-17**（距离核验日约 2.5 个月） |
| 体积 | 约 64,567 KB（`size` 字段，含架构图与截图） |
| 其他 | 开启了 GitHub Discussions；无 Wiki、无 GitHub Pages |

> 口径说明：开放 Issue 的 API 计数**包含开放中的 PR**，54 不代表纯问题数。Star 数与仓库内容会随时间变化。

## 架构：v5 的"进程边界"是这份 README 里最值得记的部分

v5 的重构重点不是加功能，而是**划清运行时与运维边界**：

- **HTTP API 不再持有长跑的交易或调度循环**；
- 交易、调度、Celery 任务、数据库迁移**各自独立进程**；
- Celery 只做"有限的、可重试的"工作，**长生命周期策略运行时留在 trading worker**；
- **缓存 Redis 与持久化任务 Redis 分开**，各用各的淘汰策略；
- 高风险 API 契约写进 OpenAPI 并用测试保护；
- 生产 overlay 里后端以**非 root 用户 + 只读根文件系统 + 去掉 capabilities + 资源限制**运行。

一个后端镜像被多个容器复用，只是命令不同：

| 进程 | 职责 |
| --- | --- |
| `migration` | 应用数据库 schema，完成后退出，再启动其它服务 |
| `backend` | HTTP、认证、校验、**提交持久化命令** |
| `trading-worker` | 策略运行时、待处理订单、券商会话、对账 |
| `scheduler-worker` | 组合、部署、支付、信号等定时任务 |
| `celery-worker` | 有限的 AI、回测、实验、报告、维护任务 |
| `celery-beat` | 派发周期性 Celery 任务 |

**"长跑的东西不放在 HTTP 进程里""缓存与持久化队列彻底分开"这两条，是自托管交易系统最容易做错的地方**，这里用显式的进程与 Redis 分离来兜住。

## 安全模型（README 自述）

- 券商凭据与 MFA secret 用稳定的 `CREDENTIAL_ENCRYPTION_KEY` **加密存储**；
- **Agent token 是哈希存储 + 限定 scope + 限流 + 审计日志**；
- **Agent 交易默认仅模拟盘**，实盘需同时满足四个条件（见下）；
- 长跑策略的所有权用 **lease + 心跳 + fencing token**（防止两个 worker 同时操盘）；
- 生产容器不以 root 运行、不带 Linux capabilities；
- 宿主端口**默认只绑回环**，公网访问应终止在 TLS 反向代理。

**Agent 走实盘的四个前置条件**（README 原文要求全部满足）：

1. 该 token 带 trading scope；
2. 该 token 上 `paper_only=false`；
3. 服务端 `AGENT_LIVE_TRADING_ENABLED=true`；
4. 运维配置了限额与白名单。

这组"多重闸门"设计本身值得借鉴——**但反过来说明"让 AI 代理下单"是一条真实存在、可打通的路径**。

## 本地端点（默认全部绑回环）

| 服务 | 默认地址 | 用途 |
| --- | --- | --- |
| Web | <http://127.0.0.1:8888> | 桌面 Web 客户端与同源 API 代理 |
| Mobile H5 | <http://127.0.0.1:8889> | 移动端 Web |
| Backend | <http://127.0.0.1:5000> | 直接 API 与健康检查 |
| Grafana / Prometheus / Alertmanager | 3000 / 9090 / 9093 | 仅启用 observability overlay 时存在 |

## 使用前需要注意

- **它会下真实订单**：README 开篇即警告"当实盘交易被显式启用时，QuantDinger 可以提交**真实订单**"，并建议"**从模拟盘开始、使用受限 API Key、自查所在司法辖区的风险与合规要求**"。**这是会给资金带来真实后果的工具**，不要当普通自托管项目试跑。
- **默认管理员账号是历史包袱**：手动 Docker 部署在未改默认变量时**保留 `quantdinger` / `123456`**（仅为向后兼容）。官方措辞很直接："**该凭据不适合面向公网的部署**，首次启动前或首次登录后立即修改"。一键安装脚本**不接受**把密码设成 `123456`。**如果你用的是旧镜像或手动部署，第一件事就是改这个账号。**
- **配置文件里有密码与 API Key，权限不能放宽**：Settings UI 会把运行时配置写进 `/app/.env`（GHCR 栈里是宿主机 `backend.env`），含密码与 API Key，当前镜像会把它设为运行 UID `10001` 属主、权限 `600`。**官方明确禁止 `chmod 755` 或递归 `777`**——即使 755，root 属主时 UID 10001 仍然没有写权限。
- **生产模式与该文件的读写方式冲突**：hardened 的 `docker-compose.production.yml` **故意把 `/app/.env` 只读挂载**，因此**在生产下从 Settings UI 保存配置会失效**，正确做法是在宿主机改好配置后重建服务。这是容易踩的一个坑。
- **开源自托管 vs 商业托管**：项目由公司运营，同时提供 SaaS 托管版与订阅/支付相关流程（调度进程里就包含 payment schedules）。**自托管与商业版的功能边界、许可边界需要自己确认**——这类"open-core 且带托管版"的结构，本 wiki 在 [firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md) 上记录过同类情形（主库与服务端许可分层）。
- **AI 侧会用到外部模型端点**：AI 研究/分析支持 OpenRouter、OpenAI 兼容接口、Google、DeepSeek、Grok、MiniMax 等。**启用后，行情、持仓与策略相关上下文会发往你配置的模型服务商**，注意数据出境与合规。
- **活跃度判断要交叉看**：最近推送是 2026-08-17，距核验日约 2.5 个月；但 Star / Fork / Issue 数显示社区活跃。**v5 是"正在演进中的版本"**，部署前建议直接看仓库页与 release 页确认最新状态。
- **我未做任何验证**：本次**只读 README、LICENSE 与 API 元数据**，未部署、未跑回测、未接交易所或券商、未验证数据源与回测口径（**是否计手续费、滑点、资金费率等一概未知**）。**上面所有架构与安全描述均引自 README 自述。**

## 什么时候会再看

- 想找**自托管、凭据不出本机**的多市场（加密 + 美股）交易系统，且需要回测与实盘打通时。
- 想参考**"HTTP 进程不要持有长跑交易循环""缓存 Redis 与任务 Redis 分离""lease + fencing token 防止双 worker 操盘"**这类交易系统架构纪律时——这是本篇最可复用的部分。
- 想研究**"AI Agent 如何被安全地允许下单"**时：它的"token scope + paper_only + 服务端开关 + 限额白名单"四重闸门是一个可借鉴的模板。
- 需要把交易系统接进 MCP / 编码助手工作流时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为量化交易/研究平台**：[ai_quant_trade](ai_quant_trade-AI量化交易操盘手.md)、[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)、[tick-stock-panel](tick-stock-panel-自托管A股量化工作台.md)、[KHunter](KHunter-A股量化选股系统.md)——**本篇是这一组里唯一明确覆盖"加密交易所 + 真实下单"的**，其余多聚焦 A 股选股或研究。
- **入门读物对照**：[xquant-beginner](xquant-beginner-人人都是量化交易员开源书.md)——那本教"怎么把想法写成 spec 并验证"，本篇是"验证完之后往哪跑"的工程平台。
- **回测与指标实现对照**：[indicator](indicator-Go技术指标与回测框架.md)。
- **同类引擎对照**：[nautilus_trader](nautilus_trader-Rust原生交易引擎.md)——Rust 原生引擎 + Python 控制面，同样把"同一份策略代码跑回测与实盘"当卖点；区别在于本篇是**带 UI / 编排 / Agent 层的应用**，而它明确把 UI、分布式编排与内置 AI 工具排除在开源范围之外。
- **MCP / Agent 接入**：[servers](servers-MCP官方参考服务器合集.md)——本篇自带 MCP server 与 Agent Gateway，可与之对照。
- **"开源主库 + 商业服务"许可分层**：[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md)。

## 参考来源

- 项目仓库与 README：https://github.com/OpenByteInc/QuantDinger
- 许可证文件（Apache-2.0 全文）：https://github.com/OpenByteInc/QuantDinger/blob/main/LICENSE
- 官网 / 托管版：https://www.quantdinger.com ｜ https://ai.quantdinger.com
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02，Star 数与仓库内容可能变化。**仅阅读 README、LICENSE 与元数据；未部署、未运行回测、未接入任何交易所或券商，因此无法评价其功能真实表现与回测口径是否严谨。** 架构、安全模型与风险提示均引自 README 自述。**本笔记不构成任何投资建议；涉及实盘交易的项目请自行评估资金、合规与安全风险。**
