# LLMs-from-scratch：从零手写并训练一个 GPT 式 LLM 的官方配套代码仓库

> 一本讲"从零实现大语言模型"的英文书的**官方代码仓库**：不用任何现成 LLM 库，用 PyTorch 从分词、注意力一路写到 GPT、预训练与微调。它值得记的不只是"能跑"，而是它作为一个**教材配套仓库**如何组织章节、如何保证代码与纸书一致、以及它已经把大量后续内容（KV 缓存、MoE、Llama / Qwen3 / Gemma 等架构复刻）作为 bonus 收进来。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 README 全文、`LICENSE.txt`、`requirements.txt`、文件清单（364 项）、Release 列表与 ungh 镜像 / shields 徽章元数据；**未 clone、未安装依赖、未运行任何 notebook 或脚本、未训练任何模型、未审计源码**）
- 适用范围：有 Python 基础、想**动手理解 LLM 内部构造**的读者；书中主章节代码设计为**普通笔记本即可在合理时间内跑完**，有 GPU 会自动使用；需要 PyTorch（附录 A 有 PyTorch 入门）

## 核心结论

**一句话定位（仓库 description）**：`Implement a ChatGPT-like LLM in PyTorch from scratch, step by step`——它是 Sebastian Raschka 所著《Build a Large Language Model (From Scratch)》（Manning，2024，ISBN 978-1633437166）的**官方配套代码**，目标是"把 LLM 从里到外讲清楚、并让你亲手一步步写出来"。

**五个最值得记的点：**

1. **"从零"是真的从零**：README 明确"**不使用任何外部 LLM 库**"，核心实现在 PyTorch 里手写；与之对照，它也提供"**加载更大预训练模型的权重来微调**"的代码，两条路并存。
2. **一条主线走完 LLM 全链路**：Ch2 处理文本数据 → Ch3 手写注意力机制 → Ch4 从零实现 GPT 模型 → Ch5 在无标注数据上**预训练** → Ch6 微调做**文本分类** → Ch7 微调**遵循指令**；再配附录 A（PyTorch 入门）、D（训练循环加料）、E（LoRA 参数高效微调）。
3. **Bonus 材料已经远超一本小书**：仓库把大量延伸内容收成可选章节——**BPE 从零实现**、多套高效多头注意力对比、**KV 缓存**、**注意力替代方案**（GQA / MLA / 滑窗 / Gated DeltaNet / DeepSeek 稀疏注意力 / 跨层 KV 共享）、**MoE**、**GPT → Llama 转换**，以及 **Llama 3.2 / Qwen3（含 MoE）/ Gemma 3 / Olmo 3 / Tiny Aya / Qwen3.5 / Gemma 4 各自"从零"复刻**、Muon 优化器、DPO、内存高效权重加载等。
4. **它是一个"工程化的教材配套仓库"，不只是代码堆**：有独立的可安装包 `pkg/llms_from_scratch/`（含按章的 Python 模块 + `llama3.py` / `qwen3.py` + `kv_cache/` + 一整套 `tests/`），有 `setup/`（Python 环境偏好、Docker、AWS SageMaker），有 `.github/` 里十几条 CI 工作流（Linux / macOS / Windows × uv / pip / pixi、旧版 PyTorch、PyTorch RC、链接检查、拼写检查、pep8 等）。
5. **它明确"不接受扩展正文的贡献"**：README 说明因为这是**印刷书**的配套代码，为保持与纸书一致，**当前不接受会扩展主章节代码的 PR**；反馈与提问请走 GitHub Discussions 或 Manning 论坛。这是"书 + 代码"这类仓库特有的维护策略。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) |
| 描述 | `Implement a ChatGPT-like LLM in PyTorch from scratch, step by step` |
| 作者 | Sebastian Raschka（README 引用的作者，配套书作者） |
| 配套书 | 《Build a Large Language Model (From Scratch)》，Manning，2024，ISBN 978-1633437166 |
| 许可证 | **Apache-2.0**（根目录 `LICENSE.txt`，11,505 字节，正文为 Apache License 2.0）——**注意文件名是 `.txt` 而非无后缀，shields 的 license 徽章显示 `not identifiable by github`**（识别失败 ≠ 无许可） |
| 主语言 | **Jupyter Notebook 72.8%**（shields 顶层语言，代码以 notebook 为主、Python 模块为辅） |
| 关注度 | **108,619 Star / 872 关注 / 16,803 Fork**（ungh 镜像，2026-11-19；shields 同期 **109k / 17k**） |
| 贡献者 | **73**（shields） |
| 待办 | **1 个开放 Issue**（shields，2026-11-19；仓库主要走 Discussions） |
| 维护状态 | **2023-07-23 创建**；pushed **2026-11-09**、updated **2026-11-18**（**活跃**） |
| 默认分支 | `main` |
| 版本 | **0 Release、0 Tag**（代码直接跟 `main`，引用时应记录具体提交） |
| 体积 | `main` 文件树共 **364 个文件**；顶层目录：`ch05` 100 · `ch04` 53 · `ch07` 46 · `pkg` 39 · `ch06` 28 · `ch02` 22 · `.github` 20 · `setup` 15 · `ch03` 11 · `appendix-A` 8 · `appendix-D` 4 · `appendix-E` 4 · `ch01` 2 · `appendix-C` 1 · `appendix-B` 1 |
| 根文件 | `README.md`(19,004 B)、`LICENSE.txt`(11,505 B)、`requirements.txt`、`pyproject.toml`、`pixi.toml`、`conftest.py`、`CITATION.cff`、`troubleshooting.md`、`.gitignore`、`.gitmodules` |

