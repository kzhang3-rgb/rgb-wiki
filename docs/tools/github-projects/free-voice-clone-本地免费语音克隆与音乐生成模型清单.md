# free-voice-clone：本地免费语音克隆与音乐生成模型清单

> 一句话定位（README 标题）：**"本地且免费的开源语音克隆 TTS 模型与音乐生成模型清单"**——它本身**不是软件、不是模型**，而是一份**纯清单仓库**：**仓库里只有 1 个文件（`README.md`）**，把本地可跑的语音克隆 / 唱歌 / 音乐生成 / 音频修复 / 语音识别模型按板块整理成**快速对比表 + 折叠详情**。它最值得记的三件事：① **它是"索引"，不是"实现"**——每个模型给参数、是否支持零样本克隆、语言、是否流式、**许可证**，配 GitHub / Hugging Face / 官网 / arXiv 链接；② **模型许可证逐条不同**（Apache-2.0 / MIT / Research License / LFM / OpenRAIL-M / Stability AI 等），**不能按整个仓库一个许可证理解**；③ **仓库无许可证、创建后 5 天即停更**（2026-04-04 创建、2026-04-09 最后推送）——**当快照用，别当长期跟踪的活列表**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（1,586 行 / 57,559 字符）、确认**无 `LICENSE` 文件**、完整文件树与元数据；**未 clone、未下载任何模型、未在本地跑任何模型、未逐条访问模型链接**）
- 适用范围：清单面向**本地 / 免费 / 开源**取向；具体某个模型的硬件要求、许可与可用性**需逐个到其原始仓库确认**

## 核心结论

**一句话定位（README 标题）**：**local & free open-source voice-clone TTS models and music generation models**（本地免费开源语音克隆 TTS 与音乐生成模型清单）。

**三个最值得记的点：**

1. **它是"索引仓库"，不是工具或模型**（文件树自证）：
   - 整个仓库**只有 1 个文件：`README.md`**（1,586 行 / 57,559 字符）；**无代码、无 `LICENSE`、无 Release**。
   - 结构是 **"快速对比表 + `<details>` 折叠详情"**：速查表列 `Model / Voice Cloning / ASR / Languages / Streaming / License`，详情给 **Parameters / Zero-shot Voice Cloning / Languages / Streaming / Sample Rate / License** 等字段，附 GitHub / HF / 官网 / arXiv 徽章链接。
2. **六个板块**（README 自述）：
   - **Text-to-Speech (TTS) Models**（快速对比表列 **36 个**；含 Voxtral-4B-TTS-2603、LongCat-AudioDiT、Fish Audio S2 Pro、KittenTTS、MOSS-TTS、Qwen3-TTS、GLM-TTS、Fun-CosyVoice 3.0、IndexTTS2、Kokoro-82M、KokoClone、Chatterbox、Orpheus-TTS、Dia、Fish Speech、Step-Audio、MiMo-Audio、Kimi-Audio、ZipVoice 等；详情另有 Audio Flamingo 3）
   - **Music Generation Models**（ACE-Step 1.5、LeVo 2 / SongGeneration 2、Foundation-1、Music Flamingo、Magenta Realtime、Uni-MoE；详情另有 SoulX-Singer）
   - **Anything to Audio**（AudioX、MMAudio、HunyuanVideo-Foley、ThinkSound、PrismAudio、Uni-MoE）
   - **Audio Restoration & Enhancement**（NVIDIA A2SB、NovaSR、AudioSR）
   - **Speech Recognition (ASR)**（VibeVoice-ASR、FunASR）
   - **Additional Resources**（ComfyUI Integrations、Open ASR Leaderboard）
3. **许可证是本清单的核心信息，也逐条不同**（README 自述）：
   - 同样是 TTS，许可证可能落在 **Apache-2.0 / MIT / Research License / LFM / OpenRAIL-M / Stability AI** 等不同条款上。
   - 例：LongCat-AudioDiT（MIT）· Voxtral-4B-TTS-2603（Apache-2.0）· Fish Audio S2 Pro（**Fish Audio Research License**）· Supertonic 2（**OpenRAIL-M**）· MioTTS-2.6B / LFM2-Audio-1.5B（**LFM**）· Foundation-1（**Stability AI**）。
   - **含义**：想用某个模型**先看它自己那一行的 License**，再决定能不能商用 / 再分发。

