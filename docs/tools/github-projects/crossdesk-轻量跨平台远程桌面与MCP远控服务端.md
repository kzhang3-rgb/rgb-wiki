# CrossDesk：轻量跨平台远程桌面（自带 MCP 远控服务端）

> 记录一个自研远控栈的国产开源项目——它先在 Windows / macOS / Linux / 移动端 / 浏览器 / 终端六端做远控，然后在 `apps/mcp/` 里给 Agent 开了一个 MCP 服务端，把"让模型操作远端桌面"这件事做成了**用户先授权、模型后动手**的流程；同时记下它的分支纪律（`main` 已停）、GPL-3.0-only 许可、以及那份异常详细的安全边界自述。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README 346 行、`apps/mcp/README.md` 232 行、`PRIVACY.md` 目录、LICENSE 678 行、30 个分支、默认分支提交、目录树 973 个文件、Release 列表与最新资产；**未安装、未运行、未连接任何远端、未审计源码**）
- 适用范围：需要自建 / 自托管远控的个人与小团队；或想参考"如何给 Agent 设计一个需要用户授权的远控 MCP 接口"

## 核心结论

**它不是 VNC / RDP 的封装，而是自研的一套实时音视频 + 信令栈。** 底层是作者自己的 [MiniRTC](https://github.com/kunkundi/minirtc)，主语言 C++、构建系统 Xmake；连接方式有两条——**ID 连接**（经信令服务器）与**局域网 IP 直连**（默认端口 52888，六位密码，`docs` 里明确写"直连会话不依赖云端信令服务"）；传输上支持 P2P / TURN 中继 / SRTP 媒体加密，服务端可以自托管。

**最值得单独写下来的是 `apps/mcp/`。** 这是一个真实落地的 MCP 服务端（包名 `@chunkdash/crossdesk-mcp` 0.1.0，绑定原生 CLI 0.2.0），让本机 Agent **观察并操作**远端 CrossDesk。它对"把桌面交给模型"这件事的处理，是我在这个知识库里见到的最克制的一份：

- **模型拿不到设备清单，也不能自己挑目标。** `crossdesk_connect` 只接受**用户已授权设备的别名**；模型无法传任意 `device_id` 绕过面板确认。密码在用户自己的浏览器面板或终端里输入，README 直接写"**不要把密码发给 Agent**"。
- **凭据没有明文降级。** 密码存 macOS Keychain / Windows 凭据库 / Linux Secret Service，**凭据库不可用时返回明确错误，不落明文文件**。
- **MCP 工具里没有 shell、没有剪贴板、没有文件操作。** 动作只有 `move / click / double_click / scroll / drag / key_press / hotkey / type_text`。
- **"发送完成"≠"执行成功"。** `submitted` 只说明本地发送完成，**远端没有执行 ACK**；超时、断网、取消会返回 `outcome_unknown` / `side_effect_possible`，并明确**禁止自动重放**。
- **作者自己划了边界**：`apps/mcp/README.md` 里写明"能够读取同用户进程 / 文件或执行任意本机代码的 Agent 不受此边界隔离"，也写明"app-only 标记本身不是可信身份凭据"。这种主动声明防护上限的写法很少见。

**许可要注意**：`LICENSE` 是 **GPL-3.0-only**（678 行，含两行自定义头 `CrossDesk` + `SPDX-License-Identifier: GPL-3.0-only`）。GitHub API 因此把许可证识别成 `Other` / `NOASSERTION`，**必须看 LICENSE 文件本身**。GPL-3.0 传染性强于 MIT/Apache，二次分发与衍生作品要按 GPL 处理。README 指向的 `crossdesk-server`、`crossdesk-web-client` 是两个**独立仓库**，其许可我没有核验。

**一个容易踩的坑是分支**：仓库共有 **30 个分支**，而**默认分支被设成了 `mcp`**（一个特性分支）。`main` 的最后一次提交是 **2026-02-12**，经 `compare/main...mcp` 核验**落后 687 个提交**——**按 `main` 克隆会拿到半年前的代码**。看这个项目必须看默认分支。

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [kunkundi/crossdesk](https://github.com/kunkundi/crossdesk) |
| 归属 | 个人账号 kunkundi；`LICENSE` 署 `Copyright (c) 2026 JUNKUN DI` |
| 定位 | 轻量、跨平台远程桌面，支持 Web 客户端访问；官方站 <https://www.crossdesk.cn>、Web 客户端 <https://web.crossdesk.cn/> |
| 许可证 | **GPL-3.0-only**（`LICENSE` 678 行；含自定义头两行，**API 报 `NOASSERTION` / `Other`**） |
| 主语言 | C++（构建系统 Xmake 3.1.1+，C++17；`deps/` 用 git submodule） |
| 关注度 | **4,676 Star / 468 Fork / 订阅者 25 / 13 开放 Issue** |
| 活跃度 | **2023-11-20 创建**；`pushed_at` **2026-11-04T06:54:46Z**；默认分支 `mcp` 最新提交 **2026-11-03**（`fc1529ee`「[fix] stabilize MCP sessions and extend input controls」）；`main` 停在 **2026-02-12**、落后默认分支 **687 个提交** |
| 分支 | **30 个**，含长期分支 `main` / `release` / `deskhub` / `ios-support` / `cli` / `daemon` / `self-hosted-server` / `sdl3` / `av1` 等；**默认分支为 `mcp`** |
| 仓库体积 | API 报约 **71 MB**（`size` 72,347 KB）；`mcp` 分支树 **973 个文件、合计约 21 MB**（差距可部分由 30 个分支的重复文件与历史解释） |
| 目录结构 | `apps/`：`desktop` 520 文件 / `harmony` 111 / `android` 99 / `ios` 76 / `cli` 26 / **`mcp` 23**；另有 `libs/` 31、`docs/` 42、`.github/workflows` 21（含 `build-mcp.yml`）、`docker/`、`localization/` |
| 入库的大文件 | `apps/desktop/src/gui/assets/fonts/fa_solid_900.h`（2.6 MB）、`apps/ios/.../ThirdPartyLicenses.json`（1.5 MB）——都是文本，**未见大二进制入库** |
| topics | 15 个：webrtc、kcp、rtp、libdatachannel、sdl3、codec、audio-streaming、video-streaming、web-client、cross-platform、remote-desktop、remote-control、cplusplus 等 |
| 相关分发物 | CLI 走 npm：`@chunkdash/crossdesk-cli`；**MCP 包 `@chunkdash/crossdesk-mcp` 0.1.0 尚未发布到 npm**（README 明说"源码实现和本地打包不代表这些版本已经发布到 npm"） |
| Release | 最新 **v1.7.5-20261104**（2026-11-04 发布，10 个资产，覆盖 Win exe/便携版、macOS pkg x64+arm64、Linux deb amd64+arm64、Xfce 虚拟桌面 deb×2、Android apk、iOS 未签名 zip）；核验时各资产下载数为 0–2（发布当天）；前 100 条内有 **53 个 release** |

## 它能做什么

- **六端远控**：Windows / macOS / Linux 桌面**可控制也可被控**；原生 iOS / Android 与 Web 客户端**仅能控制**（不能作为被控端）；鸿蒙 PC 与移动端处于**开发版**。
- **终端主控（`crossdesk-cli`）**：在终端里用 9 位设备 ID 观看并操控远端桌面，默认**只读、静音**；普通终端走 ANSI 字符画面，支持 Kitty / Sixel 协议的终端可显示像素画面；可切换显示器、缩放平移、按 `c` 才开启键鼠控制；`--quality low|balanced|high|ultra` 请求 360p–2160p、`--fps` 1–30；可独立于 GUI 构建，也可 `npm i -g @chunkdash/crossdesk-cli` 安装。
- **MCP 远控（`apps/mcp/`）**：让本机 Agent 通过 MCP 观察并操作远端桌面，同时给用户一个监看面板。工具集是 `crossdesk_open / open_browser / list_devices / connect / get_session / screenshot / action / set_control / select_display / disconnect`。
- **面板策略 5 档**（`--panel`）：`auto`（有 GUI 开浏览器、只有终端给 CLI 入口）/ `apps`（宿主内嵌 MCP Apps）/ `browser` / `terminal`（共用同一 MCP 会话、不启网页服务）/ `off`（只留截图与操作工具）。
- **桌面墙（DeskHub）**：多设备实时预览、分组、放大操控、群控与批量文件分发。
- **日常远控功能**：多显示器切换、键鼠与组合键、文本剪贴板、文件传输、会话内画质与帧率调整、远端音频、隐私屏（开启时同时屏蔽电脑本地键鼠）。
- **IP 直连**：局域网内输入 `IP:端口`（只填 IP 用默认 **52888**）+ 六位密码即可，**不需要云端信令**，支持 IPv4 / IPv6，可经 VPN 接口连接。
- **可信设备（可选，默认关闭）**：在密码之外再校验设备配对公钥；配对信息只含公钥、设备名与 ID，**不含私钥或密码**；信任是单向的。
- **无人值守 / 无头**：Windows 有 CrossDesk Service 支持**锁屏、登录和安全桌面控制**，可启用**登录前远控**（独立设备 ID 与密码）；Linux 可复用现有桌面或起独立 Xvfb / Xfce 会话（`docs/HEADLESS.md`）。
- **自托管**：可自行部署信令与 TURN 中继（服务端在独立仓库 `crossdesk-server`，Web 客户端在 `crossdesk-web-client`）。
- **画面与编码**：H.264 / AV1，硬件编解码优先、失败回落软件；MCP 视频有两条解码路径——`browser`（MiniRTC 输出 H.264 Annex B → 浏览器 WebCodecs 解码）与 `minirtc`（MiniRTC 解码后传紧凑 NV12 帧）。

## 使用前需要注意

- **GPL-3.0-only 的传染性**：比 MIT / Apache 强得多，衍生作品与二次分发需按 GPL 处理；本项目也不属于"宽松许可可直接摘用"的一类。其配套的 `crossdesk-server` / `crossdesk-web-client` 是独立仓库，**许可未核验**。
- **ID 连接默认依赖官方信令服务器**（示例配置里是 `api.crossdesk.cn:9099`）。想要完全自持，需要自建信令与 TURN 中继。**IP 直连模式不依赖云信令**，但要求两端在同一局域网且互相可达。
- **iOS 发布包是未签名应用**，README 明确要求自行签名或用 Xcode 构建，**不要当成可直接安装的成品**；鸿蒙 PC / 移动端仍是开发版，README 自陈"仍在进行真机验收"。
- **移动端不能作为被控端**：iOS / Android / 鸿蒙手机与平板都只是控制端。读 README 顶部的"支持电脑、浏览器、移动端和终端连接"时，要配合下面那张平台表格看**控制 / 被控**两列。
- **MCP 包尚未发布到 npm**：文档里的 `npx @chunkdash/crossdesk-mcp@<已发布版本>` 属于发布后的形态；当前要从源码构建，且必须用 `--native-binary` 指向自己构建的可信原生可执行文件（参数**不接受 shell 命令**）。
- **验证范围只有 macOS arm64**：`apps/mcp/README.md` 的"本地审阅验证（2026-11-03）"记录的是本机 macOS arm64 的构建与回归（含 ASan/UBSan 覆盖动作调度与输入清理、140 组随机图像边界用例、1920×1080 完整画面 PNG 编码 22.5 → 11.5 ms 的本地基准），并写明**基准只衡量图像转换与编码、不含网络与远端执行，也未连接真实远端、未完成 Windows / Linux 验收**。作者也提醒"构建成功不等于各宿主和设备上的联调完成"。
- **把桌面交给 Agent 的风险仍由使用者承担**：项目做了面板确认、凭据隔离、一次性票据、观测失效与禁止重放等一层防护，但**能读同用户进程 / 文件或执行任意本机代码的 Agent 不受这层隔离**——也就是说，MCP 侧的限制对"已经能在本机任意执行的 Agent"没有意义。真实风险大小取决于你装的是哪个宿主、给它多少本机权限。
- **接口本身的边界**：MCP 工具**不提供 shell、剪贴板与文件操作**；截图观测最多 32 个、30 秒过期，切换显示器或几何变化会立即失效；视频通道最多 8 个观看者、票据 15 秒一次性。NV12 路径不压缩，**1080p/30fps 约需 93 MB/s 本机传输、4K/30fps 约 373 MB/s**，作者自己注明"这不是 GPU 零拷贝"。
- **分支必须看默认分支**：默认分支是 `mcp`，`main` 落后 687 个提交。按 `main` clone 或引用 `main` 上的文档会拿到过期内容。
- **商业化与合规信息（仅记录，未核实适用性）**：有爱发电 / Ko-fi / GitHub Sponsors 赞助入口；Windows 包由 SignPath.io 提供免费代码签名（仓库里只有一个 committer 与一个 approver，即作者本人）；有中英双语 `PRIVACY.md`。**我未逐条阅读隐私政策正文，也未验证其宣称的数据处理方式。**
- **本笔记未验证可运行性**：没有下载安装包、没有安装、没有连接任何远端设备、没有审计 C++ 源码与 MCP 实现。上面对安全设计的描述**来自仓库文档自述**，不是我的独立验证结论。

## 什么时候会再看

- 需要一个**可自托管、支持局域网直连、且能同时覆盖桌面 / 手机 / 浏览器 / 终端**的远控方案时。
- 想设计"**让 Agent 操作真实桌面**"的接口时——这里的授权模型（设备别名白名单、密码不进模型上下文、`submitted` ≠ 执行成功、`outcome_unknown` 禁止重放、观测 `observation_id` + 代际失效）可以直接当规范抄。
- 需要参考"**给 Agent 的面板怎么做得既可用又不越权**"（五档 `--panel` 策略、`127.0.0.1` 随机端口、URL fragment 一次性 bootstrap 换 HttpOnly cookie + CSRF、模型只拿到无特权地址）时。
- 想观察一个**国产自研音视频 / 信令栈**（MiniRTC + KCP + RTP + SDL3 + AV1）的实际工程组织与 Xmake 构建体系时。

## 相关笔记

- **结构最像的一篇：桌面客户端 + 自带 CLI + MCP Server 三件套**：[boxplayer](boxplayer-聚合网盘与影视的全平台播放器.md)——那边是把网盘与媒体服务器统一进客户端、再把能力抽象成 `clouddrive-cli` 与 MCP；本篇是把远控做成客户端、再抽出 CLI 与 MCP。两篇对照能看清"桌面应用如何把能力二次暴露给 Agent"的两种落法（一个是取用媒体资产，一个是操作键鼠画面）。
- **同为"给 Agent 的 MCP 服务端"，交付的能力不同**：[playwright-mcp](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md) 把**浏览器控制权**交给模型（自起浏览器或经 CDP 接入），本篇把**远端桌面的键鼠与画面**交给模型。值得对照的是两者的授权粒度：Playwright MCP 的典型用法是直接把控制权给模型，而 CrossDesk 要求用户先在面板里完成设备授权、且模型只能连接**已授权别名**——"交给模型"这件事的边界画法差很多。
- **同为"系统级权限 + 远程操控"，风险标注的写法可对照**：[dsh-Go](dsh-Go-DeepSeek-Harness的安卓客户端.md) 记录的是手机侧无障碍 / Shizuku 权限与数据流向；本篇要标注的是"控制端把键鼠输入发到远端"以及"Agent 拿到桌面操作权"这一类权限。两篇合看，可以整理出一份"远程 / 系统级操控类项目的权限与隐私标注清单"。
- **同为"让 Agent 操作真实环境"的工具，权限面大小不同**：[bb-browser：把真实浏览器登录态给 Agent 当 API 的 CLI 与 MCP](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md)——那边只借**浏览器登录态**取网页数据，本篇是**整台桌面**的截图与键鼠控制；可对照两者"给 Agent 的权限面"与各自的隔离设计。
- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"跨平台桌面客户端 + 远程运维"，协议栈不同**：[LeePanel](LeePanel-靠本地SSH管理服务器的开源Linux面板桌面端.md)——本篇是**远程桌面 / 远控**（自有远控协议 + MCP）；那篇是**桌面客户端 + 本地 SSH** 管 Linux 服务器（Tauri 2 + Rust，服务器零安装）。两者都是"把原本在浏览器 / 服务端的运维界面搬到本地客户端"，可对照协议栈与体积取舍。

## 参考来源

- 仓库主页：<https://github.com/kunkundi/crossdesk>（默认分支 `mcp`）
- 主 README（346 行）：<https://github.com/kunkundi/crossdesk/blob/mcp/README.md>
- MCP 服务端说明（232 行，本笔记安全设计部分的主要依据）：<https://github.com/kunkundi/crossdesk/blob/mcp/apps/mcp/README.md>
- 终端 CLI 文档：<https://github.com/kunkundi/crossdesk/blob/mcp/apps/cli/README.md>
- 连接模式架构（ID 连接 / IP 直连 / 可信设备认证）：<https://github.com/kunkundi/crossdesk/blob/mcp/docs/connection-architecture.md>
- 自托管部署：<https://github.com/kunkundi/crossdesk/blob/mcp/docs/SELF_HOSTING.md>
- 无头运行：<https://github.com/kunkundi/crossdesk/blob/mcp/docs/HEADLESS.md>
- 隐私政策（中英双语）：<https://github.com/kunkundi/crossdesk/blob/mcp/PRIVACY.md>
- 许可证（GPL-3.0-only）：<https://github.com/kunkundi/crossdesk/blob/mcp/LICENSE>
- 相关独立仓库：<https://github.com/kunkundi/crossdesk-server>、<https://github.com/kunkundi/crossdesk-web-client>、底层实时栈 <https://github.com/kunkundi/minirtc>
- 核验时间：2026-11-04（GitHub API 元数据 / 分支列表 / `commits?sha=mcp` / `commits?sha=main` / `compare/main...mcp` / `releases` 与 `releases/tags/latest` / `git/trees/mcp?recursive=1`，以及 raw 通道的 `README.md`、`apps/mcp/README.md`、`LICENSE`、`PRIVACY.md`）
