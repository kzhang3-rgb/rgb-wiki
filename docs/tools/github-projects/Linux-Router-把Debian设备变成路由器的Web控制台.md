# Linux Router：把 Debian / Armbian 设备变成路由器，并配一个 Web 控制台

> 它值得记的点不在"又一个路由器面板"，而在**把"改网络"这件事关进笼子**：**Web 服务以普通用户运行，只做页面、登录、CSRF 提交；真正执行系统查询与网络变更的是一个 root 权限的独立 Agent，双方只通过 Unix Socket 交换白名单操作**——一个无特权、一个有特权、中间隔一层白名单。**但它装/卸/修网络栈时会直接改宿主机网络，可能把连接打断**，**风险与便利绑在一起**。

- 状态：草稿
- 最近核验：2026-11-12（阅读 `README.md` 全文、`README.zh-CN.md` 全文、`DEPLOYMENT.md` 前 90 行、`LICENSE` 首行、仓库 `main` 文件树与元数据、GitHub 页面内嵌数据；**未 clone、未安装、未部署、未在真实设备上运行 `install.sh`、未跑测试、未审计源码**）
- 适用范围：**目标系统为 Debian 13 或基于 Debian 的 Armbian，使用 systemd + apt + NetworkManager**；把这类设备当"路由器 / 热点网关"用，并希望有个 Web 界面管理有线、Wi-Fi 与热点

## 核心结论

**一句话定位（README 自述）**：把 **Debian / Armbian 设备**变成路由器，并提供 Web 控制台，用于查看系统状态、管理有线网络与 Wi-Fi、开启热点共享、查看接入设备、执行维护工具。官网 `https://linuxrouter.dev`。

**它明确划出的三件事：**

1. **它是"控制台 + 提权 Agent"的双进程结构**：两个 systemd 服务——**`router-panel`（普通用户 `router-panel` 运行，Flask + Gunicorn，负责页面 / 登录 / CSRF / 提交操作）** 与 **`router-panel-agent`（root 运行，通过 Unix Socket 暴露白名单化的系统查询与网络变更）**。
2. **网络变更是"串行白名单"**：README 说网络变更由 Agent **串行执行**，Web 端提交后查询操作结果并刷新状态；**Web 不直接改网络，也不持 root**。
3. **它把"改网络有风险"写在最显眼处**：README 专门有 "Network Change Risks" 一节，明说安装 / 卸载 / 网络栈修复会修改宿主机网络配置，**可能导致网络连接中断**，并给了延迟应用与跳过配置的开关。

**三个最值得记的点：**

