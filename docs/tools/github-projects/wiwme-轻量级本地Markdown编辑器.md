# wiwme：Tauri 内核的轻量级本地 Markdown 编辑器（专有 EULA，仓库不含完整源码）

> 它值得记的点**不在功能**（本地直编 `.md`、所见即所得、多主题、便携单文件，同类编辑器里并不稀奇），而在于**两条容易看走眼的事实**：**① 它不是开源软件——`LICENSE` 是一份中文"最终用户许可协议（EULA）"，明确禁止修改、衍生、逆向与转售，只允许"原样分发 + 使用"**；**② 它的 GitHub 仓库只有 15 个文件、不含可构建的工程源码**（没有 `package.json`、`Cargo.toml`、`src-tauri/`、`index.html`），只放了 README、EULA、5 套主题和零星片段——**发行靠 Release 里的绿色压缩包，不是"源码仓库"**。**"看到 GitHub 仓库"就默认"开源、可自建"，在这个项目上会直接错。**

- 状态：草稿
- 最近核验：2026-11-12（阅读 README 全文、`LICENSE`（中文 EULA）全文、`main` 分支完整文件树（15 项）、`translate/lang.js` 开头、一份主题 `manifest.json`、Release 0.3.0 资产列表与元数据；**未下载、未安装、未运行、未打开任何 `.md`、未构建、未审计二进制**）
- 适用范围：**仅见 README 列出 Windows**（**Win10 1803+ / Win11**，需 **WebView2** 运行时）；README 写"各平台最低运行版本如下"但**只列了 Windows 一栏**，**macOS / Linux 是否支持未在 README 中出现（未核验）**；无需注册、无需建库，绿色便携

## 核心结论

**一句话定位（README 自述）**：「一款专注于本地文件编辑、界面精致、开箱即用的 Markdown 编辑器。无需注册、无需建库、绿色便携，只为专注写作而生。」基于 **Tauri 内核**开发。

**三条最需要先记清的事实：**

1. **它是专有软件，不是开源项目（事实，来自 `LICENSE`）**：`LICENSE` 全文是一份中文《[wiwme] 最终用户许可协议（EULA）》，版本 1.0、生效日期 **2026-07-09**，版权方署名 **王怿武**。GitHub 的许可证识别结果为 **`NOASSERTION`（Other）**——**因为它不是任何标准 SPDX 开源协议**。
2. **仓库不是"可构建的源码仓库"（事实，来自文件树）**：`main` 分支**只有 15 个文件**，`src/` 下**只有 1 个文件**（`src/TiptapSearchExtension.js`）。**无 `package.json` / `Cargo.toml` / `tauri.conf.json` / 前端入口文件**——我逐一探测这些常规工程文件，**均 404**。**因此"从仓库自行构建"这条路径走不通**；软件以 Release 里的成品包分发。
3. **它用的是 Tauri + Tiptap 技术栈（事实，来自 README 与 EULA 的第三方声明）**：EULA 第 6 条列明内置第三方组件——**Tauri**（MIT / Apache-2.0 双许可）、**Tiptap**（MIT）、**RemixIcon**（Remix Icon License v1.0）。（这一点与已收录的 [MarkDew](MarkDew-Typora风格的桌面Markdown编辑器.md) 同用 Tauri，但编辑内核不同：**MarkDew 用 CodeMirror 6，本篇用 Tiptap**。）

