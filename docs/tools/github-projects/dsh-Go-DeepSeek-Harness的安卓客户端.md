# dsh-Go：把 DeepSeek Harness 装进 Android 手机的客户端

> 记录这个第三方 dsh 安卓客户端做了什么、依赖哪些前提，以及它在权限、许可与数据流向三个方面需要先看清的地方。未下载 APK、未安装。

- 状态：草稿
- 最近核验：2026-11-03（阅读仓库 README、ASSETS.md、GitHub API 元数据与最近提交，未下载 APK、未安装、未构建）
- 适用范围：Android 8.0（API 26）及以上；只连远程主机时不需要在手机上装运行环境

## 核心结论

这是社区开发者 ccch1mneyyy（DSH-TUI）做的 **DeepSeek Harness（dsh）第三方 Android 客户端**，用 Flutter 写。它走了两条路：一是把 Linux 运行环境（Ubuntu + Node.js + dsh）塞进 App 里在手机本机跑，免 Root、免另装 Termux；二是通过 SSH 或 DSHarness Remote 遥控电脑/服务器上已有的 dsh。

README 自己把边界写得很清楚，这点值得肯定：明说**与 DeepSeek 无关联**、本机运行不等于模型离线、开启手机操控后屏幕内容可能发给模型。**使用前最该看清三件事：AGPL-3.0 的传染范围、手机操控所需的系统级权限、以及默认开启的匿名使用统计。**

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [ccch1mneyyy/dsh-Go](https://github.com/ccch1mneyyy/dsh-Go)（README 内链接写作 `dsh-go`，GitHub 地址大小写不敏感） |
| 归属 | 个人开发者 ccch1mneyyy（提交作者名 `Chimney`，邮箱 `ccchimneyyy@gmail.com`；ASSETS.md 自述版权方为 DSH-TUI），非 DeepSeek 官方 |
| 许可证 | **AGPL-3.0**（根目录 `LICENSE`，34.5 KB，符合 AGPL 全文体量）；`sdk/` 与 `examples/` 声明为 MIT；品牌标识与吉祥物不在授权范围，见 `ASSETS.md` |
| 主语言 | Dart（Flutter） |
| 关注度 | 6,344 Star、313 Fork、17 订阅者（`subscribers_count`；`watchers_count` 与 Star 数相同，属 GitHub 历史遗留，不宜当两个指标） |
| 活跃度 | 2026-10-25 创建，最近一次推送 **2026-11-03**，即核验当天；默认分支 `main` 上最后一次提交为 2026-11-03 07:46 UTC 合并 PR #369，由作者本人完成，属**真实活跃**而非"假活跃" |
| 待办积压 | 272 个开放 Issue（仓库仅 9 天） |
| 仓库体积 | **约 408,795 KB ≈ 399 MB**，对 Flutter 应用而言明显偏大；我未逐目录定位体积来源，属待核验项 |
| 其他元数据 | 默认分支 `main`；`has_pages` / `has_wiki` / `has_discussions` 均为 false；11 个 topics（含 `dsh`、`dsh-tui`、`deepseek-harness`、`coding-agent`、`flutter`、`android`、`llm`） |
| 外部热度 | README 自述 Trendshift 全语言日榜第 1（2026-10-30），我未去第三方榜单独立核验 |

## 它能做什么

| 能力 | 说明 |
| --- | --- |
| 本机运行 | 在 App 内安装 Ubuntu、Node.js 与 dsh 运行环境，免 Root、免 Termux；需预留约 1 GB 空间。本机引擎默认走 PRoot 兼容启动 |
| 远程连接 | 通过 SSH 或李国宝的 [DSHarness Remote](https://github.com/liguobao/ds-harness-remote) 使用电脑/服务器上的 dsh；README 注明该渠道**当前只支持图片附件**、不能在 App 内打开网页版、插件管理限于开关、不支持 DS Bot 与对话日志导出 |
| 聊天与工具 | 模型切换、图片、语音输入、工具审批与提问；原生界面与网页版可一键切换且保持同一段对话 |
| 插件界面 | 提供适配 DSH Go 的原生工具卡、表单与面板，仓库内含 SDK、教程与示例（即 `sdk/` 与 `examples/`） |
| 手机操控 | 开启权限后让本机引擎读屏、点按、输入，三档确认方式 |
| 分享与外观 | 分享卡、对话回放链接（README 称端到端加密，密钥只在链接里）、主题与桌面图标、桌宠 |

安装形态：Release 页主推 **arm64** 包，x86_64 设备与模拟器用 x64 包，**仓库没有 32 位 ARM 构建**；README 明确说当前构建以 **APK 侧载**为目标，不是 Google Play 发布配置。另外提醒：装过签名不同的早期测试包时不能直接覆盖安装，卸载会清空对话、Linux 环境与设置。

## 目录结构

| 路径 | 内容 |
| --- | --- |
| `app/` | Flutter 界面与 Android Kotlin 平台实现 |
| `packages/dsh_client/`、`packages/dsh_remote/` | DSH Web API 与 DSH Remote 的 Dart 客户端 |
| `runtime/` | Linux 运行时打包、引擎补丁与 App 桥接 |
| `sdk/plugin-kit/` | 原生插件作者工具包，未发布到 npm |
| `examples/plugins/` | 插件示例 |
| `docs/guide/`、`docs/plugins/` | 用户指南与插件开发文档 |
| `tooling/` | 工具链、APK 构建与诊断脚本 |
| `services/`、`site/`、`infra/` | 配套分享服务、网站与部署材料 |

## 从源码构建（README 摘要，我未实际执行）

面向 **Linux x86_64**，工具链脚本默认安装 JDK 17、Flutter 3.47.7 与 Android SDK/NDK，App 要求 Dart `^3.13.5`：

```bash
git clone https://github.com/ccch1mneyyy/dsh-go.git
cd dsh-go
bash tooling/setup-toolchain.sh
source tooling/env.sh
bash runtime/build-runtime.sh arm64-v8a
DSH_BETA_TOKEN_FILE= bash tooling/build-apk.sh -f arm64 --keep-daemon
```

产物在 `app/build/app/outputs/flutter-apk/app-arm64-release.apk`。未配置 `app/android/key.properties` 时本地 release 构建用 debug 签名，无法覆盖不同签名的官方包。

## 许可：AGPL-3.0 的边界要看两层

- **主仓库是 AGPL-3.0**，这是**强传染性**许可证，且带"网络服务"条款——AGPL 的已知特点是：即使不分发二进制、只通过网络对外提供服务，也可能触发源码披露义务。想 fork 改造后对外提供，需要先做许可层面的确认，**本条为一般性提醒，我没有做法律分析**。
- **例外范围要记牢**：`sdk/` 与 `examples/` 是 MIT；但**品牌标识、Deepy 与鲸娘两位吉祥物、`docs/assets/` 里的展示素材都不在授权范围内**（`ASSETS.md` 原文：版权归 ccch1mneyyy（DSH-TUI）所有、保留所有权利；再发布衍生作品时须换成自己的名称与素材）。
- `ASSETS.md` 还单独说明：`app/assets/whale/` 里有一张是 **DeepSeek 官方图标，版权与商标归 DeepSeek**，仅用于表明本应用连接的是 DeepSeek 服务，同样不在 AGPL 授权范围内。
- README 里引用了 `LICENSE` 第 15–17 条作为无担保与责任限制的依据，与 AGPL 的条款结构相符。

## 使用前需要注意

- **手机操控是系统级权限，风险最高的一块**：读屏幕、点按、输入都要靠无障碍等服务实现；"不打扰模式"还要求 Android 14 及以上加 Shizuku 或自连 ADB。README 自己提示"放宽应用规则或解除限制会减少保护""不在存有支付或银行信息的设备上解除限制"。这是它能力最强也最需要谨慎的功能。
- **数据确实会离开设备**：模型请求发给你配置的提供方；**开启手机操控后，任务需要的屏幕文字可能发给模型，支持看图的模型也可能收到截图**。只连远程主机时，数据落在你的主机侧。
- **匿名使用统计默认开启**：README 写明可在首次引导或「设置 → 隐私与分享」关闭，具体采集范围在其 FAQ 里——该 FAQ 我只看到被引用，未读到全文，**采集字段未核验**。
- **分享链接是"拿到即可读"**：端到端加密意味着服务器看不到内容，但**获得完整链接的人可以读取并保存**内容。分享前务必看预览。
- **上游是 developer preview**：它基于 [deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness)（MIT），而该项目官方自述处于 developer preview、会有破坏性变更，且其 `pushed_at` 停在 2026-08-13（GitHub API，2026-11-03 复核）。**上游近三个月未动、下游客户端 9 天冲到 6 千多 Star**，这个反差值得留意：客户端能否长期跟进取决于作者个人投入。
- **反馈渠道集中在社群**：论坛 `forum.dshtui.com`、QQ 群、微信群；README 中也有"Star 点得越多，我适配得越快"的表述。这类推广性话术不影响功能判断，但看宣传口径时要心里有数。
- **仓库体积 399 MB**：对 Flutter 应用偏大，我没定位到来源（可能是 `runtime/`、`docs/assets/` 或 `market/` 之一），若要克隆请先确认空间。
- 我未下载 APK、未安装、未构建，功能与稳定性全部来自 README 自述与仓库结构，**均未实测**。

## 什么时候会再看

- 想在手机上跑 dsh、或想用手机遥控电脑上的 dsh 时。
- 需要参考"把 Linux 运行时打包进 Android App"（PRoot + 运行时桥接）这条工程路线时。
- 需要研究插件 SDK 如何同时服务原生界面与第三方插件作者时。

## 相关笔记

- **它服务的上游框架**：[deepseek-harness：DeepSeek 开源的 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)——本篇是其第三方 Android 客户端；那篇记录的"developer preview、会有破坏性变更"直接影响本客户端能稳定跟进的前提。
- **生态里的插件市场**：[dsh-plugins-store：DSH 插件市场](dsh-plugins-store-DSH插件市场.md)——那是社区插件目录与验证流水线，本篇提供插件在手机端运行的原生界面与 SDK；一个管"有哪些插件"，一个管"插件在手机上怎么跑"。
- **同为"需要系统级权限的安卓应用"**：[aerial-tv：给国产电视的 tvOS 风格桌面](aerial-tv-给国产电视的tvOS风格桌面.md)——那是靠无障碍服务接管电视默认桌面（另需通知使用权与 WRITE_SECURE_SETTINGS），本篇是靠无障碍 / Shizuku / ADB 做手机操控；两篇都该先看清"权限范围 + 数据流向"。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/ccch1mneyyy/dsh-Go
- 素材与品牌授权范围：https://github.com/ccch1mneyyy/dsh-Go/blob/main/ASSETS.md
- 使用指南：https://github.com/ccch1mneyyy/dsh-Go/blob/main/docs/guide/README.md
- 官网下载页（README 提供）：https://go.dshtui.com/download
- 上游框架：https://github.com/deepseek-ai/deepseek-harness
- 远程方案（李国宝）：https://github.com/liguobao/ds-harness-remote
- 元数据（Star / Fork / 订阅数 / 体积 / 分支 / 许可证）：GitHub REST API，2026-11-03
- 活跃度核验：`commits?per_page=3` 接口，2026-11-03

以上信息核验于 2026-11-03。该项目创建仅 9 天、迭代极快，Star 数、体积与功能边界都会迅速变化；未安装使用，实际效果、权限行为与稳定性均未验证。
