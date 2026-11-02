# xquant-beginner：《XQuant：人人都是量化交易员》开源书

> 一份把"量化入门"的起点从"先学编程"改成"**先学会描述和验证投资想法**"的开源书稿——用 Vibe Coding（氛围编程）的方式，让 AI 编程工具生成并运行 Python 策略代码。**两件事要先看清**：它**采用双许可**（正文 CC BY-NC-SA 4.0 = 禁止商用 + 相同方式共享，代码 MIT），且**与作者自己的商业平台（XQuant-Shop）绑定**，书稿充当导流入口。

- 状态：草稿
- 最近核验：2026-11-02（阅读在线书稿首页、README/LICENSE 与 GitHub API 元数据，**未逐章阅读书稿正文**、未运行任何代码）
- 适用范围：零基础读者的量化入门；配套环境为 AI 编程工具 + Python；书稿用 VitePress / GitBook 发布

## 核心结论

这是**一本开源书稿的源码仓库**，不是一个可运行的工具。书稿名为《XQuant：人人都是量化交易员》，主线是"**AI 编程工具 + 量化**"：读者先把投资想法写成**策略规格说明书（spec）**，再让 AI 生成并运行 Python 代码，通过数据、回测与复查判断策略是否站得住。

作者对书中定位的自述很克制："重点不是提供一个神奇策略，而是建立一条可以反复使用的研究路径：**提出想法、写清规则、跑出结果、评估风险、怀疑结论、继续迭代**。"

