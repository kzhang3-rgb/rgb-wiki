# xiaozhi-esp32：基于 MCP 的 ESP32 语音聊天机器人

> 记录一个活跃度极高的开源 ESP32 语音助手固件项目：了解它的开发门槛（ESP-IDF 6.x）、硬件覆盖面，以及"设备端 + 云端 MCP"这条把大模型接到真实硬件的路径。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README（英文版）、元数据与 Release 列表，未 clone、未编译、未烧录任何固件）
- 适用范围：ESP32 系 AI 硬件 / 语音交互固件的选型与上手；了解"用 MCP 控制 IoT 设备"的落地形态

## 核心结论

`78/xiaozhi-esp32` 是一个**开源的 ESP32 语音聊天机器人固件**，README 自述定位为"语音交互入口 + 通过 MCP 协议实现多端控制"，底层调用 Qwen / DeepSeek 等大模型能力。它是目前中文 AI 硬件圈最活跃的开源项目之一：**32,615 Star / 7,695 Fork / 631 个开放 Issue**，30 位贡献者，自 2024-08-31 创建以来持续高频更新（最近推送 2026-11-04）。

四点需要先看清：

1. **开发门槛已被抬高**。项目**要求 ESP-IDF v6.0.1 或更高，5.x 不再支持**；README 明确建议**用 `release/v6.1` 分支而不是 `v6.1` 发布 tag**——用该 tag 会导致 ESP32-C3 的 Wi-Fi 初始化失败（见 [issue #2344](https://github.com/78/xiaozhi-esp32/issues/2344)）。
2. **硬件覆盖极广**。README 列出**138 个板级目录、171 个 release 变体**，支持 ESP32 / C3 / C5 / C6 / S3 / P4 等芯片平台。
3. **有"零开发环境"的入门路径**。新手可直接烧录官方发行版固件，默认连接官方服务器 [`xiaozhi.me`](https://xiaozhi.me)，个人用户注册后**可免费使用 Qwen 实时模型**；要自定义则走源码开发。
4. **开源且允许商用**。MIT 许可，README 明写"允许任何人免费使用，包括用于商业用途"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [78/xiaozhi-esp32](https://github.com/78/xiaozhi-esp32) |
| 描述 | An MCP-based chatbot｜一个基于 MCP 的聊天机器人 |
| 许可证 | **MIT**（README 明写允许商用） |
| 语言 | **C++ 74.3%**（另有 C / Python 等） |
| 关注度 | **32,615 Star、7,695 Fork、254 关注、631 个开放 Issue**（核验于 2026-11-05） |
| 维护状态 | **2024-08-31 创建**，最近推送 **2026-11-04（极活跃）** |
| 分支 | 默认分支 `main` |
| 发行版 | **68 个 Release / 76 个 tag**，最新 **v2.5.2**（2026-10-11） |
| 贡献者 | **30 人**（含作者 `78`、`goodtft`、`Y1hsiaochunnn` 等） |
| topics | `chatbot`、`esp32`、`mcp` |
| 生态 | 配套 [xiaozhi-assets-generator](https://github.com/78/xiaozhi-assets-generator)（唤醒词 / 字体 / 表情 / 背景素材生成） |

## 用法要点

- **功能面**：离线语音唤醒（基于 [ESP-SR](https://github.com/espressif/esp-sr)，唤醒词可自定义）、WebSocket 与 MQTT+UDP 两种传输、Opus 音频流 + 常规 ASR→LLM→TTS 管线与 Realtime 端到端语音模型（支持 AEC 的硬件可全双工）、声纹识别（[3D-Speaker](https://github.com/modelscope/3D-Speaker)）、OLED/LCD 表情显示与摄像头视觉输入、电池与电源管理、Wi-Fi / 有线以太网 / USB RNDIS / Cat.1 4G 多种联网、**39 种界面语言**。
- **MCP 两层**：**设备端 MCP**（音箱、LED、舵机、GPIO 等设备控制）+ **云端 MCP**（智能家居、PC 桌面操作、知识搜索、邮件等扩展）。协议细节见仓库内 `docs/mcp-usage.md` 与 `docs/mcp-protocol.md`。
- **上手路径**：新手走"免环境固件烧录"（飞书文档教程）→ 连 `xiaozhi.me` 用；开发者按 `docs/custom-board.md` 自定义板卡，用 Cursor / VSCode + ESP-IDF 插件开发，遵循 Google C++ 代码风格。
- **第三方生态**：README 列出多个第三方服务端实现（Python / Java / Golang）与客户端（Python / Android / Linux / 蓝牙芯片 / QuecPython），可脱离官方服务器自建。

## 使用前需要注意

- **ESP-IDF 版本是最大的踩坑点**：必须 v6.0.1+，5.x 直接不支持；换 SDK 后要在 ESP-IDF 目录跑 `git submodule update --init --recursive`、`source export.sh`，并在本项目里 `idf.py fullclean` 再重新编译。
- **别用 `v6.1` tag，要用 `release/v6.1` 分支**：该 tag 在 ESP32-C3 上有已知的 Wi-Fi 初始化失败问题。
- **631 个开放 Issue**：项目体量大、板卡多，遇到问题先搜 Issue；自己加的板卡不一定有官方维护。
- **默认依赖官方服务器**：新手路径默认连 `xiaozhi.me`，若在意数据或想自托管，需换用第三方服务端。
- 我**未 clone、未编译、未烧录任何固件**：以上为 README、元数据与 Release 列表信息；"可直接烧录"等为 README 自述，未实测。

## 什么时候会再看

- 想做 ESP32 语音助手 / AI 玩具 / 桌面机器人，需要选一块被社区验证过的固件与板卡时。
- 想了解"**用 MCP 把大模型接到真实硬件**"的落地形态（设备端 + 云端两层工具）时。
- 想对照"**IoT / 硬件固件类开源项目**"如何组织多板卡（138 个板级目录 / 171 变体）与多语言界面时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **MCP 生态**：[servers：MCP 官方参考服务器合集](servers-MCP官方参考服务器合集.md)（服务器侧参考实现）、[java-sdk：MCP 官方 Java SDK](java-sdk-MCP官方JavaSDK.md)（自建服务端）——本篇是**把 MCP 用在设备端**的一端，可与"官方服务器清单 / SDK"对照"服务端 ↔ 设备端"两种形态。
- **另一个把 AI 接入实体/端侧的项目**：[NamiAgent](NamiAgent-从零手写Agent的中文可运行教程.md)（从零手写 Agent，可接入聊天软件）、[bb-browser](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md)（把浏览器登录态当 API）——可对照"AI 能力接到不同宿主（硬件 / 浏览器 / 聊天软件）"的差异。
- **同为"把语音与 MCP 放在一起"，方向相反**：[voicebox：本地优先的开源 AI 语音工作室](voicebox-本地优先的开源AI语音工作室.md)——那是**软件端**：Tauri 桌面 App 自带 MCP server，是"**机器对你说**"（`voicebox_speak` 克隆音色发声，且坚持绝不静默）；本篇是**硬件端**：ESP32 音箱 / 麦克风 + 设备端 MCP，是"**人对着机器说话**"。**两篇合看正好覆盖语音与 Agent 结合的"输出端"与"输入端"。**

- **同为"ESP32 上的开源固件项目"**：[esp32-usb-to-airprint：用一块 ESP32-S3 给旧 USB 打印机加上 AirPrint 与 AirScan](esp32-usb-to-airprint-用ESP32-S3给旧打印机加AirPrint与AirScan.md)——那是把打印 / 扫描协议接进 ESP32 固件，本篇是把大模型语音能力接进 ESP32；同是"给一块 MCU 写完整固件"，业务方向完全不同。

## 参考来源

- 项目仓库与 README（英文）：<https://github.com/78/xiaozhi-esp32>
- 官方服务器：<https://xiaozhi.me>
- MCP 文档：`docs/mcp-usage.md`、`docs/mcp-protocol.md`；自定义板卡：`docs/custom-board.md`
- 版本坑位参考：issue <https://github.com/78/xiaozhi-esp32/issues/2344>
- 元数据（Star / Fork / Release / 贡献者 / topics）：ungh.cc 镜像、GitHub 页面内嵌 JSON 与 shields.io，2026-11-05

以上信息核验于 2026-11-05。本次 GitHub API 匿名配额已用尽，未能取到 `size`（仓库体积）；仅阅读 README 与元数据，未 clone、未编译、未烧录。
