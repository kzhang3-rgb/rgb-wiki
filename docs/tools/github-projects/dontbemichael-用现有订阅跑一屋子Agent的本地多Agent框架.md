# dontbemichael：用现有订阅跑一屋子 Agent 的本地多 Agent 框架

> 一句话定位（README 自述）：**"An AI office for your small business"**——一个**桌面应用**，把你现有的 **Claude Code / Claude 订阅**（或 Anthropic API Key）变成**一间小型 AI 办公室**：**Michael 是办公室经理，你只跟他说**，他把活派给对应角色的团队会员，会员各有**独立文件夹、独立记忆、独立邮箱**，只把"需要你拍板的事"送到你面前。最值得记的三点：① **一切跑在你自己电脑上、走你自己的 Claude 额度**（README：「Everything runs on your computer, on the Claude plan you already have」）；② 每个团队会员是**一个真实的 Claude Code 进程**（各自终端里跑），团队通过 **hive**（一组纯文件：邮箱 / 记忆 / 任务板）协作，**只有应用会写它**；③ 这是**早期版本（early release）**——`0.3.8` 甚至**砍掉了所有者不用的功能**（语音、Webhook、Slack、git 视图、内置代码编辑器等）。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（392 行）、`LICENSE`（1,102 字符）与已核实元数据；**未 clone、未安装、未运行、未构建、未连接任何邮箱、未登录 Claude、未试用应用**）
- 适用范围：**macOS（Apple Silicon 或 Intel）/ Windows 11（x64，暂不支持 ARM）**；运行时需要**已登录 Claude 计划的 Claude Code**，或改用一个 **Anthropic API Key**；**从源码构建**另需 **Node.js ≥ 20 + npm + `node-pty` 的 C/C++ 工具链**；应用**目前仅英文**

## 核心结论

1. **它是"AI 小办公室"而不是"又一个聊天框"**（README 自述）：你只跟办公室经理 **Michael** 对话，他**派活、答疑、盯任务板**，只把**要你决策的事**放到 **Needs you** 板上；"他是唯一派活的人"——会员之间要点活也先经 Michael 流转。
2. **每个会员 = 一个真实 Claude Code 进程**（README「For developers」）：应用是 **Electron（React、TypeScript、xterm.js、node-pty）**；协作用 **hive**——一个**纯文件目录**（每个 agent 一套 mailbox / memory / task board），**只有应用会向它提交**。
3. **额度来自你自己**（README「What you need」/「Your data」）：团队会员跑在**你自己的 Claude Code 和 Claude 计划**上；README 明说 **Claude Max 计划才能整天跑**，较小计划**日间会触顶、办公室休息到重置**（定时任务仍照发、在收件箱里等）。
4. **数据留在本机、邮箱可多开**：工作、记忆、文件都在你电脑的文件夹里；能接 **Gmail / Google Workspace / iCloud / Yahoo / Zoho 或任意 IMAP（应用密码）**，每个会员只被授予**自己那一个**邮箱，且**可发 / 待批后发 / 仅草稿**三档控制。

