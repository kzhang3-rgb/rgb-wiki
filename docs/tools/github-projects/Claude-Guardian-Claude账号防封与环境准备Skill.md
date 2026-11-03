# Claude-Guardian：给 Agent 用的 Claude 账号与环境准备 Skill

> 记录这个 Skill 到底把什么交给 Agent、它的分级授权与隐私设计，以及它显式带商业推广和"规避平台风控"属性这两件必须先看清的事。未安装 Skill、未运行任何脚本。

- 状态：草稿
- 最近核验：2026-11-03（阅读仓库 README、根目录文件列表与 GitHub API 元数据，未安装、未运行脚本、未审计代码）
- 适用范围：需要支持 Skills 的 Codex；脚本需 Python 3.10+，部分静态 YAML 功能需要可选 PyYAML

## 核心结论

这不是一个"工具"，而是一份**给 Agent 读的 Skill**：把作者与社区关于 Claude 订阅、账号、网络出口与运行环境的经验、资料和一批 Python 脚本打包，让 Agent 先了解你的系统与目标，再按本机 / VM / VPS 三条路线给出做法。

它的工程侧有两个值得借鉴的设计：**访谈状态机**（上百个问题按条件调度，选定路线后才问相关问题）和**三档分级授权**（只读诊断自行做、整段计划批准一次、高风险操作必须当场确认）。