## 章节结构（README 目录）

| 章 / 附录 | 主题 | 关键文件（README 指向） |
| --- | --- | --- |
| Ch 1 | Understanding Large Language Models | 无代码 |
| Ch 2 | Working with Text Data | `ch02.ipynb`、`dataloader.ipynb`、练习解答 |
| Ch 3 | Coding Attention Mechanisms | `ch03.ipynb`、`multihead-attention.ipynb` |
| Ch 4 | Implementing a GPT Model from Scratch | `ch04.ipynb`、`gpt.py` |
| Ch 5 | Pretraining on Unlabeled Data | `ch05.ipynb`、`gpt_train.py`、`gpt_generate.py` |
| Ch 6 | Finetuning for Text Classification | `ch06.ipynb`、`gpt_class_finetune.py` |
| Ch 7 | Finetuning to Follow Instructions | `ch07.ipynb`、`gpt_instruction_finetuning.py`、`ollama_evaluate.py` |
| Appendix A | Introduction to PyTorch | `code-part1/2.ipynb`、`DDP-script.py` |
| Appendix B | References and Further Reading | 无代码 |
| Appendix C | Exercise Solutions | 练习解答索引 |
| Appendix D | Adding Bells and Whistles to the Training Loop | `appendix-D.ipynb` |
| Appendix E | Parameter-efficient Finetuning with LoRA | `appendix-E.ipynb` |

## 运行环境（README 与 `requirements.txt` 自述）

- **前提是扎实的 Python 基础**；PyTorch 熟练度不是硬门槛（附录 A 有入门，作者另有《PyTorch in One Hour》）。
- **主章节代码设计为普通笔记本即可在合理时间内跑完**、不要求专用硬件，有 GPU 会自动使用。
- 依赖（`requirements.txt`）：**`torch >= 2.2.2`**（Intel macOS 上限制 `<2.6`）、`jupyterlab >= 4.0`、`tiktoken`（ch02/04/05）、`matplotlib`（ch04/06/07）、**`tensorflow`**（ch05/06/07）、`tqdm`、`numpy`、`pandas`（ch06）、`psutil`（ch07）。
- 仓库另有 `setup/` 目录讲环境准备（pixi / uv、包安装、Docker、AWS SageMaker），并配 `troubleshooting.md`。

## 使用前需要注意

