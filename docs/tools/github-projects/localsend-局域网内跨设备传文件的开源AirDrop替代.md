# LocalSend：局域网内跨设备传文件的开源 AirDrop 替代

> 记录一个把"隔空投送（AirDrop）"搬到**全平台 + 局域网 + 无服务器**的开源项目（`localsend/localsend`，描述 `An open-source cross-platform alternative to AirDrop`）：**不需要联网、不经过任何第三方服务器**，靠 **REST API over HTTPS** 在**同一局域网**内的设备之间传文件与文本消息；**六个平台**（Windows / macOS / Linux / Android / iOS / Fire OS）用 **Flutter + Rust** 一套代码互通，**TLS 证书在每台设备上即时生成**；另有一套 **CLI**（`localsend-cli`）与一份**公开的协议规范**（`localsend/protocol`）。三条先记住：**它只在同一局域网内工作**（跨网段 / 跨公网不适用）；**入站需放行端口 53317（TCP+UDP）**，防火墙 / AP 隔离 / VPN 都会拦它；**它不带自动更新**（README 建议走应用商店或包管理器）。**未 clone、未编译、未安装、未在两台设备间实际传过任何文件。**

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md`（333 行）、`LICENSE`（Apache-2.0，11,346 B）文件、文件清单（1038 项）、ungh.cc 镜像的 repo / releases 元数据与 shields.io 徽章；**未 clone、未编译、未安装任何平台版本、未运行 CLI、未在两台设备间实际发送或接收任何文件、未审计源码**）
- 适用范围：需要在**同一局域网内**在自己多台设备间（或与他人）传文件的个人与团队；想参考"**一套 Flutter 前端 + Rust 内核跨六端**"或"**无服务器的局域网文件传输协议**"的工程实现者

## 核心结论

**一句话定位（README）**：LocalSend 是一个**免费、开源**的应用，让你**在附近设备之间通过本地网络安全地传输文件与消息，无需联网、无需第三方服务器**。

**① 为什么可以没有服务器：REST API + 即时 HTTPS 证书。** README 的 "About" / "How It Works" 说明：设备之间通过 **REST API** 通信，**所有数据经 HTTPS 加密，且 TLS/SSL 证书在每台设备上即时生成**（不依赖任何 CA 或中心服务器）。协议是公开的，单独放在 `localsend/protocol` 仓库。

**② 一套代码，六个平台。** 前端 Flutter（`app/`），底层 Rust（经 `flutter_rust_bridge` 桥接）。平台最低版本（README "Compatibility"）：**Android 7.0 / iOS 13.0 / macOS 11 Big Sur / Windows 10 / Linux**（需 `xdg-desktop-portal`；Fire OS 走 Amazon 应用商店）。

**③ 分发渠道极多，且没有自动更新。** README "Download" 列出：Windows 有 Winget / Scoop / Chocolatey / EXE 安装器 / 便携 ZIP；macOS 有 App Store / Homebrew / DMG；Linux 有 Flathub / Nixpkgs / Snap / AUR / TAR / DEB / AppImage；Android 有 Play / F-Droid / APK；iOS 与 Fire OS 走各自商店。README 明确：**应用不带自动更新，推荐从应用商店或包管理器安装**。

**④ 命令行客户端 `localsend-cli`。** 基于 **LocalSend Protocol v2**，macOS / Linux 可用 **Homebrew** 安装（`brew install localsend-cli`）。`localsend-cli send <文件/目录…>` 会打开**已发现设备列表**供交互选择；也可用 `--to "<别名>"` 或 `--to <IP>` 跳过交互（**IP 会在默认端口 53317 上直接 HTTPS 探测**）。`--text` 发文本，`-` 表示从 stdin 读（单尾换行会被去掉，**超 64 KB 转成 `.txt` 文件**，stdin 须 UTF-8 且 ≤ 16 MB）。目录**递归收集、保留根名与嵌套路径**，**空目录不传**（协议传的是"文件条目"而非"目录条目"）。

**⑤ 网络配置是最大的坑（README "Setup" / "Troubleshooting"）。** 需放行**入站 TCP+UDP 端口 53317**（Linux 示例给 `ufw` / `firewalld` 命令）；**路由器要关闭 AP 隔离**（尤其访客网络）；Windows 网络须设为"专用"；macOS / iOS 若设备不可见可切换"本地网络"权限；**开着 VPN 会拦截局域网流量**，需允许本地流量或临时关闭。可手动填对方 IP 直连，成功后把设备加入收藏即可直接探测。

**⑥ 关注度与状态（ungh 快照 / shields，2026-11-19）。** **99,590 Star / 5,718 Fork / 348 关注**（ungh；shields 同期 100k / 5.7k）；**904 个开放 Issue、贡献者 242 人**（shields）；**2022-12-16 创建**、`pushedAt` 与 `updatedAt` 均为 **2026-11-18**（活跃）；`main`；**27 个 Release**（最新 **v1.18.2**，2026-08-21）；**1038 个文件**；**Apache-2.0**（`LICENSE` 11,346 B）；shields 顶层语言 **78.4%**。

**⑦ 许可：Apache-2.0。** `LICENSE` 为标准 Apache License 2.0（11,346 B）。README 另给 Codeberg 镜像、Discord 社区与 Weblate 翻译平台入口；Windows 二进制有代码签名（见 `CODE_SIGNING.md`）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [localsend/localsend](https://github.com/localsend/localsend) |
| 描述（GitHub） | `An open-source cross-platform alternative to AirDrop` |
| 归属 | 组织 **localsend**；官方站 <https://localsend.org>；Codeberg 镜像 <https://codeberg.org/localsend/localsend>；社区 Discord |
| 许可证 | **Apache-2.0**（`LICENSE` 11,346 B） |
| 主语言 | shields 顶层语言 **78.4%**（前端 Flutter / Dart，底层 Rust，`flutter_rust_bridge` 桥接） |
| 关注度 | **99,590 Star / 5,718 Fork / 348 关注**（ungh，2026-11-19；shields 同期 100k / 5.7k）；**904 开放 Issue、贡献者 242 人**（shields） |
| 创建 / 最近推送 | **2022-12-16 创建**；`pushedAt` / `updatedAt` 均为 **2026-11-18**（活跃） |
| 默认分支 / Release / 文件数 | `main`；**27 个 Release**（最新 **v1.18.2**，2026-08-21）；**1038 个文件** |
| 支持平台 | Windows 10+ / macOS 11+ / Linux（`xdg-desktop-portal`）/ Android 7.0+ / iOS 13.0+ / Fire OS |
| 通信方式 | **REST API over HTTPS**；**TLS 证书每设备即时生成**；默认端口 **53317（TCP+UDP）**；协议规范见 `localsend/protocol` |
| 仓库结构 | `app/`（606，Flutter 六端）· `packages/`（221：core / localsend_isolates / typed_isolates）· `support/`（116）· `cli/`（25，Rust）· `.github/`（24 工作流）· `fastlane/`（17）· `server/`（14，Rust + Dockerfile） |
| 根目录可见文件 | `README.md`（14,991 B）、`.fvmrc`、`AGENTS.md`（12,307 B）、`CLAUDE.md`、`CODE_SIGNING.md`、`CONTRIBUTING.md`、`Cargo.toml` / `Cargo.lock`、`LICENSE`、`pubspec.yaml`、`rust-toolchain.toml` 等 |
| 命令行 | **`localsend-cli`**（基于 LocalSend Protocol v2，Homebrew 安装） |
| 翻译 | Weblate（<https://hosted.weblate.org/projects/localsend/app>），语言文件在 `app/assets/i18n/` |

## 它能做什么

- **同一局域网内跨平台互传**：Windows / macOS / Linux / Android / iOS / Fire OS 六端之间**互发文件与文本消息**，**无需联网、不经服务器**。
- **免中心服务器的加密通道**：REST API + **即时生成 TLS 证书**的 HTTPS 传输；协议规范公开（`localsend/protocol`）。
- **命令行发送**：`localsend-cli send`（交互选设备，或 `--to <别名/IP>` 直发；`--text` 发文本，`-` 读 stdin）。
- **多数情况开箱即用**：README 称"多数情况下可直接工作"，仅在收 / 发异常时才需放行端口、关 AP 隔离等。
- **便携模式**（v1.13.0 起）：在与可执行文件同目录放一个 `settings.json`（可为空），配置便存到该文件旁而非默认位置。
- **启动隐藏**（v1.15.0 更新）：`--hidden` 参数（仅托盘）；v1.14.0 及更早是随 autostart 隐藏。

## 使用前需要注意

- **⚠️ 只在同一局域网内工作**：跨网段 / 跨公网 / 不同 NAT 的设备之间不适用；不在同一子网就发现不到。
- **⚠️ 端口与网络配置是常见失败原因**：需放行**入站 TCP+UDP 53317**；**关闭路由器 AP 隔离**（尤其访客网络）；Windows 网络须为"专用"；macOS / iOS 可能需开"本地网络"权限；**VPN 默认会拦局域网流量**。
- **⚠️ 没有自动更新**：README 明说应用不带自动更新，推荐**从应用商店或包管理器**安装以获取更新。
- **⚠️ 速度相关**：README 的 troubleshooting 提到——用 **5 GHz**、必要时**双方关闭加密**可提速；Android 接收侧有已知的 `saf_stream` 慢速问题（指向上游 issue）。
- **⚠️ 平台边界（README "Compatibility"）**：Android 5/6 最后支持到 v1.17.0；iOS 12 最后支持到 v1.17.0，且**从其它 App 分享需 iOS 14+**；Windows 7 最后支持到 v1.15.4。
- **⚠️ 非官方 MSIX 预览**（README 的 CAUTION）：某第三方站点提供最新提交的 MSIX 构建，**稳定性不保证**——如需 Windows 包请优先官方渠道。
- **⚠️ 本笔记未实测**：以上机制、命令与兼容性表均为 **README 自述**，我**未编译、未安装、未实测任何一次传输**；Star / Fork / Issue 数为 **2026-11-19 快照**（会变）。

## 什么时候会再看

- 需要**在自己多台设备间（同局域网）快速传文件**、又不想经云盘或第三方服务器时；
- 想参考"**一套 Flutter 前端 + Rust 内核跨六端**"的工程结构，或"**无服务器的局域网 REST+HTTPS 传输协议**"的设计时；
- 想对照"**局域网 / 跨设备传输**"的其它形态（[CrossDesk](crossdesk-轻量跨平台远程桌面与MCP远控服务端.md) 跨平台远控与局域网直连 · [FluxDown](FluxDown-Rust写的多协议下载管理器.md) 互联网多协议下载）时；
- 需要一份"**设备发现失败 / 传输慢**"的排查清单（AP 隔离 / VPN / 防火墙 / 5 GHz）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"跨设备 + 局域网直连"，一个搬操作一个搬文件**：[CrossDesk：轻量跨平台远程桌面与 MCP 远控服务端](crossdesk-轻量跨平台远程桌面与MCP远控服务端.md)——那是**桌面 ↔ 桌面的远控**（自有实时音视频栈 + MCP，默认 ID 连接、可局域网 IP 直连）；本篇是**只传文件与文本的轻量工具**（REST + HTTPS、六端互通、无服务器）。两者都做"局域网内设备互连"，可对照两类用途。
- **同为"传输工具"，一个有服务器一个没有**：[FluxDown：Rust 驱动的多协议下载管理器](FluxDown-Rust写的多协议下载管理器.md)——那是**从互联网多协议下载**（含 API / CLI / MCP、可自托管）；本篇是**局域网内点对点**、不经任何服务器。可对照"取回远端内容"与"在近端设备间搬运"两类传输。

## 参考来源

- 仓库主页：<https://github.com/localsend/localsend>
- README（333 行）：<https://github.com/localsend/localsend/blob/main/README.md>
- 协议规范仓库：<https://github.com/localsend/protocol>
- 官方站：<https://localsend.org>
- 许可证（Apache-2.0）：<https://github.com/localsend/localsend/blob/main/LICENSE>
- Codeberg 镜像：<https://codeberg.org/localsend/localsend>
- 核验时间：2026-11-19（ungh.cc 镜像的 repo / releases / files(main) 元数据、shields.io 徽章，以及 raw 通道的 `README.md` 与 `LICENSE`）
