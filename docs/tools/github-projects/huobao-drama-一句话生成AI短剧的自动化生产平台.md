# huobao-drama：一句话生成 AI 短剧的自动化生产平台

> 火宝短剧（Huobao Drama）——把"小说文本 → 改写剧本 → 提取角色 / 场景 / 道具 → 生成形象参考图 → 拆解分镜 → 批量文生视频 → FFmpeg 拼接成片"整条链路做成一个可自托管平台；TypeScript 全栈（Nuxt 3 + Hono + Mastra Agent + Electron），自带桌面版与 Docker 镜像。想研究"AI 短剧自动化生产"这条管线的工程组装方式时值得再看。

- 状态：草稿
- 最近核验：2026-11-05
- 适用范围：评估"要不要自建 / 自托管一套 AI 短剧生产管线"时；本笔记只读 README / LICENSE / 元数据 / Release 列表，未下载安装包、未部署、未运行。

## 核心结论

- **卖点是"端到端自动化"**：官方描述为"一句话生成完整短剧，从剧本到成片全自动化"，六步流程在 README 里配了完整截图走查（新建项目 → 配置 AI 服务 → 剧本改写 → 资产制作 → 分镜与视频 → 拼接导出）。
- **技术栈是 TypeScript 全栈**：前端 Nuxt 3 + Vue 3 + TypeScript（**纯 CSS，无 UI 框架**）；后端 Hono + Drizzle ORM + **Mastra AI Agents** + better-sqlite3；桌面端 Electron（`utilityProcess` 承载后端）；数据落 SQLite（WAL）+ 本地文件。
- **内置 4 个 Mastra Agent**：`script_rewriter`（小说 → 格式化剧本）、`extractor`（角色 / 场景 / 道具提取与去重）、`storyboard_breaker`（剧本 → 分镜序列）、`prompt_generator`（图片提示词 + 分镜视频提示词）。Agent 技能（`SKILL.md`）放在 `backend/workspace/skills/`，**支持在界面里在线编辑**（桌面版写到 userData 的可写副本）。
- **许可为 CC BY-NC-SA 4.0，明确禁止商用**：这是本次核验里最关键的一条。**GitHub 的许可证识别报 `NOASSERTION` / "Other"，只有读 `LICENSE` 文件才知道真实条款**——典型的"许可证 API 盲区"。
- **已产品化**：发桌面安装包（macOS arm64 / Intel、Windows x64）+ **Docker 多架构镜像**（`huobao/huobao-drama`，amd64/arm64）+ 应用内更新器；维护极活跃，**核验当日（2026-11-05）刚发 v4.0.11**。
- **强绑定第三方商业服务**：AI 能力默认走 **FireMux**（`api.firemux.cn` / `firemux.com`）的聚合 Key，"火宝快捷配置"粘贴一个 Key 即写入文本 / 图片 / 视频三条配置。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | https://github.com/chatfire-AI/huobao-drama |
| 归属 | `chatfire-AI`（组织账号） |
| 一句话用途 | 基于 AI 的一站式短剧生成平台，从剧本到成片全自动化 |
| 许可 | **CC BY-NC-SA 4.0**（署名-非商业性使用-相同方式共享 4.0 国际，`LICENSE` 438 行 / 约 20.8 KB）。**GitHub 页面与 API 均显示 `NOASSERTION` / "Other"，须以 `LICENSE` 文件为准** |
| 主语言 | **TypeScript 44.8%**（其余为 Vue / CSS / 脚本） |
| 关注度（2026-11-05） | **17,046 Star / 3,176 Fork / 84 关注 / 26 开放 Issue**（页面值；ungh 镜像 17,045 / 3,176 / 84，一致） |
| 创建 / 最近推送 | **2026-01-05 创建；pushed 2026-11-05**（当日发布 v4.0.11） |
| 默认分支 | **`master`**（另有 `main` 分支，`README.md` / `LICENSE` 内容与 `master` 完全一致） |
| Release / Tag | **7 个 Release / 14 个 tag**；最新 **v4.0.11（2026-11-05）**，安装包单文件约 148~193 MB |
| 贡献者 | 11 位（`963408438` 316 次居首，`kongweigen` 27 次、`854771076` 16 次） |
| topics | **无** |
| README 语言 | `README.md` 中英双语（38.4 KB）；另有 `README.zh-CN.md` / `README.en.md` / `README.ja.md` / `README.ko.md` |
| 环境要求 | **Node.js 20+、npm 9+**；**内置 SQLite（免装数据库）**、**内置 FFmpeg/FFprobe 二进制（免装 FFmpeg）** |

