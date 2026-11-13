# gitea：自托管的一体化 Git 服务平台

> 记录一个"自托管 GitHub 替代"的成熟方案：Go 单二进制、覆盖 Git 托管到 CI/CD，MIT 许可，2016 年至今仍在高速发版。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README、目录树与 GitHub API 元数据，未部署、未安装、未运行）
- 适用范围：Linux / macOS / Windows 自托管；单二进制 + 数据库即可起步

## 核心结论

Gitea 的口号是 **"Git with a cup of tea!"**——用 Go 写的**自托管一体化软件开发服务**。它把自己定位成"开箱即用、资源占用低"的一站式平台，功能面覆盖：

- **Git 托管**与**代码评审**（Pull Request）
- **Issue**、**看板（Kanban）**、**Wiki**、协作
- **包仓库**（Package Registry）
- **CI/CD**——并且**可复用 GitHub Actions 的 workflow**，降低从 GitHub 迁移的成本

它用 Go 编写、**跨全部主流平台**，这也是"单二进制部署、内存占用低"这一卖点的基础。

README 给出的入口比较直接：官方 Demo `demo.gitea.com`、托管服务 `gitea.com` 与 `cloud.gitea.com`、文档 `docs.gitea.com`，本地启动就是 `./gitea web`。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [go-gitea/gitea](https://github.com/go-gitea/gitea) |
| 描述 | Git with a cup of tea! Painless self-hosted all-in-one software development service. |
| 许可证 | **MIT**（`LICENSE` 约 1,097 B） |
| 语言 | Go |
| 关注度 | **58,889 Star、7,358 Fork、489 关注、2,196 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2016-11-01 创建**（超过 9 年），最近推送 **2026-11-04**；默认分支 `main` |
| 发行版 | 最新 **v28.3.0**（2026-11-01，**126 个资产**）；另有 `v1.27.3`（2026-08-29，186 个资产）——**两条版本线并存** |
| 体积 | 约 370 MB（`size` 字段） |
| 分支 / 贡献者 | 24 个分支；贡献者（API 上限 100）主力 `unknwon`（2,635）、`lunny`（2,158）、`GiteaBot`（2,046） |
| 根目录元文件 | `AGENTS.md`（约 2,841 B）、`MAINTAINERS`、`Dockerfile` / `Dockerfile.rootless`、`Makefile`（约 23 KB）、`go.mod`（约 10.5 KB） |
| 部署形态 | 单二进制、Docker（含 rootless 变体）、包管理器与云托管均有 |

## 用法要点（概览）

- **本地起步**：`./gitea web` 启动服务，按引导完成初始化（数据库、管理员账号）。
- **Docker**：仓库提供 `Dockerfile` 与 **`Dockerfile.rootless`**——后者用于无 root 运行，适合权限受限或更看重隔离的环境。
- **先试再装**：官方 Demo `demo.gitea.com` 可以在部署前先看界面与流程。
- **想省运维**：`cloud.gitea.com` 是官方托管；想自己建站`gitea.com`提供的是托管 Git 服务。

## 使用前需要注意

- **2,196 个开放 Issue 是"含开放 PR"的口径**：GitHub API 的 `open_issues_count` 把开放 Pull Request 也算在内，**不等于 2196 个未修 bug**。对一个近 6 万 Star、日更的项目来说，这个量级属正常。
- **两条版本线并存（`v28.x` 与 `v1.27.x`）**：这是最容易踩的坑——**升级前务必确认自己跟的是哪条线**，跨线升级通常不是直接跳版本号那么简单。生产环境升级前请读官方发布说明与升级指引。
- **MIT 许可宽松，但要留意品牌与依赖**：代码是 MIT；若做再分发，注意第三方依赖各自的许可与项目商标使用政策（仓库根目录有 `MAINTAINERS`、`LICENSE` 等文件，部署前应一并阅读）。
- **自托管 ≠ 零成本**：它省的是软件费用，不省运维——数据库、备份、升级、证书、对外暴露的安全面都归你。**Gitea 官方文档（`docs.gitea.com`）是唯一可信的一手来源**，不要照搬第三方教程里的旧配置。
- **对外暴露需谨慎**：若要公网访问，建议放在反向代理 / 隧道之后并启用 HTTPS（本站在 [cloudflared](cloudflared-Cloudflare隧道客户端.md) 一篇里记过隧道这种做法）。
- **`AGENTS.md` 有实质内容（约 2,841 B）**：说明项目自身也按"给 AI 助手交代上下文"的约定组织仓库，可作为"成熟项目怎么写 AGENTS.md"的样本（对照 [agents.md](agents.md-给编码助手用的AGENTS文件格式.md)）。
- 我**未部署、未安装、未运行**：以上功能与命令均来自 README 与元数据，未做实际验证。

## 什么时候会再看

- 需要一个**自托管、可完全自主掌控**的 Git 服务（代码不出内网）时。
- 想参考"**单二进制 + 低资源占用**"的自托管架构，或对比 Gitea / GitLab / Forgejo 等方案时。
- 想研究"**兼容 GitHub Actions**"如何降低平台迁移成本时。
- 需要复盘"**长期维护的大型自托管项目如何管理版本线**"（`v28.x` 与 `v1.27.x` 并存）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **自托管服务对外暴露的常见做法**：[cloudflared：Cloudflare 隧道客户端](cloudflared-Cloudflare隧道客户端.md)——自托管的 Gitea 想提供公网访问时，隧道是绕开公网端口暴露的一种选择。
- **同为"自托管服务"的另一个样本**：[mistgate：自建小规模 VPN 车队面板](mistgate-自建小规模VPN车队面板.md)——那里是 Go 单二进制 + SQLite 的自托管面板；两篇可对照**"自托管产品的部署与分发形态"**（Release 资产、Docker/rootless、版本节奏）。
- **真实项目里的 `AGENTS.md` 样本**：[agents.md：给编码助手用的 AGENTS.md 文件格式](agents.md-给编码助手用的AGENTS文件格式.md)——Gitea 根目录的 `AGENTS.md`（约 2.8 KB）是又一个大型样本。
- **同为"开源替代 + 自托管"**：[docuseal](docuseal-开源自托管电子签名与PDF表单平台.md)——那是 DocuSign 的开源替代（AGPL-3.0 + Section 7(b) 附加署名条款，另有付费 Pro 功能），本篇是自托管 Git 平台；可对照两者的许可策略与自托管运维成本。

## 参考来源

- 项目仓库与 README：https://github.com/go-gitea/gitea
- 官方 Demo：<https://demo.gitea.com>；托管服务：<https://gitea.com>、<https://cloud.gitea.com>
- 官方文档：<https://docs.gitea.com>
- 元数据（Star / Fork / Release / 分支 / 贡献者 / 时间）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读 README、目录树与元数据；未部署、未安装、未运行，未评估生产环境升级可行性。
