# adu-motion-video：阿杜的 AI 剪辑模板技能与 MotionGit 开放格式

> 一句话定位（README 自述）：**"阿杜的 AI 剪辑 Skill"**——把**代码级动画模板**交给 AI 助手，用你自己的口播 / 文案 / 素材**替换内容、重排镜头、导出 60fps MP4 与可编辑工程**；模板本身是**给 AI 读的完整动画工程包**（"`.motion` 是给 AI 使用的完整动画模板包，不是提示词，也不是参考视频"）。最值得记的是三点：① **模板已从仓库迁到第三方站点 MotionGit**（`motiongit.com/@adunext`），仓库留的是**旧版 Skill、工具链、文档与效果示例**；② 它的产物是"**可继续改的工程**"而不是一次性成片（同一风格可复用到下一期）；③ **所有模板输出带统一角标**（右上角成片时间 + 动态 Circle Logo），属于品牌水印，**再发布前要确认**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（194 行）、`LICENSE`、根目录 `SKILL.md`（70 行 / 9,510 字符）、完整文件树与元数据；**未 clone、未安装依赖、未跑 `pipeline.py`、未下载任何 `.motion` 模板、未渲染过视频、未访问 MotionGit 站点**）
- 适用范围：**Windows / macOS / Linux**；面向 **Codex · Claude Code · Cursor · GitHub Copilot** 及"**具备文件和执行能力的豆包 · DeepSeek · WorkBuddy**"；制作依赖 **Node + Python + FFmpeg + Chrome**（`doctor` 检查、`setup` 安装）

## 核心结论

1. **明确定位**（README / `SKILL.md` 自述）：模板不是提示词也不是参考视频，而是**包含动画源码、画面布局、动作时间轴、转场与音效编排、可替换内容窗口、使用引导与运行说明的一个完整包**；助手读包后按你的内容**安排镜头、替换素材、生成预览，再导出 MP4 与可编辑工程**。
2. **分家的事实**：**全系列模板已升级为面向 AI 创作的 MotionGit `.motion` 开放格式**，**下载入口在第三方站点**（<https://motiongit.com/@adunext>），README 与 `SKILL.md` 都把用户导向那里；仓库里**保留的是历史风格示例**（"部分是局部或静音演示"）、旧版 Skill 工程与制作工具。
3. **一套可继续维护的生产流程**（`SKILL.md`）：`doctor`（查 Node / Python / FFmpeg / Chrome）→ `macro-plan`（按语义阶段与素材规划）→ `macro-build`（构建工程）→ `stills` / `audio` / `mix` / `render` / `check` / `preview`（抽帧抽查、混音、渲染、校验、带 SHA 绑定的预览发布）。

