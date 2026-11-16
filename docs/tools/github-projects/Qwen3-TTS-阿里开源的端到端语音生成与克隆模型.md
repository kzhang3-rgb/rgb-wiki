# Qwen3-TTS：阿里开源的端到端语音生成与克隆模型

> 一句话定位（README 自述）：**Qwen 团队发布的一系列语音生成能力**——覆盖 **声音克隆（voice clone）、声音设计（voice design）、超高质量拟人语音生成与自然语言语音控制**，README 自称"为开发者与用户提供**目前最全面的一套语音生成功能**"。最值得记的有三点：① **10 种语言 + 9 个预置音色 + 3 秒克隆**，并用 **Qwen3-TTS-Tokenizer-12Hz** 与**离散多码本 LM** 做成"端到端"架构；② **单模型同时支持流式与非流式**，README 称**首包延迟低至 97ms**（输入单个字符即可出第一包），面向实时交互；③ **但它有两条互不相同的路**——**本地权重**（`qwen-tts` 包 / vLLM-Omni）与**云上 DashScope API**，且**仓库页面 `updatedAt`（2026-11-12）与代码侧 `pushedAt`（2026-03-17）相差约 8 个月**——"页面在更新"不等于"代码在更新"。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（1,361 行 / 60,501 字节，含 Overview / Quickstart / vLLM / Fine Tuning / Evaluation / Citation）、`LICENSE` 全文、仓库文件树（37 个条目）与元数据；**未 clone、未下载模型权重、未安装 `qwen-tts`、未跑任何推理、未起 Web UI、未访问 Hugging Face / ModelScope / DashScope、未试听任何音频**）
- 适用范围：**本地部署**（NVIDIA GPU + PyTorch，推荐 FlashAttention 2，Python 3.12 隔离环境）与**云上 DashScope API** 两条路；覆盖 **10 种语言**（中 / 英 / 日 / 韩 / 德 / 法 / 俄 / 葡 / 西 / 意）与多方言音色；面向实时流式（约 97ms 首包）、离线批量、长语音等场景

## 核心结论

1. **明确定位**（README 自述）：Qwen 团队发布的一套语音生成能力，含**声音克隆、声音设计、高拟真语音生成、自然语言语音控制**四类；README 称其"提供了当前最全面的一套语音生成功能"。
2. **两档规模、五个已发布模型 + 一个 tokenizer**（README 表格）：
   - **Qwen3-TTS-12Hz-1.7B-VoiceDesign**（按文字描述"设计"音色，支持指令控制）
   - **Qwen3-TTS-12Hz-1.7B-CustomVoice**（9 个预置音色 + 指令风格控制）
   - **Qwen3-TTS-12Hz-1.7B-Base**（**3 秒**快速克隆，可作微调基底）
   - **Qwen3-TTS-12Hz-0.6B-CustomVoice** / **Qwen3-TTS-12Hz-0.6B-Base**
   - **Qwen3-TTS-Tokenizer-12Hz**（编解码：语音 → codes → 语音）
3. **关键数字**（README 自述）：**10 种语言** + 多方言音色；**9 个预置音色**（Vivian / Serena / Uncle_Fu / Dylan / Eric / Ryan / Aiden / Ono_Anna / Sohee）；**3 秒**克隆；端到端延迟低至 **97ms**；tokenizer 为 **12.5 FPS / 16 码本 / 2048 码本容量**。

