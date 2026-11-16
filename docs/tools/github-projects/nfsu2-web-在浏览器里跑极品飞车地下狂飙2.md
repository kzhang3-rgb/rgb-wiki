# nfsu2-web：在浏览器里跑《极品飞车：地下狂飙 2》

> 一个把 **2004 年的 PC 游戏《Need for Speed: Underground 2》搬进 Chrome / Firefox** 的粉丝项目：游戏的 **x86 代码被预先翻译成 C**，再编译成 **WebAssembly**；**Direct3D 9 跑在 WebGPU 上、声音走 Web Audio**；**游戏本体要使用者自备**。**但仓库已在 2026-11-09 归档**——`nfsu2.xyz` 仍在更新，代码却转为私下开发。它值得在"浏览器里跑老游戏 / 静态重编译"这类问题时回看，**归档这件事本身也是重点**。

- 状态：草稿
- 最近核验：2026-11-16（阅读 README 全文 110 行、`LICENSE` 首部、仓库文件树 1,637 项、ungh.cc 与 shields.io 元数据；**未 clone、未构建、未运行、未播放、未下载任何游戏文件**）
- 适用范围：**仅供了解其技术路线与归档状态**。需要**自备正版 PC 版游戏**并自行构建；涉及 EA 商标与游戏素材的权利边界见"使用前需要注意"。

## 核心结论

**一句话定位（README 自述）**：`A fan project that runs the PC game Need for Speed: Underground 2 (2004) in Google Chrome and Firefox.`——游戏 x86 代码**提前翻译成 C** 再编成 **WebAssembly**，**D3D9 → WebGPU**、声音走 **Web Audio**。

**三个最值得记的点：**

