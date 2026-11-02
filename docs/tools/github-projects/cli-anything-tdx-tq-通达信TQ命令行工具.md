# cli-anything-tdx-tq：通达信 TQ SDK 的命令行封装

> 值得记录的是两点：它把"宁可不给数据、也不编造数据"写成了硬门禁，以及它的交易接口默认只做离线预演。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README 全文与 GitHub API 元数据，未安装、未连接通达信客户端、未运行任何命令）
- 适用范围：**仅 Windows**；Python 3.12/3.13；需要本机安装并登录支持 TQ SDK 的通达信客户端

## 核心结论

把通达信本机的 **TQ SDK（`tqcenter.py`）** 封装成一套命令行工具，自述"100% 覆盖本机 TQ SDK 的行情、证券、基金/ETF、指数、板块、专业财务、交易统计、技术指标/选股/专家公式、客户端联动接口"，并在原生 SDK 之上补了数据完整性门禁、结构化输出与文件导出契约。

命令组织得很规整：**9 个命令组、49 个叶子命令**。

| 命令组 | 命令 |
| --- | --- |
| 全局 | `init`、`close`、`status` |
| market | `get`、`snapshot`、`divid`、`zdt`、`exday`、`pv`、`refresh` |
| stock | `info`、`more`、`list`、`calendar`、`search`、`gb`、`kzz`、`relation`、`ipo`、`etf` |
| sector | `list`、`stocks`、`user`、`create`、`delete`、`rename`、`clear`、`update-stocks` |
| financial | `get`、`by-date`、`trading`、`one` |
| formula | `list`、`info`、`zb`、`xg`、`exp` |
| client | `link`、`warn`、`msg`、`download` |
| trade | `account`、`asset`、`positions`、`orders`、`order`、`cancel` |
| config | `show`、`set` |

