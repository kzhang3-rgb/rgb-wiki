# reverse-skill：逆向/渗透/安全研究的技能路由包

> 一句话值得记住的取舍：它不自己实现逆向工具，而是**把"遇到 APK / 二进制 / 前端加密 / CTF / 渗透目标"这件事，路由到正确的方法论与工具链**——并且在动手前**先过一道"授权与范围"闸门**。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、仓库页与 GitHub API 元数据，未安装、未运行）
- 适用范围：给支持外部指令的编码 AI 客户端（Claude Code、Codex、Cursor、OpenCode 等）加载；**仅限合法安全研究、教育与获授权测试**

## 核心结论

reverse-skill 解决的是一个很具体的问题：**AI agent 不知道该用 jadx、apktool、Frida、IDA 还是 BurpSuite**；APK、ELF、JS、PCAP、CTF 各有各的 playbook；工具、MCP 服务器、脚本散落在不同机器上；同样的错误因为经验不复用而反复出现。

它给出的做法是"**路由 + 契约**"，而不是"再写一堆脚本"：

```
用户任务
 → RULES.md                          （全局规则）
 → MASTER-ROUTING / master-route.ps1 （PRIMARY，一次分诊）
 → case-init / scope.md              （先确认授权 + 网络画像；未就绪前不碰目标）
 → 场景 skill → 工具 / MCP / 脚本
 → timeline + Evidence→Finding→Path  （证据链）
 → report + field-journal            （报告 + 经验库）
```

两个值得单独记的设计：

1. **scope gate before ACT（先过范围闸门再动手）**：`RULES.md` 是全局路由规则，`case-init` 会先生成包含**授权与网络画像**的 `scope.md`，**在范围没确认前不让 agent 对目标执行动作**。对一个自带渗透/攻击链能力的包来说，这条闸门是必要的。
2. **路由只有一个事实来源**：`skills/config/routing.json`。分诊脚本 `master-route.ps1` 读它一次性给出 PRIMARY 方向；CI 在 **Windows + Ubuntu** 上跑回归与一致性校验，**保证"路由表改了会被测到"**。

覆盖的场景相当广（README 列了二十多类）：APK/Android、iOS/mobile、二进制（IDA、radare2、Binary Ninja）、.NET、前端 JS 与加密参数、DSL VM 反编译、HTTP 抓包重放、恶意软件/YARA、渗透工具、攻击链编排、案件复核、**CTF（一个含 42 个子技能的编排器）**、固件/IoT、补丁 diff/N-day、Pwn、EDR 绕过、API/GraphQL、供应链/SBOM、LLM/AI 安全、OLLVM 反混淆，以及图表与报告生成。

