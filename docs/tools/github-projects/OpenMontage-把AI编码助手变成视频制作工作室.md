# OpenMontage：把 AI 编码助手变成视频制作工作室

> 一句话定位（README 自述）：**"第一个开源的 agentic 视频制作系统"**（The first open-source, agentic video production system）——把你已经在用的 **AI 编码助手**（Claude Code / Cursor / Codex / Copilot / Windsurf）当成**整条视频流水线的编排器**，从研究、编剧、素材生成、剪辑一路做到合成与自审。它最值得记的是三件事：① **它没有代码编排器，AI 编码助手本身就是编排器**（读 YAML 流水线清单 → 读 Markdown 阶段导演技能 → 调 Python 工具 → 自审 → 人工审批门 → 渲染 → 成片自审）；② **"零 API Key 也能做出真视频"**——Piper 离线 TTS + Archive.org / NASA / Wikimedia Commons 免费档案素材 + Remotion / HyperFrames / FFmpeg，把"没配任何付费供应商"也做成一条能交付的路径；③ 它把视频生产**当成工程来治理**——**人工审批门是强制的、预算可估可锁可对账、每个决策留审计轨迹、渲染前后都有质量门**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（766 行 / 43,710 字符）、`LICENSE`（AGPL-3.0）、完整文件树与元数据；**未 clone、未安装、未运行 `make setup`、未接任何供应商、未渲染任何视频、未审计源码**）
- 适用范围：**需要 AI 编码助手 + Python 3.10+ / FFmpeg / Node.js**；本地 GPU 可选（`make install-gpu`，跑本地 wan2.2 等模型）；供应商与费用**全部可选**

## 核心结论

**一句话定位（README 自述）**：**第一个开源的 agentic 视频制作系统**——把 AI 编码助手变成**完整视频制作工作室**（研究、编剧、素材生成、剪辑、合成）。

**三个最值得记的点：**

1. **"agent-first"：没有代码编排器，AI 编码助手就是编排器**（README 自述）：
   - 统一流程：`research -> proposal -> script -> scene_plan -> assets -> edit -> compose`。
   - 实际运行链路：AI 助手读 **YAML pipeline manifest** → 读 **Markdown stage director skill** → 调 **Python 工具**（用 **7 维打分选供应商**）→ **reviewer skill 自审** → 写 **JSON 检查点** → **人工审批** → **pre-compose 验证门** → 渲染（**Remotion 或 FFmpeg**）→ **post-render 自审**（ffprobe + 抽帧 + 音频分析）→ 通过才输出。
   - **三层知识架构**：L1 `tools/` + `pipeline_defs/`（"有什么工具"）/ L2 `skills/`（"怎么用"）/ L3 `.agents/skills/`（"为什么、取舍是什么"）。
2. **零 API Key 也能做出真视频**（README 自述）：
   - **Piper TTS**（离线免费）+ **Archive.org / NASA / Wikimedia Commons** 免费档案素材 + **Pexels / Unsplash / Pixabay** 免费图库 + **Remotion**（React）+ **HyperFrames**（HTML/GSAP）+ **FFmpeg** + 内置字幕。
   - **Documentary Montage** 流水线用 **CLIP 检索真实影像**剪成片，README 明确强调**不是"一堆静态图做成幻灯片"**。
   - 无生成供应商时：agent 生成静态图，**Remotion 把它们变成带动画的完整视频**。
   - 有 GPU 时可 `make install-gpu` 跑**本地**模型（如 wan2.2 等），进一步摆脱云供应商。
3. **把视频生产当工程治理**（README 自述）：
   - **人工审批门是强制的，不是建议**：proposal、script、scene plan、生成素材、发布**都会暂停等你签字**；checkpoint writer **拒绝在没有记录审批的情况下把门控阶段标成 "completed"**，且每个被取代的检查点都会归档（含门转移，审计轨迹在修订中不丢）。
   - **pre-compose 验证**：交付承诺被违背（如号称"运动主导"但 80% 是静态图）、幻灯片风险分临界、渲染器家族缺失——**在浪费 GPU 时间之前就拦下**。
   - **post-render 自审**：ffprobe 校验 + 在 4 个位置抽帧查黑帧与坏叠加 + 音频电平分析（静音/削波）+ 校验交付承诺是否兑现 + 字幕是否在；**自审不过，视频不呈现**。
   - **幻灯片风险打分**（6 维：重复度、装饰性视觉、弱运动、镜头意图、过度依赖排版、无依据的电影化宣称）——防"会动的 PowerPoint"。