**个人判断（未验证）**：它把"**同一套能力做成三种入口**"（Python 包、vLLM-Omni 离线推理、DashScope 云 API），而三种入口的**维护进度并不一致**——README 里 vLLM-Omni 明说"**目前只支持离线推理，在线服务后续支持**"；**想上线实时服务，短期更依赖 DashScope 云 API**。另外"3 秒克隆 + 9 音色 + 声音设计"叠起来，**滥用面（冒充 / 伪造）几乎与便利面一样大**，README 正文未见面向用户的专门防滥用条款（我未核对模型卡与 DashScope 服务条款）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [QwenLM/Qwen3-TTS](https://github.com/QwenLM/Qwen3-TTS) |
| 描述 | README 开篇自述：Qwen 开发的语音生成能力系列，支持声音克隆、声音设计、超高质量拟人语音生成与自然语言语音控制 |
| 许可证 | **Apache-2.0**（`LICENSE` 正文；附录署名 `Copyright 2026 Alibaba Cloud`）；**模型权重文件自身的许可本轮未单独核对**（以各 Hugging Face / ModelScope 模型页为准） |
| 主语言 | **未在本轮核验中取到**（材料仅含 README、`LICENSE`、文件树与元数据） |
| 关注度 | **14,376 Star / 1,850 Fork / 72 Watcher**（**页面值**，2026-11-13）；开放 Issue **11**、贡献者 **2**（同日） |
| 维护状态 | **2026-01-21 创建**；页面 `updatedAt` **2026-11-12**，ungh `pushedAt` **2026-03-17**（**代码侧已停更约 8 个月，页面较新多为 Star / 徽章 / 元数据刷新**）；`main`；页面报 **0 Release** |
| 默认分支 | `main` |
| 版本 | **0 Release**（无发行版；以 PyPI 包 `qwen-tts` 与模型权重页为分发口径） |
| 体积 | `main` 分支 **37 个条目**（ungh.cc files API；含 `finetuning/`、`examples/` 等，未逐个列出） |
| 论文 | arXiv:2601.15621（`Qwen3-TTS Technical Report`，BibTeX 在 README `## Citation`，约 1347 行） |
| 相关入口 | Hugging Face 合集 `Qwen/qwen3-tts` · ModelScope 合集 `Qwen/Qwen3-TTS` · 博客 <https://qwen.ai/blog?id=qwen3tts-0115> · DashScope API 文档（阿里云百炼） |

## 模型与能力

### 1. 五个模型 + 一个 tokenizer（README 表格）

| 模型 | 特征 | 语言 | 流式 | 指令控制 |
| --- | --- | --- | --- | --- |
| Qwen3-TTS-12Hz-1.7B-VoiceDesign | 按用户描述做"声音设计" | 10 种 | ✅ | ✅ |
| Qwen3-TTS-12Hz-1.7B-CustomVoice | 指令控制目标音色；9 个预置音色 | 10 种 | ✅ | ✅ |
| Qwen3-TTS-12Hz-1.7B-Base | 3 秒快速克隆；可微调 | 10 种 | ✅ | — |
| Qwen3-TTS-12Hz-0.6B-CustomVoice | 9 个预置音色 | 10 种 | ✅ | — |
| Qwen3-TTS-12Hz-0.6B-Base | 3 秒快速克隆；可微调 | 10 种 | ✅ | — |
| Qwen3-TTS-Tokenizer-12Hz | 编解码：语音 → codes → 语音 | — | — | — |

README 另注明：技术报告里提到的**其它模型（如 25Hz 系列）"将在不久后发布"**——**Evaluation 章节里已出现 25Hz 系列的分数，但这些权重尚未发布**。

### 2. 三类用法（`qwen-tts` Python 包）

- **CustomVoice**：`generate_custom_voice(text, language, speaker, instruct)`——选一个预置音色，可用 `instruct` 控制语气（例："用特别愤怒的语气说"）。
- **VoiceDesign**：`generate_voice_design(text, language, instruct)`——**用一段自然语言描述凭空造音色**（例："撒娇稚嫩的萝莉女声，音调偏高…"）。
- **VoiceClone**：`generate_voice_clone(text, language, ref_audio, ref_text)`——给**参考音频 + 其文本**即可克隆；`ref_audio` 支持本地路径 / URL / base64 / `(numpy_array, sample_rate)`；`x_vector_only_mode=True` 时只用说话人嵌入、可不给 `ref_text`，但质量可能下降。
- **Voice Design → Clone 工作流**（README 提供范例）：先用 VoiceDesign 生成一段参考音频，再用 `create_voice_clone_prompt` 建可复用 prompt，最后用 `generate_voice_clone` 反复生成——适合"**多句台词共用一个角色音色**"。

### 3. 关键设计（README 自述）

- **Qwen3-TTS-Tokenizer-12Hz**：自研 tokenizer，做声学压缩与高层语义建模，保留副语言信息与环境特征。
- **离散多码本 LM 架构**：README 称"完全绕开了传统 LM+DiT 方案的信息瓶颈与级联误差"。
- **Dual-Track 混合流式**：**单模型同时支持流式与非流式**，README 称"输入单个字符即可输出第一个音频包"，端到端延迟低至 **97ms**。

### 4. 三条部署路径

| 路径 | 说明（README） |
| --- | --- |
| **`qwen-tts` 包 + 本地 Web UI** | `pip install -U qwen-tts`；`qwen-tts-demo` 起 Gradio 界面（Base 模型建议走 HTTPS，否则浏览器麦克风权限会出问题） |
| **vLLM-Omni** | "day-0 支持"；**目前仅支持离线推理，在线服务后续支持**；示例在 vLLM-Omni 仓库 |
| **DashScope API（阿里云百炼）** | 提供 realtime / 声音克隆 / 声音设计三类 HTTP API 文档；README 称其"更快更高效" |

另有 `finetuning/`（微调说明目录）。

## 使用前需要注意

- **⚠️ 页面更新时间 ≠ 代码更新时间**：页面 `updatedAt` 为 **2026-11-12**，但 ungh `pushedAt` 停在 **2026-03-17**——**代码侧已约 8 个月没有推送**，页面较新很可能只是 Star / 徽章 / 元数据刷新。**判断"是否还在维护"请看 push 时间。**
- **⚠️ 0 Release、贡献者 2、开放 Issue 11（事实）**：仓库**没有正式发行版**；贡献者仅 **2** 人（以官方团队为主）；Issue 积压不多，但上手前也建议先扫一遍。
- **⚠️ 克隆音色的法律与伦理风险**：**3 秒参考音频即可克隆**，再叠加 VoiceDesign"凭空造音色"，可用于冒充 / 伪造。**README 正文未见面向用户的专门防滥用条款**（我未核对模型卡与 DashScope 服务条款）。**克隆他人声音在多国涉及人格权 / 声音权与诈骗风险，使用前请确认授权与当地法律。**
- **⚠️ 25Hz 系列只出现在评测表、权重尚未发布**：Evaluation 里混排了 25Hz 与 12Hz，**下载时请认准 12Hz 系列**。
- **依赖与硬件**：推荐 **Python 3.12 隔离环境**、**FlashAttention 2**（显存 <96GB 且 CPU 核多时用 `MAX_JOBS=4 pip install`）；FlashAttention 2 要求模型以 `float16` / `bfloat16` 加载。
- **长语音质量随帧率变化（README 自评）**：长语音 WER 上 **25Hz 系列（1.517 / 1.225）优于 12Hz 系列（2.356 / 2.812）**——**帧率更高的版本长语音更稳，但当前可下载的是 12Hz**。
- **我只读了 README、`LICENSE` 与文件树**：**未 clone、未下载权重、未安装 `qwen-tts`、未跑推理、未起 Web UI、未访问 HF / ModelScope / DashScope、未试听音频**；上述数字与能力均来自 README 自述，**未逐一验证**。

## 什么时候会再看

- 想找一个**开源、可商用（Apache-2.0）、覆盖 10 语言的语音克隆 / 语音设计**模型来做本地 TTS 时。
- 想对比"**声音设计（文字造音色）**"与"**参考音频克隆**"这两条路线，并看它如何**把两者串成工作流**（Design → Clone）时。
- 想评估"**离散多码本 LM + 端到端 + 低延迟流式**"这套架构（对照传统 LM+DiT）时。
- 想对照其它语音项目选型：[fish-speech](fish-speech-Fish-Audio的开源语音生成与克隆模型.md)（Drama 3 / S2-Pro）· [VibeVoice](VibeVoice-微软开源语音AI的TTS下架与ASR演进.md)（TTS 已下架、现主攻 ASR）· [voicebox](voicebox-本地优先的开源AI语音工作室.md)（把 Qwen3-TTS 当作 8 个引擎之一）· [free-voice-clone](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)（把 Qwen3-TTS 列入候选清单）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"开源语音克隆 / 生成"三篇之一**：[fish-speech：Fish Audio 的开源语音生成与克隆模型](fish-speech-Fish-Audio的开源语音生成与克隆模型.md)——那篇的 **Drama 3（35B-A3B）/ S2-Pro（4B）** 与许可（社区许可 + 营收门槛）跟本篇（Apache-2.0）**在许可证与规模上差异很大**，且 fish-speech 的评测直接把 **Qwen3-TTS（0.77/1.24）**列为对标；**选型时建议两篇对着读**。
- **同组的另一个 TTS 项目，恰好与本篇相反**：[VibeVoice：微软开源语音 AI 的 TTS 下架与 ASR 演进](VibeVoice-微软开源语音AI的TTS下架与ASR演进.md)——那篇的 **TTS 代码已因滥用下架、现留下 ASR 系**；本篇的 TTS 仍在仓里。可对照"**同一类技术，不同机构的开放尺度**"。
- **同属本地语音方向的两篇**：[voicebox：本地优先的开源 AI 语音工作室](voicebox-本地优先的开源AI语音工作室.md)（把 **Qwen3-TTS 0.6B / 1.7B** 作为其 8 个 TTS 引擎之一——本篇正是"引擎"那一侧）与 [free-voice-clone：本地免费语音克隆与音乐生成模型清单](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)（把 Qwen3-TTS 与 Fish Speech 一并列入候选清单）。**一个软件、一个清单，正好能把本篇放回"能直接用的成品"与"横向可选名单"两个上下文里。**

## 参考来源

- 仓库主页与 README（1,361 行 / 60,501 字节；核验日期 2026-11-13）：https://github.com/QwenLM/Qwen3-TTS
- 许可证：`LICENSE`（Apache-2.0，附录署名 `Copyright 2026 Alibaba Cloud`）
- 论文 / 引用：arXiv:2601.15621（`Qwen3-TTS Technical Report`，BibTeX 见 README `## Citation`，约 1347 行）
- 评测章节：README `## Evaluation`（约 463 行起；含 Seed-TTS、多语言、Cross-Lingual、InstructTTSEval、长语音与 tokenizer 基准）
- 模型入口：Hugging Face 合集 <https://huggingface.co/collections/Qwen/qwen3-tts> · ModelScope 合集 <https://modelscope.cn/collections/Qwen/Qwen3-TTS> · 博客 <https://qwen.ai/blog?id=qwen3tts-0115>
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13）；Issue 数与贡献者数取自核验记录（同日）

以上信息核验于 2026-11-13。**未 clone、未下载模型权重、未安装 `qwen-tts`、未跑任何推理、未起 Web UI、未访问 Hugging Face / ModelScope / DashScope、未试听任何音频**；模型清单、能力与数字均来自 README 自述，**未逐一验证**；**仓库代码为 Apache-2.0，模型权重自身的许可未在本轮单独核对**。
