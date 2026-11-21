# geo-sleuth：根据一张照片定位拍摄地、并展示推理过程的 Agent 技能

> 记录一个**只给一张照片就能找出它在哪拍的、并展示推理过程**的 Agent 技能（`Oldcircle/geo-sleuth`，描述 "An agent skill that finds where a photo was taken — OpenStreetMap geometry, elevation skylines, satellite imagery and street view — and shows its work."）：它**不靠文字 / 车牌 / 地标**，而是用 **OpenStreetMap 几何 + 高程天际线 + 卫星影像 + 街景**自己找线索——"桥墩间距当尺子、影子当方位、山脊线当指纹"。三条先记住：**脚本负责排名、模型只负责在少数候选里判断**；**每个结论都必须指名"哪条命令、产出了哪个文件"**；**答案带误差半径**。未安装、未运行、未核实任何案例。

- 状态：草稿
- 最近核验：2026-11-17（阅读 `main` 分支 `README.md` 前 180 行（全文为 35,345 B 的英文版，另有 `.zh-CN` / `.ja` 版）、`LICENSE`、文件清单（147 项）与 ungh 镜像 / shields 徽章；**未安装技能、未运行任何脚本、未复现任何案例、未审计源码**）
- 适用范围：**标准 Agent Skill**（`SKILL.md` + 普通 Python 脚本）；README 称同一份文件夹可跑在 **Claude Code、Codex、Cursor、Gemini CLI、OpenCode、GitHub Copilot** 等"任何会读 `SKILL.md` 并执行 shell 命令的 Agent"

## 核心结论

**一句话定位（API 描述）**：`An agent skill that finds where a photo was taken — OpenStreetMap geometry, elevation skylines, satellite imagery and street view — and shows its work.` 中文可说"**用几何与高程，而不是靠猜，找出照片的拍摄地与朝向，并把推理过程摊开给你看**"。

**① 它挑战的正是"没有任何可读信息"的照片。** README 强调："**它在你没东西可读时也能工作**"——没有路牌、没有车牌、没有地标时，靠 **OpenStreetMap 几何、高程数据、卫星瓦片、街景**自己推进搜索。首页示例图配文："No text. No plates. No landmarks. One bridge, one mountain. Located to within 2 m."

**② 它的方法是"几何而非猜测"。** README 原句：**"桥墩间距变成距离尺，影子变成方位，山脊线变成高程数据能匹配的指纹。"** 每个案例都把照片"读"成可计算的几何约束，再拿 OSM / 高程去比对。

**③ 它把"可追溯"写成了硬规则。** README：**"Every claim points at a file."**——一个结论**必须指名本次会话里跑过的命令与它产出的文件**；**"人口与名气不算证据"**。

**④ 分工是"脚本排名、模型判断"。** README：**"Scripts rank, the model judges."**——**二十来个单用途脚本负责搜索、打分、排序，模型只在少数前几名里做判断**；脚本 / 模型职责分得很清。

**⑤ 答案带误差半径与置信度。** README：输出**坐标 ± 半径、相机朝向、一张证据图、一个分级置信度**。

**⑥ 用法极简。** `npx skills add Oldcircle/geo-sleuth` → 选择你的 Agent → 给 Agent 一张照片说 `find where this photo was taken`；首次使用让 Agent 跑安装目录 `scripts/` 下的 `doctor.py` 并通过检查项。**这就是全部接口**——Agent 读 `SKILL.md`、跑脚本、回给你相机位置 / 朝向 / 证据图。

**⑦ 它给了三个可核对的案例（README 原表）。**

| 案例 | 靠什么 | 落点精度 |
| --- | --- | --- |
| **稻田与高架桥**（广东清远） | 铁路桥 × 天际线 × 桥墩数 | **±2 m** |
| **荒漠天际线**（青海大柴旦） | 输电线 × 天际线 × 手机倾斜 1° | **100 m 内** |
| **温哥华樱花街** | 行道树开放数据 × 影子 × 街景 | **3 m** |

README 对每个案例都列了"**复现条件与检查点**"（输入、提示、关键参数、每一阶段应仍覆盖什么），方便找出自己那次跑偏在哪一步。它还诚实标注：**每个案例是"某一次、某个模型"的运行记录**，换个模型或换些决策**未必得到同样结果**。