它还强调**客户端中立**：核心路由不绑定具体 AI 客户端；对 Codex 另提供可选的适配插件 `plugins/reverse-skill/`，且**不会自动注册外部 MCP 服务器**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zhaoxuya520/reverse-skill](https://github.com/zhaoxuya520/reverse-skill) |
| 描述 | 逆向工程 / 授权渗透测试 / 安全研究技能路由包；支持 Claude Code、Kiro、Cursor、Cline 等 |
| 许可证 | **主体 MIT**；但 **`CTF-Sandbox-Orchestrator/` 为 GNU GPLv3**；**Pentest Swarm AI 原项目为 AGPL-3.0**（本仓库只经 CLI/MCP 调用、不含其源码） |
| 语言 | PowerShell（另有 Python / Node / Bash / Java / Docker 相关脚本） |
| 关注度 | **27,238 Star、3,712 Fork、92 关注、26 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-05-13 创建；GitHub API 显示最后推送 **2026-08-22**；版本标注 v1.0.1（README badge） |
| 规模 | 体积约 4,021 KB（约 4 MB） |

## 用法要点

前置依赖：Java/JDK（jadx、apktool）、Node.js 22.12+（JS 工具链与 MCP）、Python 3.x（Frida 与辅助脚本）、一个代码 AI 客户端。

```bash
git clone https://github.com/zhaoxuya520/reverse-skill.git

# 按平台刷新本机工具索引（先知道自己有什么工具）
powershell -File skills/scripts/refresh-tool-index.ps1     # Windows
bash skills/scripts/refresh-tool-index.sh                  # Linux / macOS
bash kali/scripts/refresh-tool-index.sh                    # Kali
```

刷新后看 `skills/tool-index.md` 即可知道本机检测到哪些工具。README 明确**如果客户端是 AI Agent，应先读 `README_AI.md` 并严格按其指示执行**。

**改动路由或配置后必须跑的四项校验**（CI 每次 push/PR 都会跨平台跑）：

```powershell
# 1. 路由回归：hint → 期望 PRIMARY，任一不匹配即 CI 失败
powershell -File skills/scripts/test-routing.ps1
# 2. 结构一致性 + 供应链 pin 门禁（"未固定版本的自动安装"会失败）
powershell -File skills/scripts/verify-routing-coherence.ps1
# 3. 冒烟：verify + 脚本解析 + 快速路由矩阵
powershell -File skills/scripts/smoke.ps1
# 4. INDEX.md 漂移检查
powershell -File skills/scripts/extract-summaries.ps1 -Check
```

## 使用前需要注意

- **README 内部数据自相矛盾（事实，需留意）**：同一份 README 里三处口径不一致——
  - "Current status" 表写 **44 条路由规则（R0–R45）、175 个回归用例**；
  - "Key files" 表写 `routing.json` 是 **43 条规则（R0–R44）**；
  - 测试说明写的是 **173 个用例**。
  
  **这三个数字不能当成同一份数据使用**，看规模时以 `routing.json` 与 `test-routing.ps1` 的**实际内容为准**。
- **许可证是"混搭"，不是单一 MIT**：主仓库 MIT，但 **CTF 子模块是 GPLv3、Pentest Swarm 是 AGPL-3.0**。如果你打算把它的某个子模块合进自己的项目，**要按对应子模块的许可处理**，别只看根目录的 MIT。
- **合规风险最高的一类笔记**：包内含渗透测试、攻击链编排、EDR 绕过、Pwn/exploit 等能力。README 与仓库都有明确免责声明——**仅限合法安全研究、教育、CTF、以及你拥有或获明确授权的系统**；未经授权的访问、扫描、利用、破坏、取数**被明确禁止**，后果由使用者自负。**这道"授权闸门"是设计的一部分，不是摆设**。
- **热度很高且很新**：27k Star，但仓库 2026-05 才创建。**新仓库 + 高热度**需要自己判断其真实成熟度，不要只看星数。
- **供应链门禁是个正面信号**：`verify-routing-coherence.ps1` 会因"未固定版本的自动安装"直接失败，说明维护者在意依赖可复现性。
- **我只读 README、仓库页与元数据**：未安装、未运行、未用任何客户端加载；场景 skill 的实际可用性与"路由准确率"均未验证。

## 什么时候会再看

- 需要**给 AI 客户端加一套安全研究/逆向的方法论路由**时——它把"什么任务走什么 playbook"写成了机器可读的路由表。
- 想参考**"高风险能力包如何做授权闸门"**时：`scope.md` 先确认授权与网络画像、再放行动作，这个模式可搬到其他自动化场景。
- 需要参考**"路由配置 + 跨平台 CI 回归"**的工程做法时（`routing.json` 单一事实来源 + hint→期望 PRIMARY 的回归用例）。
- 需要定位**某类逆向任务该用什么工具链**时，可当索引参考（APK / ELF / JS / .NET / 固件 / CTF 等）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **给编码助手提供能力/上下文的同族**：[agents.md](agents.md-给编码助手用的AGENTS文件格式.md)、[zframes](zframes-让Agent用对话生成行情看板.md)（skill + 校验契约）、[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md)（也有 Skill/MCP 接入）。
- **许可形态对照**：[indicator](indicator-Go技术指标与回测框架.md)（AGPLv3 分版本）、[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md)（主库 AGPL + SDK MIT）。
- **形态对照（MCP vs SKILL）**：[playwright-mcp](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md)——那是 MCP 形态的浏览器自动化服务器，其 README 亲自讨论了"MCP vs CLI+SKILLS"的取舍（编码 Agent 场景下 CLI+SKILLS 更省 token），可对照"同一份能力该以 MCP 还是 SKILL 交付"。
- **同面向安全、但给人看的形态**：[the-book-of-secret-knowledge](the-book-of-secret-knowledge-信息安全与运维清单集.md)——那是安全/运维的资料索引（清单型、无代码、MIT），本篇是给 AI 的能力路由包；可对照"人查"与"模型用"两种组织方式，两者都涉及渗透类工具，使用前都需确认授权范围。
- **引入了授权分档的另一例**：[Claude-Guardian](Claude-Guardian-Claude账号防封与环境准备Skill.md)——那是给 Codex 用的 Claude 账号与环境准备 Skill，用 A/B/C 三档按风险分配授权；本篇是关于"先确认授权范围"的提醒，两者可对照"合规前置"在 Skill 里的不同落法。
- **同为面向渗透 / OSINT，形态不同**：[sherlock](sherlock-按用户名跨平台查找社媒账号.md)——那是"给人用的单点 CLI"（按用户名到 400+ 站点做存在性探测，MIT），本篇是"给 AI 的能力路由包"；两者都面向授权范围内的安全研究，使用前都要先确认授权与合法性边界。

## 参考来源

- 项目仓库与 README：https://github.com/zhaoxuya520/reverse-skill
- 项目官网 / 在线教程：https://reverse.apivix.com/ · https://reverse.apivix.com/docs/
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README、仓库页与元数据；未安装、未运行。本仓库自带"仅限合法、获授权使用"的免责声明，本笔记不构成对其任何工具的使用许可，使用前请确认授权范围与所在地法律。
