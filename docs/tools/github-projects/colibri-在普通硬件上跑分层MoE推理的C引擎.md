# colibri：把巨型 MoE 模型分层放进 GPU／内存／NVMe 的 C 语言推理引擎

> 一篇被大量 Star 的 C 语言推理引擎笔记。它解决的问题很具体：**MoE 模型每次 token 只用到一小部分专家，所以模型不必整个塞进显存**——把常驻的稠密部分放内存、把路由到的专家按需从磁盘（NVMe）读进来、把最热的专家放 GPU，就能在"你已有的硬件"上跑几百 B 到上千 B 的模型。**代价是速度慢、强烈依赖一块快 SSD、并且要下载很大的权重文件**。这篇笔记重点核实它的许可证、技术栈与"分层"到底怎么分。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README（英文版全文，约 5.3 万字符）、`LICENSE`、`NOTICE`、`main` 分支文件树、GitHub 页面内嵌元数据与 ungh 镜像；**未 clone、未编译、未下载任何模型、未运行、未做任何性能验证**）
- 适用范围：面向 **CPU 单机**（最低 8 GB 内存、最小模型需 22 GB 空闲磁盘；GPU 可选）；Windows / Linux / macOS 均有"一键脚本"；**独立显卡（CUDA / Vulkan）与 Apple Silicon（Metal）为可选加速**。语言 **C**，另含 Python 工具脚本与一个 Tauri 桌面壳

## 核心结论

**一句话定位（README 自述）**：colibri 是"a hierarchical inference engine for frontier MoE models"——**热专家放 GPU（CUDA / Vulkan）、温专家放内存、冷专家从 NVMe 流式读取**，用 C 写成，"runs on the hardware you already own"。口号是 "Tiny engine, immense model."（小引擎，大模型）。

**它凭什么能在普通机器上跑巨型 MoE（README 自述的机制，本节为重点核实）**：

- **MoE 的本质是"大而稀疏"**：README 举例 GLM-5.2 有 744B 参数，但**每个 token 只用约 40B**，其中**逐 token 真正变化的只有约 11 GB（路由专家）**。所以模型不必全进快速存储，**关键在于"放置"（placed）而非"装下"**。
- **三层放置**：**稠密部分**（attention、共享专家、embedding）常驻内存；**路由专家**留在磁盘，路由器点名时再读入，**经过一个"学习你工作里哪些专家更常被用到"的缓存**；**GPU 存在时**，则持有**最热的专家和稠密层**。
- **放置只改速度，不改语义**：README 明确"**哪里放权重只改变回答的快慢，不改变哪些权重、哪次路由决策产生结果**"，并称默认策略"**绝不悄悄改变模型精度或路由语义**"（`the default policy never silently changes model precision or router semantics`）。
- **GPU 端有两种放置**：Vulkan 下的 **expert tier**（把路由专家的缓存放进显存，随对话自适应）与 **dense chain**（整层作为一次 GPU 提交、层间状态留在 GPU）；CUDA 下还有 **VRAM 专家 tier**、**GGUF 块上卡**、以及 **System One** 决策路径。

**三个最值得记的取舍：**

1. **"能跑"优先于"跑得快"**：它是拿**极低 token 速度**换"在消费级机器上能把 744B 级模型跑起来"。README 给的具体数字包括：GLM-5.2 在 **25 GB 笔记本上 0.05–0.1 tok/s（冷启动）**、在 **128 GB 的 Ryzen AI Max+ 395 上 1.83 tok/s**、在 **6×RTX 5090 上 9.0–9.2 tok/s**；Kimi K3（2.8T）**约 9.4 秒/token**。**引用它时必须带速度数字，否则会误导人以为"随便跑"。**
2. **许可证边界清晰：引擎 Apache-2.0，但模型权重各自为政**：引擎是 **Apache-2.0（Copyright 2026 Vincenzo Fornaro）**；README 特别声明"**每个模型保留其作者给的许可证**"——GLM-5.2 权重由 Z.ai 以 **MIT** 发布，**Qwen-Image-2.1 仅限非商用**。**"引擎开源"不等于"模型随便用"。**
3. **它是一个"可测量、可复现"的研究平台，而不是一个成品应用**：README 强调"一个优化要靠可复现的端到端测量才配留在默认路径"，且**一个模型家族一个 `.c` 文件、共享头文件**（`make` / `make check` 统一入口）；界面是 CLI（`coli chat`）+ 浏览器 Dashboard + 可选 Tauri 桌面壳，并自带 MCP 服务器。**它更像"开源的推理实验台"。**

