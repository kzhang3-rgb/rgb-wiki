# VoiceStudio：全本地运行的 AI 语音工作室（AGPL 的 ElevenLabs 替代）

> 一句话值得记住的取舍：它把**语音克隆、音色设计、视频配音、听写、转写、有声书**六件事塞进一个桌面 App，**默认全在本机跑、不需要账号**（模型自己下载、可只用 CPU，慢一点但能用），并对外给一个**本地 API 与 MCP**让 Agent 也能调用；代价是它是 **AGPL-3.0 + 闭源付费版（Pro）+ 尚未上线的 Cloud** 的混合体——**代码按 AGPL 免费可用（含商业使用），模型权重与音色却另有许可**，README 自己把这条拆开写清楚了。想找一个"完全本地、覆盖多语种"的配音 / 克隆工具时值得看；想研究"**AGPL 开源核心 + 闭源 Pro**"怎么并存、以及**一个重仓 AI Agent 协作的开源项目**长什么样时也值得看。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（571 行）、`LICENSE`、仓库文件树与元数据；**未下载、未安装、未运行、未克隆任何声音、未接任何 MCP 客户端、未试听任何音频**；**未逐份核对 `docs/licensing/model-inventory.md` 里各模型的许可**）
- 适用范围：桌面（macOS / Windows / Linux）、Docker、**手机（Android APK + F-Droid 仓库 + Obtainium；iOS/iPadOS 17+ 需侧载 IPA，均为 Preview）**；GPU 后端覆盖 CUDA / ROCm（含 Windows ROCm 实验性）/ Metal / Vulkan（GGUF 引擎）/ 纯 CPU

## 核心结论

**一句话定位（README 自述）**："AI voice studio. On your device."——**本机上的语音工作室**，覆盖六大能力：

| 能力 | 说明（README 自述） |
| --- | --- |
| **Voice cloning** | 用一段干净参考录音克隆音色并合成 |
| **Voice design** | **用文字描述一个音色**并生成（无需参考音频） |
| **Video dubbing** | 翻译并配音视频，**语速与原片对齐** |
| **Dictate & transcribe** | 悬浮听写小组件 + 本地转写 |
| **Narrate & batch** | 故事 / 有声书 / 排队任务 |
| **Connect & automate** | **本地 API、MCP、可复用 recipe 与监视文件夹** |

**关于"本地"的取舍写得很明确**：README 原文——"Everything above is free, runs on your own hardware and needs no account."；"No GPU? The app is fully usable on the CPU, just slower."；**下载任何模型前 App 会先问**。

**它最值得记的三个点：**

1. **AGPL 开源核心 + 闭源 Pro + 未上线的 Cloud 三层并存**，且边界写得清楚：Pro 那一栏直接标注"**Closed-source build, account required**"（闭源构建、需要账号），并承诺"**The AGPL-3.0 app stays free, including commercial use under its terms**"（AGPL 那个 App 保持免费，含按其条款的商业使用）。Cloud 更直接写"**Not available yet**"（还没上线，只能预约 early access）。这种"把不开源的那部分明说"的做法比含糊其辞的"开源版 / 商业版"更可核对。

2. **它把许可拆成两层并在 App 里落地**：`LICENSE` 是 **AGPL-3.0**（34,523 字符），但 "License & responsible use" 段明说——**模型权重、音色与生成输出各有各的许可**，由 **Model Manager 逐个展示**（清单在 `docs/licensing/model-inventory.md`）；并写"**Clone a voice only with its owner's permission**"。README 末尾还有一段明确的 **Disclaimer**：禁止未授权克隆、冒充、欺诈等。**这比许多同类项目只字不提要可核对得多**（可与本库另一篇语音工作室笔记对照）。

