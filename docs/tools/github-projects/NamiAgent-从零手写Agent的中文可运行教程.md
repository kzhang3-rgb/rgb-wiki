# NamiAgent：从零手写 Agent 的中文可运行教程

> 记录一份把"Agent 就是一个 while 循环"拆到能跑的中文教程：**11 课可运行快照 + 18 篇文章 + 一个不装 Web 框架的本地网页端**。第一句先记清三件事：**它不只是教程，本身就是一个能在本地执行命令、读写文件的 Agent**；**代码 MIT、`docs/` 文章 CC BY-NC 4.0（非商业）**；**这次 README 的自述数字经得起核**（"49 个测试""约 100 行最小循环"实测都成立）。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README 全文、`LICENSE` / `LICENSE-docs`、目录树、提交记录与 GitHub API 元数据；通读 `lessons/lesson-01/agent.py` 全文与 `lessons/lesson-11/permissions.py` 结构；实测 `lesson-11` 测试函数数量；**未 clone、未安装依赖、未运行任何一课、未核验 18 篇文章正文、未审计权限实现的安全强度**）
- 适用范围：需要 Python 3.10+；只依赖 `openai` 一个包（测试另需 pytest），不依赖任何 Agent 框架；默认接 DeepSeek，任何 OpenAI 兼容端点改两个环境变量即可

## 核心结论

**① "教程"和"能跑的 Agent"是同一个东西。** 仓库有两条并行的产线：

- `lessons/lesson-01` ~ `lesson-11`：**每一课都是"学完这一课时的完整代码"**，可以直接运行，也可以 diff 相邻两课看清这一课加了什么；
- `docs/01-*.md` ~ `docs/18-*.md`：18 篇文章，前 11 篇与课一一对应，**后 7 篇（12–18）只有文章、没有新代码**，内容是拆解 Claude Code、Codex、Pi、Grok Bot、DeepSeek Harness、OpenClaw 与 Hermes，最后收在一张总对照表。

这个"每课一个可运行快照"的组织方式比"一个仓库讲到底、代码越滚越大"更好用：**你能随时把某一课的代码跑起来，也能量出两课之间到底多了哪几十行。**

**② 技术栈刻意做到最薄：只有一个依赖，没有框架。** `requirements.txt` 全文是 `openai>=1.0` + `pytest`。手写的部分包括：stdio MCP 客户端（第 8 课）、子 Agent（第 9 课）、计划模式（第 10 课）、长期记忆（第 11 课）。**没有 LangChain、没有 CrewAI、没有任何 Agent 框架**——所以读到"记忆"或"计划"时，看到的就是实现本身，不是框架的 API 调用。

**③ 权限机制分三道关，而且作者主动声明它的上限。** 第 3 课的设计是"**硬规则 → 风险分级 → 人工确认**"：`Risk` 枚举配一张**工具风险登记表**（未登记的工具一律按需确认处理）、`SHELL_DENY_PATTERNS` 挡明显灾难性命令、工作区路径限定、以及一项"本会话一直允许"白名单（只给了 `write_file`，**`run_shell` 不在其中——每条命令都要过目**）。

更有价值的是它写边界的方式：`SHELL_DENY_PATTERNS` 旁直接注释"**这是安全带，不是边界**"，README 的安全提醒里也明说"这套机制能挡住误操作和低成本的越界，**挡不住有意的攻击**"，第 3 课还专门留了第八节讲"**这套机制防不住什么**"。**这类"自己先把防护上限写出来"的写法可以直接照搬**——和之前记录的 crossdesk 一样，它把"我不保证什么"写进了正文，而不是留给读者踩坑。（个人判断：教学项目能做到这一步的很少见。）

