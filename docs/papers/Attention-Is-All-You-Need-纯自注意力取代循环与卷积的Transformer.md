# Attention Is All You Need：用纯自注意力取代循环与卷积的 Transformer

> 提出 Transformer 的那篇论文——当前几乎所有大模型的架构原点。值得读的地方不在结论本身，而在它把"为什么可以完全并行、注意力到底怎么算、代价从哪转移到哪"一次讲清。

- 状态：草稿
- 最近核验：2026-11-03（读 arXiv 记录页、v7 版 HTML 目录与致谢、NIPS 2017 论文页，并取 OpenAlex 与 GitHub API 数据；**未逐节重读正文**）
- 纳入方向：大模型与 Agent

## 元信息

| 项 | 内容 |
| --- | --- |
| arXiv | 1706.03762v7（v1 提交于 2017-06-12，v7 修订于 2023-08-02，共 7 个版本） |
| 首次提交 / 最近更新 | 2017-06-12 / 2023-08-02 |
| 作者与机构 | Ashish Vaswani、Noam Shazeer、Niki Parmar、Jakob Uszkoreit、Llion Jones、Aidan N. Gomez、Łukasz Kaiser、Illia Polosukhin 共 8 人；OpenAlex 记录一作机构为 Google Brain |
| 发表状态 | **已发表于 NeurIPS 2017**（Advances in Neural Information Processing Systems 30），同页另有 arXiv 预印本 |
| 代码与数据 | 论文本身未给官方代码仓库。致谢中提到模型变体"在原始代码库和 tensor2tensor 中"实现；`tensorflow/tensor2tensor` 已于 2023-06-02 后停止推送并**归档**（Apache-2.0，17,471 Star，见下） |
| 引用量 | **120,404**（来源：OpenAlex，核验于 2026-11-03。该数字对应 arXiv DOI 条目，OpenAlex 是否把会议版另计一条我没有核验，故不宜当作"全网总被引"） |
| 许可 | arXiv 默认非独占分发许可 `nonexclusive-distrib/1.0` |
| 篇幅 | 15 页、5 张图（arXiv 标注） |

## 核心结论

- 论文自述：当时的序列转换模型以循环或卷积网络为主，最好的模型还要靠注意力机制连接编码器与解码器。本文提出一个**只用注意力机制**的简单架构 Transformer，完全去掉循环与卷积。在两项机器翻译任务上，质量更好、更容易并行、训练时间显著更短：WMT 2014 英德达 **28.4 BLEU**（比当时最好结果含集成模型再高 2 BLEU 以上），英法达单模型 **41.8 BLEU**（8 块 GPU 训练 3.5 天）；并成功迁移到英语成分句法分析，说明泛化性不限于翻译。
- 我的判断：这篇的价值随时间被放大了——它当年是在机器翻译上做的一个"更快的架构"，后来成了语言模型乃至多模态模型的事实底座。今天回头读，最该带走的是三件事：**自注意力的计算方式与复杂度**、**多头机制为什么按"子空间"拆**、**位置编码如何补回被去掉的顺序信息**。至于"取代循环与卷积"这个说法，要放回它当时的语境理解——在 2017 年的任务和规模上成立，不等于在任意序列长度和任意任务上都成立（后来自注意力的长序列代价、以及各种线性注意力、状态空间模型的复兴，说明这个取舍一直在被重新谈判）。

## 论文结构（v7）

| 节 | 内容 |
| --- | --- |
| 1 Introduction | 提出动机 |
| 2 Background | 与既有序列模型、注意力工作的关系 |
| 3 Model Architecture | 3.1 编码器与解码器堆叠；3.2 注意力（3.2.1 缩放点积注意力、3.2.2 多头注意力、3.2.3 本模型中的三种用法）；3.3 逐位置前馈网络；3.4 词嵌入与 Softmax；3.5 位置编码 |
| 4 Why Self-Attention | 自注意力与循环、卷积在计算复杂度、并行度、路径长度上的对比 |
| 5 Training | 5.1 训练数据与分批；5.2 硬件与训练计划；5.3 优化器；5.4 正则化（残差 Dropout、标签平滑） |
| 6 Results | 6.1 机器翻译；6.2 模型变体消融；6.3 英语成分句法分析 |
| 7 Conclusion | 结论与展望 |
| 附录 | Attention Visualizations（注意力可视化） |