**必须同时看两件事**：一是 README 里有明确的**商业推广位**（合作方 API 与"代搭建"服务）；二是整篇内容服务于**降低被平台封禁的概率**，包含固定出口 IP、注册节奏、以及清理代理与终端痕迹的做法——**收录只为记录它的存在与结构，不构成任何操作建议，也不构成对合规性的判断**。

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [DUNHKpcc/Claude-Guardian](https://github.com/DUNHKpcc/Claude-Guardian) |
| 归属 | 个人开发者 DUNHKpcc；README 提到社区与 Linux.do |
| 许可证 | **GPL-3.0-only**（根目录 `LICENSE`，35,149 字节，符合 GPL-3.0 全文体量） |
| 主语言 | Python |
| 关注度 | 1,677 Star、226 Fork、**0 订阅者**（`subscribers_count`；`watchers_count` 与 Star 数相同，属 GitHub 历史遗留，不宜当两个指标） |
| 活跃度 | 2026-10-15 创建，最近一次推送 **2026-11-02**，默认分支 `main`；`CHANGELOG.md` 单文件已达 **105,973 字节**，三周内迭代极为频繁 |
| 待办积压 | 2 个开放 Issue |
| 仓库体积 | 约 47,179 KB ≈ 46 MB |
| 其他元数据 | `has_wiki: true`（Wiki 由仓库自动生成，见 `tools/export_wiki.py`）；4 个 topics（`agent-skills`、`claude-code`、`claude-skills`、`codex-skill`）；`homepage` 指向合作方站点 |
| 根目录结构 | `.github/`、`AGENTS.md`、`CHANGELOG.md`、`CLAUDE.md`、`LICENSE`、`README.md` / `README.en.md`、`docs/`、`reports/`、`requirements-dev.txt`、`skills/`、`tests/`、`tools/` |

## 它把什么交给 Agent

README 的能力表主要覆盖这些方向（概括，非逐条搬运）：

- **选路线**：本机 / VM / VPS 三选一，说清各自范围与代价。
- **固定出口 IP**：机场、自建梯（可搭配家宽出口）、家宽拼车三类来源的优缺点；自建梯给 `3x-ui` + VPS 线路提示。
- **注册与使用节奏**：邮箱选择（Gmail / Outlook / Proton）、Free → Pro → Max 的间隔、额度与并发、手机端检查表。
- **订阅与支付**：从支付方式汇总里按地区/设备/预算挑候选，**付款由用户本人完成**。
- **三条路线的落地**：本机走 Clash 的 TUN 与 DNS 加系统防火墙；VM 走 `FlClash TUN` + `nftables` 的 fail-closed 脚本；VPS 走 Ubuntu 24.04 + `Xvfb + noVNC` + Remote-SSH + `tmux`。
- **整机统一出口**：只走 IPv4、堵住 IPv6、DNS 与规则兜底、泄漏与故障演练的取证清单。
- **线路优化与故障分流**：SSH 动态转发、Realm 中转、BBR（每项都记原值并可回滚）；`403` / `429` / `529`、账单冲突等先暂停重试、整理证据。
- **清理规划**：旧设备的 Claude 残留、以及封号换环境时的**代理客户端日志、VPN 日志和终端历史里查 IP 的命令**。
- **资料随答附带**：每个回答附来源链接，可当 VPS、家宽、线路、封号经验的论坛搜索工具用。

## 项目自己划的几条底线

这部分是判断它可信度时最该先读的：

- **钱和账号你来**：付款、注册、登录、KYC、OAuth 都由用户本人完成。
- **不要凭据**：不用交出 IP、订阅链接、密码、UUID 或完整配置。
- **准备阶段不碰 Claude**：不启动客户端、不登录、不访问其服务。
- **没人能保证免封**：价格和准入变化快，购买前看官方页面。
- 开篇还显式写了一句"**不要碰代过 KYC（定制除外）、成品号、代充、成品邮箱**"——即项目自身对最常见的灰色做法划线，但同时留了"定制除外"的口子。

## 分级授权：A / B / C 三档

| 档位 | 用户要做什么 | 包含什么 |
| --- | --- | --- |
| 🟢 A：范围内自行做 | 路线和目标确定后确认一次范围 | 只读诊断、查资料、出方案、预演 |
| 🟡 B：整个计划批准一次 | 批准一张计划，Agent 连续执行，失败自动撤销 | 装客户端、加允许规则、前台转发、临时切 BBR、装 TUN |
| 🔴 C：必须当场确认 | 到这一步 Agent 暂停，等用户回复 | 切默认出站为阻止、启用防火墙、改 SSH、持久化改内核、**清理数据**、账号与钱、凭据、接触 Claude |

批准与确认都以对话中的明确回复为准并记录在访谈状态里，换路线后要重新确认。**这套"按风险分档、高风险必须当场确认"的做法，即使不看具体内容，也值得作为给 Agent 写操作规范时的参考。**

## 隐私与安全设计（README 自述，我未审计代码）

这些声明如果能落实，是加分项：

- 不索取 IP、订阅链接、密码、UUID 或完整配置。
- `update_check.py check` 只读、**只连 GitHub**；更新前先备份，保留用户自己改过的文件。
- `egress_verifier.py` 按实际观察到的证据判定 `established / violated / incomplete`，**不回显 IP**。
- 输出文件（`--output`、`--evidence-output`、面板 HTML）**只写新文件，不覆盖已有文件**；`repair_cli.py` / `recovery_cli.py` 有独立的计划、回执与恢复流程。
- 项目不自行安装 Python、代理工具或后台服务；缺可选依赖时会停下并说明。

**重要限制**：以上全部来自 README 与文档自述，我**没有阅读脚本源码、没有安装、没有运行**，无法确认实现与声明一致。

## 使用前需要注意

- **README 带显式商业推广**：Partners 区块直接导流两个商业入口——一个第三方 API 服务（描述为"满血 Claude"）和一个付费"代搭建"服务，`homepage` 也指向该站点。**推荐口径因此不宜当作中立评价**；判断方案时请把这条与它给的技术建议分开看。
- **内容服务于规避平台风控**：固定出口 IP、注册与使用节奏、以及"封号后清理代理客户端日志、VPN 日志和终端历史里查 IP 的命令"，都属于可能与服务条款冲突的方向。README 自己也承认"没人能保证免封"。**本笔记只作信息记录，不代表建议，也不对合规性作判断；是否使用请自行评估服务条款与所在地区法规。**
- **"非法工具清理"一节反向说明其受众**：README 提到可清理装在 Claude 里的"破限、反代分发等非法工具"——说明这类东西在它的目标用户群里很常见。仓库约定：涉及可能触及法律或平台规则边界的工具，收录时显式关联授权提醒，见 [reverse-skill：逆向与渗透技能路由包](reverse-skill-逆向与渗透技能路由包.md) 中的授权说明。
- **GPL-3.0-only**：是强 copyleft，若二次分发修改版，需按 GPL 提供对应源码。
- **安装即写入全局 Skill 目录**：安装位置为 `~/.codex/skills/claude-guardian`，README 已提醒同名目标存在时会停止、需先备份再手动迁移。
- **我未安装、未运行**：所有功能、隐私与安全表现均来自文档自述，实际行为未验证。

## 什么时候会再看

- 要给 Agent 写"按风险分档授权 + 访谈状态机"的操作规范时（这是本篇最可复用的部分）。
- 需要参考"给 Coding Agent 的 Skill 仓库该怎么组织文档、脚本与测试"时。
- 研究此类"账号环境准备"类内容时，作为结构与话术的样本。

## 相关笔记

- **同为"Claude 账号与环境"主题**：[CCBaga：Claude 身份与浏览器环境管理工具](CCBaga-Claude身份与浏览器环境管理工具.md)——那是管理本地 Claude 身份并校验本机浏览器环境，本篇是给 Agent 用的环境准备与检查 Skill；两者都在处理"账号环境是否干净"，但一个偏工具、一个偏知识包。
- **同为"Claude 账号与环境"主题**：[claude-switcher：Fail-Closed 账号环境管理器](claude-switcher-Fail-Closed账号环境管理器.md)——那是给人用的桌面工具（启动前 14 项审计 + 进程级 Kill-Switch + 多账号快照隔离），本篇是给 Agent 用的检查清单与分级授权；可对照"工具强制"与"流程约束"两条路线。
- **同为"给 Coding Agent 的部署类技能"**：[vps-xray-reality：Xray 节点部署技能](vps-xray-reality-Xray节点部署技能.md)——那是把 VPS 部署流程固化成技能，本篇也涉及 VPS 与线路部分；可对照两者把操作固化成 Skill 的写法与安全边界标注方式。
- **合规与授权提醒**：[reverse-skill：逆向与渗透技能路由包](reverse-skill-逆向与渗透技能路由包.md)——涉及可能触及法律或平台规则边界的操作时，先确认授权与适用范围。
- **同一件事的"交易方"视角**：[awesome-chinese-secondhand-slang：中国二手交易暗语全书](awesome-chinese-secondhand-slang-中国二手交易暗语全书.md)——那份词典的 `ai-services` 分类记录的正是"AI 账号/代充服务在二手平台上的暗语与存活状态（🟢现行 / 🔴平台拦截）"，可从"账号买卖侧"对照本篇的"账号环境准备侧"。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/DUNHKpcc/Claude-Guardian
- Skill 入口：https://github.com/DUNHKpcc/Claude-Guardian/blob/main/skills/claude-guardian/SKILL.md
- 授权分档说明：https://github.com/DUNHKpcc/Claude-Guardian/blob/main/skills/claude-guardian/references/authorization-tiers.md
- 更新记录：https://github.com/DUNHKpcc/Claude-Guardian/blob/main/CHANGELOG.md
- Wiki（由仓库自动生成）：https://github.com/DUNHKpcc/Claude-Guardian/wiki
- 元数据（Star / Fork / 订阅数 / 体积 / 分支 / 许可证）：GitHub REST API，2026-11-03

以上信息核验于 2026-11-03。项目创建仅三周且迭代极快，Star 数、功能与推广口径都会迅速变化；未安装、未运行脚本，实际行为与安全表现均未验证。
