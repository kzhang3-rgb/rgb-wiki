# tymeslot：Calendly / Cal.com 的开源自托管替代

> 一个用 **Elixir / Phoenix LiveView** 写的**会议预约平台**，主打"**日程软件该是你的**"——可以**一条 Docker 命令自托管**，也可以用官方在**欧盟境内托管**的云版；预约页支持**四种主题**、与 **Google / Outlook / iCloud / CalDAV** 双向同步、自动在 **Meet / Teams / Zoom / Jitsi** 等开会。它的许可与"开放核心"口径值得在选型时回看。

- 状态：草稿
- 最近核验：2026-11-16（阅读 README 全文 197 行、`LICENSE` 首行、仓库文件树 7,560 项、ungh.cc 与 shields.io 元数据；**未部署、未注册云版、未连任何日历、未发起任何预约**）
- 适用范围：**仅供了解其功能与许可口径**。自托管需自备 **PostgreSQL 14+** 与一台可对外提供服务器的机器；"同码无门控"是**厂商自述**（见"使用前需要注意"）。

## 核心结论

**一句话定位（README 自述）**：`The open-source, self-hostable alternative to Calendly and Cal.com`——"Run it on your own server, or let us host it in the EU. **Same code, no gated features.**"

**三个最值得记的点：**

1. **自托管体验被当作一等公民**：README 给出的自托管是**一条命令**——`docker run` 起一个容器（**内置 Postgres**），浏览器打开 `4000` 端口，**第一个注册的账号即成为管理员**；另有 Cloudron、Railway 一键部署入口，`README-Docker.md` 覆盖 SMTP / TLS / Compose / 外部数据库。
2. **"自托管不是试用版"是它反复强调的卖点**：对比表把 Self-hosted 与 Cloud 并排，Self-hosted 标"**Free, forever**"，Cloud 是"Free 计划 + **Pro €9/月**"，并写明"**Self-hosted is not a trial edition: it gets every feature**""No add-ons and no per-feature upsells"。**这是厂商的自述口径，本笔记未逐个验证云版是否真的没有专属功能。**
3. **功能面接近成熟商业产品**：预约页（**Quill / Rhythm / Paper / Folio 四套主题**，可自定义配色与背景、白标）、会议类型（时长 / 自定义问题 / 预约上限 / 需审批 / 多地点）、可用性（具名时间表、缓冲、最短通知、预约窗口）、**双历同步**（Google / Outlook / iCloud / 任意 CalDAV，任一冲突即阻塞该时段）、视频与地点（自动建 **Google Meet / Teams / Zoom / Jitsi / MiroTalk / kMeet / Nextcloud Talk** 房间）、邮件（`.ics` 邀请、提醒、无需登录的取消 / 改期链接）、自动化（**HMAC 签名的 webhook** + Slack / Telegram）、嵌入（内联 / 弹窗 / 浮动按钮，**签名 + 域名锁定**）、可选 **Stripe Connect** 付费预约（默认关、自托管者免手续费）、登录与后台（邮箱密码 / Google / GitHub / **OAuth·OIDC**）、隐私（自述**无第三方追踪器与像素**、凭据静态加密、限流、CSRF、签名令牌）、**9 种界面语言**。

