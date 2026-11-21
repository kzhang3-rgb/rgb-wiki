# qwen-image-local：在 8 GB 显卡上本地跑 Qwen-Image 的桌面工具

> 一句话定位（README 自述）：**"输入一句提示词或拖入最多 10 张照片：Qwen-Image-2.1 在你 8 GB 显存的显卡上约 35 秒出图，且什么都不离开你的电脑。"**——一个 **Windows 桌面应用 + `qil` 命令行**，把 Qwen-Image-2.1（7B 生成器）装好并**一次只载入一个阶段**地塞进 8 / 12 GB 显存。最值得记的三点：① **全程本地**（提示词与照片只发往 `127.0.0.1` 的引擎，每张图附 `.json` 记提示词 / 种子 / 参数）；② **诚实的自评 benchmark**（20 条提示词、每模型只跑一次、全部公开：**照片编辑与 gpt-image-2 打平**，**文生图明显不如**）；③ **许可证有硬约束**——**代码 MIT，但模型权重是 Qwen Research License、不可商用**（见"使用前需要注意"）。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（272 行）与 `README.zh-CN.md`（272 行）、`LICENSE`（MIT + 自定义前言，1,724 字符）与已核实元数据；**未 clone、未构建、未下载权重（约 19.5 GB）、未安装、未运行 `qil`、未复现 benchmark**）
- 适用范围：**仅 Windows 10/11 + NVIDIA 显卡**；**8 GB（紧凑档）/ 12 GB（均衡档）**显存、驱动 **580+**、约 **25 GB** 磁盘、**32 GB 内存**（实测配置，16 GB 未测）；自行编译还需 **Rust / Node / VS 2022 Build Tools**

## 核心结论

1. **它解决的是"7B 图像模型塞进小显存"**（README）：Qwen-Image-2.1 是 2026-09-20 发布的 **7B 生成器**（多数优秀开源图像模型在 10–20B），本仓库给"没有 24 GB 卡"的人补上一键桌面应用 + CLI。**非官方，与阿里巴巴 / Qwen 团队无关**。
2. **"怎么塞进去"是技术看点**：三件套装不下（文本编码器 8.9 GB + 图像模型 6.9 GB + VAE 0.6 GB = **16.4 GB**），于是**一次只跑一个阶段、每阶段完整载入显存**，其余在系统内存等待——**不做 CPU / GPU 拆分**（README 认为那才让低显存方案变慢）。关键两选：用 **int8 "convrot" 权重而非 GGUF**（Ampere 及更新有 int8 张量核心、ComfyUI 有原生算子），用 **为 CUDA 13 编译的 PyTorch**（CUDA 12.8 构建会让 ComfyUI 静默关掉优化后端）；驱动需 R580+。
3. **自评 benchmark 明确"哪里赢哪里输"**（README）：20 条提示词、**每模型只跑一次、不挑好看的**；照片编辑 **8 项里 1 胜 7 平 0 负**，文生图 **12 项里 gpt-image-2 赢 11**。**结论自述**：编辑照片与 gpt-image-2 打平且免费 / 私密 / 不限次数；文生图不如它（长提示词遵循较弱、世界知识较少，示例"画马黛茶杯画成了中国葫芦"）。
4. **两种安装路径**：**A 自己编译**（推荐，约 5 分钟，`build.ps1` 跨五种终端；自编译的二进制不带"从网上下载"标记，SmartScreen 不拦）；**B 预构建安装包**（未代码签名，首次运行 SmartScreen 会警告）。