**个人判断（未验证）**：**50k 级 Star、创建仅约四个多月**（见"项目信息"），热度极高；README 把"支持哪些模型、在哪些机器上实测多少 tok/s、哪些还没测"列得很细，工程透明度高。但**它的日用体验高度取决于你的磁盘速度与内存**，且**性能数字来自社区真实机器**（README 鼓励用户提交 benchmark），**我用前会先按自己的机器型号去 README 表格里找对应行**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [JustVugg/colibri](https://github.com/JustVugg/colibri) |
| 描述 | "A hierarchical inference engine for frontier MoE models: hot experts on the GPU (CUDA, Vulkan), warm ones in RAM, cold ones streamed from NVMe. Written in C, runs on the hardware you already own." |
| 归属 | 账号 `JustVugg`（**个人账号**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false`（页面值） |
| 许可证 | **Apache-2.0**（**来源双确认**：页面 `spdxId: Apache-2.0 / Apache License 2.0`；`LICENSE` 文件为标准 Apache License 2.0 全文，`NOTICE` 与 README "License" 段均署名 `Copyright 2026 Vincenzo Fornaro`）。**注意：模型权重许可证独立**——GLM-5.2 权重 MIT（Z.ai）、**Qwen-Image-2.1 仅非商用** |
| 主语言 | **C 53.9%**（shields.io `languages/top`，2026-11-13） |
| 技术栈 | **纯 C 推理引擎**（`c/` 下 1,257 个文件；一个模型家族一个 `.c` + 共享头文件）；后端可选 **CPU / Vulkan / CUDA / Metal**；配套 **Python 工具**（一键安装、硬件探测、下载、`setup_catalog.py`、`openai_server.py`、`mcp_server.py`）；前端有**浏览器 Dashboard**（`web/`）、**Tauri 桌面壳**（`desktop/`）；含 Docker 镜像、网站（`site/`）、`Makefile` 统一入口 |
| 关注度 | **50,481 Star / 5,961 Fork / 397 Watcher / 94 开放 Issue**（**Star·Fork·Watcher 为 GitHub 页面内嵌值，Issue 数取自 shields.io**，2026-11-13。**页面值与 ungh.cc 镜像不一致：镜像报 49,957 Star / 5,882 Fork / 394 Watcher，此处记录页面值并注明镜像差异**） |
| 维护状态 | **2026-07-01 创建**；页面 `updatedAt` **2026-11-11**、ungh 报 `pushedAt` **2026-11-11**（**活跃**）；已累计 **21 Release / 21 tags**（页面值） |
| 默认分支 | `main` |
| 版本 | **21 Release / 21 tags**；README 示例显示 `colibri v2.1.0` |
| 话题标签 | 无（页面 `topics: []`） |
| 体积 | `main` 文件树约 **1,697 个文件**（ungh.cc files API；其中 `c/` 约 1,257、`docs/` 约 310） |
| 仓库结构 | `start-here.sh` / `START-HERE.bat`（一键安装）；`c/`（引擎源码 + CLI `coli` + Python 工具）；`ui/`（`coli ui` 聊天界面）；`web/`（Dashboard，纯 API 客户端）；`desktop/`（Tauri 壳）；`docker/`；`docs/`（参考文档与实验记录）；`site/`（官网） |
| 官网 | <https://justvugg.github.io/colibri> ・ 社区：Discord |

## 它解决什么问题（README 自述）

把"**几百 B 到上千 B 的开放 MoE 模型**"跑到**个人已有的硬件**上：模型不必整个塞进显存，只要把常驻部分放内存、把专家按需从磁盘读入。README 的动机有三条：让人**在自有硬件上跑这种规模的模型**、**观察它如何工作**（Dashboard 会显示每个被激活的专家）、以及让**引擎小到任何人都能测量并加速它**。

目标门槛写得很低：**最低 8 GB 内存**（16 GB 更好）、**最小模型需 22 GB 空闲磁盘**、**需要联网**、**显卡可选**。

## 支持的模型（README 自述，13 个引擎）

README 称"今天有 13 个引擎在跑"：

- **10 个语言模型引擎**：GLM-5.2/5.3（也跑 Mistral-Large-4）、GLM-5.3-Flash、Inkling、Kimi K3、DeepSeek V4 Flash、DeepSeek V4.1 Flash、MiMo-V2.6 Flash（含 Pro）、Qwen3.8-Flash-Next、Qwen3.6（同一引擎还跑 Qwen3-Coder、稠密 Qwen3.8-27B、Flint、Muse Spark Open、LFM3-VL）、OLMoE；
- **1 个图像引擎**：Qwen-Image-2.1；
- **2 个决策引擎**：Laya、GLiNER2.5-Decide，另有 Clef 跑在 Qwen3.6 引擎上。
- **可直接读已发布的 GGUF 文件**（`./coli import`，`docs/gguf.md`），CPU 上跑，或经 Vulkan / CUDA 上 GPU。

**规模与硬件的对照（README 表格摘录，体积为 Hugging Face 列出的下载大小）**：小模型**从内存运行**——Qwen3.6-35B-A3B（23 GB，10/20 GB 内存）、Qwen3-Coder-30B-A3B（19 GB）、Qwen-Image-2.1（33 GB，仅非商用）；大模型**专家从磁盘流式读取**——DeepSeek V4 Flash（167 GB）、MiMo-V2.6 Flash（172 GB）、GLM-5.2（**429 GB**）、Inkling（514 GB）、**Kimi K3（1.56 TB）**等。

## 怎么跑起来（README 自述）

- **一键方式**：Windows 双击 `START-HERE.bat`；Linux / macOS 运行 `./start-here.sh`。脚本会**探测你的机器（内存、磁盘、CPU、GPU）→ 推荐能装下的模型 → 取引擎（有编译器就本地编，否则下载预编译版）→ 下载模型（可中断续传）→ 启动并打开浏览器 Dashboard**。
- **它启动后对外暴露的地址**：`Browser: http://127.0.0.1:8000/`、**`OpenAI base URL: http://127.0.0.1:8000/v1`**、**`Anthropic base URL: http://127.0.0.1:8000`**——即**本地起一个 OpenAI / Anthropic 兼容网关**（`c/openai_server.py`），其它应用可直接接入。
- **可选 MCP 服务器**：`coli mcp` 提供探测硬件、推荐模型、安装、启停、检查等工具（`docs/MCP_SERVER.md`）；也可让 AI 助手按 `docs/AI_SETUP.md` 全自动安装。
- **手动方式**：选择预编译 release 或源码构建、自选模型，然后 `coli chat` / `coli web` / `coli serve`。

## 架构与"分层"的实现线索（README 自述）

**一个模型家族一个 `.c`，共享单头文件**：引擎只拥有自己的架构，两个引擎都要用的东西放双方都 include 的头文件里，"改一处惠及所有引擎"。关键文件包括 `expert_ffn.h` / `expert_store.h`（**路由专家 kernel 与流式专家缓存**）、`route_trace.h` / `kv_prefix.h`（路由遥测 `.coli_usage` 与 KV 前缀复用）、`gguf.h`、`park.h`（把一轮对话在 agent 两次调用间暂存）等；后端为 `backend_cuda.*` / `backend_vulkan.*` / `backend_metal.*`（可选编译）。

**GPU 三段式（README 自述）**：

- **Vulkan（任意 Vulkan 1.2 显卡，含集显）**：**expert tier**（显存里的路由专家缓存，启动时用历史对话填、聊天中自适应，GPU 算它持有的、CPU 算其余的）与 **dense chain**（整层一次提交、层间状态留在 GPU）。README 实测：集显 Radeon 780M 上 Qwen3.6-35B-A3B 解码从 CPU 的 6.0 → tier 8.0 → tier+dense chain **9.9 tok/s**。
- **CUDA（NVIDIA）**：Qwen3.6 / Qwen3.8 / OLMoE **整层上卡**；其余引擎有**路由专家 tier**（`cu_tier.h`）与稠密矩阵上卡（`cu_dense.h`）；GGUF 的块也可上卡（`cuda_bd.cuh`）。README 实测 RTX PRO 4000（24 GB）上 Qwen3.6-35B-A3B 解码 **104.8 tok/s**。
- **Apple Silicon**：**Metal** 后端对部分引擎做统一内存 GPU 上的专家计算（`COLI_METAL=1`）。
- **无 GPU 也能跑**：README 称"每个引擎都能在 CPU 上跑，不装任何东西"；**对大模型，磁盘决定速度；对小模型，内存决定速度**。

**关联研究（README 致谢）**：专家放置 / 压缩 / 路由实验参考了 REAP、EASY-EP、SERE、ReMoE、MC-SMoE、MoBE、D²-MoE、HybriMoE、ScMoE、OD-MoE 等工作；工程上用到 / 重实现了 safetensors（`c/st.h`）、tiktoken 的 `byte_pair_encode`、llama.cpp 的 GBNF 语法子集与 Metal residency 技巧、DietGPU 的 ANS 编解码等。

## 使用前需要注意

- **⚠️ 速度可能极低，别被"能跑 744B"误导**：README 的实测里，GLM-5.2 在 25 GB 笔记本上 **0.05–0.1 tok/s**、Kimi K3 **约 9.4 秒/token**。**它的卖点是"能跑"，不是"跑得快"**；引用务必带 tok/s，并到 README 表格里找与你硬件相近的那一行（**我未做任何性能验证，数字均转述 README**）。
- **⚠️ 许可证要分两层看**：**引擎本体 Apache-2.0**（可自由使用/修改/分发，需保留 NOTICE）；但**模型权重各有各的许可**——GLM-5.2 权重 MIT，**Qwen-Image-2.1 仅限非商用**，"引擎开源"**不**等于"所有模型都能商用"。要商用请逐个模型核对来源许可。
- **⚠️ 磁盘与网络是硬门槛**：**最小模型要 22 GB 空闲磁盘**，大模型从百 GB 到 **Kimi K3 的 1.56 TB**；安装期需要联网下载模型（可中断续传），无编译器时会下载预编译引擎。README 明确提醒：**WSL 下别把模型目录放在 `/mnt/c`（"many times slower"）**，应放在 Linux 盘（默认 `~/colibri-models`）；**快 NVMe 对大模型帮助最大**。
- **⚠️ 依赖"未验证的实测数字"与社区机器**：README 的性能数字来自作者与社区的真实机器，部分格子明确写着"空白 = 还没人测过"。**你的机器不一定落在已测行上**，用前先看 `./start-here.sh --list` 在你机器上的判断。
- **⚠️ 它会启动本地服务并写本机文件**：运行后是一个监听 **127.0.0.1:8000** 的本地 HTTP 网关（提供 OpenAI / Anthropic 兼容接口与 Dashboard）；引擎会写路由遥测文件（`.coli_usage`，用于"学习缓存"）。**这些都是本机行为**（README 未声称上传对话内容），但**注意本机端口占用与本地文件落盘**。
- **⚠️ 热度极高、项目尚新**：**2026-07-01 创建**、核验日 **50,481 Star**（页面值）。**四个多月长到 5 万 Star 属于异常快的增长**，建议以 README / 文档 / Releases 的实际内容为准，不要仅凭 Star 数下结论。**另：镜像（ungh.cc）与页面 Star / Fork / Watcher 有差异，此处以页面值为准。**
- **⚠️ 它偏工程而非"开箱即用的 App"**：核心界面是 CLI + 浏览器 Dashboard（另有可选 Tauri 桌面壳）；大量能力（Vulkan / CUDA / Metal、多盘、集群、投机解码、System One 等）需要通过**环境变量与编译开关**开启，README 指向一长串 `docs/*.md`。**想要"点开就用"的人会失望。**
- **我未做任何运行验证**：**未 clone、未编译、未下载模型、未运行任何命令、未做任何性能测试**；上述机制、模型清单、硬件门槛、GPU 分层与性能数字**均来自 README、`LICENSE`、`NOTICE` 与仓库文件自述**。

## 什么时候会再看

- 想在**自己已有的机器**（CPU / 集显 / 单张消费级卡）上跑几百 B 级的开放 MoE 模型，并愿意用速度换"能跑"——先来查它的硬件对照表。
- 想接入一个**本地 OpenAI / Anthropic 兼容网关**（`http://127.0.0.1:8000/v1`）给别的应用或 Agent 用。
- 想研究**"稠密常驻内存 + 路由专家流式读盘 + GPU 分层缓存"**这套放置策略怎么落地成 C 代码（`expert_store.h` / `cu_tier.h` / Vulkan tier 等）。
- 想参考它**"一个模型家族一个 `.c` + 共享头文件"**的组织方式，以及**"性能优化必须配可复现端到端测量"**的工程纪律。
- 想试它的 **System One**：`POST /v1/systemone` 输入状态与带类型的问题，**只返回每个选项的概率与置信度、不生成文本**。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **它默认在 `localhost:8000/v1` 暴露 OpenAI 兼容服务，正好是"本地模型优先"助手的默认后端**：[openfox：本地模型优先的编码助手](openfox-本地模型优先的编码助手-co-l-openfox的fork.md)——openfox 默认探测定位于 `http://localhost:8000/v1` 的本地 OpenAI 兼容服务，与 colibri 启动后打印的 `OpenAI base URL` 端口一致，两者可组合成"本地引擎 + 本地编码助手"。
- **同为"不让大模型生成文本、直接给选项概率"的思路**：[kev：一次前向输出概率的小模型](kev-一次前向输出概率的小模型.md)——colibri 的 System One（`/v1/systemone`）也是"给状态与带类型的问题、返回每个选项的概率与置信度、不做生成"，可对照两种实现路径（C 引擎内 vs Python 小模型）。
- **同为"在本机跑模型、并对外暴露本地接口"**：[VoiceStudio：全本地运行的 AI 语音工作室（AGPL 的 ElevenLabs 替代）](VoiceStudio-全本地运行的AI语音工作室与ElevenLabs替代.md)——那篇是**语音模型**的本地工作室（本地 API + MCP，硬门槛是显存 / 时间）；本篇是**文本大模型**的本地推理引擎（本地 OpenAI / Anthropic 兼容网关 + MCP，硬门槛是磁盘）。可对照"**把模型跑在本机，再开一个本地接口给 Agent 用**"的两种落地。

## 参考来源

- 项目仓库与 README：https://github.com/JustVugg/colibri
- 官网：https://justvugg.github.io/colibri
- 许可（Apache-2.0）：https://github.com/JustVugg/colibri/blob/main/LICENSE ・ 通知：https://github.com/JustVugg/colibri/blob/main/NOTICE
- 第三方代码与许可：https://github.com/JustVugg/colibri/blob/main/THIRD_PARTY_NOTICES.md
- 快速开始：https://github.com/JustVugg/colibri/blob/main/docs/quickstart.md
- GGUF 支持：https://github.com/JustVugg/colibri/blob/main/docs/gguf.md ・ Vulkan / CUDA：https://github.com/JustVugg/colibri/blob/main/docs/vulkan.md ・ https://github.com/JustVugg/colibri/blob/main/docs/cuda.md
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 许可证 / topics）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未编译、未下载模型、未运行任何命令、未做任何性能验证**；机制、模型清单、硬件门槛与性能数字均来自 README、`LICENSE`、`NOTICE` 与仓库文件自述；**镜像（ungh.cc）与页面 Star / Fork / Watcher 数值存在差异，本笔记以页面值为准并已注明。**
