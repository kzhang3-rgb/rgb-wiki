# esp32-usb-to-airprint：用一块 ESP32-S3 给旧 USB 打印机加上 AirPrint 与 AirScan

> 一块便宜的 **ESP32-S3 开发板**插进旧打印机的 USB 口、连上 WiFi，这台只能 USB 直连的打印机就变成了**无线打印机 + 扫描仪**：iPhone / iPad / Mac 走 **AirPrint**、Android 走 **Mopria**、Linux 免驱打印，扫描可从浏览器、macOS 的"图像捕捉 / 预览"或板载按键发起。它把"协议翻译"放在固件里，值得在"如何用一块小 MCU 救活停产外设"这类问题时回看。

- 状态：草稿
- 最近核验：2026-11-16（阅读 README 全文 124 行、`LICENSE` 首行、仓库文件树 142 项、`commits/master.atom`、ungh.cc 与 shields.io 元数据；**未购买开发板、未烧录固件、未连接任何打印机、未打印或扫描、未编译**）
- 适用范围：**仅供了解其方案与支持的机型**。需要自备 **ESP32-S3** 板与一台**已拥有**的 USB 打印机；适配与实测风险见"使用前需要注意"。

## 核心结论

**一句话定位（README 自述）**：把"只能用 USB 连电脑"的打印机变成**网络打印机 / 扫描仪**——ESP32-S3 插进打印机 USB 口，接入你的 WiFi；打印机与扫描仪的协议**由固件侧翻译**（无需在电脑 / 手机上装驱动或 App）。

**三个最值得记的点：**