- **许可的"识别失败"不要误读**：根目录是 `LICENSE.txt`（Apache-2.0 全文），但 shields 的 license 徽章报 `not identifiable by github`——这是**文件名 / 识别机制的问题，不代表没有许可**。复用前以 `LICENSE.txt` 正文为准。
- **0 Release、0 Tag**：代码只跟 `main` 分支演进，引用具体路径或行号时应**记录你拉到的提交**，否则后续对不上。
- **它是"书"的配套代码**：README 明确**不接受扩展主章节代码的贡献**（要保持与印刷书一致）。所以"仓库没实现某功能"不等于"作者不会做"，也不代表社区能补进来；想跟进请走 Discussions / 论坛。
- **依赖并不轻**：`requirements.txt` 里同时有 `torch` 与 `tensorflow`（后者用于 ch05/06/07），第一次装环境就会拉不少包；Intel macOS 上还对 torch 版本单独设了上限。
- **"从零"指的是不依赖现成 LLM 库**，不是"不算力"：从零**预训练**与微调仍需要相应算力；bonus 里加载更大模型权重、跑 DPO、复刻 Llama / Qwen3 / Gemma 等对资源的要求明显更高。
- **本笔记只读了文档与元数据**：章节内容、notebook 能否顺利跑通、示例依赖是否有版本坑，均**未实测**；README 自述的"普通笔记本可跑完"属作者陈述。
- **关注度数据是快照**：108,619 Star 为 2026-11-19 的 ungh 镜像值（shields 同期 109k），会继续变化。

## 什么时候会再看

- 想**系统、动手地理解 LLM 内部构造**（分词 → 注意力 → GPT → 预训练 → 微调）时，把它作为主线教材的配套代码。
- 想找"**某一章讲的东西对应哪份 notebook / 哪段代码**"时（README 的目录表就是索引）。
- 想了解**注意力替代方案、MoE、KV 缓存、把 GPT 改成 Llama / Qwen3 / Gemma 等具体架构**在最小可读实现里长什么样时（bonus 章节）。
- 想参考"**一本书的官方代码仓库如何组织**"——章节目录 + 可安装包 + 完整 CI + 与纸书一致性策略——时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为大模型系统教材，中外互补**：[Foundations-of-LLMs：大模型基础教材](Foundations-of-LLMs-大模型基础教材.md)——那是浙大团队的中文教材（PDF 分发、讲原理与架构分叉），本篇是英文书的**可运行配套代码**（notebook 手写实现）。一个偏"读"，一个偏"动手写"，对照看能覆盖"原理"与"实现"两侧。
- **同为"从零训练一个小 LLM"，规模与取向不同**：[minimind：2 小时从零训练一个 64M 小 LLM 的中文项目](minimind-从零训练64M小LLM的中文项目.md)——那是中文、单卡 3090 约 2 小时、覆盖 Pretrain→SFT→LoRA→RLHF/RLAIF→Agentic RL 全链路的工程教程；本篇是英文教材的逐步实现、强调"**不用外部 LLM 库、与纸书逐节对应**"。可对照"工程全流程"与"教学逐节实现"两种从零路线。
- **同为"从零训练中文小 GPT"的教学教具**：[GPT_teacher：3.37M 中文小 GPT 的从零训练教具](GPT_teacher-3.37M-cn-从零训练中文小GPT.md)——那是 3.37M、CPU 45 分钟、带"Transformer 透视镜"的课堂教具；本篇是配套商业书的完整实现。可对照"极低门槛的可观测教具"与"体系化教材代码"的取舍。
- **同属教材 / 系统方向，与本篇接续**：[cs249r_book：哈佛 ML Systems 开源教材四卷与配套工具](cs249r_book-ML-Systems开源教材四卷与配套工具.md)——本篇讲**模型本身**（并给出可运行实现），那篇讲"**模型之外**"的机器学习系统（硬件 / 编译 / 推理 / 服务 / 边缘 / 运维）；其中的 `TinyTorch`（从零写 ML 框架）与本篇的"从零"取向可对照。

## 参考来源

- 项目仓库与 README：https://github.com/rasbt/LLMs-from-scratch
- 配套书（Manning）：https://www.manning.com/books/build-a-large-language-model-from-scratch
- 后续书《Build A Reasoning Model (From Scratch)》配套仓库：https://github.com/rasbt/reasoning-from-scratch
- 许可证（Apache-2.0）：https://github.com/rasbt/LLMs-from-scratch/blob/main/LICENSE.txt
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / Release 数 / 文件清单）：ungh.cc 镜像 https://ungh.cc/repos/rasbt/LLMs-from-scratch 与 shields.io 徽章

以上信息核验于 2026-11-19，Star 数与仓库内容会持续变化。仅阅读 README、`LICENSE.txt`、`requirements.txt`、文件清单与元数据；未 clone、未安装依赖、未运行 notebook 或脚本、未训练模型、未审计源码。
