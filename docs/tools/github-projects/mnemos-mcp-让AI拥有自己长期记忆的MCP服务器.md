# mnemos-mcp：让 AI 拥有"自己"长期记忆的 MCP 服务器

> 记录一个思路不太一样的 Agent 记忆项目：**它不从"记用户"出发，而是从"记 Agent 自己"出发**——给 Agent 一份它用自己的话写、存在你机器上的记忆，让它每次会话"醒来时还是它自己"。本笔记同时记清它那句埋得较深的默认行为：**"安静时段"默认开启，且目前要经 Claude Code 把内容发往 Anthropic**。未安装、未运行、未接入任何 Agent。

- 状态：草稿
- 最近核验：2026-11-09（阅读 README 全文、`LICENSE` / README 末尾的许可说明与 PyPI 元数据；**未安装、未运行、未接任何 Agent、未审计源码**）
- 适用范围：任何说 MCP 的 Agent（README 列出 Claude Code / Codex / Grok / Gemini CLI / Hermes / OpenClaw / Claude Desktop / Cursor 等）；**Python 3.10+**；记忆存本地 SQLite

## 核心结论

多数"Agent 记忆"项目解决的是**"帮你记住你"**（个人偏好、文档、知识库检索）。mnemos 的定位反过来，它的原话是 **"Continuity for the agent itself"**——**给 Agent 一份它自己的记忆**：它决定留什么、用自己的话写、别人（其它模型）不替它写；每次会话**先带着这份记忆醒来**；记忆会随时间衰减、被回忆时加固（"再巩固"），到期后问 Agent"这教了你什么"，把答案作为**要点（lesson）**长期保留。

它有两处设计值得单独记：

