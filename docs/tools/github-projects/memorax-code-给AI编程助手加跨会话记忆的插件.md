# memorax-code：给 AI 编程助手加一层跨会话记忆的插件

> 一个装在本机、但**记忆放在厂商云端**的插件：它把"工程经验、仓库知识、你的工作习惯"抽成四类记忆，再注入 Codex / Claude Code / Cursor / Trae 等九种编码助手。这篇记录它的分层与客户端接入方式，并重点标注**"插件是 MIT、云端不开放"的两段式结构**。**未安装、未注册、未连接其云端，所有功能描述均为 README 自述。**

- 状态：草稿
- 最近核验：2026-11-16（阅读 README 全文、LICENSE、仓库文件树、Release 列表、提交记录、shields 徽章；**未安装、未注册账号、未连接 MemoraX 云端、未验证任何记忆效果**）
- 适用范围：Node.js 20+（推荐 24 LTS；DeepSeek Harness 需 `^22.19.0 || >=24.0.0`）；支持 Codex、Claude Code、CodeBuddy CLI、WorkBuddy、DeepSeek Harness、OpenCode、Trae、Cursor、Qoder 九种客户端。**记忆检索与写回需要联网访问 `platform.memoraxai.com`，并需 MemoraX 账号（否则只有 90 天 Guest 模式）。**

## 核心结论

**一句话定位（README 自述）**：面向 AI 编码的"记忆插件"——"把工程经验、仓库知识和你做事的方式，变成在未来任务里仍然有用的记忆"，目标是"不丢上下文、不用从头再来"。

**三个最值得记的点：**

1. **它是"本地插件 + 闭源云服务"的两段式结构**：仓库本身是 **MIT** 的 npm 包（`@memorax/memorax-code`），装在本机、负责对接各种编码助手；但 README 明写 "**MemoraX is required for cloud-backed memory**"——**真正的记忆检索与存储由厂商闭源云（`platform.memoraxai.com`）承担**。也就是说：**开源的是"连接器"，不是"记忆大脑"**。不看账号只有 90 天 Guest 模式（Guest 配额提醒还会显示完整 Mark ID）。
2. **记忆被切成四类边界**：Coding Memory（工程教训：验证过的修法、失败的尝试、设计理由、坑、回归检查）/ Repo Memory（仓库知识：架构图、模块归属、入口、commit/PR/issue 证据）/ Personal Memory（沟通偏好：语言、语气、解释深度、结果格式）/ Procedure Memory（某类任务的步骤、清单、前置条件、验收门槛）。README 强调"目标不是记住一切，而是取回与当前任务相关的那一点点"。
3. **数据边界写得很细，但要读懂**：主动检索会把 query 或选中内容发往 MemoraX；**自动写回会把选中的用户指令 + 匹配的 Agent 最终回复上传**（先去掉最终答案里的记忆影响披露），再抽取入库；本地**默认开启 trace 捕获**，trace 可能含 prompt、回复、召回的记忆、提醒文本与本地路径，可切到"仅元数据"或关闭。README 另注明云端不接收模型厂商凭证与本地 Backend token。

