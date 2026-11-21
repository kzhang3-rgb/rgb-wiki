# fish-speech：Fish Audio 的开源语音生成与克隆模型

> 一句话定位（README 标题）：**Fish Speech / Fish Audio**——一套**开源的语音生成（TTS）与声音克隆**系统，当前主推 **Drama 3（35B-A3B，每 token 激活 3B）** 与 **S2-Pro（4B）** 两档旗舰，支持**多说话人、声音克隆、自然语言指令控制**，README 称 Drama 3"**在公开基准上相较 S2 明显跃升**"。最值得记的有三点：① **它非常活跃**：`pushedAt` 就在核验当天（2026-11-12）、**15 条 Release、102 位贡献者**、开放 Issue 仅 **9**；② **许可不是常见开源协议**——代码与权重走 **Fish Audio Community License（FACL v1.0）**，**年营收 < 50 万美元才可用社区商用许可**，**做 Hosted Service（对第三方提供 TTS 服务）必须另行授权**，且 **S2-Pro 权重另按 Fish Audio Research License**；③ **能力亮点**：多轮"导演式"重录、**局部改写（词级 infilling）**、场景式多角色、内联标签（`[whisper]` `[angry]` `[laughing]`），S2-Pro 实测 **WER 0.54%（中）/ 0.99%（英）**、**RTF 0.195（单 H200）**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（10,998 字节）与 `LICENSE` 全文（FACL v1.0，114 行）、仓库文件树（137 个条目）与元数据；**未 clone、未下载模型权重、未跑任何推理、未试听音频、未访问 speech.fish.audio / fish.audio / Hugging Face**）
- 适用范围：**本地 / 自托管部署**（安装说明在 `speech.fish.audio`；README 推荐 SGLang-Omni 或 vLLM-Omni 提速；Hugging Face 提供 Drama 3 与 S2-Pro 权重）；面向**多说话人、多语言、长内容、可编辑**的语音生成；**商用受 LICENSE 的营收门槛与 Hosted Service 条款约束**

## 核心结论

1. **两款旗舰模型**（README 模型表）：

| 模型 | 规模 | 权重 | 说明（README） |
| --- | --- | --- | --- |
| **Drama 3** | 35B-A3B（每 token 激活 3B） | Hugging Face `fishaudio/drama-3` | 新一代旗舰：多角色场景、长内容、多轮修订与**局部编辑** |
| **S2-Pro** | 4B | Hugging Face `fishaudio/s2-pro` | 上一代旗舰，**推理更快** |

   模型类型从 checkpoint 的 `config.json` 自动识别，**两者共用同一套命令行与 API server**。

2. **Drama 3 的关键新能力**（README 自述）：**多轮导演式重录**（"更温柔一点，再悲伤一点"，模型带上下文重录而非从头再来）、**局部改写 / infilling**（像改文字一样改音频，替换单个词而保持音色与过渡）、**场景式多角色**（按剧本描述配一整场戏）、**长对话 / 长语音**（角色与音色稳定）、**更强表现力**（笑、哭、耳语不再被参考音"压平"）、**多语言克隆**、**内联标签控制**（`[whisper]` `[angry]` `[laughing]`）。
3. **S2-Pro 的要点**（README 自述）：内联自然语言指令（`[pause]` `[excited]` `[whisper]`）、**原生多说话人**（用 speaker ID 在一个文本里区分角色）、**10–30 秒**参考音频即可克隆、**单次流式输出长音频**、**RTF 0.195（单 H200）、首音约 100ms**。
4. **许可（LICENSE 正文，重点）**：**Fish Audio Community License v1.0（FACL v1.0，2026-10-13 更新）**——研究与非商用**免费**；**商用**仅当**年营收 < 50 万美元**时自动授权；达到门槛须书面通知并在 90 天过渡期后另签；**Hosted Service（把材料提供给第三方、让他们用自供文本 / 音频 / 声音样本生成语音，无论营收与用途）一律需单独书面授权**；**S2-Pro 权重另按 Fish Audio Research License**（README 的 License Notice 原文如此）。

