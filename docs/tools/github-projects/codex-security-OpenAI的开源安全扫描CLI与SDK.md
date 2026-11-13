# codex-security：OpenAI 开源的安全扫描 CLI 与 TypeScript SDK

> 它把"让 AI 在代码里找漏洞、验证漏洞、给出补丁"做成了一条能脚本化的流水线：可当 `cs` 命令行、TypeScript SDK、GitHub Action 或容器批量任务跑，也能在 Codex 会话里当技能用。适合想给 CI 加一道"语义级扫描"、或想参考"AI 安全扫描怎么落地成 CLI / SDK / Action 三件套"的人。**但它不是纯本地工具——要登录、要联网、要花额度**；这条边界决定了它和你自己写的 semgrep 规则是两种定位。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`LICENSE`、仓库文件树与 GitHub 页面内嵌元数据；**未 clone、未安装、未 `cs login`、未运行任何扫描、未验证其漏洞检出准确度**）
- 适用范围：需要 **Node.js 22.13.0+（限 22.x）或 Node.js 24.x / 26.x，以及 Python 3.10+**（Python 3.10 另需 `tomli`）；扫描需登录账号或提供 `OPENAI_API_KEY` / `CODEX_API_KEY`，并消耗模型额度。仓库以 **TypeScript** 实现，发布 npm 包 `@openai/codex-security`。

## 核心结论

**一句话定位（README 自述）**：帮助你在代码里**发现、调查、修复漏洞**；可在 Codex 里跑安全工作流，也可用 `@openai/codex-security` 的 **CLI + TypeScript SDK** 自动化。

**三个最值得记的点：**

1. **它是"三件套 + 一个容器路径"，不是单个脚本**：终端 `cs`（含 `cs scan` / `cs policy` / `cs export` 等）、TypeScript SDK（`import { CodexSecurity }`）、开箱的 **GitHub Action**（每周扫仓库的示例工作流）、以及 **Docker Compose 批量扫描**。**同一套能力分四种入口**，这是它区别于"一段扫描脚本"的地方。
2. **取数口径与"本地工具"相反——它要账号、要联网、要额度**：扫描前需 `cs login`（或 CI 里给 API Key），示例工作流明写"每次扫描约 **$5** 上限"，请求进行中可能超限。部分涉密/受保护请求还需 `Trusted Access for Cyber`（Daybreak）才放行。**这意味着代码会被送到模型侧处理**，不是"代码不出本机"。
3. **它显式承认了一次能力下线**：README 明说**本地的 `serve` 命令与浏览器仪表盘已被移除**，findings 改为"本地存储 + 去重"，并可通过 `publish scan --to custom` 发到自建端点或 Codex Security Cloud。**旧数据库与扫描产物仍可用**——这类"删除公开能力但保留数据"的迁移信号，值得在评估时留意。

