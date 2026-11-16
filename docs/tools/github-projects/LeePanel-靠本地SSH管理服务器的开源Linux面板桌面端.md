# LeePanel：把 Linux 面板搬进桌面端、靠本地 SSH 管服务器的开源面板

> 它的价值不在"又做了一个服务器面板"，而在**把"面板"这个东西的部署位置反过来**：传统 Web 面板（宝塔那一路）是**把 PHP 面板代码装到服务器上、开 8888/8080 端口、以 root 运行**，于是"面板自身"成了首要攻击目标；LeePanel 则做成**桌面客户端（Tauri 2 应用）**，**服务器上零安装、零驻留、不额外开端口**，所有操作都从你本地通过**已有的 SSH 通道**下发命令。**这是它最值得记的一点，也是它最大的能力边界**——面板不在服务器上，就只能在你能连上、且本地开着 App 的时候管服务器。

- 状态：草稿
- 最近核验：2026-11-12（阅读 README 全文、`README.zh-CN.md`、`LICENSE`、仓库文件树、`package.json` 与页面内嵌元数据；**未 clone、未编译（`npm run tauri dev` / `tauri build`）、未安装客户端、未连接任何服务器、未实际安装 LNMP / 管理站点 / 数据库 / Docker、未读 Issue 内容、未审计其 SSH 命令构造与凭据处理**）
- 适用范围：**桌面客户端形态**，用本机 SSH 连远端 Linux 服务器（README 称**当前功能仅在 Ubuntu / Debian 上测试过**）；自带安装包支持 Windows 10/11、macOS 12+、Linux x64/arm64；开发需 Node.js 18+ 与 Rust 工具链

## 核心结论

**一句话定位（README 自述）**："LeePanel — 免费开源，下一代 Linux 服务器管理面板软件。……所有操作通过本地 SSH 完成、服务器上零安装零残留，从根本上消除了传统 web 面板的安全风险。"

**它明确划出的取舍（README 原表）：**

| 维度 | 传统 Web 面板（README 视角） | LeePanel（README 自述） |
| --- | --- | --- |
| 部署方式 | 在服务器上安装运行面板代码，占用资源 | 完全在桌面端运行，不在服务器上安装 |
| 端口暴露 | 向互联网开放 8888/8080 端口 | 仅使用你已有的 SSH 端口 |
| 攻击面 | 面板本身成为首要攻击目标 | 服务器保持你原始的配置不变 |
| 安全风险 | 面板以 root 运行，漏洞 = 服务器完全沦陷 | 不介入服务器，自称无此风险 |
| 卸载 | 需 SSH 进服务器执行卸载脚本，残留难清理 | 关闭应用即可，自称零残留 |
| 安全指数 | ⭐⭐ | ⭐⭐⭐⭐⭐（**自述，未核验**） |

**三个最值得记的点：**

1. **把"面板装在哪"这个前提整个换掉了**：不是"更好的 Web 面板"，而是**桌面面板 App**——服务器端什么都没有，只有你自己的 SSH 端口。**用架构换掉了"面板自身被攻破"这条攻击面。**
2. **代价也随之而来（这是取舍，不是缺点）**：**面板不在服务器上，就不存在 7×24 后台**——关掉 App 或本地连不上就没法管；也**天然不适合多管理员协作 / 团队账号**。判断它是否适合你，先看这条。
3. **GUI 覆盖面在同类里算全**：SSH 终端 + SFTP + Nginx/MySQL/MariaDB/PHP-FPM（LNMP）+ 站点与 Let's Encrypt SSL + 数据库/Redis + Docker + 防火墙/BBR/系统监控，**全部塞进一个约 6 MB 的跨平台桌面客户端**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [gna1280072/LeePanel](https://github.com/gna1280072/LeePanel) |
| 描述 | Linux VPS control panel free software open source |
| 归属 | 账号 `gna1280072`（个人；页面 `isOrg: false`、`isFork: false`、`isArchived: false`、非 mirror、非 template） |
| 许可证 | **MIT**（`LICENSE` 1,065 字符，首行 `MIT License`，署名 `Copyright (c) 2026 LeePanel`） |
| 主语言 | **Rust 46.7%**（shields.io；README 徽章另标 Tauri 2.x / React 19 / TypeScript / Rust / Vite） |
| 关注度 | **674 Star / 51 Fork / 4 关注 / 17 开放 Issue**（Star、Fork、关注取 **GitHub 页面值**；Issue 数取自 shields.io，2026-11-12。**⚠️ 与 ungh.cc 镜像存在差异：镜像报 Star 659 / Fork 50 / 关注 4，与页面不一致，以页面为准**） |
| 维护状态 | **2026-07-05 创建**；ungh 报 pushed **2026-10-24**（距核验日约 3 周）；页面 `updatedAt` 为 **2026-11-06** |
| 默认分支 | `main` |
| 版本 | 页面内嵌数据：**8 Release / 23 tags / `packages: true`**；仓库内 `package.json` 版本号为 **1.0.23**（`"private": true`） |
| 官网 / 主页 | 官网 https://www.leepanel.com ；GitHub Pages https://gna1280072.github.io/LeePanel/ |
| 话题标签 | `docker` · `leepanel` · `linux` · `lnmp` · `php` · `vps` |
| 仓库文件数 | `main` 文件树 **146 个文件**（据 ungh.cc files 接口） |
| 其它 | 有 Discussions 入口（README 引导反馈）；贡献者数**未核验**；仓库体积 `size` **未取到**（GitHub API 匿名配额用尽） |

