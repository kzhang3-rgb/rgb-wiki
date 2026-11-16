# Voicebox：本地优先的开源 AI 语音工作室

> 它把**语音 I/O 闭环的两端做进同一个本地 App**——一边是 ElevenLabs 那一侧（克隆音色 + 语音合成），一边是 WisprFlow 那一侧（全局热键听写 + 自动粘贴），中间用**一个本地 LLM** 把润色与"人格"接起来，**全程跑在你自己机器上**；再通过 **MCP 给任何 Agent 一个"可选的嘴"**。最值得记的有三点：① **8 个 TTS 引擎 + 23 语种 + 零样本克隆**全在本地；② **给 Agent 发声坚持"绝不静默"**——每次 Agent 说话都弹出同一个悬浮条（与听写共用一套状态机）；③ **但它也把"声音克隆"这个高风险能力做得触手可及**，而 README 正文**未见"仅限本人 / 已获授权"之类的防滥用声明**。

- 状态：草稿
- 最近核验：2026-11-12（阅读 README 全文、`LICENSE`、仓库文件树与元数据；**未下载、未安装、未运行、未克隆声音、未接任何模型或 MCP 客户端；未试听任何音频；未逐份核对 `docs/` 与各 TTS 模型自身的许可**）
- 适用范围：桌面 App —— **macOS（Apple Silicon / Intel，DMG）· Windows（MSI）· Docker**；**Linux 暂无预编译包**（需自行构建）。GPU 后端覆盖 **MLX（Apple Silicon）/ CUDA / ROCm / DirectML / IPEX-XPU / 纯 CPU**。开发需 Bun + Rust + Python 3.12 + Tauri 前置依赖 + `just`

## 核心结论

**一句话定位（README 自述）**：免费的、开源的 **ElevenLabs + WisprFlow 合体**——"**The full voice I/O stack, running locally on your machine.**" 两个云端头部各占语音闭环的一半（ElevenLabs 管输出、WisprFlow 管输入），Voicebox 把**两半都做了**，并用一个本地 LLM 桥接。

| 方向 | 能力 |
| --- | --- |
| **输出（TTS）** | 8 个引擎、23 语种、零样本克隆、50+ 预设音色、副语言情感标签、后处理效果、无限长自动分块 |
| **输入（听写）** | 全局热键按住/切换说话、macOS 目标感知自动粘贴、任意输入框内麦克风、Whisper 转写 |
| **桥接** | 本地 Qwen3 LLM：听写润色（去掉 umm / 结巴 / 假起头）+ 音色"人格"改写 |
| **对外** | REST API（`127.0.0.1:17493`）+ 内置 **MCP server**（4 个工具）+ 打包的 stdio shim |

**三个最值得记的点：**

1. **本地优先是它对着云服务打的差异点**：README 写"models, voice data, and captures **never leave your machine**"。8 个 TTS 引擎、Whisper、本地 LLM 全部在本地跑；平台后端矩阵也铺得很全（MLX / CUDA / ROCm / DirectML / XPU / CPU）。

2. **给 Agent 发声音时"绝不静默"**：README 明确写 **no silent background TTS** —— 每次 Agent 发起的朗读都会把**同一个 OS 级悬浮条**显示出来（带音色名），并且听写的 `recording / transcribing / refining` 与朗读的 `speaking` **是同一个悬浮条的四种状态**，"一个心智模型管两个方向"。还支持**按客户端绑定音色**（把 Claude Code 绑 Morgan、Cursor 绑 Scarlett），并靠 `last_seen_at` 确认客户端真的接上了。

