# baocut：带可编辑时间轴的 AI 视频 Agent

> 一句话定位（README 自述）：**"The video editor built for agents."**——**你的 Agent 剪辑，你来复核与微调**。把 BaoCut 交给 **Claude Code / Codex / Cursor** 等 Agent，用一句话让它**转写、上字幕、翻译、配音、口播剪辑、从零做解说片**；结果落在**可继续编辑的时间轴**上（SRT / MP4 / 音频 / 转写都只是它的导出）。最值得记的三点：① 它的**四张面孔共用同一个本地 Runtime**——桌面应用（Electron）、CLI `baocut`、MCP 服务 + Skill、Web 客户端；② "**改一处只改一处**"：Agent 只动你圈定的范围并返回回执，改错可撤销（渲染缓存按真实依赖失效）；③ 许可证是**自定义的 `BaoCut Community License` 1.0（2026-10-07），属"源码可见（source-available）"而非标准开源**——**用它做内容、卖成片免费**，但**要把 BaoCut 本身或其代码商业化需另签书面许可**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（389 行）、`LICENSE`（BaoCut Community License 1.0，184 行）与已核实元数据；**未 clone、未安装应用、未运行 `baocut`、未安装 Skill / MCP、未生成或渲染过任何视频**）
- 适用范围：**macOS 14+（Apple Silicon）/ Windows x64**（CPU / CUDA / Vulkan 三种构建）；源码开发需 **Node.js 24.22.1+（LTS）**、Rust、CMake；**媒体分析 / 转写准备 / 导出还需 `ffmpeg` 与 `ffprobe`**（不随包提供）

## 核心结论

