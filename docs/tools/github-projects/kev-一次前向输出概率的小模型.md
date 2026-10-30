# kev：一次前向输出校准概率的小模型

> 记录一个和"聊天式调用大模型"完全不同的思路：不生成文本，只用一次前向算出每个选项的概率。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、模型卡与目录结构，未安装运行、未下载权重）
- 适用范围：Python 3.12+ 与 uv；作者在 Apple Silicon（MPS）上测试，**CUDA 未测试**

## 核心结论

`kev` 是一个 0.5B 规模的**结构化决策模型**：输入一份文档加若干"带类型的问题"，输出每题的选项概率分布——不是生成文字，而是给概率。

做法是在 Qwen2.5-0.5B 上挂一个 LoRA 适配器和一个指针读出头，把文档和所有问题**打成一个序列做一次前向**，用 block-causal 掩码让每道题只看文档、看不到其他题，再用 softmax 得出每题各选项的概率。概率是用交叉熵训练出来的，所以是"学出来的"而不是"生成出来的"。

它的架构是对 TypeSafe 的 Jev 的复刻（依据一篇架构分析文章），API 也照着 TypeSafe 的 System One 契约做，所以官方 `typesafe-sdk` 改一个 `base_url` 就能直接对本地的 kev 服务跑通。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [jaredpalmer/kev](https://github.com/jaredpalmer/kev) |
| 作者 | Jared Palmer（Formik、Razzle 等前端项目的作者） |
| 许可证 | Apache-2.0 |
| 技术栈 | Python 3.12+、uv、PyTorch；playground 用 Node 20+ |
| 关注度 | 678 Star、39 Fork、1 人关注、3 个开放 Issue（2026-10-30 的 API 数据） |
| 维护状态 | 2026-09-17 创建，**43 次提交**，3 个分支、1 个 Tag（v0.1.0）；API 显示最近推送 2026-09-20 |
| 权重 | Hugging Face [`jaredpalmer/kev-0.5b`](https://huggingface.co/jaredpalmer/kev-0.5b)，基座 Qwen2.5-0.5B；GitHub Release 也挂了 `kev-0.5b.tar.gz` |

## 它和"调用聊天大模型"的区别

把一堆分类、打标、路由任务交给 LLM 时，常见做法是让模型逐条输出文本再解析，慢且不稳定。kev 走的是另一条路：

- **一次前向，多个答案**：文档只编码一次，问题作为互相隔离的分支并行计算，不需要逐题解码。
- **隔离是精确的**：某道题看不到兄弟题。作者称打包发送与分开发送的结果差异在 `4e-6` 量级。
- **输出是概率而非散文**：需要的是"这单该给哪个部门"这种判断时，概率分布比一句话更可用。
- **可校准**：作者给出留出集 ECE 0.065，做一次单参数温度缩放后为 0.031。

代价是它**只能做决策，不能对话**。它不适合当通用助手，适合替代"用大模型做分类/打分/路由"这条链路。

## 三种问题类型

| 类型 | 含义 | 输出字段 |
| --- | --- | --- |
| `noul` | 是 / 否 | `noul`（即 `p[yes]`） |
| `choice` | 2 至 255 个选项的单选题 | `choice`、`probabilities`、`confidence` |
| `score` | 有序等级打分 | `score`、`legend`、`probabilities`、`confidence` |

`confidence` 由 `(p_max − 1/K) / (1 − 1/K)` 推出——即在 K 个选项里"比瞎猜好多少"。这一点值得留意：**选项越多，同样的 `confidence` 含义不同**，跨题比较时不能直接比这个数。

## 本地运行

```bash
git clone https://github.com/jaredpalmer/kev.git && cd kev
uv sync --extra serve
cd playground && npm install && cd ..

# 启动服务（权重从 HF 拉取，首次会下载基座模型）
uv run --extra serve python -m kev.serve --run jaredpalmer/kev-0.5b --port 8009
```

请求示例（送一段客服工单，同时问三个问题）：

```bash
curl -s localhost:8009/v1/systemone -H 'content-type: application/json' -d '{
  "state": "Shoes arrived two weeks late and in the wrong size. Also I see two charges on my card.",
  "model": "kev-latest",
  "questions": {
    "department": {"type": "choice", "instructions": "Which team should handle this?",
      "criteria": {"returns": "Exchanges, refunds, wrong or damaged items",
                   "shipping": "Delivery status, delays, lost packages",
                   "billing": "Charges, invoices, payment problems"}},
    "escalate": {"type": "noul", "instructions": "Does this need urgent human attention?"},
    "frustration": {"type": "score", "instructions": "How frustrated is the customer?",
      "criteria": ["Calm", "Frustrated", "Very angry"]}
  }}'
```

也可用 TypeSafe 官方 SDK，只改 `base_url` 指向本地端口。另有 playground（包含一个把国际象棋合法走法当作 Choice 选项的演示），以及 `/v1/models`、`/systemone/permute`（同一题换多种选项顺序）、`/systemone/separate`（每题单独跑一遍，用于对照）三个辅助端点。

## 训练与复现

训练数据是六个公开数据集转成 TypeSafe 请求格式：Banking77、AG News、MNLI、BoolQ、SST-5、Yelp，共 9,000 条记录、13,500 个问题、两个 epoch。

```bash
# 冒烟跑，约 1 分钟
uv run python -m kev.train --n_per_source 40 --accum 4 --out runs/smoke

# kev-0.5b，作者称在 M5 上约 1 小时 45 分
uv run python -m kev.train --n_per_source 1500 --epochs 2 --perm_kl 0 --ord_w 0 --out runs/kev
```

作者明确写了：**数据转换与采样方式已经变过，重跑这条命令无法精确复现已发布权重**。这种"直接承认不可复现"的写法在同类项目里不常见，值得记一笔。更长的训练走 Modal 上的 H100，本地 Mac 建议一次只跑一个训练任务（两个任务会让 GPU 慢约 10 倍）。

## 使用前需要注意

- **CUDA 未测试**：作者只在 Apple Silicon（MPS）上验证过。如果你手里是 NVIDIA 卡，等于走在作者没走的路径上。
- **服务没有鉴权**：README 明确写"There is no authentication. The server is intended for local use."——只能本地或可信网络内使用，别暴露到公网。
- **权重不可精确复现**：如上所述，重跑训练命令得到的是接近而非相同的模型。
- **项目很年轻且已暂停更新**：2026-09-17 创建，43 次提交集中在最初几天，最近推送为 2026-09-20（距今约六周）。属于"一次集中开发后停下"的状态，依赖前建议先确认是否仍在维护。
- **效果数字均为作者自测**：ECE 0.065 / 0.031、`4e-6` 的隔离一致性、M5 上的 1h45m 与约 160ms 延迟，都没有第三方复现，我也没有验证。
- **它解决的是分类型任务**：六个训练数据集分别对应意图分类、新闻分类、自然语言推理、是非问答、情感分级。换到差异较大的领域（比如判读股票或法律文本）属于分布外使用，效果未知。
- **架构是"复刻"而来**：README 说明架构依据一篇对 TypeSafe Jev 的逆向分析文章，API 对齐 TypeSafe 的对外契约。这说明它是一份独立实现，与 TypeSafe 官方产品无隶属关系。
- 我未安装运行、未下载权重，以上均为文档信息。

## 什么时候会再看

- 需要把"用大模型做分类 / 路由 / 打分"落地时——这是一条和生成式调用不同的技术路线，可做对比方案。
- 想参考**用一次前向并行答复多道题**的实现方式，或 block-causal 掩码 + 指针读出的做法时。
- 想参考一个小型研究项目如何组织评测套件（`evals/decision-v2`、`evals/transfer-v2`）、如何记录"trial 对应哪个 commit 与套件哈希"时。
- 想学一段诚实的 README 写法时——它在开头就承认无法精确复现权重、CUDA 未测试、服务无鉴权。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 同属"在本地跑小模型/端侧"方向：[deepseek-harness](deepseek-harness-DeepSeek开源Agent框架.md)（Agent 框架，非模型本身）。

## 参考来源

- 项目仓库与 README：https://github.com/jaredpalmer/kev
- 权重：https://huggingface.co/jaredpalmer/kev-0.5b
- 架构分析文章（README 引用）：https://archerhume.com/posts/jevs-architecture-unmasked
- TypeSafe System One API 文档（README 引用）：https://docs.typesafe.ai/api

以上信息核验于 2026-10-30，Star 数与仓库内容可能变化。未安装运行、未下载权重，效果数字均未验证。
