# openITCOCKPIT：给 Nagios / Naemon / Prometheus 做配置界面的开源监控平台

> 一个用 **Web 界面**把 **Nagios、Naemon、Prometheus** 的监控配置"点几下就建好"的**开源系统监控平台**：模板化配置、REST API、内置包管理器、HA、双因子、LDAP、多租户与分布式监控。**注意两件事**：这个仓库是**后端（提供 API 的服务器代码）**，前端在另一个仓库；许可是**双许可（GPLv3 / 商业 Enterprise）**。

- 状态：草稿
- 最近核验：2026-11-16（阅读 README 全文 230 行、`LICENSE` 首行、仓库文件树 5,704 项、ungh.cc 与 shields.io 元数据；**未安装、未部署、未登录 Demo、未接入任何监控引擎、未注册社区版**）
- 适用范围：**仅供了解其定位与许可结构**。安装面向 **Ubuntu / Debian**（含 arm64）；生产部署需按规模自备资源（见"使用前需要注意"）。

## 核心结论

**一句话定位（README 自述）**：`The open source configuration interface for Nagios, Naemon and Prometheus`——"so easy that everyone can use it"，把整套监控配置**用智能界面点几下就建出来**。

**三个最值得记的点：**

1. **它是"配置界面"，不是监控引擎本体**：README 明写这是 **backend server code**，**为官方前端提供 API**；前端在 **[openITCOCKPIT/openITCOCKPIT-frontend-angular](https://github.com/openITCOCKPIT/openITCOCKPIT-frontend-angular)**。它自己**不生产指标**——指标来自 Nagios / Naemon / Prometheus 这些引擎，它负责**把人点出来的配置变成引擎可读的配置**。
2. **双许可：GPLv3 或 商业 Enterprise**：`LICENSE` 段写明"openITCOCKPIT is dual licensed"——一是 **GNU GPL v3**；二是"**若你购买了 Enterprise Edition，则按 Enterprise 许可协议使用**"（协议与 key 随订单提供）。版权行为 `Copyright (C) 2015-2025 it-novum GmbH` 与 `Copyright (C) 2025-today AVENDIS GmbH`。另有 **[AVENDIS GmbH](https://avendis.com/) 提供商业支持**。
3. **部署与生态都比较"企业向"**：apt 仓库安装、**Ubuntu LTS / Debian**，**100% 兼容 arm64（含树莓派）**，提供**预构建 Docker 镜像**；CI 用 Drone 出 Noble / Resolute / Trixie / RHEL 8·9·10 / Docker 的 stable 与 nightly 包。README 内置**社区版注册 key**（`e5aef99e-817b-0ff5-3f0e-140c1f342792`，在 `System -> Registration` 注册后可装免费社区模块）。

**个人判断（未验证）**：这类项目的价值在"**把 Nagios 系的配置复杂度收进 GUI**"；但要注意它的**双许可 + 后端 / 前端分仓**结构，以及"社区版注册 key 直接写进 README"这种分发习惯——**功能边界的核心是"社区模块 vs Enterprise 模块"**，README 未逐条列出差异。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [openITCOCKPIT/openITCOCKPIT](https://github.com/openITCOCKPIT/openITCOCKPIT) |
| 描述 | "openITCOCKPIT is an Open Source system monitoring tool built for different monitoring engines like Nagios, Naemon and Prometheus." |
| 归属 | 组织 `openITCOCKPIT`；版权归 **it-novum GmbH / AVENDIS GmbH** |
| 许可证 | **双许可：GNU GPLv3 / 商业 Enterprise**（`LICENSE` 首行 `GNU GENERAL PUBLIC LICENSE / Version 3`，35,145 字节；README 说明 Enterprise 走另一套许可） |
| 主语言 | shields 显示 **78.6%**（后端以 PHP 为主，前端在 Angular 仓库） |
| 技术栈 / 构成 | **PHP 后端 + MySQL**，通过 **REST API** 给 Angular 前端提供数据；配置层对接 **Nagios / Naemon / Prometheus**；含**内置包管理器**、HA、2FA、LDAP、多租户、对象权限、[分布式监控](https://docs.openitcockpit.io/en/configuration/distribute-module/)、[Mod-Gearman](http://mod-gearman.org/)、[Statusengine](http://statusengine.org/) |
| 关注度 | **409 Star / 64 Fork**（ungh.cc，2026-11-16；shields 显示 **409 Star / 64 Fork**、**65 开放 Issue**、**贡献者 31**） |
| 维护状态 | **2015-08-19 创建**（已逾十年）；`pushedAt` **2026-11-12**；页面 `updatedAt` 2026-11-14；**仍在活跃维护** |
| 默认分支 | **`development`（非 `main`）**——日常开发分支即是默认分支，稳定版另有分支 / 发布渠道 |
| 版本 | **30 Release**（ungh releases 单页返回 30 条，为分页上限，实际可能更多） |
| 体积 | `development` 文件树共 **5,704 项**（ungh files API） |

## 部署与支持（按 README）

- **系统要求**：Ubuntu LTS 或 Debian；**2 CPU 核（x86-64 或 arm64）、4 GB 内存、60 GB 空间**；推荐 SSD 作主存储。
- **生产规模参考**：README 直言"没有黄金公式"，给出粗略指南：**32 GB 内存 / 16 核 / 500 GB 空间**。
- **安装方式**：官方 apt 仓库（`https://openitcockpit.io/download_server/`）；**arm64 完全兼容**（含树莓派）；提供**预构建 Docker 镜像**（`https://docs.openitcockpit.io/en/installation/docker/`）。
- **Demo**：`https://demo.openitcockpit.io/`，账号 `demo@openitcockpit.io` / 密码 `demo123`（装备了大部分社区许可模块）。
- **社区版注册**：登录后 `System -> Registration` 填社区许可 key 注册，再到 `System tools -> Package Manager` 装免费社区模块。
- **开发者入口**：搭建开发环境、做自己的翻译、写新模块、写新检查插件（文档链接见 README）。
- **安全**：漏洞请发 `security@openitcockpit.io`，已披露漏洞见 `https://openitcockpit.io/security/`。

## 使用前需要注意

- **⚠️ 双许可，边界要看清（事实）**：GPLv3 与商业 Enterprise 并行——**能用到哪些模块取决于你的许可**（README 只把社区版注册 key 与免费社区模块说清，**未逐条列出 Enterprise 专属功能**）。若用于商业分发 / 二次开发，须先确认许可义务。
- **⚠️ 这是后端仓库，不是完整产品（事实）**：README 明写本仓库是"**backend server code**，为官方前端提供 API"，**前端在另一个仓库**。想跑起来要**前后端一起部署**，别只看这一个仓库就以为拿到了全部。
- **⚠️ 默认分支是 `development`（事实）**：默认分支即开发分支，**"克隆默认分支"不等于"拿到稳定版"**；生产部署应走官方 apt / Docker 发布渠道，而不是直接拉 `development`。
- **⚠️ 真在生产用需要按规模备资源（事实）**：README 的 4 GB / 2 核只是"能装起来"，实际监控规模上去了按 32 GB / 16 核 / 500 GB 参考；**监控系统一旦成为运维依赖，可用性要求会更高**（HA、备份要自己规划）。
- **⚠️ 社区版 key 直接写在 README（事实，供参考）**：该 key 用于注册以获取免费社区模块——属**公开的分发方式**，但**它是否长期有效、是否有限制，本笔记未验证**。
- **⚠️ 我未做任何验证**：**未安装、未部署、未登录 Demo、未接入任何引擎、未注册社区版**。功能、系统要求与部署方式均**来自 README 与仓库文件本身**。

## 什么时候会再看

- 需要一套**给 Nagios / Naemon / Prometheus 做图形化配置**的开源平台、且希望**尽量少手写配置文件**时。
- 想参考"**开源监控平台的部署形态**"（apt / Docker / arm64 / 发行版矩阵）与"**双许可 + 社区模块 / Enterprise 模块**"的商业化切法时。
- 想对照"**运维/监控面板**"的不同形态（服务端平台 vs 本地桌面面板，见相关笔记）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"运维面板 / 自托管服务"**：[mistgate：自建小规模 VPN 车队面板](mistgate-自建小规模VPN车队面板.md)——那是 Go 单二进制 + SQLite 的自托管面板；本篇是 `development` 分支、PHP + MySQL、前后端分仓的企业向监控平台。可对照"**轻量自托管面板**"与"**企业级监控平台**"两种体量与部署方式。
- **同为"管理服务器"的桌面客户端形态**：[LeePanel：靠本地 SSH 管理服务器的开源 Linux 面板桌面端](LeePanel-靠本地SSH管理服务器的开源Linux面板桌面端.md)——那篇是**本地桌面客户端 + SSH** 管 Linux 服务器，本篇是**浏览器访问的服务端平台**；一个把运维界面搬到本地，一个是集中式平台。
- **同为"运维 / 安全资料与工具索引"**：[the-book-of-secret-knowledge：信息安全与运维清单集](the-book-of-secret-knowledge-信息安全与运维清单集.md)——那是给人看的清单（MIT），本篇是可落地的监控平台；可对照"资料索引"与"具体系统"两类记录。

## 参考来源

- 项目仓库与 README：https://github.com/openITCOCKPIT/openITCOCKPIT
- 前端仓库（README 指向）：https://github.com/openITCOCKPIT/openITCOCKPIT-frontend-angular
- 官网与安装文档：https://openitcockpit.io/download_server/ ；Docker：https://docs.openitcockpit.io/en/installation/docker/
- 元数据（Star / Fork / 开放 Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 主语言）：ungh.cc 镜像与 shields.io 徽章，2026-11-16

以上信息核验于 2026-11-16，Star 数、Release 与功能随时变化。**未安装、未部署、未登录 Demo、未接入任何监控引擎、未注册社区版**；功能、许可与部署说明均来自 README、`LICENSE` 首部与仓库文件本身。
