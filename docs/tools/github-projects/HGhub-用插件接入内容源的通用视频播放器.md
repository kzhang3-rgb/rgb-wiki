# HGhub：用插件接入内容源的通用视频播放器

> 记录这个"做播放器、不做内容源"的通用视频播放器：它把内容获取全部外置成 **JavaScript 插件**，自己只维护播放器与插件接口。第一句先记清三件事：**PolyForm Noncommercial 1.0.0——源码公开但明确禁商用**；**极新且增长极快（2026-11-03 创建，核验时约 9,000 Star）**；**判别它时要先分清"播放器本体"与"第三方插件提供的内容"是两回事**。未从源码运行或构建、未安装任何插件、未播放任何内容。

- 状态：草稿
- 最近核验：2026-11-16（阅读 `main` 分支 README 全文、`LICENSE` 首行、`docs/PROJECT_POLICY.md`、`docs/developers/status.md`、文件清单（276 项）、`commits/main.atom` 与 ungh 镜像 / GitHub 页面内嵌元数据 / shields 徽章；**未 clone、未从源码运行或构建、未下载安装包、未安装、未导入任何插件、未播放任何内容、未审计源码**）
- 适用范围：桌面端（Tauri 2：Windows / macOS / Linux）+ Android；README 称当前重点完善 **Windows 与 Android**，其他平台"仍在适配和验证"；构建侧为 **Rust · Tauri 2 · React · TypeScript**

## 核心结论

**一句话定位（README 自述）**：HGhub 是**一个通过插件连接内容的通用视频播放器**，把浏览、搜索、播放、个人片库放在同一个应用里；**通过导入 JavaScript 插件接入不同内容来源**。

**① 它的边界划得很清楚：做播放器与协议，不碰内容。** README 与 `docs/PROJECT_POLICY.md` 反复写明——**应用不内置内容源、不分发第三方插件、不提供官方源列表**；首次使用要**自行导入插件**，无需注册 HGhub 账号；**不运营内容托管服务**，与外部内容提供者"无官方隶属或背书关系"，并承诺"**将积极响应来自上游、版权方及其授权代表、有关机构的正式通知**"。**这条边界是理解它的关键：内容能不能看，取决于你装了哪个插件、那个插件连的是什么源。**

**② 技术栈是 Rust 桌面栈 + Web 前端。** 客户端用 **Rust · Tauri 2 · React · TypeScript** 构建（与 wiki 里 [NonsPlayer](NonsPlayer-多音源跨平台音乐播放器与插件系统.md) 的选型同类）；仓库根目录含 `src-tauri/`（158 项，后端）、`src/`（53 项，前端）、`pnpm-workspace.yaml`、`vite.config.ts`。**"插件"在这里指运行期导入的 JavaScript 内容源 / 解析器**（另有 HPP 插件协议文档），不是编译期写死的站源——这与 [guoapp](guoapp-多端短剧影视聚合客户端.md) 的"编译期 Go provider"是两条不同的扩展路线。

**③ 许可证是"源码公开但禁商用"。** `LICENSE`（4,655 字节）为 **PolyForm Noncommercial 1.0.0**，首行 `Required Notice: Copyright (c) 2026 HGhub contributors`；README 与 `PROJECT_POLICY.md` 都强调**禁止任何商业用途**（含销售、广告变现、卡密、VIP、付费解锁，以及作为收费产品或服务的一部分），且**不提供商业授权、不接受广告或商务合作**。**注意：这不是 OSI 认可的开源许可，shields 也识别为 `not identifiable by github`。**

