# screenplink：把你的显示器当靶子的激光干射射击游戏

> 它把一台哑光显示器和一只普通 60fps 摄像头变成激光训练靶场：摄像头从侧面拍屏幕，程序算出你的激光打在哪个像素上，在那里打出一个弹孔。**真正值得记的不是"这是个射击小游戏"，而是它作为一份"让程序去测量物理世界并据此决策"的真实案例**——作者公开了《Built with Claude Code》与《Lessons learned》（20 条踩坑），讲清了"量错了量、测试根本不会失败、清理靠巧合、一条说明悄悄失效"这类问题。适合对计算机视觉、实时环路、以及"用 Agent 开发硬件相关程序"感兴趣的人。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`LICENSE`、仓库文件树与 GitHub 页面内嵌元数据；**未下载发行包、未运行、未接摄像头/激光训练器、未验证检测与计分**）
- 适用范围：**发行版仅 Windows（v1.0）**。硬件前提是**哑光（防眩光）显示器 + 能做 60fps 的普通摄像头 + 可见红（约 650 nm）激光训练弹/训练手枪**；摄像头装在显示器**侧旁、斜向对着屏幕**。发行包**自带 Python 运行时**（约 231 MB，无需另装 Python）；从源码跑需 Python 与 `app/requirements.txt`。

## 核心结论

**一句话定位（README 自述）**："A laser dry-fire shooting game: your matte monitor is the target, an ordinary webcam watches it from the side, and your laser trainer leaves bullet holes where you hit."——**激光干射（dry-fire）射击游戏**，屏幕是靶、侧拍摄像头当传感器。

**三个最值得记的点：**

