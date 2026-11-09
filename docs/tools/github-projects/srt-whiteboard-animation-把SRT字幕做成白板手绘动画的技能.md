# srt-whiteboard-animation：把 SRT 字幕做成白板手绘动画的技能

> 记录一个"给一份 SRT 字幕就自动画白板手绘视频"的 Agent 技能：**编排沿用分区遮罩揭示（`annotation.json`），画法换成流式连续笔迹（ink 铺线稿 → color 添彩）**。第一句先记清三件事：**它是"技能（`SKILL.md`）"而不是一个可独立运行的软件**；**SKILL 强制"每步完成后停下来等用户确认"**（先出分镜与配图策略，确认了才画）；**仓库只有一次提交、一个 Release（v1.0.0，2026-07-27），此后无新提交**，但 Star 与 Fork 数都很高。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README 全文、`SKILL.md` 全文、`LICENSE`、目录树、Release 与 GitHub 元数据；**未安装依赖、未运行任何脚本、未生成任何视频**）
- 适用范围：Python；脚本会自建 `.venv` 并安装 `opencv-python` / `numpy` / `av`；面向支持"技能"的编码助手（仓库内带 `agents/openai.yaml`，形态偏 Codex 技能）

## 核心结论

**① 它解决的问题很单一：字幕 → 白板手绘视频。** 输入一份 SRT 字幕，输出一段白板手绘动画 MP4。README 把流程写得明确：解析 SRT → 按 **25–35 秒/幕**拆场景 → **先输出分镜与配图策略**（此时不画）→ 用户确认 → 生成统一风格线稿 → 按叙事语义标注分区 → 浏览器预览台微调 → 逐幕渲染再合并。

**② 关键技术点是把"编排"与"画法"拆成两层。**

- **编排**沿用"分区遮罩揭示"：按叙事顺序逐区域揭示，未开始的区域完全隐藏，重叠部分用 `protectedRegions` 保护；状态存在 `annotation.json`（区域 / 时序 / 字幕关联 / 重叠保护区）。
- **画法**换成"流式连续笔迹"：每个区域在自己的允许掩码里，笔尖沿骨架或网格连续滑行落墨；**先铺线稿（ink）再添彩（color）**；所有区域共享同一张持久画布，画完的区域留在画布上。
- **遮罩不变量**（`SKILL.md`）：某区域的允许掩码 = 该区域 **减去** 后续区域与 `protectedRegions`——这保证前一个区域不会先画到后面区域的地盘上。
- **两条可调路径**：`--ink-path grid|skeleton`（网格或骨架走笔）、`--color-fill contour-wipe|brush`（轮廓擦除或笔刷填色）。

**③ 它是"技能"，而且强制逐步确认。** `SKILL.md` 的 frontmatter 声明 `name: srt-whiteboard-animation`，并写明**每个阶段完成后必须停下来等用户确认**，不允许一路画到底。对"AI 一次生成一整套视频"这类容易跑偏的任务，这种分步卡点相对稳妥。（个人判断：这是它比"一键出片"更容易控住质量的地方，但我未实际跑过。）