**④ 自述数字这次经得起核，与本站多起"自述滞后"的案例相反。** README 说"第 11 课共 49 个测试"，实测 `lessons/lesson-11/tests/` 下 9 个测试文件的 `def test_` 函数**合计正好 49 个**；说"从 100 行的最小循环开始"，实测 `lesson-01/agent.py` 为 **113 行**（含文档字符串、注释与空行，代码自身注释也写的是"约 100 行"）。**口径诚实，没有虚报。**（此前记录的 lexfridman-cn、awesome-chinese-secondhand-slang、RUN 都出现过自述数字与实际不符，所以这一条值得单独记一笔。）

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [aa1143/NamiAgent](https://github.com/aa1143/NamiAgent) |
| 描述 | "零基础入门 AI Agent 的基础教程。不只是教程，也是一个能在本地跑起来的 Agent：读写文件、执行命令、MCP、长期记忆，自带网页端看清每一步。从 100 行最小循环开始，一课加一个能力，最后拆解 Claude Code、Codex 等主流 Agent" |
| 在线教程 | **https://namiai.tech/agent/**（图文版）；另配套 **https://namiai.tech/matrix/agent/**（"Agent 是怎么工作的"10 章逐格可视化） |
| 作者 | 署名**纳米AI**（`LICENSE` 版权行）；配套项目 [NamiGPT](https://github.com/aa1143/NamiGPT)（从零手写 GPT-2）；作者 X 账号 [@antiAIvo](https://x.com/antiAIvo)（写在 README 徽章里） |
| 许可证 | **代码 MIT**（`LICENSE`，© 2026 纳米AI）；**`docs/` 下的文章 CC BY-NC 4.0**（`LICENSE-docs`）——**署名 + 非商业** |
| 主语言 | Python（需 3.10+）；网页端只用标准库，无 Web 框架 |
| 关注度 | **305 Star、31 Fork、5 关注、0 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-10-30 创建（核验时仅 5 天）**；**单分支 `main`**；20 次提交**全部集中在创建当天 3 小时内**（10:54–13:41），此后 5 天无新提交；`pushed_at` 2026-10-30；**`updated_at` 2026-11-04 是被 star 之类活动刷新的，不代表代码更新** |
| 体积 | 约 2.5 MB（API `size` 2,554 KB；其中 6 张图约 590 KB，`docs/` 文章约 290 KB，`lessons/` 代码约 480 KB） |
| 话题标签 | `agent`、`ai-agent`、`chinese`、`claude-code`、`deepseek`、`from-scratch`、`llm`、`mcp`、`python`、`tutorial` |
| Release | **无**；CI 为 `.github/workflows/test.yml`（badge 挂在 README 顶部） |

> 关于关注度：**305 Star 但订阅者只有 5**，且**没有任何开放 Issue**。这是个"发布即高分"的形态——内容集中在一两天内成型，还没有经过真实使用者的反馈沉淀。核验时的 star 数不宜当作"被验证过的成熟度"。

## 它能做什么

**课程结构（README 三部分）**

| 部分 | 课 | 每课新增 |
| --- | --- | --- |
| 一、手写核心 | 01 最小循环 | `agent.py`：三个工具 + 一个 while 循环 |
| | 02 工具系统 | `tools.py`：注册表、统一执行器 |
| | 03 权限 | `permissions.py`：硬规则 → 风险分级 → 人工确认 |
| | 04 上下文管理 | `context.py`：截断 → 清理 → 摘要，会话持久化 |
| | 05 Skills | `skills.py`：`AGENTS.md` 常驻，技能按需加载 |
| | 06 接入聊天软件 | `telegram.py`、`bot.py`：常驻、聊天确认、白名单 |
| | 07 定时任务与心跳 | `scheduler.py`：无人值守的预授权、心跳 |
| 二、补上主流能力 | 08 MCP | `mcp_client.py`：stdio MCP 客户端 |
| | 09 子 Agent 与多 Agent | `subagent.py`：独立上下文，并行只读 |
| | 10 计划 | `planning.py`：待办清单、由代码强制只读的计划模式 |
| | 11 记忆 | `memory.py`：长期记忆（每条确认）、会话检索 |
| 三、拆解主流 Agent | 12–18 | **只有文章，没有新代码**：Claude Code / Codex / Pi / Grok Bot / DeepSeek Harness / OpenClaw 与 Hermes / 总对照表 |

**第 1 课的完整形态（`lesson-01/agent.py`，113 行）**

最小循环里已经包含了后面所有课的种子，值得单独记下来：

- **三个工具就是三个普通 Python 函数**：`read_file` / `write_file` / `run_shell`，用一张字典 `TOOL_FUNCS` 按名字分发；
- **工具说明用一个小工厂函数 `_tool()` 生成** JSON Schema，避免手写重复结构；
- **循环用 `for step in range(1, MAX_STEPS + 1)` 而不是 `while True`**，`MAX_STEPS = 20` 直接给死循环兜底；
- **`run_shell` 里已经埋了权限**：每条命令 `input()` 人工确认（y/n），这是第 3 课"三道关"的起点；
- **工具抛异常也喂回模型**（`result = f"工具执行出错：{type(e).__name__}: {e}"`），让模型自己修正；
- **命令输出截断到 4000 字符**，注释写明是为了"避免撑爆上下文"——第 4 课上下文管理的引子；
- **终止条件是"模型没有返回 tool_calls"**，即模型自己认为任务完成。

**网页端：在浏览器里看上下文怎么长**

```bash
python web/app.py --demo   # 演示模式：脚本化的假模型，不需要 API Key
python web/app.py          # 用真实模型，读 API_KEY / BASE_URL / MODEL
```

打开 `http://localhost:8765` 后，**左边是每一步的思考与工具调用，右边是这一次发给模型的完整上下文**（每一块是谁写的、大概占多少 token）。macOS 还能双击 `web/start.command` 自动建虚拟环境、装依赖、粘贴密钥（直接回车进演示模式）。

README 声明：网页端**直接导入 `lessons/lesson-11` 的 Agent，没有改课程代码**，只换了三个接口——模型客户端（把发出去的上下文推给浏览器）、`handle_call`（把工具调用推给浏览器）、权限确认的 `ask`（把终端 `input()` 换成弹窗）。**这是很干净的"教学代码复用"示范**（我未核对 `web/app.py` 源码验证这一点，仅记录作者声明）。另外它写明界面里展示的"思考"只是模型写出来的文字，**不等于模型内部真实的计算过程**。

**测试：不花钱、不联网**

第 3 课起每课都有测试，**第 11 课共 49 个（实测相符）**，用 `tests/fake.py` 里的假模型跑，**不需要 API Key**：

```bash
cd lessons/lesson-11 && pytest -q
```

**环境变量（README 表格）**

| 变量 | 作用 | 默认值 |
| --- | --- | --- |
| `API_KEY` | 模型密钥 | 必填 |
| `BASE_URL` | OpenAI 兼容接口地址 | `https://api.deepseek.com` |
| `MODEL` | 模型名 | `deepseek-flash` |
| `NAMI_WORKSPACE` | Agent 的工作区（第 3 课起） | 启动时所在目录 |
| `NAMI_STATE_DIR` | 会话记录、定时任务等状态（第 4 课起），放在工作区之外 | `~/.nami-agent` |

> `MODEL` 默认值是 `deepseek-flash`，提交记录里有一条"默认模型改为 deepseek-flash：**deepseek-chat 已于 2026-07-24 下线**"——作者跟进了上游模型的变更。

## 使用前需要注意

- **它会真的动你的文件系统和命令行。** 作者在 README 里专门写了安全提醒：这是教学代码，"Agent 会真的执行命令、读写文件"，第 3 课的权限机制"能挡住误操作和低成本的越界，**挡不住有意的攻击**"；并且要求"**请在专门的工作区目录里运行，不要把工作区指向你的主目录**"。如果要试，先按此设置 `NAMI_WORKSPACE`。
- **`docs/` 文章是 CC BY-NC 4.0，不得用于商业目的。** 代码是 MIT（宽松），但**文章部分的 NC 限制比 RUN 的 CC BY-SA 4.0 更强**（那份只要求署名 + 相同方式共享，这份直接禁止商业使用）。若要搬运文章，两者不能按同一套规则处理。
- **第三部分是"截至 2026 年 10 月的快照"。** 作者自己注明"这些产品更新很快，细节请以各自官方文档为准"——拆解类内容天然会过期，不要当作当前结论引用。
- **仓库极新且无社区沉淀。** 2026-10-30 创建，20 次提交全在当天三小时内完成，五天来无新提交、**0 开放 Issue、无 Release**。这意味着：没有第三方验证、没有已知问题的公开讨论、课程后续是否继续更新还不确定。**关注它可以，但别把它当"久经检验"的资料。**
- **第 6 课起的 Telegram bot 会引入外部账号。** 需要 `TG_BOT_TOKEN` 与 `TG_ALLOWED_USERS`（白名单），等于把 Agent 接到一个公网可达的入口上，试的时候要按第 6 课说明配好白名单。
- **"教程带动站点与账号"的结构。** README 的调查入口指向作者自己的图文站 `namiai.tech`、配套项目 NamiGPT 与 X 账号。（个人判断：**未见赞助商区块、未见返利或推广链接**，与之前记录的 Claude-Guardian、social-auto-upload 那类带商业推广位的仓库不同；但"教程 → 站点/账号"的导流关系是存在的，记录时留个底。）
- **本笔记的核验边界**：只读了 README、两个许可文件、目录树、`lesson-01/agent.py` 全文与 `permissions.py` 的结构清单，**没有运行任何一课，也没有评估 18 篇文章的内容质量与代码的安全强度**。

## 什么时候会再看

- **想自己动手写一遍 Agent 的最小骨架**（循环、工具、权限、上下文）时——按课顺序跑一遍，比读框架文档快；
- **需要给团队或自己讲清楚"Agent 里到底有什么"**时——三部分结构可以直接当大纲，尤其是第三部分的拆解对照；
- **想抄"主动声明防护上限"的写法**时——`SHELL_DENY_PATTERNS` 的注释与"防不住什么"那一节是现成模板；
- **要评估某个 Agent 产品的设计**时——手写过一遍权限/上下文/记忆之后，再看任何产品都会先问"它在上下文里做了什么"；
- **想参考教程的工程化组织**时——"每课一个可运行快照 + 逐课 diff + 假模型测试"这套做法可以直接搬到别的教程项目。

## 相关笔记

- **权限分级与授权规范的写法对照**：[Claude-Guardian：Claude 账号防封与环境准备 Skill](Claude-Guardian-Claude账号防封与环境准备Skill.md)——那是给 Agent 用的 A/B/C 三档分级授权，本篇第 3 课是"硬规则 → 风险分级 → 人工确认"三道关；一个偏"操作规范"，一个偏"代码实现"，合看能拼出"给 Agent 授权该怎么分层"。
- **第 16 课的拆解对象**：[deepseek-harness：DeepSeek 开源 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)——本篇第三部分专门拆了它；先用框架看"插件化 Agent 长什么样"，再回来看手写版会更容易对上。
- **MCP 主题的两条路线**：[playwright-mcp：给 LLM 用的浏览器自动化 MCP 服务器](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md)——那是直接可用的现成 MCP 服务器，本篇第 8 课是**手写一个 stdio MCP 客户端**；一个"用现成的"，一个"看清楚协议怎么连"。
- **同为"中文 + 从零 + 可运行"的教学仓库**：[minimind：2 小时从零训练一个 64M 小 LLM 的中文项目](minimind-从零训练64M小LLM的中文项目.md)——那篇是"从零训练一个模型"（Tokenizer→Pretrain→SFT→LoRA→RLHF/RLAIF→Agentic RL），本篇是"从零手写一个 Agent"（循环 / 工具 / 权限 / 记忆）；两者都不依赖框架封装、都强调每步可运行，可对照"训练模型"与"驱动模型"两件事怎么教。
- **"成熟产品"一侧的对照样本**：[opencode：开源终端编码 Agent](opencode-开源终端编码Agent.md)——终端优先的开源编码 Agent，可与本教程手写的骨架对照"产品化之后多了什么"。
- **同为 Datawhale 系中文可运行教程，"驱动模型"之后怎么"训得更好"**：[agentic-rl-lab：用 PyTRIO 复现 LLM / Agent 强化学习算法的中文实验手册](agentic-rl-lab-复现LLMAgent的强化学习算法实验手册.md)——本篇教"从零手写 Agent"（循环 / 工具 / 权限 / 记忆），那篇教"用 Agentic RL 把模型训得更会做事"（GRPO / DAPO / GSPO / Search-R1 / ReTool…）。一个管"怎么驱动"，一个管"怎么训练"。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/aa1143/NamiAgent
- 文章版权说明：https://github.com/aa1143/NamiAgent/blob/main/LICENSE-docs（CC BY-NC 4.0）
- 图文教程站：https://namiai.tech/agent/
- Agent 逐格可视化：https://namiai.tech/matrix/agent/
- 配套项目（从零手写 GPT-2）：https://github.com/aa1143/NamiGPT

以上信息核验于 2026-11-04，Star 数、提交活跃度与课程内容都可能变化。未 clone、未运行、未核验文章正文与权限实现的安全强度。