**个人判断（未验证）**：README 的价值主张是"**业务所有者不必排队等 AI**"——用固定角色 + 邮箱 + 记忆把日常杂务（客服、财务、销售外联）批发出去。真正跑起来，**瓶颈会落在 Claude 订阅额度**（作者自己说小计划会触顶）与**多进程并发**上，而"自动化是否可靠"仍要靠 `Needs you` 人工兜底。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [agentvivekkumar/dontbemichael](https://github.com/agentvivekkumar/dontbemichael) |
| 描述（原文） | "A local multi-agent harness that works with your existing Claude Code, Codex subscriptions, allows you to run an office of agents" |
| 归属 | 个人账号 **agentvivekkumar**（版权人 Vivek Kumar 与 Chaitanya Giri） |
| 许可证 | **MIT**（`LICENSE` 1,102 字符；Copyright (c) 2026 Vivek Kumar 与 (c) 2026 Chaitanya Giri）；README 注"源码"为 MIT，且**与 NBC /《The Office》/ Dunder Mifflin 无关联** |
| 平台 | README 徽章：**macOS \| Windows 11**；Windows 侧目前**未用微软证书签名**，首次运行需"More info → Run anyway" |
| 关注度 | **Star 591 / Watcher 58 / Fork 495**（ungh 与页面一致，2026-11-13）；shields 另报**开放 Issue 62、contributors 200**（同日） |
| Release | ungh 返回 **30**（单页上限）/ 页面 **43** 条；上文提及最新可见说明为 **0.3.8**（"更轻的应用"）与 **0.3.7**（Ask me 卡片标注依据） |
| 维护状态 | **2026-09-15 创建**；ungh `pushedAt` 与页面 `updatedAt` 同为 **2026-11-10**；默认分支 **`main`**；状态徽章为 **early release** |
| 官网 | <https://dontbemichael.com> |
| 面向的助手 | **本构建只提供 Claude Code**：可用 Claude 账号登录，或改用 Anthropic API Key；代码里**仍留** Codex（ChatGPT 引擎）与 **Gemini CLI** 的预设，但**本构建不提供**，其它引擎保存的会员会**迁到 Claude Code** |

## 能力结构

### 1. 团队与角色（README「Your team」）

装配时你填**业务类型**，应用从对应 **Office Pack** 建议一组起始团队，你挑谁入职、可后续再招：

| 业务类型 | 建议团队（摘要） |
| --- | --- |
| Restaurant & Food | Finance、Executive Admin、Customer Support、Marketing、Quality Control、Supply Chain、HR Manager |
| Retail Shop | Finance、Executive Admin、Customer Support、Inventory & Shipping、Marketing、Supply Chain、Sales Director |
| Pro Services | Finance、Executive Admin、Customer Support、Sales Director、Marketing、IT Security |
| Home Services | Finance、Executive Admin、Customer Support、Sales Director、Inventory & Shipping、Supply Chain、Marketing |
| SaaS/Consulting | Finance、Executive Admin、HR Manager、Customer Support、Sales Director、Marketing、IT Engineer、IT Security |
| Something else | 从核心角色里挑 |

每个角色带一份 **Role description**（告诉 Michael 什么活派到这）与一份 **Work style**（告诉会员在**你的业务里**怎么干），两者都能在 **Edit Agent** 里改。

### 2. 邮箱、记忆与"你在掌舵"（README「A mailbox for every job」/「What the office does」）

- **每个岗位一个邮箱**：Michael 之外的会员按岗位读各自的收件箱（如 Pam 管 `ceo@`、Kelly 管 `support@`、Dwight 管 `sales@`）；发放分三档——**Can send**（直接发）、**Send on approval**（每封先上 `Needs you` 由你编辑 / 批准 / 打回）、**Draft only**（只存档草稿）。会员**只读被授予的那一个邮箱**，且**所做的任何操作都不删除邮件**。
- **同类邮件可一次授权**：当你反复原样批准同一类邮件后，会员**可提议今后免批发送**；勾选后出现在其 Access 页的 **Sends without asking**（可 Revoke），发送前另有一道**快速检查**，不符的仍会推给你。
- **记忆会自我整理**：每个会员维护一份**简短长期笔记**（你的偏好与纠正、查到的信息、东西放哪、重复步骤），后台**合并去重**，让记忆"变好而不是越堆越多"。
- **金额、删除、范围变更都到你**：会员若**循环或反复失败**，会被**引导 → 限制 → 停止**；卡在某个提示上的会员由应用**重启**而非丢给你。所有会员遵守"house rules"：只陈述**可溯源**内容、不知道就说不知道、**绝不编造**人物 / 客户 / 引语。
- **可视化办公室**：桌位随上下班点亮，任务在工位间飞信封，Michael 派活前会先"指一下"，定时任务敲他墙上的钟；点任一工位可**实时看该会员的工作**。

### 3. 设一次、跑起来（README「Set up once」/「Getting started」）

装配询问**业务名、业务类型、所有者、总部地址**（联系方式 / 营业时间 / 价格可留到 Settings），最后一步 **Get Michael ready** 会**安装 Claude Code 并在你可视范围内登录**，或改用 Anthropic API Key。安装：Mac 下 `.dmg`、Windows 下 `-win-x64-setup.exe`（均来自 latest release）；Mac 版**已用 Apple Developer ID 签名并公证**。

### 4. 数据与隐私（README「Your data」）

- **留在你电脑**：团队工作、记忆、文件都在本机文件夹；会员本身**走你自己的 Claude Code / Claude 计划或 Anthropic Key**。
- **只有研究岗联网**：每个办公室里**只有一名**会员（默认 **Pete**）做网络研究；**其余人（含 Michael）不打开网页、不搜索**。发给他的每条消息先过一层**信任层**，按你在 Settings → Research & privacy 标记的私密数据过滤；**他的回答进团队前会先清掉指令性内容**，且他**看不到你的文件**、默认**关闭**需你打开。
- **不采集使用数据**：应用**不上报**你的使用方式；仅**开发者可在代码里修的失败**（崩溃、会员死循环）以**固定字段**自动上报，**不含姓名 / 消息 / 文件**。
- **私密文件夹**：Michael 在你的业务文件夹里工作，每名会员在**它自己的子文件夹**里工作；会员**只能打开自己的**，Michael **可读不可改**团队文件夹。**Mac 上**Claude Code 沙箱与**应用双重**强制；**Windows 上**应用只校验 Claude 的文件工具，**shell 命令尚未限制**。

### 5. 开发者视角（README「For developers」）

```bash
git clone https://github.com/agentvivekkumar/dontbemichael.git
cd dontbemichael
npm install        # 同时为 Electron 重建 node-pty
npm run dev        # 热重载
```

另有 `npm run typecheck` / `test:focused` / `build` / `dist:mac` / `dist:win`。文档入口：`docs/FEATURES.md`（逐版本功能与变更）、`docs/ARCHITECTURE.md`（架构图与模块图）、`HIVE.md`（agent 如何协作）、`SPEC.md`（原始规格）、`branding/DESIGN.md`（设计系统）。

## 使用前需要注意

- **消耗的是你自己的订阅额度**（README）：团队会员跑在**你自己的 Claude Code / Claude 计划**上。README 明说 **Claude Max 才能整天运转**；**较小计划日间会触顶**，办公室**休息到重置**（定时任务仍照发）。**多 agent 并发会加速消耗**——把它当成"**一份订阅要养一屋子 agent**"来估预算。
- **权限与沙箱边界不一致**（README「Private folders」）：会员只开自己的文件夹、Michael 只读不改；**Mac 上沙箱 + 应用双重强制**，**Windows 上 shell 命令尚未被限制**。**在 Windows 上跑要额外谨慎**。
- **联网面**：每个办公室**仅一名研究岗**联网（默认关、需你打开），且**经信任层过滤 + 回答清洗**；**Windows 上该研究岗仍可触达你的办公室文件**（README 明写）。**唯一的例外**：你发起案例研究时，应用会从你**自己的网站**读取 logo。
- **邮箱凭据与自动发送**：邮箱密码**在保存前先测试、加密存在本机**（非放在 agent 能读的文件里）；但 `Can send` / `Send on approval` 决定了会员**能否自动对外发信**——**先小范围用 `Send on approval` 或 `Draft only`**，确认行为再放开。**Outlook / Microsoft 365 暂不支持**。
- **描述与构建有出入**（事实对照）：GitHub **描述**说"works with your existing **Claude Code, Codex** subscriptions"，但 README「What this build leaves out」写明**本构建只提供 Claude Code**，Codex / Gemini 预设**代码里留着但不提供**。**以 README 的"本构建"口径为准**。
- **早期版本、会砍功能**：状态徽章是 **early release**；`0.3.8` **移除了**所有者不用的功能（语音、Webhook、Slack、git 视图、内置代码编辑器、临时辅助 agent、Skills 页——skills 本身仍工作）。**功能面可能在版本间变动**。
- **元数据仅有单口径**：本篇 Star / Watcher / Fork **ungh 与页面一致（591 / 58 / 495）**；开放 Issue（62）与 contributors（200）取自 shields.io；**Release** 存在 **ungh 单页 30 / 页面 43** 两个口径，**引用时注明来源与日期**。
- **我只读了 README、`LICENSE` 与已核实元数据**：**未 clone、未安装、未运行、未构建、未连接邮箱、未登录 Claude、未试用应用**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想把**现成的 Claude 订阅**变成"**一屋子分工明确的 agent**"（客服 / 财务 / 销售 / 市场）而不额外买编排平台时。
- 想看"**每个会员 = 一个真实 CLI agent 进程 + 一个纯文件 hive**"这种**本地多 Agent 编排**怎么落地（含邮箱分派、记忆、任务板、Needs you 审批）时。
- 想让 agent **代管多个邮箱**（分岗位收发、待批发送、附件隔离到各自文件夹）又希望**凭据留在本机**时。
- 想对照其它"**多 agent 编排**"路线时（见下方互链）。
- 想研究"**Office Pack + 角色 / 工作风格**"这种**面向非技术用户的角色模板**设计时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"给一堆 agent 分配角色 / 岗位"**：[agency-agents：326 个 AI 专家角色的 Agent 定义库与多工具安装器](agency-agents-326个AI专家角色的Agent定义库与多工具安装器.md)——那篇是**可安装到多种工具的角色定义库**（偏"角色提示词资产"），本篇是**带办公室界面、邮箱与记忆的本地应用**（偏"跑起来的组织"）。可对照"**角色定义**"与"**角色 + 运行时**"。
- **都是"编排多个编码 Agent"**：[paseo：编排多个编码 Agent 的桌面与移动开发环境](paseo-编排多个编码Agent的桌面与移动开发环境.md)——那篇面向**开发者的多 agent 工作台**（桌面 + 移动），本篇面向**业务所有者的 AI 办公室**（角色 + 邮箱 + 审批）。
- **都从"本地 / 你的订阅"出发**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——那篇是**个人自托管助手**，本篇是**团队化的多会员办公室**；两者都强调**数据留在自己这边**。可对照"**单助手**"与"**一屋子会员**"。
- **同属"Agent 框架 / harness"**：[deepseek-harness：DeepSeek 开源 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)——那篇是**可编程的 Agent 框架**，本篇是**开箱即用的桌面编排应用**；[NamiAgent：从零手写 Agent 的中文可运行教程](NamiAgent-从零手写Agent的中文可运行教程.md) 则**讲原理、带你手写**。三者可对照"**框架 / 应用 / 教程**"三种形态。
- **同为"编排多个 Agent"，面向与形态不同**：[agent-orchestrator：编排编码 Agent 编队的桌面与移动工作台](agent-orchestrator-编排编码Agent编队的桌面与移动工作台.md)——本篇面向**业务所有者的 AI 办公室**（角色 + 邮箱 + 审批），那篇面向**开发者的编码 Agent 工作台**（看板四车道：Working / Needs you / In review / Ready to merge，从任务到合并 PR）；可对照"业务组织"与"开发流水线"两种取向。

## 参考来源

- 仓库主页与 README（392 行；核验日期 2026-11-13）：https://github.com/agentvivekkumar/dontbemichael
- 官网：<https://dontbemichael.com>
- 许可证：`LICENSE`（MIT，1,102 字符；Copyright (c) 2026 Vivek Kumar 与 (c) 2026 Chaitanya Giri；核验日期 2026-11-13）
- 仓库内文档：`docs/FEATURES.md`、`docs/ARCHITECTURE.md`、`HIVE.md`、`SPEC.md`、`branding/DESIGN.md`、`TELEMETRY.md`、`CONTRIBUTING.md`、`CONTRIBUTORS.md`、`CHANGELOG.md`、`src/shared/buildFeatures.ts`
- 下载（latest release）：<https://github.com/agentvivekkumar/dontbemichael/releases/latest>
- 元数据（Star / Watcher / Fork / 创建与推送时间 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**两口径一致**）；开放 Issue 数 / contributors / 状态取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未安装、未运行、未构建、未连接任何邮箱、未登录 Claude、未试用应用**；能力与流程描述均来自 README 与 `LICENSE`，**未逐一验证**。