**⑧ 关注度与状态。** **7,241 Star / 900 Fork / 18 关注**（ungh 快照，2026-11-17；shields 同期 7.2k / 900）；**7 个开放 Issue、贡献者 17 人**（shields）；**2026-09-18 创建**、pushed **2026-11-16**；`main`；**0 Release**；**147 个文件**；**shields 顶层语言 100.0%（README 说是"普通 Python 脚本"）**；**MIT**（`LICENSE` 1,066 字节）；README 有**英文 / 简体中文 / 日文**三版（`README.md` 35,345 B、`README.zh-CN.md` 31,897 B、`README.ja.md` 45,521 B），另有 `CONTRIBUTING.md`（4,763 B）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Oldcircle/geo-sleuth](https://github.com/Oldcircle/geo-sleuth) |
| 描述（API） | "An agent skill that finds where a photo was taken — OpenStreetMap geometry, elevation skylines, satellite imagery and street view — and shows its work. Works with Claude Code, Codex, Cursor, Gemini CLI, OpenCode and GitHub Copilot." |
| 许可证 | **MIT**（`LICENSE` 1,066 字节） |
| 主语言 | shields 顶层语言 **100.0%**（README 称"普通 Python 脚本"） |
| 关注度 | **7,241 Star / 900 Fork / 18 关注**（ungh，2026-11-17；shields 同期 7.2k / 900）；**7 个开放 Issue、贡献者 17 人**（shields） |
| 创建 / 最近推送 | **2026-09-18 创建**；`pushedAt` **2026-11-16**、`updatedAt` 2026-11-18 |
| 默认分支 / Release / 文件数 | `main`；**0 Release**；**147 个文件** |
| 根目录可见文件 | `README.md`（35,345 B）、`README.zh-CN.md`（31,897 B）、`README.ja.md`（45,521 B）、`CONTRIBUTING.md`（4,763 B）、`LICENSE`（1,066 B）、`.gitignore` |
| 形态 / 用法 | **标准 Agent Skill**（`SKILL.md` + Python 脚本）；`npx skills add Oldcircle/geo-sleuth`，首次跑 `scripts/doctor.py` |
| 数据源 | OpenStreetMap 几何 · 高程天际线 · 卫星影像 · 街景 |

## 使用前需要注意

- **① "脚本排名、模型判断"意味着结果依赖模型与决策。** README 明确：技能只告诉 Agent **怎么做**，而"查什么、怎么过滤、检查前几名"是**模型自己的决定**——**换个模型或换些决策可能得不出同样结果**。案例是"某次运行记录"，不是可复现的保证。
- **② 案例精度是"自报的运行记录"。** ±2 m / 100 m 内 / 3 m 等落点是 README 里**单次运行的记录**（还标注了当时用的模型，如 Claude Opus 5.0 / 5.5）；**本笔记未复现、未核验其方法学与统计口径**。
- **③ 它是"定位"能力，可能触及隐私 / 合规边界。** 用一张照片反推拍摄地，可用于**自己的照片排查**（如检查照片是否泄露位置），**也可能被用来定位他人**。**本笔记只记录该技能存在与结构，不对其任何具体使用场景作评价或背书**；使用者须自行确认授权范围与所在地法律。
- **④ 依赖外部数据源的可达性。** 它靠 OSM / 高程 / 卫星 / 街景等**第三方数据**；**这些服务在部分地区 / 网络下可能不可达或受限**，首次使用应通过 `doctor.py` 检查环境。
- **⑤ 无 Release。** **0 Release**，只能经 `npx skills add` 或自行复制文件夹安装；**仓库持续更新（pushed 2026-11-16）但未发版**。
- **⑥ 我未做任何验证。** 仅阅读 README（英文版前 180 行）、`LICENSE`、文件清单与元数据；**未安装技能、未运行任何脚本、未跑 `doctor.py`、未复现任何案例、未审计源码**；方法、分工与案例均来自 README 自述。

## 什么时候会再看

- 想研究"**照片地理定位（geolocation）**"这类任务如何被拆成"**几何约束 + 数据比对 + 排序**"，以及"**脚本排名、模型判断**"这种把重计算与判断分开的 Agent 协作结构。
- 想参考"**让每个结论都可追溯到具体命令与文件**"（"Every claim points at a file"）这条可审计纪律的写法。
- 想对照其它"**用公开数据找人 / 找物**"的技能与工具（[sherlock](sherlock-按用户名跨平台查找社媒账号.md) 按用户名查账号、[anatomy](anatomy-做机器等距解剖插图的Agent技能.md) 这类单点能力包）时；想了解"**一个标准 Agent Skill 如何跨六种 Agent 运行**"时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"用公开数据找人 / 找物"，线索不同**：[sherlock：按用户名跨平台查找社媒账号](sherlock-按用户名跨平台查找社媒账号.md)——那篇是**按一个用户名枚举数百个站点、判断账号是否存在**（给人用的单点 CLI）；本篇是**按一张照片反推拍摄地**（给 Agent 用的技能，含脚本排名 + 证据图）。两者都涉及**公开信息与隐私 / 授权的边界**，可对照"**按用户名**"与"**按照片**"两条线索、以及"CLI 工具"与"Agent 技能"两种形态。
- **同为"单点能力的 Agent 技能"，产物不同**：[anatomy：做机器等距解剖插图的 Agent 技能](anatomy-做机器等距解剖插图的Agent技能.md)——那篇把**复杂工艺与自检**打包成一枚**生成插图**的技能；本篇把**几何 + 地图数据 + 排序**打包成一枚**定位**的技能。两篇都可对照"**一个 Agent Skill 如何自带脚本 / 示例 / 自检**"的打包方式，以及不同任务下"脚本做什么、模型做什么"的职责切分。

## 参考来源

- 仓库主页与 README（英文版，核验日期 2026-11-17）：https://github.com/Oldcircle/geo-sleuth
- README 多语言版：`README.zh-CN.md` · `README.ja.md`（仓库内）
- 许可证 `LICENSE`（MIT，1,066 字节）：https://github.com/Oldcircle/geo-sleuth/blob/main/LICENSE
- 安装与贡献：`npx skills add Oldcircle/geo-sleuth` · `CONTRIBUTING.md`（仓库内）
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-17

以上信息核验于 2026-11-17：Star / Fork 与版本可能变化。**未安装技能、未运行任何脚本、未复现任何案例、未审计源码**；**方法、分工、精度与案例均为 README 自述（单次运行记录），本笔记未独立验证，也不对该技能的任何具体使用场景作评价或背书。**
