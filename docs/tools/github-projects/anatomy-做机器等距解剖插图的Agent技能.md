# anatomy：做机器等距解剖插图的 Agent 技能

> 一句话定位（README 自述）：一个 **Claude Skill**——"**explains an idea by building it as a real, working object**"，把抽象概念**发明成一件真实的机械 / 仪器**，再用**几十上百个小而精确的立体零件**拼成**等距（isometric）插图**，配标题、提示与**实时读数**，并让它**随指针 / 键盘平静地动起来**。最值得记的三点：① **三档产物**——纯 **SVG**（主体）、**SVG + WebGL**（火焰 / 排气 / 水波 / 焦散 / 灯光，着色器画在前后两层 SVG 之间）、**3D（beta）**（真正在三维里转动的零件）；② **零依赖、零 Key**（`kit` 只用 Node 内置模块），装法是一行 `npx skills add wheresryan22/anatomy -g`，也支持 **Antigravity、OpenCode、Codex、MiniMax Code**；③ 它**会自我验收**——几何审计证明"**没有任何管道穿过任何东西**"，并用无头 Chrome 截图 / 逐关节特写 / 线条端点检查。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（332 行）、`LICENSE`（1,061 字符）与已核实元数据；**未 clone、未安装技能、未运行 `npx skills`、未执行任何 `build.mjs` / `--audit`、未启动无头 Chrome、未渲染任何图、未提交社区画廊**）
- 适用范围：面向 **Claude Code**（也加载于 **Antigravity / OpenCode / Codex / MiniMax Code**，遵循开放 **Agent Skills** 格式）；构建 / 检查需要 **Node.js ≥ 20.10**（**只用内置模块，无需 `npm install`**）与 **Chrome / Chromium**（无头、开 WebGL，用于自检脚本）；**React 输出**另需 **React 18+**

## 核心结论

1. **它的方法不是"画示意图"，而是"把概念造成一台机器"**（README 导语）：一个"什么都没变就跳过工作"的缓存键，被造成**只在所有弹子都对齐时才转动的弹子锁**；一个 GPU 上下文服务多张画布，被造成**一个打印头巡视 16 个槽的龙门绘图仪**。鸭子图上的**每个数字都来自驱动零件运动的同一个模型**。
2. **三类图共用一套 kit、一台相机、一套工艺规则**（README「What it makes」）：**SVG**（核心，四色立体着色、从后往前画、卡片含实时读数与缓动）、**SVG + WebGL**（接线画不出的东西：火 / 排气 / 水 / 焦散 / 灯光 / 辉光 / 蒸汽，着色器跑在前后 SVG 之间的 canvas，且**没有 WebGL 时 SVG 仍可读**）、**3D（beta）**（拨盘 / 转台 / 手指 / 脱落零件，按 kit 自己的规则**逐帧重投影重着色**）。
3. **产物是"归你所有的源码"**（README「Usage」）：一张图会以**你拥有的源码**交付——一个 Node 构建脚本 + 它写出的页面，或（React）几何模块 + 一个静态图服务端组件 + 一个运动客户端组件；**kit 随图附带**。
4. **它刻意慢、还自带"批评家"式验收**（README「Limits」/「How it works」）：一张图是**一长段会话**（列零件、建几何、审计、在两种主题下逐关节特写）；`kit/audit.mjs` 证明几何、`scripts/capture.mjs` / `drive.mjs` 截图与慢放接触表、`lines.mjs` 让**悬空的线**失败、`turn-check.mjs` 检查 3D 图。

