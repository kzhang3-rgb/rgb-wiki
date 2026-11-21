# Jev Ultrafast：让浏览器 Agent"选择"而不是"生成"的索引化动作空间

> 记录一个把"浏览器 Agent 该做什么"从**自由生成**改成**在有限动作空间里选择**的实验（`browser-use/jev-ultrafast`，GitHub 描述 `Fastest and cheapest web agent`，`pyproject.toml` 自述 `A browser agent that chooses instead of generating.`）：每次观测生成一张**带编号的元素表**（`[1] button …`），由 TypeSafe 的 Jev **一次请求同时给出"操作 + 目标"**（`CLICK` / `TYPE_TEXT` / `SELECT` / `SCROLL_*` / `WAIT` / `DONE` / `BLOCKED`），**只有需要输入文本时才调用一个小 LLM**；样例任务是 Google Flights 苏黎世→伦敦，README 自称 **7.1 秒**完成。**最需要先知道的一点是：这个仓库已被并入主项目**——README 顶部注明"**Jev Ultrafast 现在就是 [Browser Use](browser-use-给AI用的浏览器自动化Python库.md) 里的 `ultrafast` 模式**"，本仓库**保留为最初的 Browser Use × TypeSafe 实验**。**未 clone、未 `uv sync`、未运行 demo、未配任何 API Key、未审计源码。**

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（149 行）、`AGENTS.md`、`pyproject.toml`、`.env.example`、`LICENSE`（MIT，1,068 B）、`docs/performance.md`、完整文件清单（40 项）、ungh.cc 镜像元数据与 shields.io 徽章；**未 clone、未安装、未运行、未配 Key、未在任何站点上跑过、未审计源码**）
- 适用范围：想研究"**如何把浏览器 Agent 的动作空间约束成可枚举的选项**"的 Agent / 自动化作者；想读一份**极小、可通读**的浏览器 Agent 实现（`jev_ultrafast/` 共 11 个文件）的读者。运行需 **Python 3.12+ 与 uv**；**依赖两个外部服务**（TypeSafe 决策 API + 一个文本 LLM）

## 核心结论

**① ⚠️ 先看清：这是一个"已被吸收"的实验仓库。** README 顶部 NOTE 明写："**Jev Ultrafast is now ultrafast mode in Browser Use.** Same idea, built in"——主项目里用 `pip install browser-use` 后 `Agent(task, mode='ultrafast')` 即可（在 `bu-fast` / `bu-ultrafast` 模型上已是默认），云端对应 Browser Use Cloud 的 `bu-ultrafast` 模型。**"This repo stays up as the original Browser Use × TypeSafe experiment."** 因此本笔记的定位是**参考实现 / 历史资料**，而不是"该用哪个生产工具"的答案。

**② ⭐ 核心思路："选择"而非"生成"。** 每次观测产出**一张索引化的元素表**：

```text
[1] button    Change ticket type · Round trip
[2] combobox  Where from?        · San Francisco
[3] combobox  Where to?          · empty
```

动作只有 `CLICK`、`TYPE_TEXT`、`SELECT`、`SCROLL_UP`、`SCROLL_DOWN`、`WAIT`、`DONE`、`BLOCKED`，且**只提供当前受支持的操作与目标**。模型不再自由生成选择器或坐标，而是在"已观测到的元素 + 已支持的操作"里**做选择**。

**③ ⭐ "两次决策、一次网络往返"。** 一次 TypeSafe 请求同时返回 **operation** 与**该操作对应的 target**（`click_target` / `type_text_target` / `select_target`），目标是**推测式的**（若操作是 `CLICK`，只有 `click_target` 会被执行）；每个 target head **只含与之兼容的元素**。README 强调：**策略里没有站点专用的动作脚本、也没有预置的字段字符串**，Flights 示例只给一个目标并**独立校验结果**。

**④ 文本才用 LLM。** 只有操作是 `TYPE_TEXT` 时，才把任务交给一个小 LLM 生成文字（示例配置用 OpenRouter 的 `inception/mercury-2.5`、**关闭推理**）；Gemini / GLM / DeepSeek 也可通过 **OpenAI 兼容**的文本助手接入。README 的实测里，录制运行中 **Zurich 生成用 581 ms、London 用 346 ms**，两次文本调用 OpenRouter **计费 $0.00006272**。

**⑤ 安全边界写在 README 里。** 明确一句："**Model output never becomes selectors, coordinates, shell commands, or executable JavaScript.**"——每个被执行的目标都从**已观测的节点**解析，执行器会重新检查页面新鲜度与点击遮挡；文本助手的输出必须**先解析成一个小的 JSON 对象**才允许输入。

**⑥ 自报的实测数字（未复现）。** 当前视频是**一次 7,073 ms** 的 Google Flights 运行；六次交替运行里两版**都 3/3 通过**，中位任务时间 **9.450 s → 7.092 s（-25%）**，中位"浏览器协议调用"**1,092 → 101**。README 自己声明：**这是同一任务、同一浏览器 profile 的三次重复，不是通用可靠性基准**（`docs/performance.md` 还标注 sign-test p = 0.25，"样本太少"）。另有两个 smoke check：Wikipedia 打开条目 **2.798 s**、本地酒店筛选 **1.896 s**。