1. **"为 Agent 而生"是核心差异**（README 自述）：传统剪辑器为人的手设计，Agent 得绕着界面干活；多数 AI 视频工具只**渲染一条 MP4 出来**，第二句翻译错了就得重来。BaoCut 让 Agent**通过命令剪辑**，并把结果留在**时间轴**上。
2. **"命令而非点击"**：CLI、MCP 服务与 Skill **共享同一套工具目录**，每条命令都自述参数与效果，**Agent 从不点界面**。
3. **"人和 Agent 共用一个编辑器"**：你拖一个片段、Agent 改一条字幕，**都走同一命令网关、事务与撤销栈**；你随时可以接手。
4. **"有界自主 + 诚实交付"**：Agent 只在显式的**范围、访问模式与预算**内工作，破坏性操作走审批规则；交付时报告"**测了什么、实际看了哪些帧、哪里还需要人耳复核**"，检查失败会如实报失败。
5. **许可证是 source-available**：`BaoCut Community License` 1.0（2026-10-07）；shields 显示 license 为 "**not identifiable by github**"（因是自定义许可）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [JimLiu/baocut](https://github.com/JimLiu/baocut)（注意 owner 大小写为 `JimLiu`） |
| 描述（原文） | "BaoCut: an AI video agent with an editable timeline. Transcribe, subtitle, translate, dub, cut and animate." |
| 许可证 | **`BaoCut Community License` Version 1.0（2026-10-07）**——**source-available**；`LICENSE` 184 行；shields 报 "**not identifiable by github**" |
| 关注度 | **页面 Star 1,761 / Watcher 3 / Fork 177**；**ungh 镜像 1,711 / 3 / 172**；shields：开放 Issue **2**、贡献者 **9**（2026-11-13） |
| 维护状态 | **2026-07-13 创建**；ungh `pushedAt` **2026-11-10**、页面 `updatedAt` **2026-11-10**；默认分支 **`main`** |
| Release | **ungh 30 条（单页上限）/ 页面 83 条**（2026-11-13） |
| 平台 | **macOS 14+ Apple Silicon** 与 **Windows x64（CPU / CUDA / Vulkan）**；macOS 包 Developer ID 签名并公证，**Windows 包未签名** |
| 分发 | 官网 <https://baocut.com>（`/download/mac-dmg` 等动态链接）+ [GitHub Releases](https://github.com/JimLiu/baocut/releases)；源码开发经 `npm ci` |
| 工程结构 | `apps/`（desktop / runtime / cli / web / 各编辑器页 / portal）、`packages/`（protocol / runtime-core / agent-drivers / providers / ui / slides / design / docs 等）、`crates/`（video-engine / timeline / render-graph / engine-host / export-worker / model-worker 等）、`bindings/preview-wasm`、`skills/`、`templates/` |

## 功能与用法

### 来自已有素材（README 摘要）

- **转写**：本地模型（Whisper large-v3 / v3-turbo、Qwen3-ASR、MOSS（带说话人分离））或在线 API，**词级时间**；片段在任务进行中**流式进入时间轴**。
- **字幕**：原文 / 翻译 / **双语**；翻译由 **Agent 逐句完成**；字幕样式按项目记忆。
- **配音**：翻译 → **HTDemucs 分离人声与背景** → 按句合成（克隆音或预置音色）→ 新音轨铺回时间轴。
- **口播剪辑**：在时间轴上**去掉口头禅、长停顿、重录与卡壳**，**不动原始媒体**。
- **派生**：章节、摘要、标题、博客、短视频——**每条主张都可追溯到时间戳**。
- **素材处理**：防抖（`vid.stab` / deshake）、超分（Real-ESRGAN）、平滑慢动作（RIFE）、调色对齐、**无绿幕抠像**（rembg 权重）、**音轨分离**（HTDemucs）——**各写新文件、绝不动原文件**。
- **找剪辑点**：镜头检测（含手持与淡入淡出）、无转写的静音检测、**按歌曲节拍 / 小节 / 重音卡点**。

### 来自一段简述（brief）

- **一句话出片**：**24 个内置场景模板**（解说、产品发布、预告、教程、数据故事、字体动效等）。
- **解说优先**：先写脚本、一次合成配音，再**把画面剪到旁白上**。
- **虚拟主播**：卡通角色**对口型、眨眼、呼吸、转头**；可用内置主播，或**从一张正脸照生成**；帧由引擎计算，**同一旁白每次渲染结果一致**。
- **动效即代码**：Agent 写**自包含 HTML 合成**（下三分之一、大字数字、标注、品牌 sting）导入为 code bundle 并烘帧上时间轴；支持 `baocut/1` 与 `hyperframes/1` 契约。
- **图像生成**：需要静帧镜头时用本地 Qwen-Image 或在线供应商。

**交付**：烧录 / 外挂字幕的 MP4、SRT / WebVTT、转写、音轨、可跨机继续编辑的 **`.baocut` 包**（含视频快照与全部文档 / 资产版本，**不含原视频的完整事务 / 撤销历史**）。

### 四种入口，一个 Runtime（README）

| 入口 | 说明 |
| --- | --- |
| **桌面应用（Electron）** | Home 里与 Agent 协作、Space 浏览项目产物、完整时间轴编辑器（预览 / 撤销 / 实时字幕） |
| **CLI `baocut`** | 脚本与终端用的瘦客户端；管道时输出 JSON，**退出码告诉你是否需要用户介入** |
| **MCP 服务 + BaoCut Skill** | 你自己的 Agent（Claude Code、Codex、Cursor、Gemini CLI…）经**同一工具目录**驱动 BaoCut |
| **Web 客户端** | 用 `baocut web open --video <id>` 的一次性链接在浏览器打开某视频的编辑器，供**远端 Agent 展示成果** |

### 快速开始（README）

```bash
baocut status            # 列出本机当前能做什么、缺什么（本地模型、ffmpeg 等），各带修复建议
baocut skill install     # 把 BaoCut skill 装进 ~/.agents/skills 与 ~/.claude/skills（随应用更新）
baocut mcp install       # 走 MCP 的 Agent 用这条
```

## 使用前需要注意

- **许可证边界要分清（`LICENSE` 原文）**：**免费**用于个人 / 学习 / 研究、非商业修改与再分发、公司内部使用与定制；**免费**用 BaoCut 创作并**出售内容**（广告、付费视频、为客户做剪辑都行，**不需署名 / 水印 / 公开源码**，内置原创模板元素也覆盖）。**需另签书面商业许可**的情形：**出售 / 出租 / 收费授权软件或其修改版、改名重发布、作为托管服务 / 平台 / API 对外提供、把 BaoCut 代码或引擎嵌进商业软件产品**——**无论你是否修改、是否公开源码**。商业授权联系维护者 **Jim Liu（junminliu@gmail.com）**。
- **"源码可见"≠"开源"**：shields 也标 "**not identifiable by github**"；**独立编写、只用公开接口、不含 BaoCut 代码的插件 / 客户端**不受本许可覆盖，但**打包或托管 BaoCut 本身仍受限**。
- **平台与硬件有前提**：正式包只覆盖 **macOS 14+ Apple Silicon**（签名 + 公证）与 **Windows x64**（**未签名**）；Windows 装前需先装 **VC++ v14 x64 运行库**；**GPU 变体需匹配的显卡驱动**，且 README 明说"**GPU 推理尚未在硬件上测试**"（**自述未验证**）。
- **Agent 功能依赖外部引擎**：Agent 能力需**已登录的 Codex CLI 或 Claude Code**；媒体分析 / 转写准备 / 导出还需 **`ffmpeg` 与 `ffprobe`**——**这些外部工具不随包提供**。
- **部分本地模型是"研究 / 非商业"许可**（README Status）：**OmniVoice、Fish Audio S2 Pro、Qwen-Image** 等权重带**非商业许可**，应用会在你选择前提示；**商用前务必确认**。
- **源码构建较重**：完整桌面开发需 **Node.js 24.22.1+（LTS）、Rust、CMake、平台构建工具**与 **wasm 目标**；`npm run dev:lite` 可跳过 Rust / WASM，但**全新检出下"剪辑、预览、导出、本地推理与语音处理都不可用"**。首次构建**耗时长、占盘大**。
- **Windows 凭据与迁移未经充分验证**（README Status，**自述未验证**）：Windows Credential Manager 存储与 v2 凭据迁移已包含，但"**实时凭据迁移尚未经 CI 测试**"。
- **我只读了 README 与 LICENSE**：**未 clone、未安装、未运行 `baocut`、未装 Skill / MCP、未生成或渲染任何视频**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想让 **Agent 用一句话完成转写 / 字幕 / 翻译 / 配音 / 口播剪辑**，且**结果还要能自己继续改**时。
- 想研究"**人和 Agent 共用一个时间轴 + 同一事务 / 撤销栈**"这种"**命令而非点击**"的编辑器设计时。
- 想参考"**Agent 有界自主 + 诚实交付（报告看了哪些帧、哪里需人耳）**"的工程约定时。
- 想在**不违反 source-available 许可**的前提下判断"**我这种用法要不要另外付费**"时（重读许可证第 2–3 节）。
- 想对照库内其它"AI 做视频"路线：[video-shotcraft](video-shotcraft-用Remotion做电影感产品宣传片的视频技能.md) · [capcut-cli](capcut-cli-直接读写剪映草稿的命令行剪辑工具.md) · [autoclip](autoclip-把一个链接剪成多平台短视频的桌面工具.md)。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"给编码助手一套视频技能 / 流程"**：[video-shotcraft：用 Remotion 做电影感产品宣传片的视频技能](video-shotcraft-用Remotion做电影感产品宣传片的视频技能.md) · [adu-motion-video：阿杜的 AI 剪辑模板技能与 MotionGit 开放格式](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)——那两篇是**Skill + 模板 / 配方卡**（偏"把编码助手变成制作者"），本篇是**带本地 Runtime 与可编辑时间轴的应用 + Skill + MCP**（偏"Agent 剪辑、人复核"）。三者的输出都强调**可继续编辑**。
- **产物都落在"可继续编辑的剪辑工程"**：[capcut-cli：直接读写剪映草稿的命令行剪辑工具](capcut-cli-直接读写剪映草稿的命令行剪辑工具.md)——那篇**直接读写剪映 / CapCut 草稿 JSON** 做字幕、变速、长转短；本篇把结果留在 **Baocut 自己的时间轴**上。可对照"**寄生在剪映生态**"与"**自带引擎与协议**"。
- **同为"素材进、短视频出"**：[autoclip：把一个链接剪成多平台短视频的桌面工具](autoclip-把一个链接剪成多平台短视频的桌面工具.md)——那篇是**开箱即用的桌面工具**（自动选段、字幕、封面、发布包），本篇是**给 Agent 驱动的可编程编辑器**。一个偏"成品流水线"，一个偏"可反复改的工作台"。
- **同属"把 AI 编码助手变成视频工作室"**：[OpenMontage：把 AI 编码助手变成视频制作工作室](OpenMontage-把AI编码助手变成视频制作工作室.md)——那篇是**从研究到成片的完整流水线**，本篇是**以时间轴为中心的编辑器**。可对照"**流水线自治**"与"**人在环的可编辑工程**"。

## 参考来源

- 仓库主页与 README（389 行；核验日期 2026-11-13）：https://github.com/JimLiu/baocut
- 许可证：`LICENSE`（BaoCut Community License Version 1.0，2026-10-07，184 行；核验日期 2026-11-13）；`THIRD_PARTY_NOTICES.md`
- 官网与下载：<https://baocut.com> · Releases <https://github.com/JimLiu/baocut/releases>
- 文档：`docs/`（产品设计、架构、格式与协议规范、验收）、`skills/`、`templates/`、`AGENTS.md`、`apps/desktop/README.md`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc 交叉核对（2026-11-13，**页面值与镜像值略有差异**；Release 数 ungh 受单页上限影响）；开放 Issue 数 / 贡献者 / license 识别取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未安装应用、未运行 `baocut`、未安装 Skill 或 MCP、未生成或渲染任何视频**；能力与流程描述均来自 README 与 `LICENSE`，**未逐一验证**。