1. **硬件门槛极低**：只需要**一块带两个 USB-C 口的 ESP32-S3 开发板**（一个口接打印机、一个口接电脑刷机）。可选的 SSD1315 OLED 屏与 3D 打印外壳属"锦上添花"，不是必需件。
2. **支持机型是"分级"的，且大部分未经真机验证**：README 的表里只有 **Canon MG2550S 标为 🟢（已在真机验证）**，其余十余个 MG / E / iP / MP / MX / TS 系列机型标为 🟡（预计可用、**尚未在真机上测过**）。作者在 README 顶部明确征集贡献，并要求"加机型的人必须真的拥有那台打印机、必须在真机上测过，不能只在模拟里测"。
3. **固件侧不含内容，只做协议翻译**：项目自身**不含任何打印机驱动二进制**，而是**按开源社区的协议实现**来做——README 的 License 段写明打印 / 扫描协议参照 **[Gutenprint](https://gimp-print.sourceforge.io/)** 与 **[SANE](http://www.sane-project.org/)** 的 **pixma** 后端（佳能 PIXMA 系列）；JPEG 编码器的 DCT 是 libjpeg `jfdctint.c` 的移植。

**个人判断（未验证）**：这类"把老硬件接进现代生态"的小项目，价值主要在**方案本身**（用一块几十元的 MCU 换掉厂商的驱动与云服务依赖）。但**能不能跑通，取决于你手上那台打印机是否落在已适配 / 已实测的机型里**，README 的机型表是唯一参考，且八成是"未验证"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [ngxson/esp32-usb-to-airprint](https://github.com/ngxson/esp32-usb-to-airprint) |
| 描述 | "Add AirPrint / AirScan support to your old printer, using just a cheap ESP32-S3." |
| 归属 | 账号 `ngxson`（**个人**）；`isFork: false` |
| 许可证 | **MIT**（`LICENSE` 首行 `MIT License`，`Copyright (c) 2026 Xuan-Son Nguyen <contact@ngxson.com>`） |
| 主语言 | **C（shields 显示 62.5%）** |
| 技术栈 / 构成 | **ESP32-S3 固件**（PlatformIO 构建）+ 板内 **Web 应用**（README 称 Web UI 打包了 Svelte 与 pdf.js）+ 电脑端**模拟器**（`host/`，可无板运行） |
| 关注度 | **317 Star / 46 Fork**（ungh.cc，2026-11-16；shields 显示 **320 Star / 48 Fork**、**3 开放 Issue**、**贡献者 2**） |
| 维护状态 | **2026-10-26 创建**；`pushedAt` **2026-11-09**（`commits/master.atom` 最新提交同为 2026-11-09，`Merge pull request #9 … Make esp32s3 the default PlatformIO environment`，作者 `ngxson`）；页面 `updatedAt` 2026-11-15 |
| 默认分支 | **`master`（非 `main`）** |
| 版本 | **0 Release、0 tag**（ungh releases 返回空数组） |
| 体积 | `master` 文件树共 **142 项**（ungh files API） |

## 它怎么工作（按 README）

- **打印**：iPhone / iPad / Mac 走 **AirPrint**、Android 走 **Mopria**、Linux 免驱；不需要装任何驱动或 App。
- **扫描**：可从**浏览器**、macOS 的 **图像捕捉（Image Capture）/ 预览**、或**板载按键**发起。
- **板内 Web 应用**：在手机 / 电脑浏览器里打印 PDF 与图片、扫描、把文件留在浏览器里，并把多页**合成一个 PDF**（可旋转、裁剪、拼"证件照双面"）。

**搭建步骤（README 三步）**：

1. **硬件**：把 ESP32-S3 通过**标注 `COM` 的那个口**接电脑（**不是**标注 `USB` 的口）。
2. **配置**：先构建一次（`pio run`）会生成 `.env`，填 `WIFI_SSID` / `WIFI_PASSWORD` / `PRINTER_NAME` / `HOSTNAME` / `MODE`；`MODE=debug` 允许之后**通过 WiFi 升级固件**，`MODE=normal` 则不支持。`.env` 已 gitignore，WiFi 密码留在本地。
3. **烧录**：把板子的 `COM` 口接电脑，`pio run -t upload`。

**使用**：把打印机接到标注 `USB` 的口（README 提示**可能还需要桥接板上的 `USB-OTG` 焊点**，让板子向打印机供 5V——打印机虽不真正吃 5V，但靠这个信号感知 USB 插入）；浏览器打开 `http://<你的 HOSTNAME>.local/`；板载按键（BOOT 或 GPIO4）：按一下=扫描、按两下=打测试页、长按 2 秒=取消。

**可选硬件**：SSD1315 OLED（VCC→3V3、GND→GND、SCL→GPIO41、SDA→GPIO42，**须在上电前接好**，固件只在启动时查找）；按键 1→GPIO4（同 BOOT），按键 2→GPIO5（未分配，需自己在 `src/main.c` 的 `on_user_button()` 里加）。

**开发**：`MODE=dev` 时打印任务被处理但不真正打印、扫描返回内置测试页；电脑端可跑 `make -C host && cd host && ./airprint_host`，打开 `http://localhost:8631/` 用**模拟的打印机与扫描仪**；Web 应用可用 `cd web && npm run dev`（可指向板子 IP）。

**加机型**：每个机型是 `components/printers/` 下的**一个驱动**，在 `printers.c` 里登记，固件根据接入的打印机挑对应驱动；与 MG2500 数据流接近的 Canon PIXMA 在 `canon_pixma.c` 里只是**一行**，其他打印机会是同目录里的新文件。

## 使用前需要注意

- **⚠️ 机型适配是最大不确定性（事实）**：README 的支持表里**只有 Canon MG2550S 标为"真机已验证"**，其余全是 🟡"预计可用、尚未真机测试"。README 顶部也强调"你必须真的拥有那台打印机，每个驱动都要在真机上测，不能只在模拟里测"。**买板前先确认你的机型在表内、并接受它可能是未验证项**。
- **⚠️ 需要动硬件（事实）**：要一块**双 USB-C 口的 ESP32-S3**；可能要在板上**焊接桥接 `USB-OTG`** 焊点；可选 OLED 需**上电前接好**。
- **⚠️ 固件可经 WiFi 升级（事实，安全面）**：`MODE=debug` 让板子可被 WiFi 升级——方便，但也意味着**只要拿到局域网入口就能刷固件**；README 未展开这块的鉴权与加固说明。
- **⚠️ 协议实现的法律边界（事实）**：README 自述打印 / 扫描协议**参照** Gutenprint 与 SANE 的 pixma 后端，JPEG 的 DCT 移植自 libjpeg。项目本身走 **MIT**，但**与佳能官方 SDK / 驱动无关联**，是否触发厂商条款需自行判断。
- **⚠️ 我未做任何验证**：**未购买开发板、未烧录、未连接打印机、未打印或扫描、未编译任何代码**。上述步骤、机型表、引脚与协议说明**全部来自 README 与仓库文件本身**，未复现。

## 什么时候会再看

- 手上有**闲置的 USB 打印机**（尤其佳能 PIXMA 系列），想让它在 iPhone / Android / Linux 上**免驱打印并扫描**时。
- 想参考"**用一块小 MCU 做协议翻译、把停产外设接进现代生态**"的固件工程写法（多机型驱动登记、Web UI 内嵌、无板模拟器）时。
- 想对照"**把 AI / 服务接进 ESP32 硬件**"的不同方向（见相关笔记）时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"ESP32 上的开源固件项目"**：[xiaozhi-esp32：基于 MCP 的 ESP32 语音聊天机器人](xiaozhi-esp32-基于MCP的ESP32语音聊天机器人.md)——那是把大模型语音能力接进 ESP32 固件的设备端项目，本篇是把打印 / 扫描协议接进 ESP32；同是"给一块 MCU 写完整固件"，业务方向完全不同。
- **同为"AI / 软件接进实体硬件"**：[jumper：22 自由度螃蟹机器人的 AI 设计与动作工具包](jumper-22自由度螃蟹机器人的AI设计与动作工具包.md)——那是仿真与动作工具包（ESP32 作为运行硬件之一），本篇是**打印机协议翻译固件**；可对照"硬件上跑什么"的两类做法。

## 参考来源

- 项目仓库与 README：https://github.com/ngxson/esp32-usb-to-airprint
- 提交记录：https://github.com/ngxson/esp32-usb-to-airprint/commits/master.atom
- 协议参考（README 标注）：Gutenprint https://gimp-print.sourceforge.io/ ；SANE http://www.sane-project.org/
- 元数据（Star / Fork / 开放 Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 主语言）：ungh.cc 镜像与 shields.io 徽章，2026-11-16

以上信息核验于 2026-11-16，Star 数与提交活动随时变化。**未购板、未烧录、未连接任何打印机、未打印或扫描、未编译**；步骤、机型表与协议说明均来自 README 与仓库文件本身。**本笔记不构成对任何机型可用性的担保。**
