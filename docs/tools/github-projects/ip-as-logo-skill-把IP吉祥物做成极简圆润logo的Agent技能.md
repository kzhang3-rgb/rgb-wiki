# ip-as-logo-skill：把 IP 吉祥物做成极简圆润 Logo 的 Agent 技能

> README 自述：这是一个**紧凑型 Agent Skill**，专门把主体生成"**极度简单、可爱、公司可用**"的 **IP 吉祥物形象**——它把审美取舍写成硬约束：**约 4–7 个大基本形构成一个主导剪影**、**默认三种语义色**（两个 IP 基色 + 一个背景色）、**左下角探出且占比约 85–95%**、**粗圆厚实、去掉所有非必要线条与细节**，并带**极淡的新拟物质感**。最值得记的是三点：① 它**故意把"生成"当成一次性抽取（creative draw）**——**不做透明通道检查、不拦截、不自动重试、不静默修图**，出了什么就给什么；② 它**从不向图像模型暴露"这是 logo / 品牌标记 / 应用图标"**，提示词只描述"一张方形角色图"；③ 它要求**顶级图像模型**（优先 GPT Image 2，或 Seedance 5.0 Pro / Nano Banana Pro / Nano Banana 2），**明确不降级到 SVG**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（91 行）、`LICENSE`（1,064 字符）与已核实元数据；**未安装、未调用任何图像模型、未生成任何图片、未访问其站点 `visualskills.dev/ip-as-logo`**）
- 适用范围：README 点名支持 **Codex · Coze · Doubao · YouMind · Manus · Gemini Apps · Replit Agent**，并要求助手**具备顶级图像模型**（优先 GPT Image 2，或 Seedance 5.0 Pro / Nano Banana Pro / Nano Banana 2）；**没有合适模型时须启用工具或提供 API Key**，可选兼容客户端用子代理**并行出图**

## 核心结论

1. **它是一个"单文档技能"**（README "Repository structure"）：仓库只有 `SKILL.md`、`README.md`、`LICENSE` 与一张展示图 `assets/ip-as-logo-wall.webp`；**"技能本身刻意只包含一份指令文档"**——**没有脚本、没有风格参考、没有生成依赖**。
2. **它的价值在"审美约束"这层提示词工程**（README "What it guides"）：
   - 一个主导剪影，由**约 4–7 个大基本形**搭建；
   - **三种语义色**：两个 IP 基色 + 一个背景色；
   - **先给三个方向**，用户认可后**再出六个独立候选**（`A1`、`A2`、`B1`、`B2`、`C1`、`C2`，即每个方向两个变体）；
   - 默认主题是**熟悉的、公众接受度高的动物**，物件 / 机器 / 幻想器物 / 冷门生物**需要明确的产品理由**；开放式批量里 **95–100% 应是常见动物**；
   - 主体**从左侧（通常左下角）探出**，**不做左右交替**，占比约 **85–95%**；
   - 背景是**一个"有名字的纯色"**填满方形，**轻度降低饱和度**、**极淡新拟物纵深**；提示词**不出现** `opaque`、`alpha`、`transparency` 等词；
   - **发出的提示词绝不透露 logo / 品牌标记 / 应用图标 / 图标资产**这些用途。
3. **"一次抽取、不做质检"是明确设计**（README "What it guides" / "Model behavior"）：每个候选**只生成一次并原样交付**——**不检查透明通道、不把结果分类为合规/不合规、不因背景、颜色、构图、渐变、明暗或立体感而自动重试**；用户在查看整批后可**显式要求重抽或微调**（视为一次新的抽取）。