**个人判断（未验证）**：从产品设计看，"四类记忆 + 每类不同注入节奏 + 默认后台写回"是比"接个向量库"完整得多的方案，值得参照；但**它是重度依赖厂商云的闭源记忆服务，插件 MIT 不改变这一点**——是否愿意让编码过程中的指令与回复被抽取上传，是采用前的第一道判断题。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [memorax-ai/memorax-code](https://github.com/memorax-ai/memorax-code) |
| 描述 | "A memory plugin for AI coding that turns engineering experience, repository knowledge, and your way of working into memory that remains useful in future tasks." |
| 归属 | 组织 `memorax-ai`；npm 包 `@memorax/memorax-code`，官网 `code.memorax.net`，平台 `platform.memoraxai.com` |
| 许可证 | **MIT**（`LICENSE`，1,067 字节，`Copyright (c) 2026 MemoraX AI`）——**仅覆盖本仓库的本地插件代码；云端记忆服务不在此授权内** |
| 主语言 | JavaScript 85.3%（shields 语言徽章） |
| 关注度 | **2,105 Star / 71 Fork / 29 Watcher**（ungh.cc，2026-11-16）；shields 同期显示 **2.1k Star / 72 Fork / 9 开放 Issue / 11 贡献者** |
| 维护状态 | **2026-08-01 创建**（核验时约 3.5 个月）；pushed **2026-11-12**（`commits/main.atom` 最新为 `Merge pull request #273` "fix:(codex) narrow plugin list and report untrusted Hooks after setup"）；`updatedAt` 2026-11-13 |
| 默认分支 | `main` |
| 版本 | **19 个 Release**（页面上限 30 条，未分页）；最新 **v0.1.23**（2026-11-12），另有 `preview-*` 预发布通道（如 `preview-v0.1.22`） |
| 体积 | `main` 文件树共 **809 个文件**（ungh.cc files API；根目录含 `AGENTS.md`、`ARCHITECTURE.md`、`CLAUDE.md`、`README.md` + `README.zh.md`、`SECURITY.md`、`CHANGELOG.md` 等） |

## 四类记忆边界

| 记忆 | 回答的问题 | README 举例 |
| --- | --- | --- |
| **Coding Memory** | 哪些工程教训要带进下一个任务？ | 验证过的修法、失败的尝试、设计理由、坑、回归检查 |
| **Repo Memory** | Agent 该知道这个仓库的什么？ | 架构图、模块归属、入口点、commit / PR / MR / issue 证据 |
| **Personal Memory** | Agent 该怎么跟你沟通协作？ | 用户画像偏好：语言、语气、解释深度、结果格式 |
| **Procedure Memory** | 这类任务该怎么做？ | 可复用步骤、清单、前置条件、例外、验收门槛 |

个人记忆与流程记忆是**用户级全局**（`$MEMORAX_CODE_HOME/personal-memory/`，默认 `~/.memorax-code/personal-memory/`）：用户画像在 `user-profile/preferences.md`，每个流程主题各一个文件。README 明写"仓库里不存在个人记忆层"，且旧的 `.repo_memory` 个人记忆文件**会被忽略、不迁移**。

## 安装与激活（README 路径摘要）

1. **装包**：`npm install -g @memorax/memorax-code`（安装本身不启动交互式 setup）。README 提醒**不要加 `--ignore-scripts`**，因为 npm 生命周期脚本负责安全地停掉/恢复正在运行的后端。
2. **连账号（推荐）**：在平台注册后，于正常的交互式终端执行 `memorax-code setup --existing-account`，在本机输入用户名与 API key。无交互终端时经 stdin 传入（`printf '%s\n' "$MEMORAX_SETUP_API_KEY" | memorax-code setup --existing-account --non-interactive`），README 明确**不要把 key 放进命令行参数或项目文件**。
3. **或先不注册**：`memorax-code setup` 进入 **90 天 Guest 模式**，之后再绑定账号。**若要保留 Guest 记忆**，必须先 `memorax-code account --show-mark-id` 拿到 Mark ID，再在平台激活——README 注明"平台目前不支持把 Mark ID 附加到已注册账号"。
4. **激活各客户端**：setup 会自动识别已装客户端，之后需**重启或刷新**每个客户端；不同客户端动作不同（Codex 需在 Plugins 里启用适配器；**Trae 是 Skill-only，需在 设置 → Hooks → 全局 → 已配置 Hooks 手动启用一次**；Cursor / Qoder 用其原生后台 subagent）。setup 之后才装的客户端约 15 分钟内被启用。
5. **状态与验证**：`memorax-code status`（查本地后端与客户端集成）与 `memorax-cli status`（查本地记忆配置与工作区范围）**都不向 MemoraX 发测试请求**；真正验证远端连通与凭证要靠一次真实的检索或写入。

## 使用前需要注意

- **记忆能力依赖闭源云服务**：MIT 只覆盖本地插件；核心的记忆抽取、检索、存储都在 MemoraX 云端。**没有账号就只有 90 天 Guest 模式**，到期后的行为 README 未展开。
- **会上传内容**：主动检索发送 query 或选中内容；**自动写回会发送选中的用户指令与匹配的 Agent 最终回复**。README 称不发送完整本地 trace 与本地路径，**但这是厂商自述，第三方无法核实**。
- **本地默认开启 trace 捕获**：trace 可能包含 prompt、回复、召回记忆、提醒文本和本地路径，存在 `MEMORAX_CODE_HOME` 下；可切"仅元数据"或按客户端关闭。
- **配置文件含 API key**：`~/.memorax-code/config.toml` 保存凭证，README 反复提醒不要外贴；跨设备迁移要靠手动复制该文件。
- **敏感标识**：Guest 配额提醒可能显示完整 Mark ID，README 要求把它当作敏感信息处理。
- **项目较新**：创建约 3.5 个月（2026-08-01），此前无长期第三方验证；Release 迭代频繁（0.1.x 线）。
- **厂商自述**：四类记忆的实际效果、注入节奏、跨客户端一致性均来自 README，**我未安装、未注册、未连接其云端，全部未验证**。

## 什么时候会再看

- 想给编码 Agent 补"跨会话工程记忆"，需要一套分层与注入节奏的设计参考时。
- 评估"**开源连接器 + 闭源云记忆**"这类两段式产品，需要看清数据边界时。
- 需要"同一套记忆层接多种编码助手（Codex / Claude Code / Cursor / Trae…）"的接入方式示例时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"给 AI 补长期记忆"，托管方式相反**：[mnemos-mcp：让 AI 拥有自己长期记忆的 MCP 服务器](mnemos-mcp-让AI拥有自己长期记忆的MCP服务器.md)——那篇是**给 Agent 自己的记忆**、可本地 / 自托管；本篇是**给编码助手的共享记忆层**，但**记忆宿主是厂商云**。两篇都谈"遗忘 / 保留"，可对照"**自持记忆**"与"**云端托管记忆**"两种取舍。
- **同为"记忆系统"，数据边界相反**：[augur：本地优先的投资研究记忆系统](augur-本地优先的投资研究记忆系统.md)——那是**本地优先**的研究记忆；本篇则是**云端抽取 + 写回**。可对照"**本地优先**"与"**云依赖**"在记忆类工具里的分野。
- **"给 AI 的说明 / 记忆"是同一类需求的另一面**：[agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——`AGENTS.md` 是**你替 Agent 写的静态指令文件**（随仓库走、纯文本），本篇是**由 Agent 后台自动积累、需连云的动态记忆**；一个"人写、随仓库走"，一个"机器写、存云端"。

## 参考来源

- 项目仓库与 README：https://github.com/memorax-ai/memorax-code
- 项目官网：https://code.memorax.net/ ；平台与账号：https://platform.memoraxai.com/
- npm 包：https://www.npmjs.com/package/@memorax/memorax-code

以上信息核验于 2026-11-16，Star 数、Release 与仓库内容会随时间变化。未安装、未注册、未连接其云端，功能与数据边界均来自 README 自述，未经验证。