**个人判断（未验证）**：它把"**自托管 = 完整版**"当作与 Cal.com 竞争的差异化点，工程栈（Elixir/OTP + LiveView + Oban）也偏"少运维、长稳"。但**它是 AGPL-3.0 且带商标条款**，商业再分发与白标要看清边界。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Tymeslot/tymeslot](https://github.com/tymeslot/tymeslot) |
| 描述 | "Open-source meeting scheduling platform built with Elixir/Phoenix LiveView. Self-host or use the managed cloud at tymeslot.app." |
| 归属 | 组织 `Tymeslot`；商标与云服务归 **Diletta Luna OÜ**（README 称 Made in Tallinn, Estonia） |
| 许可证 | **GNU AGPLv3**（`LICENSE` 首行 `GNU AFFERO GENERAL PUBLIC LICENSE / Version 3`，34,526 字节）；另有 **`TRADEMARK.md`** 商标条款 |
| 主语言 | shields 显示 **95.9%**（Elixir 生态为主） |
| 技术栈 / 构成 | **Elixir / OTP + Phoenix LiveView + PostgreSQL 14+ + Oban**；前端为 LiveView 页面；部署形态为**单容器（内含 Postgres）**，另支持 Cloudron / Railway / 自建 |
| 关注度 | **318 Star / 61 Fork**（ungh.cc，2026-11-16；shields 显示 **318 Star / 62 Fork**、**2 开放 Issue**、**贡献者 12**） |
| 维护状态 | **2025-08-07 创建**；`pushedAt` **2026-11-13**；页面 `updatedAt` 2026-11-14；**开发活跃** |
| 默认分支 | `main` |
| 版本 | **30 Release**（ungh releases 单页返回 30 条；README 徽章指向 `/releases` 最新版） |
| 体积 | `main` 文件树共 **7,560 项**（ungh files API）——单页应用但含主题、图标与多语言资源，仓库偏大 |

## 云版 vs 自托管（README 对比表）

| | 🐳 自托管 | ☁️ Tymeslot Cloud |
| --- | --- | --- |
| 价格 | **Free, forever** | Free 计划 · Pro **€9/月** |
| 部署 | 一条 Docker 命令 | 注册即用 |
| 托管与数据 | 你的服务器、你的数据库 | **托管在欧盟** |
| 更新与备份 | 你自己做 | 厂商代做 |
| 支持 | 社区（issues） | 优先支持 |

## 使用前需要注意

- **⚠️ "同码无门控"是厂商自述（事实 + 未验证）**：README 多处强调自托管与云版"同一份代码、无功能门控""自托管不是试用版"，但**这是厂商口径**；本笔记**未逐项比对云版与自托管版的功能差异**。选型前建议自行核对发布说明与文档。
- **⚠️ AGPL-3.0 的传染性（事实）**：AGPL 要求**对通过网络提供服务的修改版也须开放源码**。若你打算**改造后对外提供预约服务**，需评估是否触发 AGPL 义务。
- **⚠️ 商标与白标边界（事实）**：README 明写 "The Tymeslot name and logo are trademarks of Diletta Luna OÜ; forks are welcome under their own name（`TRADEMARK.md`）"——**可 fork，但不能带原品牌名与 logo 对外发布**。
- **⚠️ 自托管要自己扛运维与合规（事实）**：单容器省事，但仍需自备域名 / TLS / SMTP / 备份 / 升级；**预约页会收集访客信息（姓名、邮箱、备注、时区等）**，数据合规责任在部署方。
- **⚠️ 外部集成需要自备凭据（事实）**：日历双向同步、视频会议自动建房、Stripe 收费等都要**你自己申请并配置各平台的应用 / API 凭据**，涉及各平台条款。
- **⚠️ 我未做任何验证**：**未部署、未起容器、未注册云版、未连接任何日历或会议平台、未发起任何预约**。功能清单与部署方式均**来自 README 与仓库文件本身**。

## 什么时候会再看

- 想给自己 / 小团队**搭一个不依赖 Calendly / Cal.com 的预约页**、且**完全掌控数据**时。
- 想对照"**开源替代商业 SaaS**"的许可与商业模式写法（AGPL-3.0 + 商标条款 + 自托管免费 / 云版订阅）时。
- 想参考**"一条 Docker 命令即可跑起来"的自托管产品分发形态**（内置数据库、首个账号即管理员、Cloudron / Railway 入口）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"开源替代 + 自托管"的取舍样本**：[gitea：自托管一体化 Git 服务平台](gitea-自托管一体化Git服务平台.md)、[docuseal：开源自托管电子签名与 PDF 表单平台](docuseal-开源自托管电子签名与PDF表单平台.md)——都是"用自托管换数据主权"的成熟样本，可对照三者的许可策略（Gitea 相对宽松 / DocuSign 替代为 AGPL + 附加署名 / 本篇 AGPL + 商标条款）与运维成本。
- **同为"自托管服务"的另一个样本**：[mistgate：自建小规模 VPN 车队面板](mistgate-自建小规模VPN车队面板.md)——那里是 Go 单二进制 + SQLite 的自托管面板，与本篇的"单容器内含 Postgres"可对照**自托管产品的打包与分发形态**。

## 参考来源

- 项目仓库与 README：https://github.com/tymeslot/tymeslot
- 自托管文档（README 指向）：`README-Docker.md`；官网与云版：https://tymeslot.app
- 元数据（Star / Fork / 开放 Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 主语言）：ungh.cc 镜像与 shields.io 徽章，2026-11-16

以上信息核验于 2026-11-16，Star 数、Release 与功能随时变化。**未部署、未注册云版、未连任何日历或会议平台、未发起预约**；功能与许可证口径均来自 README、`LICENSE` 与仓库文件本身。**"自托管无功能门控"为厂商自述，未逐项验证。**
