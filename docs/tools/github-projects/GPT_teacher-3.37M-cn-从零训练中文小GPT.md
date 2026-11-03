# GPT_teacher-3.37M-cn：在 CPU 上从零训练一个中文小 GPT

> 记录它"把训练过程做成教具"的思路——自带 Transformer 透视镜，能看见自己模型的逐层注意力和 top-5 预测。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README 与 GitHub API 元数据，未克隆、未训练、未运行 Demo）
- 适用范围：Python 3.10–3.12；CPU / MPS（Apple Silicon）/ CUDA；依赖管理用 uv

## 核心结论

一个**面向课堂教学**的项目：目标是让初学者用一台普通 CPU 电脑，在 45 分钟内从零跑通一个小参数中文 GPT——"看清核心流程、跑通训练、得到可用的中文回答，并支持简单的推理演示"。

模型本身很小：**3.37M 参数（含权重共享）、4 层 Decoder-only Transformer**，架构用的是当前主流做法（GQA + SwiGLU + RMSNorm + RoPE + 权重共享）。

真正让它区别于"又一个 nanoGPT 复刻"的是 **Transformer 透视镜**：Web Demo 里能看到自己模型的真实内部——token 色块 → 逐层注意力 → 下一个字的 top-5 预测，还能做"跳层 / 层数 / 注意力温度"三联对照的干预实验，以及逐头热力图，**数据全部来自 `best.pt` 的真实前向**，不是预先画好的示意图。

配套的工程细节也做得比一般教学项目扎实：中文 ByteLevel BPE 分词器从零训练并可视化词表使用情况、ONNX 导出可用 Netron 打开完整计算图、27 条金样本回归用来量化"改动后行为是否退化"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [helloworldtang/GPT_teacher-3.37M-cn](https://github.com/helloworldtang/GPT_teacher-3.37M-cn) |
| 作者 | 少年唐（helloworldtang） |
| 许可证 | MIT（© 2025 少年唐） |
| 语言 / 依赖 | Python 3.10–3.12；`uv` 管理依赖（`uv sync`） |
| 关注度 | 256 Star、63 Fork、2 关注、0 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2025-11-15 创建，最近推送 2026-09-16 |
| 体积 | **约 216 MB**（`size` 字段 221,547 KB，不含 Git 历史） |
| 模块 | `core/`（模型、分词器、推理引擎、评估、可视化）、`train/`（训练与 Web Demo）、`distill/`（知识蒸馏） |

## 自述指标（未验证）

| 指标 | 自述结果 |
| --- | --- |
| 参数量 | 3.37M（含权重共享），4 层 Decoder-only Transformer |
| 训练耗时 | MPS 3–6 分钟 / CUDA 5–10 分钟 / CPU 30–60 分钟 |
| 验收测试 | 6/6 通过（`uv run python -m core.evaluate`） |
| 行为回归 | 27 条金样本（`train/scripts/regression.py`） |
| 单元测试 | 56 个（`uv run pytest core/tests/`） |

生成样例（`temperature=0`）：

```text
问: 什么是注意力机制？
答: 注意力机制通过计算查询和键的相关性分配权重，让模型动态关注最相关的部分。
```

**快速开始**（README 给的一条命令链）：

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv python install 3.11
uv sync
uv run python train/run.py   # 分词器 → 训练 → 验收 → Web Demo
```

**蒸馏模块**是另一个玩法：用大模型做 teacher 做知识蒸馏实验，README 给的例子是 `uv run python -m distill.train --kd --teacher Qwen/Qwen2.5-1.5B-Instruct`。

## 使用前需要注意

- **"可用的中文回答"要按教学标准理解**：3.37M 参数且只训练几十分钟的模型，能稳定应对的是训练分布内的提问模板；README 展示的示例回答读起来更像"训练语料里的标准答案"，**不要当成通用助手能力**。这是判断，不是事实——判断依据是参数量与训练时长，没有实测。
- **仓库体积约 216 MB**：对于这个参数量级来说异常大，推测主要是训练数据、权重与文档图片。**建议用浅克隆（`git clone --depth 1`）**，否则拉取会明显变慢；这也是本站"不在 Git 里放大体积二进制"规则的一个现实例子。
- **没有 Release、没有 Tag、star 量小**：项目仍在演进（最近推送 2026-09-16，创建于 2025-11-15），接口与目录结构可能随教学节奏变动；**引用具体路径（如 `train/run.py`）时应以你拉到的版本为准**。
- **金样本回归是它最有借鉴价值的机制**：27 条固定样本用来量化"改动后模型行为变化"，这个思路可以搬到任何需要防止静默退化的模型项目上——但**样本本身由作者定义，覆盖面有限**（27 条对一个中文模型来说远不足以说明能力边界）。
- **离线与算力**：训练在 CPU 上即可完成（这是它的卖点），但 `uv sync` 与蒸馏实验仍需要联网拉依赖与 teacher 权重；蒸馏用 1.5B 模型时对显存/内存的要求已明显高于主训练流程。
- **我只读了 README，没有克隆与训练**：上述耗时、测试通过数、透视镜功能均未验证。

## 什么时候会再看

- 想**给自己或别人讲清楚 Transformer 到底怎么跑**时：透视镜这种"用自己训练的模型做教具"的做法，比看静态示意图有效。
- 想找**能在普通电脑上从零训练的最小可运行 LLM 项目**时：CPU 可训、一键跑通是它最实际的卖点。
- 想参考**教学项目的工程化程度能到什么水平**时：分词器可视化、ONNX 导出、金样本回归、单元测试，这几项一起做掉的教学项目并不多。
- 想对比**"从零预训练小模型"与"用大模型做蒸馏"**两条路线时：它的 `distill/` 模块在一个仓库里提供了对照。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 大模型原理的中文系统教材：[Foundations-of-LLMs](Foundations-of-LLMs-大模型基础教材.md)——一个讲原理，一个给能跑起来的最小实现，可以对着看。
- 同为小模型方向的另一条路线：[kev](kev-一次前向输出概率的小模型.md)——那是用 LoRA 微调 0.5B 模型做分类，本篇是从零训练 3.37M 做生成，可用于对比"微调"与"预训练"的取舍。
- 本篇实现的架构来源：[Attention Is All You Need](../../papers/Attention-Is-All-You-Need-纯自注意力取代循环与卷积的Transformer.md)——那是提出 Transformer 的原始论文（arXiv 1706.03762，已发表于 NeurIPS 2017），给了架构与论证，本篇是能跑起来的最小实现；想搞清"代码里的每一层对应论文哪一节"，两篇对着看最省事。

## 参考来源

- 项目仓库与 README：https://github.com/helloworldtang/GPT_teacher-3.37M-cn
- 训练模块文档：`train/README.md`；蒸馏模块文档：`distill/README.md`
- 元数据（Star / Fork / 体积 / 时间）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未克隆、未训练，全部效果指标均为作者自述。
