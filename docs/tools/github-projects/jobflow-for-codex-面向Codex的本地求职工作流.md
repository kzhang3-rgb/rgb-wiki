# jobflow-for-codex：面向 Codex 的本地求职工作流

> 一句话定位（README 自述）：**"一套面向 Codex 的本地求职工作流：筛选岗位、跟进消息、维护台账、生成日报"**——把"找岗位、按规则筛选、打招呼、看消息、发简历、记录进度、生成日报"整理成**可复用的技能、模板与脚本**（当前版本 `0.2.0`）。它最值得记的是三件事：① **它明确不是自动投递外挂**——**不绕验证码、不绕登录、不绕风控**，并在"哪些地方需要你介入"里把**必须人工确认的环节**逐条列出；② **它的核心产物是一份本地台账**（`applications.jsonl`，用**12 个固定英文状态值**），脚本只**读写你指定的本地文件、不上传求职数据**；③ **它的隐私边界写得很细**——公开仓库只放通用代码 / 模板 / 虚构示例 / 测试，**真实简历、账号、cookie、session、招聘方消息与台账只能放你自己的私有 workspace**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（257 行 / 5,617 字符）、`LICENSE`（MIT）、完整文件树与元数据；**未 clone、未安装、未在 Codex 里运行、未登录任何招聘平台、未处理任何真实求职数据**）
- 适用范围：**Windows + 本地 Codex**（README 明说**只在 Windows + 本地 Codex 工作流下自测通过**，其它系统 / 浏览器控制方式 / 自动化模式**需自行适配与验证**）；Python **3.10+**

## 核心结论

**一句话定位（README 自述）**：**给 Codex 用的本地求职工作流**——把求职动作整理成**可复用的流程、模板和脚本**。

**三个最值得记的点：**

1. **它是"流程 + 台账 + 脚本"，不是投递外挂**（README 自述）：
   - 明确边界："**它不是自动投递外挂，也不会绕过验证码、登录、平台风控或安全限制**"。
   - **必须人工介入的环节**（README 单列）：首次使用前**登录招聘平台**；遇到**验证码 / 风控 / 安全确认**；招聘方问**微信 / 电话 / 薪资 / 到岗时间 / 面试时间**；需要**结合个人情况判断**的回复；启用**定时自动化前审核 prompt**；**页面结构变化导致状态无法确认**时复核。
   - 消息处理是**分类而非代答**：明确不合适 → `not_suitable`；明确要简历且配了简历路径 → 发简历并记 `resume_sent`；需判断 → `needs_user_action`；**无法确认 → `needs_review`**。
2. **它的核心产物是一份本地台账 `applications.jsonl`**（README 自述）：
   - **12 个标准英文状态值**：`contacted` · `resume_sent` · `interviewed` · `not_suitable` · `needs_user_action` · `needs_review` · `recruiter_replied` · `interview_scheduled` · `rejected` · `withdrawn` · `offer` · `no_response`。
   - 日报里可以显示中文标签，**但脚本统一用英文状态值**；v0.2 起**统一 `BOSS直聘`/`BOSS`、`猎聘`/`Liepin` 及中英文状态别名**。
   - **`job_id` 稳定化**减少重复记录；`upsert_ledger.py` 做**原子**追加 / 更新；`migrate_ledger.py` 生成新台账、**源文件不改**。
3. **隐私边界写得很细**（README 自述）：
   - **公开仓库只含通用代码、模板、虚构示例和测试**；**真实岗位 / 公司 / 招聘方消息 / 联系方式 / 简历路径 / 台账只能放私有 workspace**。
   - **迁移与更新命令只读写你以参数指定的本地文件，不上传求职数据**；**提交前应运行发布检查**确认 Git 工作区没有私有数据。
   - `tests/` **不访问招聘平台和真实数据**（本地回归测试）。

