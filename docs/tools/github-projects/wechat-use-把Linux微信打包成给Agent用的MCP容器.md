# wechat-use：把 Linux 微信打包成给 Agent 用的 MCP 容器

> 记录一个**把"真实微信桌面"打包进 Docker、再通过一个带 token 的 MCP 端点交给 AI Agent 操作**的仓库（`invzhi/wechat-use`，描述 "Give AI agents a real WeChat desktop they can see and operate."）：它刻意**不碰非官方聊天 API**，而是保留**完整的微信图形界面**，让 Agent 与人类共用同一个会话——Agent 走 MCP 自动化，人走浏览器扫码登录 / 监督 / 接管。三条先记住：**它要真实微信账号 + 住宅 IP**；**浏览器桌面没有认证与加密**；**Agent 能通过你的账号行动**。未部署、未扫码、未运行。

- 状态：草稿
- 最近核验：2026-11-17（阅读 `main` 分支 README 全文（98 行）、`LICENSE`、文件清单（29 项）、Release 列表（1 条）与 ungh 镜像 / shields 徽章；**未 clone、未构建、未起容器、未扫码登录、未接任何 Agent、未审计源码**）
- 适用范围：**自托管**——需 Docker Engine + Docker Compose、一个微信账号、一个**住宅 IP**；占用端口 `5800`（浏览器桌面）与 `3211`（自动化）

## 核心结论

**一句话定位（API 描述）**：`Give AI agents a real WeChat desktop they can see and operate.` 它提供一个**持久、面向 Agent 的 Linux 微信环境**：Agent 连到**带 token 保护的 MCP 端点**来自动化桌面，人则在**浏览器**里用同一个环境登录、监督或在需要时接管。

**① 它选择"真桌面"而不是"非官方 API"。** README 明确：**保留完整的微信图形界面**（`Preserve the full WeChat graphical interface instead of relying on an unofficial chat API.`）。数据流是 `AI agent -> MCP -> 自动化服务 -> Linux 微信桌面 <- 浏览器 - 人`，**Agent 与人类共享同一个微信会话**。

**② Docker 只是交付手段。** README 原话："Docker packages WeChat, its desktop dependencies, Chinese input support, and the automation service into a reproducible environment. It is the delivery mechanism; the goal is to make WeChat a practical tool for AI agents."——把微信 / 桌面依赖 / 中文输入 / 自动化服务一起封装成可复现环境。

**③ 接入方式很简单：一个 MCP 端点 + 一个 bearer token。** 端点 `http://localhost:3211/mcp`，用 `.env` 里的 `CUA_ENV_TOKEN`（`openssl rand -hex 32` 生成）作 bearer token；浏览器侧打开 `http://localhost:5800` 扫二维码登录。**浏览器会话要保留着**，因为扫码登录、验证挑战、需要人类批准的动作都得靠它。

**④ 两个硬要求 + 一条硬警告。** 要求：**微信账号 + 住宅 IP**、Docker。README 用引用块加粗警告：**"Do not run WeChat without a residential IP address."**（没有住宅 IP 就别跑微信）。

**⑤ 安全面是它自己写在明面上的短板。** README 的 Security 段原文：**浏览器桌面不含认证与加密**，**不要把 `5800` / `3211` 暴露到公网**，只能用可信本地网络、防火墙规则或安全隧道，并保管好自动化 token；"An agent operating WeChat can act through your account and access information available in the desktop session."——**Agent 能借你的账号行动**，须限制可信 Agent、敏感操作要求人工确认。

**⑥ 数据落在两个目录里。** 微信把登录态与文件存在 `wechat/.xwechat` 与 `wechat/xwechat_files`；README 要求保留这两个目录以在更新 / 重启后续用 Agent 工作区，并**定期备份（备份前先 `docker compose down` 停微信）**——"These directories contain sensitive account data and personal files. Keep them private."

