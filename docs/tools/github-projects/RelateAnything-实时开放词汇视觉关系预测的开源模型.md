# RelateAnything：实时预测开放词汇视觉关系的开源模型

> 记录一个把**"一张图 + 物体框 + 你想要的关系词"变成带分数的 `(主体, 关系, 客体)` 三元组**的开源视觉模型（`Maelic/RelateAnything`，描述 "RelateAnything: Real-Time Open-Vocabulary Relation Prediction From Any Inputs"）：给它**你自选的区域**（可来自检测器 / 分割器 / 人工标注，掩码可选）和一份**推理时现给的关系词表**，它返回 `(person) --riding [0.76]--> (horse)` 这样的**关系图**——**换词表不用重训**。三条先记住：**关系词表在推理时给、由一个小文本编码器编码一次，不做逐帧语言模型**；**一次前向可返回一张排序列表，或拆成空间图与语义图两张**；**代码是 AGPL-3.0-only + 附加署名条款、权重是 Meta DINOv3 衍生、训练标注带 Gemma 条款——许可要分层看**。未安装、未运行、未复现任何基准。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `main` 分支 `README.md` 全文（258 行）、`LICENSE` 首行（确认 AGPL-3.0）、文件清单（348 项）与 ungh 镜像 / shields 徽章；**未 clone、未安装、未下载权重、未运行推理、未复现任何延迟 / 精度数字、未审计源码**）
- 适用范围：**开源研究模型 + Python 代码库**（Python 3.12+，包名 `relsgg`）；提供 PyTorch API（CPU / CUDA）、TensorRT / ONNX / OpenVINO 导出、以及**浏览器演示（WebGPU / WASM）**；面向**图像 / 视频帧上的开放词汇视觉关系（场景图）预测**

## 核心结论

**一句话定位（API 描述）**：`RelateAnything: Real-Time Open-Vocabulary Relation Prediction From Any Inputs`——中文可说"**给一张图、一批物体框和一份关系词表，实时给出它们之间带分数的主谓宾关系**"。

**① 输入是"图 + 区域 + 词表"，输出是"带分数的三元组"。** README：把**一张图片、物体区域、你想找的关系**交给它，返回带分数的 `(subject, relation, object)` 三元组，例如 `person → riding → horse`。**区域由你用**（框可来自检测器、分割器或人工，掩码可选）——**它自己不做目标检测**。

**② 关系词表在推理时给，不用重训。** README：**在推理时提供短语即可，无需重训**；一个小文本编码器把这些短语编码一次，**每帧不跑语言模型**（`no language model runs per frame`）——这是它"实时"的关键。

**③ 一次前向，一张图或两张图。** README：可返回**单个排序列表**，也可从**同一次前向**里拆出**空间图（spatial）与语义图（semantic）**两张——代码中 `decompose=True` 即得 `graphs["spatial"]` 与 `graphs["semantic"]`（如"在哪里" vs "在做什么"）。

**④ 架构（README "How it works"）：DINOv3 骨干 + 区域 token + 配对采样 + 词表打分头。** 一个 **DINOv3** 骨干读图一次；区域变成视觉 token；一个**配对采样器**挑出候选关系；一个 **transformer** 结合场景细化它们；一个**词表头（vocabulary head）**把每对与你的谓词字符串的 embedding 打分，**混合空间与语义特征**。**换掉这个 embedding bank 就换了词表，而无需重训视觉模型。**

**⑤ 七个 checkpoint，同一训练配方、不同视觉骨干。** README 推荐 **`relsgg-vits16plus`**（质量 / 速度平衡，53.2 M）；要更准用 **`relsgg-convnext-base`**（据称在 ConvNeXt-L 的一半参数内逼近其 0.3 OVS）。

| Checkpoint | Backbone | 参数 |
| --- | --- | ---: |
| `relsgg-vits16` | DINOv3 ViT-S/16 | 46.1 M |
| **`relsgg-vits16plus`** | **DINOv3 ViT-S/16+** | **53.2 M** |
| `relsgg-vitb16` | DINOv3 ViT-B/16 | 113.8 M |
| `relsgg-convnext-tiny` | DINOv3 ConvNeXt-T | 58.6 M |
| `relsgg-convnext-small` | DINOv3 ConvNeXt-S | 80.2 M |
| `relsgg-convnext-base` | DINOv3 ConvNeXt-B | 123.5 M |
| `relsgg-convnext-large` | DINOv3 ConvNeXt-L | 245.7 M |

