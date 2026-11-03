# social-auto-upload：多平台视频自动发布 CLI 与 Agent 技能

> 记录这个项目到底是"发布工具"还是"账号自动化"，以及它把登录态交给工具这件事。它用统一 CLI（`sau`）和 18 个 Agent Skill，把视频、图文、文章发布到 17 个国内外平台。第一句先记清三件事：**仓库主体 MIT（18 个 Skill 为 MIT-0）**；**保持活跃（最新提交 2026-10-22）**；**核心手段是浏览器自动化 + 本地账号会话，不是官方发布 API**。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README（raw 与 jsDelivr 两通道对比）、平台能力表、CLI 示例、Release 列表与 GitHub API 元数据，并用默认分支 `main` 的 commits 接口复核活跃度；未安装、未登录任何平台、未发布任何内容、未审计源码）
- 适用范围：Python 3.10–3.12；Windows / macOS / Linux 桌面环境（浏览器自动化需要本机可运行 Chromium）

## 核心结论

这是一个"把你的视频/图文/文章自动发到多个平台"的工具。它的价值点和风险点其实是同一件事：**它替你操作各个平台的网页**。

分三层看：

**① 它是什么。** 仓库提供统一命令行 `sau`，每个平台一条子命令（`sau douyin upload-video ...`）；同时把每个平台封装成一个 **Agent Skill**（共 18 个：1 个跨平台总入口 + 17 个平台），供 Codex、OpenClaw、Claude Code 这类 Agent 调用。README 明确写"面向内容创作者和运营者"，平台能力表覆盖 17 个平台，其中 **12 个支持视频、3 个支持文章、6 个支持图文**。

**② 它怎么做。** 关键在这句：**YouTube 那一段解释了整个项目的技术路线**——"这里走浏览器自动化而不是官方 API，是因为未通过 Google 合规审核的 API 项目上传的视频会被强制锁为私享、无法改公开"。也就是说，**为了拿到和手工操作等价的效果，项目整体选择用浏览器模拟人的登录与发布**（`--account creator` 是你给本地会话取的名字，可准备多个账号并发）。这也解释了为什么它支持那么多平台：绕开各家 API 的申请与审核门槛。

**③ 代价。** 代价是**平台登录态必须交给工具**（会话/cookie 落本地），且**自动化发布本身可能被平台判定为异常行为**（同一主题的 Easel 就在 README 里自己提示"可能被检测自动化操作、存在风控风险"，建议人工确认后再发）。项目 README 没有讨论风控后果，反而把"支持 17 个平台 + 定时发布"当作卖点——**这是本项目最需要读者自己补上的那一层判断**。