**个人判断（未验证）**：这个技能的巧妙处在于**用"负向提示词纪律"换取审美稳定**——把"这是 logo"藏起来、把颜色数量压到三种、把背景降到低饱和、把主体定在左下角，都是为了**让图像模型不去"设计"，只去"画一个圆润可爱的角色"**；而"不做质检、不重试"则等于承认图像生成是**随机抽样**，避免助手把"我不满意"包装成"自动修正"，让用户自己判断。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [s1dashu/ip-as-logo-skill](https://github.com/s1dashu/ip-as-logo-skill) |
| 描述 | "A compact Agent Skill for highly simplified, rounded, subtly neo-skeuomorphic IP mascot logos."（GitHub 描述**原文**） |
| 许可证 | **MIT**（`LICENSE` 1,064 字符，Copyright (c) 2026 s1dashu；与 GitHub 页面 `spdxId` = MIT 一致） |
| 关注度 | **页面 6,544 Star / 6 Watcher / 317 Fork**（与 ungh 镜像一致）；shields stars 6.5k、forks 317、**开放 Issue 7**、**contributors 1**（2026-11-13） |
| 维护状态 | **2026-08-18 创建**；ungh `pushedAt` **2026-11-06**、页面 `updatedAt` 2026-11-13；默认分支 `main`；**Release 0** |
| topics | codex、codex-skill、image-generation、logo-design、mascot-design |
| 站点 | 技能站点（可搜索图库，Cloudflare R2 + Supabase 支撑）：<https://visualskills.dev/ip-as-logo> |
| 形态 | 单文档技能：`SKILL.md` + `README.md` + `LICENSE` + `assets/ip-as-logo-wall.webp`；**无脚本、无依赖、无风格参考文件** |

## 功能与用法

### 1. 安装（README "Install"）

```bash
npx skills@latest add s1dashu/ip-as-logo-skill
# 需要跨项目全局安装时：
npx skills@latest add s1dashu/ip-as-logo-skill --global
```

安装器会识别仓库根的 `SKILL.md`，让你选一个支持的编码助手，并**连同支撑资源一起装入完整的 `ip-as-logo` 目录**。

### 2. 使用方式（README "Use"）

直接向助手要一张 IP 吉祥物图即可，例如：

```text
Create a very simple, cute rounded ghost IP character on a solid deep navy background.
```

- **用户已指定主体**时，技能给出**该主体的三个受控设计方向**；**主体开放**时，**先推荐常见动物吉祥物**，并把每个动物**绑定到一个产品属性或品牌承诺**。
- 默认**不问"用色模式"**：每个默认候选都用三种语义色；**两色图只在用户明确要求时生成**，且用**背景色负空间**画面部标记，而**不引入第三种颜色**。
- 批量较大时，靠**物种/品种、耳朵与口鼻比例、表情、左下探出与裁切变化、剪影、次级色组织**制造差异；**钟表、锁、工业工具、测量仪器、车辆、抽象机器、幻想器物、冷门生物都不是默认的公司吉祥物**。
- 每个结果是**独立的满分辨率方形资产**，**绝不做成六宫格拼图**。

### 3. 站点与商用（README）

- README 引导没有 Codex / 豆包 / Coze / Workbuddy 的人去站点 <https://visualskills.dev/ip-as-logo> **免费下载现成 logo**，自述"**每个 logo 均可免费商用**"。
- **注意区分口径**：仓库的 **MIT 许可覆盖的是这个技能仓库本身**（指令文档等）；"**站点下载的现成 logo 可免费商用**"是 **README 关于站点的自述**。

## 使用前需要注意

- **仓库许可证 ≠ 生成图版权**：`LICENSE` 是 **MIT**，作用在**仓库（技能文档）**上；**你用图像模型生成出来的角色图，其可商用性取决于你使用的模型/服务的条款与你的输入**——README 关于"免费商用"的说法**指向其站点的现成图库**，**不代表你生成的图自动获得同样授权**。商用前请自行确认。
- **必须有顶级图像模型，且明确不降级 SVG**：README 写"**技能绝不会回退到 SVG**"，**只有用户明确同意才换别的图像模型，且不保证同等质量**；缺模型时须启用合适工具或提供 API Key。**这意味着这不是一个"装好就能用"的技能**，前置门槛是你已接入的生成能力。
- **"单文档"意味着能力全靠助手的模型**：仓库里**没有脚本、没有依赖、没有校验**；所有约束都写在 `SKILL.md` 的指令里，**实际效果高度依赖所选图像模型的指令遵循度**。README 也承认"**图像生成模型是随机的，可能对个别约束给出不同解读**"。
- **"不质检"是特性也是风险**：技能**不做透明通道检查、不拦截、不自动重试、不静默修复**——你拿到的就是**原样返回的批次**，合不合用由你自己判断；不满意需**显式**要求重抽或微调。
- **安装命令口径**：README 给的是 `npx skills@latest add ...`（带 `@latest`）。**这与我核验的另一篇技能笔记里的 `npx skills add ...` 写法不同，属于各自仓库的自述，我未实际执行以对比**。
- **"顶级模型"名称是 README 自述**：GPT Image 2、Seedance 5.0 Pro、Nano Banana Pro / 2 等均来自 README，**我未核验这些模型的实际可用性与命名对应关系**。
- **元数据**：Star **6,544** 与 ungh 镜像一致；**开放 Issue 7 / 贡献者 1**（shields.io）。体量很高但**贡献者仅 1 人，属个人项目**。
- **我只读了 README 与 `LICENSE`**：**未安装、未调用任何图像模型、未生成任何图片、未访问站点**；`SKILL.md` 正文（README 摘要之外的具体措辞）**我未读取**，其内容未核实。

## 什么时候会再看

- 想给产品/项目做一版**极简、圆润、可爱、公司可用**的**吉祥物形象**，且希望**先看方向、再批量出候选**时。
- 想参考"**把审美取舍写成图像生成硬约束**"的提示词写法（基本形数量、语义色数量、左下探出、占比、低饱和背景、负向词表）时。
- 想对比"**单文档技能**"（只有一份指令，能力全交给模型）与"**工程化技能**"（多脚本、多阶段、带校验）的取舍时。
- 想参考"**把生成当随机抽取、不做自动质检**"这种克制的助手行为设计时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"给 Codex 装图像生成能力的技能"**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——那篇生成**2D 游戏精灵**，本篇生成**极简吉祥物形象**。可对照"**游戏资产向**"与"**品牌形象向**"两类图像技能的约束写法。
- **同属"给编码助手装生成能力的 Skill"，形态相反**：[lanshu-create-ai-presenter-video：从脚本到可验证 AI 数字人播报视频的技能](lanshu-create-ai-presenter-video-从脚本到可验证AI数字人播报视频的技能.md)——那篇是**多脚本、多阶段、带八状态证据门的视频制作技能**，本篇是**单文件、无脚本、把一切交给模型的图像技能**。可对照"**单文档技能**"与"**工程化技能**"。
- **同属"助手直接产出视觉资产"**：[svg-diagram：手写 SVG 图表的 Agent 技能与 linter](svg-diagram-手写SVG图表的Agent技能与linter.md)——那篇产出**可校验的矢量 SVG 图表**，本篇**明确不碰 SVG**、只出**位图角色形象**。可对照"**确定性的矢量产物**"与"**随机的位图抽取**"。
- **同属"给 Agent 装图像生成能力"，一个云端技能、一个本地工具**：[qwen-image-local：在 8 GB 显卡上本地跑 Qwen-Image 的桌面工具](qwen-image-local-在8GB显卡上本地跑Qwen-Image的桌面工具.md)——那篇是**本地桌面工具 + CLI**（跑本地权重、推理不出本机），本篇是**面向编码助手的云模型生成技能**（只出一张方形角色图、不碰本地权重）。可对照"**云模型技能**"与"**本地权重应用**"。
- **都用代码 / 脚本产出视觉资产，产物不同**：[anatomy：做机器等距解剖插图的 Agent 技能](anatomy-做机器等距解剖插图的Agent技能.md)——那篇用**自带绘图库 + Node 脚本**造**等距机械解剖图**（可交互、带几何审计），本篇把吉祥物做成**极简圆润 logo**（单文档技能 + 云模型）。可对照"**工程化机制图**"与"**品牌形象图**"两类视觉产物。

## 参考来源

- 仓库主页与 README（91 行；核验日期 2026-11-13）：https://github.com/s1dashu/ip-as-logo-skill
- 许可证：`LICENSE`（MIT，1,064 字符，Copyright (c) 2026 s1dashu；本地副本 `C:\Users\Administrator\AppData\Local\Temp\rgw2\s1dashu__ip-as-logo-skill.LICENSE`）
- 本地 README 副本：`C:\Users\Administrator\AppData\Local\Temp\rgw2\s1dashu__ip-as-logo-skill.README.md`
- 技能站点（README 引用，**未访问**）：<https://visualskills.dev/ip-as-logo>
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release / topics / 开放 Issue / 贡献者）：GitHub 页面内嵌数据 + ungh.cc + shields.io（2026-11-13）

以上信息核验于 2026-11-13。**未安装、未调用任何图像模型、未生成任何图片、未访问其站点**；能力与用法描述均来自 README，**未逐一验证**。