**⑥ 延迟与结果（均为 README 自述、未复现）。** 延迟：**RTX 3080 Laptop + TensorRT** 下关系模型 **14.7 ms（ConvNeXt-T）/ 16.8 ms（ViT-S+）**，全 **19,103 谓词词表**最多再 +1.5 ms；**含 YOLO26m 检测端到端 33.5 ms / 38.7 ms**；**单张 A100** 编译后含检测 **25.6 ms/图**（README 对比 OvSGTR 232 ms、ACC 498 ms、ROBIN-3B 约 40 s）。结果：ViT-S+ 用真值框、每有序对一个关系时，**VG150 16.5→36.9 · PSG 13.5→34.7 · IndoorVG 20.2→37.8**（对比 OvSGTR F1@50；README 注明 **OvSGTR 拿到物体标签、RelateAnything 不给**）。

**⑦ 关注度与状态（ungh 快照 2026-11-19）。** **1,296 Star / 178 Fork / 14 关注**（shields 同期 1.3k / 178）；**5 个开放 Issue、贡献者 2 人**（shields）；**2026-09-05 创建**、pushed **2026-11-14**、updated 2026-11-18；`main`；**0 Release**；**348 个文件**；**shields 顶层语言 99.8%**（以 Python 为主）；**代码 AGPL-3.0**（`LICENSE` 34,523 字节）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Maelic/RelateAnything](https://github.com/Maelic/RelateAnything) |
| 描述（API / README） | "RelateAnything: Real-Time Open-Vocabulary Relation Prediction From Any Inputs" |
| 论文 / 项目页 | arXiv:2609.12552（作者 Neau, Maëlic）· 项目页 `maelic.github.io/RelateAnythingProject` |
| 许可证 | **代码 AGPL-3.0-only**（`LICENSE` 34,523 字节）+ `NOTICE` 附加署名条款；**权重是 Meta DINOv3 的衍生**（另按 DINOv3 许可）；**RA-4M 标注带 Gemma Terms of Use**；**demo 检测器（ultralytics 衍生）为 AGPL-3.0 且被排除在关系模型的发布产物外** |
| 主语言 | shields 顶层语言 **99.8%**（仓库以 Python 为主） |
| 关注度 | **1,296 Star / 178 Fork / 14 关注**（ungh，2026-11-19；shields 同期 1.3k / 178）；**5 个开放 Issue、贡献者 2 人**（shields） |
| 创建 / 最近推送 | **2026-09-05 创建**；`pushedAt` **2026-11-14**、`updatedAt` 2026-11-18 |
| 默认分支 / Release / 文件数 | `main`；**0 Release**；**348 个文件** |
| 根目录可见文件 | `README.md`（12,696 B）、`LICENSE`（34,523 B）、`NOTICE`（1,392 B）、`THIRD_PARTY_NOTICES.md`（5,335 B）、`CITATION.cff`、`CONTRIBUTING.md`、`pyproject.toml`、`train.py`、`install.sh`、`.github/workflows/ci.yml` |
| 形态 / 安装 | Python 3.12+ 包 `relsgg`（`pip install -e ".[hub,detector]"`）；权重在 Hugging Face 集合 `maelic/relateanything` |
| 模型家族 | 7 个 checkpoint，均 **DINOv3** 骨干（见上表） |
| 数据 / 评测包 | **RA-4M**（训练标注，图片按 ID 引用、不再分发）· **OV-SGG-Bench**（六个评测轴的评测包与校准文件） |

## 部署形态（README）

- **PyTorch**：CPU 或 CUDA 上用 Python API（`RelateAnything.from_pretrained("maelic/relsgg-vits16plus", device="cuda")`）。
- **NVIDIA GPU / TensorRT 10**：从发布的 ONNX 图本地构建 FP32 引擎。
- **笔记本 / ONNX 或 OpenVINO**：跑本地图片或摄像头 demo（`deploy/demo_webcam.py`）。
- **浏览器**：`maelic.github.io/RelateAnythingProject/demo/`，**无需安装、用 WebGPU 或 WASM 在本机推理**。
- **检测器另装**：关系模型**只消费区域、不自己检测**；demo 检测器（ultralytics / YOLO26）**本地重建、单独许可**。

## 使用前需要注意

- **⚠️ 许可要分层看（README "License" 段）**。**代码 AGPL-3.0-only + `NOTICE` 附加署名条款**——README 明写"**若你把修改版作为网络服务运行，必须向该服务的用户提供这一版完整源码**"；**要摆脱 AGPL 义务去商用，须联系作者（Maëlic Neau）另取商业许可**。**权重是 Meta DINOv3 的衍生**（见各 model card 与 DINOv3 许可）；**RA-4M 标注带 Gemma Terms of Use 通知，源图不再分发**；**demo 检测器（ultralytics）AGPL-3.0，与关系模型发布产物分开**。**"代码开源"不等于"权重 / 数据 / 检测器都能随便用"。**
- **⚠️ 数字均为 README / 论文自述、未复现**。14.7 / 16.8 ms、33.5 / 38.7 ms、A100 25.6 ms、F1@50 等**全部来自 README（部分为 `<!-- GENERATED … -->` 自动生成的段落）与论文**；**本笔记未安装、未跑任何推理、未复现任何延迟或精度**。
- **⚠️ 0 Release、0 预编译**。仓库**没有任何 GitHub Release**，须**从源码 `pip install -e`**，或经 Hugging Face 取权重；**引用版本时只能按提交 / 权重名**。
- **⚠️ 依赖你自备区域（检测器 / 分割器）**。它**不做目标检测**：区域错，关系就错；README 的"含检测"延迟是**把 ultralytics YOLO 也算进去**的端到端口径，而**检测器本身另装、另许可**。
- **⚠️ 项目极新、贡献者少**。**2026-09-05 创建**、**贡献者 2 人**（shields）、**5 个开放 Issue**；**别把"论文 + 高星"当成"久经检验的成品库"**。
- **⚠️ 我未做任何验证**。仅阅读 README 全文、`LICENSE` 首行、文件清单与元数据；**未 clone、未安装 `relsgg`、未下载权重、未运行 `predict`、未打开浏览器 demo、未复现任何基准、未审计源码**；架构、能力、延迟与精度**均来自 README / 论文自述**。

## 什么时候会再看

- 想做**开放词汇的视觉关系 / 场景图预测**（"图里谁对谁做了什么"），又**不想为每套新关系重训模型**——先看它的"推理时给词表 + 小文本编码器"路线。
- 想参考"**同一模型家族换骨干**（ViT / ConvNeXt / 七档参数）**给出延迟 vs 精度权衡表**"与"**多后端部署（PyTorch / TensorRT / ONNX-OpenVINO / 浏览器 WebGPU·WASM）**"的写法。
- 想研究"**代码 AGPL + 附加 NOTICE + 权重与数据各自许可 + 检测器单独许可**"这套**分层许可**怎么在 README 里讲清楚时。
- 想对照其它"**开源模型仓库**"的形态（[fish-speech](fish-speech-Fish-Audio的开源语音生成与克隆模型.md) 的"代码许可 ≠ 权重许可" · [qwen-image-local](qwen-image-local-在8GB显卡上本地跑Qwen-Image的桌面工具.md) 把视觉模型落到本地 / 端侧）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"开源模型仓库"，都强调"代码许可 ≠ 权重许可"**：[fish-speech：Fish Audio 的开源语音生成与克隆模型](fish-speech-Fish-Audio的开源语音生成与克隆模型.md)——那篇是**语音**模型（代码 FACL v1.0、S2-Pro 权重另按 Research License）；本篇是**视觉**关系模型（代码 AGPL-3.0-only、权重是 DINOv3 衍生、标注带 Gemma 条款）。两篇都可对照"**一个仓库里代码 / 权重 / 数据各挂不同许可**"的写法，以及"论文 + Hugging Face 权重 + 自建基准"这一形态。
- **同为"把开源视觉模型落到本地 / 端侧"，路线不同**：[qwen-image-local：在 8 GB 显卡上本地跑 Qwen-Image 的桌面工具](qwen-image-local-在8GB显卡上本地跑Qwen-Image的桌面工具.md)——那篇是**面向小显存的图像生成 / 编辑桌面工具**（一次只载一个阶段、int8 权重）；本篇是**研究型关系预测模型**（PyTorch / TensorRT / ONNX-OpenVINO，另有浏览器 WebGPU·WASM demo）。可对照"**把模型塞进消费级硬件**"与"**提供多后端 + 浏览器演示的模型库**"两种落地。

## 参考来源

- 仓库主页与 README（258 行；核验日期 2026-11-19）：https://github.com/Maelic/RelateAnything
- 论文：arXiv:2609.12552 · 项目页与浏览器 demo：https://maelic.github.io/RelateAnythingProject（demo 路径 `/demo/`）
- 权重与数据：Hugging Face 集合 `maelic/relateanything` · 训练数据 `maelic/RA-4M` · 评测 `maelic/OV-SGG-Bench`
- 许可证 `LICENSE`（AGPL-3.0，34,523 字节）：https://github.com/Maelic/RelateAnything/blob/main/LICENSE ・ 通知 `NOTICE` · 第三方告知 `THIRD_PARTY_NOTICES.md`
- 文档（仓库内）：`docs/`（quickstart / installation / architecture / training / data / deployment / evaluation / pitfalls / results / benchmarks）· `benchmark/SPEC.md`
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-19

以上信息核验于 2026-11-19：Star / Fork 与版本可能变化。**未 clone、未安装 `relsgg`、未下载权重、未运行推理、未打开浏览器 demo、未复现任何延迟或精度数字、未审计源码**；**架构、能力、延迟、精度与许可口径均为 README / 论文自述，本笔记未独立验证，也未就许可咨询法律意见。**
