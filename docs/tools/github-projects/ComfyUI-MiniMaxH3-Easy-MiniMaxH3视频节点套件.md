# ComfyUI-MiniMaxH3-Easy：MiniMax H3 视频节点套件

> 一句话值得记住的取舍：它把 MiniMax H3 的常见生成模式**收进一个主节点**，并加了统一媒体管理、`@` 引用与长视频分段；但**安装时必须选 Nightly 版**，且 API key 明文存在插件目录里。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文与 GitHub API 元数据；仓库另有 `README_CN.md` 中文说明，未安装、未运行）
- 适用范围：在 ComfyUI 里做文生视频 / 图生视频 / 首尾帧 / 参考生视频 / 数字人 / 长视频分段的人

## 核心结论

这是给 ComfyUI 用的 **MiniMax H3 节点套件**，目标是**减少"接线"**：把常见生成模式收进一个主节点，同时补上统一媒体管理、可视化提示词参考、提示词优化和分段细化。

它自己列的几条卖点：

- **一个主节点覆盖常见模式**：文本、图像、首尾帧、全参考、数字人；
- **统一图像/视频/音频管理**：在可视化的 Media Loader 里上传、拖拽、粘贴、预览、重排、替换、删除，视频卡会显示时长；
- **在提示词里直接引用媒体**：输入 `@` 就能插入图片/视频/音频，**不用手写 `<Picture N>` 这类标签**；
- **长视频按"连接的分段"生成**：每段保留自己的提示词与引用，同时从上一段接收视觉或 AV 上下文；
- **内建分段细化**：Pixel Resize、3D Latent Upscale、Low VRAM Tile 三选；
- **仍然完全可组合**：采样器、LoRA、attention patch、解码器、保存节点都还是普通的 ComfyUI 连接。

生成模式与输入对应关系（README 原表）：

| 目标 | 模式与输入 |
| --- | --- |
| 文生视频 | 选 `I2V or First/Last Frame` 或 `Reference-to-video`，**不接媒体** |
| 图生视频 | 选 `I2V or First/Last Frame`，接 1 张图 |
| 首尾帧视频 | 选 `I2V or First/Last Frame`，接 2 张图 |
| 参考生视频 | 选 `Reference-to-video`，接图/视频/独立音频，**至少一个图或视频** |
| 数字人 | 选 `Digital Human`，接视觉参考 + **恰好一条驱动音频** |

