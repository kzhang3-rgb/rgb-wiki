# easy_investment_Agent_crewai：A 股多 Agent 分析平台

> 记录它的 Agent 分工与工具集，并说明它作为"LLM 输出投资参考"这一类项目需要额外注意什么。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、目录结构与 GitHub API 元数据，未安装运行）
- 适用范围：Python 3.12+，依赖 Poetry 或 uv；需要可用的 LLM（默认 Ollama 本地模型，或 OpenAI）

## 核心结论

用 CrewAI 编排四个角色（市场分析师、财务报表专家、市场情绪研究员、投资顾问）来分析 A 股，数据源是 akshare。特点是把 A 股特有的东西写进了分析维度：政策影响、涨跌停限制、散户情绪、资金轮动。技术结构上，Agent 与任务分别外置在 `config/agents.yaml` 和 `config/tasks.yaml`，LLM 可切换。

用法很直接：改 `main.py` 里的股票代码，运行后得到一份多角色协作生成的分析报告。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [liangdabiao/easy_investment_Agent_crewai](https://github.com/liangdabiao/easy_investment_Agent_crewai) |
| 许可证 | README 写"本项目采用 MIT 许可证"，但**仓库里没有 LICENSE 文件**（GitHub 元数据也显示无许可证） |
| 技术栈 | Python 3.12+、CrewAI、LangChain、akshare、Poetry / uv |
| 关注度 | 456 Star、97 Fork、3 人关注、6 个开放 Issue（2026-10-30 的 API 数据） |
| 维护状态 | 2025-09-26 创建，**共 9 次提交**，最近推送 2026-05-07；1 个分支、无 Tag、无 Release、单贡献者 |
| 仓库构成 | 仅三项：`stock_analysis/`、`stock_analysis_a_stock/`、`README.md`（约 512 KB） |

> 数据与口径说明：网页显示 669 Star / 127 Fork，明显高于 API 返回值。按收藏专题的约定采用 API 数据——但这次是页面更高、API 更低，说明前面那条"以 API 为准"的规则并不能保证更接近真实值，见下方"需要留意的一点"。

## 四大 Agent 与工具集

| Agent | 职责 |
| --- | --- |
| A 股市场分析师 | 技术面、政策面、资金面分析 |
| 财务报表专家 | 财务比率、趋势分析、同业对比 |
| 市场情绪研究员 | 资金流向、市场情绪、政策影响 |
| A 股投资顾问 | 综合分析、投资策略、风险控制 |

工具集分四类：A 股数据工具（实时行情、历史数据、财务指标）、财务分析工具（深度分析、同业对比）、市场情绪工具（资金流向、新闻情绪、技术情绪）、计算器工具（安全数学计算）。

**A 股特色维度**：政策影响分析、涨跌停限制影响、散户投资者心理、资金轮动规律。

支持沪市 `600519.SH`、深市 `000001.SZ`、港股 `00700.HK` 三种代码格式。注意 README 的默认分析标的是**腾讯控股（00700.HK）**，实际是港股。

## 使用方式

```bash
git clone <repository-url> && cd stock_analysis_a_stock
poetry install --no-root     # 或 uv sync
cp .env.example .env         # 配置必要参数
poetry run python src/a_stock_analysis/main.py
```

改分析标的：

```python
inputs = {
    'company_name': '贵州茅台',
    'stock_code': '600519.SH',
    'market': 'SH'          # SH=上交所, SZ=深交所, HK=港股
}
```

切换模型改 `src/a_stock_analysis/crew.py`，调整 Agent 与任务改 `config/agents.yaml`、`config/tasks.yaml`。

## 使用前需要注意

- **没有 LICENSE 文件，与 README 声明不符**：README 结尾写"本项目采用 MIT 许可证"，但仓库根目录没有 LICENSE，页面资源区也没有许可证条目，GitHub 元数据为 `null`。README 里的口头声明不构成规范的 MIT 授权，二次使用前建议直接联系作者确认。
- **维护活跃度低**：共 9 次提交，最近推送是 2026-05-07（约五个月前），无 Release、单贡献者、单个 Issue 也主要由作者自己处理。属于"能跑起来但不再演进"的状态。
- **依赖免费数据源与 LLM 输出**：分析结论由 akshare 数据 + LLM 推理生成，两者都没有校验环节。把它当"多角色复述了一遍公开数据"更合适，不宜作为投资依据。
- **已知的落地坑**：仓库里有一个已合并的修复（PR #7），原因是**自定义工具的 name 使用中文导致 OpenAI API 报错**。这个坑对国内项目很典型——凡是把中文当函数名/工具名交给 LLM 接口的场合都容易踩。
- **README 示例可能已过时**：文档里给的模型接入示例是 `from langchain.llms import Ollama` 这一类旧版导入路径，较新版本的 LangChain 已把这些集成迁到独立包。本条为我的判断，未实际运行验证。
- **默认需要本地模型或 API Key**：默认走 Ollama 的 `llama3.1`，没有本地模型就得接商业 API，会产生费用；README 未说明成本量级。
- 我未安装运行，未验证数据获取与多 Agent 协作的实际输出质量。

## 需要留意的一点：收藏专题的 Star 口径规则

这个仓库的网页数字（669）比 API（456）高，和前面几个仓库的情况相反。也就是说，我此前在收藏专题里写的"关注度以 API 为准"并不能保证更接近真实值——它只是保证了**来源统一、可比**。

我在笔记里仍然按 API 记录，但如果你在意某个仓库的真实热度，最可靠的做法是直接打开 GitHub 页面确认，而不是依赖任何单一抓取来源。

## 什么时候会再看

- 想参考 CrewAI 多 Agent 分工的组织方式，以及把 Agent 与 Task 外置成 YAML 的做法时。
- 想参考"A 股市场特色"如何落进分析维度（政策、涨跌停、散户情绪、资金轮动）时。
- 想为一个领域做多角色分析助手，需要确认 Python 版本与依赖管理选型（Poetry / uv）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 同为 A 股分析工具、可对比实现路线：[KHunter](KHunter-A股量化选股系统.md)（技术形态与评分模型，不依赖 LLM）。
- 同属投资类项目：[US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md)、[stonkfly](stonkfly-果蝇连接组交易实验.md)。

## 参考来源

- 项目仓库与 README：https://github.com/liangdabiao/easy_investment_Agent_crewai
- 作者在 linux.do 的介绍帖：https://linux.do/t/topic/993130
- About 区指向的第三方技能页：https://skillhub.cn/skills/user_8bb4b4f5/stock-deep-research-skill

以上信息核验于 2026-10-30，Star 数与仓库内容可能变化。未安装运行，分析质量与输出结果均未验证。