需要注意：**仓库里只有书稿源码、图片与发布配置**；书中配套的 spec 与 notebook **不在这里**，统一放在另一个仓库 [`xingwudao/xquant-learning`](https://github.com/xingwudao/xquant-learning)。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [xingwudao/xquant-beginner](https://github.com/xingwudao/xquant-beginner) |
| 在线书稿 | <https://xingwudao.github.io/xquant-beginner/> |
| 许可证 | **双许可**：书稿正文与图片 **CC BY-NC-SA 4.0**；构建脚本 / 配置 / 自动化工作流 **MIT**（`LICENSE` 文件明写） |
| 语言与技术栈 | TypeScript（仓库的 `language` 字段来自 **VitePress 站点构建**，不是书稿内容语言）；发布方式为 GitBook（`.gitbook.yaml` + `SUMMARY.md`）与 VitePress 静态站两套 |
| 关注度 | **870 Star、176 Fork、4 人关注、10 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-06-09 创建，最近推送 2026-09-02；元数据 `updated_at` 为 2026-11-02（说明有非代码更新，如 Star 变化） |
| 体积 | 约 10,937 KB（`size` 字段） |
| 配套练习 | [`xingwudao/xquant-learning`](https://github.com/xingwudao/xquant-learning)（spec 与 notebook，**本次未核验该仓库**） |
| 作者 | **刑无刀**（《机器学习：实用案例解析》译者、《推荐系统》作者、极客时间《推荐系统 36 式》专栏作者，自称 15 年 AI 从业、贝壳前技术总监）与 **MatrixSpk**（北大 MBA） |

> 口径说明：API 的 `license` 字段返回 `NOASSERTION`（"Other"），**真实情况是双许可**——必须读 `LICENSE` 文件才能确认。这是本 wiki 反复遇到的同一个坑（[llm_wiki](llm_wiki-会自己长大的个人知识库桌面应用.md)、[MCP servers](servers-MCP官方参考服务器合集.md)、[KHunter](KHunter-A股量化选股系统.md) 都是类似情形）。

## 书稿结构（官网 SUMMARY 自述）

- **准备工作**：AI 编程工具与 Python 环境
- **第 1 章**：跑通第一个策略
- **第 2 章**：先选买什么——从 3 只 ETF 开始
- **第 3 章**：决定各买多少——3 种分法实测
- **第 4 章**：决定何时买卖——再平衡、止损、止盈
- **第 5 章**：判断策略好不好——4 个关键视角
- **第 6 章**：别高兴太早——防止过拟合
- **第 7 章**：从理想到现实——执行交易
- **第 8 章**：策略跑起来以后——监控、诊断、迭代
- **第 9 章**：持续寻找机会——因子研究入门

官网自述的学习目标包括：把投资想法写成 spec、让 AI 生成并运行量化代码、ETF 数据获取与首次回测、基准对比与收益/风险评估、**识别过拟合 / 数据泄露 / 参数优化陷阱**、以及交易执行、监控诊断、策略迭代与因子研究。

**从章节目录看，它的重点在"方法论与陷阱"而不是"策略配方"**——把"防过拟合""数据泄露"单独成章，对入门读物来说是比较务实的取舍。

## 使用前需要注意

- **许可证是"禁止商用 + 相同方式共享"**：正文与图片为 **CC BY-NC-SA 4.0**，意味着**不能用于商业用途**，且**转载/改编需以相同许可发布并署名**。代码部分才是 MIT。**若你打算把书稿内容用于课程、付费社群或商业产品，先确认 NC 条款。**（本 wiki 收录的 [prompt-optimizer](prompt-optimizer-提示词优化器.md)、[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md) 也涉及"主库/内容与 SDK 许可不一致"的同类问题。）
- **书稿开源与商业产品绑定**：作者同时运营 **XQuant-Shop**（"面向全球个人投资者的一站式量化投资决策平台"），README 中多处导流（"最新书稿已更新至 XQuant 量化课堂页"`xquant.shop/courses`、读者群**加作者微信**）。**开源书稿同时是商业产品的入口**，阅读时留意其中可能存在的推广倾向。
- **本仓库可能滞后于官方书稿**：README 明写"最新书稿已更新至 XQuant 量化课堂页"，也就是说**站点上的商业版书稿可能比本仓库新**。想读最新版要去图书页，而不是只看这个仓库。
- **配套材料在另一个仓库**：spec 与 notebook 在 `xingwudao/xquant-learning`，**本仓库无法独立完成书中练习**；该仓库本次未核验，其许可证与维护状态需另行确认。
- **"AI 生成代码"路线本身的门槛被低估**：书中路径要求读者能判断 AI 生成代码是否正确、能读懂回测结果。README 也把"识别过拟合、数据泄露、参数优化陷阱"列为学习目标——**这些恰恰是初学者最容易漏掉的环节**，不是"让 AI 写就能跑通"。
- **风险提示（作者原文）**："本书只用于教育和研究，**不构成投资建议**。历史表现不代表未来收益，任何策略都可能亏损。"
- **我只读了在线首页与许可证**：未逐章阅读书稿正文，未运行书中任何 spec / notebook，**因此无法评价其内容质量与代码正确性**。

## 什么时候会再看

- 想找一条**"零基础 → 跑通第一个量化策略"的成体系中文路径**，且接受"先学描述与验证想法、再让 AI 写代码"这种学习顺序时。
- 想参考**"把投资想法写成 spec"**这件事怎么教时——这是它与 [ai_quant_trade](ai_quant_trade-AI量化交易操盘手.md) 这类"资源合集"最大的区别：**它给的是流程，不是清单**。
- 想对照**开源书稿的工程化做法**时：GitBook 与 VitePress **双发布**、`SUMMARY.md` 目录驱动，可与 [up](up-人生进阶指南.md)、[Foundations-of-LLMs](Foundations-of-LLMs-大模型基础教材.md) 的书稿工程化方式对比。
- 想研究**"开源内容 + 商业平台"的导流模式**及其许可证边界时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为量化入门/学习类**：[ai_quant_trade](ai_quant_trade-AI量化交易操盘手.md)（量化学习合集，覆盖面更广）、[Financial_freedom](Financial_freedom-赚钱与投资书单.md)（投资书单类）。**本篇的特点是"教流程"而非"给资源"。**
- **落地工具对照**（本笔记是"入门读物"，下面这些是"可跑的工具"）：[KHunter](KHunter-A股量化选股系统.md)、[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)、[tick-stock-panel](tick-stock-panel-自托管A股量化工作台.md)、[indicator](indicator-Go技术指标与回测框架.md)。
- **"缺失值 / 回测口径"同一主题**：[US_Stock_Crash_Monitor](US_Stock_Crash_Monitor-美股风险监测面板.md)、[Vibe-Research](Vibe-Research-A股美股港股投研工作台.md)——书里第 5、6 章讲的"判断策略好不好 / 防止过拟合"，在这两个项目里的工程化做法是"缺失如实标注 + 口径可追溯"。
- **开源书稿工程化对照**：[Foundations-of-LLMs](Foundations-of-LLMs-大模型基础教材.md)、[up](up-人生进阶指南.md)、[everyone-can-use-english](everyone-can-use-english-人人都能用英语.md)。
- **许可证"API 报 NOASSERTION 实为双许可"的同类**：[KHunter](KHunter-A股量化选股系统.md)、[llm_wiki](llm_wiki-会自己长大的个人知识库桌面应用.md)、[servers](servers-MCP官方参考服务器合集.md)。

## 参考来源

- 在线书稿首页：https://xingwudao.github.io/xquant-beginner/
- 项目仓库与 README：https://github.com/xingwudao/xquant-beginner
- 许可证文件（双许可原文）：https://github.com/xingwudao/xquant-beginner/blob/main/LICENSE
- 配套练习仓库（未核验）：https://github.com/xingwudao/xquant-learning
- 作者商业平台：https://xquant.shop/courses
- 元数据（Star / Fork / 时间 / 许可证字段 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02，Star 数与仓库内容可能变化。仅阅读在线书稿首页、README、LICENSE 与元数据；**未逐章阅读书稿正文，未运行书中代码，未核验配套练习仓库**。书稿结构、学习目标与作者信息均引自官方页面自述，未独立验证。本笔记不构成任何投资建议。
