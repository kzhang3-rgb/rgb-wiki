# paseo：跨桌面与移动编排多个编码 Agent 的开发环境

> 一句话定位（README 自述）：**Paseo 是面向桌面、移动、Web 与 CLI 的开源 agentic 开发环境**（"an open source agentic development environment for desktop, mobile, web, and CLI"）——打开桌面 App 就能干活：**Agent、编辑器、终端、diff、PR、浏览器在同一个窗口**，可以**同时跑很多个 Agent、每个在自己的 worktree 里**，手机端是**完整的原生 App（iOS / Android）**。它最值得记的是三件事：① **架构是 daemon + 多客户端**——本地守护进程（daemon）管理 Agent 与工作区，桌面 / 手机 / Web / CLI / SDK 都连它，于是"换设备继续干"或"把环境放到服务器上"都成立；② **自托管 + 无遥测**——Agent 跑在你的机器上、用你自己的凭证 / 配置 / 技能，README 明说**没有 telemetry、没有 tracking、不强制登录**；③ **Hub 是"daemon 之上的一层"**——把 GitHub / Slack / Discord 的活动变成触发器，让 Agent 自己开工。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（230 行）、`LICENSE`、`SECURITY.md`、`public-docs/why.md`、`public-docs/supported-providers.md`、`public-docs/hub/index.md`、完整文件树与元数据；**未 clone、未安装、未运行、未连任何 daemon、未配对手机、未审计源码**）
- 适用范围：**桌面 App（macOS / Windows / Linux）** + **原生移动 App（iOS / Android）** + **Web** + **CLI / TypeScript SDK**；服务端可跑 **Docker**（`ghcr.io/getpaseo/paseo`，端口 `6767`）；前置条件是**至少装一个已登录的编码 Agent CLI**

## 核心结论

**一句话定位（README 自述）**：**桌面 / 移动 / Web / CLI 的开源 agentic 开发环境**——把**多个编码 Agent 的并行运行、编排与审阅**收进一套"daemon + 客户端"里。

**三个最值得记的点：**

1. **daemon + 多客户端，把"跑 Agent 的机器"与"你手里的设备"解耦**（README / `why.md` 自述）：
   - 本地跑一个守护进程（**daemon**）管 Agent 生命周期与工作区；客户端通过 **WebSocket** 连它。
   - 两种连法：**直连**（daemon 监听网络地址，或走 Tailscale / 其它 VPN）与**中继**（daemon 主动出站连中继服务器、客户端在那边会合，**不用开端口**）。
   - 桌面 App **自动拉起本地 daemon**；在服务器 / VM 上可 `npm install -g @getpaseo/cli` 后跑 `paseo`。
2. **并行 Agent + 内建编排**（README 自述）：
   - **并行**：多个 Agent 同时跑，**各自在自己的 worktree / 分支**；可给每个 worktree 配独立端口的开发服务器。
   - **内建编排**：Agent 能通过 Paseo 工具**创建 worktree、启动别的 Agent、互相发消息**，**可以跨 provider**。
   - 工作区里内置**编辑器、diff、PR、终端、浏览器**（分屏自己排），浏览器跑在桌面 App 里。
3. **Hub：把外部事件变成 Agent 触发器**（`hub/index.md` 自述）：
   - 把 daemon 注册到 Hub 后获得：**由 GitHub / Slack / Discord 活动触发的自动开工**、**触发器可放在仓库里并从 CLI 部署**、**"到达了什么 / 匹配了什么 / 跑了什么"的全记录**、**团队共享视图**。
   - `paseo hub init` 在仓库里生成 `.paseo/triggers/*.yml`；可**自托管**（先内嵌数据库，需要再上 PostgreSQL），也可用**托管 Hub**（`hub.paseo.sh`，免费账号、无需信用卡）。