3. **它是一份"重仓 AI Agent 协作"的开源样本**：仓库里有 `.github/AGENT_PR_GUIDE.md`（Agent 提 PR 指南）、`agent_identities.py` / `identity_check.py`（**校验提交身份**）、`scam_guard.py`、`cla_check.py` / `cla_backfill.py` / `cla_reminders.py`（**CLA 流程自动化**）、`ci_scope.py` / `cancel_stale_runs.py`（CI 控成本），`.claude/agents/owner-judge.md` 与 `.agents/skills/`（FastAPI、Vite 等技能包）；还对外提供 `npx skills add debpalash/VoiceStudio`（**voicestudio** 与 **voicestudio-maintainer** 两个 skill），以及一段"**让编码 Agent 照着装**"的提示词模板。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [debpalash/VoiceStudio](https://github.com/debpalash/VoiceStudio) |
| 描述 | VoiceStudio is the open-source, fully-local ElevenLabs alternative — voice cloning, voice design, video dubbing, dictation, transcription & audiobook creation in 646 languages. |
| 官网 / 付费入口 | <https://voicestudio.sh> ・ Pro：`voicestudio.sh/pro` ・ Cloud：`app.voicestudio.sh`（**尚未上线**） |
| 归属 | 账号 `debpalash`（个人，页面 `isOrg: false`、`isFork: false`、`isArchived: false`；README 署名 Creator 为 **Palash Debnath**） |
| 许可证 | **AGPL-3.0**（`LICENSE` 34,523 字符）；**另有 `LICENSE-NOTICE.md` 与 `docs/licensing/model-inventory.md` 处理模型 / 音色 / 输出** |
| 主语言 | Python 54.8%（后端）；前端为 TypeScript / Electron |
| 关注度 | **109,328 Star / 12,113 Fork / 364 关注**（页面值，2026-11-13；ungh.cc 镜像为 109,145 / 12,099 / 360，**镜像偏低、不一致**） |
| 开放 Issue | 19（shields.io，2026-11-13） |
| 贡献者 | 153（shields.io，2026-11-13） |
| 维护状态 | **2026-04-09 创建**（约 7 个月做到 10 万+ Star）；ungh 报 pushed **2026-11-12**；页面 `updatedAt` 同日 |
| 默认分支 | `main` |
| 版本 | **53 Release / 54 tags**，发布很密 |
| 话题标签 | 18 个，含 `elevenlabs-alternative`、`local-first`、`voice-cloning`、`text-to-speech`、`speech-to-text`、`mlx`、`cuda`、**`omnivoice-studio`** 等 |
| 仓库规模 | 文件树约 **4,975 个文件**（**体量很大**，含多语言 README、docs、Docker/Dagger 与 CI 脚本） |

## 命名沿革：它原先叫 OmniVoice Studio

多处残留指向旧名：topic 里的 `omnivoice-studio`、`.claude/skills/omnivoice/LEGACY.md`、工作流 `build-omnivoice-tts.yml`、README 里 **Docker 的环境变量仍是 `OMNIVOICE_API_KEY` / `OMNIVOICE_DATA_DIR`**，容器名 `omnivoice`，Docker Hub 镜像仍是 `palashdeb/omnivoice-studio`。**默认引擎是 `k2-fsa/OmniVoice`**（README："Start with VoiceStudio (default, powered by k2-fsa/OmniVoice)"）。也就是说 **Agent 侧与旧脚本里的标识符还是 `omnivoice`**，写自动化时别按新名字硬猜。

另外 README 提到"**Upgrading from 0.5.3 or older (the Tauri app)?**"——**0.5.3 之前是 Tauri 桌面壳，之后换成 Electron**（需单独重装新 App，见 `docs/electron-migration.md`）。

## 引擎、平台与安装

- **引擎**：默认 **OmniVoice**，模型管理器里另有"**dozens of other engines**"（README 点名 CosyVoice 等；引擎目录见 `docs/engines/`），支持 **GGUF 引擎 + Vulkan 加速**（AMD / Intel / NVIDIA），并称 OmniVoice GGUF 在 Windows / Linux 上**自动选择 GPU 与模型大小**。
- **平台与包**：macOS（Homebrew tap `debpalash/tap`、DMG、一键脚本；Apple Silicon 用 Metal/MPS；Intel Mac 只能当 UI 连远程后端）· Windows（`install.ps1`、EXE；x64 支持 CUDA / ROCm(opt-in) / CPU；**Windows on ARM 为实验性**）· Linux（AUR `voicestudio-bin` / `voicestudio-git`、Snap、DEB、AppImage、Nix）· **Docker（GHCR `ghcr.io/debpalash/voicestudio:stable`，端口 `127.0.0.1:3900`）** · **Android 10+（APK / F-Droid 自建仓库 / Obtainium，Preview）** · **iOS / iPadOS 17+（IPA 侧载，Preview）**。
- **一键安装是"远端脚本管道进 shell"**：macOS / Linux `curl -fsSL https://voicestudio.sh/install | sh`，Windows `irm https://voicestudio.sh/install.ps1 | iex`；脚本不可达时可改为直接从仓库拉 `scripts/install.sh`。**支持 `--version` / `--main` / `--uninstall` 参数。**
- **从源码跑**：`git clone` 后 `bun run dev`（Bun 装 JS 依赖，在支持的本地后端下还会装 Python 依赖，再起 Electron）；只重建 Python 环境用 `bun run setup:api`。
- **给 Agent 的安装方式**：README 直接给了一段可粘贴的提示词，让编码 Agent 照 `docs/install/agent.md` 装着并验证；另有 `npx skills add debpalash/VoiceStudio` 装两个 skill。

## 使用前需要注意

- **⚠️ AGPL-3.0 的传染性（最重要）**：`LICENSE` 是 **AGPL-3.0**，不是 MIT / Apache。**若你修改后通过网络对外提供服务，AGPL 的源码提供义务会被触发**；README 说"含商业使用"指的是**按其条款**的商业使用，不等于可闭源商用。**要闭源商用应看 Pro 的商业许可，或直接联系作者。**
- **⚠️ 开源许可只管代码，模型与音色另算**：README 明说模型权重、音色与生成输出**各有各的许可**，由 Model Manager 逐个展示（清单在 `docs/licensing/model-inventory.md`）。**想商用要逐个模型核对**（我未逐份核验该清单）。
- **⚠️ 声音克隆的伦理与法律风险**：README 有 Disclaimer 明确禁止未授权克隆与冒充，并写"仅在与所有者许可下克隆音色"；但技术门槛依然很低。**克隆他人声音在多地涉及人格权 / 声音权与诈骗风险，使用前请自行确认授权与当地法律。**
- **⚠️ 一键安装会执行远端脚本**：`curl ... | sh` 与 `irm ... | iex` 属于把远端内容直接交给 shell；企业环境通常需要先审批或改为先下载审阅。Docker 方式也要注意镜像来源与 `OMNIVOICE_API_KEY` 的保存方式（README 用 `openssl rand -hex 32` 生成并要求妥善保存）。
- **⚠️ 体积与文件数都很大**：`main` 分支约 **4,975 个文件**（含多语言 README、`docs/`、Dagger、CI 脚本与 Android / iOS 打包资产）。**clone、抓取与镜像时留意体积。**
- **⚠️ 由作者自己的项目赞助，需分清**：README 的"Strategic sponsors"是 **OpenGTM** 与 **Opal**——**都在作者自己的 GitHub 账号 `debpalash` 下**；特别赞助方 **Supermetry** 在 `velixio` 账号下（Core Team 的 Shimul Debnath）。也就是说**赞助位与"同一批人做的其他项目"高度重叠**，读作"生态自举"比读作"第三方背书"更准确。
- **README 上有跨站追踪像素**：页顶 `app.supermetry.com/px/....svg` 是一个访问计数像素（作者自家做的无 cookie 分析产品）。**这在 README 上很常见，但值得知道它会记录访问。**
- **手机端只是 Preview**：Android 与 iOS 都仍标注 Preview；iOS 需自行侧载 IPA（涉及签名与信任）。
- **活跃度按 `pushed_at` 看**：ungh 报 pushed **2026-11-12**（当天有推送），页面 `updatedAt` 同日，**这次两者一致**。
- **我未做任何验证**：**未下载、未安装、未运行、未克隆声音、未接 MCP、未试听音频**；引擎数量、"646 语种"、平台矩阵、性能表述与许可分层**均来自 README 与仓库文件自述**；**`docs/licensing/model-inventory.md` 里各模型的许可未逐份核对**。

## 什么时候会再看

- 需要一套**完全本地、覆盖多语种**的语音工具（克隆 / 设计 / 配音 / 听写 / 有声书）时。
- 想参考"**AGPL-3.0 开源核心 + 闭源 Pro + 未上线 Cloud**"如何在一个仓库里并存，以及**怎么把"不开源的那部分"在 README 里说清楚**时。
- 想借鉴"**把模型权重 / 音色 / 输出与代码许可分层，并在 App 内逐个展示**"的做法（`docs/licensing/model-inventory.md`）时。
- 想研究"**重仓使用 AI Agent 协作的开源项目**"的治理装置：Agent PR 指南、身份校验、CLA 自动化、CI 范围裁剪、`owner-judge` 角色时。
- 需要**手机端也能离线跑语音模型**（Android / iOS Preview）或想用 Docker 起一个本地语音服务（`127.0.0.1:3900`）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属本地语音方向，一个是软件、一个是候选清单**：[free-voice-clone：本地免费语音克隆与音乐生成模型清单](free-voice-clone-本地免费语音克隆与音乐生成模型清单.md)——那篇**汇总本地可跑的语音 / 音乐模型**（含各模型许可）；本篇是**全本地语音工作室**。可对照"**引擎清单**"与"**成品工作台**"。
- **最直接的可比对象（同为"本地优先的开源 AI 语音工作室 / ElevenLabs 替代"）**：[voicebox：本地优先的开源 AI 语音工作室](voicebox-本地优先的开源AI语音工作室.md)——两篇几乎同题，差别正好互补：**许可**（voicebox 是 **MIT**，本篇是 **AGPL-3.0**）、**桌面壳**（voicebox 特意强调"**不是 Electron**"用 Tauri，本篇 **0.5.3 后改为 Electron**）、**规模**（voicebox 8 个 TTS 引擎 / 23 语种，本篇自称 **646 语种**）、**给 Agent 发声的契约**（voicebox 强调"**绝不静默**"与按客户端绑定音色，本篇走"本地 API + MCP"）、**维护方式**（voicebox 由外部 Agent 团队 Capy 推进，本篇有 Agent PR 指南 + 身份校验 + CLA 自动化）。**要选型时建议两篇对着读。**
- **同属"在本机跑大模型、并对外暴露本地接口"**：[colibri：在普通硬件上跑分层 MoE 推理的 C 引擎](colibri-在普通硬件上跑分层MoE推理的C引擎.md)——那篇是**文本大模型**的本地推理引擎（本地 OpenAI / Anthropic 兼容网关 + MCP，硬门槛是磁盘）；本篇是**语音模型**的本地工作室（本地 API + MCP，硬门槛是显存 / 时间）。可对照"把模型跑在本机再开一个本地接口"的两种落地。
- **同属"桌面音频应用"**：[honeydew：用 Rust 复刻 FL Studio Mobile 的音乐工作室](honeydew-用Rust复刻FL-Studio-Mobile的音乐工作室.md)——那篇是**音乐制作**（插件宿主 VST3 / CLAP、纯 Rust、GPL-3.0）；本篇是**语音生成与处理**（多引擎可插拔、Electron + Python、AGPL-3.0）。两者在"多引擎 / 插件化管理"与许可选择上可对照。
- **MCP 作为"应用把自己开放给 Agent"的入口**：[servers：MCP 官方参考服务器合集](servers-MCP官方参考服务器合集.md)——本篇内置 MCP 与本地 API，属于"业务型 MCP 服务器"；可对照官方参考实现看这类"自带 MCP 的应用"长什么样。

## 参考来源

- 项目仓库与 README（571 行，核验日期 2026-11-13）：https://github.com/debpalash/VoiceStudio
- 许可：`LICENSE`（AGPL-3.0，34,523 字符）、`LICENSE-NOTICE.md`、`docs/licensing/model-inventory.md`（据 README 指引，**未逐份核对**）
- 治理与贡献：`.github/CONTRIBUTING.md`（含 CLA 签署要求）、`.github/CLA-1.0.md`、`.github/CCLA-1.0.md`、`.github/AGENT_PR_GUIDE.md`、`.github/SECURITY.md`、`.github/CODE_OF_CONDUCT.md`
- 文档索引：`docs/README.md`、`docs/engines/README.md`、`docs/mcp.md`、`docs/speech-platform.md`、`docs/mobile.md`、`docs/code-signing-policy.md`
- 安装脚本：`scripts/install.sh`、`docs/install/*`（macOS / Windows / Linux / Docker / Nix / agent）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 与 Tag 数 / 许可证字段 / 是否 fork）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日）；Issue 数与贡献者数取自 shields.io（2026-11-13）

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未下载、未安装、未运行、未克隆声音、未接任何 MCP 客户端、未试听任何音频**；功能、引擎数、"646 语种"、平台矩阵与许可分层均来自 README 与仓库文件自述；**模型 / 音色 / 输出各自的许可未逐份核对**。**声音克隆涉及人格权 / 声音权与防诈骗风险；AGPL-3.0 有网络服务场景的源码提供义务，商用前请自行确认条款与授权。**