3. **它把"声音克隆"的门槛压到很低**：几秒参考音频即可零样本克隆，或直接用 50+ 预设音色；再叠上"人格"与 Agent 发声，**滥用面（冒充、诈骗、伪造他人语音）几乎与便利面一样大**。**README 正文未见授权/防滥用声明**（我未逐份核对 `docs/`）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [jamiepine/voicebox](https://github.com/jamiepine/voicebox) |
| 描述 | The open-source AI voice studio. Clone, dictate, create. |
| 官网 / 文档 | <https://voicebox.sh> ・ <https://docs.voicebox.sh> |
| 归属 | 账号 `jamiepine`（个人，页面 `isOrg: false`、`isFork: false`、`isArchived: false`；作者是 **Spacedrive** 的开发者 Jamie Pine） |
| 许可证 | **MIT**（`LICENSE` 1,078 字符，署名 `Copyright (c) 2026 Voicebox Contributors`）——**注意：这只管本仓库代码，8 个 TTS / STT 模型各有各的许可**（见下） |
| 主语言 | **Python 46.3%**（FastAPI 后端）；其余为 TypeScript（React 前端）与 Rust（Tauri） |
| 关注度 | **60,475 Star / 7,594 Fork / 265 关注 / 405 开放 Issue**（页面值，2026-11-12；ungh.cc 镜像报 60,403 Star / 7,588 Fork，略有差异，以页面值记录；Issue 数取自 shields.io） |
| 维护状态 | **2026-01-25 创建**；ungh 报 pushed **2026-10-07**（约 5 周前）；页面 `updatedAt` 2026-11-11（多为 Star / 徽章刷新） |
| 默认分支 | `main` |
| 版本 | **25 Release / 27 tags**（含 macOS / Windows 安装包与 Docker 资产） |
| 话题标签 | `ai` · `cuda` · `mlx` · `qwen3-tts` · `qwen3-tts-ui` · `voice-ai` · `voice-clone` · `whisper` |

## 能力一览

**① 多引擎语音克隆**（README 自报，未试听）：

| 引擎 | 语种 | 特点（README 自述） |
| --- | --- | --- |
| **Qwen3-TTS**（0.6B / 1.7B） | 10 | 高质量多语种克隆，支持"慢点说""用气声"这类**交付指令** |
| **Qwen CustomVoice** | 10 | 9 个预设音色 + 自然语言控制，**无需参考音频** |
| **Qwen VoiceDesign** | 10 | **用文字描述凭空造音色**，无需参考音频或预设 |
| **LuxTTS** | 英语 | 轻量（约 1 GB 显存）、48 kHz、CPU 上 **150 倍实时** |
| **Chatterbox Multilingual** | **23** | 语种覆盖最广（阿拉伯语、丹麦语、芬兰语、希腊语、希伯来语、印地语、马来语、挪威语、波兰语、斯瓦希里语、瑞典语、土耳其语等） |
| **Chatterbox Turbo** | 英语 | 350M 快速模型，**唯一支持副语言标签**（见下） |
| **TADA**（1B / 3B，HumeAI） | 10 | 语音-语言模型，700 秒以上连贯音频，文本-声学双对齐 |
| **Kokoro** | 8 | **50 个预设音色**，82M 极小模型，CPU 推理快 |

**② 副语言 / 情感标签（只有 Chatterbox Turbo 认）**：输入框敲 `/` 可插入标签，如 `[laugh]` `[sigh]` `[gasp]` `[cough]` `[whispering]` `[angry]` `[sarcastic]` 等；**其它引擎会把这些当普通文本照读**（README 明说）。

**③ 后处理效果**：8 种（变调最高 ±12 半音、混响、延迟、合唱/镶边、压缩、增益 ±40 dB、高通、低通），基于 **Spotify 的 `pedalboard`** 库；内置 4 个预设（Robotic / Radio / Echo Chamber / Deep Voice），可设为某音色的默认链。

**④ 无限长度生成**：按句子边界自动切块、逐块独立生成再**交叉淡入淡出**（0–200 ms 可调），过渡顺滑；**最长 50,000 字符**；切分尊重缩写、CJK 标点与 `[标签]`。

**⑤ 生成版本与出处追踪**：每次生成保留多版本（Original / Effects 版本 / 用新随机种子重生成的 Takes / 收藏），**每个版本记录自己的"血缘"**。

**⑥ 异步生成队列**：非阻塞提交，**串行执行避免 GPU 争抢**，SSE 实时状态；崩溃遗留的陈旧任务**启动时自动恢复**。

**⑦ Stories 编辑器**：多轨时间线，做对话 / 播客 / 叙事；拖拽编排、内联裁剪与切分、播放头同步、按片段锁定版本。

**⑧ 听写与语音输入**：可配置和弦热键（按住说 / 轻按切换；**按住说话时中途轻按空格，会无缝升级为切换会话**）；**macOS 目标感知粘贴**——用辅助功能校验后注入焦点输入框，并**原子化保存/恢复剪贴板**（不冲掉你的剪贴板）；首次运行有引导页，深链跳系统设置授予**辅助功能**与**输入监控**权限。

**⑨ 语音转文字**：跑 OpenAI **Whisper**（Base / Small / Medium / Large / **Turbo**），Apple Silicon 走 MLX、其它走 PyTorch；同一模型同时服务听写、Captures 页与 `/transcribe` API。规划中还有 Parakeet v3 与 Qwen3-ASR。

**⑩ Captures**：每次听写 / 软件内录音 / 上传音频都落到这里，**原始音频 + 转写始终保留**；可回放、换 Whisper 尺寸重新转写、用本地 LLM 重新润色、内联编辑、一键"用克隆音色朗读"、或升级为某音色的参考样本。

**⑪ 音色人格**：给音色挂一段自由文本人格（它是谁、怎么说话、在意什么），由**本地 Qwen3**（0.6B / 1.7B / 4B，与 TTS / STT 共用运行时）驱动两个动作——**Compose**（随机丢一句符合人设的台词进输入框）与 **Speak in character**（把你的输入先改写成它的口吻再合成）。

## 给 Agent 加声音：REST + MCP

- **REST API**（默认 `127.0.0.1:17493`）：`POST /generate`、`POST /speak`、`POST /transcribe`、`GET /profiles`；完整文档在 `http://127.0.0.1:17493/docs`。
- **内置 MCP server**（FastMCP 挂在 `/mcp`，Streamable HTTP），**4 个工具**：`voicebox_speak`、`voicebox_transcribe`、`voicebox_list_captures`、`voicebox_list_profiles`。Claude Code 一行接入：

  ```
  claude mcp add voicebox --transport http \
    --url http://127.0.0.1:17493/mcp \
    --header "X-Voicebox-Client-Id: claude-code"
  ```

- **音色解析优先级**：显式参数 → 按客户端绑定 → `capture_settings.default_playback_voice_id`。
- 对只支持 stdio 的客户端，用 App 内打包的 `voicebox-mcp` 二进制；`POST /speak` 也暴露给不走 MCP 的东西（ACP / A2A / shell 脚本）。
- 仓库根目录**预置了 `.mcp.json`**：在这个 checkout 里跑 Claude Code，dev App 一起来就自动带上 Voicebox 的 MCP 工具。
- `voicebox_speak({ text, profile, personality })` 里传 `personality: true`，就等于把工具变成一条 **文本 → 人格 LLM → TTS** 的流水线。

## 技术栈与平台支持

| 层 | 技术 |
| --- | --- |
| 桌面 App | **Tauri（Rust）**，README 特意强调"**不是 Electron**" |
| 前端 | React、TypeScript、Tailwind CSS；状态用 Zustand + React Query |
| 后端 | **FastAPI（Python）** |
| TTS / STT | 上述 8 个 TTS 引擎；Whisper / Whisper Turbo（PyTorch 或 MLX） |
| 本地 LLM | Qwen3（0.6B / 1.7B / 4B），与 TTS / STT 共用运行时与显存 |
| 其他 | SQLite、Pedalboard（Spotify）、WaveSurfer.js、librosa |
| Native Shim | Rust（Tauri 内）：全局热键、粘贴注入、焦点内省 |

GPU 后端：macOS = MLX（Metal，README 称经神经引擎快 4–5 倍）· Windows（NVIDIA）= CUDA（**App 内自动下载 CUDA 二进制**）· Linux（NVIDIA）= CUDA（需自备本地/远程 Python 后端）· Linux（AMD）= ROCm（自动配 `HSA_OVERRIDE_GFX_VERSION`）· Windows（任意 GPU）= DirectML · Intel Arc = IPEX/XPU · 任意 = CPU。

## 使用前需要注意

- **⚠️ 声音克隆的伦理与法律风险（最重要）**：几秒音频即可克隆音色，再配合"人格"与 Agent 自动发声。**README 正文未见"仅限本人或已获授权""禁止用于冒充 / 诈骗"之类的防滥用声明**（我只读了 README 与元数据，**未逐份核对 `docs/`**，也未确认 App 内是否有首次使用同意流程）。**克隆他人声音在多地涉及人格权 / 肖像权（声音权）与诈骗风险，使用前请自行确认授权与本地法律。**
- **⚠️ MIT 只管代码，模型另论**：仓库是 MIT，但它**下载并打包的 8 个 TTS 引擎与 Whisper 各有各的许可**（含第三方公司模型，如 HumeAI 的 TADA）。**README 本页未集中列出这些模型的许可**——**要商用请逐个到各模型仓库核对**（我未核验）。
- **⚠️ 405 个开放 Issue 是个不小的数字**：以 6 万 Star 的体量看积压偏多；README 提到仓库内有一份 `docs/PROJECT_STATUS.md`（在维护的"活文档"）与一个 `triage-prs` 的 agent skill 用来分流。**上手前建议先扫一遍高频问题。**
- **⚠️ macOS 上要授予高权限**：听写自动粘贴依赖**辅助功能 + 输入监控**权限，并会把文本**注入焦点输入框**（它用原子化剪贴板保存/恢复，但仍是系统级注入能力）。
- **平台落差（事实）**：**Windows / Linux 的听写自动粘贴尚未实现**（列在 Roadmap，计划用 `SendInput` / `uinput`·AT-SPI）；**Linux 目前没有预编译包**，要自己构建；预编译安装包只覆盖 macOS（Apple Silicon / Intel）与 Windows。
- **"由 AI Agent 维护"这件事写在首页（事实）**：README 有专门的 "Built with Capy" 段，说项目由 **Capy** 的一个 Captain 线日常推进、其 crew 线在 Linux 与 Mac Studio 上测试 / rebase / 评审每个 PR，再由 Jamie 合并；页顶还有 **Capy 赞助徽章**（带 `utm_*` 跟踪参数）、DeepWiki 与 Trendshift 徽章。**"保持 MIT、无启动画面 / 横幅 / 打断"是他们自己给出的承诺**——值得当作一个"**AI Agent 主力维护的开源项目**"样本来观察。另：仓库 `app/src/assets/sponsors/` 下有一个 `openai.svg`，**README 未解释其用途（未核实）**。
- **活跃度按 `pushed_at` 看，不是 `updated_at`**：ungh 报 **pushed 2026-10-07**（距核验日约 5 周），而页面 `updatedAt` 是 2026-11-11（很可能只是 Star / 徽章刷新）。**判断近期代码活跃度以 push 时间为准。**
- **仓库不算小（事实）**：`main` 分支文件树约 **633 个文件**（含 10 种语言 i18n、自动生成的 TS API 客户端、约 4 MB 的 logo PNG 与多张截图 webp）；**克隆或抓取时留意体积**。
- **我未做任何验证**：**未下载安装包、未安装、未运行、未克隆任何声音、未接任何 MCP 客户端、未试听任何音频**；上述引擎数量、语种数、效果种类、平台矩阵、`no silent background TTS` 等**均来自 README 与仓库文件自述**。GitHub API 匿名配额用尽，未取到 `size` 与贡献者统计。

## 什么时候会再看

- 想搭一套**完全本地、不上传语音**的 TTS + 听写工作流（尤其在意"录音与音色不出本机"时）。
- 想给**编码 / 桌面 Agent 加一个"说话"通道**：看它怎么把 MCP 工具、按客户端绑定音色、以及"**绝不静默发声**"的悬浮条契约设计在一起。
- 想参考"**一个 App 同时做语音输入与输出、并用同一个本地 LLM 桥接**"的架构（Tauri + FastAPI + 多引擎可插拔 + 序列化 GPU 队列）。
- 想研究"**把 8 个第三方 TTS 引擎统一到一个 App**"的工程做法（后端协议、前端接线、PyInstaller 打包；仓库还配了 `add-tts-engine` 的 agent skill）。
- 想观察"**由 AI Agent 主力维护的开源项目**"如何运转（Capy 的 Captain / crew 分工、agent skills、`PROJECT_STATUS.md`）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属本地语音方向，一个是软件、一个是候选清单**：[free-voice-clone：本地免费语音克隆与音乐生成模型清单](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)——那篇**汇总本地可跑的语音 / 音乐模型**（含是否零样本克隆与各模型许可）；本篇是**能直接用的语音工作室**。**可先在那份清单挑引擎，再回本篇看成品形态**。
- **同为"把语音与 MCP 放在一起"，形态相反**：[xiaozhi-esp32：基于 MCP 的 ESP32 语音聊天机器人](xiaozhi-esp32-基于MCP的ESP32语音聊天机器人.md)——那是**硬件端**：音箱 / 麦克风 + 设备端 MCP，语音是"人对着机器说话"；本篇是**软件端**：Tauri 桌面 App + 对外的 MCP server，语音是"**机器对你说**"（`voicebox_speak`，且坚持不静默）。**两篇合看正好覆盖语音与 Agent 结合的"输入端"与"输出端"。**
- **同为"给 MCP Agent 补一块它自己缺的能力、且本地优先"**：[mnemos-mcp：让 AI 拥有自己长期记忆的 MCP 服务器](mnemos-mcp-让AI拥有自己长期记忆的MCP服务器.md)——那个给 Agent 一份**记忆**（本地 SQLite，跨会话延续）；本篇给 Agent 一张**嘴**（本地 TTS，克隆音色发声）。两者都靠"**装成一个 MCP server，让宿主 Agent 多一种能力**"来扩展 Agent，可对照"**记住**"与"**发声**"两种扩展方向。
- **几乎同题的另一篇，差别正好互补**：[VoiceStudio：全本地运行的 AI 语音工作室（AGPL 的 ElevenLabs 替代）](VoiceStudio-全本地运行的AI语音工作室与ElevenLabs替代.md)——两篇都是"本地优先的开源 AI 语音工作室 / ElevenLabs 替代"，但**许可**（本篇 **MIT**，那篇 **AGPL-3.0**）、**桌面壳**（本篇特意强调"**不是 Electron**"用 Tauri，那篇 0.5.3 后改为 Electron）、**规模**（本篇 8 个 TTS 引擎 / 23 语种，那篇自称 646 语种）、**给 Agent 发声的契约**（本篇坚持"**绝不静默**"并按客户端绑定音色，那篇走"本地 API + MCP"）、**维护方式**（本篇由外部 Agent 团队推进，那篇有 Agent PR 指南 + 身份校验 + CLA 自动化）各不相同。**要选型时建议两篇对着读。**
- **本篇所装引擎的独立单篇（同组三篇）**：[Qwen3-TTS](Qwen3-TTS-阿里开源的端到端语音生成与克隆模型.md)（**Apache-2.0**，授权最宽松）· [VibeVoice](VibeVoice-微软开源语音AI的TTS下架与ASR演进.md)（**TTS 已下架**，引用其 TTS 段落时须注意）· [fish-speech](fish-speech-Fish-Audio的开源语音生成与克隆模型.md)（**FACL：营收门槛 + Hosted Service 需单独授权**）。想弄清"某个引擎能不能商用、还在不在维护"时，回这三篇看细节。

## 参考来源

- 项目仓库与 README：https://github.com/jamiepine/voicebox
- 官网 / 文档：https://voicebox.sh ・ https://docs.voicebox.sh
- 许可（MIT）：https://github.com/jamiepine/voicebox/blob/main/LICENSE
- 版本与安装包：https://github.com/jamiepine/voicebox/releases/latest
- MCP 指南：https://github.com/jamiepine/voicebox/blob/main/docs/content/docs/overview/mcp-server.mdx
- 工程状态活文档：https://github.com/jamiepine/voicebox/blob/main/docs/PROJECT_STATUS.md
- 元数据（Star / Fork / 时间 / 语言 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**未下载、未安装、未运行、未克隆声音、未接任何 MCP 客户端、未试听任何音频**；功能、引擎数、语种数、平台矩阵均来自 README 与仓库文件自述；**8 个 TTS / STT 模型自身的许可未逐份核对**；GitHub API 匿名配额用尽，未取到 `size` 与贡献者统计。**声音克隆涉及人格权 / 声音权与防诈骗风险，请自行确认授权与当地法律。**