**个人判断（未验证）**：它的价值是**"快速横向扫一遍可选模型"**——尤其是**许可证与是否支持零样本克隆这两列**，比翻十几个仓库快。**但它是静态快照**（2026-04 之后未更新），**新模型不会进榜、旧模型信息也可能过时**；**当作"起点名单"，不作为权威或最新依据**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [0xSojalSec/free-voice-clone](https://github.com/0xSojalSec/free-voice-clone) |
| 描述 | "local & free open-source voice-clone TTS models and music generation models"（本地免费开源语音克隆与音乐生成模型清单） |
| 归属 | 用户账号 `0xSojalSec`；`isFork: false`、`isArchived: false` |
| 许可证 | **无许可证**——**仓库无 `LICENSE` 文件**（shields.io 报 "not specified"）。**默认保留所有权利，不能因为"清单"就推定可自由再分发**；**清单内各模型另按各自许可证** |
| 主语言 | **未指定 / none**（shields.io）——**主体是 Markdown**，无代码 |
| 关注度 | **654 Star / 97 Fork / 4 Watcher**（**页面与 ungh 镜像一致**，2026-11-13）；开放 Issue **2**、贡献者 **1**（shields.io，2026-11-13） |
| 维护状态 | **2026-04-04 创建**；ungh 报 `pushedAt` **2026-04-09**（**创建后 5 天即停更**）、页面 `updatedAt` 2026-11-05（**多为徽章 / 元数据刷新，非内容更新**）；页面报 **0 Release** |
| 默认分支 | **`main`** |
| 体积 | `main` 分支 **仅 1 个条目**（ungh.cc files API）：**`README.md`** |
| 许可证（模型侧） | 逐条不同：Apache-2.0 · MIT · Research License · LFM · OpenRAIL-M · Stability AI 等（**以各模型原始仓库为准**） |

## 六个板块（README 自述）

| 板块 | 代表模型 |
| --- | --- |
| **TTS Models** | Voxtral-4B-TTS-2603 · LongCat-AudioDiT · Fish Audio S2 Pro · KittenTTS · MOSS-TTS · SoulX-Singer（唱歌）· SoproTTS · NeuTTS · Qwen3-TTS · GLM-TTS · VibeVoice-Realtime · Fun-CosyVoice 3.0 · MioTTS-2.6B · Supertonic 2 · KugelAudio · Kokoro-82M · KokoClone · IndexTTS2 · Maya1 · LFM2-Audio-1.5B · Step-Audio-EditX · FireRedTTS2 · VoxCPM · LuxTTS · MegaTTS3 · Spark-TTS · Fish Speech · Step-Audio · SoulX-Podcast · Chatterbox · Orpheus-TTS · Dia · VieNeu-TTS · MiMo-Audio · Kimi-Audio · ZipVoice（**速查表 36 个**；详情另有 Audio Flamingo 3） |
| **Music Generation** | ACE-Step 1.5（MIT，宣称兼容 CUDA/MPS/ROCm/XPU/CPU，**<4GB VRAM**）· LeVo 2 / SongGeneration 2（腾讯 AI Lab）· Foundation-1 · Music Flamingo · Magenta Realtime · Uni-MoE（详情另有 SoulX-Singer） |
| **Anything to Audio** | AudioX · MMAudio · HunyuanVideo-Foley · ThinkSound · PrismAudio · Uni-MoE |
| **Audio Restoration & Enhancement** | NVIDIA A2SB（Audio-to-Audio Schrödinger Bridges）· NovaSR · AudioSR |
| **Speech Recognition (ASR)** | VibeVoice-ASR（微软，7B，50+ 语言）· FunASR（含 VAD / 标点 / 说话人分离 / 时间戳 / 情感识别） |
| **Additional Resources** | ComfyUI Integrations（**⚠️ 该小节标题下无内容**）· Open ASR Leaderboard（HF 上的 ASR 榜单，仅一条链接） |

## 使用前需要注意

- **仓库本身无许可证**：**不要把"清单"当成可自由再分发的内容**；引用时请**自行署名并链接原仓库**。**清单内每个模型的许可另算**——同一份清单里混着 Apache-2.0 / MIT / Research License / OpenRAIL-M / LFM / Stability AI 等多种条款，**用哪个模型就看哪个模型的许可**。
- **它是静态快照、且创建后 5 天即停更**：`pushedAt` 停在 **2026-04-09**，页面 `updatedAt` 较新多为徽章刷新。**意味着新模型不会进榜**（例如 README 内引用的模型发布日期多在 2026 年上半年及之前）。**不要把它当作"最新"或"权威"列表**。
- **"本地免费"不等于"无门槛"**：清单挑的是**本地可跑 / 开源**取向，但各模型的**参数量、显存 / 内存要求、推理速度**差异很大（例：ACE-Step 1.5 标注 <4GB VRAM，而 Fish Audio S2 Pro 约 10 GB BF16）——**能否在你机器上跑，要逐个看模型仓库**。
- **清单只做了"汇总"，未做"验证"**：README **不承诺**每个模型的链接、参数、语言数或许可信息**仍然准确**；**以各模型原始仓库与 Hugging Face 页面为准**。
- **`Additional Resources` 部分不完整**：`### ComfyUI Integrations` 是**空标题**（无内容）；`ASR Leaderboard` 只有一条 HF 链接。
- **我只读了 README 与文件树**：**未 clone、未下载任何模型、未在本地跑任何模型、未逐条访问模型链接、未核对任何许可原文**；板块、模型与字段均来自 README，**未逐一验证**。

## 什么时候会再看

- 想**快速横向扫一遍"本地可跑、免费、开源"的语音克隆 / 唱歌 / 音乐生成 / 音频修复 / ASR 模型**时——尤其看**"是否支持零样本克隆"与"许可证"两列**。
- 想给某个语音项目**在本地挑一个可替换的开源引擎**（如 TTS、音乐生成），先找候选再逐个深挖时。
- 想对照语音方向的**软件类**项目：[voicebox](voicebox-本地优先的开源AI语音工作室.md)（本地优先的开源 AI 语音工作室）· [VoiceStudio](VoiceStudio-全本地运行的AI语音工作室与ElevenLabs替代.md)（全本地运行、AGPL 核心 + 闭源 Pro）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"本地语音"主题，但形态不同**：[voicebox：本地优先的开源 AI 语音工作室](voicebox-本地优先的开源AI语音工作室.md)与 [VoiceStudio：全本地运行的 AI 语音工作室与 ElevenLabs 替代](VoiceStudio-全本地运行的AI语音工作室与ElevenLabs替代.md)——那两篇是**能直接用的软件 / 工作室**（含引擎、界面、许可分层）；本篇是**模型清单**（"有哪些引擎可选"）。**可以先在本篇找候选模型，再到那两篇看"用这些引擎做成的成品"**，两边正好互补。
- **同属"把一堆模型 / 资源整理成清单"的做法**：[public-apis：人工策展的免费 API 清单](public-apis-人工策展的免费API清单.md)——那篇是**免费 API 清单**（有 CI 守格式与链接）；本篇是**本地语音模型清单**（**无校验、无 CI、静态快照**）。可对照"**清单仓库是否有自动化维护**"这件事对长期可用性的影响。

## 参考来源

- 仓库主页与 README（1,586 行 / 57,559 字符；核验日期 2026-11-13）：https://github.com/0xSojalSec/free-voice-clone
- 许可证：**无 `LICENSE` 文件**（`raw.githubusercontent.com/0xSojalSec/free-voice-clone/main/LICENSE` 返回 404；shields.io 报 "not specified"）
- 完整文件树（仅 1 条目：`README.md`）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 是否归档）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**页面与镜像一致**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；主语言取自 shields.io（"none / not specified"）

以上信息核验于 2026-11-13。**未 clone、未下载任何模型、未在本地跑任何模型、未逐条访问模型链接、未核对任何许可原文**；板块、模型清单与字段均来自 README，**未逐一验证**；**清单内各模型许可证互不相同，使用前请以各模型原始仓库为准**。
