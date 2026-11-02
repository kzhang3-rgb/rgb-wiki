# stock-tui：在终端里看 A 股 K 线与基本面

> 一句话记住它：把 A 股的 K 线图、市盈率、成交量做成**纯终端输出**的小工具——不装 GUI、不开网页，一条命令打印 ASCII 蜡烛图。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文与 GitHub API 元数据，未安装未运行）
- 适用范围：A 股（沪深），需要 Python 环境与可访问公开行情接口的网络

## 核心结论

stock-tui 解决的问题很具体：**在终端里快速看一眼某只 A 股**。它输出两块内容——

| 输出 | 内容 |
| --- | --- |
| 基本信息面板 | 最新价、涨跌幅、今开/昨收/最高/最低、市盈率(动)、市净率、成交量、成交额、换手率、总市值/流通市值 |
| 图表 | 终端 ASCII 蜡烛图 + 成交量柱状图（红涨绿跌） |

数据来源是 **AKShare**（免费、无需 API Key），AKShare 底层再走东方财富、雪球的公开行情接口。也就是说它**不自己维护数据源**，上游接口怎么变它就怎么变。

一个方便的小设计是**支持直接输入中文名**：写 `600519`、`sh600519`、`贵州茅台` 甚至 `茅台` 都能查。名称能唯一匹配就自动识别，匹配到多只就列候选让你改代码；首次按名称查询会拉一份全市场代码-名称对照表并缓存（`~/.cache/stock-tui/`，有效期 1 天）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Folgerjun/stock-tui](https://github.com/Folgerjun/stock-tui) |
| 描述 | 在终端（PowerShell / Windows Terminal / Linux Shell）中查看 A 股 K 线图、市盈率、成交量等基本信息的小工具。 |
| 许可证 | **仓库未提供许可证**（GitHub API 返回 `license: null`，README 全文未提许可） |
| 语言 / 数据源 | Python；数据源 AKShare（底层为东方财富、雪球公开行情接口） |
| 关注度 | **154 Star、14 Fork、2 关注、0 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-08-04 创建，最近推送 2026-10-21；`main` 分支（无 Tag/Release 记录） |
| 体积 | 约 81 KB（`size` 字段） |

## 用法要点

安装走标准 venv 流程：

```powershell
cd stock-tui
python -m venv .venv
.\.venv\Scripts\pip install -r requirements.txt
```

也可以装成命令行工具：

```powershell
.\.venv\Scripts\pip install -e .
.\.venv\Scripts\stock-tui 600519
```

调用参数：

| 参数 | 说明 |
| --- | --- |
| `code` | 股票代码或中文名称（必填），如 `600519`、`000001`、`贵州茅台` |
| `-d, --days` | 展示最近 N 个交易日 K 线，默认 90 |
| `--adjust` | 复权方式：`qfq`（默认，前复权）/ `hfq`（后复权）/ 空（不复权） |
| `--no-chart` | 只显示基本信息表格，不画 K 线图 |

几个示例：

```powershell
python main.py 600519                 # 贵州茅台（沪市 6 开头）
python main.py 000001 -d 60           # 平安银行（深市），近 60 个交易日
python main.py 600519 --no-chart      # 只看基本面
python main.py 600519 --adjust hfq    # 后复权
python main.py 茅台                    # 用部分中文名匹配
```

## 使用前需要注意

- **没有许可证**：仓库未声明任何开源许可。默认版权保留，**在复制、分发或用于衍生项目前应直接询问作者**，不能想当然当成 MIT 使用。
- **数据是第三方公开接口，不是交易所直连**：AKShare → 东方财富/雪球这条链随时可能限流、改版或失效；出现取不到数时**不一定是你用错，而是上游变了**。
- **不是投资建议**：工具只是把公开行情画出来，不含任何判断。
- **终端编码**：若显示乱码，README 建议确认终端为 UTF-8（PowerShell 可执行 `chcp 65001`）。
- **代理处理**：README 说若本地代理不稳定，工具会**自动绕过代理**（`NO_PROXY`）并对网络抖动自动重试——这在企业代理环境里可能反而绕错路，需留意。
- **缓存有有效期**：中文名称对照表缓存 1 天，过期会重新拉取。

## 什么时候会再看

- 需要在纯终端（无图形界面 / SSH 远程）里快速看一眼某只 A 股基本面时；
- 想用最少依赖（一个 Python 脚本 + AKShare）画 ASCII 行情图时；
- 需要一个"支持中文名直接查询"的轻量行情脚本做参考时。

## 相关笔记

- [a-stock-data：A 股全栈数据工具包](a-stock-data-A股全栈数据工具包.md)：同样是 A 股数据工具，但定位是取数库而非终端展示。
- [cli-anything-tdx-tq：通达信 TQ 命令行工具](cli-anything-tdx-tq-通达信TQ命令行工具.md)：另一条"在命令行用行情数据"的路线。
- [Vibe-Research：A 股/美股/港股投研工作台](Vibe-Research-A股美股港股投研工作台.md)：更偏投研工作流，数字出处可追溯。

## 参考来源

- 仓库主页：<https://github.com/Folgerjun/stock-tui>
- README（`main` 分支）：<https://raw.githubusercontent.com/Folgerjun/stock-tui/main/README.md>
- GitHub API 元数据：<https://api.github.com/repos/Folgerjun/stock-tui>（核验日期 2026-11-02）
