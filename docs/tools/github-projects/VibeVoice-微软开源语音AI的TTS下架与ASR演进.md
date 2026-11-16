# VibeVoice：微软开源语音 AI 的 TTS 下架与 ASR 演进

> 一句话定位（README 自述）：**"开源前沿语音 AI"**——微软开源的一个**语音模型家族**，核心创新是 **7.5Hz 超低帧率的连续语音 tokenizer（声学 + 语义）** 与 **next-token diffusion** 框架。最值得记的有三点：① **这是本篇最重要的风险点——TTS 代码已于 `2025-09-05` 因滥用被移除**（README 原文："we have removed the VibeVoice-TTS code from this repository"），**仓库现存的是 ASR 系**（ASR-7B / ASR-Streaming / ASR-BitNet-CPU）与 **Realtime-0.5B TTS**；② **现役主力是长音频 ASR**：单次可吃 **60 分钟**音频（64K token），输出"**谁在何时说了什么**"，支持**自定义热词**与**流式识别**、**50+ 语言**；③ **许可证是 MIT（只覆盖仓库代码），README 明确"不建议用于商业或真实场景"**、定位"仅供研究开发"，并列出深度伪造 / 虚假信息风险。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（13,031 字节）、`LICENSE` 全文、仓库文件树（111 个条目）与元数据；**未 clone、未下载模型权重、未跑任何推理、未试听音频、未访问 Hugging Face / Colab / Azure AI Foundry**）
- 适用范围：**研究与开发取向**（README 明写"仅供研究与开发""不建议在未做进一步测试前用于商业或真实场景"）；ASR-7B 面向 **60 分钟长音频单次转写**，Realtime-0.5B 面向**实时流式 TTS**，ASR-BitNet 面向 **CPU 边缘推理**（≥3 线程、无需 GPU）；基础模型为 **Qwen2.5 1.5b**

## 核心结论

1. **⚠️ 最重要的风险：TTS 已下架（事实，README News）**：`2025-09-05`，微软称"发布后发现该工具被以不符合声明意图的方式使用，出于负责任使用 AI 的原则，**已从本仓库移除 VibeVoice-TTS 代码**"。**这意味着仓库里拿不到 TTS 推理代码了**；README 仍保留 TTS 的介绍段落与 HF 权重链接，但模型表中 **VibeVoice-TTS-1.5B 的 "Quick Try" 列为 `Disabled`**。
2. **它现在是"ASR 系为主 + 一个实时 TTS"**（README 模型表）：

| 模型 | 权重 | Quick Try |
| --- | --- | --- |
| VibeVoice-ASR-7B | HF | Playground（`aka.ms/vibevoice-asr`） |
| VibeVoice-ASR-Streaming | HF | 文档 |
| VibeVoice-ASR-BitNet（CPU） | HF | VibeASR.cpp |
| VibeVoice-TTS-1.5B | HF | **Disabled** |
| VibeVoice-Realtime-0.5B | HF | Colab |

3. **核心技术是 7.5Hz tokenizer + next-token diffusion**（README Overview）：用**声学 + 语义两条连续语音 tokenizer**、帧率低至 **7.5 Hz**，在保真度与长序列计算效率间取平衡；用 **LLM 理解上下文 / 对话流**、**diffusion head 生成声学细节**。
4. **现役能力（README 自述）**：
   - **ASR**：单次 **60 分钟**（64K token）、**谁 / 何时 / 什么**结构化输出、**自定义热词**、**流式变体**、**50+ 语言**；可微调（`finetuning-asr/`），支持 vLLM。
   - **Realtime-0.5B（TTS）**：0.5B、**首音约 300ms 延迟**、支持**流式文本输入**、长语音约 **10 分钟**。
   - **ASR-BitNet（CPU）**：异构量化（I8_S + I2_S），**4.62GB → 1.58GB**，≥3 线程时 **RTF < 1**、无需 GPU。
5. **许可与立场（README）**：仓库 **MIT**；README 有 **⚠️ Risks and Limitations** 段，写明可能产生意外 / 偏见 / 不准确输出、**深度伪造与虚假信息风险**、**不建议商业或真实场景使用**、"**仅供研究与开发**"、基础模型为 **Qwen2.5 1.5b**。

