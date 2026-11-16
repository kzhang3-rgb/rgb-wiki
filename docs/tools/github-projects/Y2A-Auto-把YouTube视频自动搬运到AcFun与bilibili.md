# Y2A-Auto：把 YouTube 视频自动搬运到 AcFun 与 bilibili

> 一句话定位（README 自述）：**"将 YouTube 视频自动搬运到 AcFun / bilibili 的一体化工具"**——把**下载 → ASR → 字幕翻译 → 字幕质检 → AI 元信息 → 内容审核 → 上传**串成一条自动流水线，自带 **Web 管理后台**、**YouTube 频道 / 关键词监控**与**多平台通知**。最值得记的是三点：① 它是**"全流程自动化搬运"而不是单纯的下载器或上传器**（一个任务从源头 YouTube 一直走到两个国内平台的投稿）；② **它整体围绕"登录态"运转**——YouTube / AcFun / bilibili 三份 Cookie（或扫码 / CookieCloud 同步）缺一不可；③ **它的合规风险是结构性的**——搬运本身涉及源视频版权与两个平台的投稿规则，README 也只把"合法合规前提下用于学习与研究"写进末尾。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（624 行）、`LICENSE`、文件树与元数据；**未 clone、未部署、未跑 Docker、未填任何 Cookie、未连接任何平台账号、未执行任何一次搬运、未审计 `modules/` 源码**）
- 适用范围：**Docker（推荐）** / 本地 Python 运行（Python 3.11+、FFmpeg、yt-dlp）/ Windows 便携包（官方 Release 通常内置 FFmpeg·FFprobe）；可选独立部署的 Telegram 机器人 `Y2A-Auto-tgbot`；**FFmpeg 需 ≥ 5.1**（字幕烧录用 `-fps_mode cfr`，5.0 及更早会直接报错）

## 核心结论

**① 它是什么。** 一个自托管的 **YouTube → AcFun / bilibili 搬运流水线**。README 的功能清单可以归成四块：

- **自动化流水线**：`yt-dlp` 下载视频与封面 → ASR 生成字幕（Whisper / Voxtral）或按需识别 → 字幕翻译 / 后处理 / 质检（QC）/ 烧录 → AI 生成标题·简介·标签·分区推荐 → 内容安全审核（阿里云 Green）→ 自动上传到 AcFun / bilibili / 双平台。
- **Web 管理后台**：任务列表、人工审核、强制上传；设置中心按"运行概览 / 账号与网络 / 内容审核 / AI 模型 / 字幕处理 / 语音识别 / 视频转码 / 监控与维护 / 安全"分组；带登录保护、错误次数锁定与密码管理。
- **YouTube 监控**：频道监控与关键词搜索监控，支持 `latest` / `historical` 模式、视频类型筛选（video / short / live）、标题关键词过滤（任一 / 全部匹配），可自动加入任务队列。
- **周边设施**：通知推送（企业微信 / Server酱 / message-pusher，异步重试队列）、**CookieCloud 集成**（自动同步 YouTube Cookies）、日志与下载清理、并发控制、Windows 下 FFmpeg 自动补齐。

**② 它怎么做。** 关键前提是**三份 Cookie**：`cookies/yt_cookies.txt`（YouTube）、`cookies/ac_cookies.json`（AcFun）、`cookies/bili_cookies.json`（bilibili）。README 建议用浏览器扩展导出 `cookies.txt` 并**明确提示不要提交到仓库**。AcFun / bilibili 额外支持 **QR 码扫码登录**；bilibili 还支持 Netscape / JSON 两种 Cookie 导入格式。如果部署了 CookieCloud，YouTube 那份可自动拉取，免手动导出。

**③ 代价与边界。** README 末尾只有一句合规提示——"请遵守相关平台条款，仅在合法合规前提下用于学习与研究"。但项目本身做的是**把别人的视频搬到另外两个平台**，这涉及两层需要自行判断的事：**源内容的版权**与**目标平台的搬运/转载规则**；配置里那个默认开启的 `UPLOAD_APPEND_REPOST_NOTICE`（自动追加转载声明）正说明作者清楚这层关系。此外，**投稿本身是账号级风控行为**——README 专门写了 bilibili 报 **603「系统检测到异常行为」**时的处置建议（保持出口 IP 稳定、降低投稿频率、必要时先手动投稿一次）。

