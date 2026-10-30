# heretic：全自动去审查工具

> 记录它做什么、怎么做，以及这类工具在使用上绕不开的合规问题。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README 与目录结构，未安装运行、未下载或生成任何模型）
- 适用范围：Python 3.10+、PyTorch 2.2+；实测数据来自 RTX 5090 / RTX 3090，显存不足可用量化

## 核心结论

`heretic` 用来移除 transformer 大模型的安全对齐（作者称"去审查"），走的是权重层面的**方向性消融**（directional ablation，社区称 abliteration）而不是昂贵的再训练。

它在已有消融方法上加了两点：把消融参数化，并用 Optuna 的 TPE 优化器自动搜索参数，优化目标是**同时压低拒绝次数与相对原模型的 KL 散度**。也就是说：让模型少拒绝，同时尽量别把原来的能力搞坏。整个过程全自动，不需要使用者理解 transformer 内部结构。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [p-e-w/heretic](https://github.com/p-e-w/heretic) |
| 许可证 | AGPL-3.0 |
| 技术栈 | Python、PyTorch、Optuna、Transformers；依赖用 uv 管理并带 `uv.lock` |
| 关注度 | 3,100 Star、275 Fork、19 人关注（2026-10-30 的 API 数据） |
| 维护状态 | 2025-09-21 创建，158 次提交，6 个分支、4 个 Tag；含 CI、Dependabot、`.gemini` 等工程配置 |
| 安装 | `pip install -U heretic-llm` |

> 数据与口径说明：**这次是 API 明显滞后**——API 显示最近推送为 2025-11-21，而页面上的最新提交是 2026-05-22（Dependabot 升级 urllib3）；开放 Issue 数两处也不同（API 17 / 页面 55）。和前面几篇"网页滞后"的情况正好相反，说明抓取来源本身不稳定，见文末。

## 支持的模型范围

- 大多数 dense 模型，**含部分多模态模型**；
- **若干 MoE 架构**；
- 部分混合架构（README 举例 Qwen3.5）；
- 纯状态空间模型（state-space）和某些研究性架构**尚不支持**。

## 作者给出的效果对比

README 用 gemma-3-12b-it 做对比，两列分别是"对有害提示的拒绝次数"和"对无害提示相对原模型的 KL 散度"：

| 模型 | 拒绝次数 | KL 散度 |
| --- | --- | --- |
| google/gemma-3-12b-it（原始） | 97/100 | 0（定义值） |
| mlabonne/gemma-3-12b-it-abliterated-v2 | 3/100 | 1.04 |
| huihui-ai/gemma-3-12b-it-abliterated | 3/100 | 0.45 |
| **p-e-w/gemma-3-12b-it-heretic（本项目）** | **3/100** | **0.16** |

作者的结论是：拒绝抑制水平与人工专家做的消融相当，但 KL 散度低得多，即对原能力的破坏更小。数字可以用内置评测复现（`heretic --model ... --evaluate-model ...`），作者也说明**具体数值依赖平台与硬件**，该表来自 PyTorch 2.8 + RTX 5090。

作者同时写了"数学指标和自动基准从来讲不全故事，不能替代人工评估"——这句话是对的，下面几条仍需要注意。

## 使用方法

```bash
pip install -U heretic-llm
heretic Qwen/Qwen3-4B-Instruct-2507   # 换成想处理的模型
```

- 默认配置下全自动，无需调参；要细调可看 `heretic --help` 或 `config.default.toml`。
- 运行开始时会自动测硬件、选最优 batch size。作者参考值：RTX 3090 上默认配置处理 Qwen3-4B 约 20–30 分钟。
- 显存不够可开 `quantization = "bnb_4bit"`（bitsandbytes）。
- 完成后可选择：保存模型、上传到 Hugging Face、对话测试、跑标准基准，或任意组合。
- 部分模型有额外版本要求：如加载 MXFP4 量化的 gpt-oss 需要 PyTorch 2.6+ 的 `torch.accelerator`，而最低要求写的是 2.2。

### 附带的研究功能

装 `heretic-llm[research]` 后可用两个可解释性功能：

- `--plot-residuals`：对每层"有害/无害"提示的首 token 残差做 PaCMAP 降维，逐层输出 PNG 并生成展示层间变换的动画 GIF。注意 PaCMAP 在 CPU 上算，大模型可能跑一小时以上。
- `--print-residual-geometry`：输出逐层表格，含好坏提示残差均值与几何中位数的余弦相似度、模长、轮廓系数等。

对关注模型内部机制（而非只是想去掉审查）的人来说，这两个功能可能比主功能更有价值。

## 使用前需要注意

- **用途与合规需要自己判断**：这个工具的目标就是移除模型的安全对齐。使用者要自行承担后果——包括是否违反原模型提供方的许可协议与使用条款。作者的 README 只讲了方法出处与效果，**没有讨论任何合规问题**，这一点本身就是信息。
- **上传模型时还有一层许可问题**：程序提供"上传到 Hugging Face"的选项，但改过的权重能否再分发，取决于**原模型自己的许可证**，不取决于 heretic 的 AGPL。公开发布前需要单独确认。
- **效果数据来自作者自测与自引的社区评论**：对比表是作者跑的（并说明依赖硬件），"用户反馈好"部分引用的是 Reddit 评论，独立基准的引用同样是 Reddit 帖子而非同行评审。社区已产出 3,000+ 个 heretic 模型（HF 上按 `heretic` 标签统计），说明用得广，但不等于效果被严格验证。
- **优化目标不等于目标本身**：自动优化只盯着"拒绝次数"和"KL 散度"两个可测指标。指标变好不等于模型变得更可靠，也不等于它在别的维度上没被破坏。
- **AGPL-3.0**：自己本地使用没有额外义务；若把它改造后对外提供网络服务，需要履行 AGPL 的开源义务。
- 我未安装运行、未生成任何模型，以上均为文档信息。

## 什么时候会再看

- 需要理解 **abileration（方向性消融）** 这套方法的完整实现时——包括 refusal direction 如何取值、消融权重核如何参数化、如何用 TPE 自动搜索。
- 想参考"把超参搜索引入模型编辑流程"的做法，而不只是手工调参时。
- 想参考它做**模型内部的残差几何可视化**（`--plot-residuals` / `--print-residual-geometry`）用于可解释性分析时。
- 想学一个项目的工程规范时：uv + `uv.lock` 锁依赖、CI、Dependabot、可复现的评测命令。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 主题相关的另一篇：[jailbreaks](jailbreaks-大模型越狱提示词集合.md)——同属"绕过模型安全机制"，但层级不同：那篇是**提示词层面**的集合，本篇是**权重层面**的方法，后者不依赖具体模型版本的提示词技巧，因此不会随模型更新迅速失效。
- 同为"在本地处理模型权重/小模型"方向：[kev](kev-一次前向输出概率的小模型.md)。

## 参考来源

- 项目仓库与 README：https://github.com/p-e-w/heretic
- 方法出处（README 引用）：Arditi et al. 2024，https://arxiv.org/abs/2406.11717
- 权重方法与投影式 abliteration 说明（README 引用）：https://huggingface.co/blog/grimjim/projected-abliteration 、https://huggingface.co/blog/grimjim/norm-preserving-biprojected-abliteration
- 社区产出模型汇总：https://huggingface.co/models?other=heretic

## 关于抓取来源的一条补充

本篇遇到的情况与之前相反：这次 **API 数据比页面陈旧**（API 显示 2025-11 最后推送，页面已有 2026-05 的提交）。加上此前出现过的"页面滞后""Star 相差 160 倍"等情况，可以确认：**任何单一抓取的关注度与活跃度数据都不可靠**。

因此本笔记只把这类数字当作量级参考，并在意活跃度时以"页面上的最新提交时间"为主依据；需要精确判断时直接打开 GitHub 页面确认。

以上信息核验于 2026-10-30。未安装运行，效果数据均未验证。