1. **"无特权 Web + 有特权 Agent + Unix Socket 白名单"这个权限切分**是本项目最值得抄的部分（与已收录的 [weclaws](weclaws-多用户微信AI智能体控制台.md) 的"控制面 / 运行面分离"是同一种工程纪律，只是这里落在"系统网络操作"上）。
2. **安装/卸载的行为边界写得很细**：默认卸载会恢复安装前保存的 NetworkManager / netplan / IPv4 转发 / `dhcpcd` 状态，并尝试删除项目创建的 `DebianRouterHotspot` 连接与 `ap-*` 虚拟接口；`--purge-data` 才删全部运行数据且不可恢复。
3. **它明确区分"延迟应用"与"立即应用"**：SSH 环境下网络变更**默认延迟**，需要立即应用必须显式加 `--apply-network-now`（这是运维友好、但容易被忽略的细节）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Jaksay/Linux-Router](https://github.com/Jaksay/Linux-Router) |
| 描述 | **仓库 GitHub 描述字段为空**（ungh 报 `description: null`，页面 `<meta name="description">` 也只是 GitHub 默认语）——**作者没有写仓库简介**；下面的定位取自 README 首句 |
| 自我定位（README 首句） | Turn a Debian or Armbian device into a router with a clean Web console for system status, wired networking, Wi-Fi, hotspot sharing, connected clients, and maintenance tools（把它变成路由器 + Web 控制台） |
| 归属 | 账号 `Jaksay`（个人；页面 `isOrg: false`、`isFork: false`、`isArchived: false`） |
| 官网 | <https://linuxrouter.dev>（页面 `sidebarAbout.website`） |
| 许可证 | **GPL-3.0（`spdxId: GPL-3.0`）**；README 写 "GNU General Public License v3.0 **or later**"，`LICENSE` 首行为标准 GPLv3 头（`GNU GENERAL PUBLIC LICENSE Version 3, 29 June 2007`，3 万余字符）。README 明说**允许商用，但分发修改版须以同协议提供对应源码** |
| 主语言 | **Python 66.7%**（shields.io `languages/top`）；文件树也印证为纯 Python + 原生 HTML/CSS/JS |
| 技术栈 | **Flask + Gunicorn**（Web）、**NetworkManager + wpa_supplicant + dnsmasq-base + iptables + iw + iproute2 + udev**（网络）、**systemd**（两个服务）、原生模板（`templates/*.html`）与手写 CSS/JS（无前端框架） |
| 关注度 | **175 Star / 9 Fork / 1 关注 / 0 开放 Issue**（页面 `stargazerCount` = ungh `stars` = 175，二者一致；Issue 取自 shields.io，2026-11-12） |
| 维护状态 | **2026-07-18 创建**；ungh 报 pushed **2026-07-28**；页面 /镜像 `updatedAt` **2026-11-05**（**注意：pushed 与 updatedAt 差 3 个多月，`updatedAt` 可能含 star 等元数据变动，不代表代码更新**） |
| 默认分支 | `main` |
| 版本 | **0 Release / 0 Tag**（页面 `releaseCount: 0`、`tagCount: 0`）——**没有任何版本化发布** |
| 话题标签 | **无**（页面 `topics: []`） |
| 仓库体积 | `main` 文件树约 **82 个文件**（ungh.cc 镜像） |
| 未取到 / 未核验 | **仓库 `size`（字节）未取到**（GitHub API 匿名配额用尽）；**贡献者数与提交历史未核验** |

## 运行架构：一个无特权 Web + 一个 root Agent

README 与 `DEPLOYMENT.md` 自述的分工：

| 组件 | 身份 / 形态 | 负责什么 |
| --- | --- | --- |
| `router-panel.service` | 普通用户 `router-panel`（`--shell /usr/sbin/nologin`，属 `router-panel` 组） | 页面渲染、登录、**CSRF 防护**、操作提交；查询操作结果并刷新状态 |
| `router-panel-agent.service` | **root** | 通过 **Unix Socket**（默认 `/run/linux-router/agent.sock`）暴露**白名单化**的系统查询与网络变更；网络变更**串行执行** |
| `agent.py` / `app.py` | 入口脚本（各 93 / 439 字节） | 分别拉起 Agent 与 Web（开发时手跑） |

**数据与代码分离**：程序目录默认 `/opt/linux-router`，运行数据默认 `/var/lib/linux-router`（`DEPLOYMENT.md` 的设备目录示例为 `/home/router-panel` + `/home/router-panel/data`）。README 强调**不要把运行时账号、密钥、网络配置放进源码目录**。

> **判断**：这种"**Web 无特权、改系统交给有特权的 Agent、中间只过白名单**"的结构，比"Web 进程直接 `sudo` 调 `nmcli` / `iptables`"要干净——攻击面被压到"能通过 Unix Socket 提交什么操作"，而 Socket 访问又用 `router-panel` 组限制。

## 你能用它做什么（README 功能亮点）

- **系统概览**：硬件信息、IP 地址、活动连接、存储、内存、运行状态。
- **依赖检查与引导修复**：NetworkManager、wpa_supplicant、dnsmasq、iptables、`iw` 等运行环境（仓库含 `router_panel/dependencies.py` 约 26 KB）。
- **有线和 Wi-Fi 管理**：扫描、连接、断开、配置绑定（profile binding）、忘记网络。
- **热点创建**：支持**独占 AP**，以及**网卡能力允许时的 AP+STA 并发**模式。
- **热点设备可见性**：查看客户端、DHCP 租约、无线信号详情、LAN 网段配置。
- **热点保活**：热点异常断线后自动尝试恢复（`router_panel/hotspot_keepalive.py`）。
- **工具能力**：Tailscale 登录辅助（`router_panel/tailscale.py`）、服务监控、密码修改、设备重启。

**代码规模观感（文件树，供参考）**：网络相关逻辑很重——`router_panel/network.py`（约 52 KB）、`network_operations.py`（约 20 KB）、`agent_server.py`（约 30 KB）、`core.py`（约 22 KB）；测试集中在单文件 `tests/test_application.py`（约 **85 KB**，并带 `tests/fixtures/` 下的 `iw` / `nmcli` 文本夹具）。

## 安装 / 更新 / 卸载（README 自述）

**目标设备无需安装 Git**：安装器自行从 GitHub 下载源码压缩包。

```bash
# 安装
curl -fsSL https://raw.githubusercontent.com/Jaksay/Linux-Router/main/install.sh \
  -o /tmp/linux-router-install.sh
sudo bash /tmp/linux-router-install.sh install

# 更新（只替换程序与 systemd 定义，保留管理员凭据 / 密钥 / LAN 网段；含健康检查，失败回滚旧版）
sudo bash /tmp/linux-router-install.sh upgrade

# 卸载（默认恢复安装前网络状态；--purge-data 才删全部运行数据，不可恢复）
sudo bash /tmp/linux-router-install.sh uninstall
sudo bash /tmp/linux-router-install.sh uninstall --purge-data
```

**网络变更相关的三个开关（README 原样，重要）：**

- `--no-network-config`：**跳过**写入与应用项目自带的 NetworkManager / netplan 配置（**但仍会启用 NetworkManager 并开启 IPv4 转发**）。
- `--defer-network-restart`：写入网络配置但**延迟应用**。
- `--apply-network-now`：需要**立即应用**网络变更时使用（默认在可能打断当前连接的场景下延迟）。

**首次登录**：安装器创建管理员 `admin`，密码在安装时随机生成并在结束时打印，同时写入 `/var/lib/linux-router/initial_password.txt`；README 要求**登录后立即修改**。

**开发运行**：先起 root Agent 再起 Web；默认监听 `http://127.0.0.1:80` 与 `http://<设备IP>:80`；测试命令 `python3 -m unittest tests.test_application`。

## 使用前需要注意

- **⚠️ 安装 / 卸载 / 网络栈修复会直接改宿主机网络，可能把你的连接打断**：README 明说这些操作可能**重载 NetworkManager、应用 netplan、启停 `dhcpcd`、删除项目创建的热点连接与无线虚拟接口**。**务必在本地控制台或维护窗口执行，并先备份宿主机网络配置**；**不要在一台你只能 SSH 进入、且没有带外（IPMI / 串口 / 物理）访问的机器上贸然跑**——一旦断网就回不去了。
- **⚠️ 远程（SSH）场景默认"延迟应用"，别误以为已经生效**：默认延迟只是"先不立即应用"，**最终网络状态在延迟期间并未改变**；延迟卸载时**运行态网络也不会立即恢复**，需要你在维护窗口再用 `--apply-network-now` 重跑。**且延迟模式不允许与 `--purge-data` 同时使用**（README 明确）。
- **⚠️ root Agent 是整个系统的权限核心，白名单是唯一屏障**：Agent 以 root 运行、通过 Unix Socket 执行"允许的操作"。**其白名单到底覆盖哪些命令、参数如何校验、Socket 的权限与对端校验如何做，我未审计源码（`agent_server.py` 约 30 KB、`network.py` 约 52 KB，均未通读）**。要真上生产，**这一段必须自己过一遍**。
- **⚠️ 初始密码以明文落在磁盘上**：`/var/lib/linux-router/initial_password.txt` 由安装器写入。**登录后立即改密并清掉该文件**；同时数据目录里还有**密码哈希、Flask secret key、LAN 配置、热点保活配置**——README 明确说**不应提交进 Git**，**备份、权限（`DEPLOYMENT.md` 建议数据目录 `0700`、归属 `router-panel:router-panel`）与磁盘加密要自己管好**。
- **⚠️ 没有任何 Release / Tag，"升级"跟着 `main` 走**：`releaseCount: 0`、`tagCount: 0`。**项目不提供版本化发布**，`install.sh` 从 `main` 拉源码——**意味着你无法按版本 pin 住一个可复现的构建**，只能信任当次 `main`。这在一台"拿到手就不再随手折腾"的路由器上，是个现实风险。
- **⚠️ 上游很新、代码更新可能已停滞（事实）**：**2026-07-18 创建，ungh 报 pushed 2026-07-28**——**距核验日约 3.5 个月**；尽管 `updatedAt` 显示 2026-11-05（**该字段未必等同代码提交**）。**175 Star、9 Fork、0 开放 Issue**：**"0 开放 Issue"既可能是稳，也可能是还没人认真用**，别据此推断它久经检验。
- **⚠️ 并发 AP+STA 依赖硬件能力**：README 明说是"**网卡能力允许时**"才支持 AP+STA 并发；**不是所有无线网卡都能同时当热点又连 Wi-Fi**，选硬件前先确认。
- **一个我存疑但未核实的点（不据此下结论）**：README 说 Web 服务**以普通用户运行**，却又说默认监听 **80 端口**。**普通用户如何绑定特权端口（是否靠 systemd 授予 `CAP_NET_BIND_SERVICE`、或走反代）README 未说，我未核验**——部署前请自行确认，别默认它能直接绑 80。
- **我未做任何验证**：**未 clone、未安装、未部署、未在真实设备运行 `install.sh`、未跑测试、未审计源码**；上述架构、分工、依赖清单、安装/卸载行为**均来自 `README.md` / `README.zh-CN.md` / `DEPLOYMENT.md`（前 90 行）与仓库文件自述**。GitHub API 匿名配额用尽，**未取到 `size` 与贡献者统计**。

## 什么时候会再看

- 想参考"**无特权 Web 服务 + root Agent + Unix Socket 白名单**"的权限切分写法（把需要 root 的系统操作集中收敛、Web 侧完全不持特权）。
- 想把一台 **Debian / Armbian 小主机 / 开发板 / 旧笔记本**变成带 Web 界面的路由器或热点网关（尤其是带 Wi-Fi 的管理型场景）。
- 想研究"**会改宿主机网络的安装器的安全卸载**"：如何保存 / 恢复 NetworkManager、netplan、`dhcpcd` 状态，如何用"延迟应用 + 显式 `--apply-network-now`"避免把运维自己关在门外。
- 想参考"**安装器自检依赖 + 引导修复**"的做法（NetworkManager / wpa_supplicant / dnsmasq / iptables / `iw` 的检查矩阵）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"自托管基础设施面板"，但落在硬件与网络层**：[Mistgate：给"自己的几台服务器"用的自建代理车队面板](mistgate-自建小规模VPN车队面板.md)——Mistgate 是 **Go 单二进制 + SQLite** 管**服务器代理节点**，本篇是 **Python/Flask + root Agent** 管**一台设备的有线 / Wi-Fi / 热点**。**两篇可对照"自托管面板如何做权限与进程切分"，也对照"用一份面板管多台远端服务器" vs "管本机网络"这两种取向。**
- **都涉及"本机网络 / 分流配置"，层面不同**：[Shadowrocket-Rules：小火箭分流规则配置](Shadowrocket-Rules-小火箭分流规则配置.md)——那篇是**代理分流规则**（客户端侧策略），本篇是**设备侧网络栈管理**（NetworkManager / 热点 / LAN 网段）。**想搭一台"能自己管网络的家用网关"时，两篇分别覆盖"上面跑什么代理规则"和"底下这台机器怎么管网"。**

## 参考来源

- 项目仓库：https://github.com/Jaksay/Linux-Router
- 英文 README：https://github.com/Jaksay/Linux-Router/blob/main/README.md
- 中文 README：https://github.com/Jaksay/Linux-Router/blob/main/README.zh-CN.md
- 部署与故障排查文档：https://github.com/Jaksay/Linux-Router/blob/main/DEPLOYMENT.md
- 安装脚本：https://github.com/Jaksay/Linux-Router/blob/main/install.sh
- 许可（GPL-3.0-or-later）：https://github.com/Jaksay/Linux-Router/blob/main/LICENSE
- 官网：https://linuxrouter.dev
- 元数据（Star / Fork / 时间 / 默认分支 / Release / 语言 / topics）：GitHub 页面内嵌数据、shields.io 与 ungh.cc 镜像，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**未 clone、未安装、未部署、未在真实设备运行 `install.sh`、未跑测试、未审计源码**；架构、分工、依赖清单与安装 / 卸载行为均来自 `README.md`、`README.zh-CN.md`、`DEPLOYMENT.md`（前 90 行）与仓库文件自述。**仓库 GitHub 描述字段为空、无 topics、无 Release / Tag**；`size` 与贡献者统计因 GitHub API 匿名配额用尽而**未取到**；root Agent 白名单实现与"普通用户如何绑定 80 端口"**均未核验**。**本项目会在安装 / 卸载时修改宿主机网络，存在断网风险，请务必先备份并在维护窗口操作。**