**④ 仓库形态与关注度严重不成比例，值得单独记一笔。** **5,348 Star、842 Fork**，但**只有一次提交、一个 Release（v1.0.0）**，创建与推送都在 **2026-07-27**（此后无新提交），订阅者仅 **20**。这更像"发布即被传播"的技能仓库，而不是持续迭代的工程。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [geeklee/srt-whiteboard-animation](https://github.com/geeklee/srt-whiteboard-animation) |
| 描述 | "将 SRT 字幕做成暖米黄纸张底的流式笔迹白板手绘动画 skill：mask 分区遮罩编排 + stream 连续笔迹（ink→color）。" |
| 许可证 | **MIT**（`LICENSE`，Copyright (c) 2026 江哥是老登啊） |
| 主语言 | Python |
| 关注度 | **5,348 Star、842 Fork、20 关注、10 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-07-27 创建，同一天推送后无新提交**；`main` 分支仅 **1 次提交**（`feat: initial release of srt-whiteboard-animation skill`）；**1 个 Release：v1.0.0（2026-07-27，首个公开发布）**；默认分支 `main`；无 homepage |
| 体积 | API `size` 12,316 KB（约 12 MB）；目录树 23 项 / **19 个 blob / 约 12.6 MB** |
| 话题标签 | `ai-skill`、`hand-drawn`、`opencv`、`python`、`srt`、`subtitle`、`whiteboard-animation` |
| 仓库结构 | 根目录：`SKILL.md`（约 14 KB）、`README.md`（约 6.4 KB）、`LICENSE`、`.gitignore`；目录：`agents/`（含 `openai.yaml`，Codex 技能元数据）、`assets/`（含预览台 `preview.html`）、`examples/`、`scripts/` |

> 关于关注度：**5,348 Star 但订阅者只有 20，Fork 却高达 842**，且**仓库自创建当天后没有任何新提交**。star/fork 数不能当作"经过长期验证"的成熟度。

## 它能做什么

**工作流（README 六步）**

| 步骤 | 做什么 |
| --- | --- |
| 1 解析 | `parse_srt.py` 读 SRT，按 **25–35 秒/幕**拆场景 |
| 2 定策略 | **先出分镜与配图策略**（不画），等用户确认 |
| 3 画线稿 | 按统一风格生成线稿；`--ink-path grid\|skeleton` 决定走笔路径 |
| 4 标分区 | 用 `annotation.json` 标注区域 / 时序 / 字幕关联 / 重叠保护区 |
| 5 预览调 | `render_annotation_preview.py` + `assets/preview.html`（用 File System Access API）在浏览器里看与调 |
| 6 渲染合并 | `render_stream_whiteboard.py` 逐幕渲染，`merge_scenes.py` 合并为 MP4 |

**脚本清单**

- `prepare_env.py`：建 `.venv` 并安装 `opencv-python` / `numpy` / `av`；
- `parse_srt.py`：字幕解析与分幕；
- `render_annotation_preview.py`：分区 / 时序预览；
- `render_stream_whiteboard.py`：按 stream 画法逐幕渲染；
- `merge_scenes.py`：多幕合并成片。

**默认风格参数（`SKILL.md`）**

- 纸张背景色 `#F5EBD7`（暖米黄）；
- 每个区域"ink 铺线稿 → color 添彩"两遍走笔；
- 所有面向用户的说明、分镜、配置与界面文字**要求使用中文**。

## 使用前需要注意

- **它是技能，不是开箱即用的软件。** 需要放进支持 SKILL 的编码助手（仓库带 `agents/openai.yaml`，形态偏 Codex 技能），再让它读 `SKILL.md` 执行；本笔记**未安装、未跑通任何一步**，所有能力描述来自 README 与 `SKILL.md`。
- **会真的装依赖、跑 OpenCV 并编码视频。** `prepare_env.py` 会自建虚拟环境并安装包；渲染依赖 `opencv-python`、`numpy`、`av`，在本地会实际消耗 CPU 与磁盘。
- **输出质量依赖"分镜/配图策略"这一确认环节。** SKILL 强制每步停下等确认，等于把质量闸门放在人这边；如果一路回车确认，最终效果没有兜底（个人判断）。
- **仓库已静止，且高度依赖宿主技能机制。** 自 2026-07-27 后无提交、无 Issue 反馈沉淀；若宿主助手的技能接口变化，本仓库不会跟随更新。
- **本笔记的核验边界**：只读了 README、`SKILL.md`、`LICENSE`、目录树与元数据；**没有安装、没有运行、没有产出任何视频**，画法细节与最终观感未验证。

## 什么时候会再看

- 手上有 SRT 字幕（课程、讲解视频、播客切段），想快速套一个"白板手绘"视觉风格时；
- 想参考"**字幕驱动的分镜 + 分区遮罩 + 流式笔迹**"这套视频生成编排写法时；
- 想抄"**AI 生成任务如何用分步确认卡住质量**"的交互设计时；
- 想复盘"技能型仓库的 star/fork 与实际迭代量严重脱节"这个现象时。

## 相关笔记

- **同为"用工具产出视觉资产"，路径相反**：[manim：数学动画引擎](manim-数学动画引擎.md)——那是代码驱动、帧帧确定性渲染的动画引擎；本篇是"字幕驱动 + 分段确认"的技能，笔迹是生成式落墨；对照能看清"确定性渲染"与"按叙事排布的手绘笔迹"两种产线。
- **同为"给编码助手（Codex）用的生图技能"**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——一个产游戏精灵与地图，一个产白板动画；可对照技能定义（`SKILL.md`）的组织方式。
- **同属"产出视频 / 动画"**：[ComfyUI-MiniMaxH3：Easy-MiniMaxH3 视频节点套件](ComfyUI-MiniMaxH3-Easy-MiniMaxH3视频节点套件.md)——那边是 ComfyUI 节点工作流，本篇是独立 skill + 脚本管线。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/geeklee/srt-whiteboard-animation
- 技能定义：`SKILL.md`（含 frontmatter、默认实现参数与遮罩不变量）
- 许可证：`LICENSE`（MIT，Copyright (c) 2026 江哥是老登啊）
- Release：v1.0.0（2026-07-27，首个公开发布）
- 元数据（Star / Fork / 时间 / 体积 / topics）：GitHub REST API；tag 数（1 个）与 Release 经 jsDelivr 与 `releases.atom` 核验，2026-11-04

以上信息核验于 2026-11-04。仅阅读 README、`SKILL.md`、`LICENSE`、目录树与元数据；未安装、未运行、未生成任何视频，实际效果未验证。