**个人判断（未验证）**：这个仓库最"重"的是 **`kit/` 绘图库 + 审计体系**，而**不是**提示词。它的价值不在"生成一张好看的图"，而在**把"等距机械图"工程化**（相机、车削件、管线、着色器、几何审计、端点检查）——**想复用这套方法，得连 kit 一起用**；也正因如此，它的**产物上限取决于模型是否愿意按这套规矩反复迭代**（README 明说"一张图要一个长会话"）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [wheresryan22/anatomy](https://github.com/wheresryan22/anatomy) |
| 描述（原文） | "An agent skill for creating detailed, interactive isometric illustrations of real-world machines and complex systems. Generates polished SVGs with optional WebGL shaders and 3D effects." |
| 归属 | 个人账号 **wheresryan22**（`LICENSE` 版权人写 "Ryan"，X：[@wheresryan22](https://x.com/wheresryan22)） |
| 许可证 | **MIT**（`LICENSE` 1,061 字符；Copyright (c) 2026 Ryan） |
| 形态 | 一个标准 **Agent Skill**：**本仓库根目录就是那个技能文件夹**（根下有 `SKILL.md`）；面向 **Claude Code**，也加载于 **Antigravity / OpenCode / Codex / MiniMax Code** |
| 依赖 | **零 npm 依赖**（只用 Node 内置模块）；需 **Node.js ≥ 20.10**；自检需 **Chrome / Chromium**；React 输出需 **React 18+** |
| 关注度 | **Star 26,764 / Watcher 72 / Fork 2,251**（**ungh 值**）；**页面值 27,016 / 72 / 2,278**（2026-11-13，**两口径略有差异**）；shields 另报**开放 Issue 16、contributors 13**（同日） |
| Release | ungh **2** / 页面 **2**（两口径一致） |
| 维护状态 | **2026-10-08 创建**；ungh `pushedAt` **2026-10-18**、页面 `updatedAt` **2026-11-12**；默认分支 **`main`**；`CI` 徽章指向 `.github/workflows/ci.yml` |
| 官网 / 画廊 | <https://skills.wheresryan.sh/anatomy>；社区画廊 [anatomy-community](https://github.com/wheresryan22/anatomy-community) |

## 能力结构

### 1. 三类产物（README「What it makes」）

| 种类 | 是什么 | 例子 |
| --- | --- | --- |
| **SVG** | 核心。四色着色的立体等距零件，从后往前画，带实时读数与缓动；可出**独立 HTML 页**、**内嵌样式的单个 `.svg`**，或 **React / Next.js 组件** | `dial-indicator`、`desk-computer`、`arcade-cabinet`、`test-rig` |
| **SVG + WebGL** | 画线画不出的东西：火 / 排气 / 水 / 焦散 / 灯光 / 辉光 / 蒸汽；着色器在前后两层 SVG 之间、用图自己的等距相机，**没有 WebGL 时 SVG 仍可读** | `raptor-engine`、`ripple-tank` |
| **3D（beta）** | 真正三维里转动的零件：旋转拨盘 / 转台、屈伸手指、脱落零件；**按 kit 自己的规则逐帧重投影重着色**，其余部分仍是静态图 | `turning-dial` |

每张图分**浅色 / 深色**主题，**浅色是技能首先设计的主题**（含着色器）。

### 2. 工程结构（README「How it works」+ 仓库布局）

```text
anatomy/
├── SKILL.md                  the skill: workflow, setup and craft rules
├── kit/                      the drawing library
│   ├── iso-kit.ts / .mjs     camera, solids, parts, SVG renderer, card, page, CSS
│   ├── lathe.mjs             round parts on any axis
│   ├── tube.mjs              pipes, tubes and cables
│   ├── audit.mjs             the geometry audit
│   ├── gl.mjs                WebGL layer for shaders inside the drawing
│   ├── turn.mjs / turn-build.mjs / turn-audit.mjs   3D runtime / builder / orbit audit
│   └── react/                draw.tsx (server-safe), live.tsx (client hooks)
├── references/               craft.md kit.md motion.md react.md verify.md webgl.md walkthrough.md 3d.md
├── scripts/                  capture.mjs drive.mjs lines.mjs inline-kit.mjs turn-check.mjs turn-bench.mjs
├── examples/                 eight complete figures, built pages committed
├── evals/evals.json          test prompts and what a good answer contains
└── .github/                  issue forms, PR template, CI, README images
```

- **`SKILL.md`** 设定工作流：**先收集真实数字 → 发明物件 → 写零件清单 → 规划世界 → 用 kit 一次性建几何 → 从后往前画 → 加框 → 做活 → 像批评家一样验收**。
- **`kit/`** 是绘图库（等距相机、实体与零件库、任意轴车削件、管线 / 缆线、WebGL 层、3D 运行时、React 组件）；除 React 组件外**无依赖**。
- **`references/`** 是 Claude 按需打开的长文指导（工艺、kit API、运动、React、验证、WebGL、设计走查、3D）。

### 3. 安装与使用（README「Install」/「Usage」）

```sh
npx skills add wheresryan22/anatomy -g            # 交互问装在哪些 agent
npx skills add wheresryan22/anatomy -g -a claude-code   # 跳过提问
```

```text
/anatomy explain how a token bucket rate limiter works, interactive
```

- 位置：Claude Code → `~/.claude/skills/anatomy`；Antigravity → `~/.gemini/antigravity/skills/anatomy`；OpenCode → `~/.config/opencode/skills/anatomy`；Codex → `~/.agents/skills/anatomy`；MiniMax Code → `~/.minimax/skills/anatomy`。也可 `git clone` 到对应目录，或下载 ZIP 改名 `anatomy` 放进去。**`skills` CLI 会整仓复制（约 10 MB，含 examples）**。
- 调用：Claude Code / Antigravity / OpenCode / MiniMax Code 用 `/anatomy`，**Codex 用 `$anatomy`**；**不必点名技能**——你要"等距插图 / 文档解说图 / 交互式讲解"时它也会被加载。
- 三个好习惯：**说清"要讲的概念 + 真实数字"**（尺寸 / 数量 / 速率 / 阈值）、**或直接点名物件与要动的地方**、**说清它住哪**（独立 HTML / 单个 SVG / React 组件，浅色或深色）。

### 4. 示例（README「Examples」）

`dial-indicator`（最小，约 300 行）、`desk-computer`（1984 风格台式机，约 85 个实体）、`arcade-cabinet`（约 240 实体 + 15 线缆）、`raptor-engine`（Raptor 2 试车台，拆成 11 个组件，含 WebGL 焰流，**构建要几分钟**）、`ripple-tank`（水波槽，两层 WebGL 由纯模型驱动）、`test-rig` / `spot-plate`（React 站产图，无构建脚本，直接拷进 React 应用）、`turning-dial`（3D 模式，含 380 零件压力手用于测性能）。每张框架无关的图都有 **Node 构建脚本**，加 `--light` 出浅色主题；Raptor 与街机柜还能**打印**（`scripts/print.mjs` 生成三份 A4 PDF）。

## 使用前需要注意

- **它不是图表库**（README「Limits」）：**没有坐标轴 / 系列 / 数据绘图**；数字只以**实时读数**和**零件的真实尺寸与数量**出现。**它画机器、仪器、工具与家具，不画人、不做精确地图**；"**没有可造机制的抽象概念会产出偏弱**"。
- **它故意很慢**（README「Limits」）：一张图是**一长段会话**（零件清单、几何、审计、两种主题下逐关节特写）；**Raptor 构建要几分钟**，一次完整 3D 轨道审计（30 个零件）**要 15–20 分钟**。**别指望一句话秒出图**。
- **着色器依赖 WebGL、3D 仍是 beta**（README「Limits」）：没有 WebGL 时 **SVG 仍可读**，且**开启"减少动态"时所有图静止**；**3D 模式仅在它的示例与一张测试图上验证过，还没在生产图上验证**——380 零件压力手在 1440 px 宽下约 **20 fps**，限流手机档约 **13–15 fps**（重度负载机器上测得）。
- **自检会启无头 Chrome，Codex 沙箱可能挡住**（README 折叠块）：技能通过启动无头 Chrome（写临时 profile、走本地端口）自检；**Codex 沙箱可能拦住它**，此时**图仍会建好，只是检查失败**。README 给了三种办法：对该命令批准沙箱外运行（需 `approval_policy = "on-request"`）、在 `~/.codex/config.toml` 放行、或在普通终端自己跑 `node scripts/capture.mjs page.html --out ...`。**用 `--sandbox danger-full-access` 能过但会解除该会话所有限制**，只对可信项目用。
- **模型依赖与产物许可**（事实 + 提示）：产物是**你拥有的源码**（Node 构建脚本 + 页面 / React 组件），**kit 随图附带**且 **MIT**——即**生成的图不受本仓库许可约束之外的限制**，但**成品质量取决于所用 agent 与模型是否按 `SKILL.md` 的流程反复迭代**（README 强调"先收集真实数字""像批评家一样验收"）。**个人判断（未验证）**：若模型跳过审计 / 特写，产出可能"看着像"但几何经不起推敲。
- **依赖与版本边界**：需 **Node ≥ 20.10**（Node 20 上浏览器脚本会以 `--experimental-websocket` 重启自身；examples 在 **Node 20.19 与 22.11** 上逐字节重建过）；自检找 Chrome / Chromium 的常见位置与 Playwright 缓存，可用 `CHROME_PATH` 指定其它二进制；**`skills` CLI 1.7.1 起要 Node 22.20+**。
- **社区画廊是外部提交**：可通过 agent 把图"publish to the Anatomy community gallery"（它会先展示将发送的内容，经你同意后**向 [anatomy-community](https://github.com/wheresryan22/anatomy-community) 开 PR**），或自己跑 `scripts/publish.mjs --submit`。**提交前先确认要公开的内容**。
- **元数据有两个口径**：Star / Fork 存在 **ungh（26,764 / 2,251）与页面（27,016 / 2,278）** 两个值；开放 Issue（16）与 contributors（13）取自 shields.io。**引用时注明来源与日期**。
- **我只读了 README、`LICENSE` 与已核实元数据**：**未 clone、未安装技能、未运行 `npx skills`、未执行任何 `build.mjs` / `--audit`、未启动无头 Chrome、未渲染任何图、未提交社区画廊**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想给**文档 / 博客 / 落地页**做一张**等距机械图**来解释技术概念（缓存、限流、连接池、CDN 边缘缓存……）时。
- 想让**一张"能动的解说图"**同时具备**实时读数 + 缓动**（指针 / 键盘驱动），而不是静态插画时。
- 想复用它那套**工艺与验收**（四色立体着色、从后往前画、几何审计"无穿透"、线条端点检查、逐关节特写）时。
- 想对比"**用代码 / SVG 生成插画**"的不同路线（见下方互链）时。
- 想研究"**一个 Agent Skill 如何自带绘图库 + 自检脚本 + 示例 + evals**"这种打包方式时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都是"Agent 生成 SVG 图表"的技能**：[svg-diagram：手写 SVG 图表的 Agent 技能与 linter](svg-diagram-手写SVG图表的Agent技能与linter.md)——那篇偏**手写 SVG 做常规图表并带 linter**，本篇**用绘图库造等距机械实体图并做几何审计**。可对照"**一般图表**"与"**机制插图**"。
- **都用 Node 脚本 / 代码产出视觉资产**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——那篇生成**2D 游戏精灵**，本篇生成**等距机械解剖图**；[ip-as-logo-skill：把 IP 吉祥物做成极简圆润 logo 的 Agent 技能](ip-as-logo-skill-把IP吉祥物做成极简圆润logo的Agent技能.md) 则把吉祥物做成**极简 logo**。三者可对照"**精灵 / logo / 机制图**"三种视觉产物。
- **同属"用 AI 素材做 3D 感视觉"**：[dioramas：用 AI 生成素材做电影感 3D 网站](dioramas-用AI生成素材做电影感3D网站.md)——那篇是**电影感 3D 网站**（偏生成素材 + 网站），本篇是**代码生成的等距技术插图**（偏工程化几何与自检）。可对照"**观感驱动**"与"**结构驱动**"。
- **都是"用代码画动态解说"**：[manim：数学动画引擎](manim-数学动画引擎.md)——那篇是**数学动画引擎**（场景 / 对象 / 动画），本篇是**等距机械插图技能**（可交互、带读数）。可对照"**数学讲解动画**"与"**机械解说插图**"。

## 参考来源

- 仓库主页与 README（332 行；核验日期 2026-11-13）：https://github.com/wheresryan22/anatomy
- 官网 / 画廊：<https://skills.wheresryan.sh/anatomy>；社区画廊 <https://github.com/wheresryan22/anatomy-community>
- 许可证：`LICENSE`（MIT，1,061 字符；Copyright (c) 2026 Ryan；核验日期 2026-11-13）
- 技能与库：`SKILL.md`、`kit/`（`iso-kit.ts` / `iso-kit.mjs` / `lathe.mjs` / `tube.mjs` / `audit.mjs` / `gl.mjs` / `turn*.mjs` / `react/`）、`references/`（`craft.md` / `kit.md` / `motion.md` / `react.md` / `verify.md` / `webgl.md` / `walkthrough.md` / `3d.md`）
- 自检与示例：`scripts/`（`capture.mjs` / `drive.mjs` / `lines.mjs` / `inline-kit.mjs` / `turn-check.mjs` / `turn-bench.mjs` / `print.mjs`）、`examples/`（`dial-indicator` / `desk-computer` / `arcade-cabinet` / `raptor-engine` / `ripple-tank` / `test-rig` / `spot-plate` / `turning-dial`）、`evals/evals.json`
- 安装器：<https://github.com/vercel-labs/skills>（`skills` CLI）
- 元数据（Star / Watcher / Fork / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc 交叉核对（2026-11-13，**页面值与镜像值略有差异**）；开放 Issue 数 / contributors 取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未安装技能、未运行 `npx skills`、未执行任何构建或 `--audit`、未启动无头 Chrome、未渲染任何图、未提交社区画廊**；能力与流程描述均来自 README 与 `LICENSE`，**未逐一验证**。