**个人判断（未验证）**：这个仓库的价值在**"面向小显存的工程化 + 不自吹的评测"**：它没假装文生图能打 gpt-image-2，而是把编辑场景的平局与文生图的差距都摆出来。但它对**硬件与平台很窄**（仅 Windows + NVIDIA + 8 GB 起），并且**权重非商用**这条会直接决定它能不能进你的工作流。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | 本次抓取为 [ybhgl/qwen-image-local](https://github.com/ybhgl/qwen-image-local)；**但 README 内所有链接（徽章、安装命令、仓库结构、克隆地址）均指向 `Mats2208/qwen-image-local`**——两者关系（fork / 镜像 / 改名）**未核实** |
| 描述 | "Type a prompt or drop up to 10 photos: Qwen-Image-2.1 renders it on your 8 GB GPU in ~35 s. Desktop app + CLI, honest benchmark vs gpt-image-2." |
| 许可证 | **代码 MIT**（`LICENSE` 1,724 字符，Copyright (c) 2026 Mats2208）；**同一 `LICENSE` 明确声明不覆盖**：① **模型权重**（Qwen Research License，**非商用**，从 Hugging Face 下载）；② **ComfyUI**（GPL-3.0，安装时下载、作为**独立进程**运行）；③ **bench 输入照片**（作者与许可见 `bench/inputs/credits.json`，改写自 CC BY-SA 的照片按 **CC BY-SA 4.0** 发布）。shields license 显示 **"not identifiable by github"**（因 `LICENSE` 带自定义前言） |
| 关注度 | **108 Star / 1 Watcher / 13 Fork**（2026-11-13）；shields 另报**开放 Issue 0、贡献者 3** |
| 维护状态 | **2026-10-09 创建**；pushed 2026-10-31、updated 2026-11-12；默认分支 `main`；**Release 3 条**；状态徽章 **beta** |
| 形态 | **Tauri 2 桌面应用**（Rust + 纯 HTML/CSS/JS；英 / 西 / 简中 / 繁中）+ **`qil` CLI**；仓库另含 `crates/qil-core`（Rust：安装器、引擎生命周期、生成客户端、GPU 检测） |
| 站点 | 公开 benchmark 站点 <https://mats2208.github.io/qwen-image-local/> |

## 功能与用法

### 1. 能力清单（README）

| | 功能 | 说明 |
| --- | --- | --- |
| 创作 | 文生图 | 草稿 1024² · 标准 1.6 MP · 最高原生 2K，七种画幅，可锁种子 |
| 速度 | Turbo 档位 | 蒸馏 8 步权重，采样快约 3–4 倍，可与任意显存档位叠加 |
| 模型 | 官方 / 无审查 | 安装界面或主界面切换；编码器与 VAE 通用，只换一个文件 |
| 下载源 | 官方 / hf-mirror | 从 `huggingface.co` 或 `hf-mirror.com` 取权重，或复制直链自取 |
| 透明 | 原生 RGBA | 真实 alpha 由模型直接产出，不靠抠图 |
| 编辑 | 1–10 张参考图 | 重打光、改风格、移除物体 / 人物、重写招牌、合成主体；**第一张图是画布** |
| 标注 | 图片编辑器 | 画 / 圈 / 框 / 箭头 / 擦除 / 裁剪，含撤销、缩放、90° 旋转 |
| 安装 | 一条命令构建 + 一次性安装 | 锁定版 ComfyUI、Python 3.12、PyTorch CUDA 13、权重断点续传 + SHA-256 |
| 命令行 | `qil` | `doctor` · `setup` · `generate` · `edit` · `surprise` · `bench` · `config` |
| 隐私 | 全程本地 | 提示词与照片只发往 `127.0.0.1` 的引擎；每图附 `.json`（提示词 / 种子 / 参数） |

### 2. 显存档位（README 表，**自述实测**）

| 档位 | 文本编码器 | 峰值显存（含桌面） | 1024²，20 步 | 状态 |
| --- | ---: | ---: | ---: | --- |
| **均衡** | int8，8.9 GB | 11.4 GB | 32 s | 已在 **RTX 3060 12 GB** 实测 |
| **紧凑** | w4a8，6.3 GB | 7.2 GB | 33 s | 已在 **RTX 5060 Laptop 8 GB** 实测 |

紧凑档在速度不变下把峰值显存降约 2.5 GB。README 邀请 10 / 16 GB 用户跑 `bash bench/tiers/run.sh` 提交 `results.json`。

### 3. 安装与命令行（README）

```powershell
# 一次性依赖（任意终端）
winget install Rustlang.Rustup
winget install OpenJS.NodeJS.LTS
winget install Microsoft.VisualStudio.2022.BuildTools --override "--quiet --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended"
```

```bash
git clone https://github.com/Mats2208/qwen-image-local && cd qwen-image-local
./build.sh          # 或 build.ps1 / build.cmd；产物在 out\
```

```text
qil doctor                                            # 检查 GPU / 档位 / 已装组件
qil setup                                             # 安装（档位按显存自动选）
qil config --speed turbo                              # 叠加 8 步蒸馏采样
qil config --source mirror                            # 改从 hf-mirror.com 拉权重
qil generate "a red fox asleep in the snow" --preset draft
qil generate "a mate gourd sticker" --transparent --aspect 1:1
qil edit "Change the scene to a snowy night" -i street.jpg --preset standard
qil edit "Put the dog from <image2> next to the bike in <image1>" -i bike.jpg -i dog.jpg
qil bench --suite bench/suite.json --preset max       # 复现公开 benchmark
```

首次启动一次性下载约 **19.5 GB**（引擎、Python、PyTorch 与权重），支持断点续传与 SHA-256 校验。所有内容位于 `%LOCALAPPDATA%\qwen-image-local`（模型可放别的盘），**卸载即删除该文件夹**。加 `--keep-engine` 让引擎在多次调用间保持运行；已有 ComfyUI 模型目录可复用（`qil setup --models-dir <folder>`，大小匹配的不重下）。

### 4. 自述 benchmark（README，**未经我复现**）

20 条提示词、每模型只跑一次；编辑类用 Wikimedia Commons 真实照片。**照片编辑 8 项：Qwen 1 胜 / 7 平 / 0 负；摄影 6 项：0 / 1 / 5；创意 6 项：0 / 0 / 6；合计 1 胜 / 8 平 / 11 负（gpt-image-2 胜）。**

## 使用前需要注意

- **许可证有两个硬约束（务必先读）**：
  - **权重不可商用**：Qwen-Image-2.1 权重遵循 **Qwen Research License**（非商用），README 在"它（暂时）不做什么"里也明写"**weights 仍是 Qwen Research License 期间不可商用**"。**只要你打算商用，这个项目当前就不适用**（除非权重许可变更）。
  - **`LICENSE` 只覆盖仓库代码**：**不覆盖** 权重、**ComfyUI（GPL-3.0，安装时下载、作为独立进程运行）** 与 **bench 输入照片（`credits.json`，改自 CC BY-SA 的按 CC BY-SA 4.0 发布）**。**shields 的 license 徽章显示 "not identifiable by github"**，别据此以为无许可。
- **平台与硬件很窄**：**仅 Windows + NVIDIA**；8 GB（紧凑）/ 12 GB（均衡）档、驱动 580+、约 25 GB 磁盘、**32 GB 内存**（16 GB **未测**）。AMD / Intel / Apple Silicon 或 < 8 GB 显存，README 直接建议改用 [nihui/qwenimage-ncnn-vulkan](https://github.com/nihui/qwenimage-ncnn-vulkan)。
- **"全程本地"是与具体行为绑定的**：README 与功能表都称提示词与照片**只发往 `127.0.0.1`**；但**首次安装会从 Hugging Face 下载约 19.5 GB**（可选 hf-mirror）。**推理本地 ≠ 安装零联网**（**自述未验证**）。
- **预构建安装包未代码签名**：方案 B 首次运行会触发 **SmartScreen 警告**（README 建议"更多信息 → 仍要运行"）；方案 A 自编译可避免该标记。**如何取舍请自行判断安全性**。
- **明确的功能缺口**（README"它（暂时）不做什么"）：**采样时无实时预览**；**无蒙版 / 画笔**（局部编辑只能文字描述）；**无提示词扩写**（Qwen 官方改写器是另一个 9B 模型，12 GB 装不下）。
- **仓库归属存疑**：抓取到的是 `ybhgl/qwen-image-local`，而 README 内链接全部指向 `Mats2208/qwen-image-local`。**两者关系未核实**——引用仓库地址时请注意这点（`LICENSE` 署名为 Mats2208）。
- **benchmark 是自报的**：20 条提示词的胜负全部由**项目自己**判定并公开，**未经我复现或第三方复核**；"打平 / 不如"均应视为**项目自述结论**。
- **我只读了 README（英 / 简中各一版）与 LICENSE**：**未 clone、未构建、未下载权重、未安装、未运行 `qil`、未复现 benchmark**；上述能力、性能与结论均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想在一张 **8 / 12 GB NVIDIA 卡**上**本地、私密**地做**图像编辑**（重打光、改风格、去物、重写招牌、合成主体）而不上传时。
- 想看**"一次只载入一个阶段 + int8 convrot + CUDA 13 PyTorch"**这套小显存工程化怎么落地时。
- 想看一份**把"哪里赢、哪里输"讲清楚的模型自评 benchmark**（含失败样例，如"马黛茶杯画成葫芦"）时。
- 想对照其它"用 AI 生成 / 编辑图像"的路线时（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"本地跑开源图像 / 视频生成"**：[ComfyUI-MiniMaxH3：Easy-MiniMaxH3 视频节点套件](ComfyUI-MiniMaxH3-Easy-MiniMaxH3视频节点套件.md)——那篇是**挂在 ComfyUI 上的视频生成节点套件**；本篇是**把 ComfyUI 当引擎、只做 Qwen-Image 文生图与编辑的桌面工具**（并锁定版 ComfyUI、自动装权重）。可对照"**节点套件**"与"**开箱即用的单模型应用**"。
- **都用 AI 生成图像素材，场景不同**：[nvzhuang：拍片哥女装 AI 服装参考图集](nvzhuang-拍片哥女装AI服装参考图集.md)——那篇是**用 AI 做的参考图集（内容产物）**；本篇是**生产这些图像的本地工具**。可对照"**产物**"与"**工具**"。
- **都是"给 Agent / 工作流的图像生成能力"，接入方式不同**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md) 与 [ip-as-logo-skill：把 IP 吉祥物做成极简圆润 logo 的 Agent 技能](ip-as-logo-skill-把IP吉祥物做成极简圆润logo的Agent技能.md)——那两篇是**面向编码助手的生成技能**（调云端模型、出特定风格图）；本篇是**本地桌面工具 + CLI**（跑本地权重）。可对照"**云模型技能**"与"**本地权重应用**"。
- **同属"AI 生成素材做出成品"**：[dioramas：用 AI 生成素材做电影感 3D 网站](dioramas-用AI生成素材做电影感3D网站.md)——那篇把 AI 素材组装成 3D 网站；本篇**产出图像本身**。可对照"**素材生成**"与"**素材组装**"。
- **同为"给 Agent 的图像能力"，一个出提示词、一个出图**：[image-prompt-reverse：给 Codex 用的"图片反推提示词"技能](image-prompt-reverse-Codex图片反推提示词技能.md)——那篇是**从参考图反推提示词**（纯文本技能，仓库**无一行代码**）；本篇是**本地跑权重出图的桌面工具**。可作为"**提示词侧**"与"**出图侧**"的配对：先用前者把参考图变成 Prompt，再用后者本地出图。
- **同为"本地跑 Qwen-Image"，封装与门槛不同**：[qwen21-nsfw-pose-skill：Qwen-Image 2.1 的本地姿势出图配方技能](qwen21-nsfw-pose-skill-本地NSFW姿势出图配方技能.md)——那篇是 **ComfyUI + 提示词配方 + 3D 人偶姿势库**的本地出图技能（**NSFW 取向**，画面以无身份特征的 3D 人偶做姿势参考）；本篇是**面向 Qwen-Image 文生图与编辑的桌面应用 + CLI**（锁定版 ComfyUI、自动装权重）。可对照"**配方 / 姿势控制**"与"**开箱即用的单模型应用**"。
- **同为"把开源视觉模型落到本地 / 端侧"，路线不同**：[RelateAnything：实时预测开放词汇视觉关系的开源模型](RelateAnything-实时开放词汇视觉关系预测的开源模型.md)——那篇是**研究型开放词汇视觉关系（场景图）预测模型**（Python 库 `relsgg`，提供 PyTorch / TensorRT / ONNX-OpenVINO 与浏览器 WebGPU·WASM demo）；本篇是**面向 8 GB 显存的 Qwen-Image 文生图 / 编辑桌面工具**（一次只载一个阶段、int8 权重）。可对照"**提供多后端与浏览器演示的模型库**"与"**把模型塞进消费级硬件的成品应用**"两种落地。

## 参考来源

- README（英文，272 行；核验日期 2026-11-13）：https://github.com/ybhgl/qwen-image-local（README 内链接指向 https://github.com/Mats2208/qwen-image-local）
- 中文 README：`README.zh-CN.md`（272 行；核验日期 2026-11-13）
- 许可证：`LICENSE`（MIT，1,724 字符，Copyright (c) 2026 Mats2208；含自定义前言声明不覆盖权重 / ComfyUI / bench 照片；核验日期 2026-11-13）
- 模型权重许可：Qwen Research License https://huggingface.co/Qwen/Qwen-Image-2.1/blob/main/LICENSE
- benchmark 站点：<https://mats2208.github.io/qwen-image-local/>；bench 照片署名：`bench/inputs/credits.json`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 shields.io 交叉核对（2026-11-13）

以上信息核验于 2026-11-13。**未 clone、未构建、未下载权重、未安装、未运行 `qil`、未复现 benchmark**；能力、性能与结论描述均来自 README，**未逐一验证**。
