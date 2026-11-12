# NonsPlayer：多音源跨平台音乐播放器与插件系统

> 记录一个用 Rust + Tauri 重写的多音源音乐播放器：自述"**轻量 | 全平台 | 流媒体&本地**"，核心看点是把**音源适配器**和**插件**都做成带契约的 SDK，并把关键架构决定写进了 ADR（架构决策记录）。第一句先记清三件事：**它有一段改名史（旧路径 `Miaoyww/Nons` → 现 `Miaoyww/NonsPlayer`）**；**匿名遥测默认开启，但 `PRIVACY.md` 把采集字段与边界写得很具体**；**许可证是 GPL-3.0（强传染）**。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README（56 行）、`PRIVACY.md`、`CHANGELOG.md` 开头、`LICENSE` 首行、`packages/` / `plugins/` / `docs/adr/` 目录清单、`docs/music-adapters.md` 与 `docs/architecture.md` 开头、分支 / Release / 提交元数据；**用 GitHub API 核验旧路径 `Miaoyww/Nons` 重定向到同一仓库 id `1396883349`**；**未安装、未运行、未接入任何音源或插件、未审计源码**）
- 适用范围：桌面端（Tauri：Windows / macOS / Linux；仓库另有 `docs/android.md`）；插件与音源适配器的运行形态是 **WASM Component**

## 核心结论

**① 架构是"播放内核 / 音源适配器 / 插件"三段，并且用 ADR 把边界钉死了。** 仓库有一整组 `docs/adr/0001-playback-boundary.md` ～ `0009-language-neutral-wasm-host.md`，从"播放边界"一路记到"语言中立的 WASM 宿主"。**把架构决策单独留档、并编号成系列**，在这类个人音乐播放器项目里并不常见，说明作者是按"长期可维护"而不是"能跑就行"在做。

**② 音源接入被抽象成契约，而不是写死在代码里。** `docs/music-adapters.md` 描述了一套 `MusicService / AdapterManager` 路由 + `EntityRef = { source, kind, id }` 的实体寻址 + `MusicTrack` / `SourceDescriptor.shareLinkTemplates` / 多档封面 `CoverImage` / `lyricIdentifiers` 等模型，运行形态是 **WASM Component**。配套的 `packages/` 下发了五个 SDK 包：`adapter-host`、`music-adapter-sdk`、`music-web-provider`、`plugin-sdk`、`wasm-crypto`；`plugins/` 下还有 `manifest.schema.json` / `configuration.schema.json` 与 `wit/plugin.wit`（WASM 接口定义）以及一批 `*-fixture` 测试样例。**"多音源"因此不是内置一堆爬虫，而是一套宿主 + 契约 + 样例。**

**③ 匿名使用统计默认开启——但披露写得具体，不算含糊。** `PRIVACY.md`（7,405 B，2026-10-28 更新）明说遥测**默认开启**，采集字段为 `event_id`（随机 UUID）、`device_id`（本地随机 UUID v4，**非硬件派生**）、`platform`、`app_version`、`occurred_at`、`play_count`；`adapters` / `plugins` 两个字段**保留但始终发送空数组**。文件同时列出**明确不采集**的内容（账号凭据、歌曲与歌单、播放历史、本地文件路径、硬件标识），并自陈"匿名"的边界：**跨重启仍可通过 `device_id` 关联到同一安装**。**默认开启 + 细粒度披露 + 主动写明局限**，这样的组合值得作为"遥测该怎么写清楚"的样本。

