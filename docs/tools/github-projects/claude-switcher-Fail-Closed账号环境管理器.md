# claude-switcher：Fail-Closed 账号环境管理器

> 记录这个"把真实 IP 泄漏拦截在启动之前"的工具：它用 14 项环境指纹审计做启动前硬门禁，用进程级 Kill-Switch 兜底，用数据卷物理交换做多账号隔离；以及它自划的合规边界与本机的常驻组件。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README 全文与 GitHub API 元数据，并用默认分支 `main` 的 commits 接口复核活跃度；未安装、未运行任何脚本或守护进程）
- 适用范围：主平台 macOS（需代理客户端支持 TUN + Claude 桌面版 + python3）；Windows 与 Linux 为 Beta

## 核心结论

把"多账号环境是否干净"做成**启动前的硬门禁**。它的立场写在 README 里：

> 防御不建立在"用户记得"上，而建立在系统级的硬性约束上。

也就是说，代理暴毙、软件静默升级、随手乱点这些场景下，**受保护进程会被保镖在秒级终止**，裸连窗口被压到最小。作者同时明确了它的边界：这是**进程级急停，不是防火墙级保证**——10 秒巡检周期内存在固有的 TOCTOU 窗口。

它有 6 个自述的核心机制：

| 机制 | 做什么 |
| --- | --- |
| **Block-on-Fail 审计引擎** | 每次启动前跑 14 项环境指纹审计，**硬性安全项失败就不拉起应用**（非关键项可显式关闭或降为警告，关闭≠通过） |
| **手写 STUN 协议栈的 WebRTC 探针** | 不依赖浏览器内核，直接用 `socket` + `struct` 构造 RFC 5389 Binding Request 解析 XOR-MAPPED-ADDRESS；探测列表含全球节点与国内骨干节点做**交叉验证**（走隧道应回传代理出口，裸连时国内节点必秒回并"当场抓获"真实 IP） |
| **Fail-Closed Kill-Switch** | 常驻守护进程以 10s 周期巡检隧道健康度（内核进程 × 虚拟网卡双因子），隧道中断即终止受保护进程；与客户端自带的 Fail-Open 容错重试相反 |
| **Session Snapshot 快照隔离** | 多账号不是"登出再登入"，而是**物理交换数据卷**：每个账号持有独立设备指纹、OAuth 凭证与本地状态，切换时原子封存/载入；归档是同卷 rename（瞬时），载入走 APFS clonefile / reflink 的写时复制 |
| **配置漂移自愈 Watchdog** | 周期巡检 + 基线回滚 + 全程留痕（README 称生产环境里 TUN 开关被代理客户端更新重置过 4 次、系统时区被改回 8 次） |
| **Pinned Routing 置顶路由** | 目标服务全家族域名及其生态依赖（Turnstile / Telemetry / Payment / Support）置顶锁定代理路由，优先级高于任何第三方分流规则集 |

