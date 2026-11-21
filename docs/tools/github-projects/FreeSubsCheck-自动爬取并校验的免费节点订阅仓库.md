# FreeSubsCheck：靠 GitHub Actions 定时爬取并校验的免费节点订阅仓库

> 这个仓库**本体几乎没有代码**：它只是一份 GitHub Actions 工作流（每 2 小时跑一次）+ 由该工作流自动重写的 5 个订阅文件与 README。它把"从外部节点池拉取免费代理订阅、校验后打包成 Clash / Mihomo / Base64 / TXT 供人订阅"这件事做成了一条无人值守流水线。**它本身不产生节点，节点来自第三方公开池**——所以这篇只记录它的**机制与结构**，并着重标注**来源与合规风险**，不做任何可用性或安全性背书。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`.github/workflows/update-subs.yml` 工作流全文、`all.yaml` 头部样例、仓库文件树全部 7 项、GitHub 页面内嵌元数据、shields.io 徽章；**未订阅其链接、未连接任何节点、未运行其工作流、未验证任何节点可用性**）
- 适用范围：**仅供了解其自动化机制与文件结构**；产出订阅面向 Clash / Mihomo（Clash Meta）/ Base64 / 通用 TXT 客户端。**使用任何节点的合规与安全后果由使用者自负（见"使用前需要注意"）。**

## 核心结论

**一句话定位（README 自述）**：仓库自述为"**仅为个人学习 GitHub Actions 自动化流程及 YAML 数据处理的技术演示**"，分享的订阅/节点"均搜集自互联网公开渠道"。GitHub 描述为"免费节点、免费订阅……利用 subs-check 自动从节点池爬取分享，支持 v2ray clash 等各种代理软件，部分需自行转换"。

**三个最值得记的点：**

1. **仓库是"产物"，不是"工程"**：整个仓库只有 **7 个文件**——1 个工作流 + 1 个 README + 5 个订阅数据文件。所有内容（README 与 5 个订阅文件）都由工作流在每次运行时**重新生成并自动提交**。想看"GitHub Actions 定时抓取 + 校验 + 写回 + 自动提交"的完整最小范例，这是很干净的一个样本。
2. **机制可读**：工作流 `on.schedule.cron` 为 **`0 */2 * * *`（每 2 小时）**，另支持手动触发；用 `actions/checkout` 检出、临时下载 `yq`；把 5 个源地址放在 **Secrets**（`URL_ALL` / `URL_MIHOMO` / `URL_BASE64` / `URL_XIAOXI` / `URL_KOOKER`）里、映射成环境变量以免硬编码；用 `curl -sL -f -m 30` 拉取，**校验大小 > 100 字节**、YAML 还用 `yq eval` 验证可解析，通过才覆盖目标文件；随后按 `git diff` 判断是否有变化，**重新生成 README**（含节点计数与"是否更新"状态），并以 `github-actions[bot]` 身份**分文件提交、推送**。数据源标注为 **blog.kooker.jp**，引擎标注为 **beck-8/subs-check**。
3. **节点来自第三方公开池，数量随抓取浮动**：README 头部（由工作流填值）本次记为 **SubsCheck 22 / XiaoXi 2 / kooker.jp 769**，另有 `all.yaml`、`mihomo.yaml`、`base64.txt`、`byxiaoxi.txt`、`kooker.jp.txt` 五个产出。**这些数字每次运行都会变**，不要当固定值引用。