**取舍提醒**：如果你的目的是"**找一个能长期自建、能改、能嵌进自己产品的 Markdown 编辑器**"，**本项目从许可证到仓库构成都不适合**（EULA 禁止修改/衍生/逆向，且仓库无源码）；如果你的目的是"**拿一个 Windows 上开箱即用、体积小、能换主题的本地 Markdown 写作工具**"，它才落在这个点上。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [qytwyw/wiwme](https://github.com/qytwyw/wiwme) |
| 描述 | Markdown Editor——轻量级本地 Markdown 编辑器 |
| 归属 | 个人账号 `qytwyw`（页面 `isOrg: false`、`isFork: false`、`isMirrored: false`、`isArchived: false`） |
| 许可证 | **专有 EULA（非开源）**：GitHub 页面 `license.spdxId = NOASSERTION`、`name = Other`；`LICENSE` 首行「**[wiwme] 最终用户许可协议（EULA）**」，版本 1.0、生效日期 **2026-07-09**、版权方**王怿武** |
| 主语言 | 页面主语言值 **CSS 51.6%**（取自 shields.io `languages/top`）；**完整语言占比未取到**（GitHub 语言分栏的 AJAX 接口在本环境返回 404）。**推测其占比偏高与仓库内容以主题 `theme.css` 为主有关（推断，非 README 自述）** |
| 关注度 | **198 Star / 10 Fork / 0 关注 / 6 开放 Issue**（Star、Fork、关注：GitHub 页面内嵌值与 ungh.cc 镜像**一致**，2026-11-12；Issue 数取自 shields.io `issues`） |
| 维护状态 | **2026-07-09 创建**；ungh 报 `pushedAt` **2026-08-21**（距核验日约 3 个月）；页面 `updatedAt` 2026-11-08 |
| 默认分支 | `main` |
| 版本 | **13 Release / 13 tags**（页面内嵌值）；最新 **0.3.0**，标题「**0.3.0 数学公式**」，发布于 **2026-08-21** |
| 仓库体积 | **`size` 未取到**（GitHub API 匿名配额用尽，ungh 镜像亦不返回） |
| 贡献者 | **未取到 / 未核验**（页面 `contributors: true` 但贡献者统计需登录态接口，本环境未取得） |
| 仓库文件数 | **15 个文件**（`main` 分支完整文件树） |
| 话题标签 | `editor` · `markdown` · `rich-text` · `wiwme` · `wysiwyg` · `wysiwyg-editor` |

## 它能做什么（README 自述的能力清单）

- **本地文件直编**：直接打开与编辑本地 `.md` 文件，**无需创建项目或仓库**。
- **所见即所得**：基于 **Tiptap 内核**的实时渲染编辑；配工具条与右键菜单。
- **双面板编辑**：左右 / 上下两种布局，标签页可在面板间拖拽移动。
- **自动缓存保护**：按固定频率自动缓存正在编辑的内容，异常退出后再打开可恢复。
- **双模式目录栏**：① 传统标题目录（含内容层级）；② 精简大纲（只显示标题骨架）。
- **笔记本风格背景**：网格 / 横线 / 点阵三种书写背景。
- **可视化设置面板**：字体、间距、背景、快捷键行为等可在界面里调整，无需改配置文件。
- **标签页状态标识**：已保存 / 已修改未保存 / 新建空白 / 新建且已修改，用颜色区分。
- **侧边栏速查**：快捷键速查表 + Markdown 语法手册。
- **文件收藏夹**：常用文件一键打开。
- **多格式导出**：HTML、JSON、纯文本（README 称后续规划 PDF、Word）。

**README 自述的路线规划（未完成项）**：完善多语言支持、链接处理（文件链接与网址）、完善主题支持、PDF / Word 导出、更多笔记本背景模板、插件扩展系统。

## 主题系统（仓库里唯一"看得见"的源码部分）

仓库的 `themes/` 目录随仓附带 **5 套主题**（每套含 `manifest.json` + `theme.css`），目录名为中文：

`经典豆沙绿` · `奶油杏茶` · `暖调米白` · `莫兰迪冷灰` · `暗夜松绿`

一份样本 `manifest.json`（`奶油杏茶`）字段如下（**原文照录，其中 `author` 署名为"豆包修订"**）：

```json
{
  "scheme": "light",
  "primary": "#6B82A8",
  "bg": "#F0E8DA",
  "fg": "#403B36",
  "version": "v1.2.0",
  "author": "豆包修订",
  "desc": "温暖柔和的视觉氛围，适合沉浸式长文阅读。"
}
```

README 的"自定义主题"说明：以程序目录 `themes` 文件夹中的主题为模板改 CSS，可自用，鼓励提交到仓库分享（要求在说明文件里留作者名）；主题可自定义配色，**"后续甚至可以直接修改 UI"**；加载后在「设置 → 外观」中生效。`translate/lang.js` 是一份 JS 导出对象（`export const zh = { … }`），承载界面文案（语言、文件、编辑、查找替换等），说明其界面文案与语言包是外置的。

## 仓库构成：只有 15 个文件，且不含可构建工程（事实）

`main` 分支完整文件清单（共 15 项）：

| 目录 / 文件 | 说明 |
| --- | --- |
| `README.md` | 项目说明（2,207 字符） |
| `LICENSE` | 中文 EULA（2,608 字符） |
| `capabilities/default.json` | Tauri 权限声明文件 |
| `src/TiptapSearchExtension.js` | `src/` 下**唯一**文件（Tiptap 搜索扩展） |
| `translate/lang.js` | 界面文案 / 语言包（约 9,982 字符） |
| `themes/…` | 5 套主题 ×（`manifest.json` + `theme.css`）= 10 个文件 |

**我逐一探测以下常规工程文件，在 `main` 分支均为 404**：`package.json`、`Cargo.toml`、`src-tauri/tauri.conf.json`、`index.html`、`src/main.js`、`src/app.js`、`src/main.ts`、`vite.config.js`。**结论（事实）**：仓库**尚未开源完整工程**，**只公开了 README、EULA、主题包与少量源码片段**；**软件本体通过 Release 成品包分发**。

**Release 0.3.0 资产（补一条事实）**：资产为 **`wiwme.0.3.0.zip`（4.73 MB）**，附 sha256。**注意口径差异**：README 称"程序仅 **11 MB** 左右"（指解压后的程序），而**发行的压缩包为 4.73 MB**——**两个数字口径不同（11 MB 是程序体积自述，4.73 MB 是下载包实测值），不要混为一谈。**

## 使用前需要注意

- **⚠️ 它不是开源软件，这是最硬的边界（事实，来自 `LICENSE`）**：`LICENSE` 是**中文专有 EULA**——第 3 条明列**禁止修改衍生（3.1）、禁止逆向工程（3.2）、禁止转售牟利（3.3）、禁止篡改标识（3.4）**；违约则授权**自动终止**。**可以做的**是"免费下载安装、个人或商业内部场景运行、以及原样完整分发（第 2 条）"。**换句话说：能用、能传，但不能改、不能反编译、不能拿它做二开或卖。** 把它当"开源项目"收藏、或计划基于它做二次开发，都**与许可条款冲突**。
- **⚠️ 仓库没有完整源码，别指望"自行构建"**：如上一节所述，`main` 分支仅 15 个文件、无构建入口。**"仓库存在"≠"源码开放"**，此项目尤其如此。**软件行为的可审计性因此受限**——README/EULA 之外，**编辑器内部实现我无法从仓库验证**。
- **⚠️ 免安装、便携的另一面是"权限与驻留"需自行留意**：README 强调"所有配置与数据均保存在程序目录内"**（自述）**。便携意味着它可能在多个位置落盘缓存（README 提到自动缓存与恢复）。**.md 文件为本地直编**，**编辑前请自行备份**；对重要文档，我不会只依赖它一家的自动缓存（其恢复机制未核验）。
- **⚠️ 平台支持面窄且描述含混**：README 写"各平台最低运行版本如下"，**实际只列了 Windows**（Win10 1803+ / Win11，需 WebView2）。**macOS / Linux 是否有包、是否能跑，未在 README 说明（未核验）**；进而在非 Windows 环境下"能不能用"**暂时按不可用来假设**更稳妥。
- **⚠️ 项目很新、Star 数相对其"文件数"显得偏高（事实 + 推断）**：**2026-07-09 创建**，**198 Star / 10 Fork / 6 开放 Issue**，ungh 报 `pushedAt` **2026-08-21（距核验日约 3 个月未推代码）**。**198 Star 对"15 个文件 + 专有 EULA"的仓库而言关注度不算低（我的观察）**，**但社区沉淀薄、贡献者与 `size` 均未取得**——**别把它当"久经检验"**；"6 个开放 Issue"也说明**已知问题没有公开的收敛过程**。
- **⚠️ 主题的 `author` 署名为"豆包修订"**：仓库随附主题的 `manifest.json` 里 `author` 写的是 **"豆包修订"**（某 AI 助手名）。**这只说明该主题文件的署名，不能据此判断软件本体的开发方式（未核实）**——我**未核验**其余主题或软件本体的作者与来源，引用时不要过度推断。
- **⚠️ 我未做任何运行验证**：**未下载、未安装、未运行、未打开任何 Markdown 文件、未构建、未审计二进制**；上述功能、主题格式、平台要求、体积口径**均来自 README、`LICENSE` 与仓库文件自述**。GitHub API 匿名配额用尽，**`size` 与贡献者统计未取到**。

## 什么时候会再看

- 需要给 Windows 找一款**开箱即用、免安装、体积小、可换主题**的本地 Markdown 写作工具时（**前提是接受其专有 EULA 与仅 Windows**）。
- 想参考"**Tauri + Tiptap**"这条桌面编辑器技术路线（对比已收录 [MarkDew](MarkDew-Typora风格的桌面Markdown编辑器.md) 的 **Tauri + CodeMirror 6**）。
- 想研究"**用 GitHub 只发发行包、不发源码**"的分发模式，以及**中文专有 EULA** 的条款写法（第 2–3 条：允许原样分发、禁止衍生/逆向/转售）。
- 想参考**外置主题包**的约定（`manifest.json` 字段：`scheme` / `primary` / `bg` / `fg` / `version` / `author` / `desc` + `theme.css`）与**外置语言包**（`translate/lang.js`）的组织方式。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"Tauri 桌面 Markdown 编辑器"，但许可证取向与技术内核都相反**：[MarkDew：以 Typora 为参照物的桌面 Markdown 编辑器](MarkDew-Typora风格的桌面Markdown编辑器.md)——MarkDew 是**开源（AGPL-3.0-or-later + 商业双轨）**、**源码在仓库**、用 **CodeMirror 6**、跨平台（Win/macOS/Linux）；本篇是**专有 EULA**、**仓库无完整源码**、用 **Tiptap**、**仅见 Windows**。**两篇合看正好覆盖"编辑器类项目"的"开源可改 vs 专有只发成品"两个极端。**
- **同在 Markdown 生态，但方向相反（工具链的一头一尾）**：[markitdown：文件转 Markdown 工具](markitdown-文件转Markdown工具.md)——那篇解决"**把各种文件转成 Markdown**"（输入端）；本篇解决"**在本地编辑 Markdown**"（编辑端）。**一个负责进、一个负责编**，可对照记录 Markdown 工具链的不同环节。

## 参考来源

- 项目仓库与 README：https://github.com/qytwyw/wiwme
- 许可（EULA）：https://github.com/qytwyw/wiwme/blob/main/LICENSE
- 主题包目录：https://github.com/qytwyw/wiwme/tree/main/themes
- 语言包：https://github.com/qytwyw/wiwme/blob/main/translate/lang.js
- 最新 Release 0.3.0：https://github.com/qytwyw/wiwme/releases/tag/0.3.0
- 元数据（Star / Fork / 时间 / 默认分支 / Release 与 tag 数）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12
- 主语言与开放 Issue 数：shields.io `languages/top` 与 `issues` 徽章，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**本次读取的是 README 全文、`LICENSE`（中文 EULA）全文、`main` 分支 15 项文件树、`translate/lang.js` 开头、一份主题 `manifest.json` 与 Release 0.3.0 资产列表；未下载、未安装、未运行、未打开任何 `.md`、未构建、未审计二进制。** 功能、主题格式、平台要求与体积口径均来自 README、`LICENSE` 与仓库文件自述；**`size` 与贡献者统计因 GitHub API 匿名配额用尽未取到**；**完整语言占比未取到**（页面语言分栏接口在本环境不可用）；**项目为非开源专有软件（EULA），禁止修改与衍生**；**macOS / Linux 支持情况未核验**。
