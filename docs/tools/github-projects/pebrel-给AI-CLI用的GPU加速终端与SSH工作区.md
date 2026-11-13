# pebrel：给 AI CLI 用的 GPU 加速终端与 SSH 工作区

> 一句话定位（README 自述）：**"一个 GPU 加速的终端、SSH 工作区，以及你的 AI CLI 会话的家"**（A GPU-accelerated terminal, SSH workspace, and home for your AI CLI sessions）——用 **Rust + GPUI** 写的原生桌面终端，把**本地 shell、远程主机、文件管理、AI 命令行工具（Claude Code / Codex 等）**收进同一个窗口。它最值得记的是三件事：① **它不是"再写一个终端"，而是把"你同时开着好几个 AI CLI 干活"这个场景做成产品**——每个 CLI 有专属图标与活动状态、答案可抓进阅读器、支持分屏与按窗格工作目录；② **它的智能补全是本机引擎、不发 AI 请求**（从当前 Git 仓库、npm 脚本、SSH 配置、文件系统、WSL 发行版里发现候选）；③ **旧名 Nebula**——内部 cargo 包仍叫 `nebula`，但**应用与命令改叫 `pebrel`**，是一个改名中的项目。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（334 行 / 18,108 字符）、`LICENSE`（GPL-3.0）、完整文件树与元数据；**未 clone、未编译、未安装、未运行、未连任何 SSH 主机、未试中继**）
- 适用范围：**Windows 10 1809+ / 11（x64）** 为首发平台；**Linux Preview（glibc 2.35+）**、**macOS Preview（target 14+，arm64/x64）**；构建需**固定版本的 Rust 工具链** + 平台依赖

## 核心结论

**一句话定位（README 自述）**：**GPU 加速的终端 + SSH 工作区 + AI CLI 会话的家**（Rust + GPUI，跨 Windows / macOS / Linux）。

**三个最值得记的点：**

1. **它是"AI CLI 时代的工作台"，不是通用终端**（README 自述）：
   - **AI CLI 有专属图标与活动状态**：Claude Code、Codex 及**其它被识别的 CLI** 各自区分；支持的 **hook 事件**能给出更精确的进度与"需要你注意"信号。
   - **通知跟着来源窗格走**，点通知**跳回那个终端**；抓取的 Claude Code / Codex **答案在阅读器里打开**（Markdown、公式、源码、本地图片预览），"Copy source"保留完整原始回答。
   - **剪贴板图片存成 PNG 并插入其路径**（本地 / WSL / SSH 会话）。内联终端图片需要 CLI 输出走 **OSC 1337 / Kitty 协议（直出 RGB/RGBA）/ Sixel（7-bit DCS）**。
   - **分屏**：侧栏或顶栏标签、可拖拽分屏、**按窗格独立工作目录**、保存的**工作区布局**、**可选的 AI 会话恢复**。
2. **智能补全是"本机引擎"，不靠 AI**（README 自述）：
   - 候选来自**你当前 Git 仓库、项目脚本、SSH 配置、文件系统**，Windows 上还**补全已注册的 WSL 发行版**——**不需要 shell 补全插件，也不发 AI 请求**。
   - 三种模式：**Inline**（Tab 接受内联建议）/ **List**（Tab 接受列表选中项）/ **Hybrid**（→ 接受内联；Tab 打开列表）。
   - README 明确区分"**历史补全**"（回忆你已跑过的命令）与"**发现式补全**"（从当前环境发现候选）。
3. **远程与文件能力做全了**（README 自述）：
   - **SSH 主机库**：**按名称 / 地址 / 组 / 标签 / 备注搜索**（多关键词须全匹配、不区分大小写）；支持**跳板 / 代理 / 私钥 / 键盘交互认证 + 主机密钥校验**。
   - **SFTP**：浏览、上传下载、文件夹传输、进度、取消。
   - **本地文件浏览 + Git 操作**与终端并排。