1. **⚠️ 仓库已归档（最重要的事实）**：README 顶部 `[!IMPORTANT]` 写明"**This repository is archived.** `nfsu2.xyz` keeps running and getting updates, but from **9 November 2026** development continues **privately**"。作者解释：现有开发方式下**没时间精力同时把它当开源项目并认真审外部贡献**，并且"**见过有人把本项目的工作和我的更新当作自己的发表**"，想在没有被同样复制的前提下继续在此代码上做后续更新与项目。**代码仍以 MIT 保留，可自由使用 / 修改 / 分享，但须保留许可声明并给出署名**；issues 与 PR **仍可读，但不再回复**，后续交流转到 Discord。
2. **技术路线是"静态重编译"而非"模拟器"**：**不是**用 JS 写一个模拟器，而是用 Python lifter（基于 **Capstone**）把 `SPEED2.EXE` 的**每个函数翻译成 C**，再编成 WASM；同时**重写 Windows / Direct3D 9 / DirectSound / DirectInput** 的运行层，浏览器侧把游戏线程当作**共享内存上的 worker** 跑、用 WebGPU 绘制、用 AudioWorklet 出声。它 fork 自 **Talha Müderrisoğlu 的 [NFSU2Recomp](https://github.com/TalhaMudo/NFSU2Recomp)**（NFSU2 的原始 x86→C 静态重编译器），**只用其 2026-10-16 那份 MIT 版本里的代码**。
3. **本体不含游戏素材，版本校验写死到字节**：README 明确"**Not affiliated with or endorsed by Electronic Arts. This repository contains none of the game's code or data.**"——要使用必须**自备** PC 正版并打补丁到 **v1.2（北美）**，且**只能是那一个 `SPEED2.EXE`**：**4,800,512 字节**，SHA-256 `f9dd86c054878ce6276beb07c1fd61874f7a1e4bf1f241b084c65b73e24168a7`（**其他版本不行**）。生成的 C 与编译产物**从不入库**（`.gitignore`）。

**个人判断（未验证）**：这是"**把二进制翻译成可移植源码再搬上 Web**"的干净样本，工程含量高；但**归档意味着外部贡献窗口已关**，后续以 `nfsu2.xyz` 为准，本仓库更像一个**只读快照**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [th0mk/nfsu2-web](https://github.com/th0mk/nfsu2-web) |
| 描述 | "Archived: development continues privately. Need for Speed: Underground 2 in the browser, nfsu2.xyz" |
| 归属 | 账号 `th0mk`（**个人**）；`isArchived: true`；`isFork: false` |
| 许可证 | **MIT**（`LICENSE` 首行 `MIT License`，`Copyright (c) 2026 th0mk`，另注 `Portions copyright (c) 2026 Talha Müderrisoğlu (the original recompiler and macOS runtime)`）；**shields 显示 `not identifiable by github`**（因版权行被改写，GitHub 未自动识别为标准 MIT，但正文是 MIT） |
| 主语言 | shields 显示 **36.1%**（以 C 为主，另含 Python / Rust / TypeScript） |
| 技术栈 / 构成 | **Python lifter（Capstone）** + 重写的 **Windows / D3D9 / DirectSound / DirectInput 运行层** + 浏览器侧（worker + 共享内存 + **WebGPU** + AudioWorklet）+ **Rust 服务端**（`web/server-rs/`，兼做多人联机）；`web/deploy/` 提供 Docker 部署 |
| 关注度 | **1,190 Star / 240 Fork**（ungh.cc，2026-11-16；shields 显示 **1.2k Star / 266 Fork**、**45 开放 Issue**、**贡献者 13**） |
| 维护状态 | **2026-10-19 创建**；`pushedAt` **2026-11-09**；页面 `updatedAt` 2026-11-10；**已归档（不再接受回复）** |
| 默认分支 | `main` |
| 版本 | **0 Release、0 tag**（ungh releases 返回空数组） |
| 体积 | `main` 文件树共 **1,637 项**（ungh files API） |

## 需要什么 / 怎么跑（按 README）

- **游戏**：自备 PC 正版，**打补丁到 v1.2（北美）**，且上面那个**精确字节数的 `SPEED2.EXE`**。
- **构建环境**：Apple Silicon Mac（README 称测试环境）或 Linux；**Python 3、Node.js 20+、Rust（rustup）、[wasi-sdk](https://github.com/WebAssembly/wasi-sdk/releases) 34**（解压到 `deps/`）。**Windows 需在 WSL 2 里构建**（克隆到 Linux 文件系统，如 `~/nfsu2-web`；放在 `/mnt/c` 下会慢很多）。
- **构建**：`python3 -m venv .venv && .venv/bin/pip install -r requirements.txt` → `web/gen.sh "<游戏目录>/SPEED2.EXE"`（把游戏代码翻译成 C 到 `gen/`）→ `web/build.sh`（编成 `out/web/nfsu2.wasm`）。
- **运行**：`web/start.sh --data "<游戏目录>"`，浏览器打开 `http://localhost:8080/` 点 Play。
- **多人**：服务端需跑在 **HTTPS 后**（其他机器要安全上下文），`web/deploy/` 有 Docker 方案；**同一服务器上玩的人可在自由漫游里相遇**。
- **同一套树仍能构建原生 macOS 版**（`./gen.sh`、`./build.sh && ./link.sh`、`./run.sh`）。
- **语言包**：来自多个社区 mod（葡 / 波 / 土 / 匈 / 乌 / 捷 / 斯洛伐克等），捷克与土耳其文本含**本仓库自己的翻译**，希腊文本由贡献者提供（`tools/` 下相应 JSON 与字体工具）；菜单与 HUD 可用 **4 倍分辨率**贴图包重绘。
- **README 结尾注明"Built with the help of Claude Code"**，并致谢大量 mod 作者（免责声明写明不隶属 EA）。

## 使用前需要注意

- **⚠️ 仓库已归档、贡献窗口关闭（事实）**：issues 与 PR **不会得到回复**，后续开发在**私有仓库 / `nfsu2.xyz`** 进行。把它当**只读参考**，别指望上游合并你的改动。
- **⚠️ 需要自备正版游戏，且版本校验极严（事实）**：README 写明**不含任何游戏代码或数据**，且要求**精确的那个 `SPEED2.EXE`（字节数 + SHA-256）**；换句话说，**没有合法的游戏拷贝，这个项目跑不起来**。
- **⚠️ 商标与 mod 素材的授权边界（事实）**：README 自述**与 EA 无关联、未获认可**；`Need for Speed` 与 `Need for Speed: Underground 2` 是 EA 的商标。语言包 / 贴图包来自**第三方社区 mod**，各 mod 的授权与来源需自行核对（README 的 Credits 逐项列出）。
- **⚠️ 构建门槛偏高（事实）**：需要 Apple Silicon Mac 或 Linux（Windows 走 WSL 2）、Rust、wasi-sdk 34、Python、Node 20+；对普通用户**不是"下载即玩"**。
- **⚠️ `LICENSE` 的版权被改写导致识别失败（事实）**：shields 显示 `not identifiable by github`——正文仍是 MIT，但**因加了一行他人版权声明**，自动识别器没认出来；使用时以 `LICENSE` 正文为准。
- **⚠️ 我未做任何验证**：**未 clone、未安装依赖、未构建、未运行、未播放、未下载任何游戏文件、未验证 SHA-256**。技术路线、字节数与步骤均**来自 README 与仓库文件本身**。

## 什么时候会再看

- 想研究"**把 PC 原生游戏（x86 + D3D9）搬进浏览器**"的完整路线（静态重编译 → WASM → WebGPU / Web Audio）时。
- 想参考"**源码公开但不含任何原版素材、靠使用者自备 + 字节级校验**"这类项目的版权划法与免责声明时。
- 想复盘"**一个高热度开源项目为何归档、以及代码仍以 MIT 保留意味着什么**"时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"浏览器里跑老游戏"，路线不同**：[ra2：浏览器运行红色警戒 2](ra2-浏览器运行红色警戒2.md)——那篇用 **v86 模拟原版可执行文件**在浏览器里跑（未重编译），本篇是**把 x86 静态重编译成 C 再编成 WASM**；两者都**不含游戏素材、都靠使用者自备**，可对照"**模拟**"与"**重编译**"两条 Web 化路线。
- **同为"用新栈重做既有商业软件"**：[ConcordRTS：用 C++20 重写红警 2 引擎](ConcordRTS-用C++20重写红警2引擎.md)——那是**人手重写引擎源码**（不含素材），本篇是**翻译原版二进制再跑**；可对照"重写实现"与"翻译二进制"两种做法。
- **同为"把 PC 级体验搬进非 PC 环境"**：[DroidDeck：把 SteamOS 体验搬到安卓](DroidDeck-把SteamOS体验搬到安卓.md)——那篇在安卓上用独立 Linux 运行时装整套 SteamOS，本篇用浏览器复刻一款 PC 老游戏；同属"**跨设备复刻体验**"，技术栈差异很大。

## 参考来源

- 项目仓库与 README：https://github.com/th0mk/nfsu2-web
- 上游重编译器：https://github.com/TalhaMudo/NFSU2Recomp
- 游戏在线运行站（README 指向，开发已转私下）：https://nfsu2.xyz
- 元数据（Star / Fork / 开放 Issue / 贡献者 / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 主语言 / 归档状态）：ungh.cc 镜像与 shields.io 徽章，2026-11-16

以上信息核验于 2026-11-16，Star 数与归档状态随时变化。**未 clone、未构建、未运行、未播放、未下载任何游戏文件**；技术路线与版本校验值均来自 README 与仓库文件本身。**本笔记不含、也不提供任何游戏素材。**
