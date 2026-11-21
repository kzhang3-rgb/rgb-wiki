# FluxDown：Rust 驱动的多协议下载管理器（内置 MCP，可自托管）

> 一个 **Rust + Tokio** 引擎的**多协议下载管理器**，自称"**免费的 IDM 替代**"：桌面（GPUI）、安卓（Kotlin）、iOS（SwiftUI）、Web 管理界面与 CLI 共用**同一个引擎**；支持 **HTTP/HTTPS、FTP、BitTorrent 磁力、eD2K、HLS / DASH**，带浏览器扩展、RSS 与自动化，并**内置 MCP server（12 个工具）**让 AI 客户端直接管下载。**它也明确写了"这不是零遥测应用"**——这点值得单独记住。

- 状态：草稿
- 最近核验：2026-11-16（阅读 README 全文 316 行、`LICENSE` 首行、仓库文件树 3,433 项、ungh.cc 与 shields.io 元数据；**未下载任何安装包、未安装、未运行、未启用 MCP、未连接任何服务、未构建源码**）
- 适用范围：**仅供了解其功能、架构与遥测口径**。许可为 **AGPL-3.0**；下载内容的版权与各平台条款边界由使用者自担。

## 核心结论

**一句话定位（README 自述）**：`A blazing fast, multi-protocol download manager — the free & open-source IDM alternative.`

**三个最值得记的点：**

1. **一个 Rust 引擎，多个宿主与客户端**：桌面走 `fluxdown-desktop → fluxdown-agent → fluxdownd`；**无头部署复用 agent + daemon**（配 React Web UI）；原生安卓 / iOS 通过 **UniFFI**（`native/mobile`）接同一个引擎。`native/protocol` 定义与传输无关的 DTO / 方法 / 事件，`native/api` 通过同一个 `ApiHost` trait 同时暴露 **REST、aria2 兼容 JSON-RPC 与 MCP**。
2. **⚠️ 内置 MCP server，但明确"不是零遥测"**：读取的隐私段写明——**本地下载不需要 FluxCloud 账号、云功能可选**；**匿名安装与日活统计由 `analytics_enabled` 控制、可关闭，且不收集下载 / 任务信息**；随后**逐字写着 "This is not a zero-telemetry application."**——这句话是判断该项目隐私口径的关键，别把"永久免费、零广告"误读成"零遥测"。
3. **AI 集成是把它接给 Agent，而非内置 AI**：`/mcp` 端点实现 **无状态的 Streamable HTTP 子集**（`POST /mcp` 上的 JSON-RPC 2.0），**与 REST 管理 API 共用端口、不另起 MCP 进程**；鉴权用 **Bearer token**（`Authorization: Bearer <token>` 或 `X-FluxDown-Token`），与 REST 共用同一把 token；提供 **12 个工具**（`download_add` / `download_list` / `download_get` / `download_pause`·`resume` / `download_pause_all`·`resume_all` / `download_remove` / `queue_list` / `rss_list` / `rss_add` / `rss_remove`）。