**④ 很新，也长得很快。** **2026-11-03 创建**，核验时约 **9,000 Star / 7,157 Fork**——**13 天内**达到这个量级，且 **fork 数与 star 数之比异常高（约 79%）**；仓库尚在"**插件版早期开发阶段**"（`docs/developers/status.md` 审计日期 2026-11-15：应用 **0.0.1**、协议 **1.0.0-dev**），**0 个 Release**。**这类"极新 + 高星高 fork"的仓库要留意：热度不代表成熟，也不代表内容合规。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Minireel/HGhub](https://github.com/Minireel/HGhub) |
| 描述（API） | `Plug-in media player` |
| README 自述 | "一个通过插件连接内容的通用视频播放器"；不内置内容源、不分发插件、不提供源列表 |
| 许可证 | **PolyForm Noncommercial 1.0.0**（`LICENSE` 4,655 字节；`Required Notice: Copyright (c) 2026 HGhub contributors`）——**非商业专有许可，非 OSI 开源** |
| 主语言 | Rust 42.5%（shields 顶层语言；实为 Rust + TypeScript 的 Tauri 工程） |
| 关注度 | **9,005 Star / 40 关注 / 7,157 Fork**（ungh 镜像；shields 9.2k / 7.5k）；**6 个开放 Issue**、**贡献者 1 人**（shields） |
| 创建 / 最近推送 | **2026-11-03 创建**；`pushed_at` **2026-11-15**；`main` 最新提交 **2026-11-16 05:34 UTC**（`feat: refine playback experience and add cross-platform proxy settings`，作者 `WEP-56`） |
| 默认分支 / Release / 文件数 | `main`；**0 个 Release**；**276 个文件** |
| 文档站 | 在线文档 <https://minireel.github.io/HGhub/>（GitHub Pages，`docs/site/` 提供站点资源） |
| 社区 | [Linux.DO](https://linux.do/) |
| 平台状态 | README：当前重点 **Windows 与 Android**，其他平台在适配；`docs/developers/status.md` 列明大量能力"尚缺 / 未完成" |

## 它能做什么

**核心体验（README 表格摘要）**

| 能力 | 说明 |
| --- | --- |
| 沉浸式播放 | 桌面与移动端界面，手势与播放控制，面板内查看详情、选集与评论 |
| 多来源浏览与搜索 | 推荐、分类筛选、聚合搜索；可按来源查看结果，保留本地搜索历史 |
| 灵活的播放选择 | 支持 HLS 与普通视频；切换线路、清晰度、解析器，也可搜其他来源继续看 |
| 自己的片库 | 本地收藏、观看历史、续播进度；支持追更，可把插件提供的云端列表导入本地收藏 |
| 按需扩展 | 通过**文件、网址或插件列表**导入内容源与解析器，检查更新，更新失败可**回滚上一可用版本** |
| 更多内容形式 | 除点播外，还可通过插件接入**直播、信息流、图文**内容 |

**使用流程（README）**：打开**插件中心 → 导入与订阅**（移动端从"我的"进入）→ 选择插件文件、粘贴代码或输入插件 / 列表网址 → 确认安装并启用 → 从首页、搜索或分类找到内容开始播放。

**插件与协议文档**（`docs/`）

- **插件开发**：`docs/developers/`（`index.md`、`quickstart.md`、`host-api.md`、`testing.md`、`deployment.md`、`distribution.md`、`agent.md`、`status.md`）
- **HPP 插件协议**：`docs/plugin-protocol/01-plugin-file` ～ `09-media-transport` + `IMPLEMENTATION.md`（插件文件、运行时、宿主规则、宿主 API、内容模型、能力、生态、前端、媒体传输）
- **维护政策**：`docs/PROJECT_POLICY.md`、`docs/DEVELOPMENT.md`
- 另注明 README 只为 Agent 参与插件开发提供了单独指南（`docs/developers/agent.md`）

## 使用前需要注意

- **① 许可证是"非商业"而非开源。** PolyForm Noncommercial 1.0.0 **禁止一切商业用途**（销售、广告变现、卡密、VIP、付费解锁，或作为收费产品 / 服务的一部分）。**fork 改造或再分发前先读全文**；维护者明确**不提供商业授权**。
- **② 内容来源完全取决于第三方插件——这也是最大的不确定点。** 应用本身**不内置、不分发、不推荐任何内容源**。这类"插件式影视播放器"在实际使用中常被用来接入来源不明的影视资源，**内容合法性、清晰度与可用性均由所装插件决定，不属于 HGhub 能保证的范围**。README 只说"第三方内容的可用性由相应来源决定"。**本笔记只记录该项目存在与结构，不对任何插件或内容来源作评价或推荐。**
- **③ 极新 + 高星 + 高 fork，别把热度当成熟。** 创建仅十余天、应用版本 **0.0.1**、**0 Release**、**1 位贡献者**；`status.md` 自列大量"尚缺"（设备能力探测、字幕 SRT/ASS、外部播放流、跨端 / 后台等）。**fork 比异常高（约 79%）**，是否符合一般项目的增长形态、fork 被用作什么用途，均需自行判断，本笔记未核验。
- **④ 没有分发好的安装包。** 0 个 Release，README 说"目前可**从源码运行或构建**"（详见 `docs/DEVELOPMENT.md`）。**我没有实际构建或运行**，因此"能否顺利跑起来、各平台完成度如何"均为文档自述，未复现。
- **⑤ 插件是运行期加载的 JavaScript。** 导入第三方插件等于在应用内运行他人代码；README 未展开说明插件的权限边界与沙箱程度。**本笔记未审计源码，插件的实际权限与数据流向未验证**。
- 本笔记**只读了 README、许可证、两份文档与元数据**，未 clone、未构建、未运行、未导入任何插件、未播放任何内容。

## 什么时候会再看

- 想参考"**播放器只做宿主与协议、内容全部外置成插件**"这种把内容与工具解耦的架构时。
- 想对照"**插件式内容客户端**"的几种扩展路线（HGhub 运行期 JS 插件 · [NonsPlayer](NonsPlayer-多音源跨平台音乐播放器与插件系统.md) WASM Component · [guoapp](guoapp-多端短剧影视聚合客户端.md) 编译期 Go provider）时。
- 想复盘"**源码公开但用 PolyForm Noncommercial 禁商用**"这种介于开源与闭源之间的许可选择时。
- 想观察"**极新仓库短期冲高 Star / Fork**"这类现象、并练习"热度 ≠ 成熟 / 合规"的判别时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"多源影视聚合播放客户端"，扩展与许可路线不同**：[boxplayer：聚合网盘与影视的全平台播放器](boxplayer-聚合网盘与影视的全平台播放器.md)——那篇聚合的是**网盘 + 媒体服务器 + 本地目录**（GPL-3.0，Electron），本篇聚合的是**运行期导入的第三方 JS 插件**（PolyForm Noncommercial）；可对照"存储 / 媒体源聚合"与"插件化内容源"两种路线，以及许可态度差异。
- **同为"聚合型影视客户端"，站源形态不同**：[guoapp：Flutter 写的多端短剧 / 影视聚合客户端](guoapp-多端短剧影视聚合客户端.md)——那篇把**在线站源做成编译期 Go provider**（无许可证、源码来自他人），本篇把内容源做成**运行期导入的 JS 插件**（非商用许可）；可对照"编译期站源"与"运行期插件"两类，以及"无许可证"与"非商业许可"两种版权状态。
- **同为"Rust + Tauri 的多源媒体播放器 + 插件系统"**：[NonsPlayer：多音源跨平台音乐播放器与插件系统](NonsPlayer-多音源跨平台音乐播放器与插件系统.md)——那篇做**音乐**、插件与音源适配器的运行形态是 **WASM Component**（GPL-3.0，有 ADR 留档）；本篇做**视频**、插件是 **JavaScript**；可对照同类技术栈下"插件契约怎么定、运行形态选什么"的两种取舍。
- **同为"第三方内容聚合客户端"，许可与分发不同**：[AvPlay：成人视频聚合播放客户端](AvPlay-成人视频聚合播放客户端.md)——那篇**仅为分发仓库**（无源码、无许可证，内容敏感）；本篇**源码公开但有非商业许可证**。两篇放在一起，可对照"内容型客户端"在"源码可得性 + 许可状态 + 内容边界"上的不同组合。

## 参考来源

- 仓库主页与 README（核验日期 2026-11-16）：https://github.com/Minireel/HGhub
- 许可证 `LICENSE`（PolyForm Noncommercial 1.0.0，4,655 字节）：https://github.com/Minireel/HGhub/blob/main/LICENSE
- 项目声明 `docs/PROJECT_POLICY.md`、支持状态 `docs/developers/status.md`（审计日期 2026-11-15）、开发指南 `docs/DEVELOPMENT.md`、插件协议 `docs/plugin-protocol/`（核验日期 2026-11-16）
- 在线文档站：<https://minireel.github.io/HGhub/>
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 提交时间）：GitHub 页面内嵌数据、ungh.cc 镜像与 `commits/main.atom`，2026-11-16；开放 Issue 数与贡献者数取自 shields.io，2026-11-16

以上信息核验于 2026-11-16：Star / Fork / 版本号与支持状态都可能快速变化（该仓库创建仅十余天）。未 clone、未构建、未运行、未导入任何插件、未播放任何内容、未审计源码；**内容来源与许可边界请以许可证全文与维护者公开记录为准**。
