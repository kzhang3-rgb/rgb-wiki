# minimind：2 小时从零训练一个 64M 小 LLM 的中文项目

> 记录它怎么把"从零预训练 + 全流程后训练"压进一套能跑通的开源教程：不依赖 `transformers` / `trl` / `peft` 封装，核心算法自己实现，单卡 3090 约 3 元成本即可跑通一个最小对话模型。

- 状态：草稿
- 最近核验：2026-11-05（阅读中英文 README 章节结构、目录树与 GitHub API 元数据，未克隆、未安装、未训练、未下载权重）
- 适用范围：Python（README 示例用 3.10.16）+ PyTorch；CPU / MPS 可跑但速度差异大，推荐 CUDA 单卡；训练脚本支持 DDP / DeepSpeed 多卡

## 核心结论

MiniMind 的定位不是"又一个 nanoGPT 复刻"，而是**把大模型训练的全流程摊开成可运行的教材**：它的主张是不用 `transformers` / `trl` / `peft` 这类高层封装，**关键算法与核心模块从 0 实现**，让读者从每一行代码开始理解 LLM——README 原话是"从**零开始训练**，而不是仅仅停留在**推理**层面"，并给了一个很具体的门槛数字：**最低不到 3 块钱的服务器成本**。

它覆盖的流程比一般教学项目长得多：Tokenizer 训练 → Pretrain → SFT → LoRA → RLHF（DPO）→ RLAIF（PPO / GRPO / CISPO / SPO）→ Tool Use → Agentic RL → 知识蒸馏，且**主线结构已对齐 `Qwen3 / Qwen3-MoE` 生态**，方便后续转成 `transformers` / `llama.cpp` / `ollama` / `vllm` 可直接加载的形态。

