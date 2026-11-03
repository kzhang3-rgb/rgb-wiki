# Easel：开源的 AI 社媒内容工作台

> 记录浙大 REAL Lab 的开源项目：用**一个 Agent 贯穿"发现热点 → 策划选题 → 创作素材 → 多平台发布 → 归因复盘"五层社媒工作流**，底座是 **OpenClaw**（v0.3.0 起可切换到 Codex / OpenCode / 通用 ACP）。第一句先记清三件事：**Apache-2.0 开源、Star 已过 4 千、但对国内平台的"自动发布"是它自己都在提醒有风控风险的环节**；**要用就得把各平台登录态交给它**；**浅克隆前先看体积**（约 326 MB，仓库里塞了大量演示图与视频）。

- 状态：草稿
- 最近核验：2026-11-03（阅读仓库 `README.md`、`README_EN.md`、Release v0.3.0 说明、项目主页、仓库目录树与 GitHub API 元数据及默认分支 `main` 的 commits 接口；未安装、未运行、未接入任何平台账号）
- 适用范围：Linux / macOS / Windows 10-11；Python 3.10+、Node.js（24.16+ / 26.1+ / 27+，25.x 与 26.0 不兼容）、FFmpeg、Playwright Chromium

## 核心结论

**它是一个"内容生产流水线"型的 Agent 应用，而不是单个创作工具。**与常见的"文案生成器"不同，Easel 把发现、策划、创作、发布、归因五层串成一条链，并给每个账号维护独立的**画像**（定位、风格、受众、平台、偏好与记忆），让后续输出逐步贴合该账号。

三点先看清：

**① 底座是 OpenClaw，且从 v0.3.0 起变成"可切换底座"。** README 自称 "OpenClaw-powered"；v0.3.0 的 Release 说明写明 Web 设置里新增统一的"执行引擎"入口，可在 **OpenClaw / Codex / OpenCode / 通用 ACP / 仅聊天模式**之间切换，且执行引擎与模型供应商分开配置。也就是说它站在 Agent 宿主之上做垂直场景，而不是自己造一套 Agent 运行时。

**② 它能把内容真的发出去——这正是风险所在。** 支持小红书、抖音、快手、知乎、B 站、微信视频号、微信公众号、推特 X、掘金、今日头条共 **10 个平台的登录与发布**（README 口径）。README 自己给出提示："**谨慎自动发布到小红书：小红书平台可能检测自动化操作，存在验证、限流或账号风控风险**"，建议走"预览 + 发布前检查 + 人工确认后手动发布"。项目里另有 `docs/manual-publishing.md`（手动发布说明）。

