# bowel：macOS 上不用 WebView 的 mihomo 客户端

> 记录一个刚发布 v0.1.0 的 macOS 代理客户端：**界面用 Go 直接走 GPU 渲染、没有 WebView**，代理内核是 mihomo（GPL-3.0，独立进程运行）。第一句先记清三件事：**它是"指定机型"的 macOS 专用客户端（Apple 芯片）**；**许可为 PolyForm Noncommercial 1.0.0（禁止商用），而 GitHub 页面只显示 "Other"**；**它内置了一个 MCP server，供 Claude Code 等 Agent 读取状态与排障**。

- 状态：草稿
- 最近核验：2026-11-09（阅读 README（英文 113 行）、`LICENSE` 首部、`mygo.json`、Release 说明与元数据；**未下载安装包、未安装、未运行、未接任何订阅**）
- 适用范围：macOS 12+、仅 Apple 芯片；需要自备订阅链接 / 节点 / 配置；开发需 Go 1.27.3+

## 核心结论

**① 它最特别的地方是"渲染方式"：Go 原生 UI，不走 WebView。** README 原文："**The interface is built in Go and rendered directly via the GPU, with no WebView.**"——多数 Electron / Tauri 客户端要么打包整个浏览器，要么依赖系统 WebView；bowel 用 Go 的 UI 工具包 [MyGo](https://github.com/egoist/mygo) 自绘界面（判断：这是它在"同类 mihomo GUI"里最差异化的点，代价是界面能力与生态自担，本笔记未运行验证实际观感与性能）。

**② 定位是"macOS 上的 mihomo 图形客户端"，内核交给 mihomo。** 代理内核用 [MetaCubeX/mihomo](https://github.com/MetaCubeX/mihomo)（**GPL-3.0**），README 明写"**bowel runs it as a separate program and does not link it**"（独立进程运行、不做链接）——这是它在"自身非商用许可 + 内核 GPL"之间做的隔离处理。

**③ 它内置 MCP server，把自己变成"能被 Agent 操作的客户端"。** README 的功能项 **MCP** 写："Built-in MCP server for Claude Code and other agents to read status and troubleshoot connections. **It asks you before changing settings by default.**"，并给了接入命令。**"默认改设置前先询问"是一个值得注意的默认值**（判断：把网络代理状态暴露给 Agent 有便利也有风险，是否接受要自行权衡）。

**④ 版本极早期，是"刚上线"而非"成熟可选"的项目。** **2026-11-07 创建**，核验时（2026-11-09）仅 **v0.1.0 一个 Release**（2026-11-08 发布）、**贡献者只有 1 人**（`Obooman`，29 次提交）。README 也交代了它的前身：**bowel 原叫 Moho**，Go module 仍为 `github.com/oboo/moho`、bundle id 仍是 `dev.moho.app`（为保留 macOS 已有的摄像头 / 录屏权限），首次启动会把 `~/Library/Application Support/Moho` 的数据迁移到 `…/bowel`。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [21stware/bowel](https://github.com/21stware/bowel)（组织账号 `21stware`） |
| 描述定位 | macOS 上的 mihomo 客户端；Go 原生渲染，无 WebView |
| 许可证 | **PolyForm Noncommercial License 1.0.0（仅限非商业用途）**，`LICENSE` 4,611 字符，含一行 `Required Notice: Copyright (c) 2026 Oboo Cheng`；**GitHub 页面与 API 均报 `NOASSERTION` / "Other"，必须查 `LICENSE` 文件** |
| 内核许可 | 捆绑的 mihomo 内核仍为 **GPL-3.0**；App 内 `Contents/Resources/licenses/mihomo/` 随包附许可，Release 同时附对应源码 |
| 主语言 | Go 60.0%（shields.io top 语言） |
| 官网 | https://21stware.github.io/bowel/ |
| 关注度 | **244 Star、12 Fork、0 关注、3 个开放 Issue**（页面值与镜像一致） |
| 维护状态 | **2026-11-07 创建**；`pushed_at` 2026-11-08；默认分支 **`main`**；**贡献者仅 `Obooman` 一人 29 次提交** |
| 发行版 | **1 个 Release / 1 tag，仅 v0.1.0（2026-11-08）**；资产 `bowel-0.1.0-arm64.dmg`（约 36.2 MB，自发布起下载 174 次） |
| 话题标签 | 空（未设置 topics） |
| 打包标识 | `mygo.json`：`identifier` = `dev.moho.app`（历史遗留）、`version` 0.1.0、签名身份 `Developer ID Application: Cheng Aobo (R54TY86R4C)` |

## 功能与形态

- **Routes（分流）**：按用途分流，退出节点可手动选或按速度自动选。
- **Connections（连接）**：逐条查看每个连接的 App / 目标 / 命中规则 / 出口 / 速度，并可临时改某域名的走向。
- **Providers（订阅源）**：导入订阅链接、节点 URL、YAML 配置或二维码，每条源显示用量与到期时间。
- **Menu bar（菜单栏）**：切换系统代理接管、切换代理模式、选默认节点。
- **Skins（皮肤）**：六套皮肤（Broadsheet / Phosphor / Neon / Blueprint / Sakura / Paper（默认））。
- **自定义规则**：按域名 / 关键词 / IP 段指定走向，并可测试某地址最终走向。
- **真实出口核验**：通过 Cloudflare trace 验证实际出口位置。
- **Gateway 模式**：把本机变成局域网网关，给电视、游戏机等设备用。
- **TUN**：接管那些无视系统代理设置的 App 的流量。
- **MCP**：内置 MCP server，供 Claude Code 等 Agent 读状态、排障；默认改设置前先询问。接入命令：

  ```bash
  claude mcp add --scope user bowel -- /Applications/bowel.app/Contents/MacOS/bowel mcp
  ```

- **设置与语言**：可搜索设置（⌘F）；英文 / 简体中文 / 繁体中文 / 日文；浅色与深色。

**安装**：从 Releases 下载 `bowel-<版本>-arm64.dmg` 拖入 Applications，再添加一个订阅源（订阅 URL、节点链接或二维码）并打开接管。**要求 macOS 12 或更高，正式构建仅面向 Apple 芯片，经 Developer ID 签名并由 Apple 公证。**

**开发**（需 Go 1.27.3+，`GOTOOLCHAIN=auto` 自动拉取；**不需要 Node 或 Bun**）：

```bash
./scripts/fetch-core.sh     # 拉取 mihomo + GeoSite/GeoIP 到 resources/
go tool mygo dev            # 开发模式，改动即重建
go tool mygo build          # 产出 bowel.app 与 .dmg（签名但不公证）
./scripts/release.sh        # 构建 + 公证 + staple，正式发版用
CGO_ENABLED=0 go test ./... # 含会启动真实内核的测试
```

架构说明见 `docs/architecture.md`，产品信息架构见 `docs/product-ia.md`。

## 使用前需要注意

- **非商用许可**：PolyForm Noncommercial 1.0.0 **只允许非商业用途**，商用需另行联系作者；且 GitHub 页面 / API 把它标成 "Other"，**下载前务必自行读 `LICENSE`**。
- **平台限定**：**只有 macOS、且当前只发 Apple 芯片（arm64）构建**，Intel Mac 与其它系统不在支持范围。
- **极早期 + 单人维护**：一个 Release、一位贡献者，接口与功能可能随时变化；"能长期跟进"需自行判断。
- **MCP 暴露面**：内置 MCP server 让 Agent 可读代理状态并（在询问后）改设置；是否交给 Agent 操作要自行权衡。
- **系统级网络改动**：Gateway 模式、TUN、系统代理接管都会改动本机 / 局域网的网络走向，误配可能影响联网。
- **合规风险**：代理 / 翻墙类工具在不同司法辖区有不同法律约束，使用前需自行判断当地法规与用途。
- 本笔记**只读文档与元数据，未下载安装包、未安装运行、未接任何订阅**；功能描述均来自 README 自述。

## 什么时候会再看

- 想找"**不是 WebView 套壳**"的 macOS 代理客户端、对比不同 GUI 技术路线时；
- 想参考"**给个人工具内置 MCP server**"（让 Agent 读状态、默认改设置前先问）的做法时；
- 想研究"**非商用许可 + GPL 内核**"这种"客户端自有许可、内核独立运行隔离"的组合时。

## 相关笔记

- **同为"客户端侧的分流 / 规则"**：[Shadowrocket-Rules：小火箭分流规则配置](Shadowrocket-Rules-小火箭分流规则配置.md)——那是 iOS 小火箭的分流规则集（另有 Clash 版本），本篇是 macOS 上带图形界面的 mihomo 客户端；一个管"规则怎么写"，一个管"客户端怎么用"。
- **服务端侧的对照**：[mistgate：自建小规模代理车队面板](mistgate-自建小规模VPN车队面板.md)——那是**自建面板 + 节点车队**（Hysteria2 / VLESS / AmneziaWG / MTProto，AGPL-3.0）；本篇是**消费侧客户端**，两者常配套使用。
- **同为"自建代理"，一个管节点、一个用节点**：[vps-xray-reality：Xray 节点部署技能](vps-xray-reality-Xray节点部署技能.md)——那是把"单台 VPS 搭 Xray REALITY"写成 Agent 技能；本篇是把这些节点接进客户端的 GUI。
- **同属"网络出入口"的不同思路**：[cloudflared：Cloudflare 隧道客户端](cloudflared-Cloudflare隧道客户端.md)——那是 Cloudflare Tunnel 客户端，与本篇的"本地代理客户端"是两种不同的打通方式。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/21stware/bowel
- 中文 README：https://github.com/21stware/bowel/blob/main/README.zh-CN.md
- 架构说明：https://github.com/21stware/bowel/blob/main/docs/architecture.md
- 官网：https://21stware.github.io/bowel/
- 代理内核 mihomo：https://github.com/MetaCubeX/mihomo ；UI 工具包 MyGo：https://github.com/egoist/mygo

以上信息核验于 2026-11-09（仓库 2026-11-07 才创建，Star 数、Release 与内容变化很快）；GitHub API 匿名配额用尽，未取到 `size`。
