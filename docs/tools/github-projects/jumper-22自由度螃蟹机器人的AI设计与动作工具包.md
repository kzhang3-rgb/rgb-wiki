# jumper：22 自由度螃蟹机器人的 AI 外观 / 动作 / 场景工具包

> 一句话定位（README 自述）：**"Jumper is a 22-DoF crab robot."**——这个仓库是 **Jumper 螃蟹机器人的 AI 工具包**（"Jumper's AI toolkit for appearance, motion, and scene creation"），主打**在 AI 编码助手里打开仓库、用一句话生成外观 / 训练动作 / 创建场景**（README 三种示例分别对应"设计外观导出 `.skin`""训练步态打包 `.app`""创建场景导出 `.map`"）。最值得记的三点：① 面向的是**实体多足（螃蟹）机器人**，训练栈建立在 **mjlab / rsl_rl / MuJoCo / MuJoCo Warp** 之上；② **外观与场景在配套仓库 `jumper-design`**，本仓库是**动作训练与打包主入口**；③ 许可为 **Apache-2.0，但明写"生成的产物不自动继承本仓库许可"**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（70 行）、`LICENSE`（Apache-2.0 全文 202 行）、文件树与元数据；**未 clone、未安装依赖、未在 AI 助手中执行任何提示、未训练任何动作、未渲染仿真、未下载任何 `.skin` / `.app` / `.map`**）
- 适用范围：面向**有 Jumper 硬件、或想复现其训练流程的人**；训练与打包依赖 **Python 训练栈 + GPU**（文档给出 Colab 路径）；外观 / 场景制作在配套仓库 `jumper-design` 内用 AI 助手完成

## 核心结论

1. **明确定位**（README 自述）：这是 **22 自由度螃蟹机器人的 AI 工具包**，覆盖**外观（appearance）、动作（motion）、场景（scene）**三块；README 用三条"一句话"提示演示用法，产物分别是 `.skin`（外观）、`.app`（动作包）、`.map`（场景）。
2. **训练栈是公开的机器人学习套件**（README）：`mjlab`（MuJoCo 系仿真训练框架）、`rsl_rl`（leggedrobotics 的强化学习库）、`MuJoCo`、`MuJoCo Warp`（NVIDIA Warp 加速）。
3. **外观与场景属于另一个仓库**：README 明写外观 / 场景创建看配套仓库 **`jumper-design`**（助手读它的 `AGENTS.md` 并用其工具）；本仓库 `docs/media/` 里的设计图、演示 GIF **图源来自 jumper-design**（`docs/media/DESIGN_SOURCES.md` 与 `NOTICE` 记录归属）。
4. **许可 = Apache-2.0，但有边界**（`LICENSE` 与 README）：仓库自有材料（"Maintainer-owned project materials"）走 **Apache-2.0**；**第三方材料仍归各方条款**；**"生成的产物不自动继承本仓库许可"**（原文 "generated outputs do not automatically inherit this repository's license"）。
5. **本仓库的文档入口**（README "Where to find things"）：`docs/TUTORIAL.md`（动作训练 / 回放 / 策略导出）、`docs/COLAB.md`（Colab GPU 训练、断点续训、ONNX 导出、MP4 仿真回放）、`deploy/BUNDLE.md`（把训练好的动作与控制器打包成 `.app`，前置条件见 `deploy/README.md`）、`docs/PROJECT_GUIDE.md`（安装、当前能力与更多文档）。