**个人判断（未验证）**：它切的是"**同时开着好几个 AI CLI 干活的人**"这一人群——把**终端、SSH、SFTP、文件、AI 会话阅读器**合成一个原生窗口，并用**本机补全（不烧 token）**降低操作成本。**代价是平台成熟度不均**（Windows 是主力，Linux / macOS 仍是 Preview），且 **GPL-3.0 会对再分发有约束**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Kuddev/pebrel](https://github.com/Kuddev/pebrel) |
| 旧名 | **Nebula**（README 明写 "Pebrel (formerly Nebula)"；**内部 Cargo 包仍叫 `nebula`**，桌面端与应用/命令叫 `pebrel`；旧渲染器仅通过显式 `legacy-shell` feature 提供） |
| 描述 | "A GPU-accelerated terminal, SSH workspace, and home for your AI CLI sessions" |
| 归属 | 组织 / 用户账号 `Kuddev`；`isFork: false`、`isArchived: false` |
| 许可证 | **GPL-3.0**（`LICENSE` 35,149 字符）——**强 copyleft；再分发 / 派生需遵守 GPL 义务**；另含 `THIRD-PARTY-NOTICES` 与 `licenses/` 目录保存第三方组件声明 |
| 主语言 | **Rust 83.2%**（shields.io，2026-11-13） |
| 技术栈 | **Rust + GPUI**（README 明确 "GPUI is the product interface"）；**Lua 配置**（`require 'pebrel'`，保留 TOML）；构建命令 `cargo build --release --locked -p nebula --bin pebrel --features gpui-shell` |
| 关注度 | **3,766 Star / 218 Fork / 4 Watcher**（**页面值**，2026-11-13）；开放 Issue **97**、贡献者 **42**（shields.io，2026-11-13）。**ungh 镜像 3,752 / 4 / 215，略有差异** |
| 维护状态 | **2026-07-05 创建**；ungh 报 `pushedAt` **2026-11-11**、页面 `updatedAt` 2026-11-11（**活跃**）；页面报 **42 Release / 43 tags**（发版频繁，README 提到 **v2.1.1**） |
| 默认分支 | **`main`** |
| 体积 | `main` 分支 **2,759 个条目**（ungh.cc files API）；顶层以 **`nebula_app`（1,101）**、**`mobile`（475）**、**`architecture`（288）**、`nebula_terminal`（257）、`third_party`（218）、`scripts`（140）、`docs`（70）、`extra`（68）、`nebula_settings`（22）、`.github`（18）、`nebula-completions`（18）、`nebula_gpui`（15）、`packaging`（10）、`assets`（10）、`licenses`（8）、`nebula_config_derive`（7）、`tools`（5）、`nebula_hook`（3）为主 |
| 官网 / 社区 | 官网 <https://pebrel.com> · Discord [discord.gg/VFn4rcxmhn](https://discord.gg/VFn4rcxmhn) · linux.do · 另有 **AtomGit 镜像**（atomgit.com/kettys/pebrel） |

## 能力结构（README 自述）

| 板块 | 内容 |
| --- | --- |
| 终端与会话 | 侧栏 / 顶栏标签、可拖拽分屏、按窗格工作目录、保存布局、**可选 AI 会话恢复**（WSL/Linux 上 Codex hook 未上报时可用会话元数据补 ID）；Windows 上可选**后台驻留**让会话在关窗后存活 |
| 智能补全 | 从 **Git 分支 / npm 脚本 / SSH 别名 / WSL 发行版 / 文件系统**发现候选（**本机引擎，无 AI 请求**）；三种接受模式（Inline / List / Hybrid）；另有历史补全 |
| SSH 与文件 | SSH 主机库（按名 / 址 / 组 / 标签 / 备注搜索）+ 跳板 / 代理 / 私钥 / 键盘交互认证 + 主机密钥校验；**SFTP** 浏览上传下载、文件夹传输、进度与取消；本地文件浏览 + Git 动作 |
| AI CLI 工作流 | Claude Code / Codex 等**专属图标与活动状态**；hook 事件提供更精确进度；通知跟随来源窗格并可回跳；**答案抓进阅读器**（Markdown / 公式 / 源码 / 本地图）；**剪贴板图片存 PNG 并插路径** |
| 原生界面 | GPU 加速 GPUI、明暗主题、背景与透明度；**11 种 UI 语言**；可搜索设置、命令面板、**Lua 配置（校验 + 热重载）+ 保留 TOML**；**原生数学排版，无需 WebView** |

**自托管中继（手机连接）**：手机上保存一个 SSH 主机（密码或私钥认证），在桌面 **Settings → Phone connection → Relay** 里选该主机，生成手机配对二维码。安装说明见 `mobile/relay-native/INSTALL.md`。**注意**：**原始 v2.1.1 Release 资源不含离线中继套件**；从含安装器的源码检出可用 `mobile/tools/package_manual_relay.py` 从**已校验的 APK** 生成**双架构 Linux 套件 + SHA256**（该工具**不下载可执行文件、不发布或替换 Release 资源**）。

## 使用前需要注意

- **平台成熟度不均**：**Windows** 是主力（安装器 `/便携 zip`，且提供"旧命名 Windows 安装器"兼容老的自动更新客户端）；**Linux / macOS 仍是 Preview**（包名带 `-preview`）。macOS 采用 ad-hoc 签名，**首次启动可能要在"系统设置 → 隐私与安全性"里点"仍要打开"**；README 明说 **"deployment target 本身不等于在每个更老的系统版本上都验证过"**。
- **GPL-3.0**：这是**强 copyleft**；若你要基于它做派生或再分发，需遵守 GPL 义务（**与 MIT/Apache 类项目的使用姿势不同**）。
- **README 含赞助位与推广链接**：两处赞助（**AIAPI**、**Sidrune AI**）都带**推广链接与邀请代码**（如 Sidrune 注册送 $3 额度），属**商业推广内容**；另含**微信社群二维码**（**标注有效期至 2026-11-11**）。**阅读时把"技术说明"与"赞助文案"分层**。
- **手机中继要自建**：手机连接依赖**自托管中继**，且**原始 v2.1.1 Release 不含离线中继套件**——需要自己按 `mobile/relay-native/INSTALL.md` 处理（或从源码用脚本从已验证 APK 生成套件）。**打通手机链路有额外工作量**。
- **配置语言在迁移中**：Lua 配置为新方式（`require 'pebrel'` + `pebrel.config_builder()`），**TOML 仍受支持**；**无效热重载会保留上一份有效配置**。
- **改名中**：对外是 `pebrel`，**但内部 Cargo 包、部分目录（`nebula_app` / `nebula_terminal` / `nebula_gpui` / `nebula_hook` 等）与文档路径仍是 `nebula`**；看源码 / 构建命令时要按这个对应关系理解。
- **我只读了 README、许可与文件树**：**未 clone、未编译、未安装、未运行、未连任何 SSH 主机、未试中继、未验证补全与阅读器行为**；功能描述均来自 README，**未逐一验证**。

## 什么时候会再看

- 想要一个**面向 AI CLI（Claude Code / Codex）的原生桌面工作台**——把终端、SSH、SFTP、文件、AI 会话阅读器合到一起时。
- 想参考"**本机智能补全**"（从 Git / npm / SSH / 文件系统 / WSL 发现候选，**不发 AI 请求**）的实现思路时。
- 想研究"**GPU 加速的原生 UI 框架 GPUI**"在实际应用里的样子（Rust + GPUI 的桌面终端）时。
- 想参考"**旧名迁移中的仓库**"如何同时处理**内部包名与对外品牌**的差异，以及**离线中继套件不随 Release 发布**时的补救脚本做法时。
- 想对照终端 / AI 助手其它形态：[opencode](opencode-开源终端编码Agent.md)（终端编码 Agent）· [openclaw](openclaw-自托管个人AI助手.md)（自托管个人 AI 助手）· [postcat](postcat-终端里的键盘优先HTTP调试TUI.md)（终端 TUI 调试工具）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同样把"终端"当主战场的工具**：[postcat：终端里的键盘优先 HTTP 调试 TUI](postcat-终端里的键盘优先HTTP调试TUI.md)——那篇是**纯 TUI**（Rust / ratatui，键盘优先）；本篇是**GPU 加速的原生桌面终端 + SSH 工作区**。一个偏"**塞进终端的最小工具**"，一个偏"**把多会话收进一个原生窗口**"，可对照两种终端取向。
- **同为"围绕 AI CLI 会话"的周边**：[opencode：开源终端编码 Agent](opencode-开源终端编码Agent.md)与 [openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——那两篇是**AI CLI / 助手本体**；本篇是**承载这些 CLI 的工作台**（区分图标、抓答案、看活动状态）。可对照"**AI CLI 生态**"里**工具本体**与**宿主终端**的分工。
- **同属"本地优先 / 不上传"的取向**：[codeburn：本地追踪 AI 编码 Token 用量与成本](codeburn-本地追踪AI编码Token用量与成本.md)——那篇只读本机会话文件算成本；本篇的补全也是**本机引擎、不发请求**。两者都把"**数据留在本机**"当默认，可对照。

## 参考来源

- 仓库主页与 README（334 行 / 18,108 字符；核验日期 2026-11-13）：https://github.com/Kuddev/pebrel
- 许可证：`LICENSE`（GPL-3.0，35,149 字符）· 第三方声明：`THIRD-PARTY-NOTICES`、`licenses/`
- 其它文档：`INSTALL.md` · `docs/lua-configuration.md` · `docs/internationalization.md` · `docs/answer-reader-language.md` · `mobile/relay-native/INSTALL.md`
- 官网：<https://pebrel.com> · AtomGit 镜像：<https://atomgit.com/kettys/pebrel>
- 完整文件树（2,759 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）

以上信息核验于 2026-11-13。**未 clone、未编译、未安装、未运行、未连任何 SSH 主机、未试中继**；功能、平台支持与配置方式均来自 README，**未逐一验证**；README 中的赞助文案与推广链接仅作事实记录，**不构成推荐**。
