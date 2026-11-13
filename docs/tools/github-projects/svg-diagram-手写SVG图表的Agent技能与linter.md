# svg-diagram：手写 SVG 图表的 Agent 技能与 linter

> 一句话定位（README 自述）：**"一套让你的 Agent 能照着画的手写 SVG 图表 house style"**——包含**布局算式、配色系统，以及一个零依赖、用来证明它确实照做了的 linter**。它最值得记的是三件事：① **它要的是"手放"而不是"自动布局"**——技能把**盒子高度、基线偏移、箭头间距、块间距**这些算式交给 Agent，让**每个元素都落在被选择的位置**，而不是交给布局引擎；② **`svg-lint` 是零依赖的纯 Node linter，12 项检查**——**`0 errors, 0 warnings` 才算通过，出现 warning 也是失败**；③ **它是 bybit-exchange 官方组织账号**维护的，且 **`SKILL.md` 是唯一真源**（仓库就一个技能，`SKILL.md` 在根目录）。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（196 行 / 11,436 字符）、`LICENSE`（MIT）、完整文件树与元数据；**未安装技能、未跑 `svg-lint`、未跑 `npm test`、未用任何 Agent 生成过图、未审计 `SKILL.md` 全文**）
- 适用范围：**需要 Node 才能跑 linter**（技能文本本身可用，但 `svg-lint` 是纯 Node 脚本）；安装走 `npx skills add`，覆盖 Claude Code / Codex / Cursor / Gemini CLI / Copilot / opencode / Antigravity / Pi / Windsurf / Continue / Roo / Goose / Kiro / **Trae** 等 40+ 客户端

## 核心结论

**一句话定位（README 自述）**：**手写 SVG 图表的 house style + 零依赖 linter**——把"怎么画"写进技能、把"画对没有"写进检查器。

**三个最值得记的点：**

1. **"Placed, not auto-laid-out"（手放，而非自动布局）**（README 自述）：
   - 技能把**算式**交给 Agent：**盒子高度、基线偏移、箭头间距、块间距**——**每个元素落在被选择的位置**，而不是布局引擎挑的位置。
   - **每个坐标都是字面量并带注释说明为什么取这个值**，所以**改一张图读起来像 diff，而不是重新渲染**。
   - **输出是纯 SVG**：无运行时、无查看器、无 JavaScript，能渲染进 README、文档站、PDF 和终端预览。
2. **`svg-lint`：零依赖、读成品、12 项检查**（README 自述）：
   - **检查对象是画完的 SVG 本身**（不是中间格式）——"收据和你要交付的文件之间没有别的东西"。
   - **12 项检查**覆盖：XML 转义 · viewBox 裁剪 · 字体栈 · 盒子高度 · 基线偏移 · 块间距 · 箭头标记 · 文本溢出 · 重叠 · 浅色背景兜底 · 调色板一致性 · 连接线几何。
   - **第 13 个 id `document-model` 自身不报错**——它是**模型层用来报告"它没读懂什么"**的通道，让几何检查**不会从读错的坐标里下结论**。
   - **`0 errors, 0 warnings` 是唯一通过标准；warning 即失败**。**失败信息会给出"发现值 → 应有的值"**（如 `viewBox: 11 → 20–25`），而不是笼统说"边距看起来不对"。
   - **它是维护者工具，靠手动跑**——"没有东西替你调用它，交付前自己跑一遍"。
3. **固定规则非常具体**（README 自述，完整规则在 `SKILL.md`）：
   | 领域 | 约束 |
   | --- | --- |
   | 布局 | viewBox 边距 **20–25px**；标题按**内容中心**居中（不是 viewBox 中心）；偏心内容用单个 `<g transform="translate(dx,0)">` 平移 |
   | 盒子 | 高度由字号推导：**单行 = `font-size × 3`**，**每多一行加 `font-size × 1.5`**；同一行盒子尺寸差 **≤ 60px** |
   | 连接线 | **只有两端共轴时才用直线 `L`**，任何转弯用 **`C` 或 `Q` 曲线**，**不要直角肘** |
   | 箭头 | 带缺口 marker、**`markerUnits="userSpaceOnUse"`**，按线宽定尺寸，**起点距源 5px、终点距目标 11px** |
   | 文本 | 基线在 `box y + height/2 + font-size × 0.35`；与直线连接线至少 **10px** 间距、与曲线 **15px**；标签在**向下弯的上方、向上弯的下方** |
   | 字体 | **16px 标题 / 12px 盒内文字 / 10px 注释**；字体栈**必须保留 `Noto Sans CJK SC`**（否则 Linux 渲染会变成豆腐块） |
   | 颜色 | **五组语义 fill/stroke/text 三元组**，用**呈现属性**而非 CSS 类、无媒体查询 |
   | 转义 | 文本与属性里的 `&`、`<`、`>`、`"`、`'` 转义；**来自数据库 / API 的内容在拼接进文件前先转义** |
   - **每张图都把白色 canvas rect 作为第一个元素画出**——**这不是主题切换**（图只带一套颜色），只是为了**在 GitHub 暗色主题下读起来像一张浅色卡片**。