> 口径说明：数据来自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章**，核验日 GitHub API 匿名配额已用尽，未取到 `size`。许可证以仓库 `LICENSE` 文件实测为准（已确认标题为 "Attribution-NonCommercial-ShareAlike 4.0 International"）。

## 功能与工作流

**架构目录**（README 原样）：`frontend/`（Nuxt 3 + Vue 3 + TS，纯 CSS）· `backend/`（Hono + Drizzle ORM + Mastra + better-sqlite3）· `backend/workspace/skills/`（Agent 技能 `SKILL.md`，界面在线编辑）· `desktop/`（Electron 主进程 + esbuild + electron-builder）· `data/`（生成资源与 SQLite）。

**六步生产流程**：① 新建项目（选画面比例 16:9 / 9:16，创建后不可改；选画面风格，影响全局生图提示词）→ ② 配置 AI 服务（首次）→ ③ 剧本阶段（粘贴原始小说 →「AI 改写」生成拍摄剧本，按集拆分、标注场景与角色）→ ④ 资产制作（对剧本执行**提取**得到角色 / 场景 / 道具清单，逐个或批量生成一致性参考图）→ ⑤ 分镜与视频（拆解分镜 + 生成视频提示词，顶栏切换视频模型，提示词里用 `@角色名` 自动映射参考图，支持批量生成与失败一键重试）→ ⑥ 拼接导出（勾选镜头 → FFmpeg 合成完整剧集，支持在线播放与下载）。

**多厂商适配**：文本 = OpenAI（兼容接口）/ Gemini；图片 = OpenAI / Gemini / 火山引擎；视频 = **火山引擎 Seedance 2.0（标准 / Fast / Mini）、MiniMax H3、阿里云百炼 Wan 3.0（Prime / 标准）**。

**部署方式**：桌面应用（README 标注"推荐"，预编译 dmg / exe，自带更新器）· 服务器部署（构建前端 → 拷到 `frontend/dist` → `backend` 跑 `npm start`，附 Nginx 反代示例）· Docker（预构建多架构镜像，或 compose 源码构建 + **Watchtower** 应用内更新）。默认端口：后端 `5679`，开发模式前端 `3013`。

## 使用前需要注意