## 它和传统面板的根本差别

**LeePanel 把自己定位成"传统 Web 面板的替代品"**，差别集中在**部署位置**：

- **传统面板（如宝塔一路）**：在服务器上装一套 Web 程序 + 面板端口，浏览器登录管理；**面板自己就是一个常驻的、以高权限运行的网络服务**。
- **LeePanel**：**桌面 App 作客户端**，服务器端**不装任何面板代码**、**不额外监听端口**；操作以 **SSH 命令**在远端执行，文件走 **SFTP**。

**这条差别同时是它的"安全卖点"和"能力天花板"**：换掉了面板自身的攻击面，也换掉了 Web 面板"随时随地打开浏览器就能管"和"后台常驻"的便利（README 未提后台任务 / 定时调度 / 多用户能力）。

## 能管什么（README 功能清单）

| 模块 | 内容（README 自述） |
| --- | --- |
| 连接与终端 | SSH 密码 / 密钥认证与凭据保存；基于 **xterm.js** 的完整终端（剪贴板、链接、搜索）；断线自动重连；多服务器侧栏一键切换、并发各会话互不阻塞 |
| 文件管理 | 远端文件浏览、拖拽上传下载、大文件**分块上传**、压缩包解压 / 打包（zip、tar.gz）、`chmod`、批量复制/移动/删除/重命名、收藏、目录缓存 |
| LNMP 栈 | 一键安装 Nginx / MySQL / MariaDB / PHP-FPM（可选版本）；服务启停重启重载；实时状态；PHP 多版本管理与切换 |
| 站点管理 | Nginx 虚拟主机创建与配置；**Let's Encrypt** 免费 SSL；带 WebSocket 的反向代理；防盗链；按站点切 PHP 版本；rewrite 规则；站点启停删除 |
| 数据库 | MySQL / MariaDB 库的增删改查；用户权限（localhost / any host / 指定 IP）；zip 备份还原；SQL 文件导入；root 密码管理；库备注 |
| Redis | 基于 SCAN 分页的键浏览；键增删改查与 TTL；DB0–DB15 切换；带确认的 flush；备份还原 |
| Docker | 容器生命周期；镜像拉取/删除/运行；容器日志；Docker 镜像源配置；一键装 Docker（多镜像源） |
| 系统与网络 | CPU/内存/磁盘/网络实时监控；进程列表与资源占用；防火墙规则（**ufw / firewalld / iptables**）；**BBR** 拥塞控制；系统信息面板；运行时长 |
| 软件源 | apt 集成；自定义软件源；安装/卸载/更新；可用 PHP 版本浏览 |
| 服务器设置 | SSH 认证方式（密码/密钥）管理；SSH 密钥生成（RSA、Ed25519、ECDSA）与部署；普通/强制重启；文件缓存管理 |
| 国际化 | README 声称支持**英文与简体中文**；本地持久化语言偏好；应用内切换 |

## 技术栈与仓库结构

**技术栈（README 原表，字段与仓库 `package.json` 大体一致）：**

| 层 | 技术 |
| --- | --- |
| 桌面框架 | **Tauri 2.x**（本次核验时 `@tauri-apps/api` ^2.11.0） |
| 前端 | **React 19**（`react` ^19.2.7）+ TypeScript |
| 构建 | **Vite**（`vite` ^8.0.12） |
| 终端 | **xterm.js**（`@xterm/xterm` ^6.0.0，另用 clipboard / fit / web-links 插件） |
| SSH | **russh**（Rust） |
| SFTP | **russh-sftp**（Rust） |
| 存储 | **SQLite（rusqlite）** |
| 国际化 | **react-i18next** |