**个人判断（未验证）**：这个仓库把机器人开发重写成"**可以被 AI 助手代跑的一段工程**"——外观、动作、场景各对应一个"一句话 → 一个产物"的循环，并把**外观 / 场景从训练仓库里拆出去**（`jumper-design`），让本仓库专注动作训练与打包。**价值取决于你是否拥有或打算复现这台螃蟹机器人**；没有硬件时，它更像一个"多足机器人仿真训练流水线"的参考样本。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [KingKongRobotics/jumper](https://github.com/KingKongRobotics/jumper) |
| 描述 | Jumper——22 自由度螃蟹机器人的 AI 工具包（外观 / 动作 / 场景）；README 自述 "Jumper is a 22-DoF crab robot." |
| 许可证 | **Apache-2.0**（`LICENSE` 全文 202 行；README 写 "Maintainer-owned project materials are licensed under Apache-2.0"）；`NOTICE` 记录第三方归属 |
| 主语言 | 未取到（未核验；工具包以训练脚本与文档为主） |
| 关注度 | **★3,073 / Fork 344 / Watcher 26**（GitHub 页面值，2026-11-13）；开放 Issue **7**、贡献者 **4** |
| 维护状态 | **2026-09-28 创建**（核验时约 1.5 个月）；页面 `updatedAt` **2026-11-04**、ungh `pushedAt` **2026-10-30**；默认分支 `main`；**0 Release** |
| 体积 | `main` 分支 **951 个条目**；根目录含 `LICENSE`、`NOTICE`、`README.md` 与中文版 `README.zh.md`，另有 `docs/`、`deploy/` 等 |
| 配套仓库 | [KingKongRobotics/jumper-design](https://github.com/KingKongRobotics/jumper-design)（外观与场景创建，含 `library/skins/`、`library/maps/`、`AGENTS.md`） |
| 活动页 | "Get a free Jumper!" <https://beunlimited.me/zh/events/crab-robot-challenge-2026> |

## 能力结构

### 1. 三种"一句话"工作流（README）

| 方向 | 示例提示（README 摘要） | 产物 |
| --- | --- | --- |
| 外观 | Design a warm sand ranger appearance for Jumper with coordinated body and limb colors, then export a `.skin`. | `.skin`（皮肤） |
| 动作 | Train a stable tripod gait for Jumper, replay and evaluate the result, then package it as an `.app`. | `.app`（动作包） |
| 场景 | Create a park pump-track scene for Jumper with rolling terrain, trees and benches, then export a `.map`. | `.map`（地图） |

README 展示了示例外观（暖沙游侠 / 银色装甲 / 拉斐尔龟）、动作演示（走 / 姿态 / 挥手 / 跳舞 / 跳跃 / 抓取，均附"仿真 GIF + 官网展示 GIF"两套）与场景（公园泵道 / 卧室 / 足球场），外观与场景成品链接均指向 `jumper-design` 的 `library/skins/`、`library/maps/`。

### 2. 文档入口（README "Where to find things"）

- `jumper-design`（配套仓库）：外观与场景创建；助手读它的 `AGENTS.md` 并按其工具执行。
- `docs/TUTORIAL.md`：**动作训练、回放与策略导出**。
- `docs/COLAB.md`：**Colab 里的 GPU 训练**——断点备份、恢复、**ONNX 导出**、**MP4 仿真回放**。
- `deploy/BUNDLE.md`：把训练好的动作与其控制器打包成 `.app`；构建前置见 `deploy/README.md`。
- `docs/PROJECT_GUIDE.md`：安装、**当前能力**与更多文档；`docs/HARDWARE.md`：硬件说明（README 顶部 "View hardware →"）。

### 3. 训练栈与产物格式

- 训练建立在 **mjlab + rsl_rl + MuJoCo + MuJoCo Warp** 上。
- 产物三类：`.skin`（外观）、`.app`（动作 + 控制器打包）、`.map`（场景）；README 另提到动作导出 **ONNX** 与 **MP4 仿真回放**。
- 外观 / 场景素材来自 `jumper-design`；`docs/media/DESIGN_SOURCES.md` 与 `NOTICE` 记录图源与第三方归属。

## 使用前需要注意

- **核心是实体机器人**：README 顶部即 "Jumper is a 22-DoF crab robot."，并链到一个"领取免费 Jumper"的活动页；**没有硬件时，这套流程只能当作仿真训练与设计的参考**。
- **外观 / 场景不在本仓库**：要生成 `.skin` / `.map` 需切到 `jumper-design`（并让其 `AGENTS.md` 生效）。本仓库的示例图是**从那里引用的**，不是在本仓库产出。
- **生成产物的许可要单独判断**：README 明写 "generated outputs do not automatically inherit this repository's license"，**别默认产出的 `.skin` / `.app` / `.map` 就是 Apache-2.0**。
- **训练有硬件与人力门槛**：依赖 mjlab / rsl_rl / MuJoCo（+ Warp 加速），Colab 路径便于上手但训练仍需 GPU；README 未在此展开安装细节（指向 `docs/PROJECT_GUIDE.md`）。
- **仓库很新、很薄**：**2026-09-28 创建**、核验时约 1.5 个月，**贡献者仅 4 人、0 Release、7 开放 Issue**；`main` 上 951 个条目，文档与脚本仍在早期。
- **我只读了 README、`LICENSE` 与文件树**：**未 clone、未安装、未训练、未渲染、未下载任何产物**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想给一台**多足 / 四足机器人**搭"仿真训练 → 回放评估 → 打包部署"的流水线，并想看别人怎么把 **mjlab / rsl_rl / MuJoCo** 串起来时。
- 想看"**把外观 / 动作 / 场景都收敛成一句提示 + 一个产物文件（`.skin` / `.app` / `.map`）**"这种面向 AI 助手的设计时。
- 想参考"**训练仓库与设计仓库拆开、用 `AGENTS.md` 把助手引到另一仓库**"的组织方式时。
- 想对照"**AI 接真实硬件 / 生成美术素材**"的其它路线（见下）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"让 AI 助手按提示产出成品"**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——那篇是**让助手生成 2D 精灵图集**的技能包，本篇是**让助手生成机器人外观 / 动作 / 场景**的工具包；都属于"把创作交给助手 + 固定产物格式"的思路。
- **同为"AI 接实体硬件"**：[xiaozhi-esp32：基于 MCP 的 ESP32 语音聊天机器人](xiaozhi-esp32-基于MCP的ESP32语音聊天机器人.md)——那篇把大模型接进 **ESP32 固件**（设备端 + 云端 MCP），本篇把 AI 接进**螃蟹机器人的仿真与设计**；一个是"聊天实体固件"，一个是"机器人训练工具包"。
- **同为"把仓库变成助手可用的东西"**：[github-skill-forge：把 GitHub 仓库转成 Trae 技能](github-skill-forge-把GitHub仓库转成Trae技能.md)——那篇是**把任意仓库转成技能的通用方法**，本篇是**作者自带的、面向具体机器人的技能式仓库**。
- **同为"仿真接外部系统"**：[stonkfly：果蝇连接组交易实验](stonkfly-果蝇连接组交易实验.md)——那篇用**真实果蝇连接组仿真**驱动外部交易实验，本篇用 **MuJoCo 仿真**训练机器人动作；可对照两者"仿真 → 产物"的边界写法。

- **同为"AI / 软件接进实体硬件"**：[esp32-usb-to-airprint：用一块 ESP32-S3 给旧 USB 打印机加上 AirPrint 与 AirScan](esp32-usb-to-airprint-用ESP32-S3给旧打印机加AirPrint与AirScan.md)——那是打印机协议翻译固件（ESP32-S3），本篇是机器人设计与动作工具包（ESP32 作为运行硬件之一）；可对照"硬件上跑什么"的两类做法。

## 参考来源

- 仓库主页与 README（70 行；核验日期 2026-11-13）：https://github.com/KingKongRobotics/jumper
- 许可证：`LICENSE`（Apache-2.0 全文 202 行）；`NOTICE`（第三方归属）；`docs/media/DESIGN_SOURCES.md`（图源）
- 配套仓库：`KingKongRobotics/jumper-design`（`AGENTS.md`、`library/skins/`、`library/maps/`）
- 训练栈（README 引用）：`mujocolab/mjlab`、`leggedrobotics/rsl_rl`、`google-deepmind/mujoco`、`google-deepmind/mujoco_warp`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 文件数 / 贡献者与 Issue）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13）

以上信息核验于 2026-11-13。**未 clone、未安装依赖、未执行任何 AI 提示、未训练任何动作、未渲染仿真、未下载任何 `.skin` / `.app` / `.map`**；能力与流程描述均来自 README 与 `LICENSE`，**未逐一验证**。