另外两个先记下的事实：仓库首页与 README 都带**赞助商区块**，其中一家是"AI 大模型中转服务商"（自称价格低至官方 30%），属于商业推广位；README 的 jsDelivr 副本与 raw 版本**内容不一致**（详见"使用前需要注意"）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [dreammis/social-auto-upload](https://github.com/dreammis/social-auto-upload) |
| 描述 | "自动化上传视频到社交媒体：抖音、小红书、视频号、tiktok、youtube、bilibili" |
| 许可证 | **MIT**（仓库主体）；**纳入版本控制的 18 个 Agent Skill 为 MIT-0**（README「许可证」一节明确区分，理由是便于在要求该许可证的平台分发） |
| 主语言 | Python（3.10–3.12） |
| 关注度 | **16,066 Star、2,717 Fork、95 关注、82 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2023-12-04 创建；`pushed_at` **2026-10-22**；默认分支 `main` 最新提交 `3847fff`「docs: clarify skill marketplace publishing status」为 **2026-10-22**（未签名）→ **活跃**，但近几次提交集中在文档与 Skill 分发 |
| 体积 | 约 24 MB（API `size` 24,752 KB，含演示 GIF 与截图） |
| 主页 | `https://sap-doc.nasdaddy.com/`（文档站） |
| 话题标签 | `agent-skills`、`bilibili`、`douyin`、`tiktok`、`xiaohongshu`、`youtube` |
| 版本 | 最新 Release **v1.0.0-skills.2**（2026-10-22，作者 `dreammis`，无附件）；上一版 v1.0.0-skills.1（同日）；此前的 `baijiahao`（2025-04-12） |
| 目录结构 | `sau_commands/`（各平台 CLI 命令）、`uploader/`（发布流程）、`skills/`（18 个 Skill）、`examples/`（历史直连示例）、`docs/` |

> 补充：`watchers_count` 与 `stargazers_count` 恒等是 GitHub 的历史遗留，真正的关注者是 `subscribers_count`（此处 95）。注意到最近 4 次提交全部是文档类（`docs: ...`）与 Skill 分发状态更新，说明当前阶段重心在**把能力包装成 Agent Skill 并上架各技能市场**，而不是新增平台支持。

## 它能做什么

**平台能力表（README 原文，`✅` 支持、`-` 不适用）**

| 平台 | 视频 | 图文 | 文章 | 定时 | CLI | Skill |
| --- | --- | --- | --- | --- | --- | --- |
| 抖音 | ✅ | ✅ | - | ✅ | ✅ | ✅ |
| Bilibili | ✅ | - | - | ✅ | ✅ | ✅ |
| 小红书（浏览器版） | ✅ | ✅ | - | ✅ | ✅ | ✅ |
| 快手 | ✅ | ✅ | - | ✅ | ✅ | ✅ |
| 视频号 | ✅ | - | - | ✅ | ✅ | ✅ |
| 百家号 | ✅ | - | - | - | ✅ | ✅ |
| 大鱼号 | ✅ | - | ✅ | ✅ | ✅ | ✅ |
| 支付宝生活号 | ✅ | - | - | - | ✅ | ✅ |
| 微博 | ✅ | - | - | - | ✅ | ✅ |
| 虎扑 | ✅ | - | - | - | ✅ | ✅ |
| 雪球 | - | - | ✅ | - | ✅ | ✅ |
| 知乎 | ✅ | - | ✅ | 实验性 | ✅ | ✅ |
| YouTube | ✅ | - | - | 开发中 | ✅ | ✅ |
| TikTok | ✅ | - | - | ✅ | 无 CLI | ✅ |
| Facebook | ✅ | ✅ | - | - | ✅ | ✅ |
| Facebook Page | ✅ | ✅ | - | ✅ | ✅ | ✅ |
| Instagram | ✅ | ✅ | - | - | ✅ | ✅ |

几个附带约束（README 明写）：微博视频**标题最多 30 字**、虎扑**标题 4–40 字**；大鱼号文章支持 HTML / Markdown / TXT 并可保留近似排版，但"圆角和精确间距可能与本地预览不同"；Facebook / Instagram 默认直连，需要代理时用 `--proxy` 或 `SAU_FACEBOOK_PROXY` 等环境变量。

**用法（README 示例，节选）**

```bash
# 先登录一次平台，会话用你自取的名字标识
sau douyin login --account creator
sau douyin check --account creator

# 视频
sau douyin upload-video --account creator --file videos/demo.mp4 --title "示例标题" --desc "示例简介"
sau bilibili upload-video --account creator --file videos/demo.mp4 --title "示例标题" --desc "示例简介" --tid 249

# 图文（独立图片帖/轮播）
sau xiaohongshu upload-note --account creator --images videos/1.png videos/2.png --title "图文标题" --note "图文正文"

# 文章（大鱼号 / 雪球 / 知乎）
sau dayu upload-article --account creator --title "标题" --content-file examples/article.md --theme modern --source "无需标注"
```

**Agent 集成**

- 提供 `docs/agent-bootstrap.md`（Bootstrap 提示词）与 `skills/` 下 18 个 Skill；跨平台总入口在 `skills/social-auto-upload/SKILL.md`。
- 单个 Skill 可从 GitHub 装进兼容 Agent：`npx skills add dreammis/social-auto-upload --skill douyin-upload`。
- `skills.sh` 已收录抖音 Skill；各市场上架状态见 `docs/skill-distribution.md`。
- 明确提醒：`sau skill install` **不是**本项目提供的命令（README 特意澄清）。

**技术细节（README 记录）**

- Bilibili 上传**基于开源项目 `biliup` 封装**，首次运行会自动下载并跟上游 release 更新，用户不必手装。
- 抖音发布若触发短信二次验证，程序会优先读项目根目录的 `verify_code.txt`，交互式终端也可直接输入。
- YouTube 上传会**等进度到 100% 再点发布**（浏览器上传靠窗口开着传）；`--visibility` 可选 `public`/`unlisted`/`private`；被墙地区需在 `conf.py` 里显式设 `YT_PROXY`（chromium 不吃系统代理）。
- 发布平台用 cookie/会话方案，海外平台可选 `--proxy ""` 忽略环境变量强制直连。

## 使用前需要注意

**① 本质是把平台登录态交给本地工具。** 每个平台先 `login` 一次，之后所有发布都复用这份会话。**能同时管理多个账号正是因为它把多份登录态放在本地**。这些会话怎么存、是否加密、会不会被其他进程读走，README 未说明——**要不要用、用哪个账号，请自行权衡**。

**② 自动化发布本身可能触发平台风控。** 项目选择了浏览器自动化（原因见上文 YouTube 那段），但 README 没有讨论"被判定为自动化操作"的后果。**同类项目的自陈风险可作为参照**：Easel 明确提示自动发小红书可能被检测、建议人工确认后再发。用这个工具做批量分发时，账号风险由使用者承担。

**③ 首页与 README 都含赞助商区块。** 三家赞助商中包括**"AI 大模型中转服务商"**（自称 Claude / GPT / Gemini 60+ 模型接入、低至官方价格 30%，附优惠码）。这属于商业推广位，也意味着**项目方与灰色转售类服务有商业往来**；不影响代码本身，但记录在此供判断项目性质时参考。

**④ 许可证要分两段读。** 仓库主体 MIT；但**18 个 Agent Skill 采用 MIT-0**（比 MIT 更宽松，无需保留署名），目的是"便于在要求该许可证的平台分发"。也就是说，**你拿到的 Skill 文本在分发条件上与仓库代码不同**——要二次分发时按各自 `SKILL.md` frontmatter 声明的许可证走。

**⑤ 同一份 README 在两个通道内容不一致。** 核验时 **jsDelivr 版本（223 行）缺"贡献指南 / 主要贡献者 / 致谢 / 许可证 / Star History"等章节，而 `raw.githubusercontent.com` 版本有 372 行**。本笔记的许可证与致谢信息以 **raw 版本**为准。这条也与之前记录过的实例一致：**同一份 README 在不同通道可能给出不同结果，需要交叉核对**。

**⑥ 82 个开放 Issue、且近期提交偏文档。** 相对 16,066 Star，Issue 不算少；近几次提交集中在 Skill 分发与文档。用之前建议先翻 Issue，判断你关心的平台是否已有人报问题。

**⑦ 只作信息记录。** 我未安装、未登录、未发布任何内容，上文全部来自 README、Release 与 API 元数据的自述。**批量发布涉及的平台条款、内容版权与账号风险由使用者自行判断**，本笔记不构成使用建议。

## 什么时候会再看

- 需要把一个视频/图文批量分发到多个国内外平台，想先看有没有统一 CLI 方案时。
- 想参考"**一个 Python 项目如何同时提供 CLI 与一套 Agent Skill**"的组织方式（`sau_commands/` + `skills/` 分层的做法）时。
- 想复盘"为什么有的工具宁可放弃官方 API、改用浏览器自动化"（合规审核门槛 → 私享锁定，这个具体理由很有参考价值）时。
- 想复盘"jsDelivr 与 raw 两通道 README 不一致"这个核验实例时。

## 相关笔记

- **同为"多平台内容分发"，定位不同**：[AiToEarn：AI 内容变现与多平台发布](AiToEarn-AI内容变现与多平台发布.md)——那边是"内容变现 + 平台分发"的一体化产品（12 个平台、带 Relay 授权中转），本篇是不带变现的发布工具、且用本地会话而非第三方中转；可对照"授权走官方服务器"与"登录态留本地"两条路线。
- **同为"内容生产 → 多平台发布"，取向不同**：[Easel：开源 AI 社媒内容工作台](Easel-开源AI社媒内容工作台.md)——那是从选题到复盘的全流程工作台，本篇只解决"发布"这一段；两者都涉自动发布，**Easel 反而把风控风险写进了 README**，可对照两种风险披露态度。
- **Agent 宿主**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——本项目 README 把 OpenClaw 与 Codex、Claude Code 并列为 Skill 的首选运行环境；可对照"通用 Agent 宿主 + 领域 Skill"的分工方式。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库：https://github.com/dreammis/social-auto-upload
- README（raw，核验以此版本为准）：https://raw.githubusercontent.com/dreammis/social-auto-upload/main/README.md
- 文档站：https://sap-doc.nasdaddy.com/
- Bilibili 能力依赖的开源项目：https://github.com/biliup/biliup

以上信息核验于 2026-11-03：Star / Fork / Issue 数、平台支持矩阵与 Release 都会变化；**各平台的自动化能力尤其脆弱，平台改版后可能失效**。未安装、未运行、未登录任何平台、未发布任何内容。
