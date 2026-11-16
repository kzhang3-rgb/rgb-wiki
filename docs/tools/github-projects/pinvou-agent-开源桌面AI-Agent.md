# pinvou-agent：面向工作、设计与代码的开源桌面 AI Agent 工作台

> 一句话定位（README 自述）：**"面向工作、设计与代码的开源桌面 AI Agent 工作台"**——把**通用工作、视觉设计、代码开发**放进同一个桌面应用，让 AI 从"回答问题"走到"**完成任务**"。最值得记的三点：① 它不是聊天窗，而是**以"可交付产物"为中心**（产物面板自动收集 AI 创建/修改的文件，Markdown 产物可直接编辑）；② 通过 **ACP** 把 **Codex、Claude Code、Kimi** 接进同一工作台，会话与工作区绑定、重启后可续；③ 数据边界由**你启用的模型与工具**决定——本地模型 + 本地工具才能保持**闭环**，云模型 / 远程 MCP / 第三方连接器会把相关请求发给对应服务。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（英文 242 行、简体中文 248 行）、`LICENSE`（1,063 字符）与已核实元数据；**未 clone、未安装依赖、未编译、未运行应用、未接入任何模型 / MCP / 连接器**）
- 适用范围：桌面端 **Windows / macOS / Linux**（Tauri 2 + React 19 + Vite + Rust）；源码树需 **Git（含 submodule）、Node.js/npm、Rust toolchain、Tauri 2 系统依赖**，以及一个可访问的 **OpenAI-compatible** 模型端点

## 核心结论

1. **一个工作台，三种工作方式**（README）：**Work**（附件 + 个人知识 + 专家角色 + Skills + MCP + 工作流，交付可复用文件）、**Design**（自然语言生成海报 / 数据可视化，再进编辑模式选中元素改文案 / 字体 / 颜色 / 尺寸 / 布局）、**Code**（经 ACP 使用 Codex / Claude Code / Kimi，可读写真实项目或隔离临时工作区、跑命令、展示计划与文件变更）。
2. **界面之上有明确的能力边界**：模型调用、流式输出、工具循环、Session、MCP client、Skills、Hooks 与 Compaction 由 **CodeWhale 底座**（一个 submodule）提供；`pinvou3-app/` 只负责桌面 UI、运行时配置、业务编排与系统集成，"**不重复实现底座能力**"。
3. **数据是否离开本机取决于配置**（README 的 NOTE）：本地模型 + 本地工具 = 全本地；Cloud 模型、远程 MCP、第三方连接器会把相关请求发给各自服务。会话、设置、知识与运行时扩展统一保存在 `~/.pinvou3/`。
4. **模型接入**：支持**本地 vLLM** 与任意 **OpenAI-compatible API**；内置 DeepSeek、Kimi、通义千问、豆包、MiniMax、智谱、MiMo、OpenAI、Anthropic、Gemini、xAI 等模板，可保存多套配置并按会话切换（不改发给供应商的实际模型 ID）。