**个人判断（未验证）**：它切的是"**同时用多个编码 Agent 干活、且想跨设备继续**"的人——用 daemon 把"干活的那台机器"与"你手里的界面"分开。代价是**要接受"守护进程 + 客户端"这套部署形态**并自己管好它的边界（`SECURITY.md` 明确写了 daemon 默认只绑 loopback、未设密码时能连上就能控制），以及**个别 provider 只提供"全权"模式**（见下文 Antigravity）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [getpaseo/paseo](https://github.com/getpaseo/paseo) |
| 描述 | "Orchestrate multiple coding agents from desktop and mobile" |
| 归属 | 组织账号 `getpaseo`（ungh 组织接口可查到该组织，`description` 为空）；`isFork` 无、`isArchived` 无（页面未给出该字段） |
| 许可证 | **Apache-2.0**（`LICENSE` 11,708 字符，`Copyright (c) 2025-present Mohamed Boudra`）。**注意：该文件带自定义前言**——"第三方组件按各自许可、其余按 Apache-2.0"，**因此 GitHub 与 shields.io 都识别不出**（shields 返回 `not identifiable by github`，页面 `license` 字段为空）；README 末尾明写 "License: Apache-2.0"，文件正文即 Apache-2.0 |
| 主语言 | **TypeScript 97.5%**（shields.io，2026-11-13） |
| 技术栈 | npm workspaces monorepo：`packages/server`（daemon：Agent 进程编排 + WebSocket API + MCP server）、`packages/app`（**Expo**，iOS / Android / Web）、`packages/desktop`（**Electron**）、`packages/cli`、`packages/client`（`@getpaseo/client` TS SDK）、`packages/relay`（中继传输与加密）、`packages/protocol`、`packages/website`；另有 `expo-two-way-audio` / `plugin` / `highlight` 等包 |
| 工具链 | 根目录 `flake.nix` + `nix/`、`.mise.toml`、`.tool-versions`、`lefthook.yml`（Git hooks）、`vitest.config.ts`、`knip.json`、`.oxlintrc.json` / `.oxfmtrc.json`（oxlint / oxfmt）、`patches/`（11 个 `patch-package` 补丁，含 `@opencode-ai/sdk`、多个 `react-native-*`） |
| 关注度 | **23,945 Star / 2,938 Fork / 56 Watcher**（**页面值**，2026-11-13）；开放 Issue **986**、贡献者 **234**（shields.io，2026-11-13）。**ungh 镜像 23,916 / 2,935 / 57，略有差异** |
| 维护状态 | **2025-10-13 创建**；ungh `pushedAt` **2026-11-12**、页面 `updatedAt` 2026-11-12（**活跃，当天有推送**）；**30 条 Release**，最新稳定 **v0.15.4（2026-11-10）**，另有 **v0.16.0-beta.2（2026-11-11）**、**v0.16.0-beta.1（2026-11-10）** 等预发布（**发版节奏很快**） |
| 默认分支 | **`main`** |
| 体积 | `main` 分支 **6,749 个条目**（ungh.cc files API）：`packages` 5,738（`app` 3,218 / `server` 1,345 / `website` 308 / `cli` 262 / `desktop` 219 / `protocol` 159 / `expo-two-way-audio` 61 / `plugin` 55 / `client` 52 / `highlight` 41 / `relay` 30）、`plugins` 369、`plugin-examples` 154、`public-docs` 142、`fastlane` 102、`docs` 70、`scripts` 54、`assets` 44、`.github` 17、`patches` 11、`skills` 6、`docker` 5、`nix` 4、`.claude` 3、`.agents` 2 |
| 根目录要点 | `README.md` + `README.zh-CN.md` / `.ja.md` / `.ko.md`（**四语**）、`AGENTS.md`、`CLAUDE.md`、`CONTRIBUTING.md`、`CHANGELOG.md` + `CHANGELOG.zh-CN.md`（约 32.4 万字符）、`SECURITY.md`、`paseo.json`、`cli-client-id` |
| 官网 / 社区 | 官网 <https://paseo.sh> · 应用域名 `app.paseo.sh` · 托管 Hub <https://hub.paseo.sh> · 插件站 <https://paseo.sh/plugins> · X `@moboudra` · Discord `discord.gg/jz8T2uahpH` · Reddit `r/PaseoAI` |
| 相关项目 | `getpaseo/paseo-relay`（**Elixir** 写的官方分布式中继）· `paseo-vscode`（VS Code 扩展，Marketplace `hinnes.paseo-vscode`） |

## 能力结构（README 与官方文档自述）

### 1. 并行与编排

| 能力 | 说明 |
| --- | --- |
| 并行 Agent | 多个 Agent 同时跑，**每个在自己的 worktree**；给每个 worktree 配独立端口的开发服务器 |
| 内建编排 | Agent 可**创建 worktree、启动其它 Agent、互相发消息**，**跨 provider** |
| 完整开发流 | 在分屏里改文件、看 diff、开 PR、跑终端；浏览器内置在桌面 App |
| 自托管 | Agent 跑在你的机器、用**你的工具 / 配置 / 技能**，不上传你的代码 |

### 2. provider（两类）

- **原生支持（装好 CLI 并登录即可用）**：**Claude Code**、**Codex**、**OpenCode**、**Pi**、**Antigravity**（走你已装的 `agy` CLI）、**Muse Code**（Meta 的终端编码 Agent，**随 Paseo 打包为 provider 插件**）。
- **ACP 目录（应用内一键装底层 CLI）**约 **38 项**：Agoragentic、Amp、Auggie CLI、Autohand Code、Cline、Codebuddy Code、CodeWhale、Cortex Code（Snowflake）、Corust Agent、crow-cli、Cursor、DeepAgents、DimCode、Dirac、Factory Droid、fast-agent、**fx（Vercel Labs）**、Gajae Code、Gemini CLI、GitHub Copilot、GLM Agent、goose（Block）、Grok（xAI）、Hermes Agent、**Hoplite**、Junie（JetBrains）、Kilo Code、Kimi Code CLI、Minion Code、Mistral Vibe、Nova、Poolside、Qoder、Qwen Code、siGit Code、Stakpak、**TRAE CLI**、VT Code 等。文档明说：**应用内目录才是"权威且带版本锁定"的来源**，未列出者可用 **Custom providers** 手动接。
- **口径差异**：README 首屏把 **Copilot** 与 Claude Code / Codex / OpenCode / Pi / Antigravity / Muse Code 并列（"One interface for …"），而 provider 文档把 Copilot 归在 **ACP 目录**里；**看具体接入方式时以文档为准**。

### 3. 插件

- 插件**跑在 daemon 上**，**装一次在所有客户端都可见**（桌面 / Web / iOS / Android 同一份 UI）；安装 `paseo plugin add owner/slug`，也可从 Git 或本地目录装。
- 能扩展的面：**UI**（屏幕、侧栏项、工作区面板、命令中心项、斜杠命令、composer pills、附件来源、时间线条目、主题）、**Agent 生命周期**（改配置 / 环境 / MCP server、应答权限、轮次结束后跟进）、**provider**（把某个编码 Agent 加为 provider）。
- 仓库内含 **`plugin-examples/` 24 个示例**（`buttons`、`diff-watch`、`linear`、`media-stream`、`pdf-panel`、`provider-acp-transformer`、`provider-direct`、`sidebar-views`、`timeline-items` 等）与 **`plugins/` 14 个官方插件**（`antigravity-provider`、`muse-provider`、`fx-provider`、`hoplite-provider`、`worktree-include`，以及一组用量来源插件：`claude-usage-source` / `codex-usage-source` / `copilot-usage-source` / `cursor-usage-source` / `grok-usage-source` / `kimi-usage-source` / `minimax-usage-source` / `zai-usage-source` / `opencode-go-usage-source`）。

### 4. CLI、SDK 与技能

- **CLI**（"应用里能做的，终端里都能做"）：

```bash
paseo run --provider claude/opus-4.6 "implement user authentication"
paseo run --provider codex/gpt-5.5 --worktree feature-x "implement feature X"

paseo ls                           # 列出运行中的 Agent
paseo attach abc123                # 实时看输出
paseo send abc123 "also add tests" # 追加任务

# 在远端 daemon 上跑；--cwd 是那台主机上的路径
paseo run --host workstation.local:6767 --cwd /workspace "run the full test suite"
```

- **TypeScript SDK**：`@getpaseo/client`（`createPaseoClient({ url: "ws://127.0.0.1:6767/ws" })`，可 `agents.create(...)` 后 `waitForFinish()`），用于做 issue 集成、看板、编排服务。
- **技能**：`npx skills add getpaseo/paseo` 后可在任意 Agent 会话里用 **`/paseo-handoff`**（在 Agent 之间交接，例如用 Claude 规划、交给 Codex 实现）、**`/paseo-advisor`**（拉一个 Agent 当"第二意见"而不把活交出去）、**`/paseo-committee`**（两个取向相反的 Agent 组委员会做根因分析与方案）。仓库 `skills/` 下共 6 个 `SKILL.md`（另含 `paseo` / `paseo-help` / `paseo-plugin`）。

### 5. 部署形态

```bash
# 桌面 App（推荐）：从 paseo.sh/download 或 GitHub Releases 装，打开即自动起本地 daemon
# 服务器：装 CLI 起 daemon
npm install -g @getpaseo/cli
paseo

# Docker：daemon + 自托管 Web UI
docker run -d --name paseo \
  -p 6767:6767 \
  -e PASEO_PASSWORD=change-me \
  -v "$PWD/paseo-home:/home/paseo" \
  -v "$PWD:/workspace" \
  ghcr.io/getpaseo/paseo:latest
```

- 起 daemon 时会问**是否启用端到端加密中继**；拒绝则改走 **TCP / Tailscale / 其它 VPN 直连**。
- Docker 里官方镜像**默认以非 root 的 `paseo` 用户**跑 daemon 与 Agent（挂载的工作区与凭证对 Agent 仍完全可用）。
- 仓库另有 `docker/docker-compose.example.yml` 与 `docker/Dockerfile.agents.example`（示例：在基础镜像上叠加你用的 Agent CLI 并注入凭证）。

### 6. 语音与跨设备

- **语音**：口述任务或语音模式对话；**语音识别与合成默认在本机跑**，可在设置里切云服务（`public-docs/voice.md`）。
- **跨设备**：iOS / Android / 桌面 / Web / CLI 连同一个环境；桌面上 **Settings → your host → Pair Device** 生成手机配对入口。

## 使用前需要注意

- **许可别信自动识别**：`LICENSE` 带自定义前言，**GitHub 与 shields.io 都识别不出**（shields 显示 `not identifiable by github`）；**README 明写 Apache-2.0**，文件正文也是 Apache-2.0 + 第三方声明。**引用许可时以 LICENSE 文件为准**。
- **daemon 的本地信任边界很宽**：默认只绑 `127.0.0.1`，但 `SECURITY.md` 明说——**未设密码时，任何能连到该 socket 的东西都能控制 daemon**，而 **loopback 对同机其它用户与某些转发工具可达**。已连接的客户端被当作"**以 daemon 用户身份操作的受信操作者**"：文件预览可读 **daemon 进程本身能读的任意常规文件**（workspace 相对路径只是 UI 便利，**不是安全边界**）。**暴露到 loopback 之外（绑 `0.0.0.0`、隧道 / 反代、Docker 发布端口）要自己负责隔离与加固，并强烈建议设密码**。密码走 `auth.password` 或 `PASEO_PASSWORD`，**bcrypt 存哈希**；daemon 每次运行写 `$PASEO_HOME/local-credential`（`0600`，退出时删除），**同用户进程仍可读它**——所以密码防的是网络客户端与其它 OS 用户，**不防以 daemon 用户身份运行的进程**。
- **中继被设计为"不可信"，但旧客户端有可重放弱点**：新版握手用 **Noise `NK`（`Noise_NK_25519_ChaChaPoly_SHA256`）** + 每条记录计数器 nonce 的 **ChaCha20-Poly1305**；旧客户端回落到 **Curve25519 ECDH + XSalsa20-Poly1305（NaCl `box`）** 与**随机 nonce**。`SECURITY.md` **自己指出**：该旧构造**既无前向性也无防重放**，"**仍接受旧客户端的 daemon 会把录下的旧会话（hello、凭证、命令）当新会话接受**"；建议所有客户端升级后设 `daemon.relay.requireProtectedSecurity: true`。中继可见的只有 **IP、时间、消息大小、会话 ID，以及只含公钥的明文握手帧**。
- **配对链接就是信任锚**：配对 URL 把 daemon 公钥放在 **URL fragment**（`https://app.paseo.sh/#offer=…`，fragment 不发给服务器），daemon 首次运行在 `$PASEO_HOME/daemon-keypair.json` 生成 **Curve25519 长期密钥对**（`0600`）。**文档明说"像密码一样对待它"**；配了 daemon 密码时，中继客户端在**加密的 `hello`** 里带密码，中继看不到。
- **Antigravity 只有"全权"一种权限模式**：文档写明 Paseo 以 **`agy --dangerously-skip-permissions`** 启动它——**每个工具调用（含 shell 命令）都不询问**，**每次会话开始都弹一条警告**（重开聊天也弹）；它还**明说这不是 Google 背书的集成**，且**重启 daemon 后 Paseo 的 transcript 会从头开始**（`agy` 不回放历史），Paseo 的 MCP 工具与 steering 在该 provider 下不可用。
- **"云工作区"类 provider 会缺本地能力**：以 **Hoplite** 为例，Agent 跑在**云端工作区**，**你本地的文件与 Paseo 的 loopback MCP 工具在那边都不可用**。
- **插件即"在你机器上运行的第三方代码"**：插件跑在 **daemon 所在机器**并进入**已连接的客户端**；README 直接写"**只安装你信任的代码**"。仓库自带 `plugin-examples/` 里也有 `local-plugin`（本地目录装插件）这类例子。
- **HTML 预览有一个已知缺口**：`.html` 在文件面板里**会作为页面渲染**——即 **Agent 写的、或你 clone 的仓库里带的标记会执行**。文档说沙箱用**不透明 origin + 只允许内联脚本 / 样式**，拒掉远程脚本 / 字体 / 图片 / 媒体、`fetch` / XHR / WebSocket / beacon、表单提交、插件、嵌套 frame，且无法访问 Paseo 的 DOM、storage 与 cookie 会抛错；**但承认当前浏览器没有 CSP 能拦住"文档自我导航"**（`navigate-to` 已从 CSP Level 3 移除、`<meta http-equiv="refresh">` 不需要脚本），**因此恶意页面仍可能靠跳转把预览内可获得的信息（自身内容、浏览器 / 设备属性、你在页面里的输入、你的 IP）带出去**；原生构建拒掉首次文档后的所有导航，**但那是 JS 层决策（Android 超时还会回落放行）**，属"**强缓解、不是保证**"。**不信任的页面请用 Source 视图（不执行任何东西）**。
- **跨仓库工作区有显式安全门**：从**另一个仓库**检出变更请求时，Paseo **不会自动执行该工作区的 `paseo.json` 设置、自动终端、命名脚本与 teardown**，要你显式跑一次 setup；该决定**按工作区记住、新提交不再重问**。同仓库变更、普通分支、本地工作区、Agent 启动、终端、显式 shell 命令、元数据生成说明**不受此门限制**。
- **README 有赞助位**："Sponsors" 表含 **hoplite.sh（cloud coding agents）** 一格与两格 "Become a sponsor"；文末 "Support Paseo" 自述**单人维护、靠赞助**，企业可月度赞助并把 logo 放到 **README 顶部与官网 / 文档**。**阅读时把技术说明与赞助文案分层**。
- **"单人维护"与贡献者数口径不同**：README 自述 "built by one person"，而 shields.io 报 **234 贡献者**、页面开放 Issue **986**——**两者口径不同，不宜互相替代**。
- **我只读了 README、许可、安全说明与部分官方文档**：**未 clone、未安装、未跑 daemon、未连任何客户端、未配对手机、未试中继、未审计源码**；架构、能力与安全描述**均来自 README 与 `public-docs/`**，**未逐一验证**。

## 什么时候会再看

- 想**同时跑多个编码 Agent**（每个在自己的 worktree）、并在**编辑器 / diff / PR / 终端 / 浏览器**之间不切窗口时。
- 想搭"**本地 daemon + 多客户端**"这套架构——桌面 / 手机 / Web / CLI **连同一个环境**，或把环境放到服务器 / Docker 上时。
- 想研究"**Agent 互相编排**"（Agent 建 worktree、启别的 Agent、跨 provider 对话）以及把它**固化成技能**（`/paseo-handoff` / `/paseo-advisor` / `/paseo-committee`）的写法时。
- 想参考"**把 GitHub / Slack / Discord 活动变成 Agent 触发器**"、"**触发器放仓库、CLI 部署**"（Hub）这类做法的形态时。
- 想做**端到端加密的中继设计**（daemon 出站连接、公钥放 URL fragment、Noise NK 握手、**并如实写出旧客户端可重放这一弱点**），或参考"**daemon 安全说明该写到什么颗粒度**"时。
- 想对照 **AI CLI 生态的不同层次**：[opencode](opencode-开源终端编码Agent.md)（CLI 本体）· [pebrel](pebrel-给AI-CLI用的GPU加速终端与SSH工作区.md)（宿主终端）· [openclaw](openclaw-自托管个人AI助手.md)（自托管助手）· [codeburn](codeburn-本地追踪AI编码Token用量与成本.md)（用量观测）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **"编排层"与"Agent 本体"**：[opencode：开源终端编码 Agent](opencode-开源终端编码Agent.md)——OpenCode 是 Paseo 的**原生 provider 之一**（Paseo 的 provider 文档与 `patches/@opencode-ai+sdk` 里都有对应痕迹）；本篇做的是**把多个这样的 CLI 编排进同一个工作区**。可对照"**Agent 本体**"与"**编排它们的环境**"。
- **同为"AI CLI 的工作台"，形态不同**：[pebrel：给 AI CLI 用的 GPU 加速终端与 SSH 工作区](pebrel-给AI-CLI用的GPU加速终端与SSH工作区.md)——那篇是**原生终端 + SSH / SFTP**（一个窗口收下多个 CLI 会话，侧重本机与远程主机）；本篇是 **daemon + 桌面 / 手机 / Web / CLI 客户端**（侧重跨设备、worktree 并行与编排）。一个偏"**宿主终端**"，一个偏"**跨设备开发环境**"。
- **同属"自托管 + 不上传"的取向**：[openclaw：自托管的个人 AI 助手](openclaw-自托管个人AI助手.md)——两者都跑在你自己的机器上、都拿"**没有遥测 / 不强制登录**"当卖点；差别在本篇专注**编码 Agent 的编排与工作区**，那篇是更宽的个人助手。
- **"跑与编排 Agent" ↔ "量它们花了多少钱"**：[codeburn：本地追踪 AI 编码 Token 用量与成本](codeburn-本地追踪AI编码Token用量与成本.md)——Paseo 的官方插件仓库里就有一组 **`claude-usage-source` / `codex-usage-source` / `copilot-usage-source` / `cursor-usage-source` / `grok-usage-source` / `kimi-usage-source` / `minimax-usage-source` / `zai-usage-source` / `opencode-go-usage-source`** 插件；两篇正好是这条链的两端。
- **都是"编排多个 Agent"，面向不同**：[dontbemichael：用现有订阅跑一屋子 Agent 的本地多 Agent 框架](dontbemichael-用现有订阅跑一屋子Agent的本地多Agent框架.md)——本篇是**面向开发者的多 Agent 工作台**（桌面 + 移动），那篇是**面向业务所有者的 AI 办公室**（角色 + 邮箱 + 审批）；可对照"开发者编排"与"业务组织"。
- **都在"多 provider"这件事上用力，层不同**：[aimux：用 Rust 写的统一 LLM 接入层](aimux-用Rust写的统一LLM接入层.md)——paseo 在 **Agent 编排层**做多 provider（原生 Claude Code / Codex / OpenCode 等 + ACP 目录），aimux 在**服务接入层**做多 provider（统一各家 HTTP API）；可对照"**编排层的多 provider**"与"**接入层的多 provider**"。
- **"跑与编排 Agent" ↔ "回放它们到底做了什么"**：[agentplay：把编码 Agent 会话回放成地图与时间线的本地查看器](agentplay-把编码Agent会话回放成地图与时间线的本地查看器.md)——那是**事后回放某个 Agent 的会话**（读本机 transcript → 脱敏地图 + 时间线，本地查看器）；本篇是**把多个编码 Agent CLI 编排进同一工作区**（跨设备、worktree 并行）。两者覆盖编码 Agent 的不同环节，可对照"**编排运行**"与"**复盘观测**"。
- **同为"桌面 + 移动编排多个编码 Agent"，授权与遥测取向不同**：[agent-orchestrator：编排编码 Agent 编队的桌面与移动工作台](agent-orchestrator-编排编码Agent编队的桌面与移动工作台.md)——形态最接近（都是桌面 + 手机看一队编码 Agent），但本篇是**自托管、无遥测、个人维护**，那篇是**商业公司出品的开放核心**（含机房侧 `contracts/cloud/openapi.yaml`、`public-boundary.yml` 与私有子模块）、**默认开遥测**、并有市场化的 Cloud 托管面；可对照"个人自托管"与"公司开放核心 + 托管增值"两种取向。

## 参考来源

- 仓库主页与 README（230 行；核验日期 2026-11-13）：https://github.com/getpaseo/paseo
- 许可证：`LICENSE`（Apache-2.0，11,708 字符，含第三方声明前言；`Copyright (c) 2025-present Mohamed Boudra`）
- 安全模型：`SECURITY.md`（客户端-服务端架构、中继威胁模型与 Noise NK 握手、本地 daemon 信任边界与密码准入、DNS rebinding 的 Host 校验、HTML 预览沙箱与已知缺口、Agent 认证、"跨仓库工作区"安全门、漏洞上报邮箱 `hello@moboudra.com`）
- 官方文档（`public-docs/`，142 个文件，多为 `*.md` + `*.zh-CN.md` 双语）：`why.md`、`supported-providers.md`、`hub/index.md`、`plugins/`、`sdk/`、`worktrees/`、`connectivity.md`、`voice.md`、`mcp.md`、`schedules.md` 等
- 仓库内工程文档：`docs/`（70 个，含 `architecture.md`、`docker.md`、`providers.md`、`design.md`、`data-model.md`、`permissions.md`、`release.md`、`plugins.md`）；`CHANGELOG.md`（约 32.4 万字符，另有 `CHANGELOG.zh-CN.md`）
- 官网与文档站：<https://paseo.sh> · 插件站 <https://paseo.sh/plugins> · 托管 Hub <https://hub.paseo.sh>
- 相关项目：`getpaseo/paseo-relay`（Elixir 分布式中继）· `paseo-vscode`（VS Code 扩展，Marketplace `hinnes.paseo-vscode`）
- 完整文件树（6,749 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**略有差异**）；Issue 数 / 贡献者数 / 主语言取自 shields.io（2026-11-13）；Release 列表取自 ungh.cc releases API（同日，共 30 条）

以上信息核验于 2026-11-13。**未 clone、未安装、未运行、未连任何 daemon、未配对手机、未审计源码**；架构、能力与安全说明均来自 README 与官方文档，**未逐一验证**；README 中的赞助内容仅作事实记录，**不构成推荐**。