**个人判断（未验证）**：本项目最值得记的不是"能做什么"，而是"**一次真实的下架**"——它是研究"**开源语音模型的滥用治理如何落地**"的现成案例：一家大厂**先开源 TTS、两个月后仅移除 TTS 代码、保留 ASR 与实时 TTS**。**想用它的 TTS 功能，本仓库已不是入口**（须去 Hugging Face 找权重，但推理代码需自备，且 README 的 TTS 段落已不再对应仓库内的可用代码）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [microsoft/VibeVoice](https://github.com/microsoft/VibeVoice) |
| 描述 | README 标题 "Open-Source Frontier Voice AI"：微软开源的前沿语音 AI 模型家族，**目前以 ASR 系为主**，另含 Realtime-0.5B TTS |
| 许可证 | **MIT**（`LICENSE` 1,071 字符，署名 `Copyright (c) 2025 Microsoft`）——**只覆盖仓库代码；各模型权重自身的许可本轮未单独核对**（以 Hugging Face 模型页为准） |
| 主语言 | **未在本轮核验中取到**（材料仅含 README、`LICENSE`、文件树与元数据） |
| 关注度 | **57,036 Star / 6,432 Fork / 276 Watcher**（**页面值**，2026-11-13）；开放 Issue **132**、贡献者 **22**（同日） |
| 维护状态 | **2025-08-25 创建**；页面 `updatedAt` **2026-11-11**，ungh `pushedAt` **2026-10-08**（**代码侧近期仍在推送**）；`main`；页面报 **0 Release** |
| 默认分支 | `main` |
| 版本 | **0 Release**（无发行版；以 Hugging Face 权重与 `docs/` 文档为分发口径） |
| 体积 | `main` 分支 **111 个条目**（ungh.cc files API；含 `docs/`、`finetuning-asr/`、`Figures/`、`demo/` 等，未逐个列出） |
| 报告 | TTS：ICLR 2026 Oral（<https://openreview.net/forum?id=FihSkzyxdv>）· ASR：arXiv:2601.18184 · Streaming ASR：arXiv:2609.02812 · BitNet：arXiv:2607.21075 |
| 项目页 | <https://microsoft.github.io/VibeVoice> |

## 时间线与现状（README News）

| 日期 | 事件 |
| --- | --- |
| 2026-09-03 | 发布 **VibeVoice-ASR-Streaming**（统一流式 ASR，持续转写"谁说了什么"，支持热词与 10 语言） |
| 2026-07-23 | 发布 **VibeVoice-ASR-BitNet**（边缘 CPU 推理；4.62GB → 1.58GB，RTF < 1） |
| 2026-03-12 | VibeVoice-ASR 集成进 **Azure AI Foundry Labs** |
| 2026-03-06 | VibeVoice-ASR 进入 **Transformers** 发布 |
| 2026-01-21 | 开源 **VibeVoice-ASR**（单次 60 分钟、Who/When/What、50+ 语言） |
| 2025-12-16 | 为 **Realtime-0.5B** 增加实验性音色（9 语言 + 11 种英语风格） |
| 2025-12-03 | 开源 **VibeVoice-Realtime-0.5B**（实时 TTS，流式文本输入） |
| **2025-09-05** | **⚠️ 因滥用移除 VibeVoice-TTS 代码** |
| 2025-08-25 | 开源 **VibeVoice-TTS**（最长 90 分钟、最多 4 说话人；ICLR 2026 Oral） |

## 模型与能力

- **ASR-7B**：统一语音转写，**单次 60 分钟**（64K token 长度内），输出 **Who（说话人）/ When（时间戳）/ What（内容）**，支持**自定义热词**与**流式识别**，**50+ 语言**；可微调、可用 vLLM 加速。
- **ASR-BitNet（CPU）**：面向边缘设备，异构量化后 **1.58GB**、≥3 CPU 线程即 **RTF < 1**、**无需 GPU**。
- **Realtime-0.5B**：轻量实时 TTS，**约 300ms 首音延迟**、支持**流式文本输入**、长语音约 **10 分钟**；另有实验性音色。
- **TTS-1.5B**：README 仍保留介绍（最长 **90 分钟**、最多 **4 说话人**、表达力 / 多语言），但 **Quick Try 已标为 `Disabled`，且代码已从仓库移除**。

## 使用前需要注意

- **⚠️ 不要照着 README 的 TTS 段落找代码**：`VibeVoice-TTS` 的**推理代码已于 2025-09-05 移除**；README 里 TTS 介绍段与 HF 链接还在，但**仓库内已无 TTS 实现**。**要复现需自备推理代码，风险与成本自担。**
- **⚠️ README 明写"仅供研究与开发"**：`Risks and Limitations` 段声明**不建议在未做进一步测试前用于商业或真实场景**，并列出**深度伪造 / 虚假信息**风险，要求使用者**核实转写、依法使用、披露 AI 生成**。
- **⚠️ 开放 Issue 132 个**：以 5.7 万 Star 的体量看积压偏多，**上手前建议先扫高频问题**。
- **MIT 只管代码**：仓库为 MIT，但**各模型权重（Hugging Face 页）自身的许可本轮未核对**——**要商用请逐个到 Hugging Face 模型页确认**。
- **ASR 才是当前主线**：README 顶部 News 与 Overview 都以 ASR 为主；**若你需要的是 TTS，这个仓库已不再是合适入口**。
- **我只读了 README 与 `LICENSE`**：**未 clone、未下载权重、未跑任何推理、未试听音频、未访问 Hugging Face / Colab / Azure AI Foundry**；时间线、能力与数字均来自 README 自述，**未逐一验证**。

## 什么时候会再看

- 想研究"**开源语音模型的滥用治理**"如何落地——**一次真实、可核查的下架案例**（2025-08-25 开源 TTS → 2025-09-05 移除 TTS 代码）。
- 想找一个**长音频（单次 60 分钟）ASR**（带说话人 + 时间戳 + 热词）时。
- 想看 **7.5Hz 超低帧率语音 tokenizer + next-token diffusion** 这套设计思路时。
- 想对照本组其它语音项目：[Qwen3-TTS](Qwen3-TTS-阿里开源的端到端语音生成与克隆模型.md)（**TTS 仍在仓**）· [fish-speech](fish-speech-Fish-Audio的开源语音生成与克隆模型.md)（**Drama 3 / S2-Pro + 社区许可**）· [free-voice-clone](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)（其 ASR 板块也收录了 **VibeVoice-ASR**）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同组三个开源语音项目**：[Qwen3-TTS：阿里开源的端到端语音生成与克隆模型](Qwen3-TTS-阿里开源的端到端语音生成与克隆模型.md)（**TTS 仍在仓、Apache-2.0、10 语言**）与 [fish-speech：Fish Audio 的开源语音生成与克隆模型](fish-speech-Fish-Audio的开源语音生成与克隆模型.md)（**Drama 3 / S2-Pro、社区许可带营收门槛**）——**三篇正好凑齐"在仓 / 下架 / 社区许可"三种开放尺度**，本篇是**TTS 已下架**的那一个。
- **与"清单类"笔记的关系**：[free-voice-clone：本地免费语音克隆与音乐生成模型清单](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)——那份清单的 **ASR 板块**收录了 **VibeVoice-ASR（微软，7B，50+ 语言）**，**可视为本篇在"模型清单"里的条目**。
- **同属给语音能力做本地化的软件侧**：[voicebox：本地优先的开源 AI 语音工作室](voicebox-本地优先的开源AI语音工作室.md)——它把多个 TTS 引擎（含 **Qwen3-TTS**、Chatterbox 等）统一进一个本地 App，**正好是"把这类引擎装进成品"的另一种路线**。

## 参考来源

- 仓库主页与 README（13,031 字节；核验日期 2026-11-13）：https://github.com/microsoft/VibeVoice
- 许可证：`LICENSE`（MIT，`Copyright (c) 2025 Microsoft`）
- 项目页：<https://microsoft.github.io/VibeVoice> · Hugging Face 合集：<https://huggingface.co/collections/microsoft/vibevoice-68a2ef24a875c44be47b034f>
- 报告：TTS <https://openreview.net/forum?id=FihSkzyxdv>（ICLR 2026 Oral）· ASR arXiv:2601.18184 · Streaming ASR arXiv:2609.02812 · BitNet arXiv:2607.21075
- 关键事实（**TTS 代码于 2025-09-05 因滥用被移除**）：README `📰 News` 段落原文引用
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13）；Issue 数与贡献者数取自核验记录（同日）

以上信息核验于 2026-11-13。**未 clone、未下载模型权重、未跑任何推理、未试听任何音频、未访问 Hugging Face / Colab / Azure AI Foundry**；时间线、模型能力与数字均来自 README 自述，**未逐一验证**；**仓库代码为 MIT，各模型权重自身的许可未在本轮单独核对**；**TTS 推理代码已于 2025-09-05 从本仓库移除，README 中的 TTS 介绍段不再对应仓库内的可用代码。**
