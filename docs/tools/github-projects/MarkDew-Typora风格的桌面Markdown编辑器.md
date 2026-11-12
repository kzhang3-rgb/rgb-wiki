# MarkDew：以 Typora 为参照物的桌面 Markdown 编辑器

> 记录一个用 CodeMirror 6 + Tauri 写的桌面 Markdown 编辑器：**`TARGET.md` 里明写"以 Typora 作为用户体验与 Markdown 功能集的参照物"**。第一句先记清三件事：**它的许可证是"双许可"——AGPL-3.0-or-later + 商业授权另谈**（`LICENSE` 前半段就是双许可声明，故 API 返回 `NOASSERTION`）；**它改过名（`MarkBend` → `MarkDew`）**；**它的文档体量很反常**（`BENCHMARKS.md` 约 403 KB、`ROADMAP.md` 约 170 KB）。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README（83 行，中英双版）、`LICENSE` 双许可声明段与 AGPL 全文存在性、`package.json` 全文、`TARGET.md` 开头、`tests/` 与 `src/` 目录规模、Release 与元数据；**未安装、未运行、未打开任何文档、未做性能基准、未审计源码**）
- 适用范围：开发需 **Node.js + Rust + Tauri**；桌面端（Windows / macOS / Linux，macOS 分 Apple Silicon 与 Intel）

## 核心结论

**① 许可证是"双许可"，这一点必须看清。** `LICENSE`（35,451 B）标题即 "**MarkDew Dual Licensing Notice**"，前 24 行是双许可声明，其后内嵌 AGPL-3.0 全文。条款为：① 开源 / 个人使用走 **AGPL-3.0-or-later**；② **商业使用需另购商业许可**——尤其针对"把 MarkDew 嵌入专有软件"或"提供托管服务却想规避 AGPL 传染"的场景。`package.json` 的 `license` 字段写作 `AGPL-3.0-or-later`。**这也是 GitHub API 的 `license` 返回 `NOASSERTION`（Other）的原因：文件头不是标准 SPDX 许可证全文，而是自定义的双许可声明。**（提醒：本库反复出现的经验——**API 的 license 字段不可尽信，务必回看 `LICENSE` 文件本身**；此处又是一个实例。）

**② 它把"参照物"和"真值规则"写进了 `TARGET.md`。** 明写 "**Typora is the reference for the user experience and Markdown feature set**"，并交代技术底座是 "**CodeMirror 6 in a Tauri desktop WebView**"；源真值规则是 "**The Markdown byte sequence is the only saved representation**"（即 Markdown 字节序列是唯一的保存形式，不做隐藏的富文本状态）。目标清单还列了 **CJK / 双向文本 / 输入法 / 1 MiB 文档的响应式编辑 / PDF 导出** 等。**把"我要做成什么样、以谁为标杆、什么才是唯一真值"前置写清，是这类工具少见的做法。**

**③ 一段改名史，且被明确记录。** README 自述 "**MarkDew was named MarkBend through v1.0.10**"，前身为仓库 `shenghsi/MarkBend`；并说明 **Bend 编译器已不再是应用的一部分**（项目早期似乎与某种 "Bend" 语言/编译器有关，后剥离）。**改名而非新建**是常见但容易混淆的点——查旧项目名时要注意。

