# BettaFish：多 Agent 舆情分析系统"微舆"

> 记录一个"从 0 实现、不依赖任何 Agent 框架"的多智能体舆情分析系统：它把"多 Agent 论坛式协作"做成了一套完整工程，并可与同作者的 MiroFish 串成"分析 → 预测"链路。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README（中文版）全文与目录树、元数据，未 clone、未部署、未运行）
- 适用范围：多 Agent 系统 / 舆情分析 / 数据采集与报告生成的选型与架构参考

## 核心结论

`666ghj/BettaFish`，中文名"**微舆**"（谐音"微鱼"，取 BettaFish 斗鱼"小而强大、不畏挑战"之意），是一个**从 0 实现的多智能体舆情分析系统，不依赖任何 Agent 框架**。用户只需像聊天一样提出分析需求，系统即全自动分析**国内外 30+ 主流社媒与数百万条评论**，自述目标是"打破信息茧房、还原舆情原貌、预测未来走向、辅助决策"。

架构上它由**四类专业 Agent + 一个论坛引擎 + 一个爬虫系统**组成：

- **Query Agent**：国内外网页搜索。
- **Media Agent**：多模态内容分析（含短视频）。
- **Insight Agent**：私有舆情数据库挖掘。
- **Report Agent**：内置模板的多轮报告生成（含 IR 中间表示与 HTML/PDF/Markdown 渲染）。
- **ForumEngine**：给不同 Agent 赋予不同工具集与思维模式，用**论坛主持人 LLM** 引导链式思维碰撞与辩论，避免单一模型同质化。
- **MindSpider**：社媒爬虫子系统（基于 MediaCrawler），分"话题提取"与"深度舆情爬取"两块。

README 自述六大优势：AI 驱动的全域监控、超越 LLM 的复合分析引擎（5 类专业 Agent + 微调模型 + 统计模型中间件）、强大的多模态能力、Agent"论坛"协作机制、公私域数据无缝融合、轻量化与高扩展性框架。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [666ghj/BettaFish](https://github.com/666ghj/BettaFish) |
| 描述 | 微舆：人人可用的多 Agent 舆情分析助手，打破信息茧房…从 0 实现，不依赖任何框架 |
| 许可证 | **GPL-2.0** |
| 语言 | **Python 98.5%** |
| 关注度 | **42,584 Star、7,614 Fork、229 关注、3 个开放 Issue**（核验于 2026-11-05） |
| 维护状态 | **2024-07-01 创建**，最近推送 **2026-10-16** |
| 分支 | 默认分支 `main` |
| 发行版 | **6 个 Release / 9 个 tag**，最新 **v3.0.0**（2025-12-23） |
| 贡献者 | **30 人**（含作者 `666ghj`、`MaYiding` 等） |
| topics | 含 `multi-agent-system`、`public-opinion-analysis`、`sentiment-analysis` |
| 关联项目 | 同作者的 [MiroFish](MiroFish-群体智能预测引擎.md)（预测方向）；另有入门 Demo [DeepSearchAgent-Demo](https://github.com/666ghj/DeepSearchAgent-Demo) |

## 内容结构（一次完整分析流程）

| 步骤 | 阶段 | 主要操作 | 参与组件 |
| --- | --- | --- | --- |
| 1 | 用户提问 | Flask 主应用接收查询 | Flask 主应用 |
| 2–4 | 并行启动 / 初步分析 / 策略制定 | 三 Agent 并行、概览搜索、分块研究策略 | Query / Media / Insight Agent |
| 5–N | **循环阶段** | **论坛协作 + 深度研究**（多轮） | ForumEngine + 所有 Agent |
| N+1–N+3 | 结果整合 / IR / 报告生成 | 收集结果 → Document IR → 渲染交互式 HTML 报告 | Report Agent |

关键实现细节（读 README 目录树所得）：

- **Insight Engine** 内置 `sentiment_analyzer.py` 与多种情感分析模型（`SentimentAnalysisModel/` 下有 BERT 中文 LoRA、GPT-2 LoRA、小参数 Qwen3、传统机器学习四类）。
- **Report Engine** 用"模板选择 → 文档布局 → 篇幅规划 → 章节生成"四节点 + **Document IR** + 渲染器（HTML / PDF / Markdown），有 `regenerate_latest_*.py` 重装订工具。
- **ForumEngine** 含 `monitor.py`（日志监控与论坛管理）与 `llm_host.py`（主持人 LLM）。
- 支持 Docker Compose 一键部署（PostgreSQL，也支持 MySQL）与源码启动；LLM 全部走 **OpenAI 兼容接口**，可换任意提供商。
- 提供三个 Agent 各自的 Streamlit 独立应用（`SingleEngineApp/`）与命令行报告工具 `report_engine_only.py`。

## 使用前需要注意

- **GPL-2.0（强传染）**：若把本项目代码并入自有产品，需评估 GPL 的传染范围。
- **README 有明确的免责声明**：仅供学习、学术研究与教育；**严禁商用**；爬虫须遵守目标站 `robots.txt` 与法律法规，法律后果由使用者自担。这与 GPL-2.0 的"可商用"是两回事，**以 README 声明为准**。
- **爬虫合规风险**：读 README 与目录即可见其内置社媒爬取能力，**实际采集行为涉及目标平台条款与数据合规**，本站只记录其存在，不提供使用建议。
- **依赖与配额**：需要数据库、Playwright 浏览器驱动、多个 LLM API Key；一次完整分析会大量消耗 LLM 调用。
- 我**未 clone、未部署、未运行、未采集任何数据**：以上为 README 与目录树信息。

## 什么时候会再看

- 想研究"**多 Agent 通过论坛机制辩论协作**"（而非简单串行/并行）的工程实现时。
- 需要一套可自托管、可换模型的"舆情采集 → 多模态分析 → 报告生成"完整链路时。
- 想对照"**分析（BettaFish）→ 预测（MiroFish）**"如何由同一作者串成一条链路时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同作者的姊妹项目**：[MiroFish：群体智能预测引擎](MiroFish-群体智能预测引擎.md)——BettaFish 完成"分析"（产出报告），MiroFish 在此之上做"预测"（仿真推演）；README 的"下一步开发计划"直接指向 MiroFish，其演示视频也用 BettaFish 生成的"武大舆情报告"作输入。
- **同为中文 AI 应用向项目**：[NamiAgent](NamiAgent-从零手写Agent的中文可运行教程.md)（从零手写 Agent）、[minimind](minimind-从零训练64M小LLM的中文项目.md)（从零训练小 LLM）——三者都强调"从 0 实现 / 不依赖框架"，可对照"多 Agent 系统""Agent 骨架""小模型"三种"从零"的边界。
- **数据采集相关**：[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md)（把网站转成 LLM 可用数据）——可对照"通用抓取 API"与"面向特定社媒的专用爬虫子系统"的差别。

## 参考来源

- 项目仓库与 README（中文）：<https://github.com/666ghj/BettaFish>
- 示例报告：`final_reports/final_report__20250827_131630.html`（README 给出）
- 常见问题：<https://github.com/666ghj/BettaFish/issues/700>
- 元数据（Star / Fork / Release / 贡献者 / topics）：ungh.cc 镜像、GitHub 页面内嵌 JSON 与 shields.io，2026-11-05

以上信息核验于 2026-11-05。本次 GitHub API 匿名配额已用尽，未能取到 `size`（仓库体积）；仅阅读 README（796 行）与目录树，未 clone、未部署、未运行、未采集任何数据。文中 Star / 功能均为作者自述与元数据，未经实际验证。