- **CC BY-NC-SA 4.0 = 禁止商用**：README 明写"未经作者书面许可，不得将本项目整体或部分用于任何商业目的（包括付费服务、商业部署、转售等）"；允许修改与再分发，但须署名并以相同许可证共享。**要商用必须先取得作者书面许可**。
- **许可证识别有盲区**：GitHub 把这个仓库标成 `NOASSERTION` / "Other"，只看页面会误以为是"自定义许可"。真实条款在 `LICENSE` 文件里（CC BY-NC-SA 4.0 全文）。引用本项目时也必须按 CC 系列要求**标注非商业限制**。
- **安装包未签名**：macOS 首次打开可能提示"App 已损坏，无法打开"（Gatekeeper 隔离属性），官方给了两种修复——dmg 内自带的 `.command` 脚本，或终端执行 `sudo xattr -cr /Applications/HuobaoDrama.app`；Windows 未签名包会被 SmartScreen 拦，需要"更多信息 → 仍要运行"。**`sudo xattr -cr` 是绕过 Gatekeeper 的操作，等于放弃系统对应用签名的校验，执行前请自行判断风险**。
- **必须接第三方商业服务**：所有 AI 功能都要先在「设置 → 火宝快捷配置」粘贴 **FireMux** 的 API Key（`api.firemux.cn` / `firemux.com`）一键写入三条推荐配置，或手动按厂商逐个添加。**等于把 Key 与全部文本 / 图片 / 视频请求交给该聚合服务**；不配置则任何 AI 功能都不可用。
- **Docker compose 版本带 Watchtower**：容器可经 Watchtower HTTP API 自动拉新镜像并重建，`WATCHTOWER_TOKEN` 必须自己改且两侧一致——**该 token 泄露等价于给出"重建本机容器"的权限**。不需要自动更新时，README 也给了"删掉 `HUOBAO_WATCHTOWER_*` 两个环境变量"的退化为手动模式的做法。
- **README 内部口径不一致（多处）**：v4.0.0 更新日志写"**移除 Docker / MySQL 部署方式**"，但根目录 `Dockerfile` 仍在、部署章节也仍完整讲 Docker + Watchtower；v3.0.0 写"**厂商收敛：仅保留 OpenAI / Gemini / 火山引擎**"，但正文视频表里仍列 MiniMax、阿里云百炼；桌面下载表格里的文件名还停在 `4.0.5`，而同一页的下载按钮指向 `v4.0.11`。**以正文与 Release 为准，更新日志明显滞后**。
- **内容合规由使用者承担**：更新日志提到"真人 / 敏感内容审核失败时提示切换模型重试"——这类"换一个模型绕过内容审核"的引导，涉及各家平台条款，风险由使用者自负。
- **数据目录**：桌面版数据在用户目录（README 只给了 macOS 路径 `~/Library/Application Support/HuobaoDrama/`，**Windows 侧的具体路径未写明**）；卸载应用不影响数据，但需自行管理 SQLite 与媒体文件。
- 本笔记**未下载任何安装包、未 `docker pull`、未部署、未运行**，功能面全部来自 README 自述与 Release 说明。

## 什么时候会再看

- 想找一套**可自托管**的"AI 短剧 / 短视频自动化生产"方案（而不是纯云端 SaaS）时。
- 想参考"**Mastra Agent + 多厂商视频模型 + FFmpeg 拼接 + Electron 打包 + Watchtower 自更新**"这套工程怎么组装时。
- 想研究"**CC BY-NC-SA 开源 + 商业 API Key 服务**"这种"开源引流、增值收费"的商业模式时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同一个"短剧"题材的两端**：[hongguo-drama：红果短剧桌面版](hongguo-drama-红果短剧桌面版.md)——那是**看**短剧的第三方桌面客户端（消费侧，对接平台接口），本篇是**做**短剧的自动化平台（生产侧）。一个解决"怎么看"，一个解决"怎么造"。
- **上游的文本来源**：[easy-writing：易创网文写作软件](easy-writing-易创网文写作软件.md)——本篇第一个 Agent 干的正是"**小说 → 格式化剧本**"，那篇是面向长篇连载的网文写作软件（BYOK 接模型）。想串成"写小说 → 改剧本 → 生成短剧"，两篇是上下游。
- **视频模型侧的另一条接入方式**：[ComfyUI-MiniMaxH3：Easy-MiniMaxH3 视频节点套件](ComfyUI-MiniMaxH3-Easy-MiniMaxH3视频节点套件.md)——本篇把 **MiniMax H3** 作为可选的云端视频模型之一封装在设置页里，那篇是把 H3 做成 ComfyUI 节点放进自己的工作流。可对照"**在完整产品里调模型**"与"**在节点图里调模型**"两种做法。

## 参考来源

- 项目仓库与 README（中英双语）：https://github.com/chatfire-AI/huobao-drama
- 许可证：https://github.com/chatfire-AI/huobao-drama/blob/master/LICENSE（CC BY-NC-SA 4.0）
- 最新发行版：https://github.com/chatfire-AI/huobao-drama/releases/latest
- Docker 镜像：`huobao/huobao-drama`（Docker Hub）
- 配套商业服务：FireMux <https://www.firemux.cn> / <https://api.firemux.cn>；安装包国内镜像 `installer.firemux.site`
- 元数据来源（2026-11-05）：ungh.cc 镜像、GitHub 页面内嵌 JSON、shields.io 徽章；GitHub API 匿名配额用尽，未取到仓库体积。