**④ 文档体量反常地大，且以"测试"为重心。** 根目录 `BENCHMARKS.md` **约 403 KB**、`ROADMAP.md` **约 170 KB**，另有 `PORTABILITY.md`、`RELEASE.md`、`BETA_RELEASE.md`、`UPDATES.md`。目录树 625 项 / **592 个 blob / 约 14.9 MB**，其中 **`tests/` 占 434 个文件**，`src/` 140 个。技术栈上，前端依赖 CodeMirror 6 全家桶、`@mathjax/src`（公式）、`markdown-it` + `mdast-util-*` + `micromark-extension-*` + `parse5`（解析）、`dompurify`（净化）、`html-to-image`、`pele`，桌面壳是 `@tauri-apps/api`。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [shenghsi/MarkDew](https://github.com/shenghsi/MarkDew)（个人账号；**前身 `shenghsi/MarkBend`**） |
| 描述定位 | 桌面 Markdown 编辑器，`TARGET.md` 以 **Typora** 为参照物 |
| 许可证 | **双许可**：开源/个人 **AGPL-3.0-or-later**；**商业使用需商业许可**（`LICENSE` 前 24 行为双许可声明，之后为 AGPL 全文；API 返回 `NOASSERTION`） |
| 版权署名 | `LICENSE` 载明 Copyright (C) 2026 **Sheng-Hsi `<shengehsi@gmail.com>`** |
| 主语言 | TypeScript（`package.json` `name: markdew-desktop`，`version: 1.2.7`，`private: true`，`type: module`） |
| 关注度 | **19 Star、1 Fork、0 关注、1 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-10-22 创建**；`pushed_at` 2026-11-01；默认分支 **`main`**；**贡献者列表为空（API 未返回）** |
| 版本节奏 | 最新 **v1.2.7**（2026-11-01，18 个资产）… v1.1.1 |
| 体积 | API `size` 3,609 KB（约 3.5 MB）；目录树 625 项 / **592 个 blob / 14,964 KB** |
| 关键文件 | `BENCHMARKS.md`(403,271 B)、`ROADMAP.md`(169,620 B)、`LICENSE`(35,451 B)、`TARGET.md`(5,579 B) |

## 它能做什么（结构清单）

- **编辑内核**：CodeMirror 6 + Tauri WebView；`TARGET.md` 的源真值规则为"Markdown 字节序列是唯一保存表示"
- **解析 / 扩展**：`markdown-it`、`mdast-util-*`、`micromark-extension-*`、`parse5`、`dompurify`、`@mathjax/src`（公式）
- **脚本**（`package.json`）：`dev`（`vite --host 127.0.0.1`）、`build`（`tsc --noEmit && vite build`）、`test`（node --test + vitest）、6 个 benchmark 脚本、`desktop` / `desktop:build`
- **安装**：Linux/macOS `curl … markdew-installer.sh | sh`；Windows `irm … markdew-installer.ps1 | iex`；或下载 Release 包（macOS DMG / Linux DEB / Windows EXE）
- **目录**：`src/web/`、`src/desktop/`（源码）；`tests/`（434 个文件）；根目录一批大体量 Markdown 文档

## 使用前需要注意

- **许可证是"AGPL + 商业许可"双轨**：开源/个人可用，但**嵌入专有软件或提供托管服务想规避 AGPL 传染时需购买商业许可**；企业使用前务必读 `LICENSE` 前 24 行。
- **安装脚本形式**：与许多项目一样采用 `curl | sh` / `irm | iex`；执行前建议先审阅脚本（本笔记**未运行**）。
- **项目很新、贡献者信息缺失**：2026-10-22 创建、19 Star，属于早期项目；API 未返回贡献者，实际产能与维护持续性待观察。
- **`BENCHMARKS.md` / `ROADMAP.md` 体量大但未核**：本笔记只记录其体量，**未阅读其内容、未复现任何基准**。
- **改名史**：检索资料时注意 `MarkBend` 旧名；`Bend` 编译器已不在应用内。

## 什么时候会再看

- 需要一个**以 Typora 为标杆、开源可自建的桌面 Markdown 编辑器**时；
- 想参考"**把产品目标、参照物与源真值规则写进 `TARGET.md`**"这种"先定目标再动手"的做法时；
- 想研究"**AGPL + 商业双许可**"声明该怎么写（同时满足开源与商用授权）时；
- 想对照"**CodeMirror 6 系 + Tauri**"这类桌面编辑器的技术组合时。

## 相关笔记

- **同属"Markdown / 文档"这条线**：[markitdown：文件转 Markdown 工具](markitdown-文件转Markdown工具.md)——那是把各种文件**转成** Markdown；本篇是**编辑与产出** Markdown。一个在入口，一个在出口，可对照文档工作流的两端。
- **同为"桌面写作软件"**：[easy-writing：易创网文写作软件](easy-writing-易创网文写作软件.md)——那是本地优先的网文写作软件（BYOK 接模型）；本篇是通用 Markdown 编辑器。两者对照"**面向特定体裁的写作工具**"与"**通用文档编辑器**"的差异。
- **同为"个人知识 / 文档桌面应用"**：[llm_wiki：会自己长大的个人知识库桌面应用](llm_wiki-会自己长大的个人知识库桌面应用.md)——那是带 AI 的知识库桌面应用；本篇是纯编辑器。可对照"**编辑器**"与"**知识库应用**"在桌面端的取舍。
- 本页索引位置：[GitHub 项目收藏](README.md)
- **同为"Tauri 桌面 Markdown 编辑器"，许可证与技术内核相反**：[wiwme：轻量级本地 Markdown 编辑器](wiwme-轻量级本地Markdown编辑器.md)——本篇是**开源（AGPL-3.0-or-later + 商业双轨）、源码在仓库、CodeMirror 6、跨平台**；那篇是**专有 EULA、仓库不含完整源码、Tiptap、仅见 Windows**。两篇合看正好是编辑器类项目"开源可改"与"专有只发成品"两个极端。

## 参考来源

- 仓库主页：https://github.com/shenghsi/MarkDew （核验日期 2026-11-04）
- README（中英）、`LICENSE`（双许可声明段）、`package.json`、`TARGET.md` 开头、目录树统计（核验日期 2026-11-04）
- GitHub API：`repos/shenghsi/MarkDew`、`git/trees/main?recursive=1`、`releases`、`contributors`（核验日期 2026-11-04）