**个人判断（未验证）**：它和"做一个视频生成 SaaS"是两条完全不同的路——**它不提供算力也不托管模型，只提供"把一堆你已有的工具和你的 AI 编码助手串成一条受管控的产线"**。这让它的**成本和可控性都比托管服务好**（README 给了示例成本：一条片约 **$1.33**、另一条约 **$4**、约 **$5**），**代价是你必须真的有一套会用它（会读技能、会调工具）的 AI 编码助手**，否则这堆技能文件只是文档。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [OpenMontageInc/OpenMontage](https://github.com/OpenMontageInc/OpenMontage) |
| 旧地址 | [calesthio/OpenMontage](https://github.com/calesthio/OpenMontage)（**仓库已重命名 / 转移，旧 URL 会跳转到新地址**；**README 内多处链接仍写旧名 `calesthio/OpenMontage`**，属残留） |
| 描述 | "The first open-source, agentic video production system"（把 AI 编码助手变成完整视频制作工作室） |
| 归属 | 组织账号 `OpenMontageInc`；`isFork: false`、`isArchived: false` |
| 许可证 | **AGPL-3.0**（`LICENSE` 34,523 字符）——**copyleft，且有网络服务场景的源码提供义务；若改造成对外提供的服务，需评估 AGPL 义务** |
| 主语言 | **Python 73.1%**（shields.io，2026-11-13）；其余含 TypeScript / JavaScript（Remotion / HyperFrames 前端）等 |
| 技术栈 | **Python 工具层** + **Markdown 技能层** + **YAML 流水线清单**；渲染引擎 **Remotion（React）/ HyperFrames（HTML/CSS/GSAP）/ FFmpeg**；前置 **Python 3.10+ / FFmpeg / Node.js（22.23.4+ / 24.22.1+ / 26.12.1+）**；`make setup` |
| 关注度 | **80,092 Star / 10,774 Fork / 405 Watcher**（**页面值**，2026-11-13）；开放 Issue **97**、贡献者 **118**（shields.io，2026-11-13）。**ungh 镜像 79,973 / 403 / 10,742，略有差异** |
| 维护状态 | **2026-03-29 创建**；ungh 报 `pushedAt` **2026-11-09**、页面 `updatedAt` 2026-11-09（**活跃**）；页面报 **0 Release / 0 tag**（**不发版，直接跟 main**） |
| 默认分支 | **`main`** |
| 话题标签 | `ai-video` · `video-production` · `agentic` 等（README 未逐条列出，以页面为准） |
| 体积 | `main` 分支 **2,285 个条目**（ungh.cc files API）；顶层以 **`.agents`（997）**、**`.claude`（433）**、`tools`（236）、`tests`（212）、`skills`（158）、`remotion-composer`（50）、`schemas`（35）、`ink-theater`（30）、`lib`（21）、`docs`（14）、`pipeline_defs`（13）、`.github`（13）、`backlot`（11）、`scripts`（10）、`assets`（8）、`styles`（7）、`.codex`（4）、`.cursor`（4）为主 |
| 官网 / 社交 | 官网 <https://openmontage.video> · YouTube `@OpenMontage` · X [@calesthioailabs](https://x.com/calesthioailabs) |
| 贡献方式 | `docs/ARCHITECTURE.md`（技术参考）· `docs/PROVIDERS.md`（供应商指南：设置/定价/免费额度）· `AGENT_GUIDE.md`（agent 契约）· GitHub Discussions（Show and Tell / Ideas / Q&A） |

## 能力结构（README 自述）

**11 条流水线**：

| 流水线 | 说明 |
| --- | --- |
| Animated Explainer | 动画解说 |
| Animation | 动画 |
| Avatar Spokesperson | 数字人口播 |
| Cinematic | 电影感短片 |
| Clip Factory | 批量切片 |
| **Documentary Montage** | 纪录式蒙太奇（**用 CLIP 检索真实影像剪成片**，非幻灯片） |
| Hybrid | 混合 |
| Localization & Dub | 本地化 / 配音 |
| Podcast Repurpose | 播客再加工 |
| Screen Demo | 屏幕演示 |
| Talking Head | 真人 / 数字人出镜 |

**供应商覆盖（README 分块列出，共 60+）**：

| 类别 | 代表项 |
| --- | --- |
| 视频生成 | Google Veo 3.1 · Grok Imagine Video · Higgsfield · MiniMax/H3 · HeyGen · **WAN 2.1/2.2（本地 GPU）** · Hunyuan · CogVideo · LTX-Video · **Pexels / Pixabay / Wikimedia Commons（免费素材）** |
| 图片生成 | FLUX · Google Imagen · Grok Imagine Image · GPT Image 2 · Seedream 5.0 · Nano Banana 2 · Atlas Cloud · Recraft · Kling Official · **Local Diffusion（本地 GPU）** · Pexels / Pixabay / Unsplash · ManimCE |
| TTS | ElevenLabs · Google TTS · Kling Official TTS · OpenAI TTS · **Piper（本地离线免费）** · Azure Speech · DashScope / Doubao / Fish Audio |
| 音乐音效 | Suno AI · ElevenLabs Music · ElevenLabs SFX |
| 后期（**始终可用、始终免费**） | FFmpeg · Video Stitch · Video Trimmer · Audio Mixer · Audio Enhance · Color Grade · Subtitle Gen |
| 增强 | Upscale（Real-ESRGAN）· Background Remove（rembg/U2Net）· Face Enhance · Face Restore（CodeFormer/GFPGAN） |
| 分析 | Transcriber（WhisperX 词级时间戳）· Scene Detect · Frame Sampler · Video Understand（CLIP/BLIP-2） |
| 数字人与对口型 | Talking Head（SadTalker/MuseTalk）· Lip Sync（Wav2Lip）· Kling Avatar · Kling Lip Sync |
| 合成渲染 | **Remotion**（React 程序化视频）· **HyperFrames**（HTML/CSS/GSAP）· **FFmpeg** |

**风格系统（Style Playbooks）**：Clean Professional（企业/教育/SaaS）· Flat Motion Graphics（社媒/TikTok/创业）· Minimalist Diagram（技术深挖/架构）。Playbook 控制排版、配色、动效、音频配置与质量规则。

**平台输出预设**：YouTube Landscape 1920x1080（16:9）· YouTube 4K 3840x2160 · YouTube Shorts 1080x1920（9:16）· Instagram Reels 1080x1920 · Instagram Feed 1080x1080（1:1）· TikTok 1080x1920 · LinkedIn 1920x1080 · Cinematic 2560x1080（21:9）。

## 生产治理（README 自述）

- **质量门**：人工审批门强制；pre-compose 验证（交付承诺、幻灯片风险分、渲染器家族）；post-render 自审（ffprobe + 抽帧 + 音频 + 字幕）；幻灯片风险 6 维打分；用户自带素材会被逐个探测（分辨率/编码/声道/时长）**再进入创作决策**——"不从文件名瞎猜内容"。
- **供应商打分（7 维）**：任务契合 **30%** · 输出质量 **20%** · 控制能力 **15%** · 可靠性 **15%** · 成本效率 **10%** · 延迟 **5%** · 连续性 **5%**；胜出供应商与得分**连同所有被比较的备选一起写进决策轨迹**。选择器会把模糊需求（如"皮克斯风、角色一致"）**先扩写成打分友好的意图与风格信号**，并输出所选供应商的 `agent_skills`，让 agent 立刻去读对的 Layer 3 技能。
- **决策审计轨迹**：供应商选择、风格/playbook、音乐、配音、渲染器家族、任何回退/降级**都记录备选、置信度与理由**，跨阶段累积，可追溯"为什么成片长这样"。
- **预算控制**：**估算（Estimate）→ 预留（Reserve）→ 对账（Reconcile）**；模式 `observe`（只跟踪）/ `warn`（超支记日志）/ `cap`（硬上限）；**单次操作超过阈值（默认 $0.50）暂停确认**；**总预算上限默认 $10，可配**。
- **渲染器切换是治理红线**：运行时在 proposal 阶段选定（`render_runtime`）并经 `edit_decisions` 锁定；**运行时静默互换属治理违规**（见 `skills/core/hyperframes.md`）。

## 使用前需要注意

- **旧地址会跳转**：`calesthio/OpenMontage` 已重命名为 `OpenMontageInc/OpenMontage`；**README 内仍多处引用旧名**，引用时建议统一用新地址。
- **AGPL-3.0，不是宽松许可**：这是**强 copyleft**，**若你把改造后的版本作为网络服务对外提供，需要按 AGPL 向使用者提供对应源码**。想闭源二次分发 / 提供线上服务前，先评估许可证义务。
- **不提供算力、不托管模型、不替你付费**：项目只提供"把工具与你的 AI 编码助手串成产线"的技能与工具层；**用哪家、花多少，全看你配了哪些供应商**（README 给了示例成本，如 $1.33 / $4 / $5，**仅为示例、非报价**）。
- **它强依赖"你有一个会用它的 AI 编码助手"**：核心是 **Markdown 技能 + YAML 清单 + Python 工具**，**编排靠 AI 助手读技能后自主决策**。若助手能力/上下文不足，这堆文件不会自己动。
- **不发版（0 Release / 0 tag）**：使用即跟 `main` 走，**没有版本锚点**；想固定行为只能自己记 commit。
- **仓库体量与技能密度很大**：`.agents`（997）+ `.claude`（433）+ `skills`（158），**真正要读的是技能与清单，不是代码**；上手成本主要在"理解三层知识架构"。
- **我只读了 README、许可与文件树**：**未 clone、未安装、未运行 `make setup`、未接任何供应商、未渲染任何视频、未验证质量门 / 预算控制 / 审计轨迹的实际行为**；功能与流程均来自 README，**未逐一验证**。
- **零 Key 路径虽在，但"能做真视频"仍取决于你对免费素材的取向**：Archive.org / Wikimedia 档案素材**多为公共领域或开放许可，但每条素材的许可与署名要求需逐条确认**（README 不替你担保素材可商用）。

## 什么时候会再看

- 想**用 AI 编码助手驱动一条完整的视频产线**（研究 → 剧本 → 分镜 → 素材 → 剪辑 → 合成 → 自审）时。
- 想参考"**agent-first 架构**"——**不写代码编排器，把编排交给 LLM + 技能文档 + 工具**——的组织方式（YAML manifest + Markdown 技能 + Python 工具三层）时。
- 想借鉴它的**治理装置**：**强制人工审批门 + 决策审计轨迹 + 7 维供应商打分 + 渲染前后质量门 + 预算估/锁/对账**——这套"给 AI 生产流程加质检与留痕"的做法可迁移到别的生成式流水线。
- 想研究"**零 API Key / 本地优先**"如何在生成类项目里落地（Piper + 免费档案素材 + 本地 GPU 模型 + 开源渲染引擎）时。
- 想对照 AI 视频生产的其它形态： [huobao-drama](huobao-drama-一句话生成AI短剧的自动化生产平台.md)（一句话自动出片）· [aid-studio](aid-studio-开源AI漫剧与短剧创作平台.md)（自托管创作平台）· [dioramas](dioramas-用AI生成素材做电影感3D网站.md)（AI 素材做 3D 网页）· [autoclip](autoclip-把一个链接剪成多平台短视频的桌面工具.md)（链接转多平台短视频）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都是"让编码助手直接产出视频"，一个流水线自治、一个模板驱动**：[adu-motion-video：阿杜的 AI 剪辑模板技能与 MotionGit 开放格式](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)——本篇是**从研究到成片的完整流水线**（11 条 pipeline、供应商打分、预算控制、强制审批门）；那篇是**以动画模板为中心的制作包**（选风格 → 换素材 → 出 MP4 与可编辑工程）。可对照"**流水线自治**"与"**模板驱动的确定性风格**"。
- **同属"AI 生成视频 / 短剧"但形态不同**：[huobao-drama：一句话生成 AI 短剧的自动化生产平台](huobao-drama-一句话生成AI短剧的自动化生产平台.md)与 [aid-studio：开源 AI 漫剧与短剧创作平台](aid-studio-开源AI漫剧与短剧创作平台.md)——那两篇是**面向短剧/漫剧的成品工具或平台**（自动出片 / 自托管创作平台）；本篇是**把通用视频产线交给你的 AI 编码助手来编排**的"系统"。可对照"**AI 视频生产**"的三种形态：**自动化工具** vs **可运营平台** vs **agent-first 编排系统**。
- **同为"AI 生成素材 → 成品"的工程化尝试**：[dioramas：用 AI 生成素材做电影感 3D 网站](dioramas-用AI生成素材做电影感3D网站.md)——那篇是 **3D 网页**（Nano Banana + Meshy 管线 + 成本可查 + 提示词全在仓库）；本篇是**视频**。两篇都把**"提示词 + 管线 + 成本"透明化**，可对照。
- **同为"把真实软件 / 工具交给 AI Agent 去驱动"**：[craft-anything：看清软件如何工作再用 AI 重建自己的版本](craft-anything-看清软件如何工作再用AI重建自己的版本.md)——那篇让 AI Agent **逆向理解并重建软件**；本篇让 AI Agent **驱动一条视频产线**。两者都把**"AI 编码助手当执行主体"**，可对照它们的**安全规则与治理设计**。
- **同属"agent-first 的视频制作"，粒度不同**：[video-shotcraft：用 Remotion 做电影感产品宣传片的视频技能](video-shotcraft-用Remotion做电影感产品宣传片的视频技能.md)——那篇聚焦**产品宣传片**这一条细分流水线，用 Remotion 把每帧画出来并交付**可编辑工程**；本篇是**研究 → 编剧 → 素材 → 剪辑 → 审批 → 预算**的通用流水线。可对照"**细分产线**"与"**通用编排**"。
- **同属"让编码助手直接产出视频"**：[lanshu-create-ai-presenter-video：从脚本到可验证 AI 数字人播报视频的技能](lanshu-create-ai-presenter-video-从脚本到可验证AI数字人播报视频的技能.md)——那篇以**旁白为母时钟 + 数字人 / 风格化表演**为中心，并把**八个制作状态由磁盘产物算出**（证据门）；本篇是**多 pipeline、供应商打分、预算控制**的自治产线。可对照"**流水线自治**"与"**证据驱动交付**"。
- **同属"把 AI 编码助手变成视频工作室"，中心不同**：[baocut：带可编辑时间轴的 AI 视频 Agent](baocut-带可编辑时间轴的AI视频Agent.md)——本篇是**从研究到成片的完整流水线**（偏"流水线自治"），那篇是**以时间轴为中心、人在环的可编辑编辑器**；可对照"流水线自治"与"人在环的可编辑工程"。

## 参考来源

- 仓库主页与 README（766 行 / 43,710 字符；核验日期 2026-11-13）：https://github.com/OpenMontageInc/OpenMontage
- 旧地址（会跳转到上面）：https://github.com/calesthio/OpenMontage
- 许可证：`LICENSE`（AGPL-3.0，34,523 字符）
- 其它文档：`docs/ARCHITECTURE.md` · `docs/PROVIDERS.md` · `AGENT_GUIDE.md` · `PROJECT_CONTEXT.md` · `skills/core/hyperframes.md`
- 官网：<https://openmontage.video> · X：[@calesthioailabs](https://x.com/calesthioailabs) · YouTube：`@OpenMontage`
- 完整文件树（2,285 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号类型经 ungh `orgs` 接口确认为组织

以上信息核验于 2026-11-13。**未 clone、未安装、未运行、未接任何供应商、未渲染任何视频、未审计源码**；功能、流水线、供应商清单、治理机制与成本数字均来自 README，**未逐一验证**。
