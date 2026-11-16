# video-shotcraft：用 Remotion 做电影感产品宣传片的视频技能

> 一句话定位（README 自述）：对 Claude Code 或 Codex 说一句"给我的产品做一支宣传片"，agent 就会**抓取你的真实页面 → 从调校好的动效配方库里挑镜头 → 排分镜 → 用 Remotion 做动画 → 按音乐卡点剪辑 + 配音效 → 逐帧检查 → 交给你一支可以继续修改的 MP4**。最值得记的三点：① 它**不是视频生成模型**——"每一帧都由代码基于你自己的截图和文案绘制，画面清晰、品牌准确，也能逐项修改"；② 镜头库是 **124 张配方卡 / 150 个镜头**（分 10 类），可在浏览器里的"动效工作台"继续改，也能导出**可编辑的剪映（CapCut CN）草稿**；③ Star 很高（页面 19,070），但它对**Remotion 自带许可**、**发布前替换演示素材**这两点有明确前提。

- 状态：草稿
- 最近核验：2026-11-13（阅读仓库 README（英文，分段）与中文版 `README_CN.md`（288 行）全文、`LICENSE`（Apache-2.0，11,340 字符）与元数据；**未 clone、未安装、未运行任何脚本、未渲染视频、未打开动效工作台、未访问 Gallery / Showcase 站点**）
- 适用范围：**面向 Claude Code 与 Codex**（skill 形态，含 `SKILL.md`）；制作依赖 **Node.js + npm（Remotion）、ffmpeg / ffprobe**，分析 BGM 节拍需 **uv**、抓取页面需 **puppeteer**；README 称**已在 macOS 上测试**，Linux / CI 另有注意事项（见下）

## 核心结论

