# WeClaws：多用户、按用户沙盒隔离的微信 AI 智能体控制台

> 它的价值不在"又做了一个微信机器人"，而在**把一条架构纪律写死了**：**网页只写入"用户想让机器人运行"的持久化意图，SQLite 是控制面的事实来源，supervisor 才拥有运行时，真正执行智能体的进程是外部的 FastAgent CLI**——四层各管一段、互不越界。配合"**按用户开远程沙盒**"，它把"手工命令行跑一个 bot 脚本"升级成了"部署一次、多用户各自管理并长期托管"。**但它扫的是你的个人微信**，登录态托管在服务器上——**这既是最实用的地方，也是风险最集中的地方。**

- 状态：草稿
- 最近核验：2026-11-12（阅读 README 全文、`LICENSE`、仓库文件树与元数据；**未 clone、未 `pnpm install`、未部署、未扫码、未接微信或任何模型服务、未跑任何测试**）
- 适用范围：**自托管（Docker Compose 四服务 / 本地开发双进程）**；本地开发需 **Node.js 20+ 与 pnpm**，另需一条**可用的模型服务 API 密钥**；生产建议部署在云服务器并配 `WECLAWS_DATA_ROOT` 持久化目录

## 核心结论

**一句话定位（README 自述）**：面向**团队、多账号、按用户沙盒隔离和长期运行**的**微信 AI 智能体控制台**。"如果你要的不是'本地手工跑一个机器人脚本'，而是'部署一次后，让多个用户各自管理、分享、登录并长期运行自己的微信 AI 机器人'，WeClaws 更接近这个答案。"

**它明确划出的三件事：**

1. **它是控制台，不是单机脚本**：用户账号 + 邀请码注册 + 模型配置 + Bot 管理 + Bot 快速分享 + 状态刷新 + Web 入口，面向"真正交给多人使用"。
2. **它面向长期运行**：启动、扫码登录、运行状态、异常可见、重启与 supervisor 状态收敛都围绕持续托管设计；**SQLite 存的是"意图"，不被当作内存事实来源**。
3. **它不重造 AI 运行时**：真正的微信对话与工具执行交给外部 npm 包 **`@fastagent/cli`**，隔离执行交给 **`@fastagent/sandbox-runtime`**；WeClaws 只做多用户控制面与运维体验。

**三个最值得记的点：**

