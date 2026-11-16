# autoclip：把一个链接剪成多平台短视频的开源桌面工具

> 一句话值得记住的取舍：它把"**长视频 → 多平台短视频**"做成一条可复现的流水线（自动选段、字幕、封面、发布文案、ZIP 发布包），并且**剪辑与渲染全部在本机完成**，云端只按需发送字幕 / 文案 / 抽样画面 / 音频；同时提供**桌面版、Docker / Web、CLI / MCP 三种形态共用一条链路**。想研究"本地优先的 AI 视频再加工产线"或"桌面 + CLI + MCP 三形态同源"的工程做法时值得再看。

- 状态：草稿
- 最近核验：2026-11-09（阅读 README 全文（中文）、`LICENSE` 与仓库页内嵌元数据；**未下载安装包、未部署、未运行 `autoclip` CLI 或 MCP、未实际出片**）
- 适用范围：想把播客 / 访谈 / 课程等长视频批量剪成短视频的创作者；macOS（Apple 芯片）或 Windows x64 桌面用户；需自建 Web 服务 / Linux 时走 Docker 或 CLI

## 核心结论

**它解决的具体问题**：一条长访谈 / 播客 / 课程，人工剪成多平台短视频成本很高。autoclip 的卖点是"**贴一个链接就出片**"——选好平台，按平台的画幅与时长要求**自动生成至多 10 条合格片段**（其余列为备选，点"生成这条"再制作），每条都带**封面、标题、简介、话题和 ZIP 发布包**。

**三个"本地优先"的事实值得单独记：**

1. **剪辑、取景与渲染在本机完成**；成片**只有在你选择投稿后**才上传到已连接的平台。
2. **模型自选**：云端 API（OpenAI 兼容，填自己的 API Key）/ Ollama / LM Studio 三种；**本地分析 + 本地转写时不需要对应云端 API**。
3. **有明确的数据流边界**（README 明写）：云端分析会发送相关字幕与文案；画面理解 / 参考帧生图会发送所需抽样画面；云端转写会发送音频；**匿名统计与错误报告可在设置中关闭**。