**个人判断（未验证）**：从工程角度看，它的**工作流写法（Secrets 注入、下载校验、按 diff 提交）值得参考**；但**作为"免费节点来源"，它的可靠性、安全性与合规性都不可托付**——节点来路不明、仓库无许可证、节点明文含地址与凭据，使用者承担全部风险。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [kooker/FreeSubsCheck](https://github.com/kooker/FreeSubsCheck) |
| 描述 | "免费节点、免费订阅 FreeSubs FreeNodes 利用 subs-check 自动从节点池爬取分享，支持 v2ray clash 等各种代理软件，部分需自行转换。" |
| 归属 | 账号 `kooker`（**个人**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false` |
| 许可证 | **无许可证**（页面元数据无 `license` 字段；仓库文件树 **无 `LICENSE` 文件**——7 个文件中不含许可证。即"未声明授权"） |
| 主语言 | **未取到**（页面 `languages: false`，未给出语言统计；仓库主体为 YAML / TXT 数据文件，非代码） |
| 技术栈 / 构成 | **GitHub Actions 工作流**（`.github/workflows/update-subs.yml`，shell + `curl` + `yq`）+ 5 个订阅数据文件：`all.yaml`（Clash Standard）、`mihomo.yaml`（Mihomo / Clash Meta）、`base64.txt`（通用 Base64）、`byxiaoxi.txt`、`kooker.jp.txt`（通用 TXT） |
| 关注度 | **456 Star / 59 Fork / 1 Watcher / 0 开放 Issue**（页面 Star/Fork/Watcher 与 shields Issue 数，2026-11-13；**页面值与 ungh.cc 镜像一致，无差异**） |
| 维护状态 | **2026-04-07 创建**；ungh 报 `pushedAt` **2026-11-13**（由机器人每 2 小时自动提交，故"最后推送"几乎总在当天）；页面 `updatedAt` **2026-11-12**；累计 **0 Release / 0 tag** |
| 默认分支 | `main` |
| 版本 | 无 Release、无 tag（它不发版本，只做数据更新） |
| 话题标签 | 无（页面 `topics: []`） |
| 体积 | `main` 文件树共 **7 个文件**（ungh.cc files API）：`.github/workflows/update-subs.yml`、`README.md`、`all.yaml`、`mihomo.yaml`、`base64.txt`、`byxiaoxi.txt`、`kooker.jp.txt`；`all.yaml` 约 **4.2 KB**（仅头部样例，全文未统计） |
| 数据来源 / 引擎 | 源：**blog.kooker.jp**（README 标注）；引擎：**[beck-8/subs-check](https://github.com/beck-8/subs-check)** + GitHub Actions |

## 工作流做什么（逐条对照 `update-subs.yml`）

| 触发 | **定时** `cron: '0 */2 * * *'`（每 2 小时）+ **手动** `workflow_dispatch` |
| --- | --- |
| 权限 | `contents: write`（要能提交回仓库） |
| 取源 | 从 **Secrets** 读 5 个源地址（`URL_ALL` / `URL_MIHOMO` / `URL_BASE64` / `URL_XIAOXI` / `URL_KOOKER`），映射为环境变量 |
| 下载与校验 | `curl -sL -f -m 30 -o tmp`；要求文件 **> 100 字节**；YAML 类型再用 `yq eval '.'` 验证可解析，**通过才替换目标文件**（失败则保留旧文件） |
| 生成 README | 用 `git diff --exit-code` 判断 5 个文件是否有变化，决定"✅ 已更新 / ⏸️ 与上次一致"；用 `yq '.proxies \| length'` 数 Mihomo 节点、`grep -c` 数 TXT 行数；写入北京时间戳，重写整个 README |
| 提交 | 配置 `github-actions[bot]` 身份；**分三次 add/commit**（XiaoXi、Kooker、SubsCheck+README），最后 `git push origin HEAD:main` |

## 产物长什么样（`all.yaml` 头部样例）

`all.yaml` 是 Clash 格式的 `proxies:` 列表，每项含 `name`（带国旗 emoji 与速度标签，如 `🇳🇱NL_1|1.4MB/s`）、`server`（**明文 IP**）、`port`、`type`、`tls`、`skip-cert-verify`、`udp`，部分项还有 `username` / `password` / `cipher`。**注意：这是节点地址与凭据明文，不是可安全公开的数据**。

## 使用前需要注意

- **⚠️ 来源与合规风险（最重要）**：仓库自述节点"搜集自互联网公开渠道"，**项目方不存储、不分发、也不保证任何节点**。使用免费代理节点在不同国家和地区可能**触犯当地法律法规或服务条款**；**是否使用、如何使用、后果如何，全部由使用者自负**。本笔记**只记录其机制与结构，绝不背书其内容或可用性**。
- **⚠️ 隐私与安全风险**：第三方免费节点**可能存在流量审计、日志记录、中间人劫持**。README 自己也提示"避免通过本项目提供的节点传输任何涉及个人隐私、财务安全或敏感信息的数据"。工作流使用的 `skip-cert-verify` 等配置进一步降低传输安全性——**不要用于任何敏感流量**。
- **⚠️ 无许可证**：仓库**没有 `LICENSE` 文件**，页面也无授权信息，即**未声明任何授权**。默认状态下他人对该仓库内容**没有明确的再使用许可**；转载 / 二次分发前应自行确认法律边界。
- **⚠️ 仓库里直接存节点凭据**：`all.yaml` / `base64.txt` / `*.txt` 中**明文包含服务器地址、端口，部分含用户名与密码**。仓库公开可读，等于这些凭据对所有访问者可见。
- **⚠️ 完全依赖外部来源与 Actions 可用性（事实）**：源地址藏在 Secrets 里（**无从审计其来源**）；节点会**随时失效、被封锁**；一旦外部源变更、Secrets 失效或 Actions 被限，**产物就会停更或保留旧数据**。README 顶部的节点计数与"最后同步时间"**每次运行都会变**。
- **⚠️ 我未做任何验证**：**未订阅任何链接、未连接任何节点、未运行该工作流、未验证任何节点是否可用**；上述机制、文件结构与计数均**来自 README、工作流 YAML 全文与仓库文件本身**。

## 什么时候会再看

- 想参考**"GitHub Actions 定时抓取外部源 + 校验 + 写回 + 自动提交"**的最小工作流写法（含 Secrets 注入、按 diff 决定提交、自动重写 README）。
- 研究**"数据即仓库"**这类纯产物仓库的组织方式（无代码、全自动生成）。
- 需要对照**免费订阅类项目的免责声明与风险提示写法**时（其 README 的免责声明结构可作为"来源与风险如何声明"的样本）。
- **不建议**把它当作可靠的节点来源或生产依赖。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"代理/分流"主题，但来源与用途不同**：[Shadowrocket-Rules：小火箭分流规则配置](Shadowrocket-Rules-小火箭分流规则配置.md)——那篇是**分流规则配置（节点需自备）**，本篇是**外部节点订阅产物**；对照可看"规则"与"节点来源"是两件事。
- **同为消费代理订阅的客户端侧工具**：[bowel：macOS 上原生 Go 渲染的 mihomo 客户端](bowel-macOS上原生Go渲染的mihomo客户端.md)——本篇产出的 `mihomo.yaml` 正是 mihomo 类客户端可读的格式；两篇合看是"订阅产物"与"读它的客户端"两端。**注意：二者只是格式上的上下游关系，本篇不对该订阅做任何背书。**
- **另一种客户端形态（格式不通用）**：[B-box：基于 sing-box 换 Flutter 新界面的安卓客户端](B-box-基于sing-box换Flutter新界面的安卓客户端.md)——那是 **Android 上的 sing-box 客户端**，读取的是 **sing-box JSON** 配置；本篇产出的订阅为 Clash / mihomo 格式，**两者并不通用**。同为"读订阅的客户端"，可对照不同内核的配置格式。

- **同为"代理订阅"主题、角度互补**：[Paolujichang：记录"跑路机场"的社区众包名单仓库](Paolujichang-记录跑路机场的社区众包名单仓库.md)——那篇是**对代理服务商（"机场"）的社区风险名单**（众包投稿、由单一维护者整理、**未经核实**），本篇是**外部节点订阅的自动抓取产物**；一个讲"免费节点从哪来"，一个讲"卖节点的可能怎么没的"。**两篇都不为其内容背书。**

## 参考来源

- 项目仓库与 README：https://github.com/kooker/FreeSubsCheck
- 工作流全文：https://github.com/kooker/FreeSubsCheck/blob/main/.github/workflows/update-subs.yml
- 引擎项目：https://github.com/beck-8/subs-check
- 数据来源站点（README 标注）：https://blog.kooker.jp/
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / topics）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数、节点计数与产出内容随时变化。**未订阅、未连接、未运行工作流、未验证任何节点**；机制、结构与计数均来自 README、工作流 YAML 全文与仓库文件本身。**本笔记不构成对任何节点可用性或安全性的背书，来源与合规风险由使用者自负。**