1. **"物理约束决定软件设计"是它的主线（README 自述的事实）**：训练弹每次扣扳机约亮 **100 ms**，60fps 摄像头有 **6 帧** 能看到光点——这正是"普通摄像头就够用"的理由（**一帧是噪声，六帧才是一次射击**）。检测器**不找"最亮"，而是找"红色"**（红减去绿与蓝中较大者），所以整个游戏刻意做成灰暗配色；**显示器必须是哑光的**——哑光涂层把光点散射开，侧拍摄像头才看得到，亮面玻璃则按每次不同的方向把光反射走。**这套"约束—设计"的推理比游戏本身更值得记。**
2. **它公开了自己作为"Agent 开发物理测量程序"的复盘（README 自述）**：整个项目由 **Claude Code 约两周**写成（作者做指挥、试射与验收）。仓库顶层就是《[Built with Claude Code](https://github.com/vkorost/screenplink/blob/master/BUILT-WITH-CLAUDE.md)》（索引）与《[Lessons learned](https://github.com/vkorost/screenplink/blob/master/LESSONS-LEARNED.md)》（20 条，每条配导致它的那次测量）。作者明说"**几乎没有一条是关于激光的**"——例如：**用了一个与目标"相关"而非目标本身的数值去选曝光**，结果"把房间调暗"这一最优条件反而把它弄坏；**整套启动测试跑完第一次启动就报告成功**，一个被引用多日的数字**从未真正被测量过**；一次清理**靠"恰好跑在它之后的东西"**才正常，那个前提失效当天就崩了。**对做"实时环路 / 自诊断硬件 / 需要向人解释自己"的程序，这份清单是可迁移的。**
3. **它把"让 Agent 自己把应用跑起来"做成了发布能力（README 自述）**：发行包除了程序，还**附带写给 Claude Code 的 `CLAUDE.md` 和 `.mcp.json`**——解压后用 Claude Code 打开该文件夹，Agent 会**自己跑摄像头诊断、按"每只摄像头实际测到的数值（而非名称）"挑出对着屏幕的那一只、启动应用、读应用测到的房间数据、并告诉你是该开火还是有一件物理上的东西挡路**。应用自身也通过 **MCP 或普通 HTTP** 对外回答"它看到了什么"（给出**测量值而非结论**）。**"把应用变成 Agent 可以动手操作的仪器"是它最有借鉴价值的形态。**

**个人判断（未验证）**：它把"硬件相关、需要现场调参"的软件做成了"**一切测量都写下来、可由文件定位问题**"的风格（`camera_lab.log` 每次启动一行 `GATE`，含判定、标定误差、帧率、噪声底、检测阈值），这在同类小项目里少见。**但它明确是一人两周、单套硬件（一块 Dell U4320Q + Razer Kiyo Pro + 两支 LaserLyte LT-TTL）的产物**，作者自己说"**第二个 rig 的结果没人知道**"，这就是它最大的不确定性。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [vkorost/screenplink](https://github.com/vkorost/screenplink) |
| 描述 | "A laser dry-fire shooting game: your matte monitor is the target, an ordinary webcam watches it from the side, and your laser trainer leaves bullet holes where you hit." |
| 归属 | 账号 `vkorost`（**个人**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false` |
| 许可证 | **Apache-2.0**（页面 `spdxId: Apache-2.0`；**直读 `LICENSE` 文件**为标准 Apache-2.0 全文，11,354 字节）。**注意：README 明确写"代码是 Apache-2.0，但素材不都在此许可下"**——见"使用前需要注意" |
| 主语言 | **Python 98.3%**（shields.io，2026-11-13） |
| 技术栈 | **Python 应用（`app/src/lsa/`，含 camera / detect / display / modes / artwork / audio / introspect 等模块）+ 打包发布（`app/packaging/`，PyInstaller 风格 `.spec`）+ 测试（`app/tests/`，无需摄像头/屏幕即可跑）+ 用户手册（`manual/`，含 PDF/EPUB）+ PowerShell/BAT 脚本**；应用暴露 **MCP 与 HTTP 自省接口** |
| 关注度 | **69 Star / 13 Fork / 0 Watcher**（**页面值**，2026-11-13）；开放 Issue **0**（shields.io，2026-11-13）。**页面值与 ungh.cc 镜像一致（镜像同为 69 / 13 / 0）** |
| 维护状态 | **2026-10-24 创建**；页面 `updatedAt` **2026-11-09**、ungh 报 `pushedAt` **2026-11-09**（距核验日约 4 天）；已累计 **1 Release / 3 tags**（页面值） |
| 默认分支 | **`master`** |
| 版本 | **1 Release / 3 tags**；当前发布 **v1.0.2**（README，Windows，约 231 MB）；页面 `packages: true`、`usedBy: false` |
| 话题标签 | `computer-vision` · `dry-fire` · `dry-fire-laser-training` · `dryfire` · `firearms-training` · `game` · `laser` · `laser-gun` · `laser-shooting-simulator` · `laser-training` · `marksmanship` · `opencv` · `python` · `shooting` · `shooting-game` · `shooting-range` · `shooting-simulator` · `training-game` · `webcam` · `windows` |
| 体积 | `master` 文件树约 **520 个条目**（ungh.cc files API）；顶层以 `resources`（304）、`app`（177）、`manual`（29）为主 |
| 官网 | <https://www.youtube.com/watch?v=tg2Bt8K5IE0>（作者演示视频） |

## 玩什么（README 自述）

- **九种练习**：静态靶、逐渐加速的球、弹跳球、反应射击、清台、弹出式人形靶、持靶人形、走进走出的人形、中弹倒下的士兵。
- **十五种靶图**：每个都印有计分环，且各有倒地动画。
- **一把"你的"弹匣**：默认 17 发，上下方向键可在 **6–30** 间调整并记忆，**对着你自己的枪练**；每发射击（无论是否命中）耗一发，空仓开火只发出"咔"声；右方向键给当前弹匣组加 10 发。
- **同屏双人**：左右各自计分、各自弹匣；**同一帧里出现两道激光会被识别为两次独立射击**，可以同时开火。
- **自己定节奏**：Page Up / Page Down 调整靶运动速度，每种练习各自记忆；也可"射击菜单"——菜单本身也是靶，不用键盘。
- **不用开火就自动校准**：程序拍摄屏幕上的自定义标记，自己算出屏幕位置与像素映射（标定不耗弹、不耗耐心），约 **30 秒**给出"就绪"，或告诉你唯一要修的那一处。

## 下载与运行（README 自述）

- **发行版**：下载最新 release，解压到任意位置，运行 **`Play.cmd`**——**无需 Python、无依赖、无安装器**；Windows only（v1.0）。
- **先自检**：可选先跑 **`Doctor.cmd`**，它会测试找到的每只摄像头并写出 `doctor-report.txt`，告诉你哪只适合。
- **从源码跑**：`python -m venv app\.venv` → 装 `app\requirements.txt` → `app\scripts\Play.cmd`。
- **交给 Claude Code**：解压 release，用 Claude Code 打开该文件夹并让它"setup"——包内 `CLAUDE.md` 与 `.mcp.json` 让它自己完成摄像头诊断、挑摄像头、启动、读数据并给出提示。
- **退出**：按 Escape，再在 YES 上按 Enter / Space（会先确认）；**不要用"结束任务"强杀**，强杀可能让摄像头需要重新插拔。
- **测试**：完整测试套件随 release 与仓库一同提供，**不需要摄像头和屏幕**，用 `app\scripts\run_tests.bat` 运行。

## 使用前需要注意

- **⚠️ 许可证边界：代码与素材两套（README 明说，页面未体现）**：**代码是 Apache-2.0，但素材不在此许可下**——音效来自 **Pixabay**，启动画面/十五个靶图与其倒地动画/菜单贴图/弹匣弹药图由 **Google Gemini** 生成，语音台词由 **ElevenLabs** 生成；打包发行还含**两个 LGPL 库**（见 `THIRD-PARTY-NOTICES.md`）。**fork 时素材随其各自条款走，而不是 Apache-2.0**。**GitHub 页面把整个仓库标为 Apache-2.0，容易让人误以为素材也适用——这是页面显示与实际授权的落差，务必以 README 的说明为准。**
- **⚠️ 硬件是硬门槛，不是可选优化（事实）**：必须**哑光（防眩光）显示器**（不是亮面、不是电视）、**能做 60fps 的摄像头**、**可见红激光训练器**。光点能不能被侧拍摄像头看到，取决于面板散射与房间光照；**README 明确说这"没得商量"**。
- **⚠️ 安全前提由使用者负责（README 自述）**：这是**干射（dry-fire）练习**——**房间里任何位置都不得有实弹**；训练器为 **5 mW Class IIIa**，不会损坏 LCD，但**不要对着枪口看**。程序无法知道你手里拿的是什么。
- **⚠️ 仓库含二进制 / 大体积素材（按文件树判断）**：`manual/` 内含 **PDF 与 EPUB** 手册，`resources/`（304 个条目）含大量图片/音频素材。**注意：约 231 MB 的发行包在 release 而非仓库中**。
- **⚠️ 联网与权限（README 自述）**：发行版**离线、免费、无账号、无广告**。但"交给 Claude Code 自动安装"这条路需要 **Claude Code 与网络**；此外应用运行需**摄像头访问**（以及被自省接口监听的本机端口）。
- **⚠️ 单套硬件、单人两周的产物（事实）**：一切测量基于"一块 Dell U4320Q + Razer Kiyo Pro + 两支 LaserLyte LT-TTL 训练器"，作者**明说第二个 rig 的表现未知**，并**把"第二个人的数据"列为对本项目最有价值的贡献**。**无支持承诺、无响应时间承诺。**
- **⚠️ README 自述与仓库实际**：README 提到的 `CLAUDE.md`/`.mcp.json`（在发布包内）、`manual/`、`TROUBLESHOOTING.md`、`LESSONS-LEARNED.md`、`MCP.md`、`PROJECT-LAYOUT.md` 等**在文件树中均能找到对应**（README 引用的 `BUILT-WITH-CLAUDE.md`、`LESSONS-LEARNED.md`、`MCP.md` 位于仓库顶层），**未发现明显不符**（仅按文件树判断）。
- **我未做任何运行验证**：**未下载发行包、未运行、未接摄像头或激光训练器、未验证检测与计分准确度**；上述玩法、物理推理、性能数字与许可边界**均来自 README、`LICENSE` 与仓库文件自述**。

## 什么时候会再看

- 想做**"用摄像头 + 计算机视觉做实时交互"**的项目，需要一份讲清"物理约束 → 设计取舍"的实例（100 ms 光照 / 60fps / 找红不找亮 / 哑光屏散射）。
- 想参考**"让程序自我诊断硬件、把测量值写成可读日志（每行一个 `GATE`）、并据此向人解释"**的工程做法（`TROUBLESHOOTING.md` 就是按故障写的）。
- 想读一份**"用 Claude Code 开发真实系统"的坦诚复盘**（《Built with Claude Code》+《Lessons learned》20 条），尤其是"测量错了量 / 测试不会失败 / 清理靠巧合"这几类可迁移的坑。
- 想抄**"应用自带 `CLAUDE.md` + `.mcp.json`，让 Agent 自己完成安装与现场调试"**的发布形态。
- 自己有一处可调暗的角落、一块哑光屏和一只 60fps 摄像头，想试试激光干射训练游戏。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"把应用本身变成 Agent 可调用的 MCP 服务"**：[servers：MCP 官方参考服务器合集](servers-MCP官方参考服务器合集.md)——那一篇是官方参考服务器清单，这一篇是一个**具体应用把自身测量数据经 MCP/HTTP 开放给 Agent**（且只给测量值、不给结论）的实例，可对照"自建 MCP 服务端"的形态与边界。
- **同属"AI Agent 参与游戏/内容制作"**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——一个是"让 Agent 生成游戏美术"，一个是"让 Agent 把整个游戏写出来并按现场测量调好"，可对照 **Agent 在游戏项目里的不同分工与可信度边界**。
- **同属"把花活塞进小设备"**：[DroidDeck：把 SteamOS 体验搬到安卓](DroidDeck-把SteamOS体验搬到安卓.md)——那篇把整套 **SteamOS 游戏模式与桌面**硬塞进**安卓掌机**，本篇把**哑光显示器 + 普通摄像头**变成**激光干射靶场**。一个偏**安卓上的完整游戏生态**，一个偏**硬件创意**。

## 参考来源

- 项目仓库与 README：https://github.com/vkorost/screenplink
- 演示视频（预告）：https://youtu.be/tg2Bt8K5IE0 ・ 原理讲解：https://www.youtube.com/watch?v=pIdJhRYK-Bw
- 许可（Apache-2.0）：https://github.com/vkorost/screenplink/blob/master/LICENSE
- 用 Claude Code 开发复盘：https://github.com/vkorost/screenplink/blob/master/BUILT-WITH-CLAUDE.md ・ 经验教训：https://github.com/vkorost/screenplink/blob/master/LESSONS-LEARNED.md
- 故障排查：https://github.com/vkorost/screenplink/blob/master/TROUBLESHOOTING.md ・ 自省接口（MCP / HTTP）：https://github.com/vkorost/screenplink/blob/master/MCP.md
- 第三方声明：https://github.com/vkorost/screenplink/blob/master/THIRD-PARTY-NOTICES.md
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 与 Tag 数 / 许可证）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未下载发行包、未运行、未接摄像头或激光训练器、未验证检测与计分准确度**；玩法、物理推理、性能数字与许可边界均来自 README、`LICENSE` 与仓库文件自述。
