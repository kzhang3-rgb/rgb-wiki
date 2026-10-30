# cloudflared：Cloudflare Tunnel 客户端

> 记录它解决什么问题（不用在防火墙上开洞就能对外提供服务），以及"只支持一年内版本"这类容易被忽略的运维约束。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README、下载与部署文档入口、GitHub API 元数据，未安装运行、未建隧道）
- 适用范围：服务端（把内网/源站暴露到 Cloudflare 网络）与客户端（`cloudflared access` 访问 L4 源站）；Windows / macOS / Linux / Docker

## 核心结论

Cloudflare Tunnel 的命令行客户端（前身 Argo Tunnel），一个把流量从 Cloudflare 网络转发到你源站的守护进程。

它要解决的问题很具体：**让源站不必在防火墙上开洞**。守护进程夹在 Cloudflare 网络与源站（如 Web 服务器）之间，Cloudflare 接收客户端请求后经这个守护进程送回源站，源站可以保持尽可能封闭，不用暴露公网 IP 或开放入站端口。

除了 HTTP/WebSocket 转发，它还能用 `cloudflared access` 访问受 Tunnel 保护的 **L4（TCP）源站**，典型场景是 SSH、RDP；如果不想在客户端装 `cloudflared`，可以改用 WARP 客户端访问私有源站。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [cloudflare/cloudflared](https://github.com/cloudflare/cloudflared) |
| 组织 | `cloudflare`（官方仓库，非个人项目） |
| 许可证 | Apache-2.0 |
| 语言 | Go |
| 关注度 | 16,346 Star、1,508 Fork、139 关注、576 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2017-10-13 创建，最近推送 2026-10-29；仓库页显示 1,845 次提交、257 个 Tag |
| 发布物 | 独立二进制、Docker 镜像、Debian / RPM / Homebrew 包 |

> 关于"开放 Issue"这个数：API 的 `open_issues_count` 会把开放的 Pull Request 一并算进去，所以它通常大于页面 Issues 标签页显示的条数——这是**口径差异，不是数据错误**。本篇记录的是 API 值（576）。

## 使用前提与安装

**用之前要先在 Cloudflare 后台做两件事**（README 明确列出）：

1. 把一个网站添加到 Cloudflare 账号；
2. 把域名的 NS 改到 Cloudflare。

作者补充了一句很实在的说明：现在**技术上**已经可以在没有网站的情况下使用 Tunnel（比如私有路由），但"由于历史原因"这个前置要求仍然保留——也就是说，文档要求的步骤里有一部分是历史包袱，不是当下必需。

安装方式：独立二进制、Docker 镜像、Debian / RPM 包、macOS 用 Homebrew；Windows、Linux、macOS 的具体下载入口在开发者文档里；从源码构建需先装对应版本的 Go，再 `make cloudflared`。

**想先试再决定**：可以用 **TryCloudflare**，在还没把网站加到 Cloudflare 的情况下先跑通一次隧道。

## 使用前需要注意

- **只支持"最近一年内"的版本**：README 原文是"Cloudflare currently supports versions of cloudflared that are **within one year** of the most recent release"，并明确说，与功能可用性无关的破坏性变更**可能**被引入，从而影响一年以前发布的版本。**这意味着长期不升级的部署会自己烂掉**——这是使用该工具最容易被忽略的运维约束，值得单独记住。
- **破坏性变更的公告流程是写明的**：移除 CLI 参数、环境变量、配置键或命令都算破坏性变更，必须在 [Cloudflare Tunnel changelog](https://developers.cloudflare.com/changelog/product/tunnel/) 中、在移除它的那个版本发布之前公告。**升级前查这个 changelog 就能预先知道会不会踩到。**
- **从源码构建的门槛不低**：需要 GNU Make、capnp、**Go ≥ 1.27.3**，另有 capnpc-go、goimports、golangci-lint、gomocks 等可选工具。
- **`go.mod` 里的 Go 版本写法和常见习惯不同**：必须写成完整的 `major.minor.patch`（例如 `go 1.27.3`），不能省略补丁号（`.0` 版本也一样）；README 明确说 **`toolchain` 指令会被忽略，不要加**。Makefile 会用这个版本导出 `GOTOOLCHAIN=go<version>`，本机 Go 版本不符时 `make` 会自动下载对应工具链；想用本机的 Go 就加 `GOTOOLCHAIN=local`（但本机版本低于 `go.mod` 时会构建失败）。
- **升级 Go 要改多处**：`go.mod`、`.gitlab-ci.yml`、`.ci/image/Dockerfile`、`.github/workflows/check.yaml`、各 Dockerfile，以及 Linux CI 构建镜像的摘要都要同步。
- **我没安装、没建隧道、没跑过 CI**：以上全部来自文档自述，实际的连通性、性能、与现有反向代理的配合方式均未验证。
- **安全提醒**：把内网服务挂上公网入口，等于把原来的边界换了位置——Tunnel 省掉了开端口，但**访问控制、鉴权与暴露面仍需自己设计**，不要因为"没开端口"就默认安全。

## 什么时候会再看

- 需要**在不开放入站端口的前提下把本机 / 内网服务暴露到公网**时（本地开发联调、给自托管服务加公网入口、临时演示）。
- 需要**通过 Tunnel 访问 L4 源站**（SSH、RDP）时：看 `cloudflared access help` 与 WARP 客户端的取舍。
- 想参考**"长期支持的版本窗口"怎么写进 README**时：给出"一年内"的明确窗口 + 破坏性变更的预先公告渠道，这比只写"请保持更新"有用。
- 想参考**项目构建对工具链版本的严格管理**时：`GOTOOLCHAIN` 与 `go.mod` 补丁号的约定是个具体样本。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 同为"把服务暴露出去"的路线：[vps-xray-reality](vps-xray-reality-Xray节点部署技能.md)——那是自建 VPS 上的 Xray REALITY 直连节点（不依赖域名），本篇是借助 Cloudflare 网络的隧道（需要域名与 NS 托管），两条路线的取舍点在实际部署时对比。
- 可能的需要场景：[openclaw](openclaw-自托管个人AI助手.md)（自托管服务的公网入口）。
- 环境相关：[WSL](WSL-Windows上的Linux子系统.md)（Windows 上跑 Linux 工具链时，Tunnel 客户端常装在子系统里）。

## 参考来源

- 项目仓库与 README：https://github.com/cloudflare/cloudflared
- 官方文档（Cloudflare Tunnel）：https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel
- 版本支持与升级说明：https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/downloads/update-cloudflared/
- Tunnel 变更日志：https://developers.cloudflare.com/changelog/product/tunnel/
- 元数据（Star / Fork / 时间）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未安装运行，未建立隧道，连通性与性能均未验证。
