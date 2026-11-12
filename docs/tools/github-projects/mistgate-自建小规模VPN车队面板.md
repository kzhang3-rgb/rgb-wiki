# Mistgate：给"自己的几台服务器"用的自建代理车队面板

> 记录一个 Go 写的自托管代理面板：**一个二进制 + SQLite**，管自己 handful 的服务器与家人朋友。第一句先记清三件事：**它明写"由 AI 编码 Agent 写、带 `Co-Authored-By` 的提交"**；**AGPL-3.0，且用 `serve --source-url` 主动履行 AGPL 的源码提供义务**；**它把自己定位成"Beta"，一个月发了 60 多个 tag**。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README（166 行，含协议表、稳定性说明、对比表、快速开始）、`llms.txt` 全文、`AGENTS.md` 存在性、`go.mod` 首几行、`LICENSE` 首行、分支 / Release / 提交 / 贡献者元数据；**未部署、未安装、未运行、未在服务器上执行任何安装脚本、未审计源码**）
- 适用范围：Linux 服务器；`go 1.27` / `toolchain go1.27.2`；安装脚本 `curl … | sudo bash`；面向自建小规模"代理车队"（面板 + 节点）

## 核心结论

**① 它公开承认"用 AI 编码 Agent 协作开发"，并把这件事写进 README。** 原文有 "**Mistgate is written with AI coding agents**" 与 "**Commits made with an agent carry a `Co-Authored-By` line**"。对照分支命名也能看到痕迹：`claude/klick-probe-reports-5cbb35`、`claude/klick-reports-mcp`、`codex/m2-roadmap-site`、`codex/profile-name-labels` 等——**分支名里直接带 `claude/`、`codex/` 前缀**，是"多 Agent 并行开发"的工作流外显。**在一个代理面板里主动声明 AI 参与开发，是较少见的透明做法**（判断：透明度是加分项，但代码质量仍需自行审计，本笔记未审计）。

**② 面向 AI 消费的元文件齐全，这是它的一个特色。** 仓库根目录同时放了 **`llms.txt`（1,257 B）与 `AGENTS.md`（5,212 B）**，另有多语 README（`README.zh.md` / `README.ru.md` / `README.fa.md` / `README.tr.md`）。**"给 LLM 的入口文件 + 给 Agent 的项目说明 + 多语言首页"三件套**，说明作者把"让 AI 能读懂这个项目"当成了一等公民。

**③ 协议选择是明确的"减法"，不是"全能面板"。** 支持：**Hysteria2**（UDP/QUIC、Salamander 混淆、端口跳跃）、**VLESS**（xray-core，REALITY over XHTTP 或 TCP）、**AmneziaWG**（v3.1 与 2.0）、**MTProto**（自家 core，实验性）。**明确不支持** VMess / Trojan / Shadowsocks / TUIC / 纯 WireGuard。**它的卖点不是"协议最全"，而是"面板轻"**：自述面板空闲内存 **16–20 MiB**；对比表并列了 3x-ui（68–74 MiB）、Remnawave（426–454 MiB）、PasarGuard（262–265 MiB）、Hiddify、s-ui，并对**测量方法与"对方 README 未说明"用脚注 `¹` 与 `?` 分别标注**（判断：这种"把不确定项也标出来"的对比表，比只挑对自己有利的数字要可信）。