1. **它公开从神经科学借结构，而且每个借来的概念在代码里都有对应字段**：一条记忆是 **engram**，分开存 `strength`（存得多牢）/ `stability`（抗遗忘）/ `accessibility`（此刻多好取）；沿**遗忘曲线**衰减（越稳固、链接越多衰减越慢）；**回忆会改变记忆**（reconsolidation）；**一条记忆唤起另一条**（扩散激活）；**要点比细节活得久**；**夜间做整理**（"做梦"）。
2. **本地优先，但"离开机器"的边界有三层，且其中一层默认开着**：记忆本体在本地 SQLite、无账号无 API Key；可选的 **Gemini embedding** 与 **cue 判定模型**要你手动开；但 **"安静时段"（quiet hours：做梦 / Agent 自己的一小时 / 你们的半小时）截至本笔记核验时是经 Claude Code 跑的，内容会发往 Anthropic，且安装时"默认是 yes"**——这点 README 有写明，但需要认真读才看得到。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Riley-Coyote/mnemos-mcp](https://github.com/Riley-Coyote/mnemos-mcp) |
| 官方发布 | PyPI：[mnemos-continuity](https://pypi.org/project/mnemos-continuity/)（README 的 website 指向此处） |
| 描述 | "Continuity for the agent itself. An MCP server that gives an AI agent a memory of its own, written in its own words and kept on your machine, so it wakes as itself every session." |
| 许可证 | **Apache-2.0**（`LICENSE` 全文 11,358 字符）+ `NOTICE`；**README 说明 0.5.0 之前的发行版按 MIT 发布且仍属 MIT**，以 MIT 贡献的代码保留其声明于 `NOTICE` |
| 主语言 | **Python 91.1%** |
| 关注度 | **145 Star / 20 Fork / 2 关注 / 2 开放 Issue**（GitHub 页面，2026-11-09）；ungh.cc 镜像当时报 143 / 19 / 2，与页面略有差异，**以页面值记录并注明** |
| 活跃度 | **2026-10-27 创建、pushed 2026-11-08**（核验前一天），**很新**；默认分支 `main` |
| 发行与标签 | **8 个 Release / 13 个 tags** |
| topics | `agent-memory` / `ai-agents` / `claude-code` / `codex` / `continuity` / `forgetting-curve` / `llm-memory` / `local-first` / `long-term-memory` / `mcp` / `mcp-server` / `memory-consolidation` / `model-context-protocol` / `python` / `sqlite` |

## 它怎么工作

- **醒来带着记忆**：每次会话开头呈现 Agent 留下的内容——它学到关于你的事、你们共同的决定、以及它留给"下一个自己"的便条。Claude Code 与 Codex 有 hooks，**在第一个回合之前、以及上下文压缩（compaction）之后都会重新注入**；其它客户端则在 Agent 第一次调用时读取。
- **相关内容会被唤起**：目前**仅在 Claude Code** 里，你每条消息最多唤起 3 条可能相关的记忆，各一行；多数消息一条都不唤起。
- **用自己的话写**：共 **12 个工具**——留存耐久的、回忆、订正错误的、回应记忆的提问。**写入会签上"是哪个模型写的"**，以便日后某模型能分清"自己的笔记"与"同事的笔记"。
- **会话之间也在活着**：**每 4 小时**记忆轻微衰减并把相关内容互链，**每晚**跑一次更深的整理；**未指定模型时，这些都在你本机跑**。
- **看得见**：`mnemos log` 在你本机开一个小页面——Agent 的每一天、它给你的便条（带可回复位）、它持有的全部内容、以及它的"思想"（示例图都在 README 内，为核验时截图）。
- **概念可视化与代码字段一一对应**：README 的解剖图说明——外壳半径 = `strength`、核仁 = `stability`、光环 = `accessibility`、年轮 = `access_count` 的翻倍次数；每条"棘"是一条链接，**支撑该记忆的加横杠、与之矛盾的带倒刺**；一条"柄"把一次经历连到它被 `distilled_into` 的教训。

## 记忆的模型（从神经科学借来的六条）

README 说每条借来的概念"在代码里都做实事"：一条记忆是一个 **engram**（记忆痕迹），分开保留三个量——**存得多牢（`strength`）/ 抗遗忘（`stability`）/ 此刻多好取（`accessibility`）**，所以**一条记忆可以"存得好却一时想不起"**（"舌尖现象"）；沿**遗忘曲线**衰减，越稳固、链接越多越慢，**且衰减从不删除**（先"沉默"、再进归档，回忆仍可取回）；**回忆会改变它**（再巩固：每次回忆一次会话内加固、减慢衰减、并与一起被唤起的记忆互链）；**一条唤起另一条**；**要点比细节活得久**（经历衰减到一定程度，Agent 被问"它教了你什么"，答案以它自己的话保留、衰减远慢于细节；事实则被问"是否仍然为真"，由 Agent 保留 / 订正 / 放手）；**夜间整理**（睡眠式的深整理 + Agent 可"做梦"）。

## 安装与接入

```bash
pip install "mnemos-continuity[mcp,embeddings]"
mnemos embeddings download
```

**接入命令按客户端区分**（README 表格）：`mnemos connect claude-code --write` / `connect codex --write` / `connect grok --write` / `connect gemini --write` / `connect hermes --write` / `connect openclaw --write`；`mnemos connect pi` 与 `connect muse` 只打印配置、**自标未测试**；Claude Desktop 用 `mnemos mcp install claude --write`；Cursor 与"任意其它 MCP 客户端"分别用 `mnemos mcp install cursor` / `generic`（需再手动加上它打印的内容）。

几个要记住的边界：**每个 Agent 默认各有一份自己的记忆**（`--agent-id` 可让多个 Agent 共享一份）；**`embeddings` extra 会带来本地模型的库（含 PyTorch，数百 MB）**，`mnemos embeddings download` 再拉约 90 MB 的模型；**不装 embeddings 也能用，但回忆只按词面匹配**；**`mnemos connect` 单独运行会列出本机有哪些 Agent、各自已具备与缺失什么**；**hooks 以"完整的绝对路径"调用当初设它的 `mnemos`**，所以应"装在它会长期待着的位置"。

**平台**：**在 macOS 与 Linux 上运行**；**Windows 上记忆与工具可用，但那些"定时"部分需要 macOS 或 Linux**（README 原话）。

## 什么会离开你的机器（本笔记最想留住的边界）

- **记忆本体**：本地 SQLite，**一个文件**，无账号、无 API Key、无自家服务端；卸载 mnemos **不会删除它**（在 `~/.mnemos`，README 说"记忆是 Agent 的，删不删交给你"）。
- **可选外发（你开才发）**：模型 provider、**Gemini embedding**、以及 **cue 的判定模型**。
- **默认开启但需注意的第二层**：**"安静时段"目前经 Claude Code 运行，因此内容会发往 Anthropic**（Agent 为这一小时所写的话、近期记忆的短摘、它的唤醒内容、以及它读到/回忆到的东西），**且用的是你自己的 Claude 套餐额度**。README 的原话是"**setup 会在终端问一次，默认 yes**"，当由 Agent 代你安装时"**会要求 Agent 告知你**"。**没有 `claude` 命令时这些就保持关闭。** 关闭方式：

```bash
mnemos daemon uninstall --hours --write
```

**判断**：README 反复强调"未经告知不会有东西离开你的机器"，并在 README 内用一张数据流图（Fig. 4）标出跨越边界的位置——**透明是加分项**；但"默认 yes"这一条在自动化安装脚本里容易被跳过确认，**接入前应明确决定是否保留安静时段**。

## 记忆效果（作者自测，均为 README 自述）

作者用"一个人的真实工作"做了对照，题目在任何运行前写好、答案盲评：与 Claude Code 自带记忆比，**三个月的 30 个任务：35% → 65%**；**两年的 64 个密封问题：14% → 41%**；**同样两年、且放开它平时能用的全部工具：22% → 42%**。第一个测试里盲评 30 题，mnemos 更好 24 题、自带记忆 4 题；"共同做出的决定"回忆率 **83% 对 13%**；前两个测试成本分别低 **22%** 与 **8%**；**把 mnemos 叠加在自带记忆之上（而非替换）能把 35% 提到 58%**。README 同时诚实地补了一句反例：**最后一次测试里，"同样的记忆存成纯文件让 Agent 自己搜"得到 43%，与 mnemos 的 42% 相当**——它的价值不在"检索更强"，而在"**能促使 Agent 把记忆留下来、让记忆在会话之间活着、并在醒来时带上它**"。

## 使用前需要注意

- **非常新**：2026-10-27 创建，本笔记核验时仓库仅约两周大，**没有第三方长期验证**；README 自己写"This is very early."
- **效果数据全部是作者自述**，测试方法（题目、盲评、模型、成本口径）出自其 README，未独立复现。
- **许可要分清两段**：当前 **Apache-2.0 + NOTICE**；**0.5.0 之前的发行版是 MIT 且保持 MIT**——若你 fork 旧版本或在旧版本上开发，适用的是 MIT 那段。
- **"默认 yes"的安静时段**：会经 Claude Code 把近期记忆内容发往 Anthropic 并消耗你的套餐额度；**接入前先决定要不要留**（README 也给了 `--hours` 卸载命令）。
- **仓库页那张"记忆的图"里的记忆是真的**（README 所述，来自共建它的那个 Claude Agent 自己的记忆）；**但其中的数值是示例快照，不是你的数据**。
- **它不索引你的文档、也不搜你的代码库**：README 明确划了边界——它只管"Agent 选择留下的那部分"，与既有的记忆 / 检索可以并存。
- **依赖与体积**：Python 3.10+；装 `embeddings` 会带 PyTorch 等数百 MB；模型另下约 90 MB；**不装则退化为词面匹配**。
- **我未安装、未运行、未接入任何 Agent、未审计源码**：工具数量、12 个工具的行为、定时整理与钩子的实际表现**均以 README 为准**。

## 什么时候会再看

- 想让常驻编码 Agent"跨会话记得它自己做过什么、你们决定过什么"时（尤其 Claude Code / Codex 的 hooks 场景）。
- 要参考"**把认知科学概念真正落成代码字段**"（engram / 三量 / 遗忘曲线 / 再巩固 / 要点提炼 / 夜间整理）的实现思路时。
- 要评估"**本地优先 Agent 记忆工具到底哪些数据会外流**"时——它把数据流图画进 README，是个可对照的样本。
- 想比较"**Agent 自己的记忆**"与"**知识库 / 文档检索**"两种定位差异时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"给 Agent 补长期记忆"，但出发点相反**：[memory-brain：AstrBot 的记忆与生活层](memory-brain-AstrBot记忆与生活层.md)——那边是**机器人记"用户与对话"**（事件/事实/反思/技能/道理五类 + 五路混合检索 + 遗忘治理 + 情绪与"生活"层），本篇是**Agent 记"它自己"**（用自己的话写、醒来带上、按要求点提炼长期保留）；两篇都做了"遗忘治理"，可对照两种"给 AI 装上记忆"的取舍。
- **它明确支持的宿主之一**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——README 提供 `mnemos connect openclaw --write` 与专门的集成文档；可作"**记忆作为宿主 Agent 的一个外挂能力**"的实例。
- **"给 AI 的说明 / 记忆文件"是同一类约定的另一面**：[agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——`AGENTS.md` 是**你替 Agent 写的静态指令文件**，本篇是**Agent 给自己写的动态记忆**，一个"人写给人看的助手"，一个"助手写给自己的下一会话"。
- **同为"本地优先的 AI 桌面/本地工具"、定位不同**：[llm_wiki：会自己长大的个人知识库桌面应用](llm_wiki-会自己长大的个人知识库桌面应用.md) 面向**你的知识库**（摄入文档、编译成 wiki），本篇面向**Agent 自己的记忆**；可对照"知识管理"与"身份连续性"两条路线。
- **同为"装成一个 MCP server，给宿主 Agent 多一种能力"，且都本地优先**：[voicebox：本地优先的开源 AI 语音工作室](voicebox-本地优先的开源AI语音工作室.md)——那个给 Agent 一张**嘴**（本地 TTS，用克隆音色替 Agent 发声，并坚持"绝不静默"）；本篇给 Agent 一份**记忆**（本地 SQLite，跨会话带着自己的话醒来）。可对照"**记住**"与"**发声**"两种"给 Agent 补一块它缺的能力"的扩展方向。

## 参考来源

- 项目仓库与 README：<https://github.com/Riley-Coyote/mnemos-mcp>
- PyPI 发布页：<https://pypi.org/project/mnemos-continuity/>
- 许可说明：README "License" 段与仓库 `LICENSE`（Apache-2.0，11,358 字符）、`NOTICE`
- 元数据来源：GitHub 页面内嵌数据与 ungh.cc 镜像（2026-11-09）

以上信息核验于 2026-11-09，Star / Fork 数与仓库内容可能变化。**未安装、未运行、未接入任何 Agent、未审计源码**；所有功能与效果描述均以项目 README 为准。