**个人判断（未验证）**：它可能是本组三篇里**工程与社区最成熟**的一个（102 贡献者、15 Release、Issue 积压仅 9、`pushedAt` 在核验当天），**但它的许可也是最需要细读的**——对个人 / 小团队（营收 < 50 万美元）基本够用，**一旦要做"给第三方调用的 TTS 服务"就落到 Hosted Service 条款、必须单独授权**。**把它当"开源"用之前，请先按许可证口径确认自己的用途。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [fishaudio/fish-speech](https://github.com/fishaudio/fish-speech) |
| 描述 | Fish Audio 的开源语音生成（TTS）与声音克隆系统，当前旗舰为 Drama 3 与 S2-Pro |
| 许可证 | **Fish Audio Community License v1.0（FACL v1.0）**（`LICENSE` 114 行；**shields 报 "not identifiable by github"**，属**非标准**许可）；**S2-Pro 权重另按 Fish Audio Research License**（README License Notice） |
| 主语言 | **未在本轮核验中取到**（材料仅含 README、`LICENSE`、文件树与元数据） |
| 关注度 | **33,695 Star / 2,932 Fork / 161 Watcher**（**页面值**，2026-11-13）；开放 Issue **9**、贡献者 **102**（同日） |
| 维护状态 | **2023-10-10 创建**；页面 `updatedAt` **2026-11-12**、ungh `pushedAt` **2026-11-12**（**核验当天仍在推送，活跃**）；`main`；页面报 **15 条 Release** |
| 默认分支 | `main` |
| 版本 | **15 条 Release**（以 Hugging Face 权重与 Docker 镜像 `fishaudio/fish-speech` 为分发口径） |
| 体积 | `main` 分支 **137 个条目**（ungh.cc files API；含 `docs/`（多语言 README）、`tools/`、`fish_speech/` 等，未逐个列出） |
| 报告 | Fish-Speech v1.4 arXiv:2411.01156（2024）· Fish Audio S2 arXiv:2603.08823（2026）· Drama 3 arXiv:2610.18010（2026） |
| 官网 / 文档 | <https://fish.audio> · <https://speech.fish.audio> |

## 模型与能力

- **Drama 3 vs S2-Pro（README 对比表）**：多说话人（S2 支持 → Drama 3 做成"场景式多角色"）、声音克隆（→ 更低的多语言错误率）、内联自然语言指令（都支持）、**局部编辑 / infilling（S2 不支持 → Drama 3 支持）**、**多轮导演式修订（S2 不支持 → Drama 3 支持）**、长内容（S2 分块生成 → Drama 3 长对话 / 长语音 + 流式）、强情绪（S2 中等 → Drama 3 更强）。
- **评测数字**（README 自述）：
  - **Drama 3**：在多语言克隆（MiniMax Multilingual Testset）、自由语音编辑（Ming-Freeform）、多轮情绪修订（Step-Audio-Edit）上**全面超过 S2**（CER / WER、控制精度、风格准确率）。
  - **S2-Pro**：Seed-TTS Eval **WER 中文 0.54% / 英文 0.99%**（README 称含闭源系统在内最低）；Audio Turing Test **0.515**；EmergentTTS-Eval **81.88%**；多语言测试集 **11/24 语言 WER 最优、17/24 语言 SIM 最优**；并直接对比 **Qwen3-TTS（0.77/1.24）· MiniMax Speech-02（0.99/1.90）· Seed-TTS（1.12/2.25）**。
- **部署与提速**（README）：安装见 `speech.fish.audio`（含权重下载、Docker 部署）；**强烈推荐 SGLang-Omni** 追求效率，另有 **vLLM-Omni Fish Speech S2 Pro Recipe**；命令行 / API server / WebUI 三种用法。
- **面向 Agent 的入口**（README）：专门给了 "For LLM Agent" 一节，一段安装提示词即可让 Agent 按 `speech.fish.audio/install/` 自行安装配置。

## 使用前需要注意

- **⚠️ 许可证是本篇最需要读懂的部分（FACL v1.0）**：
  - 研究 / 非商用**免费**；**商用仅在"你与关联方年营收 < 50 万美元"时自动授权**；达到门槛须**书面通知**并在 **90 天过渡期**后另签。
  - **Hosted Service 一律需单独书面授权**——即使营收未到门槛。定义很宽：**把材料提供给第三方，让他们用自己提供的文本 / 音频 / 声音样本来生成语音**（含 API / SDK / 模型托管 / 以 TTS、配音、语音克隆为主要目的的网站或功能）都算。**例外**：为自己创建的内容配音、纯生产交付（如配音工作室只交付成品）、免费非商用研究演示。
  - **署名义务**：分发须附许可副本并在 "Notice" 文件保留指定署名；对外还要显著展示 **"Built with Fish Audio"**。
  - **禁止用途**：未经本人同意克隆可识别自然人声音、冒充欺骗、涉未成年人或真实人物性内容、**移除 / 规避水印与来源信号**；**不得用其输出训练其它生成式 / 语音模型**（检测合成音频者除外）。
  - 准据法为**美国加州**；许可方主体是 **39 AI, INC.**。
- **⚠️ 代码许可 ≠ 模型权重许可（README 明说）**：README 的 License Notice 写"**本代码库及其关联的模型权重**"按 FACL 发布，**但 S2-Pro 权重另按 Fish Audio Research License**；`LICENSE` 定义也重申"**以不同许可发布的权重（如 S2-Pro）不属于本协议的材料**"。**用哪个模型，先看哪个模型的许可页。**
- **⚠️ 声音克隆的伦理与法律风险**：**10–30 秒**参考音频即可克隆，且 Drama 3 支持**局部改写与多轮重录**——伪造与篡改的便利性更高。**FACL 正文确有禁止冒充与"未经同意克隆他人声音"的条款**，但**合规责任在使用者**。**使用前请确认授权与当地法律。**
- **模型很大**：**Drama 3 为 35B-A3B**（每 token 激活 3B），**本地跑需要可观显存 / 内存**；README 推荐用 **SGLang-Omni / vLLM-Omni** 提速——**消费级单卡能否跑要先按规模评估**。
- **README 有原生多语言版**：仓库 `docs/` 下有 `README.zh.md`、`README.ja.md`、`README.ko.md`、`README.pt-BR.md`、`README.ar.md`、`README.es.md` 等。
- **我只读了 README 与 `LICENSE`**：**未 clone、未下载权重、未跑任何推理、未试听音频、未访问 speech.fish.audio / fish.audio / Hugging Face**；模型规模、评测数字与许可条款均来自 README 与 `LICENSE` 正文，**未逐一验证，也未就许可咨询法律意见**。

## 什么时候会再看

- 想找一个**社区活跃、迭代快**的开源 TTS / 克隆系统（尤其在意**多说话人、长内容、可编辑 / infilling**）时。
- 想参考"**多轮导演式修订**（'更温柔一点'就重录）"与"**词级语音编辑**"这类**把音频当文本改**的交互时。
- 想研究"**语音模型的开源许可怎么设计**"——**营收门槛 + Hosted Service 单独授权 + 署名与"Built with Fish Audio"义务**，是一个不同于 Apache / MIT 的模板。
- 想对照本组其它语音项目选型：[Qwen3-TTS](Qwen3-TTS-阿里开源的端到端语音生成与克隆模型.md)（**Apache-2.0、10 语言、3 秒克隆**）· [VibeVoice](VibeVoice-微软开源语音AI的TTS下架与ASR演进.md)（**TTS 已下架**）· [free-voice-clone](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)（清单里 **Fish Audio S2 Pro** 的许可被标为 **Fish Audio Research License**）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同组三个开源语音项目**：[Qwen3-TTS：阿里开源的端到端语音生成与克隆模型](Qwen3-TTS-阿里开源的端到端语音生成与克隆模型.md)（**Apache-2.0、10 语言、可商用**）与 [VibeVoice：微软开源语音 AI 的 TTS 下架与 ASR 演进](VibeVoice-微软开源语音AI的TTS下架与ASR演进.md)（**TTS 已下架**）——**三篇的许可证与开放尺度各不相同**（社区许可带门槛 / Apache-2.0 / 下架），**选型时建议三篇对着读**。
- **与"清单类"笔记的关系**：[free-voice-clone：本地免费语音克隆与音乐生成模型清单](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)——那份清单的 TTS 板块收录了 **Fish Audio S2 Pro**（许可标注为 **Fish Audio Research License**），**可视为本篇在"模型清单"里的条目**。
- **同属本地语音软件侧**：[voicebox：本地优先的开源 AI 语音工作室](voicebox-本地优先的开源AI语音工作室.md)——它把多个第三方 TTS 引擎统一进一个本地 App（其一为 Qwen3-TTS），**是"把这类引擎做成成品"的另一种路线**。
- **同为"代码许可 ≠ 权重许可"的开源模型仓库，模态不同**：[RelateAnything：实时预测开放词汇视觉关系的开源模型](RelateAnything-实时开放词汇视觉关系预测的开源模型.md)——那篇是**视觉**关系模型（代码 AGPL-3.0-only + NOTICE、权重是 Meta DINOv3 衍生、标注带 Gemma 条款、检测器另按 ultralytics AGPL）；本篇是**语音**模型（代码 FACL v1.0、S2-Pro 权重另按 Research License）。两篇可对照"**一个仓库里代码 / 权重 / 数据 / 第三方组件各挂不同许可**"的写法。

## 参考来源

- 仓库主页与 README（10,998 字节；核验日期 2026-11-13）：https://github.com/fishaudio/fish-speech
- 许可证：`LICENSE`（**Fish Audio Community License Agreement v1.0**，114 行，"Last Updated: October 13, 2026"；许可方 **39 AI, INC.**；正文含营收门槛、Hosted Service、署名、禁止用途、准据法等条款）
- 模型权重与报告：<https://huggingface.co/fishaudio/drama-3> · <https://huggingface.co/fishaudio/s2-pro> · arXiv:2411.01156（v1.4）· arXiv:2603.08823（S2）· arXiv:2610.18010（Drama 3）
- 安装与推理文档：<https://speech.fish.audio/install/> · <https://speech.fish.audio/inference/> · 官网 <https://fish.audio>
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13）；Issue 数与贡献者数取自核验记录（同日）

以上信息核验于 2026-11-13。**未 clone、未下载模型权重、未跑任何推理、未试听任何音频、未访问 speech.fish.audio / fish.audio / Hugging Face**；模型规模、评测数字与许可条款均来自 README 与 `LICENSE` 正文，**未逐一验证，未就许可咨询法律意见**；**S2-Pro 权重按 README 自述另受 Fish Audio Research License，与其代码许可并不相同。**