1. **它做的是"代码画的片子"，不是像素生成的片子**（README 自述）：agent 先抓取你的**真实页面截图**，从配方库挑镜头、排分镜、用 [Remotion](https://www.remotion.dev/) 写 React 动画，最后交付一个**每帧都由代码绘制**的成片——所以分辨率高、品牌准确、**每一项都能改**（改文案、挪镜头、换颜色、变速）。
2. **流程是一条固定流水线**：`产品 → 抓取真实页面 → 用镜头配方卡排分镜 → Remotion 动画 → 踩点剪辑与声音设计 → 渲染与逐帧验收 → MP4 / 动效工作台 / 剪映工程`。
3. **交付物不止一个 MP4**：还包括**整支片的 Remotion 源码工程**（每个镜头是一个 React 组件）、一个**剪映式浏览器动效工作台**（多轨时间线、属性面板、变速、换镜头、导出），以及可选的**剪映草稿**与**社交平台发布**。

**产出清单（README 表格）：**

| 产出 | 说明 |
| --- | --- |
| 成片 | `MP4`，1920×1080、30fps、h264，含音乐和音效 |
| Remotion 工程 | 整支片的源码；每个镜头是一个 React 组件，你或 agent 都能改 |
| 动效工作台 | 交付后自动打开：多轨时间线、属性面板、变速、换镜头、导出 |
| 剪映工程 | 可选：可编辑的剪映草稿，镜头分段、字幕和音轨都能改 |
| 社交平台发布 | 可选：通过 Upload-Post 发到 TikTok / Instagram / YouTube / LinkedIn / X 等；先确认文案与平台、dry-run 后再发 |

**附带能力**：可复用的 Remotion 组件（2.5D 页面相机、字幕、闪切、数字滚动）、Freesound 音效搜索下载工具与 5 首 BGM、页面素材采集脚本，以及整套制作方法（素材采集、风格定调、分镜、声音设计、节奏卡点、最终验收）。

**相关项目**：同作者另有 **video-talkcraft**（口播视频版：给它口播稿和成品配音，所有动效节拍都钉在人声上，含 78 条口播动效样片）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | https://github.com/Vincentwei1021/video-shotcraft |
| 描述（原文） | "AI video skill for Claude Code & Codex — cinematic, editable product promos with Remotion: 124 shot recipe cards, 150 motion previews, beat sync, sound design, Motion Workbench & JianYing (CapCut CN) export." |
| 归属 | **个人账号 Vincentwei1021**（非组织） |
| 许可证 | **Apache-2.0**（`LICENSE` 11,340 字符，文件头为 "Apache License Version 2.0"；页面 spdxId 与 shields 均报 Apache-2.0） |
| 默认分支 | `main` |
| 关注度 | **页面 Star 19,070 / Fork 1,798 / Watcher 37**；ungh 镜像 18,918 / 1,782 / 37；shields 另报 **stars 19k、forks 1.8k、开放 Issue 3、贡献者 14**（2026-11-13，**页面值与镜像值有差异**） |
| Release | 2（tagCount 2） |
| 维护状态 | **2026-07-19 创建**；ungh `pushedAt` 2026-11-11、页面 `updatedAt` 2026-11-12（活跃） |
| 官网 / 样片 | Gallery <https://vincentwei1021.github.io/video-shotcraft/> · Showcase <https://vincentwei1021.github.io/video-shotcraft/showcase.html> |
| 镜像 | AtomGit：`git clone https://atomgit.com/VincentWei/video-shotcraft.git` |
| topics | agent-skills, ai-agents, ai-video, claude-code, claude-code-skills, claude-skills, codex, motion-design, motion-graphics, product-video, promo-video, remotion, video-generation, video-production |

## 镜头库与用法

### 镜头库（10 类 / 124 卡 / 150 镜头）

| 类别 | 数量 | README 举例 |
| --- | --- | --- |
| 开场与品牌 | 11 | `text-as-mask`：透过巨型标题字看到产品界面 |
| 运镜与空间 | 13 | `exploded-view`：页面 3D 倾斜后构件炸开 |
| 转场 | 19 | `card-flock-tumble`：一群卡片翻滚成大标题 |
| 文字与字卡 | 25 | `flying-words`：功能词穿过文字隧道 |
| 界面登场 | 22 | `card-stack`：一叠配方卡扇形发牌落位 |
| 交互演示 | 13 | `palette-theme-ripple`：换主题时颜色波纹扫过界面 |
| 数据与指标 | 9 | `axis-rescale-shock`：数据冲高，坐标轴重新缩放 |
| 光效与强调 | 23 | `spotlight-sweep`：舞台追光找到标题 |
| 节奏与蒙太奇 | 8 | `paparazzi-flash`：三次闪光切换不同裁切 |
| 收尾 | 7 | `input-morph-assemble`：聊天输入框拼成标志 |

每张卡在 `references/shots/<类别>/<卡名>.md`，对应的 Remotion 实现放在 `demos/<类别>/<卡名>/`；可在 Gallery 里搜索、筛选、复制卡名。

### 怎么用（无需命令）

| 你说 | 会发生什么 |
| --- | --- |
| `用 video-shotcraft 给我的产品做一支宣传片。` | agent 先介绍 Ink Press 模板并问你是否采用，然后抓取页面、做片 |
| `用 Ink Press 模板给这个仓库做一支发布片。` | 最快路径：把截图、文案和标志放进已验收的模板 |
| `用 deck-deal-flyin 和 row-embed 两张镜头卡展示这个功能。` | 你按名字点镜头（名字可直接从 Gallery 复制） |
| `参考 spotlight-hero-card，为这个页面设计一个产品特写镜头。` | 单个镜头，按你的产品改编 |

### 安装

```bash
npx skills add Vincentwei1021/video-shotcraft
# 或手动：git clone 后软链到 ~/.claude/skills/video-shotcraft 与 ~/.codex/skills/video-shotcraft
```

### 动效工作台

交付后 skill 会在浏览器里打开一个剪映式编辑器（`node workbench/scripts/open.mjs <工程>`）：成片按原始镜头拆成**镜头 / 转场 / 字幕 / 音效**多轨，选中任意镜头可改文案、字号和颜色，镜头可挪、可裁、可变速，镜头库里的 150 个镜头都能直接拖上轨，改完用 Remotion 导出；预览与渲染逐帧一致。

## 使用前需要注意

- **Remotion 有它自己的许可**：README 明写 [Remotion](https://www.remotion.dev/) 的许可为"**个人与小团队免费，公司可能需要付费许可**"。也就是说这个仓库是 Apache-2.0，但渲染引擎的商用许可要单独确认。
- **剪映导出只在 macOS 上实测过**：README 称"Mac 剪映 11.2 实测验收"，项目结构里 `jianying-export/` 也标注"**Mac 实测 / Windows 未验证**"。
- **模板里的截图是演示素材**：README 提示"对外发布成片前，请替换为你自己产品的截图，并确认其中的数据、客户信息和个人信息是否需要脱敏"。
- **音效许可逐文件不同**：Freesound 音效的许可记在项目来源清单里，**CC BY 署名需随发布保留**；BGM 见 `assets/audio/ATTRIBUTION.md`。
- **Linux / CI 上有三个坑**（README 实测：2 核、Node 22）：低核机器要加 `--concurrency=1`；新版 Chrome 删了旧 headless 模式，要改用 `chrome-headless-shell` 二进制；remotion.media 访问不了（国内常见）时需用 `--browser-executable=<本地 chrome-headless-shell 路径>` 指定本地二进制。
- **社交发布是可选动作**：通过 Upload-Post 发布时，README 要求"**先确认文案与平台、dry-run 后再发**"。
- **镜头配方来自对优秀宣传片的研究**：README 致谢称多张镜头卡受 ClickUp、Perplexity、Slack、Notion、Figma、Framer、Raycast 等品牌片启发，但"记录的是从零重新实现的动效技法（时序、缓动、编排）"，仓库**不含上述影片的任何素材、画面或品牌资产**。
- **我只读了 README 与 LICENSE**：**未 clone、未安装、未运行、未渲染、未打开工作台、未访问在线站点**；上述能力均来自文档自述，**未逐一验证**。
- **引人注意的活跃度**：README 提到 2026-10 镜头库刷新（全库 216 个镜头择优保留 124 卡 / 150 镜头），并挂有 Trendshift 徽章；Star 页面值与 ungh 镜像值相差约 150，**引用数字时注明来源与日期**。

## 什么时候会再看

- 想给**自己的产品 / 开源仓库做发布片**，且希望结果**品牌准确、可逐项修改**（而不是一次性生成）时。
- 想看"**用配方卡（shot recipe card）+ 代码动画**组织镜头库"这种形态怎么设计（`references/shots/` + `demos/` 一一对应）时。
- 想把**动效节拍钉在音乐卡点上**、并参考"声音设计 + Freesound 选材 + 署名清单"的完整方法时。
- 想研究**成片 → 可编辑剪映草稿**的导出思路（镜头分段、字幕重建为原生文本轨、SFX/BGM 独立音轨）时。
- 想对照其它"AI 做视频"的路线：[adu-motion-video](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)（模板工程包）· [OpenMontage](OpenMontage-把AI编码助手变成视频制作工作室.md)（agentic 视频流水线）· [srt-whiteboard-animation](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)（字幕驱动白板动画）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **产线正好对接**：[capcut-cli：直接读写剪映草稿的命令行剪辑工具](capcut-cli-直接读写剪映草稿的命令行剪辑工具.md)——本篇把成片**导出为可编辑的剪映草稿**，那篇则**直接读写剪映 / CapCut 的草稿 JSON**做字幕、变速、长转短；一个负责"生成可编辑工程"，一个负责"用命令行继续改工程"。
- **同属"给编码助手一套视频技能"**：[adu-motion-video：阿杜的 AI 剪辑模板技能与 MotionGit 开放格式](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)——那篇是**动画模板工程包**（`.motion`，模板迁到第三方站点 MotionGit），本篇是**镜头配方卡 + Remotion 代码动画**（配方卡与实现都在仓库内）。可对照"**模板分发**"与"**镜头库 + 流水线**"两种组织方式。
- **也是 agent-first 的视频制作**：[OpenMontage：把 AI 编码助手变成视频制作工作室](OpenMontage-把AI编码助手变成视频制作工作室.md)——那篇把编码助手当**整条流水线的编排器**（研究、编剧、素材、剪辑、审批门、预算治理），本篇聚焦**产品宣传片**这一条细分流水线且强调**可编辑性**。
- **都用字幕 / 单帧驱动的动画产物**：[srt-whiteboard-animation：把 SRT 字幕做成白板手绘动画的技能](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)——那篇输入 SRT、输出**单一手绘风格**；本篇是**多风格镜头库**。可对照"**单一视觉隐喻**"与"**可复用镜头配方**"。
- **同属"给编码助手一套视频技能 / 流程"，形态更重**：[baocut：带可编辑时间轴的 AI 视频 Agent](baocut-带可编辑时间轴的AI视频Agent.md)——本篇是**Skill + 镜头配方卡**（偏"把编码助手变成制作者"），那篇是**带本地 Runtime 与可编辑时间轴的桌面应用 + Skill + MCP**（偏"Agent 剪辑、人复核"）；两者的输出都强调**可继续编辑**。

## 参考来源

- 仓库主页：https://github.com/Vincentwei1021/video-shotcraft
- README：英文 `README.md` 与中文 `README_CN.md`（288 行）；Gallery <https://vincentwei1021.github.io/video-shotcraft/>
- 许可证：`LICENSE`（Apache-2.0，11,340 字符；核验日期 2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌 JSON 与 ungh.cc、shields.io 交叉核对（2026-11-13，**页面值与镜像值存在差异**）
- 关联说明：`references/jianying-export.md`、`assets/audio/ATTRIBUTION.md`（README 链接）

以上信息核验于 2026-11-13。**未 clone、未安装、未运行任何脚本、未渲染过视频、未打开动效工作台、未访问 Gallery / Showcase 站点**；能力与流程描述均来自 README 与中文版 README，**未逐一验证**。
