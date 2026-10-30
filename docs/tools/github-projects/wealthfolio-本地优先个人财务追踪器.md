# wealthfolio：本地优先的个人财务追踪器

> 记录它的功能边界，以及"开源免费"与"订阅功能"之间的分界——这是使用前最容易误解的地方。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、目录结构与 GitHub API 元数据，未安装运行）
- 适用范围：桌面版（Windows / macOS / Linux）、iOS App、Docker 自托管；源码构建需 Node.js + pnpm + Rust + Tauri

## 核心结论

一个本地优先的个人财务追踪器：投资组合、净资产、支出与模拟测算。数据存在本机，不需要注册账号，可用 CSV 导入手工记账。技术栈是 Rust + Tauri + TypeScript，跨桌面、iOS 和自托管 Web 三种形态。

它的核心卖点是**数据不出本机**，而这点在实现上是认真的：本地 SQLite、无云数据库、无账号体系。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [wealthfolio/wealthfolio](https://github.com/wealthfolio/wealthfolio)（组织账号） |
| 官网 | [wealthfolio.app](https://wealthfolio.app) |
| 许可证 | **AGPL-3.0** |
| 技术栈 | Rust、Tauri、TypeScript；pnpm 工作区；Playwright 端到端测试 |
| 关注度 | 9,288 Star、783 Fork、33 人关注、543 个开放 Issue（2026-10-30 的 API 数据） |
| 维护状态 | 2024-05-27 创建，**5,292 次提交**，40 个分支、70 个 Tag；最近推送就在核验当天（2026-10-30） |
| 当前版本 | Cargo.toml 显示 v3.11.0（2026-10-23）；README 平台要求章节提到 3.7 |
| 仓库配套 | 有 `ROADMAP.md`、`CONTRIBUTING.md`、`CLA.md`、`TRADEMARKS.md`、`AGENTS.md` 和 `docs/` 完整文档 |

维护活跃度是这批收藏里最高的：提交按 Conventional Commits 规范，记录里能看到同一小时内多次提交、以及常规的安全与依赖更新（如调整 TLS 根证书信任列表）。

## 主要功能

- **投资组合追踪**：多账户、多资产类型。
- **业绩分析**：时间加权与资金加权收益率、基准对比、历史分析。
- **交易活动管理**：导入和管理全部交易记录。
- **目标规划**：设定财务目标并管理配置比例。
- **多币种**：汇率管理。
- **插件系统**：TypeScript SDK、热重载、权限与用户授权机制、可注册自定义页面与导航项，可打包图片 / 字体 / 媒体 / Wasm 到隔离沙箱。官方与社区插件放在独立的 `wealthfolio-addons` 仓库。

## "开源免费"与"订阅功能"的分界

这一点最好先弄清楚，README 写得很明确，但容易只看标题就误解：

- **免费且不依赖服务端**：本地记账、CSV 导入、投资组合与净资产分析等核心功能，永久免费。
- **需要订阅的部分**：**Wealthfolio Connect** 是可选订阅，提供 30+ 家券商的只读自动同步，以及加密的多设备同步。仓库里没有这部分服务端实现。

所以"开源"覆盖的是应用本体，"自动同步券商数据"是靠订阅服务实现的。对手工记账的人来说，免费部分已经够用。

## 许可证对自托管场景的影响

AGPL-3.0 是**带有网络服务条款的强著佐权许可**：如果你修改了它的代码，并通过网络向他人提供服务，需要向使用者提供修改后的源码。

- 个人自己用、自己部署，没有义务。
- 若基于它改造后做成对外服务，或做二次分发，需要履行 AGPL 义务。
- 仓库另有 `TRADEMARKS.md`，商标使用单独受限。

## 使用前需要注意

- **本地存储意味着备份责任在你**。数据是本机 SQLite 文件，没有云端副本；桌面版默认的 profile 目录按平台存放在系统应用数据目录下（记录里能看到 macOS 的 `~/Library/Application Support/.../profiles/<uuid>/app.db` 结构）。数据丢失的风险由自己承担。
- **数据库加密是可选功能**，需要显式开启（仓库中有 "optional SQLCipher encryption" 相关提交），不是默认状态。
- **开放 Issue 数量较大（543）**：既说明项目需求旺盛、社区活跃，也说明存在不少已知问题与功能缺口。用前建议按自己关心的功能搜一下 Issue。
- **iOS App 走 App Store 发布**，不是从本仓库构建的产物。
- **版本迭代快**：几十个 Tag、频繁发版，配置或数据格式可能随版本变化，升级前建议先备份数据目录。
- 我未安装使用，未验证记账、收益率计算与导入功能的实际表现。

## 什么时候会再看

- 需要一套本地存储、不依赖云账号的记账与投资追踪工具时。
- 想参考 Tauri 应用的工程化做法时——这个仓库的组织度很高：pnpm workspace 分层、Playwright 多套配置（应用 / 设计系统 / 净资产 / profile 分别一套）、Clippy 规则明确禁止测试外的 `unwrap` 与 `panic`、按 Conventional Commits 提交。
- 想参考"开源本体 + 可选订阅服务"这种商业模式的边界划分方式时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 同属个人信息管理、强调本地存储的项目：[easy-writing](easy-writing-易创网文写作软件.md)。

## 参考来源

- 项目仓库与 README：https://github.com/wealthfolio/wealthfolio
- 官方网站：https://wealthfolio.app
- 路线图：https://github.com/wealthfolio/wealthfolio/blob/main/ROADMAP.md
- 插件文档：https://github.com/wealthfolio/wealthfolio/blob/main/docs/addons/index.md

以上信息核验于 2026-10-30，Star 数与仓库内容可能变化。未安装运行，功能与收益计算均未验证。