**④ 稳定性自述是"Beta"，发版节奏却极快。** README 把它定位成 Beta；自述 **v0.1.0 于 2026-10-03 打标，到 v0.1.63（2026-10-27）已是第 64 个 tag**——**一个月内 60 多次发版**。另有自述"**我在自己的六台服务器上给家人朋友跑**"、以及**陌生访问者会看到诱饵站**的设计。**高速迭代 + 单人维护（贡献者仅 `limeflash` 一人 822 次提交）**，意味着功能前沿但接口稳定性需自行承担。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Mistgate/mistgate](https://github.com/Mistgate/mistgate)（组织账号） |
| 描述定位 | 自托管代理面板：单二进制 + SQLite，面向自建小规模代理车队 |
| 许可证 | **AGPL-3.0**（`LICENSE` 34,523 B）；另有 `CLA.md`（贡献者许可协议）、`SECURITY.md` |
| 主语言 | Go（`module github.com/mistgate/mistgate`，`go 1.27`，`toolchain go1.27.2`） |
| 官网 | https://mistgate.app/ |
| 关注度 | **223 Star、13 Fork、0 关注、2 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-10-01 创建**；`pushed_at` 2026-11-04；默认分支 **`main`**；**贡献者仅 `limeflash` 一人 822 次提交**；分支众多（`feat/*`、`fix/*`、`ci/*`、`claude/*`、`codex/*`） |
| 版本节奏 | 最新 **v0.1.74**（2026-11-04，15 个资产）… v0.1.65；自述 v0.1.0 于 2026-10-03、v0.1.63 为第 64 个 tag |
| 体积 | API `size` 64,582 KB（约 63 MB）；目录树 2969 项 / **2709 个 blob / 74,839 KB** |
| 话题标签 | 20 个，含 `mcp-server`、`anti-censorship`、`censorship-circumvention`、`self-hosted`、`single-binary`、`sqlite`、`hysteria2`、`vless`、`reality`、`amneziawg`、`xray`、`mihomo` 等 |
| 面向 AI | 根目录 **`llms.txt`** + **`AGENTS.md`**；多语 README（zh / ru / fa / tr） |

## 它能做什么（结构清单）

- **支持的协议**：Hysteria2（UDP/QUIC、Salamander 混淆、端口跳跃）、VLESS（xray-core；REALITY over XHTTP 或 TCP）、AmneziaWG（v3.1 与 2.0）、MTProto（自家 core，实验性）
- **不支持的协议**：VMess、Trojan、Shadowsocks、TUIC、纯 WireGuard
- **安装**：`curl -fsSL https://mistgate.app/install.sh | sudo bash -s -- --public-url https://panel.example.com`；安装器**校验签名 manifest**，支持 `--yes` / `--dry-run`
- **目录结构**（一级）：`internal` 1374、`web` 508、`docs` 231、`design` 112、`edge` 82、`cmd` 69、`third_party` 64、`gen` 55、`tools` 52、`site` 31、`proto` 28、`video` 27、`scripts` 24、`deploy` 12
- **对比表**：3x-ui、Remnawave、Hiddify、PasarGuard、s-ui（含脚注测量方法与 `?` 未说明项）

## 使用前需要注意

- **AGPL-3.0（强传染）+ 自带 `CLA.md`**：若把它作为托管服务对外提供，需按 AGPL 履行源码提供义务；作者提供 **`serve --source-url`** 用于指向对应源码地址。
- **`curl | sudo bash` 安装**：安装脚本会以 root 执行且访问网络；即便安装器校验签名 manifest，也建议先本地审阅脚本再执行（本笔记**未运行该脚本**）。
- **该类工具的合规风险**：代理/翻墙工具在不同司法辖区有不同法律约束，使用前需自行判断当地法规与自身用途。
- **Beta + 高速迭代**：一个月 60+ tag、单人维护，升级前建议先看 Release 说明，避免跨版本踩破坏性变更。
- 本笔记**未部署、未运行、未审计源码**；关于"轻量""诱饵站""AI 协作"等描述均为文档层面转述。

## 什么时候会再看

- 需要"**极小内存占用的自建代理面板**"时，或想对比 3x-ui / Remnawave / PasarGuard 的取舍时；
- 想参考"**用签名 manifest 校验安装脚本**"与"**AGPL 项目如何主动提供源码入口**"的做法时；
- 想研究"**多 Agent（Claude / Codex）并行开发的工作流外显**"（分支命名、`Co-Authored-By`、`AGENTS.md`、`llms.txt`）时。

## 相关笔记

- **同样是"自建代理节点"的技术路线**：[vps-xray-reality：Xray 节点部署技能](vps-xray-reality-Xray节点部署技能.md)——那是把 Xray REALITY 的单机部署写成 Agent 技能；本篇是**面板 + 车队**的管理面，两者一个管"节点怎么搭"，一个管"多节点怎么统管"。
- **客户端侧的分流规则**：[Shadowrocket-Rules：小火箭分流规则配置](Shadowrocket-Rules-小火箭分流规则配置.md)——面板负责服务端与节点，客户端要按服务分流时看那篇。
- **客户端侧的对照**：[bowel：macOS 上不用 WebView 的 mihomo 客户端](bowel-macOS上原生Go渲染的mihomo客户端.md)——那是 **macOS 上的 mihomo 图形客户端**（Go 原生渲染、内置 MCP server、PolyForm 非商用许可）；一个在服务端管节点车队（本篇），一个在客户端消费节点。
- **同属网络打通基础设施**：[cloudflared：Cloudflare 隧道客户端](cloudflared-Cloudflare隧道客户端.md)——那是 Cloudflare Tunnel 客户端，与本篇的"自建代理面板"是两种不同的出入口思路。
- **它用的 `AGENTS.md` 格式本身也值得单看**：[agents.md：给编码助手用的 AGENTS 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——本篇是少数**同时放 `AGENTS.md` 与 `llms.txt`** 的项目，可作实例对照。
- **同为"自托管服务"的成熟样本**：[gitea：自托管一体化 Git 服务平台](gitea-自托管一体化Git服务平台.md)——Go 单二进制、MIT、9 年持续维护、两条版本线并存；可对照"自托管产品的部署与分发形态"（Release 资产规模、Docker / rootless、版本节奏）。
- 本页索引位置：[GitHub 项目收藏](README.md)
- **同为"自托管网络基础设施面板"，层面不同**：[Linux-Router](Linux-Router-把Debian设备变成路由器的Web控制台.md)——那篇是**无特权 Flask Web + root Agent** 管**一台设备的有线 / Wi-Fi / 热点**；本篇是 **Go 单二进制 + SQLite** 管**多台服务器的代理节点**。可对照"管本机网络"与"管多台节点"两种取向。

## 参考来源

- 仓库主页：https://github.com/Mistgate/mistgate （核验日期 2026-11-04）
- README（含协议表、稳定性说明、对比表、快速开始）、`llms.txt`、`AGENTS.md`、`go.mod`、`LICENSE`、`CLA.md`（核验日期 2026-11-04）
- GitHub API：`repos/Mistgate/mistgate`、`git/trees/main?recursive=1`、`branches`、`releases`、`contributors`（核验日期 2026-11-04）
