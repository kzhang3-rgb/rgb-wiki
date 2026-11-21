# dsh-pixluna：把 PixLuna 图片能力移植成 DSH 插件的图像工具

> 记录一个**把 PixLuna（一个 Koishi 平台的图片获取插件）的能力移植为 DeepSeek Harness（DSH）Cordis 插件**的小仓库（`PixLunaLab/dsh-pixluna`，描述 "dsh-plugin-pixluna \| 让 DSH 自己看涩图！"）：它**不注册聊天命令，而是把原命令改造成模型可调用的工具**（`pixluna_get` / `pixluna_get_pixiv` / `pixluna_sources`），配置项落在 DSH 的 settings 与 credentials 里。三条先记住：**它是原 PixLuna 的 DSH 移植版、许可沿用 MPL-2.0**；**默认关闭 R18、默认图源 `lolicon`**；**仓库很旧（2026-08-15 后无新提交）且仅 5 Star**。未安装、未调用、未审计源码。

- 状态：草稿
- 最近核验：2026-11-17（阅读 `main` 分支 README 全文（68 行）、`LICENSE`、文件清单（22 项）与 ungh 镜像 / shields 徽章；**未 clone、未 `yarn build`、未装入任何 DSH profile、未调用任何工具、未审计源码**）
- 适用范围：**DeepSeek Harness（DSH）的 Cordis 插件**——README 通过 `dsh plugin --profile <name> add dsh-pixluna` 装入目标 profile；插件声明 `inject = ['tools']`，在 `@deepseek-ai/dsh-tools` 服务可用后激活

## 核心结论