**个人判断（未验证）**：它把"AI 安全扫描"从"提示词"升级成"有退出码、有 SARIF/JSON/CSV 导出、能在 CI 里限费"的工程件，方向清楚；**但它对 OpenAI 账号与模型服务的绑定很深**，且迭代极快（核验日已 **55 Release / 59 tags**），长期可用性取决于账号策略与接口稳定性，而非代码本身。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [openai/codex-security](https://github.com/openai/codex-security) |
| 描述 | "OpenAI's Codex Security CLI and TypeScript SDK for finding, validating, and fixing security vulnerabilities. npm: https://www.npmjs.com/package/@openai/codex-security" |
| 归属 | 账号 `openai`（**组织**，页面 `isOrg: true`）；`isFork: false`、`isArchived: false`、`isPrivate: false` |
| 许可证 | **Apache-2.0**（页面 `spdxId: Apache-2.0`；**直读 `LICENSE` 文件**首行为 "Apache License Version 2.0, January 2004"，长度 10,926 字节，为标准 Apache-2.0 全文） |
| 主语言 | **TypeScript 76.2%**（shields.io，2026-11-13） |
| 技术栈 | **CLI + TypeScript SDK（`sdk/`）+ Codex 插件与技能（`plugins/`）+ GitHub Action（`github-action/`）+ Docker / Wolfi 容器（`docker/`）+ 评估集（`evals/`）**；另有 `docs/scanner-imports/`（把第三方扫描器结果导入的一众适配说明） |
| 关注度 | **12,454 Star / 978 Fork / 45 Watcher**（**页面值**，2026-11-13）；开放 Issue **36**（shields.io，2026-11-13）。**页面值与 ungh.cc 镜像不一致：镜像报 12,141 Star / 963 Fork / 43 Watcher，此处记录页面值** |
| 维护状态 | **2026-07-13 创建**；页面 `updatedAt` **2026-11-12**、ungh 报 `pushedAt` **2026-11-12**（距核验日约 1 天，**活跃**）；已累计 **55 Release / 59 tags**（页面值） |
| 默认分支 | `main` |
| 版本 | **55 Release / 59 tags**；发布 npm 包（页面 `packages: true`）并标记 `usedBy: true`（有下游依赖方） |
| 话题标签 | `ai-security` · `application-security` · `cli` · `code-scanning` · `codex` · `codex-security` · `cybersecurity` · `devsecops` · `nodejs` · `npm` · `openai` · `sdk` · `security` · `typescript` · `vulnerability-scanning` |
| 体积 | `main` 文件树约 **1,580 个条目**（ungh.rs files API）；顶层以 `sdk`（678）、`plugins`（545）、`evals`（108）、`.github`（60）、`github-action`（50）、`docker`（46）、`examples`（38）、`docs`（29）为主 |
| 官网 | <https://developers.openai.com/codex/security> ・ 在线文档：<https://learn.chatgpt.com/docs/security/cli> |

## 它解决什么问题（README 自述）

扫描仓库、指定路径或 Git 变更；**深度（deep）扫描会重复标准扫描并去重**后返回结果。它能：验证候选漏洞、生成补丁、核验已有修复；起草 `SECURITY.md` 策略并保存威胁模型；浏览已保存的扫描与发现、识别重复、按你的评分标准判定严重级别、从源码与 Git 历史推测责任人；预览/保存第三方扫描器结果，或把受支持的报告发布到 Codex Security Cloud；对接 Linear / Jira / GitHub 跟踪发现，导出 SARIF、JSON、CSV。还可跨仓库或组件批量自动化，包括在 CI 与容器里跑。

## 三种入口与主要命令（README 自述）

| 入口 | 关键用法 |
| --- | --- |
| CLI | `npm install --global @openai/codex-security` → `cs login` → `cs scan .`；`cs` 与 `codex-security` 是同一 CLI（冲突时用全名）；也可用 `npx @openai/codex-security` 免全局安装 |
| 扫描范围 / 模式 | `cs scan . --path src --path tests`（指定路径）；`cs scan . --diff origin/main`（按提交差异）；`cs scan . --mode deep`（深度）；`cs scan . --max-time 15`（约 15 分钟标准调查时限） |
| 在 Codex 里 | 在启用 Codex Security 插件的 Codex 会话里直接下达扫描任务（插件/技能方式），README 给了示例提示词与 agent 工作流指引 |
| TypeScript SDK | `import { CodexSecurity } from "@openai/codex-security"`，`await security.run("/path/to/repository")` 取 `reportPath`；支持深度扫描配置、严重级别分类、责任人建议等 |
| 策略与导出 | `cs policy .`（起草/更新 `SECURITY.md`，草稿存到仓库外、不改动现有文件）；`cs export --scan SCAN_ID --artifact threat-model --output threatmodel.md` |
| CI / 容器 | 设置 `OPENAI_API_KEY` 或 `CODEX_API_KEY`；GitHub Action 示例工作流（每周 + 手动触发，示例约 $5/次上限）；Docker Compose 批量扫描并可对接独立 findings 服务 |

> 状态目录：链接的 Git worktree 在共享同一 Codex Security 状态目录时共享扫描历史与已保存发现，README 建议保持 `CODEX_SECURITY_STATE_DIR` 稳定。

## 其他模型提供方（README 自述）

扫描支持 **OpenAI、Amazon Bedrock、OpenRouter、Fireworks AI、Cheaper Inference**。其中 **Bedrock 用 AWS 凭据、不需要单独的 OpenAI 登录**；其余为设置对应 API Key 并选一个受支持模型。

## 使用前需要注意

- **⚠️ 它不是离线工具（事实）**：必须登录（`cs login`）或提供 `OPENAI_API_KEY` / `CODEX_API_KEY`，扫描要联网、要走模型，**代码会被送到模型侧处理**。**这与"纯本地、代码不出本机"的工具是两种隐私模型**，请按此评估是否可对敏感仓库使用。
- **⚠️ 会花钱，且可能在请求进行中超出上限（README 自述）**：示例工作流设"每次约 $5 上限"，但明说**进行中的请求可能超过该上限**。纳入 CI 前要评估额度与失败回退。
- **⚠️ 部分请求需要额外权限**：README 提到某些网络安全请求与受保护发现需要 **Trusted Access for Cyber**；有 Daybreak 访问权的账号还需显式 opt-in / 选择程序。**不是所有账号都能拿到全部能力**（我未核实各账号的开通条件）。
- **⚠️ 许可证是 Apache-2.0，但可用性绑定 OpenAI 服务**：Apache-2.0 只覆盖代码。**开源的 CLI/SDK 不等于"可自由离线运行"**——核心扫描能力依赖其后端模型服务与账号策略。
- **⚠️ 能力下线信号（事实）**：README 明写**本地 `serve` 命令与浏览器仪表盘已移除**。若你此前依赖本地面板，需要改用导出/Cloud/自建端点；旧数据库与扫描产物仍保留。
- **⚠️ 迭代极快（事实）**：**2026-07-13 创建**，核验日已 55 Release / 59 tags、`pushedAt` 约 1 天内——文档与行为可能快速变动。
- **二进制 / 构建产物**：文件树以源码、文档、配置为主；另有 `docker/wolfi/` 下的容器构建脚本、`wolfi-signing.rsa.pub` 等，**我未逐一确认是否含大体量二进制**。
- **README 自述与仓库实际**：描述称"CLI and TypeScript SDK"，仓库确有 `sdk/` 与 CLI 相关结构，**未发现明显不符**（仅按文件树判断）。
- **我未做任何运行验证**：**未 clone、未安装、未登录、未运行扫描**；上述能力、命令、配额与权限边界**均来自 README、`LICENSE` 与仓库文件自述**，**检出准确度未验证**。

## 什么时候会再看

- 想给 CI 加一道**"AI 语义级安全扫描 + SARIF/JSON/CSV 导出 + 限费"**的现成流水线（GitHub Action / Docker 批量两种形态）。
- 想研究**"把 AI 能力同时做成 CLI、TypeScript SDK、Action、容器任务"**的打包与限费设计。
- 需要**导入并统一第三方扫描器结果**（仓库 `docs/scanner-imports/` 覆盖 Snyk、Semgrep/OpenGrep、Sonar、Trivy、Checkmarx、Veracode、Wiz 等）。
- 评估**多模型提供方**方案（OpenAI / Bedrock / OpenRouter / Fireworks AI / Cheaper Inference）在合规上的取舍。
- 给仓库**起草 `SECURITY.md` 策略与威胁模型**并希望这步也能被 CLI 化。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"挂在编码助手 / Codex 上的 CLI"**：[oracle：给编码助手请第二意见的CLI](oracle-给编码助手请第二意见的CLI.md)——两篇都是"给你正在用的编码 Agent 外挂一个命令行能力"，一个是**请第二意见**、一个是**做安全扫描**，形态相近、用途互补。
- **同处"AI + 安全"交集**：[reverse-skill：逆向与渗透技能路由包](reverse-skill-逆向与渗透技能路由包.md)——一个偏**攻**（逆向 / 渗透技能路由）、一个偏**防**（漏洞扫描 / 修复），可对照看"AI 进入安全领域"的两种落点。

## 参考来源

- 项目仓库与 README：https://github.com/openai/codex-security
- 在线文档：https://learn.chatgpt.com/docs/security/cli ・ 官网：https://developers.openai.com/codex/security
- npm 包：https://www.npmjs.com/package/@openai/codex-security
- 许可（Apache-2.0）：https://github.com/openai/codex-security/blob/main/LICENSE
- Agent 工作流：https://github.com/openai/codex-security/blob/main/docs/agent-workflows.md
- 第三方扫描器导入：https://github.com/openai/codex-security/tree/main/docs/scanner-imports
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 与 Tag 数 / topics / 许可证）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未安装、未登录、未运行任何扫描、未验证检出准确度**；功能、命令、配额与权限边界均来自 README、`LICENSE` 与仓库文件自述。