**个人判断（未验证）**：把"**一个引擎、多客户端 + 统一 API 面（REST / aria2 / MCP）**"做扎实，是它区别于一般"套壳下载器"的地方；桌面默认**关闭管理 API 与 MCP**、由用户显式开启，也是较稳妥的默认值。但**安卓新版无法装在旧 Flutter 版之上**、以及"非零遥测"两点，需要在采用前留意。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zerx-lab/FluxDown](https://github.com/zerx-lab/FluxDown) |
| 描述 | "Rust 驱动的多协议下载管理器，支持 HTTP/FTP/BitTorrent 磁力链接及 HLS/DASH 流媒体，智能多线程加速与浏览器无缝集成。精美界面，极致性能，永久免费，零广告。" |
| 归属 | 组织 `zerx-lab`；作者署名 `zerx-lab` |
| 许可证 | **GNU Affero General Public License v3.0（AGPL-3.0）**（`LICENSE` 首行 AGPLv3，34,526 字节） |
| 主语言 | shields 显示 **61.0%**（Rust 为主，另有 Kotlin / Swift / TypeScript） |
| 技术栈 / 构成 | **Rust + Tokio 引擎**（`native/engine`）+ 桌面 **GPUI**（`crates/`）+ **Kotlin / Jetpack Compose** 与 **SwiftUI + UniFFI** 移动端 + **React + TypeScript + Vite** Web UI + **WXT** 浏览器扩展 + CLI；数据库默认 **SQLite（WAL）**，服务端可用 **PostgreSQL** |
| 关注度 | **5,699 Star / 339 Fork**（ungh.cc，2026-11-16；shields 显示 **5.7k Star / 339 Fork**、**179 开放 Issue**、**贡献者 25**） |
| 维护状态 | **2026-07-03 创建**（很新）；`pushedAt` **2026-11-16**（核验当日仍有推送）；页面 `updatedAt` 2026-11-16；**开发非常活跃** |
| 默认分支 | `main`（README：`main` = 活跃开发，`stable` = 稳定发布） |
| 版本 | **30 Release**（ungh releases 单页返回 30 条，为分页上限）；README 徽章指向 `/releases/latest` |
| 体积 | `main` 文件树共 **3,433 项**（ungh files API） |

## 功能与形态（按 README）

- **引擎与协议**：**动态分片下载加速**（运行期动态切分、空闲 worker 接管慢分片）、**多协议专用引擎**——HTTP/HTTPS、FTP、BitTorrent（DHT / UPnP / magnet）、eD2K（服务端 + Kad DHT 找源、MD4 校验）、HLS（AES 解密）、DASH。
- **队列与限速**：具名队列、调度、**令牌桶**全局限速。
- **持久化与续传**：默认 **SQLite + WAL**，服务端可换 PostgreSQL；从持久化状态恢复任务。
- **浏览器集成**：下载拦截、流媒体嗅探、**Alt+Click 绕过**、右键发送、连接诊断；Chrome / Edge / Firefox 扩展（经 **Native Messaging** 连桌面 agent）+ Tampermonkey 用户脚本。
- **界面**：GPUI 桌面 + React Web 管理，明暗主题与自定义主题、任务详情与分片可视化；**托盘常驻**（关掉 UI 服务仍在）。
- **自动化**：RSS 订阅与过滤、无人值守下载、**JavaScript 插件**、托管的 FFmpeg / yt-dlp 组件、任务事件 **webhook**。
- **远程设备**：可选 **FluxCloud** 账号、设置同步与远程任务管理，以及局域网设备配对与直连。
- **API 与 CLI**：`fluxdown` CLI（默认连桌面 / 服务器的 HTTP API，可 `--local` 用内嵌引擎）、**REST `/api/v1/*`**、**OpenAPI `/api/v1/openapi.json`**、**aria2 兼容 JSON-RPC `/jsonrpc`**、**MCP `/mcp`**、官方 UI 协议 `/rpc`。

**安装形态**：GitHub Releases / `fluxdown.com`；Windows（x64 / ARM64）、macOS（Intel / Apple Silicon）、Linux（x64）、**Android**（arm64-v8a / armeabi-v7a / x86_64）、**NAS / 服务器无头**（Docker `ghcr.io/zerx-lab/fluxdown-server`、Synology `.spk`、QNAP `.qpkg`、OpenWrt、Unraid、CasaOS / ZimaOS）。

**无头服务**：`fluxdown-agent --server` + `fluxdownd`，内嵌 Web UI；默认监听 `[::]:17800`（可用 `FLUXDOWN_BIND` 覆盖），**首次向导须设访问 key**（8–128 个可见 ASCII，含字母与数字）；持久化 `/data` 与 `/root/Downloads`；无人值守可用 `FLUXDOWN_TOKEN` 注入（仅在未设 key 时初始化）。

## 使用前需要注意

- **⚠️ "这不是零遥测应用"（事实，README 原文）**：README 隐私段在说明"统计可控、不收集下载 / 任务信息"后**明确写着 "This is not a zero-telemetry application."**——即**默认存在匿名安装与日活统计**（可用 `analytics_enabled` 关闭）。不要因为"永久免费、零广告"就当作无遥测。
- **⚠️ 管理 API 与 MCP 是"能力面"，默认值要记住（事实）**：**桌面默认关闭管理 API 与 MCP**（需在 Settings → API Service 手动开并生成 token）；**无头服务器模式默认两者都开、且强制访问 key**。若把无头服务暴露到公网，README 建议**先在内网设好 key、再用 HTTPS 反代**。
- **⚠️ AGPL-3.0（事实）**：AGPL 要求**通过网络提供服务的修改版也须开放源码**。若你打算把它的服务端改造后对外提供，需评估 AGPL 义务。
- **⚠️ 安卓新版**无法覆盖安装在**旧 Flutter 版**之上（事实）——README 提示升级前先备份数据。
- **⚠️ 下载器的通用版权风险（事实）**：它支持磁力 / BT / eD2K 与流媒体，**下载什么、是否合规取决于使用者**；README 也把"永久免费、无广告"作为定位，但**内容来源与平台条款的边界不在项目责任范围内**。
- **⚠️ 很新且迭代极快（事实）**：**2026-07-03 创建**、核验当日仍在推送、**179 个开放 Issue**——功能与行为可能快速变化，文档与实现有出入属正常。
- **⚠️ 我未做任何验证**：**未下载安装包、未安装、未运行、未启用 MCP、未连接任何服务、未构建源码**。功能、端点、默认值与遥测口径均**来自 README 与仓库文件本身**。

## 什么时候会再看

- 需要一款**跨平台、可自托管、带完整 API / CLI 的多协议下载器**（并希望**浏览器接管 + RSS + 远程管理**）时。
- 想参考"**一个引擎多宿主 + 统一 API 面（REST / aria2 兼容 / MCP）**"的架构组织方式，或"**下载管理器如何把控制权交给 AI 客户端（MCP）**"时。
- 想对照"**下载工具**"的不同定位（多协议管理器 vs 媒体专用下载器，见相关笔记）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"下载工具"，定位不同**：[galaxy-downloader：多平台媒体下载器](galaxy-downloader-多平台媒体下载器.md)——那是**视频 / 图文为主的网页应用**（解析靠作者自建后端、自定义非开源许可），本篇是**跨平台多协议管理器**（AGPL-3.0、可自托管、含 API / CLI / MCP）；一个面向"取回某平台内容"，一个面向"管理各类下载"。
- **同为"下载工具"，媒介与许可不同**：[go-music-dl：多平台音乐搜索与下载工具](go-music-dl-多平台音乐搜索与下载工具.md)——那是**音频为主、可 Docker 自托管、AGPL-3.0** 的 Go 服务，本篇是 **Rust 引擎的多协议管理器**（含桌面 / 移动 / Web / CLI）；可对照两者在"自托管 + AGPL"上的不同落点。
- **同为"下载工具"，内核来源不同**：[videdown：开源视频下载工具](videdown-开源视频下载工具.md)——那是**桌面端套壳 yt-dlp（MIT）**（借成熟命令行引擎），本篇是**自研 Rust 引擎**（自己实现协议与加速）；可对照"套壳成熟工具"与"自研引擎"两条路线。
- **同为"传文件"，一个跨互联网一个只在局域网**：[LocalSend：局域网内跨设备传文件的开源 AirDrop 替代](localsend-局域网内跨设备传文件的开源AirDrop替代.md)——那是**同局域网内设备之间点对点**传文件 / 文本（不经任何服务器，默认端口 53317）；本篇是**从互联网多协议下载**（含 API / CLI / MCP、可自托管）。可对照"取回远端内容"与"在近端设备间搬运"两类路径。

## 参考来源

- 项目仓库与 README：https://github.com/zerx-lab/FluxDown
- 官网 / 下载 / FAQ：https://fluxdown.com ；MCP 实现：`native/api/src/mcp.rs`
- 元数据（Star / Fork / 开放 Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 主语言）：ungh.cc 镜像与 shields.io 徽章，2026-11-16

以上信息核验于 2026-11-16，Star 数、Release 与功能随开发快速变化。**未下载安装包、未安装、未运行、未启用 MCP、未连接任何服务、未构建源码**；功能、端点、默认值与遥测口径均来自 README 与仓库文件本身。**"这不是零遥测应用"为 README 原文表述。**
