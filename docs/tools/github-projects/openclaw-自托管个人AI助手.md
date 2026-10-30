# openclaw：自托管的个人 AI 助手

> 记录它的架构（本地 Gateway 作控制平面、模型与执行器都可换插件），以及它在隐私边界上写得很具体的声明。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、文件列表与元数据，未安装运行）
- 适用范围：macOS / Linux / Windows；安装器需要 Node.js，官方包要求 Node 24.16+ 或 26.1+（推荐 26）

## 核心结论

一个开源的**自托管 AI 助手**：跑在你自己的电脑上，接入你已经在用的聊天工具（Discord、iMessage、Slack、Teams、Telegram、WhatsApp 等 20 多个），另有 macOS、iOS、Android、Windows、Linux 的原生应用。

架构上最关键的一点：**一个 Gateway 同时充当个人助手和团队部署**，configuration 是唯一区别。状态、记忆、凭据都在本机；**模型和 agent 执行器（Claude、Codex、本地模型）都是可替换的插件**，换模型不影响其他部分。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [openclaw/openclaw](https://github.com/openclaw/openclaw) |
| 许可证 | MIT |
| 技术栈 | TypeScript（pnpm workspace，Rust crate 目录用于部分组件）；另有 UI、Docker、Nix 等部署路径 |
| 关注度 | 361,670 Star、73,810 Fork、1,782 关注（2026-10-30 的 API 数据） |
| 活跃度 | 95,626 次提交、6,344 个分支、433 个 Tag；**提交量是我收藏过的项目里最高的** |
| 开放 Issue | API 显示 19,221，页面显示 4.8k Issue + 2.8k PR，两处不一致 |
| 仓库体积 | 约 492 MB（API `size` 字段 503,540 KB） |
| 治理 | OpenClaw Foundation（独立的 501(c)(3) 非营利组织），核心团队由基金会雇用、由基金会签署发布 |
| 站点 / 文档 | https://openclaw.ai/ 、https://docs.openclaw.ai/ |

治理部分 README 写得很明确：**没有付费层、没有托管服务、没有代币**；捐赠者和基础设施赞助方不拥有也不指挥项目，"OpenAI 是捐赠者而非所有者"。

## 组成

- **Gateway**：本地控制平面，管会话、工具、事件与渠道连接。
- **Control UI / CLI / TUI**：都连到 Gateway，用来操作与调试。
- **Channels**：把助手接进 WhatsApp、Telegram、Slack、Discord、Google Chat、Signal、iMessage 等。
- **Companion apps 与 nodes**：在支持的平台上提供语音、Canvas、摄像头、屏幕与设备本地操作。
- **Tools / Skills / Plugins**：扩展能力，通过 plugin SDK 开发、在 ClawHub 分发。

## 安装与上手

```bash
# macOS / Linux / WSL2
curl -fsSL https://openclaw.ai/install.sh | bash
```

```powershell
# Windows PowerShell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

已有 Node 环境的话可以装官方包（Node 24.16+ 或 26.1+，推荐 26）：

```bash
npm install -g openclaw@latest --allow-scripts=openclaw
```

安装脚本会自动进入 onboarding；用包管理器装的要手动跑：

```bash
openclaw onboard --install-daemon
openclaw gateway status
openclaw dashboard
```

开发时注意：仓库是 pnpm workspace，**根目录不支持直接 `npm install`**。

## 隐私边界（README 的原话要点）

这段写得比我收藏的多数项目具体，值得单独记：

- 状态、记忆、凭据都存在**你自己的硬件**上；
- prompt 会发给你自己配置的**模型提供方和聊天平台**，以及你自己启用的诊断导出；
- 默认情况下 OpenClaw 自身**只做每日版本检查**；匿名功能统计是 opt-in；
- `update.checkOnStart: false` 可以**同时关掉这两项**；
- 官方提供了一页说明它到底发什么数据（docs 里的 telemetry 页面）。

也就是说：它没有服务端，但**你的聊天内容仍然会经过你选定的模型提供方**——"自托管"说的是数据和状态，不是推理过程。

## 使用前需要注意

- **默认没有沙箱，工具直接跑在宿主机上**。README 的安全一节写明：除非你自己配置 sandboxing，工具在主会话中是跑在宿主机的。要连接其他用户或把 Gateway 暴露到远程之前，应先读安全指南、暴露处置手册和沙箱指南。
- **要把入站消息当不可信输入**。这是它安全模型的前提：DM 类渠道默认对未知发送者启用配对，需要你手动 `openclaw pairing approve <channel> <code>` 才放行。
- **开放 Issue 数量巨大且两处口径不一致**（API 19k / 页面 4.8k）。以这种提交量（9.5 万次）和 PR 规模看，属于"跑得很快、问题池也很深"的项目，遇到问题时检索既有 Issue 会更有效。
- **仓库体积约 492 MB**，克隆成本高。
- **本项目历史上更名过**（从 Molty 相关的命名而来，README 也仍以"为 Molty 这只太空龙虾而建"叙事），检索资料时注意不同时期的叫法。
- 我未安装运行，以上均为文档信息。

## 什么时候会再看

- 想参考**"可信网关 + 不可信执行 + 确定性策略"**这套架构论证时，README 指向 docs 里的 Why OpenClaw 一节。
- 想参考**可替换插件式的模型与执行器抽象**：换 Claude、Codex、本地模型不改动其他部分，这是它和同类工具的主要区别之一。
- 想参考**开源项目的治理写法**：明确写出谁拥有、谁捐赠、有无商业层，并用基金会形式固化。
- 想给助手接多个聊天渠道时，看它的 channels 与 nodes 设计。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **可以直接配合这个宿主使用的项目**：[AiToEarn](AiToEarn-AI内容变现与多平台发布.md)——它的使用方式之一就是"在 OpenClaw 中使用"，通过插件安装后接收任务；两篇可以对照看"宿主 + 插件"的关系。

## 参考来源

- 项目仓库与 README：https://github.com/openclaw/openclaw
- 官网与文档：https://openclaw.ai/ 、https://docs.openclaw.ai/
- 数据处理说明（README 引用）：https://docs.openclaw.ai/gateway/telemetry
- 架构说明（README 引用）：https://docs.openclaw.ai/start/why-openclaw
- 基金会：https://openclaw.org/

以上信息核验于 2026-10-30。未安装运行，功能与安全边界均未验证。