**目录结构（据文件树，未读实现）：**

- `src-tauri/`：Rust 后端——`src/commands/` 下分 `ssh`、`server`、`server_ops`、`ssl`、`tfa`、`port`、`tunnel`、`audit`、`credentials`、`fb`、`config` 等模块；顶层另有 `ssh.rs`、`ssl_cert.rs`、`tunnel.rs`、`db.rs`、`credentials.rs`、`permissions.rs`、`audit.rs`；`capabilities/default.json` 为 Tauri 权限清单。
- `src/`：React 前端——`components/panels/` 下按模块拆分（Dashboard、Nginx、Php、Database、Redis、Docker、Firewall、Monitor、Ssl、Sites、SiteLogs、SoftwareRepo、Bbr、Port、Tunnel、TwoFa、ServerSettings、Update 等）；另有 `AuditLogDialog`、`HostKeysDialog`、`FileBrowser`、`Terminal`、`Sidebar`、`QuickCommandsBar`。
- `src/i18n/`：`en`、`zh-CN`、`zh-TW`、`ar`、`de`、`fr`、`ja`、`ko`、`pt`、`ru` 共 **10 个 locale JSON**。
- 其它：`.github/workflows/build.yml`（构建流水线）、`tests/i18n.test.ts`（Vitest）、`scripts/`（几个 i18n 注入脚本）、`public/img/01–03.png`（README 预览截图）。

## 使用前需要注意

- **⚠️ "零服务端代码"同时是它的能力边界（最先要判断的一条）**：面板**不在服务器上**，所以**关掉 App / 本地断网 / SSH 连不上时，它就完全管不了服务器**，也**不存在后台常驻任务**；**多管理员协作、团队分账号这类场景 README 未提供支持**。它是"单人在桌面客户端上手动运维"的形态——**别指望它像 Web 面板那样 7×24 挂在那**。
- **⚠️ 把 root 级操作 GUI 化，凭据与私钥存在本地**：LNMP 安装、防火墙、**root 密码修改**、SSH 认证方式切换、SSH 私钥生成与部署都被做成了界面操作。好处是省事，代价是**本地凭据库（SQLite）一旦被读取 ≈ 一把能连上你所有服务器的钥匙**。**README 未说明凭据/私钥是否加密存储（我未核验 `credentials.rs` 实现）**，**本地机器的磁盘加密与凭据文件权限得自己管好**。
- **⚠️ README 的"安全指数 ⭐⭐⭐⭐⭐"是自述，不是审计结论**：那张对比表是项目自己的定位话术。**我未审计它构造的 SSH 命令是否做了命令注入防护、私钥如何处理、卸载/删除类操作是否有二次确认**。"不装面板所以更安全"这个逻辑成立，但**不等于"这个客户端本身没有安全问题"**。
- **⚠️ 运维 GUI 化 = 误操作代价高**：一键装 LNMP、一键 flush Redis、一键重启（含强制重启）、批量删除文件——**界面点错就是线上事故**。用它管生产环境前，先想清楚权限收窄与备份策略（README 未提权限分级 / 操作审计导出）。
- **⚠️ README 明确说"仅 Ubuntu / Debian 测试过"**：其它发行版（CentOS、Alpine 等）**未验证**；而它的防火墙模块又同时适配 ufw / firewalld / iptables，**跨发行版的实际可用性未核验**。
- **⚠️ 国际化声称与仓库文件不符**：README 只说支持**英文 + 简体中文**，但 `src/i18n/` 里实有 **10 个 locale JSON**（含 zh-TW、ja、ko、ar、de、fr、pt、ru）。**这些语言是占位还是可用，我未核验**——README 口径比文件更保守。
- **⚠️ README 的功能清单不完整，代码里已有它没写的模块**：文件树里出现 **`audit`（审计日志）、`HostKeysDialog`（主机密钥校验）、`TwoFaPanel`（2FA）、`tunnel`（隧道）、`port`（端口）** 等 README 未展开的能力。**这是按文件名推断，我未读实现**——想知道真实能力边界，得看代码而不是 README。
- **⚠️ Star 数镜像与页面不一致**：GitHub 页面 **674 Star / 51 Fork**，ungh.cc 镜像报 **659 / 50**，以**页面**为准；差异原因未查明。仓库体积 `size` 与贡献者数**未取到 / 未核验**（GitHub API 匿名配额用尽）。
- **⚠️ 项目很新、沉淀薄（事实）**：**2026-07-05 创建**，5 个月即有 674 Star，说明传播快；但 **17 个开放 Issue 的内容我未阅读**，**README 之外没有第三方验证**。**Star 涨得快不等于久经检验**，尤其这类"直接操作 root 的运维工具"。

