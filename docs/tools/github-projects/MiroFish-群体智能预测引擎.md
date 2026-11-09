# MiroFish：群体智能预测引擎

> 记录一个把"多 Agent 社会仿真"做成通用预测引擎的项目：它从真实世界提取种子信息、在数字沙盒里推演未来，可与同作者的 BettaFish 串成"分析 → 预测"链路。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README（英文版）与元数据，未 clone、未部署、未运行任何仿真）
- 适用范围：多 Agent 仿真 / 群体智能 / 预测类应用；了解"用 Agent 沙盒做推演"的产品形态

## 核心结论

`666ghj/MiroFish` 自我定位为"**简洁通用的群体智能引擎，预测万物**（A Simple and Universal Swarm Intelligence Engine, Predicting Anything）"。它的思路是：从真实世界提取**种子信息**（突发新闻、政策草案、金融信号，甚至小说故事）→ 自动构建一个**高保真平行数字世界** → 世界中**数千个具有独立人格、长期记忆与行为逻辑的智能体**自由交互、社会演化 → 用户以"上帝视角"动态注入变量，**推演未来走向**。

对用户的接口被压缩成两句：

> 你只需：上传种子材料（数据分析报告或有趣的小说故事），并用自然语言描述预测需求；
> MiroFish 会返回：一份详细预测报告 + 一个可深度交互的高保真数字世界。

它的仿真引擎基于开源项目 **[OASIS (Open Agent Social Interaction Simulations)](https://github.com/camel-ai/oasis)**（CAMEL-AI 团队），并**获得盛大集团（Shanda Group）的战略支持与孵化**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [666ghj/MiroFish](https://github.com/666ghj/MiroFish) |
| 描述 | A Simple and Universal Swarm Intelligence Engine, Predicting Anything｜简洁通用的群体智能引擎，预测万物 |
| 许可证 | **AGPL-3.0（强传染，尤其注意"通过网络提供服务"也触发开源义务）** |
| 语言 | **Python 66.6%**（前后端分离，另有 TypeScript 等） |
| 关注度 | **84,109 Star、12,716 Fork、481 关注、88 个开放 Issue**（核验于 2026-11-05） |
| 维护状态 | **2025-11-26 创建**，最近推送 **2026-10-16** |
| 分支 | 默认分支 `main` |
| 发行版 | **3 个 Release / 3 个 tag**，最新 **v0.1.2**（2026-03-07） |
| 贡献者 | **16 人**（含作者 `666ghj`、`Ghostubborn`、`github-actions[bot]`、`cursoragent` 等） |
| topics | 含 `swarm-intelligence`、`multi-agent-simulation`、`knowledge-graph`、`financial-forecasting` |
| 关联项目 | 同作者的 [BettaFish](BettaFish-多Agent舆情分析助手微舆.md)（分析方向）；仿真引擎上游 OASIS |
| 其他 | 演示站 `666ghj.github.io/mirofish-demo`；社交账号 `@mirofish_ai`；团队在招募（`mirofish@shanda.com`） |

## 工作流（README 五阶段）

1. **图构建（Graph Building）**：种子提取 + 个体/集体记忆注入 + GraphRAG 构建。
2. **环境搭建（Environment Setup）**：实体关系抽取 + 人格（Persona）生成 + Agent 配置注入。
3. **仿真（Simulation）**：双平台并行仿真 + 自动解析预测需求 + 动态时序记忆更新。
4. **报告生成（Report Generation）**：ReportAgent 携带丰富工具集，与仿真后的环境深度交互生成报告。
5. **深度交互（Deep Interaction）**：可与仿真世界中任一 Agent 对话，也可与 ReportAgent 交互。

## 用法要点

- **部署方式**：源码部署（推荐）或 Docker。源码需 **Node.js 18+**、**Python ≥3.11 且 ≤3.12**、**uv** 包管理器；`npm run setup:all` 一键装依赖，`npm run dev` 同时起前端（`localhost:3000`）与后端 API（`localhost:5001`）。
- **必需的环境变量**：LLM API（任何 OpenAI SDK 兼容格式，README **推荐阿里百炼的 `qwen-plus`**）+ **Zep Cloud**（长期记忆服务，有免费月度额度）。
- **README 自己提示消耗高**：建议先用**少于 40 轮**的仿真试跑。
- **演示**：README 给出两个 B 站视频——"武大舆情仿真 + 项目介绍"（**用 BettaFish 生成的武大舆情报告作输入**）与"《红楼梦》遗失结局推演"。
- **团队化运营**：除开源仓库外，有 Discord / X / Instagram / QQ 群，并在招多 Agent 仿真与 LLM 应用方向的全职/实习。

## 使用前需要注意

- **AGPL-3.0 的传染性最强**：不仅分发触发义务，**通过网页提供修改版服务通常也需向用户开放源码**；商业化前务必评估。
- **高度依赖外部服务**：LLM API（消耗大）+ Zep Cloud 记忆服务，缺一不可；Zep 免费额度可能不足以支撑大规模仿真。
- **11 个月就到 8.4 万 Star**：Star 增速异常快，需警惕"热度 ≠ 成熟度"——核心仍处 **v0.1.x**，最新 Release 停在 2026-03-07。
- **结果性质**："预测报告"是基于仿真推演的**推演产物，不是事实预测**；用于金融/舆情决策前需自行评估可靠性。
- 我**未 clone、未部署、未运行任何仿真**：以上为 README、元数据与 Release 列表信息；Star 与功能均为作者自述。

## 什么时候会再看

- 想研究"**用多 Agent 社会仿真做预测**"的产品化形态与工作流时。
- 需要一条"**报告 → 仿真 → 推演**"的链路，想与 BettaFish 组合使用时。
- 想复盘"**Star 极高但版本极早期（v0.1.x）**"这类项目的评估方式，或了解 AGPL-3.0 对"网络服务"的约束时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同作者的姊妹项目**：[BettaFish：多 Agent 舆情分析系统"微舆"](BettaFish-多Agent舆情分析助手微舆.md)——BettaFish 做"分析"（产出舆情报告），MiroFish 在其上做"预测"（仿真推演）；两者由同一作者 `666ghj` 维护，README 互指，可串成"分析 → 预测"链路。
- **量化 / 预测向工具**：[Vibe-Research：A股美股港股投研工作台](Vibe-Research-A股美股港股投研工作台.md)、[US_Stock_Crash_Monitor：美股风险监测面板](US_Stock_Crash_Monitor-美股风险监测面板.md)——那些是"基于真实市场数据"的分析工具，本篇是"基于 Agent 仿真"的推演引擎，可对照两类"预测"在数据来源与可信度上的根本差别。
- **同为"多 Agent / 从 0 实现"**：[NamiAgent](NamiAgent-从零手写Agent的中文可运行教程.md)（从零手写 Agent 骨架）——可对照"教学型 Agent 骨架"与"产品型多 Agent 仿真"的工程规模差异。

## 参考来源

- 项目仓库与 README（英文）：<https://github.com/666ghj/MiroFish>
- 中文文档：`README-ZH.md`
- 演示站：<https://666ghj.github.io/mirofish-demo/>
- 仿真引擎上游：<https://github.com/camel-ai/oasis>
- 元数据（Star / Fork / Release / 贡献者 / topics）：ungh.cc 镜像、GitHub 页面内嵌 JSON 与 shields.io，2026-11-05

以上信息核验于 2026-11-05。本次 GitHub API 匿名配额已用尽，未能取到 `size`（仓库体积）；仅阅读 README 与元数据，未 clone、未部署、未运行。Star 与功能均为作者自述，未经实际验证。