当前主线模型 `minimind-3` 是 **64M 参数的 Dense Decoder-only**（结构：Pre-Norm + RMSNorm、SwiGLU、RoPE 且支持 YaRN 外推、`q_heads=8` / `kv_heads=4`、`max_position_embeddings=32768`、`rope_theta=1e6`）；另有 `minimind-3-moe`（198M 总参 / 64M 激活，`4 experts / top-1`，去掉了 shared expert）。README 给的训练开销估算：单卡 3090 上 `pretrain_t2t_mini` + `sft_t2t_mini` 约 **2.31 小时 / 约 3.0 元**，即可得到一个 `minimind-3 Zero` 对话模型——但 README 自己也说这个版本"事实知识与泛化效果仍较有限"，是**验证 Zero 路线可行性**的参考，不是能用的助手。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [jingyaogong/minimind](https://github.com/jingyaogong/minimind) |
| 主页 | <https://jingyaogong.github.io/minimind> |
| 描述 | 🧠 Train a 64M-parameter LLM from scratch in just 2h! |
| 许可证 | Apache-2.0（`LICENSE` 11,357 字节） |
| 语言 | Python |
| 关注度 | **64,878 Star、8,488 Fork、288 关注、61 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2024-07-27 创建**，最近推送 **2026-10-27**（持续维护两年）；默认分支 **`master`**（另有 `website` 分支） |
| 发行版 | **仅 2 个 Release**（`v2` 2025-10-21、`minimind-v1` 2024-09-02），**均为 0 资产** |
| 贡献者 | **24 人**，主力 `jingyaogong` 一人 222 次提交 |
| 体积 | API `size` 约 39,476 KB（约 39 MB）；默认分支树实测 49 个 blob / 约 12,251 KB（大头是 `images/`，仅 `minimind-3.gif` 一个就 5.99 MB） |
| 主题标签 | `artificial-intelligence`、`large-language-model`；已开启 GitHub Discussions |

## 它能做什么

- **一条龙训练链路**（`trainer/`）：`train_tokenizer.py`（自训 BPE + ByteLevel 分词器，支持 `<tool_call>` / `<tool_response>` / `<think>` 等标记）→ `train_pretrain.py` → `train_full_sft.py` → `train_lora.py` → `train_dpo.py` → `train_ppo.py` / `train_grpo.py`（含独立 `rollout_engine.py`）→ `train_agent.py`（多轮 Tool-Use 的 GRPO / CISPO）→ `train_distillation.py`。
- **模型定义**（`model/`）：`model_minimind.py`（18,986 字节，Dense + MoE 主线）、`model_lora.py`、`tokenizer.json`（451 KB）。
- **推理与接入口**（`scripts/`）：`eval_llm.py` CLI 推理；`serve_openai_api.py` 兼容 OpenAI 协议（支持 `reasoning_content` / `tool_calls` / `open_thinking`，可接 FastGPT、Open-WebUI 等）；`web_demo.py` 基于 Streamlit 的极简聊天界面；`convert_model.py` 合并 LoRA 权重；`eval_toolcall.py` 测 Tool Call。
- **开源数据与权重**：数据在 ModelScope（`gongjy/minimind_dataset`），权重在 ModelScope / HuggingFace（如 `jingyaogong/minimind-3`），**不放在本仓库**。
- **断点续训**：所有训练脚本支持 `--from_resume 1`，检查点存 `./checkpoints/`，支持跨 GPU 数量恢复与 wandb 记录连续。
- **实验性拓展**：离散扩散语言模型（dLM）与线性注意力（Linear Attention），可基于主线 AR 模型续训。
- 配套孪生项目 **minimind-v**（多模态视觉）在另一仓库。

## 用法要点

README 的主路径是"下载模型 → CLI 推理"和"下载数据 → 训练"两条：

```bash
# 第 0 步：克隆（README 用浅克隆）并安装依赖
git clone --depth 1 https://github.com/jingyaogong/minimind
cd minimind && pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple

# 推理：拉 transformers 格式权重后直接跑
python eval_llm.py --load_from ./minimind-3

# 训练：默认只需下载 pretrain_t2t_mini.jsonl 与 sft_t2t_mini.jsonl
cd trainer && python train_pretrain.py
python train_full_sft.py
```

README 明确"当前默认仅需下载两个 mini 数据文件，即可较快复现 MiniMind Zero 对话模型"，其余数据搭配方案按任务与 GPU 资源自选。第三方引擎也可直接跑：`ollama run jingyaogong/minimind-3`、`vllm serve /path/to/model --served-model-name "minimind"`。

## 使用前需要注意

- **"2 小时 64M" 是训练开销估算，不是效果承诺**：README 把成本与耗时写得很细（3090 约 `1.3￥/h`、`7￥ ≈ 1 美元`），并注明"均为估算值，仅用于快速感知训练门槛"。同时它自己贴的 Zero 模型样例对话里，英文回答已经明显不通顺——**这正是它想展示的"最低门槛跑通"，不是可用能力**。别把 64,878 Star 当成"这模型好用"。
- **权重与数据都不在 GitHub**：仓库只放代码、文档与图片；模型权重、训练数据集都依赖 ModelScope / HuggingFace 下载。**GitHub Release 只有 2 个且 0 资产**，别指望从 Release 页拿权重。
- **两份 README 体量极大**（`README.md` 约 138 KB、`README_en.md` 约 153 KB）：内容确实完整，但体积反常——**阅读或抓取时要注意**，不宜整篇读入。
- **默认分支是 `master` 而非 `main`**：按 `main` 克隆会失败或拿到非预期分支，脚本化拉取时需写明分支。
- **版本改名与兼容坑史**：模型命名经过 `minimind-v1` → `minimind2` → `minimind-3` 三次大改；2025-04-26 的更新明确写了"**不再支持直接加载 25-04-26 以前的旧模型**，`minimind-v1` 全系列停止维护并在仓库下线"。**引用旧教程或旧权重时务必对版本**。
- **对第三方框架的取舍是明说的**：`minimind-3-moe` 的 README 主动解释——原生 PyTorch 下 MoE 训练因 kernel 启停与调度开销，`4 experts / top-1` 大约比同尺寸 Dense 慢 50%，并直说"这和 MoE 推理更快放在一起看会有点反直觉"，想更快得靠 Triton / DeepSpeed-MoE / Megatron-LM 的融合算子。
- **API 报体积与默认分支树体积差距大**（39 MB vs 约 12 MB）：这是口径差异（API `size` 通常含完整 Git 历史），不是仓库真有 39 MB 现值。
- **我未克隆、未安装、未训练、未下载任何权重**：上述耗时、成本、样例对话与"2 小时复现"均为 README 自述，未独立验证。

## 什么时候会再看

- 想**亲手跑一遍 LLM 全流程**（分词器 → 预训练 → SFT → LoRA → DPO → RLHF/RLAIF → Tool Use → Agentic RL → 蒸馏），又不想被高层框架封装挡住内部实现时。
- 想参考**一份"从 0 实现、结构对齐 Qwen3"的小模型代码库**如何组织目录（`model/` / `trainer/` / `scripts/` / `dataset/`）与训练脚本时。
- 想快速拿一个**能被 `transformers` / `llama.cpp` / `ollama` / `vllm` 直接加载的小模型**做端侧或教学演示时。
- 想对照"**从零预训练**（本篇）与"**从零手写 Agent**"（NamiAgent）、"**从零训练更小的 GPT**"（GPT_teacher）三种"从零"路线的取舍时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"从零训练一个极小的中文模型"**（[GPT_teacher：在 CPU 上从零训练一个中文小 GPT](GPT_teacher-3.37M-cn-从零训练中文小GPT.md)）——那个是 3.37M 参数、CPU 45 分钟可跑通、带"Transformer 透视镜"的课堂教具；本篇是 64M、单卡 3090 约 2 小时、覆盖完整后训练链路的工程教程。**规模与深度不同，可对照"教具"与"全流程"两种从零路线**。
- **同为"中文 + 能跑起来"的教学式仓库**（[NamiAgent：从零手写 Agent 的中文可运行教程](NamiAgent-从零手写Agent的中文可运行教程.md)）——那是从零手写 Agent（循环 / 工具 / 权限 / 记忆），本篇是从零训练模型本身；两者都走"每步可运行 + 不依赖框架封装"的路子，可对照"训练一个模型"与"驱动一个模型"两件事怎么教。
- **大模型原理的系统教材**（[Foundations-of-LLMs：大模型基础教材](Foundations-of-LLMs-大模型基础教材.md)）——一个讲原理与架构分叉（PDF 分发、偏理论），一个给能跑全流程的最小实现；对着读能补上"书里那一节对应代码里哪一段"。
- **同为"小模型 / 端侧"方向的另一条路线**（[kev：一次前向输出校准概率的小模型](kev-一次前向输出概率的小模型.md)）——那个是 0.5B 上挂 LoRA 做结构化决策（不生成文本、只出概率），本篇是从零预训练做生成；可用于对比"微调做决策"与"预训练做生成"的取舍。
- **模型结构那套架构的原始论文**（[Attention Is All You Need](../../papers/Attention-Is-All-You-Need-纯自注意力取代循环与卷积的Transformer.md)）——本篇的 Decoder-only 结构（Pre-Norm / RMSNorm / RoPE / SwiGLU）都源自那一支；想搞清"代码里每一层对应论文哪一跳"，两篇对着看最省事。
- **把 "Agentic RL" 这一节单独拆开做实验的仓库**（[agentic-rl-lab：用 PyTRIO 复现 LLM / Agent 强化学习算法的中文实验手册](agentic-rl-lab-复现LLMAgent的强化学习算法实验手册.md)）——本篇链路里的 Agentic RL 只是其中一段，那仓库把这一段扩成 16 个章节（GRPO / DAPO / GSPO / OPSD / Search-R1 / ReTool / ALFWorld…），并用远程 Train API 回避多卡环境。想深入某一篇 RL 算法时从那边找最小实现。
- **同为"从零训练一个小 LLM"，但走"跟着书逐节手写"的路线**（[LLMs-from-scratch：从零手写并训练一个 GPT 式 LLM 的官方配套代码仓库](LLMs-from-scratch-从零手写并训练一个GPT式LLM的官方配套代码仓库.md)）——本篇是 64M、单卡 3090 约 2 小时、覆盖完整后训练链路的工程教程；那是英文商业书的可运行配套代码，强调"不用外部 LLM 库、与纸书逐节对应"。可对照"工程全流程"与"教学逐节实现"两种从零路线。

## 参考来源

- 项目仓库：https://github.com/jingyaogong/minimind
- 主页与在线体验：<https://jingyaogong.github.io/minimind>；ModelScope 创空间 <https://www.modelscope.cn/studios/gongjy/minimind>
- 数据与权重的托管处：ModelScope `gongjy/minimind_dataset`、`gongjy/minimind-3`；HuggingFace `jingyaogong/minimind-3`
- 孪生项目（多模态）：https://github.com/jingyaogong/minimind-v
- 元数据（Star / Fork / 时间 / 许可证 / 分支 / Release / 贡献者）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读 README 章节结构（中英文各约 138 KB / 153 KB，未整篇精读）、目录树与元数据；未克隆、未安装依赖、未训练、未下载权重，训练耗时、成本与样例对话均为作者自述。