## 什么时候会再看

- 想给**单人 / 小团队**的 VPS 找一套"**不想在服务器上装面板**、但又想有图形界面省事"的运维工具。
- 想参考"**把传统服务端 Web 面板反过来做成桌面客户端 + 本地 SSH**"这种架构取舍（安全面 vs 便利性、常驻 vs 客户端）。
- 想研究 **Tauri 2 + Rust（russh / russh-sftp）+ React** 如何组合出一个**跨平台、体积约 6 MB** 的桌面运维客户端（含 xterm.js 终端、SQLite 本地存储、i18n）。
- 想对照"**面板 / 控制台该装在哪一侧**"这一设计问题：本篇是"装在你自己的桌面上"，传统面板是"装在服务器上"，可与其他自托管控制面项目对比。
- 评估"**用 GUI 一键操作 root 级配置**"的风险边界（凭据本地存储、误操作、审计缺失）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"跨平台桌面 / 远程运维工具"，但控制对象不同**：[crossdesk：轻量跨平台远程桌面与 MCP 远控服务端](crossdesk-轻量跨平台远程桌面与MCP远控服务端.md)——那篇是**跨平台远程桌面 + 远控**，本篇是**跨平台桌面客户端 + 用 SSH 管 Linux 服务器**；两者都是"把一个原本在浏览器 / 服务端的运维界面搬到本地客户端"，可对照其协议栈（远控协议 vs SSH/SFTP）与体积取舍。
- **同落在"VPS / 服务器运维"场景，但一个是面板一个是技能包**：[vps-xray-reality：Xray 节点部署技能](vps-xray-reality-Xray节点部署技能.md)——那篇面向**在 VPS 上部署 Xray 节点**的具体交付，本篇是**管理 VPS 上 LNMP/站点的通用面板**。若你的 VPS 用途正是搭节点，两篇可先后阅读：先建好可用服务，再考虑是否用面板做后续管理。
- **同为"跨平台桌面运维 / 数据客户端"，控制对象与许可不同**：[LakeDB：本地优先的多数据库桌面客户端](LakeDB-本地优先的多数据库桌面客户端.md)——那篇是**多数据库桌面客户端**（凭据本地加密、**专有许可**）；本篇是**用 SSH 管 Linux 服务器的面板桌面端**（开源）。两篇都把"原本在服务端 / 浏览器做的操作搬到本地客户端"，可对照其凭据处理与许可形态。

- **同为"管理服务器"的服务端 / 客户端两种形态**：[openITCOCKPIT：给 Nagios / Naemon / Prometheus 做配置界面的开源监控平台](openITCOCKPIT-给Nagios与Prometheus做配置界面的监控平台.md)——那是浏览器访问的集中式监控平台（PHP + MySQL、双许可 GPLv3 / 商业版），本篇是本地桌面客户端 + SSH；一个把运维界面集中到服务端，一个搬到本地。

## 参考来源

- 项目仓库与 README（英文）：https://github.com/gna1280072/LeePanel
- 中文 README：https://github.com/gna1280072/LeePanel/blob/main/README.zh-CN.md
- 许可（MIT）：https://github.com/gna1280072/LeePanel/blob/main/LICENSE
- 前端依赖与版本（`package.json`）：https://github.com/gna1280072/LeePanel/blob/main/package.json
- 官网：https://www.leepanel.com ；项目页：https://gna1280072.github.io/LeePanel/
- 元数据（Star / Fork / 关注 / 时间 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12；开放 Issue 数与主语言取自 shields.io

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**仅阅读了 README（中英文）、`LICENSE`、`package.json`、仓库文件树与页面内嵌元数据**；**未 clone、未编译（`npm run tauri dev` / `tauri build`）、未安装客户端、未连接任何服务器、未实际安装 LNMP / 管理站点 / 数据库 / Docker、未读 Issue 内容、未审计 SSH 命令构造与凭据/私钥处理、未核验各语言文案完成度**。项目信息、功能清单、技术栈与"零服务端代码"架构**均来自 README 与仓库文件自述**；仓库体积 `size` 与贡献者数**未取到**；Star / Fork 的页面值与 ungh 镜像值不一致，已在表中注明。**"安全指数 ⭐⭐⭐⭐⭐"是项目自评，非独立验证结论。**
