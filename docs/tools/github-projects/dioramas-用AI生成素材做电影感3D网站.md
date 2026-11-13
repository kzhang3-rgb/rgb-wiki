# dioramas：用 AI 生成素材做"电影感"3D 网站

> 一句话定位（README 自述）："**A free, open-source framework for cinematic, interactive 3D websites — with AI-generated hero assets.**"——做**"3D 场景就是页面"**的落地页：**一个超精细物体 + 真实光照 + 编排好的相机运动 + 一个你能摸的交互**，并附 **20 个成品示例站**。**最值得记的三点**：① 它的**资产管线是"提示词 → 图 → 3D"**——[fal](https://fal.ai) 上的 **Nano Banana 2** 出白底产品图 → **Meshy 7.1** 图生 3D（6 万–25 万三角面、4K）→ 自写 `optimize.mjs` 焊接/压缩（meshopt）/转 WebP，把 **25 MB 压到 3–8 MB**；② **代码是 MIT，但生成的模型与图片是 CC BY 4.0**（另一份 `ASSET-LICENSES.md`），**是典型的"代码与素材分许可"**；③ **它是"单日投放"**——`pushedAt` 与 `createdAt` **同为 2026-09-30**，此后**再无代码推送**（约 1.5 个月），shields 的贡献者数还是 **0**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（99 行）、`LICENSE`、`ASSET-LICENSES.md`、`THIRD_PARTY_NOTICES.md` 存在性、完整文件树与元数据；**未 clone、未 `npm install`、未运行任一示例站、未申请 fal key、未做任何资产生成、未在浏览器里看过效果**）
- 适用范围：前端项目（Node + npm）。跑起来：`npm install` → `npm run dev`（→ `http://127.0.0.1:5190`，示例在 `/examples/<name>/`）。**模型文件随仓库走（纯 git，约 330 MB，不用 LFS）**；**建议有桌面 GPU**，一切按 **60 fps** 调优。仓库语言为 **JavaScript**。

## 核心结论

**一句话定位（README 自述）**：**免费的、开源的、做电影感交互 3D 网站**的框架；**没有付费层、没有账号、没有要装的 CLI**。

**它的三层结构（README 自述）：**

| 层 | 做什么 |
| --- | --- |
| **① 资产管线**（prompt → image → 3D） | **Nano Banana 2**（fal）出白底产品图 → **Meshy 7.1 image-to-3D**（fal）转成 **6 万–25 万三角面的 PBR 模型**（4K 几何，可选绑定骨骼 + 动画）→ `scripts/optimize.mjs` 焊接、meshopt 压缩、贴图转 WebP（**25 MB 原始模型 → 3–8 MB**） |
| **② 引擎**（`src/core/`） | 一个小 three.js 层，让生成模型"看起来很贵"：HDR 后处理栈（**N8AO 环境光遮蔽、mip-chain bloom、AgX tone mapping、颗粒、暗角**）、**raymarch 体积聚光灯**、**带粗糙度模糊的平面反射**、GPU 烘焙的程序化贴图、**Lenis + GSAP 滚动编排**、自定义光标、预加载与跨站导航 |
| **③ 方法**（brief → build → review → revise） | 每个示例始于**一页 brief**（`docs/briefs/`），由 **AI 编码 Agent 对着引擎搭出来**，再用**无头 GPU 截图批处理**（`scripts/review.mjs`）反复迭代、直到**每一个滚动停点都是一张构图完整的画面**。README 建议：**把一份 brief + `docs/ENGINE.md` 交给你的 Agent** 来搭自己的世界，或给示例换主题 |

**三个最值得记的点：**

1. **资产是"生成"的，不是"建模"的**：整个仓库的 3D 物件都来自"**提示词 → AI 图 → AI 3D**"。**每次生成用的提示词全部在仓库里**（`assets/jobs/` 与 `assets/manifest.json`）。**成本可查（README 自述）**：一个带贴图模型约 **$1.20**；整套合集（**65 个模型、111 张图**）约 **$90**。
2. **20 个示例站就是它的"作品集"**：例如 01 Reliquary（提灯逛暗博物馆、拍照点亮圣物）· 02 Abyssal（滚动 = 11 km 下潜、操纵探照灯）· 03 Hammer & Ember（滚动加热大马士革刀、点击溅火星）· 05 Still Water（真实涟漪的锦鲤池）· 06 Florae（20 万粒子花随你的风散开）· 12 Gambit（滚动重放"不朽对局"）· 15 Artemis（滚动让绑定骨骼的宇航员在月球行走）· 19 Nightshift Motor Co.（按住在雨中轰一台 café racer）……**它们是"一套方法是否成立"的实证**。
3. **许可分层很清楚**：**代码 MIT**（`Copyright (c) 2026 Blendi`）+ **生成资产 CC BY 4.0**（`ASSET-LICENSES.md`，署名 "Dioramas (github.com/blendi-remade/dioramas)"）+ **字体 SIL OFL**（Google Fonts）+ **第三方库各自许可**（`THIRD_PARTY_NOTICES.md`）。

**个人判断（未验证）**：它把"**AI 生成资产 + 手工调好的 WebGL 引擎 + 让 Agent 照着引擎去搭**"拼成了一条可复用的落地页生产线，**方法（brief→build→review→revise）比代码更有参考价值**。**但要注意三件事**：① **它是"单日投放"且此后再无代码推送**，贡献者数为 0——**更像是作者一次性的作品集/方法发布，而不是持续维护的框架**；② **要复刻资产必须自己掏 fal 的钱**（整套约 $90），且 `npm install` 拉一堆 WebGL 依赖；③ **330 MB 仓库、要桌面 GPU、按 60 fps 调优**——**不是给低端设备或移动端用的**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [blendi-remade/dioramas](https://github.com/blendi-remade/dioramas) |
| 描述 | "A free, open-source framework for cinematic, interactive 3D websites with AI-generated assets (Nano Banana 2 + Meshy 7.1 on fal). 20 example sites." |
| 归属 | 个人账号 `blendi-remade`（ungh `orgs` 查询非组织）；`isFork: false`、`isArchived: false` |
| 许可证 | **代码 MIT**（`LICENSE` 1,063 字符，`Copyright (c) 2026 Blendi`）；**生成的模型与图片为 CC BY 4.0**（`ASSET-LICENSES.md`，426 字符）；**字体 SIL OFL**；**第三方库各自许可**（`THIRD_PARTY_NOTICES.md`，372 字符） |
| 主语言 | **JavaScript 85.4%**（shields.io，2026-11-13） |
| 技术栈 | **three.js**（`src/core/`）· **N8AO / mip-chain bloom / AgX tone mapping** 等后处理 · **Lenis + GSAP**（滚动编排）· **meshopt** 与自写 `scripts/optimize*.mjs`（资产压缩）· `scripts/gen.mjs`（调 fal 生成）· `scripts/review.mjs`（无头 GPU 截图）· Vite 开发服务器（5190 端口）· 资产走 **fal**（Nano Banana 2 + Meshy 7.1） |
| 关注度 | **913 Star / 83 Fork / 4 Watcher**（**页面值**，2026-11-13）；**开放 Issue 0**、**贡献者 0**（shields.io，2026-11-13）。**ungh 镜像 892 / 82 / 4，略有差异，以页面值记录** |
| 维护状态 | **2026-09-30 创建**；ungh 报 `pushedAt` **2026-09-30（与创建同日，即之后无代码推送，距核验日约 1.5 个月）**；页面 `updatedAt` 2026-11-07（多为 Star/徽章刷新） |
| 默认分支 | `main` |
| 版本 | **0 Release / 0 tag**（页面 `sections.releases` 为 0） |
| 话题标签 | `3d` · `creative-coding` · `fal` · `generative-ai` · `landing-page` · `meshy` · `threejs` · `webgl` |
| 体积 | `main` 分支 **384 个条目**（ungh.cc files API）；顶层以 `public`（160，含 `models/`）、`examples`（141）、`docs`（34）、`assets`（15）、`scripts`（12）、`src`（12）为主；**模型随仓库走，约 330 MB** |

## 目录结构（README 自述）

```
examples/<name>/   the 20 example sites (index.html, main.js, style.css)
src/core/          the engine
src/index/         the gallery
scripts/           asset pipeline + QA (gen, optimize, shot, review, thumbs)
assets/jobs/       every prompt used to generate the assets
public/models/     optimized GLBs     docs/   engine guide, briefs, media
```

## 使用前需要注意

- **要复刻素材得自己花钱**：资产靠 **fal** 的 **Nano Banana 2 + Meshy 7.1** 生成，需 `FAL_KEY`（README 指示 `cp .env.example .env` 后填）。**一个带贴图模型约 $1.20，整套约 $90**（README 自述）。**仓库里已带好生成结果**，所以**只用示例不需要 key**，**要生成自己的资产才需要**。
- **代码与资产许可不同**：**代码 MIT，但生成的模型/图片是 CC BY 4.0——使用时必须署名**（"Dioramas (github.com/blendi-remade/dioramas)"）。**别把 CC BY 的资产当 MIT 用。** 另外，**作者声明 CC BY 4.0 的转授效力，仍取决于 fal 与各生成服务自身的条款是否允许**——**本笔记未核验各生成服务的条款**。
- **它是"单日投放"**：`pushedAt` 与 `createdAt` 同为 **2026-09-30**，此后**无代码推送**、**0 Release**、**shields 贡献者数 0**。**把它当"一次性发布的方法样本 / 作品集"更符合现实，而不是指望它持续迭代。**
- **仓库很重、硬件门槛高**：**约 330 MB**（模型随仓库走，不是 LFS）、**建议桌面 GPU**、**按 60 fps 调优**。**低端机与移动端的实际表现未验证，但按 README 的调优目标推断会很吃力。**
- **强依赖 WebGL/后处理**：N8AO、体积光、平面反射、GPU 烘焙贴图都是**吃 GPU** 的手段；**首屏体积与加载时间会明显高于普通落地页**（有预加载器与 gzip 但未量化）。
- **"AI 生成 + 无头截图迭代"的产物质量上限未知**：README 说每站都经过 art-direction review 直到"每个滚动停点都是完整构图"，**但这是作者自述**；**我没有看过任何一站的实际效果**。
- **我只读了文档**：**未 clone、未安装、未运行、未看过任一示例、未申请 fal key**；三层结构、成本数字、示例清单与许可说明**均来自 README 及相关许可文件**。

## 什么时候会再看

- 要做"**3D 场景即页面**"的落地页 / 作品集，需要一个**现成的 three.js 后处理与滚动编排骨架**时（`src/core/` 与 `docs/ENGINE.md`）。
- 想参考"**AI 生成资产 → 优化 → 上网页**"的完整管线（含"提示词全部入库"与"无头截图迭代"这两点做法）时。
- 想借鉴"**让 AI Agent 照着引擎搭站**"的 brief→build→review→revise 工作法时。
- 想对照"**代码与生成素材分许可**"的做法（MIT 代码 + CC BY 4.0 资产 + SIL OFL 字体 + 第三方声明）。
- 想对照另一条"**AI 生成 3D / 游戏素材**"的路线：[universal-modder](universal-modder-把改任何PC游戏做成一整套Agent技能.md)（同样用 fal 生成资产，产物是游戏 Mod）· [agent-sprite-forge](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)（生成 2D 精灵）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"用 AI 生成素材做成品"的工程化走向**：[OpenMontage：把 AI 编码助手变成视频制作工作室](OpenMontage-把AI编码助手变成视频制作工作室.md)——那篇做**视频**（agent-first 产线 + 治理装置）；本篇做 **3D 网页**。两篇都把"**提示词 + 管线 + 成本**"透明化，可对照。
- **同用 fal 生成资产，产物不同**：[universal-modder：把「改任何 PC 游戏」做成一整套 Agent 技能](universal-modder-把改任何PC游戏做成一整套Agent技能.md)——两篇都靠 **fal** 出美术/3D（本篇是"图 → 3D 模型 → WebGL 网站"，那篇是"图 → 3D 模型 → 游戏 Mod"）。可对照**同一条资产管线在不同产物上的复用**，以及**许可处理方式**（那篇不分发游戏资源，本篇明确"代码 MIT / 资产 CC BY 4.0"）。
- **同为"AI 生成游戏/网页素材"**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——那篇专攻 **2D 精灵**（单张/动画表），本篇是 **3D 资产 + 引擎**。可对照"**生成素材**"在 2D 与 3D 两条路上的工具链差异。
- **"由 AI 编码 Agent 搭出成品"的近邻**：[craft-anything：看清软件如何工作，再用 AI 重建自己的版本](craft-anything-看清软件如何工作再用AI重建自己的版本.md)——那篇用 AI 重建**已有软件**（clean-room），本篇用 AI **从 brief 搭新页面**（对着自己的引擎）。可对照"**把 AI 编码 Agent 当主力施工队**"的两种任务类型。

## 参考来源

- 仓库主页与 README（5,167 字符 / 99 行，核验日期 2026-11-13）：https://github.com/blendi-remade/dioramas
- 代码许可：`LICENSE`（MIT，1,063 字符，`Copyright (c) 2026 Blendi`）
- 资产许可：`ASSET-LICENSES.md`（426 字符，"3D models … images … released under **Creative Commons Attribution 4.0 (CC BY 4.0)**"，字体 SIL OFL）
- 第三方声明：`THIRD_PARTY_NOTICES.md`（372 字符）
- 引擎与 brief：`docs/ENGINE.md`、`docs/briefs/`（README 指向）
- 完整文件树（384 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档 / topics）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**与页面略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号经 ungh `orgs` 接口确认为个人账号

以上信息核验于 2026-11-13。**未 clone、未安装、未运行、未看过任一示例站、未申请 fal key、未核验各生成服务的转授条款**；三层结构、成本数字、示例清单与许可分层均来自 README、`ASSET-LICENSES.md` 与 `THIRD_PARTY_NOTICES.md`，"单日投放 / 未持续维护"的判断基于 ungh 的 `pushedAt`（2026-09-30）与 shields 的贡献者数（0）。
