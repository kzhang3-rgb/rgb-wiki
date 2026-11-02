# indicator：Go 的技术指标与回测框架

> 值得记的是两条"反直觉"的取舍：**所有指标和策略都不给预设参数**，以及 **v2 起改用 Go channel 做数据流**；另外它的许可证 v2 是 AGPLv3，v1 才是 MIT，用之前必须先分清版本。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文与 GitHub API 元数据，未克隆、未编译、未回测）
- 适用范围：Go（引用路径 `github.com/cinar/indicator/v2`）；回测需要外部行情源（README 用 Tiingo）

## 核心结论

`cinar/indicator` 是一个 **Go 语言的技术分析指标库 + 回测框架**，自述 **80+ 指标**，并按四大类组织：

| 类别 | 例子 |
| --- | --- |
| 趋势 | SMA、EMA、MACD、KAMA、HMA、T3、TRIX、KST、Aroon、Envelope、Pivot Point…… |
| 动量 | RSI、Stochastic RSI、Awesome Oscillator、Ichimoku Cloud、Connors RSI、TD Sequential…… |
| 波动率 | ATR、Bollinger Bands、Keltner Channel、Donchian Channel、Choppiness、Ulcer Index、Z-Score…… |
| 成交量 | OBV、MFI、CMF、VWAP、NVI、Force Index、Klinger…… |

以及一小类**资产估值**（FV / NPV / PV，本质是金融数学，不算技术指标）。

**v2 相对 v1 做了整体重写**，README 列了五条理由，其中三条对使用方式影响很大：

1. **数据流化**：输入输出都改成 **Go channel**。习惯切片的人要用 `helper.SliceToChan` / `helper.ChanToSlice` 转换，或者干脆退回 v1。
2. **泛型**：支持多种数值类型，不再限定单一数字类型。
3. **不给预设值**：README 原话"所有指标和策略都被设计成完全可配置、没有预设值"。这是一条**明确的设计选择**——好处是不会有隐藏默认值误导，代价是每个指标都得自己指定周期等参数。

回测侧也不只给库，还给了两个命令行工具：

- `indicator-backtest`：对仓库里的资产跑策略回测，产出 HTML 报告。**默认不跑任何策略**，必须用 `-strategies` 显式点名，或用 `-config` 传整份 JSON。`-list-strategies` 能列出全部策略名。
- `indicator-sync`：在**源仓库与目标仓库之间同步**行情快照（README 的示例是 Tiingo → 本地文件系统），支持多 worker 并发，可指定补齐天数。

