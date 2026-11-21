# Jev 聊天助手：先判断、再写字的聊天辅助决策应用

> 记录一个**先判断对方意图与风险、再起草回复**的聊天辅助应用（`jev-chat/jev-chat-jarvis`，描述 "The chat decision assistant: before you reply, Jev reads the chat, judges intent and risk, and drafts replies you fill in with one tap. You press send."）：它**只用系统无障碍服务读屏**（不 hook、不注入、不走对方 App 的接口），把"发不发"的决定权**始终留给用户**——程序只把回复填进输入框，**从不自动发送、不碰转账 / 红包 / 收款**。三条先记住：**它读的是你屏幕上正在显示的对话**；**判断 / 回复 / 视觉三路接口各自配置，作者不运营中转服务器**；**在第三方 App 里使用可能违反其用户协议、有封号风险**。未安装、未运行、未扫码授权。

- 状态：草稿
- 最近核验：2026-11-17（阅读 `cn/README.md` 国内版全文（219 行）、`LICENSE`、文件清单（263 项）、Release 列表（2 条）与 ungh 镜像 / shields 徽章；**未下载 APK、未安装、未开启无障碍 / 悬浮窗、未接任何模型服务、未在 QQ / 飞书 / X / WhatsApp 中实际使用、未审计源码**）
- 适用范围：**Android 端（国内版 / 国际版）**，README 称 Android 11+ 且仅 **ARM64（`arm64-v8a`）**；另有两个**独立项目**分别提供 macOS 版（`jev-chat/jev-chat-jarvis-mac`）与 Windows 版（`jev-chat/jev-chat-windows`），README 标注"网页版规划中、尚无"

## 核心结论

**一句话定位（API 描述）**：`The chat decision assistant: before you reply, Jev reads the chat, judges intent and risk, and drafts replies you fill in with one tap. You press send.` 中文自述"聊天辅助决策工具：先看懂对方再回复，发不发由你"。

**① 它的流程是"先判断，再写字"。** 大多数工具直接让模型编一句回复；Jev **先让判断模型给出**：对方真实意图、危险等级（1–9）、对方要什么、该不该马上回、最佳动作（约 1 秒、带把握度）；随后生成模型起草 **3 条口语化候选**，判断模型按"最合适"**排序并给出占比**。

**② 它不碰对方的 App。** README 明确：**不 hook、不改包、不走任何 App 的接口或账号、不读数据库**，只用**系统无障碍服务**读"屏幕上正在显示的对话"（和读屏软件的工作方式一样）；**不需要 root，也不用装任何模块**。

**③ 发送权始终在用户手里。** 程序只把选中的回复**填进输入框**（`ACTION_SET_TEXT`，失败退到剪贴板 + `ACTION_PASTE`），**任何情况下都不发送**，且**不碰转账 / 红包 / 收款**。

**④ 一套内核，多平台（靠"适配器"扩展）。** 平台支持表：**QQ Android**（全链路，读无障碍树节点，9.3.50 实测）、**X / Twitter 私信**（全链路，解析 Compose 节点 `content-desc`）、**飞书 / Lark**（OCR 兜底，正文自绘不在无障碍树里，对每个气泡矩形做 **ML Kit 离线 OCR**）、**WhatsApp**（一对一，读取规则来自社区 PR）、**其它未适配 App**（悬浮窗菜单手动"截屏识别一次"整屏 OCR）。**新增一个 App 只需写一个几十行的适配器**（`capture/ChatAppAdapter.kt` + 在 `ChatCaptureService.kt` 的 `adapters` 加一行）。

**⑤ 接口自己配，作者不经手请求。** **判断 / 回复 / 视觉三路**分别可填地址、密钥、模型；内置预设含 OpenRouter、博查 Jev、TypeSafe 直连、Vercel、OpenCode Zen、DeepSeek 官方、通义兼容等，每张卡有**独立的一键连通测试**；**只有一把密钥也能用**（回复、视觉留空自动继承判断接口）。README 强调"项目作者不运营中转服务器"，触发分析时聊天文字与启用的背景信息会发给**你配置的模型服务商**。

**⑥ 本地知识库与联系人，且可控。** 知识库（笔记，标签 / 标题命中才带，最多 5 条，支持"常驻"）、联系人档案（姓名 / 别名 / 关系 / 备注）、可选历史（默认关闭，开启后带最近 N 条，默认 30）；数据存 **App 私有目录**，设置里可**一键清空**、"不进日志、不进 git"；**截图只在本机 OCR，不上传**。