**④ 一段改名史，加上单人开发。** 用 API 查旧路径 `Miaoyww/Nons` 会**返回同一仓库（id 一致）**，即**改名而非新建**，历史与 Release 均保留。贡献者列表里**只有 `Miaoyww` 一人、636 次提交**——关注度（292 Star）与维护人数之间的落差，需要在评估长期可持续性时一并考虑。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Miaoyww/NonsPlayer](https://github.com/Miaoyww/NonsPlayer)（**旧路径 `Miaoyww/Nons`**） |
| 改名核验 | API 查 `Miaoyww/Nons` **返回同一仓库 id `1396883349`**，判定为改名/迁移，非新建 |
| 描述定位 | README 自述"**轻量 | 全平台 | 流媒体&本地**"；`灵感来源` 提及 YesPlayMusic、coriander_player、SPlayer |
| 许可证 | **GPL-3.0**（`LICENSE` 35,149 B），强传染 |
| 主语言 | Rust（前端 React + Tauri） |
| 关注度 | **292 Star、14 Fork、1 关注、2 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-09-30 创建**；`pushed_at` 2026-11-04；默认分支 **`main`**（另有 `feat/adapter`）；**贡献者仅 `Miaoyww` 一人 636 次提交** |
| 版本节奏 | Release **v1.11.0**（2026-11-03，9 个资产）、v1.9.x、v1.8.0～v1.8.8；`CHANGELOG.md` **自 1.11.0 起记**（README / tag 用 `v1.11.0`） |
| 体积 | API `size` 31,894 KB（约 31 MB）；目录树 1058 项 / **856 个 blob / 15,833 KB** |
| 话题标签 | `music-player`、`plugin-system`、`react`、`rust`、`tauri` |
| 关键文件 | 根目录 `CHANGELOG.md`(102,685 B)、`LICENSE`(35,149 B)、**`PRIVACY.md`(7,405 B)** |

## 它能做什么（结构清单）

- **五个 SDK 包**（`packages/`）：`adapter-host`、`music-adapter-sdk`、`music-web-provider`、`plugin-sdk`、`wasm-crypto`
- **插件契约**（`plugins/`）：`manifest.schema.json`、`configuration.schema.json`、`wit/plugin.wit`，以及 `music-fixture` / `runtime-fixture` / `settings-fixture` / `ui-fixture` 四类样例
- **架构文档**（`docs/`）：`architecture.md`、`music-adapters.md`、`plugin-configuration.md`、`plugins.md`、`wasm-adapter-runtime.md`、`cache-and-local.md`、`resource-providers.md`、`android.md`、`diagnostics.md`、`performance.md`、`release.md`
- **ADR 0001～0009**（`docs/adr/`）：从 `0001-playback-boundary` 到 `0009-language-neutral-wasm-host`
- **应用目录**（`app/`）：`src`、`src-tauri`、`public`、`preview`、`tests`
- **CI / 发布**（`.github/workflows/`）：`ci.yml`、`publish.yml`、`publish-plugin-sdk.yml`、`signpath-test.yml`
- **变更记录**：本地标签编辑、Windows 原生下载、全屏播放器重做（1.11.0 起）

## 使用前需要注意

- **许可证是 GPL-3.0（强传染）**：二次分发或用其代码做闭源产品需按 GPL 处理，比 MIT/Apache 约束重。
- **遥测默认开启**：如需关闭应在运行时设置中确认开关位置（本笔记未实测开关项）。`device_id` 为随机会话/安装标识，跨重启可关联同一安装。
- **多音源依赖第三方适配器**：本项目提供宿主与契约，实际能不能听取决于所用适配器；此类工具的音源合规性与可用性随时间变化，需自行判断。
- **`CHANGELOG.md` 从 1.11.0 起记**：早期版本变更不在此文件内，追溯旧版行为需另查提交。
- 本笔记**未审计源码、未运行**，以上均为文档与元数据层面的观察。

## 什么时候会再看

- 需要给音乐播放器做"**音源 / 插件用 WASM Component 插件化**"的架构参考时；
- 想抄"**超过一定规模就用 ADR 留档架构决策**"的做法时；
- 想参考"**默认开启遥测但把字段、非采集项与局限写具体**"的隐私披露写法时；
- 需要一份 GPL-3.0 音乐客户端的实际工程组织样本时。

## 相关笔记

- **同样是"音乐获取"，但方向相反**：[go-music-dl：多平台音乐搜索与下载工具](go-music-dl-多平台音乐搜索与下载工具.md)——那是命令行下载器，回答"怎么把歌拿下来"；本篇是桌面播放器，回答"怎么把多音源接进统一播放体验"。一个管获取，一个管播放与扩展。
- **同样是"多源媒体客户端"，侧重不同**：[boxplayer：聚合网盘与影视的全平台播放器](boxplayer-聚合网盘与影视的全平台播放器.md)——那是把 12 家网盘 + 媒体服务器聚进客户端并内置 CLI/MCP；本篇聚焦音乐与插件系统。两篇可对照"**多源聚合**"在影视与音乐两种内容上的不同抽象。
- **媒体下载这条线的其它样本**：[galaxy-downloader：多平台媒体下载器](galaxy-downloader-多平台媒体下载器.md)——同为"媒体获取"类，可与 `go-music-dl` 一起对照"下载器"与"播放器"的边界。
- 本页索引位置：[GitHub 项目收藏](README.md)
- **同为"桌面应用 + 开放插件 SDK"，且都桥接既有社区插件生态**：[Lertaro](Lertaro-Windows本地文件搜索与效率启动器.md)——那篇是 **Windows 本地文件搜索与效率启动器**（.NET/WPF，兼容 Flow Launcher 插件）；本篇是**多音源音乐播放器 + 插件系统**。可对照"插件契约怎么定、第三方插件如何桥接"这一层。

## 参考来源

- 仓库主页：https://github.com/Miaoyww/NonsPlayer （核验日期 2026-11-04）
- 隐私说明 `PRIVACY.md`、变更记录 `CHANGELOG.md`、音源适配器文档 `docs/music-adapters.md`、ADR `docs/adr/`（核验日期 2026-11-04）
- GitHub API：`repos/Miaoyww/NonsPlayer`、旧路径 `repos/Miaoyww/Nons`、`git/trees/main?recursive=1`、`releases`、`contributors`（核验日期 2026-11-04）