**三形态共用一条链路**：桌面版（macOS Apple Silicon / Windows x64，安装包自带 Python 与 FFmpeg）、Docker / Web、CLI / MCP。README 明确 "CLI / MCP 与桌面共用同一条一键出片链路"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zhouxiaoka/autoclip](https://github.com/zhouxiaoka/autoclip) |
| 描述 | AutoClip｜一个链接，一键出片。开源 AI 视频剪辑桌面工具，将播客、访谈、课程等长视频自动剪成短视频，生成字幕、封面和发布文案，适配抖音、小红书、TikTok、Reels 与 YouTube Shorts |
| 官网 | https://zhouxiaoka.github.io/autoclip_intro/ （含案例库 `/cases/`） |
| 归属 | 个人开发者 `zhouxiaoka`（README 称"个人维护"） |
| 许可证 | **MIT**（`LICENSE` 1,070 字符，署名 `Copyright (c) 2024 AutoClip Team`） |
| 主语言 | **未取到权威值**（shields.io `languages/top` 返回 `invalid`）；README 提及技术栈含 Tauri、React、FastAPI、FFmpeg、yt-dlp、Whisper、FunASR，**未核验** |
| 关注度 | **10,169 Star / 1,805 Fork / 38 关注 / 14 开放 Issue**（GitHub 页面值，2026-11-09；ungh.cc 镜像报 10,144 Star / 1,805 Fork，**略有差异，以页面值记录**） |
| 维护状态 | 2025-07-08 创建；ungh.cc 报最后推送 **2026-11-09**（核验当日仍有提交，**很活跃**） |
| 默认分支 | `main` |
| 版本 | **19 Release / 22 tags**（最新正式版见 Releases；桌面、CLI、MCP 同步更新） |
| 话题标签 | `ai-agent`、`ai-video-editor`、`content-repurposing`、`desktop-app`、`douyin`、`mcp`、`podcast`、`subtitles`、`tiktok`、`xiaohongshu`、`youtube-shorts` 等 |

## 三形态与安装

README 的"按场景选方式"表：

| 场景 | 方式 | 准备事项 |
| --- | --- | --- |
| 在电脑上出片 | **桌面版** | macOS Apple Silicon 或 Windows x64 |
| 自建 Web 服务 / Linux | **Docker** | Docker 与 Compose v2 |
| 批量处理 / 接入 Agent | **CLI / MCP** | Python 3.10+（建议 3.11）与 FFmpeg |

- **桌面版**：Releases 下载 `.dmg`（macOS）或 `-setup.exe`（Windows），安装包**自带 Python 与 FFmpeg**。macOS 15+ 首次打开若被拦截，需到「系统设置 → 隐私与安全性 → 仍要打开」；Windows 若弹 SmartScreen 点「更多信息 → 仍要运行」。
- **Docker**：`git clone` → `cp env.example .env` → `docker compose up -d --build`；Web 界面 `localhost:3000`，API 文档 `localhost:8000/docs`。
- **CLI / MCP**：下载正式 CLI / MCP ZIP（或 `uvx autoclip-mcp` / `npx skills add zhouxiaoka/autoclip`）。典型命令：

```bash
autoclip --version
autoclip produce talk.mp4 --srt talk.srt --platform douyin --portrait-style podcast --json
autoclip outputs PROJECT_ID --export-kits
```

MCP 服务由客户端启动，需单独调试时另跑 `autoclip mcp`；新入口为 `start_quick_output` / `get_quick_output_status`，旧 `run` / `export` 继续兼容。另附 Agent skill：`skills/autoclip/SKILL.md`。

## 出片与版式

- **平台**：抖音、小红书、TikTok、Reels、YouTube Shorts、**B 站与 YouTube**。
- **默认版式**：抖音 / 小红书默认**访谈式**，TikTok / Reels / Shorts 默认**满屏播客式**；竖版也可自行选择两种版式；**B 站 / YouTube 为横版**。
- 画面**跟随说话人**，无人镜头保留完整画面；自动品牌片尾默认开启（可在设置中关闭）；**复制发布文案不附 AutoClip 署名**。
- **字幕来源**：作者字幕 / 本地 Whisper·SenseVoice / 已配置的云端转写（如百炼 `qwen-audio-3.1-asr-flash`）。README 提示：本地转写首次需下载约 220 MB 模型（国内网络约 15 分钟），装不上或太慢可切云端（音频会上传）。

## 成本与耗时（作者自报的实测）

README 给了开发期间**三条不同素材**的实测（**不是同一输入的速度对照**），费用按当时 `qwen-plus` 的文字模型用量估算，不含云端语音识别、AI 生图或投稿服务费用：

| 版本 | 原片 | 出片 | 文字模型费用估算（人民币） |
| --- | --- | ---: | ---: |
| **新版 · 有字幕** | Jensen · 1h43m（英 → 小红书） | **7.5 分钟 / 10 条** | **¥0.09** |
| 新版 · 无字幕 | TIM × 罗永浩 · 2h52m（中 → 抖音） | 29.5 分钟 / 10 条 | ¥0.20 |
| 旧版 | MrBeast · 2h06m（英 → TikTok） | 65 分钟 | ¥0.64 |

**以上均为作者自述，实际账单以所选服务商为准。**

## 使用前需要注意

- **README 商业化色彩明显（事实）**：正文含 **88API / Infistar.cc / APIMart 三个赞助商位**（各带推广链接与返利说明），另有作者自己的**阿里云 / 百炼推荐链接**（README 自注"这是作者推荐链接，不是赞助"）。**读它时要把"功能说明"与"推广文案"分开看。**
- **云端会产生费用与数据外发**：软件本身免费、MIT；但云端分析、转写与 AI 生图**按所选服务商计费，需自己的凭据**。使用云端分析 / 转写时，字幕、文案、抽样画面或音频**会发往对应服务商**——敏感素材建议走本地模型。
- **macOS 首次打开需手动放行**：README 要求被拦截时用「仍要打开」或右键「打开」，提示可能存在**未公证 / 签名状态问题**（README 未直说是否公证，**未验证**）。较旧 macOS 也应右键「打开」。
- **内容版权归使用者自担**：案例库使用他人访谈 / 播客原片，README 注明"**原片版权归原作者，仅作效果展示**"。批量剪辑第三方素材时的授权与平台条款风险由使用者承担。
- **投稿需第三方账号**：直接投稿需自己的 B 站或 Upload-Post 账号。
- **语言统计未取到权威值**：shields.io 返回 `invalid`，README 只列了"感谢 FastAPI、React、Tauri、FFmpeg、yt-dlp、Whisper、FunASR"，**具体语言占比未核验**。
- **多语言 README**（中 / 英 / 日 / 韩 / 西 / 葡 / 俄 / 法）**可能不同步，未逐版比对**。
- **我未实际使用**：所有功能、耗时与费用均来自 README 自述，**未下载安装包、未部署、未跑通任何一次出片**。

## 什么时候会再看

- 想搭一条"**长视频 → 多平台短视频**"的批量产线、又不想把素材整段上传到某个 SaaS 时。
- 想参考"**本地渲染 + 云端按需分析**"的数据流边界怎么划、以及在 README 里怎么如实标注"什么会上传、什么不会"。
- 想研究"**桌面 App + Docker/Web + CLI/MCP 三形态共用一条核心链路**"的工程组织方式，或对比"**从长视频里挑高光片段（本篇）**"与"**从小说生成短剧（huobao-drama）**"两条 AI 视频产线的差别时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都属于"用素材产出视频"，驱动源不同**：[adu-motion-video：阿杜的 AI 剪辑模板技能与 MotionGit 开放格式](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)——那篇是**把代码级动画模板交给 AI 助手替换内容**（模板驱动、可导出可编辑工程）；本篇是**把一个链接剪成多平台短视频的桌面工具**（真实素材剪取）。可对照"**模板化的确定性风格**"与"**源素材自动分段出片**"。
- **同属"把素材剪成成片"的视频工具**：[OpenMontage：把 AI 编码助手变成视频制作工作室](OpenMontage-把AI编码助手变成视频制作工作室.md)——那篇是**交给 AI 编码助手编排的通用视频产线**；本篇是**把一个链接剪成多平台短视频的桌面工具**。可对照"**窄场景桌面工具**"与"**通用 agent-first 产线**"。
- **同为"AI 驱动的视频生产"，起点与终点不同**：[huobao-drama：一句话生成 AI 短剧的自动化生产平台](huobao-drama-一句话生成AI短剧的自动化生产平台.md)——那边是**从小说文本"凭空造"短剧**（剧本 → 分镜 → 文生视频 → 拼接），本篇是**把已有的长视频"剪成"短视频**（选段 → 字幕 → 封面 → 发布包）。一个解决"无中生有"，一个解决"长剪短"；可对照两者在多厂商视频模型接入与 FFmpeg 拼接上的取舍。
- **同为"用素材产出视频 / 动画"，驱动源不同**：[srt-whiteboard-animation：把 SRT 字幕做成白板手绘动画的技能](srt-whiteboard-animation-把SRT字幕做成白板手绘动画的技能.md)——那篇是**字幕驱动、分段确认**的画布动画技能；本篇是**长视频驱动、自动分平台出片**的桌面工具。可对照"生成式逐段落墨"与"真实素材剪取"两种产线。
- **素材来源侧的相关工具**：[videdown：开源视频下载工具](videdown-开源视频下载工具.md) 与 [galaxy-downloader：多平台媒体下载器](galaxy-downloader-多平台媒体下载器.md)——本篇出片前需要先拿到源视频（README 也提到依赖 yt-dlp），这两篇是"取素材"的一环；账号与版权边界同样要自担。
- **同属"长片进、短视频出"，落地形态不同**：[capcut-cli：直接读写剪映草稿的命令行剪辑工具](capcut-cli-直接读写剪映草稿的命令行剪辑工具.md)——那是**面向 agent 的 CLI + Library**，产物落在**剪映草稿**里；本篇是**端到端桌面应用**（自动选段、字幕、封面、发布包，本机渲染）。可对照"**端到端桌面工具**"与"**可编程的草稿层工具**"。
- **同为"素材进、短视频出"，定位不同**：[baocut：带可编辑时间轴的 AI 视频 Agent](baocut-带可编辑时间轴的AI视频Agent.md)——本篇是**开箱即用的桌面工具**（自动选段、字幕、封面、发布包，偏"成品流水线"），那篇是**给 Agent 驱动的可编程编辑器**（偏"可反复改的工作台"）；可对照两种出片路线。

## 参考来源

- 项目仓库与 README（多语言）：https://github.com/zhouxiaoka/autoclip
- 官网与案例库：https://zhouxiaoka.github.io/autoclip_intro/ 、https://zhouxiaoka.github.io/autoclip_intro/cases/
- 许可证：https://github.com/zhouxiaoka/autoclip/blob/main/LICENSE（MIT，署名 `2024 AutoClip Team`）
- 最新发行版：https://github.com/zhouxiaoka/autoclip/releases/latest
- 隐私说明：`docs/PRIVACY.md`；成本与时间：`docs/COST_PER_VIDEO.md`

以上信息核验于 2026-11-09，Star 数与仓库内容可能变化。**未下载安装包、未部署、未运行任何出片流程**；功能、耗时与费用均为 README 自述；GitHub API 匿名配额用尽，未取到 `size` 与贡献者统计。