**⑦ 关注度与状态。** **8,430 Star / 1,346 Fork / 14 关注**（ungh 快照，2026-11-17；shields 同期 8.5k / 1.4k）；**29 个开放 Issue、贡献者 17 人**（shields）；**2026-09-21 创建**、pushed **2026-11-05**、`updatedAt` 2026-11-17；`main`；**2 个 Release**（`global-v0.1.0` 为 prerelease，2026-10-06；`v1.7` 非 pre，2026-10-05）；**263 个文件**。README 位于**多语言子目录**（`cn/` 国内版、`global/` 国际版、`.github/` 总览与语种版），根目录**没有** README。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [jev-chat/jev-chat-jarvis](https://github.com/jev-chat/jev-chat-jarvis) |
| 描述（API） | "The chat decision assistant: before you reply, Jev reads the chat, judges intent and risk, and drafts replies you fill in with one tap. You press send. Android · Windows · macOS · iOS \| 聊天辅助决策工具：先看懂对方再回复，发不发由你。" |
| 许可证 | **MIT**（`LICENSE` 1,103 字节）+ **`NOTICE`**（1,117 字节）；README 提到姊妹项目与许可说明见总览 |
| 主语言 | shields 顶层语言 **95.9%** |
| 关注度 | **8,430 Star / 1,346 Fork / 14 关注**（ungh，2026-11-17；shields 同期 8.5k / 1.4k）；**29 个开放 Issue、贡献者 17 人**（shields） |
| 创建 / 最近推送 | **2026-09-21 创建**；`pushedAt` **2026-11-05**、`updatedAt` 2026-11-17 |
| 默认分支 / Release / 文件数 | `main`；**2 个 Release**（`global-v0.1.0` prerelease，2026-10-06；`v1.7`，2026-10-05）；**263 个文件** |
| 根目录可见文件 | `.gitignore`（499 B）、`LICENSE`（1,103 B）、`NOTICE`（1,117 B）；**README 在 `cn/` / `global/` / `.github/` 下** |
| 平台（README） | Android（国内版 / 国际版，Android 11+、仅 `arm64-v8a`）；macOS / Windows 为**独立仓库**；网页版规划中 |
| 技术栈（README） | Android 应用 Kotlin（传统 View）；`tools/jev/` 为 Jev 题目集与校准脚手架（Python）；构建 JDK 17 + Android SDK（platform 35） |

## 功能要点（README 自述）

- **判断与候选回复**：判断模型一次给出对方真实意图、危险等级（1–9）、对方要什么、该不该马上回、最佳动作；生成模型起草 3 条候选，判断模型排序并给占比；悬浮窗点一下复制或填入，**任何情况下都不发送**。
- **知识库与联系人**：常驻笔记每次带，其它笔记要标签 / 标题命中才带（最多 5 条）；联系人匹配会话标题（忽略群名尾部人数、首尾空白、大小写）；历史默认关闭，开启后带最近 N 条并去掉屏幕上已显示的部分。
- **接口与模型**：判断 / 回复 / 视觉三路的地址、密钥、模型分别可填；一卡一键连通测试；升级时旧密钥一次性迁移到三卡结构。
- **采集与 OCR**：一个 App 一个适配器、按前台包名分发；无障碍树里没有正文时自动截屏 + ML Kit 中文离线模型识别（**不上传图片、不需要 Google 服务**）；截屏有限频与失败退避，识别时躲开自己的悬浮窗。
- **已知限制（README 列出）**：国产 ROM 后台冻结（小米 / HyperOS 尤为明显）；飞书正文靠 OCR 且"我 / 对方"按已读状态判断、可能判反；X 只按中文界面验过；群聊按一对一分析不准；知识库检索是**标签 / 标题包含匹配**、不做语义检索；OCR 依赖系统放行截屏且只认屏幕上可见部分；ML Kit 中文离线模型让 APK 从约 12 MB 增至约 27 MB。

## 使用前需要注意

- **① ⚠️ 使用可能违反第三方 App 的用户协议。** README 用引用块标注"**使用风险**"：在 QQ、飞书、X、WhatsApp 等第三方 App 里使用本助手，**账号有被限制或封禁的风险**，需自行判断是否使用。**这是功能与风险绑在一起的一体两面**。
- **② 它读的是"屏幕上正在显示的对话"。** 采集靠**系统无障碍服务**（不是 hook / 注入 / 调接口），但这也意味着**当前聊天文字与启用的背景信息会发送给你配置的模型服务商**；作者不运营中转服务器，但**第三方服务商如何处理收到的内容，以其隐私政策为准**。截图只在本机 OCR。
- **③ 权限与保活门槛不低。** README 要求开**无障碍、悬浮窗 / 显示于其他应用上层、自启动 + 省电无限制**；升级到 1.3+ 后需**把无障碍关掉再打开一次**截屏能力才生效；小米 / HyperOS 重装后悬浮窗权限会被重置。国产 ROM 后台冻结可能导致读不到消息。
- **④ 装包与密钥。** release 包在仓库内（`cn/apk/jev-assistant-v1.7-release.apk`）；**装过 debug 包的要先卸载再装 release（签名不同），卸载会清掉密钥与设置**。模型调用费用按用量在对应服务商处结算。
- **⑤ 主训练语言是英文。** README 称 Jev 主训练语言是英文，题目用英文、聊天内容保留中文；建议用自己的真实对话做一批标注校准（`tools/jev/`）。
- **⑥ 我未做任何验证。** 仅阅读国内版 README 全文、`LICENSE` / `NOTICE`、文件清单、Release 列表与元数据；**未下载 APK、未安装、未开权限、未接任何模型服务、未在任何聊天 App 中实际使用、未审计源码**；平台支持、OCR、适配器与隐私边界均为 README 自述。

## 什么时候会再看

- 想研究"**接住对话、先判断再起草、把发送权交给用户**"这类"聊天副驾"的产品流程与提示词分工（判断模型 / 生成模型两段式 + 候选排序）。
- 想参考"**只用系统无障碍服务读屏、不 hook 不改包**"的工程路线，以及"**一个 App 一个适配器 + OCR 兜底**"的扩展结构（QQ 读节点 / X 读 `content-desc` / 飞书 OCR）。
- 想对照"**给人用的聊天辅助**"与"**给机器人用的记忆 / 生活层**"两种把 AI 放进对话的做法（对照 [goutoujunshi](goutoujunshi-先接住情绪再给策略的AI恋爱军师.md) 的恋爱军师、[memory-brain](memory-brain-AstrBot记忆与生活层.md) 的机器人记忆）时。
- 作为"**第三方 App 自动化 / 辅助**"的合规样本，评估其**封号、协议与隐私风险**时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"用 AI 辅助聊天 / 回复"，对象不同**：[goutoujunshi：先接住情绪再给策略的 AI 恋爱军师](goutoujunshi-先接住情绪再给策略的AI恋爱军师.md)——那篇是**把一整个领域（情感 / 关系）编译成的 Codex 技能**（跨学科知识库 + 先接情绪再拆事实的流程），给人写回复做参考；本篇是**装在手机上的应用**、直接读你屏幕上的对话并起草候选回复、发送权留在你手里。一个是"**离线知识 + 方法论**"，一个是"**实时读屏 + 起草**"，可对照两种"AI 帮你想怎么回"的形态。
- **同为"给 AI 补记忆 / 上下文"，落点不同**：[memory-brain：AstrBot 的记忆与生活层](memory-brain-AstrBot记忆与生活层.md)——那篇是给**聊天机器人**挂五类记忆 + 五路检索 + 遗忘治理，让机器人跨会话记住"用户与对话"；本篇是给**人**在回复时带上**本地知识库 + 联系人档案 + 可选历史**（标签 / 标题命中才带、最多 5 条）。一个服务"机器人记人"，一个服务"人怎么回"，可对照两种把记忆装进对话的取舍。

## 参考来源

- 仓库主页与 README（国内版 `cn/README.md`，核验日期 2026-11-17）：https://github.com/jev-chat/jev-chat-jarvis
- 许可证 `LICENSE`（MIT，1,103 字节）与 `NOTICE`（1,117 字节）：https://github.com/jev-chat/jev-chat-jarvis/blob/main/LICENSE
- 姊妹项目：macOS 版 <https://github.com/jev-chat/jev-chat-jarvis-mac> · Windows 版 <https://github.com/jev-chat/jev-chat-windows>
- Release 列表（`v1.7` / `global-v0.1.0`，2026-10）：https://github.com/jev-chat/jev-chat-jarvis/releases
- 元数据（Star / Fork / 关注 / Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数）：ungh.cc 镜像与 shields.io，2026-11-17

以上信息核验于 2026-11-17：Star / Fork 与版本可能变化。**未下载 APK、未安装、未运行、未开权限、未接任何模型服务、未在任何聊天 App 中实际使用、未审计源码**；**平台支持、OCR、适配器与隐私 / 合规边界均来自 README 自述**；**在第三方 App 中使用存在账号被限制或封禁的风险，使用前请自行评估并承担后果。**