**个人判断（未验证）**：这个仓库的价值现在分两块——**工具链与制作规范**（仍在仓里，且有 `packs/` + `scripts/` 大量实现）与**模板分发**（已搬去 MotionGit）。要真正用起来，必须走第三方站点，**所以"站点是否长期可用、格式是否长期开放"就成了外部依赖**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [adunext/adu-motion-video](https://github.com/adunext/adu-motion-video) |
| 描述 | "阿杜的 AI 剪辑 Skill｜全系列模板已升级为 MotionGit .motion 开放格式，下载后直接发给助手即可创作。下载：https://motiongit.com/@adunext ｜精选阿杜与 Opus 5.5 制作的代码级动画模板，适合口播、知识讲解，支持多平台、多助手。" |
| 许可证 | **MIT**（`LICENSE` 1,063 字符；shields 报 MIT）；仓库另有 `THIRD_PARTY.md` 第三方说明 |
| 主语言 | **JavaScript 78.1%**（shields.io，2026-11-13） |
| 关注度 | **834 Star / 89 Fork / 5 Watcher**（**页面值**，2026-11-13）；开放 Issue **2**、贡献者 **1**（shields.io，同日）。**ungh 镜像 769 / 84 / 4，差异较明显（约 8%）** |
| 维护状态 | **2026-09-28 创建**；ungh `pushedAt` **2026-10-19**、页面 `updatedAt` 2026-11-08；`main`；**6 条 Release**——最新为**预发布** `3.14.1-rc.1`（2026-10-18）、`3.14.0-rc.1`（10-05）、`3.13.0-rc.2`（10-04），最新**稳定**为 `3.3.0`（2026-10-01） |
| 体积 | `main` 分支 **1,042 个条目**：`packs` 699、`scripts` 62、`tests` 61、`assets` 48、`references` 35、`adapters` 34、`examples` 33、`adaptation-profiles` 22、`docs` 20、`template` 14、`third-party` 3；根目录另有 `SKILL.md`、`AGENTS.md`、`THIRD_PARTY.md`、`package.json`、`requirements-runtime.txt`、`.nojekyll` |
| 站点 | 模板主页 <https://motiongit.com/@adunext> · 使用指南 <https://motiongit.com/use> · 格式规范 <https://motiongit.com/spec> · 示例预览页 `adunext.github.io/adu-motion-video/examples/gallery/` |

## 能力结构

### 1. 六类主风格（README）

| 编号 | 风格 | 说明（README 原文摘要） |
| --- | --- | --- |
| 01 | 现代图文口播 | 清爽文字、图解与真人窗口；节奏强调、连续形变、纵深舞台、杂志分屏、节拍字效 |
| 02 | 纸面透视讲解 | 纸张质感、透视卡片、天平与信息网络；适合选择对比、资料整理、流程说明 |
| 03 | 深色 3D | 暗色舞台、立体窗口、多屏展陈、镜头推进；适合产品演示、软件评测、作品集 |
| 04 | 深色科技控制台 | 粒子仪器台、玻璃面板与信息流；适合技术讲解、自动化流程、工具实测 |
| 05 | 鲜色角色叙事 | 鲜色便签、角色贴纸、物件接力、光环强调 |
| 06 | 活力色彩 | 纸白 / 红 / 蓝 / 荧光黄绿，大字冲击、回声标题、步骤归一 |

README 标注**横竖屏均支持**（06 的竖屏属"MotionGit 新版"）。`SKILL.md` 另给出**当前十一种风格、54 个完整组参与竖屏选型**、横屏 **10 风格 / 47 组**参与自动选型——**README 的"6 类"是历史示例分类，与 `SKILL.md` 的"11 种风格"不是同一口径**。

### 2. 工程包与脚本（文件树）

- **`packs/` 13 组模板包**（共 699 个文件）：`anim3` 10、`anim4` 13、`classic-performance` 52、`continuous-performance` 49、`dark-3d-showcase` 111、`doubao-console-performance` 62、`editorial-performance` 81、`kinetic-performance` 63、`paper-balance` 56、`paper-ball-performance` 64、`stage-performance` 48、`vibrant-color-performance` 31、`vivid-sticker-performance` 59。
- **`scripts/` 62 个**：`pipeline.py` / `pipeline.sh`（统一入口）、`doctor.py`、`plan_macro_project.py`、`build_macro_project.py`、`rematch_macro_project.py`、`adapt_project.py`、`render_project.mjs`、`face_tracking.py`（另有 `face_track.sh` / `face_track.swift`）、`font_policy.py`、`music_policy.py`、`color_management.py`、`srt` / 字幕相关（`build_subs.py`、`retime_subtitles.py`、`subtitle_position.py`）等。
- **`template/` 14 个**：运行时基座（`index.html` / `main.js` / `lib.js` / `scenes.js` / `config.js` / `style.css` / `subtitles.js` / `audio.py`）与**品牌角标资源** `template/brand/motiongit-circle-*.webm`、`template/brand/NOTICE.txt`、`template/output-signature.json`。
- **`references/` 35 个**文档：`rules.md`（制作约定）、`start-creating.md`、`template-catalog.md`、`adaptive-composition.md`、`automatic-selection.md`、`portrait.md`、`full-project-templating.md`、`local-rematch.md`、`handdrawn-animation.md`、`compatibility.md`、`color-and-preview.md`、`output-signature.md`、`lottie-assets.md` 等。
- **`adapters/` 34 个**：`doubao-console`、`paper-balance`、`paper-ball`、`vibrant-color`、`vivid-sticker`（含 `extract.py` / `bridge.js` / `inventory.mjs`）。
- **`docs/` 20 个 + `tests/` 61 个 + `examples/` 33 个**：能力矩阵（`capability-matrix.md`）、各版本质量升级与压力测试记录、验证 JSON。

### 3. 制作流程（`SKILL.md` 的六步）

1. `doctor` 检查 Node / Python / FFmpeg / Chrome；缺依赖且允许安装再 `setup`。
2. 锁定模板来源，对齐新口播与文案，**用真实时间码**（SRT 只有整句时间时"不可臆测句内某个词的时间"）。
3. 写 `brief.json` → `macro-plan 包ID@版本 brief.json 新规划目录`（产出 `spec.json` / `report.json` / `report.md` / `profile.json`；退出码 0 通过 / 2 待补或受阻 / 1 无效输入）。
4. `macro-build` 构建工程（**关键动作窗口锁原速**，长段落只伸展可停留区；时间不够就报错，**不靠全片线性拉伸**）。
5. `stills` 抽帧抽查（首帧、每个 cue 前中后、最长文字、场景接缝、片尾）、`audio` → `mix` → `render` → `check`。
6. `preview` 发布预览（绑定真实成片 SHA、显示播放版本）。

### 4. 助手与平台

同一份 Skill 面向 Windows / macOS / Linux，可由 **Codex、Claude Code、Cursor、GitHub Copilot**，以及"**可读写文件并运行命令的豆包 / DeepSeek / WorkBuddy**"使用；README 提醒"**助手名称相同，不同客户端的能力也可能不同**"。

## 使用前需要注意

- **模板本体不在这个仓库**：全系列模板已迁到 **MotionGit（第三方站点）**，README 里"下载最新 `.motion` 模板"指向 `motiongit.com/@adunext`。**仓库里的示例是历史版本、部分是局部或静音演示**；"新版完整示例及可下载版本以 MotionGit 主页为准"。**用之前先确认站点可用、格式与许可条款**。
- **输出带品牌角标**：README 明写"**旧版 Skill 的所有模板在官方静帧、视频预览及导出中，统一带右上角成片时间与动态 Circle Logo 交替**"，并链到 `references/output-signature.md`。**这意味着成片不是完全"干净"的**——再发布、商用或改品牌前请先确认是否能去掉。
- **很多能力被作者自己标为未验收**：`SKILL.md` 反复写"**最终效果待用户同视频 A/B 确认**""**能力仍为 adaptation-experimental**""静音新文案检查不代替新口播完整声画或独立复用验收""**机器检查仍为 experimental**"；当前默认版本号是 **`3.14.1-rc.1`（候选，不是稳定版）**。
- **依赖链较长且带浏览器**：需要 **Node + Python + FFmpeg + Chrome**；人脸跟踪在 **macOS 走 Vision、其它平台走本地 OpenCV**，检测不可用时"使用宽幅固定构图并提示复核"，文档明确"**不能把回退说成跟踪通过**"。
- **字体是"优先推荐"而非硬要求**：指定字体缺失 / 不匹配时会**自动使用随包 OFL 中英文字体并记录替换**；只有用户明确要求才用 `fontPolicy="exact"`。
- **单文件角标脚本与外部服务**：`scripts/` 里有 `chrome.mjs`、`render.mjs` 等直接驱动浏览器与渲染的脚本；README 提到旧版安装包要从 Release 下载（`v3.14.1-rc.1` 的 zip）。
- **贡献者仅 1 人**（shields.io），属**个人项目**；页面 Star 与 ungh 镜像差异约 8%，**引用数字时注明来源与日期**。
- **我只读了 README、`SKILL.md` 与文件树**：**未 clone、未装依赖、未跑任何脚本、未下载 `.motion`、未渲染视频、未访问 MotionGit**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想给**口播 / 知识讲解类视频**套一套现成的动效模板，并希望**导出可继续改的工程**（而不是一次性成片）时。
- 想看"**把动画工程打包成给 AI 读的模板包**"这种形态怎么设计（含 `manifest.json`、`role` / `cues` / `motionWindows` / `slots` 这类字段）时。
- 想看**横竖屏同一套模板的重排策略**（画幅 1080×1920、标题与人物层重排、控制台连接线按落点重绘）时。
- 想参考"**多个编码助手通用一个 Skill**"的写法（`SKILL.md` + `AGENTS.md` + 平台兼容说明）时。
- 想对照其它"AI 做视频"的路线：[OpenMontage](OpenMontage-把AI编码助手变成视频制作工作室.md)（agent-first 视频流水线）· [autoclip](autoclip-把一个链接剪成多平台短视频的桌面工具.md)（链接剪成短视频）· [srt-whiteboard-animation](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)（字幕驱动的白板动画）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"让编码助手直接产出视频"**：[OpenMontage：把 AI 编码助手变成视频制作工作室](OpenMontage-把AI编码助手变成视频制作工作室.md)——那篇是**从研究到成片的完整流水线**（11 条 pipeline、供应商打分、预算控制），本篇是**以动画模板为中心的制作包**（选风格 → 换素材 → 出 MP4 与工程）。可对照"**流水线自治**"与"**模板驱动的确定性风格**"。
- **同样是"链接 / 素材进、短视频出"**：[autoclip：把一个链接剪成多平台短视频的桌面工具](autoclip-把一个链接剪成多平台短视频的桌面工具.md)——那篇是**桌面应用**，本篇是**交给助手的模板 + 脚本**；一个偏"工具软件"，一个偏"Skill + 工程包"。
- **都用字幕当输入，产物不同**：[srt-whiteboard-animation：把 SRT 字幕做成白板手绘动画的技能](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)——那篇把字幕渲成**手绘白板动画**；本篇是**套模板的图文动效**。可对照"**单一视觉隐喻**"与"**多风格模板库**"。
- **同样"给编码助手一套视频技能"，组织方式不同**：[video-shotcraft：用 Remotion 做电影感产品宣传片的视频技能](video-shotcraft-用Remotion做电影感产品宣传片的视频技能.md)——那篇是**镜头配方卡 + Remotion 代码动画**（配方卡与实现都在仓库内），本篇是**动画模板工程包**（模板本体迁到站外 MotionGit）。可对照"**模板分发**"与"**镜头库 + 流水线**"。
- **同属"模板 / 风格驱动的视频工程"**：[lanshu-create-ai-presenter-video：从脚本到可验证 AI 数字人播报视频的技能](lanshu-create-ai-presenter-video-从脚本到可验证AI数字人播报视频的技能.md)——那篇把**十二种解说风格**做成 kits + starter 并带**八状态证据门**；本篇把动画工程打包成**给 AI 读的 `.motion` 模板**。一个偏"表演式解说引擎"，一个偏"动效模板库"。
- **产物都落在可继续编辑的剪映草稿**：[capcut-cli：直接读写剪映草稿的命令行剪辑工具](capcut-cli-直接读写剪映草稿的命令行剪辑工具.md)——那篇**直接读写剪映 / CapCut 的草稿 JSON** 做字幕、变速、长转短；本篇导出**可继续改的工程**。两者都强调"**产物是可编辑的、不是一次性成片**"，可对照各自的可编辑边界。
- **同属"给编码助手一套视频技能 / 流程"，形态更重**：[baocut：带可编辑时间轴的 AI 视频 Agent](baocut-带可编辑时间轴的AI视频Agent.md)——本篇是**Skill + 动画模板工程包**（偏"把编码助手变成制作者"），那篇是**带本地 Runtime 与可编辑时间轴的桌面应用 + Skill + MCP**（偏"Agent 剪辑、人复核"）；两者都强调产物可继续编辑。

## 参考来源

- 仓库主页与 README（194 行；核验日期 2026-11-13）：https://github.com/adunext/adu-motion-video
- 技能说明：根目录 `SKILL.md`（9,510 字符，`version: 3.14.1-rc.1`）与 `AGENTS.md`
- 许可证：`LICENSE`（MIT，1,063 字符）；`THIRD_PARTY.md`（第三方说明）
- 文档：`references/` 35 个（`rules.md` / `start-creating.md` / `template-catalog.md` / `adaptive-composition.md` / `automatic-selection.md` / `portrait.md` / `full-project-templating.md` / `local-rematch.md` / `handdrawn-animation.md` / `compatibility.md` / `color-and-preview.md` / `output-signature.md` / `lottie-assets.md` 等）；`docs/` 20 个（`capability-matrix.md`、`quality-upgrade-3.13.md`、`template-stress-3.9.md`、`template-auto-selection-3.10.md`、`output-signature-3.14.1.md` 等）
- 模板分发（**站外**）：<https://motiongit.com/@adunext> · <https://motiongit.com/use> · <https://motiongit.com/spec>
- Release 列表（6 条）：ungh.cc releases API（2026-11-13）
- 完整文件树（1,042 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**差异较明显**）；Issue 数 / 贡献者数 / 主语言取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未安装依赖、未运行任何脚本、未下载任何 `.motion` 模板、未渲染过视频、未访问 MotionGit 站点**；能力与流程描述均来自 README 与 `SKILL.md`，**未逐一验证**。