**⑦ 明确的局限（README "Evidence and limits"）。** DOM 读取器只处理**常见 HTML 与 ARIA 控件**，未实现完整 accessible-name 规范；**shadow root、frame、canvas、上传、弹出新标签、嵌套滚动、任意键盘控件**均不在 MVP 内；**`DONE` 不等于成功**——仍需独立校验最终结果。

**⑧ 关注度与状态（ungh 快照 / shields，2026-11-19）。** **24,645 Star / 57 关注 / 1,889 Fork**（ungh；shields 同期 25k / 1.9k）；**57 开放 issue、贡献者 1 人**（shields）；**2026-09-16 创建**、`pushedAt` **2026-11-11**、`updatedAt` 2026-11-18；`main`；**0 Release**；**40 个文件**；**MIT**（`LICENSE` 1,068 B，`Copyright (c) 2026 Browser Use`）；shields 顶层语言 **Python 57.8%**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [browser-use/jev-ultrafast](https://github.com/browser-use/jev-ultrafast) |
| 描述（GitHub） | `Fastest and cheapest web agent` |
| 归属 | 组织 **browser-use**；README 提到协作方 **TypeSafe**（`docs.typesafe.ai`）与子项目 [browser-harness](https://github.com/browser-use/browser-harness) |
| 状态 | **已被并入 `browser-use/browser-use` 的 `ultrafast` 模式**；本仓库保留为最初的 Browser Use × TypeSafe 实验 |
| 许可证 | **MIT**（`LICENSE` 1,068 B，`Copyright (c) 2026 Browser Use`；`pyproject.toml` 亦标 `license = "MIT"`） |
| 主语言 / 运行环境 | shields 顶层 **Python 57.8%**；`pyproject.toml` 要求 **Python >= 3.12**，构建用 **hatchling**，包名 `jev-ultrafast` 版本 `0.1.0` |
| 运行时依赖 | **仅两个**：`browser-harness==0.1.13`、`httpx[http2]>=0.28,<1`（dev 组另有 pytest / ruff / pillow） |
| 关注度 | **24,645 Star / 57 关注 / 1,889 Fork**（ungh，2026-11-19；shields 同期 25k / 1.9k）；**57 开放 issue、贡献者 1**（shields） |
| 创建 / 最近推送 | **2026-09-16 创建**；`pushedAt` **2026-11-11**、`updatedAt` 2026-11-18 |
| 默认分支 / Release / 文件数 | **`main`**；**0 Release**；**40 个文件** |
| 目录结构 | `docs/`（13）· `jev_ultrafast/`（11）· `scripts/`（6）· `examples/`（2）· `tests/`（1） |
| 根目录可见文件 | `README.md`（8,780 B）、`AGENTS.md`（1,176 B，给编码 Agent 的协作约定）、`LICENSE`、`pyproject.toml`（581 B）、`uv.lock`（48,798 B）、`.env.example`（254 B）、`.gitignore` |
| 核心模块 | `agent.py`（7,880 B，loop + 文本助手交接）· `browser.py`（9,266 B，连接 / 几何 / 执行）· `model.py`（8,469 B，动态 operation/target heads）· `questions.py`（2,048 B，模型指令）· `snapshot.js`（6,490 B，原子 DOM 快照）· `demo.py`（5,062 B，本地 inspector） |
| 关键环境变量（`.env.example`） | `TYPESAFE_API_KEY`、`TYPESAFE_MODEL=jev-latest`、`TEXT_MODEL_API_KEY`、`TEXT_MODEL_BASE_URL=https://openrouter.ai/api/v1`、`TEXT_MODEL=inception/mercury-2.5`、`TEXT_MODEL_REASONING=none` |

## 它能做什么

- **一个目标驱动的浏览器 Agent**：给它一句自然语言目标，它循环执行"观测 → 选择操作 + 目标 → 执行"。
- **本地 inspector（`uv run jev`）**：打开 `http://127.0.0.1:8766`，点 **Start demo → Run automatically**，可看**编号元素、操作概率、目标概率与已执行动作**；**Choose next** 会在执行前暂停。
- **当库用**：

```python
from jev_ultrafast import Agent

with Agent("https://www.google.com/travel/flights?hl=en", "Find one-way flights ...") as agent:
    for state in agent.run():
        print(state["elapsed_ms"], state["status"])
```

- **可换任务、可换文本模型**：`examples/run.py --url ... --goal ...` 换任务；`examples/flights.py --keep-open` 做一次真实航班搜索并**校验航线 / 日期 / 结果**（**不选座、不下单**）。
- **浏览器连接**：Chrome 经 **Browser Harness** 连接（`uv sync` 会装），`uv run browser-harness --doctor` 自检。
- **可读性作为特性**：README 有一节 "Small enough to read" 逐个文件说明职责——整个策略循环刻意做小。

## 使用前需要注意

- **⚠️ 仓库已被主项目吸收**：功能现已是 `browser-use` 的 `mode='ultrafast'`；**要"用"应看 [browser-use](browser-use-给AI用的浏览器自动化Python库.md)**，本仓库的价值在**读实现**。README 也把商业版本（Browser Use Cloud 的 `bu-ultrafast`）指向了站外链接。
- **⚠️ 不是自包含的本地工具**：决策依赖 **TypeSafe API**（`TYPESAFE_API_KEY`），文本生成依赖**外部 LLM**（示例是 OpenRouter key）。**没有 Key 就跑不起来**；`TEXT_MODEL` 一行明写"credentials stay server-side"。
- **⚠️ Star 数与该仓库体量不成比例**：**24.6k Star / 1.9k Fork**，但**贡献者只有 1**、只有 **40 个文件**、**0 Release**——关注度更可能来自组织影响力与那条"7 秒订机票"的演示视频，**不等于"久经使用"**。
- **⚠️ 性能数字是自报且样本极小**：`-25%`、`1,092 → 101` 等均来自 `docs/performance.md`，README **自己限定**"三次重复一个任务、一个 profile、非通用基准"（p = 0.25）；本笔记**未复现任何数字**。
- **⚠️ 能力边界明确**：不支持 shadow root / frame / canvas / 上传 / 新标签 / 嵌套滚动 / 任意键盘控件；**`DONE` 需独立校验**（README 反复强调）。
- **⚠️ 需要 Chrome 远程调试**：所谓"浏览器连接"要在 Chrome 里**允许远程调试**——这与[其它](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md) [浏览器](moli-为AI-Agent打造的Rust无头浏览器.md) Agent 方案共享同一片权限雷区：**用哪个 profile、给了多少权限，要自己想清楚**。
- **⭐ 值得单独记的一点（对写 Agent 的人）**：`AGENTS.md` 把"护栏"写成了给编码 Agent 的硬约束——"**目标必须映射到已观测元素与受支持操作、绝不让模型输出 selector 或可执行代码**"、"**永不重试浏览器写操作**"、"**执行先记日志再观测结果**"、"**`DONE` 不是成功的证据**"、"**除非用户要求否则不 commit / push**"。**这是把安全与诚实约束前置到仓库约定里的一个可抄样本。**
- **我只读了文档与元数据**：**未 clone、未 `uv sync`、未运行 demo、未配 Key、未在任何站点上跑过、未审计源码**；上述机制、性能与边界均来自 README / `docs/performance.md` / `AGENTS.md` / `pyproject.toml`，**未逐一验证**。

## 什么时候会再看

- 想研究"**把浏览器 Agent 的动作空间约束成有限可枚举选项**"（索引化元素表 + 动态 operation/target heads + 一次往返出两个决策）时；
- 想读一份**小而全**的浏览器 Agent 实现（6 个核心文件、循环清晰、附 inspector）时；
- 想参考"**把 Agent 安全护栏写成仓库约定**"（`AGENTS.md`：不生成 selector、不重试写操作、`DONE` 非证据）时；
- 想了解"**一个 Agent 实验如何被主项目吸收**"（从独立仓库 → `browser-use` 的 `ultrafast` 模式）这条路径时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **它就是主项目的分支成果——真正"用"要看这一篇**：[browser-use：给 AI 用的浏览器自动化 Python 库](browser-use-给AI用的浏览器自动化Python库.md)——那篇的主库里 `mode='ultrafast'`（在 `bu-fast` / `bu-ultrafast` 模型上默认开启）正是从本仓库吸收而来，README 顶部也把 `bu-ultrafast` 指向 Browser Use Cloud。**本仓库宜当"参考实现 / 历史资料"读，落地选型看 browser-use。**
- **同属"给 AI 用的浏览器自动化"，层次与思路都不同**：[moli：为 AI Agent 打造的 Rust 无头浏览器](moli-为AI-Agent打造的Rust无头浏览器.md)——moli 是**被驱动的独立无头浏览器内核**（执行层，Rust 自建）；本篇是**跑在真实 Chrome 上的 Agent 策略循环**（Agent 层，靠 Browser Harness 连接），且把动作空间**收窄成"选择"**。可对照"**内核 / 运行时**"与"**策略 / 决策**"。

## 参考来源

- 仓库主页：<https://github.com/browser-use/jev-ultrafast>
- README（149 行）：<https://github.com/browser-use/jev-ultrafast/blob/main/README.md>
- 性能与证据：<https://github.com/browser-use/jev-ultrafast/blob/main/docs/performance.md>
- 设计说明：`docs/design.md`；给编码 Agent 的约定：`AGENTS.md`
- 打包与依赖：<https://github.com/browser-use/jev-ultrafast/blob/main/pyproject.toml>
- 运行时依赖：<https://github.com/browser-use/browser-harness>
- 许可证（MIT）：<https://github.com/browser-use/jev-ultrafast/blob/main/LICENSE>
- 已被吸收的主项目：<https://github.com/browser-use/browser-use>
- 核验时间：2026-11-19（ungh.cc 镜像的 repo / releases / files(main) 元数据、shields.io 徽章，以及 raw 通道的 `README.md`、`AGENTS.md`、`pyproject.toml`、`.env.example`、`LICENSE`、`docs/performance.md`）