报告里的"业绩指标"只提了两个风险调整指标：**Sharpe 与 Sortino**。README 也说明了它们是从策略的 `Outcome` 曲线算出来的。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [cinar/indicator](https://github.com/cinar/indicator) |
| 描述 | High-performance technical analysis indicators and backtesting framework in Go. Zero dependencies. |
| 许可证 | **v2.x 起双许可：AGPLv3 + 商业许可；v1.x 为 MIT**（详见下节） |
| 语言 / 依赖 | Go；README 称 **Zero dependencies**（指代码依赖，不含行情数据源） |
| 关注度 | **1,913 Star、285 Fork、22 关注、37 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2021-06-11 创建，最近推送 2026-09-14；最新 Tag `v2.1.44`（另有 v2.1.43、v2.1.42） |
| 体积 | 约 2,352 KB（`size` 字段） |
| 同族项目 | TypeScript 版：[cinar/indicatorts](https://github.com/cinar/indicatorts) |

## 许可证：先分版本，再谈能不能用

这是本篇最需要先确认的一件事：

- **`v2.x.x` 及以上：AGPLv3 与商业许可双许可**。免费使用走 **AGPLv3**——它对**网络服务**有传染性要求（提供网络交互即需向用户提供源码），**对闭源商业产品通常不可接受**，需要另谈商业许可。
- **`v1.x.x`：MIT**。

也就是说，**同一个仓库、同一个项目名，不同版本线的许可完全不同**。README 明确写了这一点，但只在文件末尾，标题级信息里没强调。**任何商用前必须确认自己引的是 v1 还是 v2，以及是否接受 AGPLv3 的约束。**

README 整篇前面还压着一段非常长的**风险披露与免责声明**：声明仅供教育/研究，作者不是注册投顾，引用 **CFTC Rule 4.41** 说明"回测与假设业绩有其固有局限，不代表真实交易结果"，并强调杠杆与衍生品可能亏光本金。**这段是免责文本，不改变 AGPLv3 或回测结论本身的效力。**

## 用法要点

库的引入方式很常规：

```bash
go get github.com/cinar/indicator/v2
```

```go
import "github.com/cinar/indicator/v2"
```

回测 CLI 的两种用法：

```bash
# 直接点名策略
indicator-backtest \
  -repository-name filesystem \
  -repository-config /home/user/assets \
  -report-config /home/user/reports \
  -strategies apo,macd,rsi \
  -workers 1

# 整份 JSON 描述（-config 会替换掉其他同名 flag）
indicator-backtest -config backtest.json
```

`-config` 的 JSON 里，`strategies` 每项只需写**要改的字段**——它会**逐字段叠加到该策略的默认实例上，包括嵌进嵌套结构**（README 的例子是 `Rsi.Rma.Period`），其余保持文档里的默认值。字段名就是对应 Go 结构体的导出字段名。**注意这与"库本身不给预设值"并不矛盾**：库层面没有默认，但回测示例策略这一类"上层封装"是带默认实例的。

Docker 是最省事的入手方式（镜像在 `ghcr.io/cinar/indicator`）：

```bash
docker run -it --rm \
  -v $(pwd)/output:/app/output \
  ghcr.io/cinar/indicator:latest \
  --api-key YOUR_TIINGO_API_KEY --days 365 \
  --assets aapl msft googl --strategies apo,macd,rsi
```

**需要一个免费的 Tiingo API key**——回测的数据不来自仓库自身。

## 使用前需要注意

- **许可证是最大的坑**：v2 是 **AGPLv3 / 商业双许可**，v1 才是 MIT。**把 v2 用在闭源产品或提供网络服务前，务必先确认合规**；README 把这一段放在最末，很容易被"Go 模块 / 零依赖"的好印象掩盖。
- **"Zero dependencies" 只指代码**：回测所需的历史行情**必须自己接数据源**（README 全线用 Tiingo，要 key），`indicator-sync` 也是为"从远端仓库同步快照到本地"服务的。**别把零依赖理解成开箱即用。**
- **数据流 API 有学习成本**：v2 全面转到 Go channel。如果你的代码是切片思维，要么用 helper 来回转，要么直接用 v1——README 明说这是给"习惯切片的人"留的退路。
- **"90% 代码覆盖率""每个指标都有专属 CSV 测试数据"是作者自述**：README 把它列为 v2 的改进目标。**我没有跑覆盖率、也没有核对测试数据，此处按未验证记录。**
- **回测默认不跑策略这一点是好设计，但也意味着别指望"跑一下就有结论"**：必须先用 `-list-strategies` 找到策略名再点名。报告里 README 只点了 Sharpe / Sortino 两个指标，**是否还有别的口径、图形质量如何，我未看到实际报告。**
- **回测输入的"资产"来自仓库快照**：`asset` 包提供 FileSystem / InMemory / Tiingo 三种 Repository。**数据的质量、复权与否取决于你同步进来的快照**，工具本身不做判断。
- **商标提示**：README 声明 TD Sequential®、Bollinger Bands®、ConnorsRSI® 均为他人注册商标，本库是**独立的开源数学实现，与商标方无关**。若要商用，命名与宣传上需注意。
- **只读了 README 与元数据**：未编译、未回测、未验证 80+ 指标中任何一个的计算结果。

## 什么时候会再看

- **需要在 Go 服务里算技术指标**时：这是覆盖度较高、且明确说明设计取舍的一个库。
- 想参考**"库层不给预设值"这种设计**是否适合自己时：好处是无隐藏默认，代价是调用方必须显式指定所有参数——**适合追求确定性的场景，不适合想"调一下就用"的场景**。
- 想参考**"回测工具默认什么都不跑"**这一安全默认时：与 [cli-anything-tdx-tq](cli-anything-tdx-tq-通达信TQ命令行工具.md) 的"交易默认预演"是同一思路——**危险或昂贵的动作默认不执行，必须显式指定**。
- 想参考**"双许可 + 大段免责声明"怎么写**时：可作为金融类开源库的合规模板（但它也提醒我们，**许可证信息放在文末是有风险的写法**）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **量化/回测类对照**：[ai_quant_trade](ai_quant_trade-AI量化交易操盘手.md)（量化学习合集）、[KHunter](KHunter-A股量化选股系统.md)（A 股选股）、[easy_investment_Agent_crewai](easy_investment_Agent_crewai-A股多Agent分析平台.md)（多 Agent 分析）、[US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md)（风险面板）——可对照"**指标库**"与"**端到端系统**"的分工：本库只提供可组合的数学与回测原语，不含选股、AI 或界面。
- **数据来源对照**：[a-stock-data](a-stock-data-A股全栈数据工具包.md)、[global-stock-data](global-stock-data-美股与全球市场数据工具包.md)、[cli-anything-tdx-tq](cli-anything-tdx-tq-通达信TQ命令行工具.md)——本库的行情**不是自带的**，回测得自己接源；这几篇正好是"数据从哪来"的不同答案。
- **AGPL 传染性的另一例**：[ConcordRTS](ConcordRTS-用C++20重写红警2引擎.md) 是 GPL-3.0-only——两者都提示：**用之前先看许可证版本与条款**，不要只看仓库首页的徽章。
- **同类"AI 生成配置 + 校验"思路**（跨领域但可对照）：[zframes](zframes-让Agent用对话生成行情看板.md)。

## 参考来源

- 项目仓库与 README：https://github.com/cinar/indicator
- TypeScript 同族实现：https://github.com/cinar/indicatorts
- v1 分支（MIT）：https://github.com/cinar/indicator/tree/v1
- 元数据（Star / Fork / 时间 / 许可证 / Tag）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README 与元数据；未编译、未运行、未回测。指标清单、90% 覆盖率与测试数据完备性均以作者自述为准，未独立验证。许可证以 README 的说明为准，**商用前请自行核对 `LICENSE.md` 原文**。本笔记不构成任何投资建议。