**一句话定位（README 首行）**：**"让 DSH 自己看涩图！"**——把 [PixLuna](https://github.com/PixLunaLab/pixluna) 的图片获取能力**移植为 DSH Cordis 插件**。

**① 它把"命令"改造成"工具"。** README 明确："插件不再注册聊天命令，而是注册模型可调用的工具；原命令选项均转换为具名工具参数。" 映射表：

| 原 PixLuna command | DSH tool | 参数 |
| --- | --- | --- |
| `pixluna` | `pixluna_get` | `number`, `source`, `tag`, `r18` |
| `pixluna.get.pixiv <pid>` | `pixluna_get_pixiv` | `pid`, `pages`, `all` |
| `pixluna.source` | `pixluna_sources` | 无 |

**② 配置分两处：常规项进 settings、凭据进 credentials。** 插件把 `Config` 注册为 `pixluna` 用户设置命名空间，并向 DSH 自带的"设置 → 插件 → 插件配置"注册配置卡片；**常规字段写入 DSH settings，Pixiv Cookie 与各图源 `keyPairs` 参照模型 API Key 的做法写入 DSH credentials，只向浏览器返回"已配置 / 未配置"状态**，修改实时作用于后续工具调用。

**③ 安装与 patch 语义。** 开发目录可直接作为带 `dsh.bundle` 清单的本地 profile layer 安装：`dsh plugin --profile web add dsh-pixluna`；`dsh plugin` 会把本包加入 profile 的 `dsh.profile.bundles` 并自动应用包内 `cordis.patch.yml`。README 特别提醒：**DSH patch 对 `config` 是整项替换而非深度合并**——要覆盖配置得在该 profile 的用户 `cordis.patch.yml` 里按**稳定 row id `pixluna` 覆盖整份配置**。

**④ 主要配置项（README 原列）。** `defaultSourceProvider`（默认图源数组，默认 `lolicon`）、`isR18` / `r18P`（是否允许 R18 及随机请求概率，**默认关闭 R18**）、`excludeAI`、`isProxy` / `proxyHost`、`baseUrl`（Pixiv 图片反代域名，默认 `i.pixiv.re`）、`maxConcurrency`（1–10）、`apiDelay`、`pixiv.phpSESSID`、各 booru 的 `keyPairs`。

**⑤ 与 Koishi 原项目的差异（README 列出）。** 移除 Koishi `Context.http` / 消息发送 / @ 用户 / 转发消息 / 自动撤回 / Koishi `h` 元素依赖；改用 `undici` 发起可取消请求并转发 `ToolRunContext.signal`；**图片处理（翻转、混淆、压缩）未带入 DSH 工具输出——工具返回远程图片 URL，避免把大体积二进制数据写入会话日志**；**R18 请求须同时满足插件级 `isR18=true` 与工具参数 / 概率选择**。

**⑥ 关注度与维护状态。** **5 Star / 0 Fork / 1 关注**（ungh 快照，2026-11-17；shields 同期 5 / 0、0 开放 Issue、贡献者 1）；**2026-08-13 创建**、pushed **2026-08-15**（核验时已**约三个月无新提交**）；`main`；**0 Release**；**22 个文件**。**主语言 shields 顶层占比 98.8%（TypeScript 工程）**；根目录含 `.npmignore` / `.oxfmtrc.json` / `.oxlintrc.json` / `.yarnrc.yml` / `cordis.patch.yml` / `rolldown.config.js` / `tsconfig.json` / `yarn.lock`（约 45 KB）等。

**⑦ 许可证沿用原项目。** README 末句："许可证沿用原 PixLuna 的 **MPL-2.0**。"（`LICENSE` 16,725 字节与 MPL-2.0 体量相符。）

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [PixLunaLab/dsh-pixluna](https://github.com/PixLunaLab/dsh-pixluna) |
| 描述（API） | "dsh-plugin-pixluna \| 让 DSH 自己看涩图！" |
| 许可证 | **MPL-2.0**（`LICENSE` 16,725 字节；README 自述"沿用原 PixLuna 的 MPL-2.0"） |
| 主语言 | shields 顶层语言 **98.8%**（TypeScript 工程） |
| 关注度 | **5 Star / 0 Fork / 1 关注**（ungh，2026-11-17；shields 同期 5 / 0）；**0 开放 Issue、贡献者 1 人**（shields） |
| 创建 / 最近推送 | **2026-08-13 创建**；`pushedAt` **2026-08-15**（核验时约三个月无新提交）、`updatedAt` 2026-11-17 |
| 默认分支 / Release / 文件数 | `main`；**0 Release**；**22 个文件** |
| 根目录可见文件 | `README.md`（3,147 B）、`LICENSE`（16,725 B）、`cordis.patch.yml`（196 B）、`package.json`（2,998 B）、`rolldown.config.js`、`tsconfig.json`、`yarn.lock`（45,140 B）、`.gitignore` / `.npmignore` / `.oxfmtrc.json` / `.oxlintrc.json` / `.yarnrc.yml` |
| 宿主 / 上游 | 宿主为 **DSH（DeepSeek Harness）**、插件框架为 **Cordis**；上游为 [PixLunaLab/pixluna](https://github.com/PixLunaLab/pixluna)（**Koishi 平台**插件） |

## 使用前需要注意

- **① 它是"移植版"，上游与宿主都不是本仓库。** 能力来自 **PixLuna**（Koishi 插件），宿主是 **DSH + Cordis**；本篇只记这一层移植。**上游 PixLuna 的维护 / 许可 / 行为、以及 DSH 的破坏性变更**都会直接影响它——README 也提醒要按 rc 版本适配。
- **② 默认关闭 R18，但这是一个"内容获取"插件。** README 明写默认图源 `lolicon`、`isR18: false`；R18 需**插件级与工具参数双重满足**。**本笔记只记录该项目存在、工具与配置结构，不对其抓取的任何内容作评价或推荐**；使用者须自行确认**图源服务条款、版权与所在地法律**。
- **③ 凭据落到 DSH credentials。** **Pixiv Cookie（`phpSESSID`）与各 booru 图源 `keyPairs`** 由 DSH 按模型 API Key 的做法托管（只回"已配置 / 未配置"）。**这些凭据的存储、权限与备份要自己管好**。
- **④ 配置是"整项替换"而不是"深度合并"。** 覆盖 `config` 时须在用户 `cordis.patch.yml` 里按 row id `pixluna` **整份覆盖**，否则容易改错——README 明确标注了这一点。
- **⑤ 极旧、极小、单一贡献者。** **2026-08-13 创建 + pushed 停在 2026-08-15 + 0 Release + 5 Star + 1 位贡献者**——**别把它当"久经检验"的插件**；它更像是一个"移植能不能跑通"的个人实验。
- **⑥ 我未做任何验证。** 仅阅读 README 全文、`LICENSE`、文件清单与元数据；**未 clone、未安装到任何 DSH profile、未调用任何工具、未接任何图源凭据、未审计源码 / 未核验 `cordis.patch.yml` 内容**；工具映射、配置项与差异均来自 README 自述。

## 什么时候会再看

- 想研究"**把一个平台（Koishi）的插件移植到另一个宿主（DSH / Cordis）**"时，命令 → 工具的参数映射、依赖裁剪（去掉 `Context.http` / 消息元素）、以及 settings / credentials 的存放分工。
- 想参考"**Agent 插件如何把大体积二进制（图片）换成远程 URL**"以避免污染会话日志的取舍。
- 想对照 DSH 生态的其它插件 / 目录（本篇是"内容获取"类；对照 [dsh-plugins-store](dsh-plugins-store-DSH插件市场.md) 的目录、[dsh-purge](dsh-purge-给DeepSeek-Harness打补丁的破甲与红队插件.md) 的激进改宿主路线）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **它就是给 DSH 写的插件，宿主见本篇**：[DeepSeek Harness：DeepSeek 开源的 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)——那是**官方自己做的、插件化扩展的 Agent 框架**（底层框架 Cordis）；本篇是**跑在它之上的一枚小插件**（`inject = ['tools']`）。看本篇要理解 DSH 的 profile / bundle / patch 语义，先看宿主那篇。
- **同属"DSH 生态的插件"，形态相反**：[dsh-plugins-store：DSH 插件市场](dsh-plugins-store-DSH插件市场.md)——那是**"有哪些插件"的目录**（收集、分类、隔离验证、定时同步），本篇是这样目录里可能被收录的**某个具体插件**（内容获取类）。一个回答"有什么"，一个回答"这一枚怎么做 / 能做什么"。
- **同属"DSH 插件"，激进程度不同**：[dsh-purge：给 DeepSeek Harness 打补丁的破甲与红队插件](dsh-purge-给DeepSeek-Harness打补丁的破甲与红队插件.md)——那篇**直接备份并替换 `@deepseek-ai/*` 包内文件**、按会话注入提示词；本篇走 **Cordis 标准插件形态**（`dsh.bundle` + `cordis.patch.yml`）挂在宿主外侧。可对照"**改宿主文件**"与"**按插件协议挂载**"两条路线的差异与风险。

## 参考来源

- 仓库主页与 README（核验日期 2026-11-17）：https://github.com/PixLunaLab/dsh-pixluna
- 许可证 `LICENSE`（MPL-2.0，16,725 字节，README 自述沿用原 PixLuna 许可）：https://github.com/PixLunaLab/dsh-pixluna/blob/main/LICENSE
- 上游原项目（Koishi）：https://github.com/PixLunaLab/pixluna
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-17

以上信息核验于 2026-11-17：Star / 版本可能变化（该仓库核验时已约三个月无推送）。**未 clone、未安装、未调用任何工具、未接任何图源凭据、未审计源码**；**工具映射、配置项、与上游差异均来自 README 自述**；**本笔记不对该插件抓取的任何内容作评价或推荐。**
