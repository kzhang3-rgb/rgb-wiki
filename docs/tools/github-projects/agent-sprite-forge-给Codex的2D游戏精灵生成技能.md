# agent-sprite-forge：给 Codex 的 2D 游戏精灵与地图生成技能

> 记录它为什么不是"一堆提示词模板"，以及它把哪些环节交给模型、哪些环节交给确定性脚本。未安装技能、未在 Codex 里跑过生成。

- 状态：草稿
- 最近核验：2026-11-03（阅读仓库 README、简体中文 README、`skills/generate2dsprite/SKILL.md` 与 GitHub API 元数据；未安装、未生成任何素材）
- 适用范围：Codex（依赖其**内置图像生成** `image_gen`）；本地后处理需 Python 与 `requirements.txt`；导出面向 Godot / Unity / 普通 2D 工作流

## 核心结论

它不是提示词合集，而是一条**给 Codex 的 2D 游戏资产流水线**：用自然语言说需求，**由 Agent 自己判断**要什么资产、什么动作、几帧、怎么排版，用 Codex 内置图像生成产出原始图，再用本地 Python 脚本做确定性的去背、切格、对齐、校验与导出。

**它最值得借鉴的设计是分工边界**：SKILL.md 里写得很直白——"**脚本不是创意大脑**"，Agent 负责视觉与流程决策，Python 工具只做可重复的像素处理。原始图一律要求**纯洋红 `#FF00FF` 背景**，这样去背就退化成一次颜色判定，而不是让模型直接产出透明通道。

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [0x0funky/agent-sprite-forge](https://github.com/0x0funky/agent-sprite-forge) |
| 归属 | 个人开发者 0x0funky |
| 许可证 | MIT（根目录 `LICENSE`） |
| 主语言 | Python（技能脚本；图像本身由 Codex 内置生成） |
| 关注度 | 4,897 Star、482 Fork、**16 订阅者**（`subscribers_count`） |
| 活跃度 | 2026-04-23 创建，最近一次推送 **2026-10-06**（距核验日约一个月），默认分支 `main`；不算日更型项目，但也不是停更 |
| 待办积压 | 14 个开放 Issue |
| 仓库体积 | 约 88,022 KB ≈ **86 MB**——主要来自 `src/` 下的示例动图、预览图与 banner，不是代码体量 |
| 其他 | 4 个 topics（`agent-skills`、`codex`、`pixel-art`、`sprite-generator`）；无 Wiki / Pages / Discussions |

## 它给出什么

仓库 README 只列了两个技能，SKILL.md 里还提到一个 `generate2dgamepack`（我未核对目录，**两处口径略有出入**）：

| 技能 | 用途 | 典型输出 |
| --- | --- | --- |
| `generate2dsprite` | 角色 / 怪物 / NPC / 道具 / 攻击 / 法术 / 投射物 / 命中特效 / 参考图驱动的变体 | 原始 sheet、去背后的透明 sheet、单帧 PNG、GIF、QA metadata |
| `generate2dmap` | 烘焙地图、分层栅格地图、高清 RPG 地图、prop pack、碰撞与触发区、**可编辑的 Godot 场景**、横版 / 视差关卡 | base map、dressed reference、prop pack、提取出的透明 props、预览图、场景元数据 |

脚本侧支持的具体动作：洋红去背（chroma-key）、frame extraction、对齐、prop pack 切割（`2x2` / `3x3` / `4x4`）、透明 PNG / GIF 导出、QC metadata。

## 安装（README 原文要点）

本质是"克隆 → 装 Python 依赖 → 把技能目录拷进 Codex 的 skills 目录"：

```powershell
git clone https://github.com/0x0funky/agent-sprite-forge.git
cd .\agent-sprite-forge
python -m pip install -r .\requirements.txt
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\skills\*" "$env:USERPROFILE\.codex\skills\"
```

macOS / Linux 用 `cp -R ./skills/* ~/.codex/skills/`。README 提醒**装完要重开 Codex session** 才会干净载入。

使用方式是自然语言加技能名，例如 `Use $generate2dsprite to create a 3x3 idle for an ultimate earth titan.` 或 `Use $generate2dmap to create a Godot-editable RPG map with ...`。

## 使用前需要注意

- **硬绑定 Codex**：README 与 SKILL.md 都要求用**内置 `image_gen`** 出原始图。这意味着它不是一个可以塞进任何 Agent 的通用技能——换成别的宿主，核心那步就没有对应能力。
- **示例素材里有知名 IP 角色**：仓库演示图包含悟空、鸣人等动画角色的精灵与 GIF。README 自己在 Notes 里提醒"**商业项目请优先使用原创角色或你拥有权利的 IP**"。这些示例**不能当作可商用素材**使用，它们只是效果演示。
- **装的是可执行脚本，不是纯文本**：`pip install -r requirements.txt` 会引入 Python 依赖，脚本会读写你指定的输出目录。**我未审计代码、未安装、未运行**，脚本行为与 README 描述是否一致未验证。
- **86 MB 的仓库体积**：克隆与拉取会明显慢于普通技能仓库，主要成本在示例素材。
- **不是"一键出成品游戏"**：它产出的是**资产与场景数据**（sheet、GIF、Godot 场景 / TileMap 层、碰撞与触发区），可玩原型仍需 Agent 继续组装代码；README 的 Unity / Godot 演示是"用这套工作流组装出来的"，不等于克隆完就有游戏。
- **地图产物有两种形态**：简单地图是单张烘焙图（不可再编辑），复杂地图才走"分层 + prop pack + 提取透明 props"的管线——想要可编辑就得接受更长的流程。

## 什么时候会再看

- 想给 Codex 加"从一句话到可玩 2D 原型"的资产生产能力时。
- 想参考**"LLM 负责决策、脚本负责像素"这条分工边界怎么在技能里写清楚**时（`SKILL.md` 里的 Agent Rules / Defaults 是很好的范本）。
- 需要给 Godot / Unity 批量准备 sprite sheet、TileMap 与碰撞数据时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"用 AI 生图产出可复用视觉资产"**：[nvzhuang：拍片哥女装 AI 服装参考图集](nvzhuang-拍片哥女装AI服装参考图集.md)——那边是服装参考图集 + 虚拟试穿（资产是成品图库，靠多模型后端生成）；本篇是游戏精灵与地图（资产由 Agent 按管线现场生成，再交给引擎）。两者正好是"图库型"与"流水线型"两种 AI 生图用法。
- **同为"用工具生成视觉产物"**：[manim：数学动画引擎](manim-数学动画引擎.md)——那是代码驱动、帧帧确定性渲染的动画引擎；本篇是模型生成 + 脚本清理的非确定性路径，对照起来能看清"确定性渲染"与"生成后处理"两种产线。

## 参考来源

- 项目仓库与 README：https://github.com/0x0funky/agent-sprite-forge
- 简体中文 README：`README.zh-CN.md`（另有 zh-TW / ja / ko 版本）
- 技能定义：`skills/generate2dsprite/SKILL.md`（含参数表、Agent Rules、Defaults 与后处理流程）
- 元数据（Star / Fork / 订阅数 / 体积 / 分支 / 许可证 / topics）：GitHub REST API，2026-11-03

以上信息核验于 2026-11-03。Star 数、技能清单与产出格式都可能变动；我未安装技能、未在 Codex 中生成任何素材，全部能力描述来自 README 与 SKILL.md，实际效果未验证。