1. **控制面 / 运行面 / 数据面分得很干净**（见下表）——这是本项目最值得抄的部分，也是判断"自托管项目能不能长期跑"的关键。
2. **"按用户沙盒隔离"是它对着同类项目打的差异点**：默认 Compose 会起独立的 `sandbox-runtime` 服务与 `browserless` sidecar，镜像内预装一整套工具链（含 `bubblewrap`），**目的是让不同用户的工具执行互不影响**。
3. **它把"微信个人号"当作通道**：扫码登录的是**个人微信**，而登录态（OAuth token、设备配对态）**托管在你部署的服务器上**——功能上很顺，**风险也最集中在这里**（详见"使用前需要注意"）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [yokingma/weclaws](https://github.com/yokingma/weclaws) |
| 描述 | Self-hosted 多用户 WeChat / 微信 AI 智能体控制台，支持 Web 管理、Docker 部署、MCP、Skills、记忆、定时任务、沙盒执行和长期运行，支持分享 AI Bot 给团队成员 |
| 归属 | 账号 `yokingma`（个人；页面 `isOrg: false`、`isFork: false`、`isArchived: false`） |
| 许可证 | **MIT**（`LICENSE` 1,064 字符，署名 `Copyright (c) 2026 WeClaws`）——**注意：这只覆盖控制面代码，真正的运行时是两个外部 npm 包** |
| 主语言 | **TypeScript 62.6%** |
| 技术栈 | **pnpm monorepo**：`apps/web`（Next.js 控制台 + HTTP 接口 + **Better Auth** + **SSE**）、`apps/supervisor`（Node/TS，子进程管理与状态收敛）、`packages/db`（**SQLite + Drizzle**，迁移与仓储 API）、`packages/shared`（跨工作区契约）；部署用 **Docker Compose** |
| 关注度 | **52 Star / 7 Fork / 2 关注 / 0 开放 Issue**（页面值与 ungh.cc 镜像一致，2026-11-12；Issue 数取自 shields.io） |
| 维护状态 | **2026-05-07 创建**；ungh 报 pushed **2026-09-17**（距核验日约 2 个月）；页面 `updatedAt` 2026-10-21 |
| 默认分支 | `main` |
| 版本 | **10 Release / 11 tags**；同时发布 npm 包（`packages: true`） |
| 话题标签 | `agents` · `ai-agents` · `harness` · `harness-engineering` · **`openclaw`** · `sandbox-bot` · `self-hosted` · `wechat` · `wechat-bot` · `weclaws` · `weixin` |
| 仓库体积 | `main` 文件树约 **757 个文件** |

## 分工：四层各管一段（README 原表）

| 层级 | 负责什么 | **不**负责什么 |
| --- | --- | --- |
| `@fastagent/cli`（外部包） | 智能体运行时、微信通道、工具调用、技能、MCP、记忆、定时任务、沙盒参数、JSONL 事件 | 多用户网页管理、账号权限、实例数据库、页面状态 |
| `@fastagent/sandbox-runtime`（外部包） | 多用户远程沙盒、进程池、隔离执行环境 | 网页控制台、机器人生命周期、业务数据库 |
| `apps/supervisor` | 读数据库里的运行意图，启停 FastAgent 子进程，注入实例环境变量，消费事件并落库 | 网页界面、认证、用户交互 |
| `apps/web` | 控制台、HTTP 接口、Better Auth、SSE、Bot / 模型配置管理、技能同步入口 | **直接拉起本地进程、直接持有运行态真相** |
| `packages/db` | SQLite 结构、迁移、仓储 API、持久化语义 | 业务界面、外部运行时 |
| `packages/shared` | 跨工作区稳定类型、路径规则、JSONL 结构 | 各工作区内部实现 |

**数据流（README 自述）**：用户 → 网页 → SQLite；Supervisor ↔ SQLite；Supervisor → FastAgent 子进程；FastAgent → 微信 / 沙盒运行时 / JSONL 事件；事件回流 Supervisor → SQLite；网页经 **SSE** 推送状态。

## 设计原则（README 自列，值得单独记）

- **SQLite 是控制面的事实来源。**
- **网页不直接管理本地进程，只写入持久化运行意图。**
- **Supervisor 拥有运行时，负责持续收敛状态。**
- **FastAgent CLI 通过外部契约接入，不依赖它的内部包结构。**
- **机器人路径通过共享路径解析器派生，不持久化宿主机特定路径。**（README 甚至建议生产目录用 `/srv/weclaws/data` 这类标准路径，"避免路径里出现个人用户名、临时项目名等宿主机细节"。）
- **不为已经退役的行为添加兼容层，除非明确需要。**

## 你能用它做什么

- 在网页控制台创建和管理**多个**微信智能体机器人；支持**首个管理员自举** + **邀请码注册** + 多用户账号体系。
- 为每个用户保存**多条模型服务配置**，不同机器人可绑定不同服务商 / 模型 / API 密钥（**不再靠仓库级环境变量绑死默认模型**）。
- 启动机器人后展示**微信扫码登录二维码**，登录状态经 **SSE** 实时刷新。
- **Bot 二维码公开分享**：别人打开分享页后可在"等待扫码"时直接扫码领取，分享页会自动刷新到最新二维码。
- 停止 / 重启 / 查看运行状态，生命周期由 supervisor 统一管理。
- 用 **SQLite 保存**用户、机器人、模型配置、运行意图与状态。
- 按用户分配远程沙盒进程池，让机器人在隔离环境里使用工具。
- 同步和管理 WeClaws 托管技能包，**同时保留用户自己的技能**。

**模型服务支持**：每条配置含服务商 / 模型名 / API 密钥 / 可选网关地址 / 接口类型。接口类型支持 `anthropic-messages`、`openai-completions`、`openai-responses`、`google-generative-ai`；另对 OpenAI Chat Completions 兼容网关下的 **Kimi K2**（`kimi-k2*`）与**小米 MiMo**（`mimo-v2*`）做了多轮工具调用兼容处理。

## 沙盒镜像内置环境

默认 Compose 会构建 `sandbox-runtime` 镜像，工具**在构建阶段预装**（README 说这是为"机器人在远程沙盒里处理常见任务，不需要每个用户重复安装"）：

| 类别 | 内容 |
| --- | --- |
| 运行时 | Node.js 20、`bun`、`pnpm`、`python3`、`uv` |
| 系统工具 | `bash`、`curl`、`git`、`gh`、`ripgrep`、`jq`、`file`、`zip`、`unzip`、`socat`、`procps` |
| 官方 CLI | **飞书 / Lark 官方 CLI `lark-cli`** |
| 构建 | `make`、`g++` |
| 媒体 / 文档 | `ffmpeg`、`pdftotext`、`pdfinfo`、`pandoc` |
| 浏览器 | 预置 `agent-browser` 客户端 + `browserless` sidecar（**受支持路径是连远程浏览器后端，不支持本地启动浏览器**） |
| 沙盒基础 | **`bubblewrap`** 与 `@fastagent/sandbox-runtime` |

**关键安全边界（README 明确）**：**用户 API 密钥、OAuth token、微信登录态、设备配对态等个性化状态不会被打进镜像**，需通过运行时配置或外部状态注入。

## 托管技能同步

内置一组官方托管技能（来源 `resources/skills/managed`），会同步到每个机器人实例的 `data/skills`。`manifest.json` 是默认同步清单的唯一来源；supervisor 启动机器人前尝试同步一次，**同步失败或已有锁时不阻断启动**；机器人详情页另有手动 `Sync Skills` 入口（只同步、不带重启语义）；**只托管实例级 `data/skills`，不动用户自管的 `workspace/.fastagent/skills`**；目标目录已有同名但无托管标记的技能时**保留用户内容并跳过**。

当前默认同步的技能（README 原表）：

| 技能 | 用途 | 主要依赖 |
| --- | --- | --- |
| `weather` | 天气与预报 | `curl` |
| `github` | 用 `gh` 处理仓库 / Issue / PR / CI | `gh` |
| `skill-creator` | 创建 / 编辑 / 校验 / 打包 FastAgent 技能 | `python3` |
| `video-frames` | 用 ffmpeg 从视频截帧 | `ffmpeg` |
| `personal-planner` | 复杂任务"先规划再执行"工作流 | 无 |
| `agent-browser` | 浏览器自动化（默认走 Browserless sidecar） | `agent-browser`、Browserless |
| **`lark-*`（24 个）** | **飞书 / Lark 官方公开技能包**：IM、日历、文档、Drive、Sheets、Slides、Base、Task、Mail、Wiki、会议纪要、OKR、审批等 | `lark-cli` |
| `ppt-skill` | 生成 HTML 网页 PPT 并交付可预览 deck 目录 | `node` |
| `editorial-card-screenshot` | 生成 editorial 风格信息卡并经远程 Browserless 导出 PNG | `curl`、`python3`、Browserless |

README 也标注了口径：**技能是否真能用还取决于运行环境是否有对应命令与授权**（如 `gh` 认证、`lark-*` 需 bot 自己完成 `lark-cli` 授权），**用户级密钥与 OAuth 状态不会内置进镜像**。

## 部署要点

**本地开发**：`pnpm install` → `cp .env.example .env` → `pnpm db:generate` → `pnpm db:migrate` → 分别 `pnpm dev:web` 与 `pnpm dev:supervisor`，打开 `http://localhost:3000`。

**Docker Compose 一键部署**（默认栈四服务：web、supervisor、sandbox-runtime、browserless）：

```bash
cp infra/compose/.env.example infra/compose/.env
docker compose --env-file infra/compose/.env -f infra/compose/docker-compose.yml up -d
```

生产环境用已发布镜像（不必在服务器构建），叠加 `docker-compose.prod.yml` 后 `pull` 再 `up -d`。**上线前至少确认**：`APP_BASE_URL` 与实际地址一致；`BETTER_AUTH_SECRET` 换成真随机密钥（如 `openssl rand -hex 32`，**不要留 `replace-me`**，否则 web 会报 `DATABASE_URL` / `APP_BASE_URL` / `BETTER_AUTH_SECRET` 缺失）；`WEB_ADMIN_EMAILS` 填首个管理员邮箱（首次注册命中即可完成**管理员自举**）；`WECLAWS_DATA_ROOT` 指向宿主机持久化目录。

## 使用前需要注意

- **⚠️ 它扫的是你的个人微信，这是最大风险**：通道是**个人微信扫码登录**，而**微信登录态与设备配对态托管在你部署的服务器上**。两个后果：① **服务器一旦被攻破，等于微信账号被接管**（README 强调这些状态不会进镜像，但仍存在你的部署环境里）；② **个人微信自动化长期处于灰色地带**，存在**账号被限制 / 封禁**的风险，且可能不符合平台条款。**这是功能与风险绑在一起的一体两面，不是可以绕开的实现细节。**
- **⚠️ MIT 只管控制面，核心运行时在两个外部 npm 包**：真正的智能体执行与沙盒能力来自 **`@fastagent/cli`** 与 **`@fastagent/sandbox-runtime`**。**README 未说明这两个包是否由同一批维护者维护、它们的许可与版本策略也需另行核对（我未核验）**——要长期托管，这两个上游包的稳定性与许可就是你的实际依赖面。
- **⚠️ 模型 API 密钥存在 SQLite 里**：README 说每条模型配置含 API 密钥，且**用户级密钥不进镜像**；但**未见说明数据库中的密钥是否加密存储（未核实）**。数据库文件即等价于凭据清单，**备份 / 权限 / 磁盘加密要自己管好**。
- **⚠️ 暴露面不止一个**：默认 Compose 会起 **`browserless` sidecar** 与 sandbox-runtime；这类"远程浏览器 + 远程执行"服务**若被暴露到公网风险较高**，务必只在内网 / 反代 + 鉴权后使用。
- **它从"默认无沙盒"的另一端出发（对照价值）**：同类项目（如已收录的 [openclaw](openclaw-自托管个人AI助手.md)）**默认让工具跑在宿主机上**，要靠用户自己配沙箱；WeClaws 则**默认就把执行放进独立沙盒**。**这一条是它相对同类项目最实在的差异。**
- **项目很新、社区沉淀薄（事实）**：**2026-05-07 创建**，**52 Star / 7 Fork / 0 开放 Issue**，ungh 报 **pushed 2026-09-17（约 2 个月前）**。**"0 开放 Issue"既可能是质量好，也可能是没人用**——没有第三方验证与已知问题的公开讨论，**别把它当"久经检验"**。
- **仓库里有一个第三方 Codex 技能，且把 `__pycache__` 提交了进来（卫生问题）**：`.codex/skills/ui-ux-pro-max/` 内含一整套 UI/UX 设计数据（CSV）与 Python 脚本，**其中 `scripts/__pycache__/*.pyc` 三个编译缓存文件已入库**——说明 `.gitignore` 有遗漏。另可见 `.cnb.yml`（CNB 流水线）、`AGENTS.md`、两份 `PATTERNS.md` 与两份 `CHANGELOG.md`。
- **topics 里有 `openclaw`，但 README 全程未提 openclaw（未核实）**：README 描述的运行时是 **FastAgent CLI**，不是 OpenClaw。**这个标签的含义（生态定位 / 检索用）我未核实**，引用时不要据此断定它基于 OpenClaw。
- **我未做任何验证**：**未 clone、未安装、未部署、未扫码登录、未接微信、未接任何模型服务、未跑任何测试**；上述架构、分工、技能清单、沙盒工具链**均来自 README 与仓库文件自述**。GitHub API 匿名配额用尽，未取到 `size` 与贡献者统计。

## 什么时候会再看

- 想参考"**把控制面 / 运行面 / 数据面拆开**"的落地写法：网页只写持久化意图、独立 supervisor 拥有运行时、CLI 通过外部契约接入、SQLite 作事实来源、SSE 推状态。
- 想搭一套**多用户、按用户隔离沙盒、可长期托管**的聊天机器人平台（尤其面向团队成员 / 客户分账号时）。
- 想研究"**把外部 Agent CLI 当运行时、自己只做控制台**"这种分工（含 JSONL 事件流契约、实例级技能同步、托管技能与用户技能的共存规则）。
- 想参考"**托管技能同步**"的具体规则：清单唯一来源、失败不阻断启动、只动托管目录、同名冲突保留用户内容。
- 作为"**个人微信自动化通道**"的工程样本，评估其**封号与合规风险**、以及登录态托管带来的安全边界。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"把 Agent 接进聊天工具 + 一个控制面"，但宿主与隔离取向相反**：[openclaw：自托管的个人 AI 助手](openclaw-自托管个人AI助手.md)——那是**本地 Gateway 作控制平面**、把助手接进 20 多个聊天渠道、面向**个人**；本篇是**自托管服务端**、只做**微信**一个通道、面向**多用户**。**最关键的差别是沙盒**：openclaw **默认无沙箱、工具直接跑在宿主机**，本篇**默认按用户开远程沙盒**。两篇合看正好覆盖"聊天通道 + Agent"的**个人单机**与**团队托管**两种形态。
- **都强调"控制面不持有长跑运行态"，领域不同**：[QuantDinger：开源的 AI 交易操作系统](QuantDinger-开源AI交易操作系统.md)——那篇的分界线是"**HTTP API 不再持有长跑的交易 / 调度循环**"，交易 / 调度 / 异步任务 / 迁移各自独立进程；本篇的分界线是"**网页只写入运行意图，supervisor 才拥有运行时，FastAgent CLI 才执行**"。**同一个架构纪律，一个落在交易系统、一个落在多用户 Agent 控制台**，可对照"控制面 / 运行面分离"在不同领域的写法。

## 参考来源

- 项目仓库与 README：https://github.com/yokingma/weclaws
- 许可（MIT）：https://github.com/yokingma/weclaws/blob/main/LICENSE
- 部署与运维文档索引：https://github.com/yokingma/weclaws/blob/main/docs/manuals/README.md
- 托管技能手册：https://github.com/yokingma/weclaws/blob/main/docs/manuals/managed-skills.md
- 环境变量与密钥矩阵：https://github.com/yokingma/weclaws/blob/main/docs/manuals/env-and-secrets-matrix.md
- 外部运行时：<https://www.npmjs.com/package/@fastagent/cli> ・ <https://www.npmjs.com/package/@fastagent/sandbox-runtime>
- 元数据（Star / Fork / 时间 / 语言 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**未 clone、未安装、未部署、未扫码登录、未接微信、未接任何模型服务、未跑任何测试**；架构、分工、技能清单、沙盒工具链与部署参数均来自 README 与仓库文件自述；上游 npm 包 `@fastagent/*` 的维护者、许可与版本策略**未核验**。**个人微信自动化存在账号封禁与合规风险，部署前请自行评估。**