**个人判断（未验证）**：它把"**让 LLM 画一张能看的 SVG 图**"这件常被吐槽的事，做成了**可验收**的工程：**规则写死在技能里、正确性交给 linter 判**。这套"**技能 + 检查器 + 示例库**"的三件套，比单纯堆提示词更可复制。**代价是它接受"手放"的取向**——要做自动布局的图（大型依赖图等）不在它的射程内。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [bybit-exchange/svg-diagram](https://github.com/bybit-exchange/svg-diagram) |
| 描述 | "A house style for hand-written SVG diagrams your agent can follow — the layout arithmetic, the colour system, and a zero-dependency linter that proves it did." |
| 归属 | **组织账号 `bybit-exchange`**（bybit 官方）；`isFork: false`、`isArchived: false` |
| 许可证 | **MIT**（`LICENSE` 1,071 字符） |
| 主语言 | **JavaScript 100.0%**（shields.io，2026-11-13）——即 `svg-lint`（纯 Node，无依赖） |
| 技术栈 | **Agent Skill（根目录 `SKILL.md`）+ Node linter（`tools/svg-lint/`）**；**零依赖**（无 npm 包、无 lockfile）；测试用 `npm test` |
| 关注度 | **671 Star / 44 Fork / 2 Watcher**（**页面值**，2026-11-13）；开放 Issue **1**、贡献者 **1**（shields.io，2026-11-13）。**ungh 镜像 669 / 2 / 44，略有差异** |
| 维护状态 | **2026-08-13 创建**；ungh 报 `pushedAt` **2026-09-03**、页面 `updatedAt` 2026-11-12——**代码推送约 2 个月前，近期更新多为元数据 / 徽章刷新**；页面报 **1 Release / 1 tag** |
| 默认分支 | **`main`** |
| 体积 | `main` 分支 **79 个条目**（ungh.cc files API）；以 **`tools`（63）** 为主，另有 `gallery`（10）、`.gitignore`、`assets`、`LICENSE`、`package.json`、`README.md`、**`SKILL.md`（根目录，唯一真源）** |

## 安装与使用（README 自述）

**安装（一句话交给 Agent 或自己跑）**：

```bash
npx skills add bybit-exchange/svg-diagram -g
```

> CLI 会检测你装了哪些 Agent 并写入各自路径；**去掉 `-g` 装到当前项目**。

| 客户端 | 路径 |
| --- | --- |
| Claude Code | `~/.claude/skills/svg-diagram/` |
| Codex、Cursor、Gemini CLI、Copilot、opencode、Antigravity | `~/.agents/skills/svg-diagram/`（**共享同一目录**） |
| Pi | `~/.pi/skills/svg-diagram/` |
| Windsurf、Continue、Roo、Goose、Kiro、**Trae** 等 40+ | `~/.<agent>/skills/svg-diagram/` |
| Claude.ai | 打包文件夹，在 Settings → Capabilities → Skills 上传（**仅技能文本；`svg-lint` 需要本地 Node**） |

**跑 linter**：

```bash
node tools/svg-lint/bin/svg-lint.mjs diagram.svg
node tools/svg-lint/bin/svg-lint.mjs diagram.svg --json
```

**只要技能文本**（放弃 linter）：

```bash
mkdir -p ~/.claude/skills/svg-diagram
curl -fsSL https://raw.githubusercontent.com/bybit-exchange/svg-diagram/main/SKILL.md \
  -o ~/.claude/skills/svg-diagram/SKILL.md
```

**示例库（Gallery）**：**10 张图，每张演示一条规则**——架构图（技能加载）、工作流（绘图回路）、时序图（lint 门位置，含 `alt` 修复分支）、数据流（指标 API → 文档图，**CJK 标签按宽度表逐字用一个字号**）、生命周期图（技能状态）、仓库结构图、安装扇出图、CJK 字体参考图、分层路由图、A/B 基准回路图。

**贡献与测试**：

```bash
npm run lint:svg:all   # 本仓库自带的 11 张 SVG
npm test               # 984 个测试
```

> `tools/svg-lint/test/fixtures/` 下的内容**被排除在 lint 之外**——`fail/` 下 14 个**失败是它们的职责**，`pass/` 下 2 个**由测试套件断言**。

## 使用前需要注意

- **许可证是 MIT，但技能是"一个技能一个仓库"**：仓库只维护 `SKILL.md` 这一个技能；`SKILL.md` 的 frontmatter `name` 决定**所有安装路径**（挪动仓库内文件不改路径）。
- **`svg-lint` 是维护者工具、手动跑**：**没有东西替你调用它**——"交付前自己跑一遍"。**不要以为装了技能就自动过检**。
- **`0 errors, 0 warnings` 是唯一通过标准**：**warning 也算失败**。用别的风格画的 SVG（哪怕是好图）在这个 linter 下**大概率不合规**——它是**house style 检查器**，不是通用 SVG 校验器。
- **要跑 linter 得有本地 Node**：Claude.ai 等**只上传技能文本**的场景**拿不到 `svg-lint`**（技能本身仍可用）。
- **它刻意不做自动布局**：定位是**手放**（架构 / 流程 / 时序 / 数据流 / 生命周期这类图）。**大型自动布局的依赖图不在射程内**。
- **代码推送约 2 个月前**（`pushedAt` 2026-09-03），**贡献者 1 人**——迭代节奏需自行评估。
- **我只读了 README、许可与文件树**：**未安装、未跑 `svg-lint`、未跑 `npm test`、未用任何 Agent 生成过图、未审计 `SKILL.md` 全文**；规则与检查项均来自 README，**未逐一验证**。

## 什么时候会再看

- 想给**文档 / README 配可秒读的架构图、流程图、时序图、数据流图**，且希望**输出是纯 SVG、可 diff、无运行时**时。
- 想参考"**把一套视觉规范写死成 Agent 技能 + 零依赖 linter + 示例库**"的三件套做法（**规则在技能、正确性交检查器、示例演示每条规则**）——这套模式可迁移到别的"让 Agent 产出规范化文件"的场景。
- 想借鉴它的**具体规则**：**盒子高度 / 基线偏移 / 箭头间距 / CJK 字体栈与字符宽度表 / 五组语义色 / 白色 canvas rect 兜底暗色主题**——写 SVG 或做图表规范时可当清单。
- 想对照其它"把图形 / 动画做成 Agent 技能"的项目：[srt-whiteboard-animation](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)（把 SRT 字幕做成白板手绘动画）· [agent-sprite-forge](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)（给 Codex 生成 2D 游戏精灵）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"把图形 / 可视化做成 Agent 技能"**：[srt-whiteboard-animation：把 SRT 字幕做成白板手绘动画的技能](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)——那篇把**字幕 → 白板手绘动画**；本篇把**需求 → 手写 SVG 图表**。两者都**让 Agent 产出规范化图形**，可对照它们的**规则固化和验收方式**。
- **同属"给 Codex / Claude Code 装能力"的技能与插件**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md) · [vibe-wise：边写边学的 Claude Code 与 Codex 插件](vibe-wise-边写边学的Claude-Code与Codex插件.md) · [jobflow-for-codex：面向 Codex 的本地求职工作流](jobflow-for-codex-面向Codex的本地求职工作流.md)——都是"**把一类流程 / 规则固化进 Agent 插件**"，**本篇的独特之处是配了一个可判定的 linter**，可对照"**技能 + 检查器**"与"**纯技能 / 纯提示词**"的差异。

## 参考来源

- 仓库主页与 README（196 行 / 11,436 字符；核验日期 2026-11-13）：https://github.com/bybit-exchange/svg-diagram
- 许可证：`LICENSE`（MIT，1,071 字符）
- 关键文件：`SKILL.md`（根目录，唯一真源）· `tools/svg-lint/bin/svg-lint.mjs` · `tools/svg-lint/README.md` · `gallery/`（10 张示例图）· `package.json`（`npm run lint:svg:all` / `npm test`）
- 安装机制：`npx skills add bybit-exchange/svg-diagram -g`（agent skills 生态，README 徽章指向 vercel-labs/skills）
- 完整文件树（79 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号类型经 ungh `orgs` 接口确认为组织

以上信息核验于 2026-11-13。**未安装、未跑 `svg-lint`、未跑 `npm test`、未用任何 Agent 生成过图、未审计 `SKILL.md` 全文**；规则、检查项与安装路径均来自 README，**未逐一验证**。