**个人判断（未验证）**：这个仓库的价值主要在两块——**"一条搬运流水线该怎么拆"的工程结构**，以及**字幕质检（QC）这一节写得异常细**（规则层硬拦截 + 边界样本才调 AI 复核，还给了时间轴维度的三条边界语义，见下文）。要真正用起来，必须先解决**合规**与**账号风控**这两道前置问题。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [fqscfqj/Y2A-Auto](https://github.com/fqscfqj/Y2A-Auto) |
| 描述 | "将 YouTube 视频自动搬运到 AcFun / bilibili 的一体化工具"（topics：`acfun`、`bilibili`） |
| 许可证 | **GPL-3.0**（`LICENSE` 35,149 字符） |
| 主语言 | **Python 82.8%**（shields.io，2026-11-13） |
| 关注度 | **4,766 Star / 803 Fork / 15 Watcher**（**页面值**，2026-11-13）；开放 Issue **13**、贡献者 **6**（shields.io，同日）。**ungh 镜像 4,727 / 796 / 15** |
| 维护状态 | **2025-05-10 创建**；ungh `pushedAt` **2026-10-28**；默认分支 **`main`**；**30 条 Release**（最新 **4.11.1**） |
| 体积 | `main` 分支 **203 个条目**：`tests` 77、`modules` 70，其余为 `static` / `templates` / `config` / `db` / `downloads` / `fonts` / `logs` / `temp` / `cookies` / `acfunid` / `build-tools`；根目录有 `app.py`、`requirements.txt`、`Dockerfile`、`docker-compose.yml`、`docker-compose-build.yml` |
| 镜像 | Docker Hub `fqscfqj/y2a-auto:latest`（README 默认），可切换 `ghcr.io/fqscfqj/y2a-auto:latest` |
| 默认端口 | `5000`（本地 `python app.py` 后访问 `http://127.0.0.1:5000`） |

## 能力与结构

### 1. 流水线与配置（README）

配置集中在 `config/config.json`（首次运行自动生成），分组如下：

- **基础与安全**：`AUTO_MODE_ENABLED`（无人值守总开关，**默认 `false`**）、`password_protection_enabled`（**默认 `false`**）、`LOGIN_MAX_FAILED_ATTEMPTS`（5）、`LOGIN_LOCKOUT_MINUTES`（15）、`LOGIN_SESSION_TIMEOUT_MINUTES`（30）、`UPLOAD_TARGET_DEFAULT`（`acfun` / `bilibili` / `both`）、`UPLOAD_APPEND_REPOST_NOTICE`（**默认 `true`**）。
- **账号与网络**：三份 Cookie 路径；`BILIBILI_SUBMIT_API`（`auto` 默认，优先"必剪 PC 接口"、失败回退 Web）；YouTube 下载代理与**监控 API 独立代理**（不继承下载代理）；`YOUTUBE_DOWNLOAD_THREADS`、`YOUTUBE_THROTTLED_RATE`；`YOUTUBE_API_KEY`（监控功能需要）；`FFMPEG_LOCATION`、`FFMPEG_AUTO_DOWNLOAD`。
- **AI 与投稿**：`OPENAI_API_KEY` / `OPENAI_BASE_URL` / `OPENAI_MODEL_NAME` 全局配置，另有 `SUBTITLE_OPENAI_*`、`SUBTITLE_QC_*` 两级覆盖；`TRANSLATE_TITLE` / `TRANSLATE_DESCRIPTION` / `GENERATE_TAGS` / `RECOMMEND_PARTITION` / `FIXED_PARTITION_ID*` / `PRESET_TAGS_*`。
- **字幕处理**：翻译开关、源/目标语言、烧录字体与外观（字号/边距倍率、字色、描边、阴影、粗体、半透明底板）、`SUBTITLE_EMBED_IN_VIDEO`、并发与重试。
- **语音识别（ASR）**：`SPEECH_RECOGNITION_PROVIDER` 支持 `whisper`、`voxtral`；`VAD_ENABLED`（默认 `true`）；Whisper 走 `segment` 级时间戳并自动兼容不支持 `timestamp_granularities` 的接口；Voxtral 走 Mistral `/v1/audio/transcriptions`（默认模型 `voxtral-mini-latest`）。
- **视频转码**：`VIDEO_ENCODER`（`auto` / `cpu` / `nvidia` / `intel` / `amd`），默认优先 HEVC/H.265 硬件编码（`hevc_nvenc` / `hevc_qsv` / `hevc_amf` / `hevc_vaapi`），失败回退 CPU；`VIDEO_CPU_CODEC`（`x264` 默认 / `x265`）、`VIDEO_CPU_PRESET`、`VIDEO_QUALITY_MODE` / `VIDEO_QUALITY_VALUE`、`VIDEO_HW_QUALITY_BOOST`、`VIDEO_COLOR_METADATA_MODE`、`VIDEO_X264_TUNE`、`VIDEO_CUSTOM_PARAMS*`。
- **维护**：`MAX_CONCURRENT_TASKS`（2）、`MAX_CONCURRENT_UPLOADS`（1）、日志/下载清理、`DELETE_DOWNLOAD_FILES_AFTER_UPLOAD`。
- **通知**：`NOTIFY_CHANNELS`（`wecom` / `serverchan` / `message_pusher`）+ `NOTIFY_EVENTS`（任务添加·完成·失败、登录成功·锁定、QR 登录成功·失败）。
- **CookieCloud**：`COOKIECLOUD_SERVER_URL` / `UUID` / `PASSWORD` / `ENCRYPT_MODE`（`auto` / `legacy` / `aes-128-cbc-fixed`）。
- **内容审核**：`CONTENT_MODERATION_ENABLED` + 阿里云 AK/SK，默认文本审核服务 `comment_detection_pro`。

### 2. 字幕质检（QC）——README 里写得最细的一节

`SUBTITLE_QC_ENABLED` 默认 `true`：**先用规则做硬拦截，只有边界样本才调用 AI 严格复核**。命中署名行、噪声提示、界面操作词、模板化重复句等明显低质量字幕时，规则层直接失败，**不再进入宽松放行**。

时间轴维度（覆盖率 / 间隙 / 语速）在 ASR 来源退化（`subtitle_quality_state == degraded`）时随严格模式一并参与判定，三条边界语义：

- 片尾留白容忍带取「30 秒」与「片长 25%」的**较小值**；
- 零时长 cue 达到「≥3 条且 ≥20%」即视为实质缺陷，**AI 不可用时不予放行**（≥25% 无条件硬失败）；
- 畸形时间戳的 cue 会被跳过并记 warning，**不再静默退化为 `0.0`**（退化会虚高覆盖率、反过来帮质检通过）。

可疑样本在 AI 不可用 / 返回异常 / 输出不合规时**默认按失败处理**；QC 失败时**跳过烧录但仍保留字幕文件并继续上传原视频**，任务标记为完成并显示字幕异常标记。被判不合格的 ASR 字幕会改名为 `*.rejected.txt` 移出复用范围。另有 **烧录门控开关 `ASR_FAILURE_BLOCKS_EMBED`（默认 `true`）**，并区分"质检不可用"（按拒绝处理）与"用户主动关闭质检"（放行）两种结局。

### 3. 周边：Telegram 机器人与 Windows 便携包

- **[Y2A-Auto-tgbot](https://github.com/fqscfqj/Y2A-Auto-tgbot)** 是**独立部署**的 Telegram 机器人：在 Telegram 里发 YouTube 链接，机器人调本服务的 `/tasks/add_via_extension` 接口提交任务。它用**最小权限 API Token**（`y2a_tgbot_v1_...`，明文只显示一次，只能调这一个接口，可随时重置/撤销）。
- **公共实例 `@Y2AAuto_bot` 不保证可用**：README 与仓库页都标注它"运行在个人 VPS 上，该服务器已被墙"，需要稳定使用请自部署。
- **Windows 便携包**：`build-tools/` 提供构建工具；官方 Release 包通常内置 FFmpeg / FFprobe，手工打包时保持 `ffmpeg/` 目录完整即可。
- **内置字体**：默认 `SourceHanSansHWSC-VF.otf`（字幕烧录用），许可证见 `fonts/LICENSE.txt`。

## 使用前需要注意

- **合规风险是结构性的**：它的功能就是"把 YouTube 视频搬到 AcFun / bilibili"。**源视频版权**与**目标平台的搬运/转载规则**需要使用者自行确认；README 只有一句"请遵守相关平台条款，仅在合法合规前提下用于学习与研究"。
- **Web 界面默认不安全**：`password_protection_enabled` **默认关闭**，且是**明文 HTTP**。README 明确劝告**不要直接把 5000 端口向公网开放**，推荐用 `ssh -N -L 5000:127.0.0.1:5000` 做本地端口转发；确需公网访问要先开密码保护、走反向代理配 HTTPS、并把入站来源限制到可信 IP。
- **必须自备三份登录态**：YouTube / AcFun / bilibili 的 Cookie（或扫码 / CookieCloud）。README 提醒**不要把 Cookie 提交到仓库**；这类文件等同于账号凭证，泄露即账号风险。
- **投稿会遇到平台风控**：README 单独记录了 bilibili 的 **603「系统检测到异常行为」**——属账号级风控，建议保持出口 IP 稳定、降低投稿频率，两条投稿通道都被拦时先手动投稿一次或按提示完成实名/申诉。
- **公共 Telegram 实例不可依赖**：`@Y2AAuto_bot` 部署在个人且已被墙的 VPS 上，"不保证一直可用，也可能随时停止服务"。
- **FFmpeg 版本是硬门槛（≥ 5.1）**：字幕烧录用 `-fps_mode cfr`，若通过 `FFMPEG_LOCATION` 指向 5.0 或更早，软件编码路径会因 `Unrecognized option 'fps_mode'` 直接失败；`VIDEO_CPU_CODEC=x265` 还要求构建带 `libx265`（缺失时自动降级 `libx264` 并继续烧录）。
- **`x265` 的收益作者自己说不准**：README 实测（1080p30 带字幕烧录）耗时约为 `libx264` 的 5 倍，且**体积收益不保证**（合成图文素材上 `veryfast` 反而大 1.6~1.8 倍），建议自行对比。
- **贡献者仅 6 人、开放 Issue 13 条**（shields.io）：属**小团队 / 个人维护**的项目；页面 Star 与 ungh 镜像略有差异，**引用数字时注明来源与日期**。
- **我只读了 README 与元数据**：**未 clone、未部署、未跑任何一次搬运、未填 Cookie、未接平台账号、未审计 `modules/` 源码**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想搭一条**"源站 → 目标平台"的自动投稿流水线**（下载 / ASR / 字幕 / 审核 / 上传一站到底）时，当作架构参考。
- 想抄**字幕质检（QC）**的做法——规则层硬拦截 + 边界样本才调 AI、时间轴维度三条边界语义、"质检不可用"与"主动关闭"分开处理时。
- 想参考**"多级配置覆盖"**的写法（全局 AI → 字幕专用 → 质检专用，留空则逐级回退）时。
- 想对照"**把任务交给别的客户端提交**"的最小权限 Token 做法（只开一个接口、明文只显示一次、可随时撤销）时。
- 想对比其它"视频搬运 / 下载 / 发布"路线的工具时：[videdown](videdown-开源视频下载工具.md)（给 yt-dlp 套 UI 的桌面下载器）· [social-auto-upload](social-auto-upload-多平台视频自动发布CLI与Agent技能.md)（面向 17 个平台的发布 CLI + Agent 技能）· [AiToEarn](AiToEarn-AI内容变现与多平台发布.md)（内容变现为主、发布为辅的商业服务）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都建立在 yt-dlp + FFmpeg 之上，目标不同**：[videdown：开源视频下载工具](videdown-开源视频下载工具.md)——那篇是**给人用的桌面下载器**（把 yt-dlp 包进 GUI），本篇是**无人值守的搬运流水线**（下载只是第一环，后面还有字幕、审核与投稿）。可对照"**工具外壳**"与"**端到端流水线**"。
- **都做"多平台发布"，路线相反**：[social-auto-upload：多平台视频自动发布 CLI 与 Agent 技能](social-auto-upload-多平台视频自动发布CLI与Agent技能.md)——那篇用**浏览器自动化 + 本地会话来绕开各家 API 的审核门槛**，覆盖 17 个平台；本篇用**平台 Cookie / 扫码登录 + 自建 Web 后台**，只做 YouTube → AcFun/bilibili 这一条链。可对照"**通用发布层**"与"**单条搬运链**"。
- **都是"发布 + 变现"，一个是开源工具一个是商业服务**：[AiToEarn：AI 内容变现与多平台发布](AiToEarn-AI内容变现与多平台发布.md)——那篇的价值主张在**内容交易市场与自动互动**，发布只是其中一环；本篇只做搬运，不含变现。可对照"**以钱为中心**"与"**以流程为中心**"。

## 参考来源

- 仓库主页与 README（624 行；核验日期 2026-11-13）：https://github.com/fqscfqj/Y2A-Auto
- 许可证：`LICENSE`（GPL-3.0，35,149 字符）
- 配置文件与说明：`config/config.json` 字段清单（README"配置说明"节）；`fonts/LICENSE.txt`（内置字体许可）
- 关联仓库：[fqscfqj/Y2A-Auto-tgbot](https://github.com/fqscfqj/Y2A-Auto-tgbot)（独立部署的 Telegram 转发机器人）
- Release 列表（30 条，最新 4.11.1）：ungh.cc releases API（2026-11-13）
- 完整文件树（203 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**略有差异**）；Issue 数 / 贡献者数 / 主语言取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未部署、未跑 Docker、未填任何 Cookie、未连接任何平台账号、未执行任何一次搬运、未审计 `modules/` 源码**；能力与配置描述均来自 README，**未逐一验证**。**使用前请自行评估源视频版权与目标平台投稿规则。**