几个"不报错而是降级"的细节值得注意：首尾帧图会**居中裁剪**去适配画布（不拉伸）；`I2V or First/Last Frame` 与 `Reference-to-video` **在不接媒体时自动变成文生视频**；数字人**没有提供音频时会自动回退成普通参考生视频，而不是失败**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [nkxx188/ComfyUI-MiniMaxH3-Easy](https://github.com/nkxx188/ComfyUI-MiniMaxH3-Easy) |
| 描述 | "The easiest way to use MiniMax H3… One compact workflow for T2V, I2V, first/last-frame, and reference video generation…" |
| 许可证 | **MIT**（GitHub API 返回；**README 正文本身未提许可证**，以 API 为准，建议在仓库页二次确认） |
| 语言 | JavaScript（另有 `README_CN.md` 中文说明） |
| 关注度 | **880 Star、60 Fork、1 关注、3 个开放 Issue**（GitHub API，2026-11-02） |
| 维护状态 | 2026-08-05 创建；GitHub API 显示最后推送 **2026-10-26**（较新） |
| 规模 | 体积约 3,517 KB（约 3.4 MB） |

## 用法要点

**安装（注意版本选择很关键）**：

```bash
# 先把 ComfyUI 更新到包含官方 MiniMax H3 节点的较新版本
cd ComfyUI/custom_nodes
git clone https://github.com/nkxx188/ComfyUI-MiniMaxH3-Easy.git
```

也可以在 ComfyUI Manager 里搜 `ComfyUI-MiniMaxH3-Easy`——**README 明确要求安装时选 Nightly 版本，因为 Nightly 才是当前最新发布，其他已发布版本可能落后于本仓库**。安装或更新 Python 文件后要重启 ComfyUI。

**模型放置**：

```
ComfyUI/models/diffusion_models/
ComfyUI/models/text_encoders/
ComfyUI/models/vae/
ComfyUI/models/latent_upscale_models/   # 只有用 3D Latent Upscale 细化时需要
```

Easy Loader 可直接选 FL2VA、Ref2VA、文本编码器与两个 VAE；它**移除了文件名白名单与命名过滤**，所以会列出对应目录下的所有文件，**需要你自己选对每个角色对应的文件**。其他原生/社区/GGUF 加载器可通过 **MiniMax H3 Easy Model Adapter** 接入。

**最快上手**：导入 `workflow/1.MiniMax_H3_Easy.json` → 在 Easy Loader 选模型 → 在主节点选模式 → 输入提示词并接媒体 → 选分辨率/宽高比/时长 → 排队。基本结构是 `Easy Loader → MiniMax H3 Easy → Easy Output → sample/decode/save`，主节点的 `Model` 输出可以继续接 LoRA、模型 patch 或采样器，`H3 Context` 接到 Easy Output。

**媒体输入有四种方式**：

| 方式 | 适合 |
| --- | --- |
| **Media Loader** | 日常推荐；**唯一会缓存"解码后视频参考"的方式** |
| 直接媒体端口连线 | 接普通图像/视频/音频节点，一个端口可接多条虚拟连线 |
| **Media Bridge** | 工作流 API、无头执行、需要显式编号输入 |
| **Media Splitter** | 把一个 Media Bundle 拆成独立的 IMAGE/VIDEO/AUDIO 输出给别的流程 |

**只有 Media Loader 有解码缓存**——同一段参考视频解码一次后可经 ComfyUI 节点缓存复用（换文件会自动失效）；它省的是视频准备时间，**不是采样时间**。Media Splitter 最多可拆出 **27 张图 / 9 段视频 / 9 段音频**。

**`@` 引用与对话块**：在引用/上下文提示词里输入 `@` 选媒体；图、视频、音频**各自独立编号**（`@Image1`、`@Video1`、`@Audio1` 可共存）。输入 `#` 创建对话块，运行时转成 `<d>...</d>`。

**长视频用 Context Segments**：用单独一行 `---` 分隔各段提示词，在 `Segment seconds` 填每段时长（如 `5,5,5`）。**关键规则是：一段只会用在"该段提示词里用 `@` 显式引用"的媒体，引用不继承上一段**——所以每段要用同一素材就得重复写引用。连续性模式有四种：

| 模式 | 携带什么 | 上下文帧数 |
| --- | --- | --- |
| Motion Context | 上一段的视频 latent 作为时间对齐的运动上下文（通常最好的起点） | 5 / 22 / 39 / 56 / 73 |
| RGB Guide | 把上一段尾部重编码成多帧视觉 Guide | 5 / 22 / 39 / 56 / 73 |
| Soft AV Prefix | 同时带视频与音频前缀，边界处音频释放更柔和 | 39 / 90 / 141 |
| Hard AV Prefix | 严格保留重叠的视频与音频前缀 | 39 / 90 / 141 |

README 特别提醒：**要锁定说话人或歌手的音色，优先用 AV Prefix 模式或提供清晰音频参考**——RGB Guide 主要携带视觉边界信息，**不能可靠地锁音色**。

**提示词优化**支持 OpenAI 兼容 Chat Completions、OpenAI Responses、Gemini Native、Ollama，可选读连通媒体，并有针对模式的中英提示词指南。`Segment seconds` 也告诉优化器要产出几条提示词（填 `5,5,5` 就会把一段故事拆成**恰好 3 条**、自动用 `---` 分隔）。

## 使用前需要注意

- **API key 是明文存储（最重要）**：README 明说 API 设置存在插件目录里的 **`prompt_optimizer.json`**，且 **API key 以明文保存**，**不要发布或上传该文件**。如果你会把 ComfyUI 目录同步到网盘/Git，务必先排除这个文件。
- **安装时必须选 Nightly**：这是它自己反复强调的坑——从 ComfyUI Manager 装其他已发布版本**可能落后于本仓库**。换句话说，**这个插件的"正式发布渠道"和仓库并不是同步的**。
- **依赖较新的 ComfyUI 与官方 H3 节点**：README 第一步就要求先把 ComfyUI 更新到包含官方 MiniMax H3 节点的版本，也就是说**这不是一个完全自包含的插件**，基础能力由 ComfyUI 官方提供。
- **模型体积与显存是硬约束**：H3 系列模型需要放到 `diffusion_models`/`text_encoders`/`vae`，并可选 3D latent upscaler；README 提供 **Low VRAM Tile** 用"更多采样时间换更低显存"，说明**默认配置对显存有要求**。
- **`@` 引用不继承**：分段生成时，**每段都得重新写 `@` 引用**，这是最容易写错的一点（写漏了该段就不会用到那个素材）。
- **许可证以 API 为准**：README 正文没写许可证，GitHub API 返回 MIT——**本篇按 API 记录**，正式使用前建议在仓库页确认一次。
- **关注度还小**：880 Star、60 Fork、**1 个关注**，仓库 2026-08 才创建，**属较新的社区插件**，接口与节点名可能变动。
- **我只读 README 与元数据**：未安装、未运行、未生成任何视频；各模式的画质、连续模式的音色锁定效果、显存占用均未验证。

## 什么时候会再看

- 要在 ComfyUI 里**搭 MiniMax H3 的视频生成流程**，又不想手动接一堆节点时。
- 需要**长视频分段 + 段间连续性**（尤其要锁音色的 AV Prefix 模式）时。
- 想参考**"把多模式收进一个主节点 + 统一媒体管理"**的 ComfyUI 插件设计时。
- 想参考**"缺输入就降级而不是报错"**（无音频自动回退、无媒体自动转文生视频）这类健壮性处理时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **生成 / 可视化类**：[manim](manim-数学动画引擎.md)（用代码做数学动画）、[FlyCoder](FlyCoder-果蝇连接组控制CSS.md)。
- **"给助手/工具加能力"的插件层对照**：[dsh-plugins-store](dsh-plugins-store-DSH插件市场.md)、[openclaw](openclaw-自托管个人AI助手.md)。
- **这套节点被用在了什么产品里**：[nvzhuang](nvzhuang-拍片哥女装AI服装参考图集.md)——那边的本地零成本换装后端内置的就是 Qwen-Image-Edit-2511 与 **MiniMax H3** 双工作流，未就绪时自动回退 H3；一篇讲节点怎么用，一篇讲它在真实项目里的落法。

## 参考来源

- 项目仓库与 README：https://github.com/nkxx188/ComfyUI-MiniMaxH3-Easy
- 中文说明：仓库内 `README_CN.md`
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README 与元数据；未安装、未运行。"最省接线""分割细化"等为作者自述，未验证。许可证取自 GitHub API（MIT），README 未声明，使用前建议在仓库页二次确认。
