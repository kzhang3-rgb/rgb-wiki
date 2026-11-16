# lanshu-create-ai-presenter-video：从脚本到可验证 AI 数字人播报视频的技能

> README 自述：这是一个 **Agent Skill**——把你**已有的一段主题或脚本**（可选一张**有授权的真人肖像**）交给你的编码助手，由助手走完**脚本 → 配音 → 数字人播报 → 唇形同步 → 字幕与关键词动效 → 剪辑 → 渲染 → 质检**的全流程，产物是**经过解码与响度校验的可发布成片**。最值得记的是三点：① 它有**两条路线**——"数字人播报（presenter）"与"无数字人的风格化解说（styled explainer）"，后者内置**十二种表演风格**；② 它把"**不可交付**"写进流程——**八个制作状态全靠磁盘上的产物算出来**（`check_state.py`），"**输出未通过解码与响度检查前不交付**"；③ 它自称 **Harness-neutral / Provider-neutral**（同一份 `SKILL.md` 适配多种助手，配音/数字人/唇形按能力在运行时选型并记录实际用的提供方与模型）。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（339 行）、`LICENSE`（1,063 字符）、README 中的 Highlights / Two routes / How it works / Scripts / Defaults / Safety 等章节，以及已核实元数据；**未 clone、未安装、未运行任何脚本、未调用任何语音 / 数字人 / 唇形 / MiniMax 服务、未渲染任何视频**）
- 适用范围：面向 **Agent Skills 客户端**，README 点名 **Claude Code · Codex · Gemini CLI · Cursor · OpenCode · GitHub Copilot**，"任何能读文件并运行 shell 命令的助手都可直接照做"；运行需 **Python 3.9+、FFmpeg（含 `ffprobe`）、Bash、`jq`、`awk`、`sed`**；presenter 路线另需**语音合成 + 数字人视频生成 + 唇形同步**能力（云端 CLI / API / 本地模型任选，可选 HyperFrames 做确定性合成），styled 路线另需 **Node.js、`rsync`、带 `numpy` 的 Python 与 `MINIMAX_API_KEY`**（或自备配音）

## 核心结论

1. **两条路线，产物形态不同**（README）：

   | 路线 | 成片是什么 | 你要提供 | 付费生成 | 画幅 |
   | --- | --- | --- | --- | --- |
   | **Presenter** | 数字人出镜播报，字幕与关键词图形辅助 | 主题或脚本 + **有授权的真人肖像** | 语音 + 数字人视频 | 任意，默认 **9:16** |
   | **Styled explainer** | 无数字人；每句narration在屏幕上"演"出来（十二种风格之一） | 主题或脚本（或你自录的旁白） | 仅语音（自备旁白则无） | **16:9** |

2. **十二种表达风格，一套统一契约**（README）：**九种视觉风格**——editorial（社论纸面）、tech HUD、notebook、paper pop-up、pop comic、3D one-take、drafting sheet、chalkboard、clay town；**三种叙事风格**——**推演**（一张画布上推导，公式与几何用颜色绑定）、**案卷**（证据板上查案，从异常到结案）、**小剧场**（生活情景喜剧，从误区/账单/排队引出成因，再用一句话把类比映射回真实术语）。所有风格共享同一套"词级时间码字幕 + 章节条 + 表演式结尾句 + 一帧回顾"。
3. **"证据门"是这套 Skill 的骨架**（README）：从 `intake` 到 `verified` 共**八个状态**，每个状态由**作业目录里的产物**判定，而不是靠人声明；`check_state.py` 会**在记录的进度超过实际产物时报错退出**。

   | 状态 | 需要的证据 |
   | --- | --- |
   | `intake` | 作业已创建 |
   | `content_locked` | 通过预检的报告、脚本、beat sheet |
   | `audio_locked` | 可解码的最终旁白、ASR 报告 |
   | `visual_plan_locked` | 时间轴、分镜、已批准的方案 |
   | `presenter_generated` | 已记录的数字人能力、选中的视频、画面审查 |
   | `composition_checked` | 合成报告 |
   | `rendered` | 含视频与音频的可解码渲染 |
   | `verified` | 母版、分享版、以及输出响度通过的交付报告 |