**⑦ 很新、很小。** **12 Star / 4 Fork / 0 关注**（ungh 快照，2026-11-17）；shields 同期 **28 Star / 5 Fork、0 开放 Issue、贡献者 1**；**2026-11-15 创建**、pushed **2026-11-16**；`main`；**1 个 Release**（`v0.1.0`，2026-11-16）；**29 个文件**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [invzhi/wechat-use](https://github.com/invzhi/wechat-use) |
| 描述（API） | "Give AI agents a real WeChat desktop they can see and operate." |
| 许可证 | **Apache-2.0**（`LICENSE` 11,341 字节） |
| 主语言 | shields 顶层语言 **91.3%** |
| 关注度 | **12 Star / 4 Fork / 0 关注**（ungh，2026-11-17；shields 同期 28 / 5）；**0 开放 Issue、贡献者 1 人**（shields） |
| 创建 / 最近推送 | **2026-11-15 创建**；`pushedAt` **2026-11-16**、`updatedAt` 2026-11-17 |
| 默认分支 / Release / 文件数 | `main`；**1 个 Release**（`v0.1.0`，2026-11-16）；**29 个文件** |
| 根目录可见文件 | `README.md`（3,556 B）、`LICENSE`（11,341 B）、`Dockerfile`（905 B）、`compose.yaml`（1,095 B）、`.env.example`（15 B）、`.gitignore` |
| 依赖 / 端口 | Docker Engine + Docker Compose；端口 `5800`（浏览器桌面）、`3211`（自动化） |

## 它能提供什么（README 自述）

- **MCP 桌面自动化**：兼容的 AI Agent 通过**单一自动化端点**操作微信。
- **一个真实桌面**：保留完整微信图形界面，不依赖非官方聊天 API。
- **人工监督与接管**：在浏览器打开同一个桌面，扫登录二维码、查看 Agent 的动作、或直接接管。
- **持久工作区**：登录态与收到的文件跨重启 / 更新保留。
- **可复现部署**：用 Docker Compose 连同所需依赖一起跑起桌面与自动化服务。

## 使用前需要注意

- **① 它扫的是你的真实微信账号，且要求住宅 IP。** 没有住宅 IP 就不应运行（README 原文加粗警告）。**个人微信自动化存在账号被限制 / 封禁的风险**，且可能不符合平台条款；**本笔记不对其合规性作评价**。
- **② 浏览器桌面无认证、无加密。** README 明说不要把 `5800` / `3211` 暴露公网。**从另一台机器访问时要换成 Docker 主机地址，并且只走可信网络或安全隧道**；自动化 token 要保密。
- **③ Agent 能借你的账号行动。** README 提醒：Agent 可经你的账号操作、并访问会话内可见的信息；**要限制可信 Agent 并给敏感操作留人工确认**。这是功能也是风险，一体两面。
- **④ 敏感数据落在两个目录里。** `wechat/.xwechat` 与 `wechat/xwechat_files` 含账号数据与个人文件，**保持私有、定期备份**（备份前先停微信）。
- **⑤ 极新、极小、单一贡献者。** **2026-11-15 创建 + 1 个 Release + 29 个文件 + 1 位贡献者**——**尚无可参考的第三方使用记录**，别当成熟方案。
- **⑥ 我未做任何验证。** 仅阅读 README 全文、`LICENSE`、文件清单、Release 列表与元数据；**未 clone、未构建、未起容器、未扫码登录、未接任何 Agent、未审计源码**；配置项、依赖与安全边界均为 README 自述。

## 什么时候会再看

- 想研究"**把真实 GUI 应用（而非其非官方 API）交给 Agent 操作**"这条路线时，尤其是"**Agent 与人类共用一个会话、人负责扫码与兜底**"的分工写法。
- 想对照"**给 Agent 操作真实桌面 / 聊天工具**"的几种形态（本篇走容器化 Linux 桌面 + MCP；对照 [weclaws](weclaws-多用户微信AI智能体控制台.md) 的多用户控制台、[bb-browser](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md) 的"复用真实登录态"）时。
- 想参考"**Docker 交付 + `.env` token + 单一 MCP 端点**"这种最小可行的 Agent 接入形态时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"把微信交给 Agent 操作"，形态相反**：[WeClaws：多用户微信 AI 智能体控制台](weclaws-多用户微信AI智能体控制台.md)——那是**自托管服务端 + 多用户控制台**（网页写运行意图、supervisor 拥有运行时、FastAgent CLI 执行、按用户开远程沙盒），通道是**个人微信扫码**；本篇是**单个容器化的真实 Linux 微信桌面 + MCP 端点**，Agent 与人类共用同一会话。一个是"**多用户托管平台**"，一个是"**给一个 Agent 开一扇真实桌面**"，可对照两种把 Agent 接进微信的做法与风险边界。
- **同为"把真实登录态 / 真实应用交给 Agent"**：[bb-browser：把真实浏览器登录态给 Agent 当 API 的 CLI 与 MCP](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md)——那篇复用你**已登录的真实 Chrome**、把网站变成命令；本篇把**整个微信桌面**放进容器、经 MCP 交给 Agent。两者都在"**如何安全地把真实账号会话暴露给 Agent**"这件事上踩同一片雷区（本篇自述无认证无加密、须住宅 IP；那篇本地会开调试端口），可对照它们的边界设法与风险提示。

## 参考来源

- 仓库主页与 README（核验日期 2026-11-17）：https://github.com/invzhi/wechat-use
- 许可证 `LICENSE`（Apache-2.0，11,341 字节）：https://github.com/invzhi/wechat-use/blob/main/LICENSE
- Release 列表（`v0.1.0`，2026-11-16）：https://github.com/invzhi/wechat-use/releases
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-17

以上信息核验于 2026-11-17：Star / Fork 与版本可能变化（该仓库创建仅两天）。**未 clone、未构建、未起容器、未扫码登录、未接任何 Agent、未审计源码**；**住宅 IP、账号封禁与合规风险、浏览器桌面无认证无加密、Agent 借账号行动等边界，均以 README 与维护者公开记录为准，使用前请自行评估并承担后果。**