14 项审计覆盖：代理内核、TUN 网卡、出口 IP、IP 一致性（含 **48 小时出口 IP 历史，变化 ≥3 次亮黄灯不阻断**）、时区、地区 Locale、IPv6 双栈旁路、DNS 隧道（Fake-IP 校验）、WebRTC、保镖守护进程、置顶路由规则、客户端语言、ASN 属性、生态可达性。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [YOURSJEYO/claude-switcher](https://github.com/YOURSJEYO/claude-switcher) |
| 描述 | "Fail-Closed 账号环境管理器：11 项环境指纹审计（Block-on-Fail）、手写 STUN 协议栈的 WebRTC 泄漏探针、Kill-Switch 断网急停、多账号快照隔离、配置漂移自愈" |
| 许可证 | **AGPL-3.0** |
| 主语言 | PowerShell（Windows 版；macOS 版为 Swift/SwiftUI + bash） |
| 关注度 | **1,495 Star、148 Fork、1 关注、4 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2026-10-14 创建；`pushed_at` 2026-11-02；**默认分支 `main` 最新提交在 2026-11-02** → 判定为**真实活跃** |
| 体积 | 约 4.6 MB（API `size` 4,693 KB） |
| 其他入口 | 有 GitHub Pages（零安装 Web 检测页）；无 topics、无 Discussions |
| 平台 | 主 macOS；`windows/`（PowerShell + WPF）与 `linux/`（bash + systemd）均为 **Beta** |

**一处口径不一致**：仓库描述写"**11 项**环境指纹审计"，README 的审计表列的是 **14 项**（编号 01–14）。看着像是项目从 11 项扩到 14 项后，GitHub 描述没跟着改。以 README 为准，但两处数字对不上这一事实值得记下。

## 安装

**系统要求**：macOS + 代理客户端（Clash Verge / FlClash / Shadowrocket / sing-box / ClashX / Surge 任一，需 TUN 可用）+ Claude 桌面版 + `python3`。

官方推荐从 Releases 下载 `ClaudeSwitcher-<版本>-installer-kit.zip`（首次安装），解压后双击 `installer/安装.command`，会安装核心脚本、守护进程与桌面 App；只需替换界面时用 `-app-only.dmg` / `.zip`（不含核心脚本）。从源码则是 `git clone` 后双击同一个 `安装.command`。

**macOS 产物是 ad-hoc 签名、未公证**，首次打开可能被 Gatekeeper 拦截（右键打开或到「隐私与安全性」放行）；每个 Release 附带 `SHA256SUMS-release.txt` 可校验。

**地区自适应**：安装程序按当前出口 IP 的国家自动锁定"目标国家 + 时区 + 地区格式"三件套（出口是 CN 会直接拒装）；换国家节点后不用重装，跑一次 `claude-switch.sh set-region` 重新适配。多账号支持 A–D 四个槽位，切换用 `claude-switch.sh switch A|B|C|D`。

## 使用前需要注意

**① 项目自划的合规边界要看清。** README 的「用途声明」原文：

> 本项目用于在合规地区管理多个 Claude 账号的本地环境与登录态，面向因出差、旅居等原因身处支持地区的用户。请遵守 Anthropic 服务条款；**禁止用于账号转售、共享、代注册或规避地区限制**。本地环境检查只影响本机配置，不改变服务端记录，也不保证账号不被限制。

**这一段只作信息记录，不代表我的建议，也不对使用行为的合规性作定性。**

**② AGPL-3.0 的传染范围。** 许可证是 AGPL-3.0，若要把它的代码并入对外提供网络服务的项目，需先确认传染义务。若只是本地自用则无此问题。

**③ 它会在本机常驻几个组件，且退出切换器不会卸载它们。** README 明确：退出切换器**不会**卸载或停止独立的**保镖、时区看门狗和自动更新服务**，应用本身也不会因此新增登录自启动——意思是这些常驻件要自己清楚怎么停。此外它会**改系统时区/区域设置**（会弹一次授权）、需要 root 权限边界。安全类工具本身改系统配置，装之前建议先读它的 `SECURITY.md`。

**④ 跨身份保留默认关闭是有意的。** 若开启（在 `keep-in-workspace.txt` 里列相对路径），这些文件会成为**多个账号之间的共同特征**；README 自己提示不要放登录凭证、Cookie、设备标识或任何账号专属数据。

**⑤ "实战战绩"与日志脱敏都是作者自述。** README 展示的两条拦截记录（代理离线自动关闭 Claude、时区异常自动恢复）来自作者自述日志；日志"不记录公网/住宅/代理 IP、token/session/cookie"也是自述。我没有审计代码验证。

**⑥ 未安装、未验证。** 没有安装、没有运行任何脚本或守护进程，机制与行为全部来自 README 自述。

## 什么时候会再看

- 想研究"多账号环境隔离 + 启动前硬门禁（Fail-Closed）"这套工程做法时。
- 想参考"把风控对抗从'靠用户自觉'改成'系统级约束'"的思路，以及它怎么把固有边界（TOCTOU 窗口）主动写进文档时。
- 想参考它的**零安装 Web 检测页**（纯前端、无追踪、无 localStorage，请求只发公共 API）的隐私设计时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"Claude 账号与环境"主题**：[CCBaga](CCBaga-Claude身份与浏览器环境管理工具.md)——那是管理本地 Claude 身份并校验本机浏览器环境，本篇是把环境审计做成启动前硬门禁并做多账号快照隔离；一个偏"身份与浏览器"，一个偏"网络环境与进程急停"。
- **同为"Claude 账号与环境"主题**：[Claude-Guardian](Claude-Guardian-Claude账号防封与环境准备Skill.md)——那是给 Agent 用的环境准备与检查 Skill（含三档分级授权），本篇是给人用的桌面工具；可对照两种交付形态怎么处理同一类问题。
- **它的 Pinned Routing 会盖过第三方分流规则**：[Shadowrocket-Rules](Shadowrocket-Rules-小火箭分流规则配置.md)——那是分流规则配置，本篇选择"不信任、不验证、直接置顶覆盖"规则库，可对照两种截然不同的路由治理取向。

## 参考来源

- 项目仓库与 README：https://github.com/YOURSJEYO/claude-switcher
- 安全边界说明：仓库 `SECURITY.md`
- 零安装 Web 检测页：https://yoursjeyo.github.io/claude-switcher/
- Windows / Linux 说明：仓库 `windows/README.md`、`linux/README.md`
- 元数据（Star / Fork / 订阅数 / 体积 / 许可证）：GitHub REST API，2026-11-03

以上信息核验于 2026-11-03。未安装、未运行，实际行为与安全性均未验证。
