# HGhubX：从 HGhub 分叉出的跨平台短剧客户端

> 记录这个**明确是 [Minireel/HGhub](https://github.com/Minireel/HGhub) fork** 的跨平台短剧客户端（`zxsos/HGhubX`，Tauri 2 + React + Rust）：它把父仓库的"通用插件式播放器"收敛成**面向短剧的成品客户端**，加了 iOS 与竖屏沉浸式播放，并**已发出 3 个 Release（v2.1.0 → v2.1.2）**。三条先记住：**它是 fork（GitHub 元数据 `isFork:true`，父仓库 `Minireel/HGhub`）**；**许可证是 PolyForm Noncommercial 1.0.0——源码公开但禁商用（沿用父仓库路线）**；**README 顶部以最大字号写明"禁止在中国大陆地区下载 / 复制 / 分享 / 传播本软件"**。未 clone、未构建、未安装、未播放任何内容。

- 状态：草稿
- 最近核验：2026-11-17（阅读 `main` 分支 README 全文（66 行）、`LICENSE` 首行、文件清单（269 项）、Release 列表（3 条）、`commits/main.atom`、GitHub 仓库页 HTML（用于确认 fork 关系）与 ungh 镜像 / shields 徽章；**未 clone、未从源码构建或运行、未下载安装包、未安装、未播放任何内容、未审计源码**）
- 适用范围：**跨平台短剧客户端**——README 称 Windows / macOS / Linux / Android / iOS（"iOS 圆角与玻璃材质 · 沉浸式竖屏播放"）；构建栈为 **Tauri 2 · React · Rust · TypeScript**

## 核心结论

**一句话定位（API 描述）**：README / API 描述为 "Cross-platform short-drama client built with Tauri 2, React and Rust."。它**是 HGhub 的 fork，但定位已从"通用插件播放器"改为"短剧客户端"**。

**① 它是 fork，不是独立全新项目。** GitHub 仓库页 HTML 明确：`"isFork":true`、`parentRepo: {"ownerLogin":"Minireel","name":"HGhub"}`；README 也保留了从旧版迁移的痕迹——**存储键 `hghub_*` → `hghubx_*`**、**环境变量 `HGHUB_*` → `HGHUBX_*`**（`HGHUBX_PROXY` / `HGHUBX_STRICT` / `HGHUBX_TEST_COVER_URL` / `HGHUBX_TEST_MEDIA_URL` / `HGHUBX_TEST_MEDIA_OUTPUT`）。**本笔记核实到的是"fork 关系"这一事实，不判断它与父仓库的代码重合度有多深。**

**② 它能出成品包了——这是与父仓库最直观的差别。** **3 个 Release**：`v2.1.0`（2026-11-15 08:37）、`v2.1.1`（15:26）、`v2.1.2`（16:56），**全在同一天发布**，均非 pre-release。最新 v2.1.2 的更新点例如"标签详情同时展示短剧和吃瓜帖子并分别分页""横屏视频按当前播放源媒体尺寸识别"等。**核验时父仓库 HGhub 为 0 Release、只能从源码构建**——两者差别明显。

**③ 许可证是"源码公开但禁商用"。** `LICENSE`（4,654 字节）为 **PolyForm Noncommercial 1.0.0**，首行 `Required Notice: Copyright (c) 2026 HGhubX contributors (https://github.com/zxsos/HGhubX)`；README 写明**自 v1.1.0 起改为该许可**，**禁止任何商业用途**（打包出售、接入付费 / 会员 / 广告变现、作为收费产品或服务的一部分分发），个人学习 / 研究 / 自用 / 非营利不受限，并要求分发时**附带许可证全文或链接 + `Required Notice` 声明行**。**这不是 OSI 认可的开源许可，shields 也识别为 `not identifiable by github`**（与父仓库一致）。

**④ README 顶部有一条很强的地域限制声明（原文）**："**禁止在中国大陆地区下载、复制、分享、传播本软件及通过本软件获取的相关资源；不得提供相关下载链接或传播服务**"，并声明"本条亦适用于相关安装包、更新包、镜像与转发"。这条比父仓库更前置、更醒目。

**⑤ 很新、增长快、fork 比例高。** **4,326 Star / 2,754 Fork / 17 Watcher**（ungh 快照，2026-11-17；shields 同期 4.3k / 2.8k）；**5 个开放 Issue、贡献者 6 人**。**2026-11-09 创建**、`pushedAt` **2026-11-16**；**fork / star 比约 64%**。仓库 **269 个文件**，主语言 **TypeScript 46.0%**（Tauri 工程的 Web 前端）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [zxsos/HGhubX](https://github.com/zxsos/HGhubX) |
| 描述（API） | "Cross-platform short-drama client built with Tauri 2, React and Rust." |
| **fork 关系** | **是 [Minireel/HGhub](https://github.com/Minireel/HGhub) 的 fork**（GitHub 仓库页 HTML：`"isFork":true`、`parentRepo = Minireel/HGhub`） |
| 许可证 | **PolyForm Noncommercial 1.0.0**（`LICENSE` 4,654 字节；`Required Notice: Copyright (c) 2026 HGhubX contributors`）——**非商业专有许可，非 OSI 开源**；shields `not identifiable by github` |
| 主语言 | **TypeScript 46.0%**（shields 顶层语言；实为 Tauri 2 + Rust + React + TS 工程） |
| 关注度 | **4,326 Star / 2,754 Fork / 17 Watcher**（ungh，2026-11-17；shields 4.3k / 2.8k）；**5 个开放 Issue、贡献者 6 人**（shields） |
| 创建 / 最近推送 | **2026-11-09 创建**；`pushedAt` **2026-11-16**、`updatedAt` 2026-11-17；`main` 最新提交（atom）2026-11-15 `fix(tags): import post card component`（作者 `zxsos`） |
| 默认分支 / Release / 文件数 | `main`；**3 个 Release**（v2.1.0 / v2.1.1 / v2.1.2，均 2026-11-15）；**269 个文件** |
| 平台 | README：**Windows / macOS / Linux / Android / iOS**；"iOS 圆角与玻璃材质 · 沉浸式竖屏播放" |
| 目录结构（前几层） | `src-tauri/`（83 项，Rust 后端）、`src/`（80 项，前端）、`scripts/`（36）、`tests/`（24）、`docs/`（20）、`.github/`（4）、`rules/`（4）；根目录含 `package.json`、`vite.config.ts`、`pnpm-lock.yaml`、`LICENSE`、`hghubx.svg` 等 |

## README 关键内容（自述）

- **顶部地域限制**：禁止在中国大陆下载 / 复制 / 分享 / 传播本软件及其获取的资源，也不得提供下载链接或传播服务（含安装包 / 更新包 / 镜像 / 转发）。
- **免责声明**：仅供**学习与技术交流**，**不提供、不存储、不分发任何音视频内容**，与任何内容提供方无关联；内容均来自第三方站点，版权归原作者与上游站点；后果自负。若权利方认为侵权，可经 GitHub Issues 联系，"会尽快核实并配合处理"（含修改 / 移除内容、暂停功能、停止维护、归档或调整仓库可见性）。
- **使用须知（六条）**：① 以访客身份正常使用，勿滥用；② **不要给上游增加压力**——本项目**本身不提供下载功能**，勿改代码做批量下载 / 抓取、勿高频轮询或并发轰炸接口、勿挂机刷播放量，且**内置封面与播放列表缓存不要改小或关闭**；③ 尊重上游版权——**不去水印与站点标识、不搬运 / 转码 / 修改后二次分发、更不出售**；④ **不要公开传播接口与直链**（接口地址、播放直链、解密方式勿公开张贴或打包成"资源站"）；⑤ **不要商业化**（详见 LICENSE）；⑥ 合理使用——仅供个人学习 / 研究 / 自用，**24 小时内删除相关资源**并支持正版；并须遵守当地法律。
- **仿冒提醒**：明确点名 **[`hichuv` 仓库](https://github.com/hichuv)** 与本项目无关，是**在旧版本源码基础上擅自加入卡密验证、VIP 付费解锁等变现功能的修改版**，代码改动不透明、安全性无法验证，"请勿下载、勿注册、勿付费"；并声明本项目**从未、也不会**加入任何卡密 / VIP / 付费解锁 / 广告。
- **升级兼容（承接父仓库的痕迹）**：本地收藏 / 观看记录 / 偏好 / 切集迁移标记 / 主题使用 `hghubx_*` 存储键，首次启动时把当前 WebView 存储中的旧 `hghub_*` 数据迁移过来（确认写入成功后才清理旧键，写入失败则保留并读取旧数据、下次启动重试）；环境变量统一为 `HGHUBX_*`，旧 `HGHUB_*` 继续兼容。
- **免责声明（第二处）**：本项目与内容提供方无关联，作者不参与内容制作 / 上传 / 分发；仅在官方渠道发布，任何第三方修改版（尤其加卡密、VIP 变现的版本）与作者无关。

## 使用前需要注意

- **① 它是 fork，先分清"父仓库"与"这个分支"。** 本仓库为 `Minireel/HGhub` 的 fork（已由 GitHub 元数据确认），**定位已改为"短剧客户端"**。**本笔记未比对两仓库的代码重合度、未判断它相对父仓库衍生得多深**；父仓库笔记见：[HGhub](HGhub-用插件接入内容源的通用视频播放器.md)。
- **② 许可证是"非商业"而非开源。** PolyForm Noncommercial 1.0.0 **禁止一切商业用途**；分发修改版须**附带许可证全文或链接 + `Required Notice` 行**。**fork 改造或再分发前先读 LICENSE 全文**。
- **③ README 有强地域限制声明。** 顶部明确"禁止在中国大陆地区下载 / 复制 / 分享 / 传播"，并含安装包 / 更新包 / 镜像 / 转发。**该要求为项目自述，是否及如何遵守由使用者自行判断并承担后果。**
- **④ 内容来源与合规风险不在本客户端能保证的范围。** 免责声明称不提供 / 不存储 / 不分发内容、内容来自第三方站点。**本笔记只记录该项目存在与结构，不对其内容获取方式、所连来源或合法化作任何评价或推荐。**
- **⑤ 极新 + 高 Star 高 Fork。** 创建仅数天、**3 个 Release 全在同一天**、**fork / star 比约 64%**——**热度不代表成熟**；README 也未展开说明缓存、代理（`HGHUBX_PROXY`）与"安全模式"的完整行为。
- **⑥ 官方包的真实性要自己认。** README 特别提示存在同名仿冒（加卡密 / VIP 的 `hichuv` 版本）；下载前请核对**官方 Release 页**与项目自述的渠道，勿从第三方"资源站"获取。
- **⑦ 我未做任何验证。** 仅阅读 README、`LICENSE` 首行、文件清单、Release 列表与元数据；**未 clone、未构建、未运行、未安装、未播放、未审计源码**；功能与平台完成度均为 README 自述。

## 什么时候会再看

- 想对照"**同一个项目分成父仓库 / fork 分支两条线**"（通用插件播放器 → 面向短剧的成品客户端），以及它如何用**存储键 / 环境变量改名**承接旧数据时。
- 想参考"**Tauri 2 + Rust + React**"的跨平台（含移动端）短剧客户端工程结构与目录划分时。
- 想复盘"**源码公开但用 PolyForm Noncommercial 禁商用 + 强地域限制声明 + 仿冒提醒**"这套组合式 README 写法时。
- 想观察"**极新 fork 在数天内冲高 Star / Fork**"的现象（与父仓库同类的增长形态）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **直接父仓库（本仓库是它的 fork）**：[HGhub：用插件接入内容源的通用视频播放器](HGhub-用插件接入内容源的通用视频播放器.md)——那是**通用插件式视频播放器**（父仓库，核验时 0 Release、只能从源码构建、插件是运行期 JS），本仓库是**从它分叉出的跨平台短剧客户端**（已有 3 个 Release、定位收敛到短剧、加了 iOS）。**两篇合看可对照"父仓库 / fork 分支"的分化**，以及许可（同为 PolyForm Noncommercial）、存储键与环境变量（`hghub_*` → `hghubx_*`）的承接关系。
- **同为"短剧客户端"，端与侧重不同**：[hongguo-drama：红果短剧桌面版](hongguo-drama-红果短剧桌面版.md)——那是**围绕红果单一平台的 Electron 桌面客户端**（GPL-3.0，含下载 / NAS 落库 / 弹幕归档）；本篇是 **Tauri 2 跨平台短剧客户端**（PolyForm Noncommercial，覆盖含 iOS 的多端）。可对照"单一平台 + 下载归档"与"跨平台 + 播放"两种侧重。
- **同为"多端短剧 / 影视客户端"，站源形态不同**：[guoapp：Flutter 写的多端短剧 / 影视聚合客户端](guoapp-多端短剧影视聚合客户端.md)——那是 **Flutter 多端 + 多站源（红果 / 韩小圈 / 鬼片…）**、站源为**编译期 Go provider**（无许可证）；本篇是 **Tauri 2 多端短剧客户端**（PolyForm Noncommercial）。可对照"多站源聚合"与"聚焦短剧"，以及许可状态差异。

## 参考来源

- 仓库主页与 README（核验日期 2026-11-17）：https://github.com/zxsos/HGhubX
- 许可证 `LICENSE`（PolyForm Noncommercial 1.0.0，4,654 字节）：https://github.com/zxsos/HGhubX/blob/main/LICENSE
- Release 列表（v2.1.0 / v2.1.1 / v2.1.2，2026-11-15）：https://github.com/zxsos/HGhubX/releases
- fork 关系（`isFork:true`、`parentRepo = Minireel/HGhub`）：GitHub 仓库页 HTML 内嵌元数据，2026-11-17
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 提交）：ungh.cc 镜像、`commits/main.atom` 与 shields.io，2026-11-17

以上信息核验于 2026-11-17：Star / Fork / 版本与平台支持都可能快速变化（该仓库创建仅数天、当天连发三个 Release）。**未 clone、未构建、未运行、未安装、未播放任何内容、未审计源码**；**内容来源、许可边界与地域限制要求请以 LICENSE 全文、README 与维护者公开记录为准。**