**个人判断（未验证）**：它把"**求职**"这个高度个人化、又高度重复的流程，做成了**Agent 技能 + 结构化台账**——**AI 处理重复劳动，判断权留给人**。这类项目真正的看点不是功能，而是它的**边界纪律**（不代答、不绕风控、不上传数据、公开仓库不含隐私），以及**平台适配的现实约束**（只支持 BOSS 直聘与猎聘，页面一变就要更新规则）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [laok775/jobflow-for-codex](https://github.com/laok775/jobflow-for-codex) |
| 描述 | "一套面向 Codex 的本地求职工作流：筛选岗位、跟进消息、维护台账、生成日报" |
| 归属 | 用户账号 `laok775`；`isFork: false`、`isArchived: false` |
| 许可证 | **MIT**（`LICENSE` 1,077 字符） |
| 主语言 | **Python 100.0%**（shields.io，2026-11-13） |
| 技术栈 | **Codex 插件（`.codex-plugin/plugin.json`）+ Skill（`skills/jobflow/SKILL.md`）+ Python 脚本**；无第三方重依赖（核心是脚本 + 模板 + 文档） |
| 关注度 | **317 Star / 68 Fork / 0 Watcher**（**页面值**，2026-11-13）；开放 Issue **0**、贡献者 **1**（shields.io，2026-11-13）。**ungh 镜像 313 / 0 / 68，略有差异** |
| 维护状态 | **2026-06-10 创建**；ungh 报 `pushedAt` **2026-08-09**、页面 `updatedAt` 2026-11-03——**代码推送已过去约 3 个月（更新多为徽章 / 元数据刷新），可视为阶段性放缓**；页面报 **0 Release**（**不发版，README 标注版本 `0.2.0`**） |
| 默认分支 | **`main`** |
| 版本 | README 标注 **`0.2.0`**（v0.2 增量：空台账初始化、别名规范化、稳定 `job_id`、台账迁移、原子更新、严格校验、CI 多版本测试） |
| 体积 | `main` 分支 **29 个条目**（ungh.cc files API）；以 `skills`（19）为主，另有 `tests`（2）、`.codex-plugin`（1）、`.github`（1）、`docs`（1）、`LICENSE` / `README.md` / `README.en.md` / `RELEASE_CHECKLIST.md` |
| 支持平台 | **BOSS 直聘**、**猎聘**（README 明说，其它平台可按新增 reference 与流程规则扩展） |

## 能力结构（README 自述）

**你可以用它做什么**：初始化本地求职工作区 · 维护 `applications.jsonl` 台账 · 用 `screening_rules.md` 记录筛选规则 · 让 Codex 按规则辅助筛选岗位 · 让 Codex 检查招聘方消息并分类处理 · 生成每日日报 · 生成**可审核的定时自动化 prompt**。

**主要脚本**：

| 脚本 | 用途 |
| --- | --- |
| `init_jobflow.py` | 初始化私有 workspace（创建 `user_profile.yaml` / `screening_rules.md` / `applications.jsonl` / `applications.example.jsonl` / `automation_prompt.md` / `reports/`） |
| `validate_ledger.py` | 校验台账；**区分错误与警告**，支持 **`--strict`**（查重复 ID、重复 URL、未知别名、推荐字段缺失） |
| `summarize_day.py` | 生成某天日报 |
| `check_targets.py` | 检查当天投递目标是否达成（如 `--boss 5 --liepin 5`） |
| `build_automation_prompt.py` | 生成定时自动化 prompt（**需人工审核后再启用**） |
| `migrate_ledger.py` | 台账迁移到新文件（**源文件不被覆盖**） |
| `upsert_ledger.py` | 通过 `job_id` **原子**追加或更新一条本地记录 |

**项目结构**：

```text
.codex-plugin/
  plugin.json
skills/
  jobflow/
    SKILL.md          # 告诉 Codex 如何执行求职流
    references/       # BOSS 直聘、猎聘、消息处理、筛选规则说明
    scripts/          # 初始化、校验、日报、目标检查、自动化 prompt
    templates/        # 用户配置、筛选规则、台账、日报、自动化 prompt 模板
      schema-v0.2.md  # 台账字段、别名与迁移说明
tests/                # 不访问招聘平台和真实数据的本地回归测试
```

## 使用前需要注意

- **平台合规风险**：项目本身声明"**不保证平台一定允许自动化操作**"、"**不能绕过验证码或平台风控**"；**浏览器自动化操作招聘平台可能违反平台服务条款、并影响账号安全**——**是否使用、如何使用，风险自担**（README 有 7 条免责声明）。
- **不要误解为"无人值守投递"**：README 反复强调**关键判断由人确认**（登录、验证码、风控、复杂消息、简历发送、自动化 prompt 审核）。**把"辅助"当成"代劳"会踩坑**。
- **只支持 BOSS 直聘与猎聘**，且**页面结构一变就可能要更新平台操作规则**；README 明说**只在 Windows + 本地 Codex 下自测通过**，其它环境需自行适配。
- **隐私边界必须遵守**：**真实简历 / 账号 / cookie / session / 招聘方消息 / 台账不得进入公开仓库**；**提交前运行发布检查**，确认 Git 工作区没有私有数据。
- **代码推送已放缓**：`pushedAt` 停在 **2026-08-09**（约 3 个月前），页面 `updatedAt` 较新多为徽章 / 元数据刷新；**平台规则若变化，跟进速度需自行评估**。
- **贡献者仅 1 人、0 Release**：项目很小（29 个文件），**没有版本锚点**，使用即跟 `main`。
- **我只读了 README、许可与文件树**：**未 clone、未安装、未在 Codex 里运行、未登录任何招聘平台、未处理任何真实求职数据、未跑任何脚本**；功能与流程均来自 README，**未逐一验证**。

## 什么时候会再看

- 想用 **Codex 辅助处理 BOSS 直聘 / 猎聘的求职流程**（筛选、跟进、台账、日报），并想要一份**可复用、可审计**的落地做法时。
- 想参考"**把个人重复流程做成 Agent 技能 + 结构化台账**"的工程做法（固定状态值、稳定 ID、原子更新、严格校验、迁移命令）时。
- 想参考"**Agent 做重复劳动、关键判断留给人**"的**边界设计与免责声明写法**（哪些必须人工介入、哪些只做分类不代答）时。
- 想对照其它 Codex / Claude Code 技能与插件：[vibe-wise](vibe-wise-边写边学的Claude-Code与Codex插件.md)（边写边学的插件）· [svg-diagram](svg-diagram-手写SVG图表的Agent技能与linter.md)（手写 SVG 技能 + linter）· [agent-sprite-forge](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)（给 Codex 的精灵生成技能）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"给 Codex / Claude Code 装能力"的插件与技能**：[vibe-wise：边写边学的 Claude Code 与 Codex 插件](vibe-wise-边写边学的Claude-Code与Codex插件.md)（在设计与实现之间加检查点）· [svg-diagram：手写 SVG 图表的 Agent 技能与 linter](svg-diagram-手写SVG图表的Agent技能与linter.md)（house style + 零依赖 linter）· [agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)。几篇都是"**把领域流程 / 规则写进 Agent 技能**"，可对照它们的**技能组织与边界写法**。
- **同属"用 Agent 处理真实世界的事务"**：[universal-modder：把改任何 PC 游戏做成一整套 Agent 技能](universal-modder-把改任何PC游戏做成一整套Agent技能.md)——那篇让 Agent **改你拥有的游戏**；本篇让 Agent **辅助求职流程**。两者都**把敏感边界（不越权、先问、不绕风控）写进规则**，可对照。

## 参考来源

- 仓库主页与 README（257 行 / 5,617 字符；核验日期 2026-11-13）：https://github.com/laok775/jobflow-for-codex
- 许可证：`LICENSE`（MIT，1,077 字符）· 发布检查：`RELEASE_CHECKLIST.md`
- 英文 README：`README.en.md` · 文档目录：`docs/`
- 完整文件树（29 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）

以上信息核验于 2026-11-13。**未 clone、未安装、未在 Codex 里运行、未登录任何招聘平台、未处理任何真实求职数据**；功能、流程、状态值与隐私边界均来自 README，**未逐一验证**；**本项目涉及浏览器自动化操作招聘平台，使用前请自行评估平台服务条款与账号安全风险**。