代码体系覆盖 8 大数据领域、26 种市场后缀（股票 `.SH/.SZ/.BJ/.HK/.US/.NQ`、基金 `.OF`、指数 `.CSI/.CNI/.SSI/.HI/.UZ`、期货 `.CFF/.CZC/.DCE/.SHF/.INE/.GFE/.QHZ/.SGE`、期权与可转债的独立后缀、宏观指标 `.HG` 等）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [blee0909/cli-anything-tdx-tq](https://github.com/blee0909/cli-anything-tdx-tq) |
| 许可证 | MIT |
| 语言 / 依赖 | Python；`uv` 管理依赖；入口 `cli-anything-tdx-tq` 与短命令 `tdx-tq` 等价 |
| 关注度 | **4 Star、2 Fork、0 关注、0 个开放 Issue**（GitHub API，2026-10-30） |
| 维护状态 | 2026-09-23 创建，最近推送 2026-10-24；无 Tag / 无 Release |
| 体积 | 约 247 KB（`size` 字段） |

## 前置条件与配置

必须先在通达信客户端中**启动并登录**，且安装目录下存在 `PYPlugins/sys/tqcenter.py` 与配套的 `TPythClient.dll`、`TPyth.dll`；默认安装路径为 `D:\tdx`。

```powershell
uv sync
uv run tdx-tq --version
uv run tdx-tq status --json          # 离线诊断，不建立客户端连接
uv run tdx-tq config set --tdx-path D:\tdx --json
```

路径解析优先级为：命令行 `--tdx-path` > 环境变量 `TDX_PATH` > 配置文件 > 默认值；`config set` 会把路径**原子保存**到 `~/.tdx_tq/config.json`。

## 三个值得借鉴的设计

**① 数据完整性门禁：默认拒绝返回不完整数据**

`market get` 与 `financial get / by-date / one / trading` **默认执行严格完整性校验**：遇到部分标的无数据、请求字段全缺失、或无效占位行（如宏观指标的零日期 `'0'`、交易统计的 `'--'`）时，**直接以退出码 1 报 `DATA_INCOMPLETE`**。

业务上确实需要可用子集时，才显式加 `--allow-partial`，此时返回一个 `schema_version=1` 的信封，**明确列出** `available_stocks`、`missing_stocks`、`missing_fields`、`dropped_records`。

**最典型的例子是 1 分钟线**：`market get -p 1m` 在本地没有 1m 数据时**不会返回任何东西，而是报错并给出解决路径**——README 解释了原因（全市场 5000+ 只股票的 1 分钟历史达数百 GB，客户端默认不同步），并指明需要手动在客户端执行**【系统】→【盘后数据下载】→ 勾选 1 分钟线数据**。

> 这个取舍很少见：**它宁愿让你拿到"没有数据"，也不给你填充出来的数据。** README 的原话是"杜绝用虚假数据误导量化策略"。

**② 输出契约与退出码**

- `--json` 保证 **`stdout` 只输出合法 JSON**，SDK 诊断与告警全部重定向到 `stderr`——这让管道串联可靠；
- 退出码：`0` 成功、`1` 操作或数据完整性错误、`2` 参数校验错误（`INVALID_PARAMETER`）、`130` 中止；
- 导出支持 `.csv/.json/.xlsx`，且是**原子写入**；
- 支持 `-` 作为"从标准输入读代码列表"，可整条流水线串起来：

```powershell
$all = uv run tdx-tq stock list -m 5 --json
$all | uv run tdx-tq market get -s - -p 1d -c 60 --allow-partial -o all_a_daily.csv --json
```

**③ 交易接口默认只做预演**

`trade order` **默认仅执行离线参数校验与安全预演，必须显式传 `--confirm` 才会向客户端发送真实报单**；`trade cancel` 没有预览模式（README 明确提示了这一点）。README 还专门提示：**SDK 返回受理回执不代表已成交或已撤单**，遇到状态未知（`TRADE_STATE_UNKNOWN`）时应先用 `trade orders` 核对，**切勿盲目重复发单**。

另外几处细节也处理得比较谨慎：

- `market get` 强制 `fill_data=False`，**杜绝 SDK 用前值填充制造出的"假 K 线"**；
- `--start` 与 `-c`（尾部数量）**严格互斥**；
- `market zdt` 的涨跌停历史区分三态——`found`（查到日期）、`no_record`（SDK 零值）、`unavailable`（字段缺失），后两者日期为 `null`，**README 明确说明不能解释为"历史上从未涨跌停"**；
- `market refresh` 只支持客户端缓存的 `1m/5m/1d`，其它周期**在建立连接前就拒绝**（退出码 2），避免连上再报错。

## 使用前需要注意

- **强依赖本机专有客户端**：这不是一个能连公开 API 的工具，**必须在本机装好并登录通达信客户端**，且客户端插件目录里要有 `tqcenter.py` 与两个 DLL。**换机器、换券商版客户端、或客户端升级后 SDK 路径与行为都可能变化**，这些依赖不在本仓库内，作者也无法保证兼容。
- **仅 Windows**：Python 版本限定 3.12/3.13。
- **涉及真金白银**：即使默认是预演，**`--confirm` 之后就是真实报单**。请在模拟账户上先走完整流程；`trade cancel` 无预览这一条尤其要记住。README 顶部有醒目的免责声明（不构成投资建议），但**声明不改变操作后果由使用者承担这一事实**。
- **"100% 覆盖""零侵入缺陷热修补"是自述，未验证**：README 开头声称在原生 SDK 之上提供"零侵入缺陷热修补"，但**该机制的原理与实现我未读到**（应在 `docs/` 内），此处按未验证记录。所谓"修补"意味着它对第三方 SDK 的运行期行为有干预，**上线前应确认它到底改了哪些函数**。
- **受众极小**：4 Star、2 Fork、无任何开放 Issue——**基本没有社区兜底**，遇到问题大概率只有作者一条路（而作者也不一定响应）。
- **数据口径依赖客户端**：涨跌停日期窗口"官方描述为近两年"、1m 数据要靠手动下载、`stock list` 的集合代码依赖 SDK 定义——**这些都随客户端版本变化**，不是本工具的稳定契约。
- **我只读了 README 与元数据，没有安装、没有连接客户端、没有运行命令**：命令是否全部可用、SDK 兼容范围、导出与完整性校验的实际行为均未验证。

## 什么时候会再看

- 需要**在 Windows 上批量取通达信本机数据、并把它接进脚本或 AI 助手**时：这是比较完整的 CLI 封装，且输出契约清晰。
- 想参考**"数据完整性门禁怎么写"**时：默认严格校验 + `--allow-partial` 显式降级 + 结构化缺失清单，这套三段式可以搬到任何取数工具上。
- 想参考**"交易类工具的安全默认值"**时：默认预演、必须显式 `--confirm`、提醒"受理回执 ≠ 成交通知"、状态未知时先查询而非重发。
- 需要**用一个工具同时覆盖行情、财务、板块、公式与客户端联动**时：49 个叶子命令的覆盖度在同类里算高。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **A 股取数的另一条路线对照**：[a-stock-data](a-stock-data-A股全栈数据工具包.md)——那个走公开 HTTP 数据源（mootdx / 腾讯财经 / akshare / 东财等），需要自己处理限流、防盗链与封 IP；本篇走**本机客户端 SDK**，不用管这些，代价是必须装客户端、只能 Windows、且依赖专有接口。
- 使用 akshare 的 A 股分析项目：[KHunter](KHunter-A股量化选股系统.md)、[easy_investment_Agent_crewai](easy_investment_Agent_crewai-A股多Agent分析平台.md)——可对照"公开数据源"与"本机客户端 SDK"两类方案在选股/分析场景下的取舍。

## 参考来源

- 项目仓库与 README：https://github.com/blee0909/cli-anything-tdx-tq
- 开发文档索引（仓库内，未阅读）：`docs/README.md`
- 元数据（Star / Fork / 时间 / 许可证）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未安装、未连接客户端、未运行命令；命令清单、覆盖范围与"缺陷热修补"机制均以作者自述为准，未独立验证。本笔记不构成任何投资建议。