## 版本差异（引用数据前务必确认取哪一版）

- **arXiv v7** 摘要：英德 **28.4 BLEU**、英法 **41.8 BLEU**，8 块 GPU 训练 3.5 天。
- **NIPS 2017 会议页**摘要：165M 参数单模型英德 **27.5 BLEU**、英法 **41.1 BLEU**，比此前单模型最好高 0.7 BLEU。
- 两个版本的数字对不上，属于论文持续修订的典型情形。**引用具体指标时必须写明版本与出处**，否则会出现"引用同一篇论文却报不同分数"的情况。

## 是否仍然成立

- 架构层面：结论不仅成立，而且被极其广泛地验证——此后十余年的主流语言模型几乎都建立在它的骨架上。
- 论文里的**实验数字**已不是 SOTA，引用时应作为"历史基线"而非当前最好结果。
- 论文里的**论证**仍可读：第 4 节关于复杂度与并行度的对比、第 6.2 节的模型变体消融，是理解"哪些设计真正重要"的核心部分。
- 我没有查到针对本文的复现争议，但也没有专门核验；本条为基于公开印象的表述，不算已验证事实。

## 使用前需要注意

- **论文没提供官方实现**：正文与致谢只提到 tensor2tensor，没有给出仓库链接。想读实现要另找——`tensorflow/tensor2tensor` 虽标注 Apache-2.0、仍可读，但已于 2023-06-02 之后无推送并**处于归档状态**（GitHub API，核验于 2026-11-03：17,471 Star、3,805 Fork、589 个开放 Issue、9 位订阅者、默认分支 `master`）。当年能复现的环境与今天的依赖栈差距很大，直接跑通未必顺利。
- **许可**：arXiv 默认 `nonexclusive-distrib/1.0`，作者只授权 arXiv 分发，**没有**给第三方商用、改写、翻译或再发布的权限。想大段引用或翻译需先联系作者。注意这与代码仓库的 Apache-2.0 是两回事，代码许可能覆盖的范围以仓库声明为准。
- **引用量口径**：120,404 是 OpenAlex 针对该 arXiv DOI 条目的数字，与 Google Scholar 口径会有出入，且不排除会议版被单列。要写进正式材料前建议再交叉核对一次。
- **本笔记的核验边界**：只读了记录页、v7 的 HTML 目录与致谢、会议页与第三方书目数据，**没有逐节重读正文**。"我的判断"一节混入了既有背景知识，未逐条回到原文核验。

## 相关笔记

- 索引位置：[论文收藏](README.md)。
- **把 Transformer 放进完整课程的中文教材**：[Foundations-of-LLMs：大模型基础教材](../tools/github-projects/Foundations-of-LLMs-大模型基础教材.md)——第 2 章按 Encoder-only / Encoder-Decoder / Decoder-only 梳理架构，可与本篇对照"原始架构"与"后来的分叉"。
- **自己动手实现一遍**：[GPT_teacher：从零训练中文小 GPT](../tools/github-projects/GPT_teacher-3.37M-cn-从零训练中文小GPT.md)——那是能跑起来的最小 GPT 实现，本篇给的是架构与论证；对着看能把公式落到代码。
- **同为讲生成式模型数学基础**：[生成式 AI 基础小书：从 PCA 到扩散模型的推导路线](生成式AI基础小书-从PCA到扩散模型的推导路线.md)——那本第 7 章讲自回归分解，而 Transformer 正是现代自回归模型所用的骨架。

## 参考来源

- arXiv 记录页（版本历史与摘要）：https://arxiv.org/abs/1706.03762
- v7 HTML 版（本笔记的章节结构与 tensor2tensor 出处取自这里）：https://arxiv.org/html/1706.03762v7
- NeurIPS 2017 论文页（会议版摘要，数字与 arXiv v7 不同）：https://papers.nips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html
- DOI：https://doi.org/10.48550/arXiv.1706.03762
- 许可全文：http://arxiv.org/licenses/nonexclusive-distrib/1.0/
- 引用量：OpenAlex 记录 `W2626778328`，https://api.openalex.org/works/https://doi.org/10.48550/arXiv.1706.03762
- 相关代码仓库（已归档）：https://github.com/tensorflow/tensor2tensor

以上信息核验于 2026-11-03。这个 arXiv 条目可能会再出新版本，引用指标与版本号前请重新确认。