**③ 多处口径不一致，别把单一处数字当准。** 技能数：README badge 写 113、项目主页写 113、而 v0.3.0 Release 正文写"内置技能仍为 114 个"；平台数：README 列 10 个，项目主页写"7 个已接入发布平台"；关注度：项目主页写 3.9k Star / 600+ Fork，API 实测已是 4,349 Star / 711 Fork。**页面数据明显滞后于 API**，本笔记统一以 API 值为准。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [ZJU-REAL/Easel](https://github.com/ZJU-REAL/Easel) |
| 描述 | "An open-source AI agent for social media — discover trends, create content, publish everywhere, and learn what works across Xiaohongshu, Douyin, Zhihu, Bilibili, and more." |
| 归属 | 浙江大学 REAL Lab / OpenDCAI Lab（README 顶部同时出现浙江大学与北京大学标识） |
| 许可证 | **Apache-2.0**（API 与 README badge 一致，仓库 `LICENSE` 为 11,357 字节的标准 Apache 2.0 全文） |
| 主语言 | Python 3.10+（另有 React 构建的 Web 工作台） |
| 关注度 | **4,349 Star、711 Fork、20 关注、25 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2026-08-28 创建；`pushed_at` **2026-11-02**；默认分支 `main` 最新提交 `5c12e42`「fix: avoid misleading hook warning for profile request cleanup」为 **2026-11-02**（提交者 Dingming Li，`lidingm@zju.edu.cn`），此前 10-12 发布 v0.3.0 → **真实活跃** |
| 最新版本 | **v0.3.0**（2026-10-12，Release 标题"多 Agent 底座 · Codex 授权开关 · 创作数据修复"；Release 无附件资产，以源码 tag 交付） |
| 体积 | 约 **326 MB**（API `size` 333,743 KB）——**明显偏大，主要是 `assets/readme/` 下的演示图与成片视频** |
| 项目主页 | https://zju-real.github.io/Easel/ （内嵌 http://zjureal.com/Easel/ 的展示页） |
| topics | `agent`、`agent-skill`、`content-automation`、`content-creation`、`content-generation`、`mcp`、`openclaw`、`social-media`、`social-media-management` |
| 其他 | 开启 Wiki 与 GitHub Pages；未开启 Discussions |

## 它能做什么

**五层工作流（README 口径）**

1. **发现**：聚合全网热榜、行业新闻、竞品动态与用户讨论，按账号画像筛选"真正能做"的机会。
2. **策划**：选题矩阵与评分、标题与 Hook、文章大纲、分镜脚本、内容日历、跨平台复用。
3. **创作**：文案、金句卡/知识卡、海报、信息图、Meme、AI 生图；TTS、多角色配音、声音克隆、AI 音乐；AI 视频、字幕与翻译、剪辑、切片、横竖版转换、视频转图文。
4. **发布**：平台适配（标题/正文/画幅/媒体要求）+ 发布前检查 + 发送到已登录账号。
5. **归因**：读取播放、互动、评论与内容表现，把有效结构与偏好沉淀回账号画像。

**工作台里的四个代表功能**：账号画像、热点雷达（支持接入自写的 Python 采集脚本 / HTTP 服务 / RSS-Atom）、内容日历、发布中心。

**安装与运行**

```bash
git clone https://github.com/ZJU-REAL/Easel.git
cd Easel
bash setup.sh          # Windows 用 .\setup.ps1（不需要 WSL）
```

- 安装器会检查 Python / `venv` / Node.js / Git / FFmpeg，并**检查或安装 OpenClaw、创建独立的 `easel` profile（不覆盖已有的 `~/.openclaw/`）**。
- FFmpeg 缺了只降级提示（音视频类技能不可用）；Chromium 下载失败同样只降级（浏览器登录/发布不可用）。
- **模型 API Key 在浏览器里配**：装完运行 `easel web`，首次打开停在「设置 → 模型配置」，填 Key 保存即可；也可直接编辑 `.env` 后跑 `easel doctor` 复检。
- 运行入口：`easel web`（推荐）、CLI；Windows 原生安装与 Web/CLI 入口已提供，但 README 说明**各技能与浏览器发布的 Windows 全链路兼容仍在完善中**。

**真实产物**：README 与项目主页展示了论文解读卡片（SpatialEvo、SpatialLadder、CULTURE-MT、KnowU-Bench 等）、小说/故事卡、生活与梗内容、成片视频（口播剧、AI 短剧、新闻等）。

## 使用前需要注意

**① 自动发布是有账号风险的环节。** README 点名小红书可能检测自动化操作，存在验证、限流或风控风险，建议人工确认后再发。也就是说"一键发全网"在实际使用中会被平台风控卡住，**不能假设十个平台都能稳定无人值守发布**。

**② 要用就得交出平台登录态。** 发布与"回收账号数据"的前提是扫码登录或配置 Cookie（v0.3.0 的更新大量篇幅在改小红书/知乎/微信视频号的二维码抓取、登录态判定与缓存失效）。这些凭据存在本地，但**"把多个社媒账号交给一个本地 Agent"本身就是一个需要自己权衡的信任决定**。

**③ 运行环境不轻，且 Node 版本口径偏"紧"。** Python 3.10+、Node.js（**24.16+、26.1+ 或 27+；25.x 与 26.0 明确不兼容**）、FFmpeg、Playwright Chromium 都要就位；缺 FFmpeg 或 Chromium 会静默降级成"部分技能不可用"，**但安装流程不报错**——这是典型需要留意的"静默降级"。

**④ 关于 v0.3.0 的「Codex 永久通过授权」开关。** 该开关默认关闭；开启后自动批准普通文件读取、搜索、Git 查看/拉取，以及项目工作区内的文件修改授权。Release 说明同时强调"不启用无限权限、不关闭 Codex 沙箱"，sudo、删除、系统修改等仍需确认——**设计上是克制的，但这是一个会持续生效的授权开关，开之前要清楚它的范围**。

**⑤ 演示数据有标注，值得记一笔。** 项目主页明确注明"复盘指标为合成演示数据，不代表真实发布效果或因果结论"，图文为真实作品、策划与发布环节为场景重构。**这类自陈式的数据边界说明在 AI 类项目里并不常见**，可以当作正面样本。

**⑥ 仓库体积与素材。** 约 326 MB，且 README 的图片与视频都在 `assets/readme/` 下。想快速看代码建议用浅克隆或只读文件树，别整仓拉。

**⑦ 未安装、未运行、未审计。** 以上全部来自 README、Release 说明、项目主页、目录树与 API 元数据；我没有安装、没有运行、没有接入任何平台账号，也没有审计其浏览器自动化与抓取代码。

## 什么时候会再看

- 想搭一个**从热点发现到多平台发布闭环的 AI 内容流水线**，尤其是中文平台（小红书 / 抖音 / 知乎 / B 站 / 公众号 / 视频号）时。
- 想参考"**垂直场景 Agent 应用站在通用 Agent 宿主（OpenClaw）之上**"的分层做法，以及 v0.3.0 那套"执行引擎与模型供应商解耦、可切换底座"的设置模型时。
- 想研究"**Agent 技能的工程化**"：README 的 113 项能力、`docs/skill-function-mapping.md`（能力地图）、`docs/SKILL-SPEC.md` 与 `skills/` 目录，是一份现成的"技能如何组织与校验"样本。
- 想复盘"**自动发布类工具的边界怎么写进文档**"时（小红书风控提示、手动发布说明、合成演示数据声明）。
- 想对照"**面向中文社媒的多平台发布**"的不同路线时（见下方 [AiToEarn](AiToEarn-AI内容变现与多平台发布.md)）。

## 相关笔记

- **同在"多平台发布"，切入不同**：[social-auto-upload：多平台视频自动发布 CLI 与 Agent 技能](social-auto-upload-多平台视频自动发布CLI与Agent技能.md)——那是一个只做"发布"环节的 CLI + Agent Skill 集合，本篇是从选题到复盘的全流程工作台；两者都涉及自动发布的风控问题，可对照各自在 README 里对风险的披露态度。
- 索引位置：[GitHub 项目收藏](README.md)。
- **宿主与被托管的关系**：[openclaw](openclaw-自托管个人AI助手.md)——Easel 自称 "OpenClaw-powered"，安装时会创建独立的 `easel` profile 且不覆盖已有的 `~/.openclaw/`；v0.3.0 起还可切到 Codex / OpenCode / ACP。两篇可以对照看"通用 Agent 宿主"与"垂直场景应用"的分工。
- **同为"AI 内容生产 + 多平台发布"，取向不同**：[AiToEarn](AiToEarn-AI内容变现与多平台发布.md) 更偏**变现与多平台分发**（作为 OpenClaw 插件的方式接入），本篇更偏**完整内容工作流 + 账号画像记忆**——一个讲"发出去怎么赚钱"，一个讲"从头到尾怎么做得像这个账号"。
- **同为"把能力做成可组装的技能、交给 Agent 执行"**：[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)、[agents.md](agents.md-给编码助手用的AGENTS文件格式.md)——那两篇是"能力包 / 约定格式"，本篇是一个把技能规模做到上百项的完整应用。

## 参考来源

- 项目仓库与 README：https://github.com/ZJU-REAL/Easel
- README 镜像（内容一致）：https://cdn.jsdelivr.net/gh/ZJU-REAL/Easel@main/README.md
- 英文 README：https://cdn.jsdelivr.net/gh/ZJU-REAL/Easel@main/README_EN.md
- Release 说明（v0.3.0）：https://github.com/ZJU-REAL/Easel/releases/tag/v0.3.0
- 项目主页：https://zju-real.github.io/Easel/
- 元数据与活跃度：GitHub REST API（`/repos`、`/commits?sha=main`、`/git/trees/main`、`/releases`），2026-11-03

以上信息核验于 2026-11-03（元数据取自 GitHub API，活跃度经默认分支 `main` 的 commits 接口复核，许可证按仓库 `LICENSE` 与 API 双向确认）。未安装、未运行、未接入平台账号、未审计源码。