**个人判断（未验证）**：它的差异化不在"又一个聊天客户端"，而在**把"干活 / 出图 / 写码"三条动线压进一个桌面壳**，并把**产物**当一等公民（产物面板、可编辑 Markdown、Plan/YOLO 双模式）。代价是**依赖链长**（Node + Rust + Tauri 2 系统依赖 + 一个外部模型端点），且"里程碑式能力"（ACP 多 Agent、远控、专家卡牌池）都来自自述，**未逐一验证**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Pinvou/pinvou-agent](https://github.com/Pinvou/pinvou-agent) |
| 描述（原文） | "Open-source desktop AI agent for tools, files, knowledge, workflows, and real deliverables." |
| 许可证 | **MIT**（`LICENSE` 1,063 字符，"Copyright (c) 2026 Pinvou"） |
| 默认分支 | **`main`** |
| 关注度 | **ungh 镜像 2,968 Star / 431 Fork / 313 Watcher**；页面值 **3,004 / 434 / 320**（口径不同，并列） |
| 社区 | shields：开放 Issue **33**、贡献者 **9**；QQ 用户群 1108909346 |
| 维护状态 | **2026-07-24 创建**；ungh `pushedAt` **2026-11-11**、页面 `updatedAt` 2026-11-11（活跃） |
| Release | ungh **30**（单页上限）/ 页面 **35**；更新经 GitHub Releases 分发，**应用内更新检查暂未启用** |
| 技术栈 | Tauri 2 · React 19 · Vite · Rust stable |
| 官网 | <https://pinvou.com/> |

## 功能与用法

### 1. 三条动线（README）

| 动线 | 做什么 |
| --- | --- |
| **Work** | 结合附件、个人知识、专家角色、Skills、MCP 工具与工作流，完成资料整理、公文写作、信息检索、业务分析，并把结果交付为**可继续使用的文件** |
| **Design** | 自然语言生成海报 / 数据可视化，进编辑模式选中元素调**文案 / 字体 / 颜色 / 尺寸 / 布局**，或继续对话迭代 |
| **Code** | 经 ACP 使用 **Codex / Claude Code / Kimi**，读写真实项目或隔离临时工作区、跑命令，展示计划、工具步骤、权限请求与文件变更；会话绑定工作区、重启后继续 |

### 2. 能力清单（README 分组）

- **从对话到可交付产物**：多会话管理 + 标题搜索；PDF / Office / 图片 / 文本附件（拖拽或粘贴）；**产物面板**自动收集 AI 创建或修改的文件；Markdown 产物可直接编辑；**Plan / YOLO 双模式**；**辅助对话**（任务运行期旁路提问，唯一工具是服务端强制的本任务历史只读视图，ADR-0024）。
- **知识与记忆**：**本地知识库**（文件管理 + 全文 + 向量检索，一个对话可挂多个库并分别启停，回答保留来源）；**记忆中心**（长期偏好候选确认）；**专家卡牌池**；Skills / Commands / 工作流。
- **连接真实业务工具**：**工具商店**统一管理本地 MCP、远程 MCP、CLI 与 API 连接器；OAuth / SSO 免手填密钥；内置**飞书、钉钉、企业微信、腾讯会议、腾讯文档、腾讯 ima、Obsidian**，以及企查查、智慧芽、Canva、高德天气等；**扫码远控**当前工作区。
- **面向实际运行**：Windows 托盘常驻；全局唤醒快捷键（默认关闭，`Ctrl/Cmd+Alt+P`）；本地语音输入；GPU / 内存 / 磁盘 / 模型服务 / 上下文集中监控。

### 3. 快速开始与架构（README）

```bash
git clone --recursive https://github.com/Pinvou/pinvou-agent.git
cd pinvou-agent/pinvou3-app && npm ci
cd .. && ./pinvou3-app/run-dev.sh
```

仓库结构：`pinvou3-app/`（桌面应用）、`CodeWhale/`（Agent 底座 submodule）、`pinvou-knowledge/`（可复用知识库核心与自包含服务器）、`remote-control-relay/`（扫码远控的可选自托管 relay）、`pinvou3-app/resources/mcp-servers/`（独立本地 MCP 服务）、`scripts/`、`docs/`。扩展落位原则：领域 Agent / 工具组合 → `SKILL.md`；接外部 API → 独立 MCP server 或连接器；调整模型行为 → bundle 的 `instructions.md`；桌面 UI / 系统能力 → `pinvou3-app/`；修底座通用问题 → CodeWhale fork。

## 使用前需要注意

- **权限与数据边界要靠自己把关**：README 明写"数据是否离开本机取决于你选择的模型服务和工具"。启用**云模型、远程 MCP 或第三方连接器**时，相关请求会发送给对应服务；要闭环就必须用**本地模型 + 本地工具**。**别把"开源桌面应用"当成"数据不出本机"的同义词**。
- **发布渠道与版本来源**：更新**经 GitHub Releases 分发**，且**应用内更新检查暂未启用**；README 与页面均以 **"Preview / 下载预览版"** 指称安装包。**下载与安装前请自行确认来源为官方仓库 release**（本条为对分发方式的提示，非对具体产物签名的判断，**未验证**）。
- **远控是额外的暴露面**：扫码后可从手机**查看并控制**当前工作区；`remote-control-relay/` 是可选的**自托管 relay**。启用前先想清谁能扫到、relay 部署在哪。
- **依赖链较长**：源码运行需 **Git（submodule）、Node/npm、Rust、Tauri 2 系统依赖**；Linux 发布包基线为 Ubuntu 22.04+（glibc 2.35+）；**旧世界龙芯系统（UOS V20 龙芯版、Loongnix 20、麒麟 V10 时代）不受支持**（缺 webkit2gtk-4.1/libsoup3，rustc ≤ 1.58）。文件解析（PDF / Office / OCR / 压缩包）依赖**可选外部工具**（见 `pinvou3-app/INSTALL.md`）。
- **底座是 fork 维护**：CodeWhale 以 fork 形式跟进，README 指向 `docs/fork-policy.md` 与 `docs/fork-modifications.md`；改动底座通用问题需按 fork 策略处理——**这意味着升级与上游差异要额外关注**（**个人判断（未验证）**）。
- **元数据两套口径**：Star / Fork / Watcher 的**页面值与 ungh 镜像值不同**（页面 3,004/434/320 vs 镜像 2,968/431/313），**引用时注明来源与日期**。
- **我只读了 README 与 LICENSE 及元数据**：**未 clone、未安装依赖、未编译、未运行任何界面（Work / Design / Code）、未接入任何模型 / MCP / 连接器、未扫码远控**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想要一个**桌面级 Agent 工作台**，把"干活 / 出图 / 写码"三条动线放在一个界面里，并**以产物交付**而不是以聊天结束。
- 想参考"**用 ACP 把外部编码 Agent（Codex / Claude Code / Kimi）接进自己的桌面壳**"的做法。
- 想研究 **Tauri 2 + React/Vite + Rust 底座 submodule** 这种"UI 层与 Agent 引擎解耦"的架构分工。
- 想对照其它**桌面 / 自托管 AI 客户端**的定位差异。
- 想核对 "**本地闭环 vs 云端出网**" 的数据边界在真实产品里怎么被描述。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"个人 AI 助手"，交付形态不同**：[openclaw：自托管的个人 AI 助手](openclaw-自托管个人AI助手.md)——那篇重**自托管**（把助手跑在自己的服务器 / 通道上），本篇是**桌面应用**且强调**三种动线与产物交付**。可对照"**自托管**"与"**桌面工作台**"两条路线。
- **同为"多模型 + 知识库的桌面工作台"**：[cherry-studio：把模型、Agent 与知识库收进一个桌面工作台](cherry-studio-多模型Agent与知识库的桌面工作台.md)——两篇都做多模型接入、知识库与 Agent 编排；本篇额外把**可视化设计与 ACP 编码 Agent** 收进同一壳。可对照"**知识库 + Agent**"与"**工作/设计/代码三合一**"。
- **同属桌面 AI 客户端**：[chatbox：桌面 AI 客户端的社区版（GPLv3）](chatbox-桌面AI客户端社区版.md)——那篇是**轻量对话客户端**（许可是 GPLv3），本篇是**重量级 Agent 工作台**（MIT，Tauri + Rust）。可对照"**聊天优先**"与"**任务 / 产物优先**"。
- **都谈"记忆 / 长期上下文"**：[mnemos-mcp：让 AI 拥有"自己"长期记忆的 MCP 服务器](mnemos-mcp-让AI拥有自己长期记忆的MCP服务器.md)——那篇把**长期记忆**做成独立 MCP 服务，本篇把它做进**记忆中心 + 本地知识库**。可对照"**记忆外置为服务**"与"**记忆内建在工作台里**"。

## 参考来源

- 仓库主页与 README（英文 242 行 / 简体中文 248 行；核验日期 2026-11-13）：https://github.com/Pinvou/pinvou-agent
- 简体中文 README（248 行；核验日期 2026-11-13）：https://github.com/Pinvou/pinvou-agent/blob/main/README.zh-CN.md
- 官网：<https://pinvou.com/>
- 许可证：`LICENSE`（MIT，1,063 字符；核验日期 2026-11-13）
- 相关文档（README 指向，未逐一打开）：`CONTRIBUTING.md`、`SECURITY.md`、`SUPPORT.md`、`THIRD_PARTY_NOTICES.md`、`TRADEMARKS.md`、`docs/fork-policy.md`、`docs/fork-modifications.md`、`docs/sbom.md`、`docs/multi-agent-acp.md`、`docs/windows-system-tray.md`、`pinvou3-app/INSTALL.md`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13，**页面值与镜像值存在差异**）

以上信息核验于 2026-11-13。**未 clone、未安装依赖、未编译、未运行任何界面、未接入任何模型 / MCP / 连接器、未扫码远控**；能力与流程描述均来自 README，**未逐一验证**。