4. **成本与安全护栏写在流程里**（README）：上传前先确认**肖像权与"成年出镜人"**；**声音克隆必须显式授权，且不从图片反推声音**；**首次付费调用前**先说明上传内容、请求秒数、已知价格、试跑规模与重试上限；**被中断的远程任务先按保存的 task ID 轮询再重提**，避免重复计费；**连续三次付费候选被拒后停止**并总结复发原因。

**个人判断（未验证）**：这条音视频流水线的"**以旁白为母时钟（audio-locked timeline）**"是它区别于"先剪画面再配音"的关键设计——旁白一旦锁定，数字人动作、字幕、切点与总时长都以它为准，唇形与场景边界才不容易漂。真正用起来的主要门槛是**依赖链长**（Python + FFmpeg + Bash + `jq` 等，styled 路线再加 Node + MiniMax），以及 presenter 路线需要你自己准备**合规的肖像与三项生成能力**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [cclank/lanshu-create-ai-presenter-video](https://github.com/cclank/lanshu-create-ai-presenter-video) |
| 描述 | "Provider-neutral Codex Skill for producing verified AI presenter videos from a script and an authorized presenter image."（GitHub 描述**原文**） |
| 许可证 | **MIT**（`LICENSE` 1,063 字符，Copyright (c) 2026 lanshu；与 GitHub 页面 `spdxId` = MIT 一致） |
| 作者 | README 署名 **岚叔（Lanshu）**，X [@LufzzLiz](https://x.com/LufzzLiz)；`LICENSE` 版权人为 **lanshu** |
| 关注度 | **页面 2,974 Star / 10 Watcher / 445 Fork**；ungh 镜像 **2,938 / 10 / 443**；shields stars 3k、forks 445、**contributors 2**（2026-11-13） |
| 维护状态 | **2026-08-20 创建**；ungh `pushedAt` **2026-10-11**、页面 `updatedAt` 2026-11-09；默认分支 `main`；**Release 1**（`tagCount` 1）；**开放 Issue 数未见**（shields issues 返回 invalid） |
| topics | ai-video、codex、codex-skill、digital-human、video-generation |
| 形态 | 一个 **Agent Skill**：仓库根即技能（根 `SKILL.md`）。据 README "Repository layout" 章节：`scripts/`（作业创建、门禁、分段规划、交付）、`references/`（按阶段**按需加载**的五份指南）、`explainer/`（十二风格引擎：`kits/`、`starter/`、`tools/`、`examples/kv-cache/`）、`tests/`（`smoke.sh`、`explainer_smoke.sh`） |

## 功能与用法

### 1. 两条路线的流程（README "How it works"）

- **Presenter 路线**：`主题或脚本 + 授权肖像` → **锁定脚本与完整旁白**（旁白成为**母时钟**）→ **低成本数字人试跑** → 数字人生成（**提供方有单次时长上限时，按真实停顿切分**）→ 音频驱动的剪辑（字幕、关键词图形、封面、结尾）→ 技术与画面质检 → 输出版本 / 分享版 / 联系表 / 交付报告。
- **Styled 路线**：`主题或脚本` → 选风格（菜单 + 画廊，或在九种视觉风格里同时出草稿）→ **按风格写脚本** → **带词级时间戳的配音**（MiniMax 或你自备的旁白）→ 从风格 starter 出**可播放草稿** → 分镜 → **每一句在屏幕上按词演出** → 质检（check、静止检测、封面）→ 渲染 → 输出版本 / 分享版 / 字幕 / 交付报告。

### 2. 脚本与工具（README "Scripts" / "Explainer tools"）

| 脚本 | 作用 |
| --- | --- |
| `init_job.py` | 建**自包含**作业目录，把输入拷进去并记录作业相对路径 |
| `preflight.py` | 校验输入、人工复核与批准；**区分本地错误与远程生成阻塞** |
| `plan_segments.py` | 提供方对单次请求或参考音长有限制时，**按真实 ASR 停顿切分已锁定的旁白** |
| `check_state.py` | 从产物**计算**制作状态；记录的进度超过实际产物时**非零退出** |
| `finalize_delivery.sh` | 生成母版与分享版，**校验全片解码、交付响度、黑帧/冻帧**，再连同联系表和报告发布 |

`explainer/tools/` 下另有 `story.py`（`script.md` → MiniMax 逐句配音 + 词时间戳 → `story.json`；`--audio` 自备旁白、`--dry` 静音草稿、`--check` 预估时长）、`new_film.sh` / `new_topic.sh`、`docs.py`、`qa.py`、`render.sh`（`DRAFT=1` 出 720p 手机预览版）、`srt.py`、`still_grid.py` 等。

### 3. 安装与默认值（README "Installation" / "Defaults"）

- 仓库目录即技能，**文件夹名必须保持 `lanshu-create-ai-presenter-video`**（Agent Skills 规范要求与技能 `name` 一致）。示例：`git clone <仓库> ~/.claude/skills/lanshu-create-ai-presenter-video`；Claude Code 用 `/lanshu-create-ai-presenter-video` 显式调用，Codex 用 `$lanshu-create-ai-presenter-video`。不支持 Agent Skills 的助手可 `Read <路径>/SKILL.md and follow its workflow exactly.`。
- 默认：Presenter **9:16 / 1080×1920 / 30fps**，styled **16:9 / 1920×1080 / 30fps**；主题默认生成 **45–75 秒**脚本（自带脚本保持自然长度）；默认结构 **Hook + 2–4 个内容 beat + 精简收尾**；音乐与 CTA **默认关闭**；响度 **−16 LUFS ±0.5 LU，真峰 ≤ −1 dBTP**（可用 `PROGRAM_LUFS` 覆盖）。

## 使用前需要注意

- **GitHub 描述说"Codex Skill"，README 说 harness-neutral**：描述原文写的是 "**Provider-neutral Codex Skill**"，但 README 的 Highlights 第一条是 "**Harness-neutral**"，并列出 Claude Code、Codex、Gemini CLI、Cursor、OpenCode、GitHub Copilot 等 **Agent Skills 客户端**。**"Codex Skill" 更像是导入期的定位，实际按通用 `SKILL.md` 走**——引用时以 README 为准。
- **Provider-neutral 不等于"自带模型"**：presenter 路线需要你自己接**语音合成、数字人视频生成、唇形同步**三类能力（"云端 CLI、一个 API、或本地模型"），并**记录每个作业实际用的提供方、模型、参数与 task ID**；styled 路线要 **MiniMax API key**（或自备旁白）。**能力从哪来、要花多少钱，取决于你接入的提供方。**
- **会花钱，但作者做了护栏**：首次付费调用前会给出上传内容、请求秒数、已知价格、试跑规模与重试上限；中断任务先按 task ID 轮询；连续三次付费候选被拒即停。**这些是"文档承诺的护栏"，而非对费用上限的硬保证**——实际计费仍取决于你所接的提供方。
- **合规前置是硬门槛**：肖像权与"成年出镜人"在上传前确认；**声音克隆需显式授权，且不从图片反推声音**。**拿真人肖像做数字人播报涉及肖像/人格权，请自行确认授权范围**。
- **依赖链较长**：基础需 **Python 3.9+、FFmpeg（含 `ffprobe`）、Bash、`jq`、`awk`、`sed`**；styled 路线另需 **Node.js（HyperFrames 走 `npx`）、`rsync`、带 `numpy` 的 Python、`MINIMAX_API_KEY`**。**Windows 原生环境下的 Bash 依赖需自行解决**（README 未点名 Windows）。
- **十二种风格是"README 自述"**：README 列出九种视觉 + 三种叙事风格，并给出同一 KV cache 主题在各风格下的成品图；**我未下载样例、未渲染，风格实际观感与成品图一致性未验证**。
- **仓库自带的测试是"合成媒体冒烟测试"**：`smoke.sh` 用**合成素材**从 `intake` 走到 `verified` 且**不调用任何远程服务**；CI 在每次 push / PR 跑元数据校验、可移植性检查、语法检查与两个冒烟测试。**这验证的是流程骨架，不是真实生成质量。**
- **元数据口径差异**：页面 Star **2,974** 与 ungh 镜像 **2,938** 有约 1% 差异；**引用数字时注明来源与日期**。**贡献者仅 2 人**。
- **我只读了 README 与 `LICENSE`**：**未 clone、未装依赖、未跑任何脚本、未调用任何语音 / 数字人 / 唇形 / MiniMax 服务、未渲染视频**；README 提及 `README.zh-CN.md`、`CHANGELOG.md`、`explainer/THIRD_PARTY.md`、`references/*.md` 等文件，**我未逐一读取，其内容未核实**。

## 什么时候会再看

- 想给**一段脚本或一个主题**落成**数字人播报视频**，并希望流程**自带"未通过检查不交付"的把关**时。
- 想参考"**把一次生成作业拆成八个由产物判定的状态**"这种可审计工作流怎么设计（`job.json` + `check_state.py`）时。
- 想做**无数字人的解说视频**，需要**十二种可复用风格**（各含 kits / starter / 示例）时。
- 想看"**Provider-neutral / Harness-neutral 的 Agent Skill**"怎么写（运行时按能力选型 + 记录实际提供方与参数）时。
- 想对照其它"AI 做视频"的路线：[OpenMontage](OpenMontage-把AI编码助手变成视频制作工作室.md)（agent-first 视频流水线）· [adu-motion-video](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)（以剪辑模板为中心）· [srt-whiteboard-animation](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)（字幕驱动的白板动画）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"让编码助手直接产出视频"**：[OpenMontage：把 AI 编码助手变成视频制作工作室](OpenMontage-把AI编码助手变成视频制作工作室.md)——那篇是**从研究到成片的自治流水线**（多 pipeline、供应商打分、预算控制），本篇是**以"旁白为母时钟 + 数字人/风格化表演"为中心的制作链**，并有**八状态证据门**。可对照"**流水线自治**"与"**证据驱动交付**"。
- **同样是"模板/风格驱动的视频工程"**：[adu-motion-video：阿杜的 AI 剪辑模板技能与 MotionGit 开放格式](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)——那篇把动画工程打包成**给 AI 读的 `.motion` 模板**，本篇把**十二种解说风格**做成 kits + starter。一个偏"动效模板库"，一个偏"表演式解说引擎"。
- **都用字幕/旁白当输入，产物不同**：[srt-whiteboard-animation：把 SRT 字幕做成白板手绘动画的技能](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)——那篇把字幕渲成**单一隐喻的白板动画**；本篇在 styled 路线里让**每一句按词演出来**（十二风格）。可对照"**单一视觉隐喻**"与"**多风格库 + 词级时间轴**"。
- **同属"给编码助手装生成能力的 Skill"**：[ip-as-logo-skill：把 IP 吉祥物做成极简圆润 Logo 的 Agent 技能](ip-as-logo-skill-把IP吉祥物做成极简圆润logo的Agent技能.md)——那篇是**单文件的图像生成指令型技能**，本篇是**多脚本、多阶段、带证据门的视频制作技能**。可对照"**单文档技能**"与"**工程化技能**"两种形态。

## 参考来源

- 仓库主页与 README（339 行；核验日期 2026-11-13）：https://github.com/cclank/lanshu-create-ai-presenter-video
- 许可证：`LICENSE`（MIT，1,063 字符，Copyright (c) 2026 lanshu；本地副本 `C:\Users\Administrator\AppData\Local\Temp\rgw2\cclank__lanshu-create-ai-presenter-video.LICENSE`）
- 本地 README 副本：`C:\Users\Administrator\AppData\Local\Temp\rgw2\cclank__lanshu-create-ai-presenter-video.README.md`
- README 内引用但**未核实**的文件：`README.zh-CN.md`、`CHANGELOG.md`、`explainer/STYLES.md`、`explainer/KITS.md`、`explainer/GOTCHAS.md`、`explainer/THIRD_PARTY.md`、`references/generation.md`、`references/editing.md`、`references/qa-recovery.md`、`references/styled-explainer.md`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release / topics）：GitHub 页面内嵌数据 + ungh.cc + shields.io（2026-11-13）

以上信息核验于 2026-11-13。**未 clone、未安装依赖、未运行任何脚本、未调用任何语音 / 数字人 / 唇形 / MiniMax 服务、未渲染任何视频**；能力与流程描述均来自 README，**未逐一验证**。
