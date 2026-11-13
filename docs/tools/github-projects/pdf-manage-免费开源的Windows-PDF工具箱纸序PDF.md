# 纸序 PDF（pdf-manage）：免费开源的 Windows PDF 工具箱

> 一个 **Windows 桌面 PDF 工具箱**：阅读、页面整理、批注、PDF 对比、打印、格式转换，外加**可选的离线 OCR**；定位是"**本机处理、无需登录**"。它值得记的两点：一是把**多功能的边界**（对比是实验性、涂黑不适用于安全脱敏、页面处理会丢可编辑性）在 README 的 "Before You Use It" 里逐条写清；二是它的**许可边界比 `LICENSE` 文件复杂**——项目代码 MIT，但发行版捆绑了 AGPL / GPL 组件，不能只看 MIT。

- 状态：草稿
- 最近核验：2026-11-13（阅读 GitHub 页面内嵌数据、ungh.cc 元数据与 `main` 文件树、README 全文、`LICENSE`；**未下载安装包、未运行、未从源码构建、未测试任何 PDF 处理 / OCR / 对比功能**）
- 适用范围：**Windows x64**（安装版与绿色便携版均**不需要单独安装 Python**）；从源码运行需 **64 位 Python**（README 推荐 3.12）。部分功能需**本机安装 Microsoft Word**（Word→PDF 与 Word reflow；PDF 导入需 Word 2013+）；离线 OCR 需**单独下载 OCR 附加包**。**核验时未验证 macOS / Linux 支持（README 只提 Windows）。**

## 核心结论

**一句话定位（README 自述）**："A free, open-source PDF toolbox for Windows."——阅读与页面整理、批注、扫描清理、PDF 对比、打印与格式转换，**文档在本机处理、无需账号**。

**三个最值得记的点：**

1. **"本机处理"但有明确的联网出口**：文档**不上传**（README 明说 "Documents are not uploaded"），但**自动更新检查默认开启**（≤ 每 24 小时一次、可在 About 关闭），手动更新检查 / Release notes / 组件下载需联网。**"离线"主要指文档处理，不等于整个应用零联网**——这点值得记清。
2. **许可边界比 `LICENSE` 文件复杂得多**：仓库 `LICENSE` 是 **MIT**（项目**自有源码**），但 README 明确：**Windows 发行版捆绑了 PyMuPDF / MuPDF（AGPL-3.0）与 PyQt6（GPL-3.0）等组件，"整个发行版并不只受 MIT 覆盖"**；无论付费或免费、修改与否，再分发都须遵守相应 AGPL / GPL 条款（保留许可与版权声明、按需提供对应源码与构建材料）。**引用"它是 MIT"时，必须注明那只指项目自有代码。**
3. **把每个功能的边界写进 "Before You Use It"**：例如 PDF 对比是**实验性**且"**未找到差异不等于两份文件相同**"；涂黑 / 马赛克功能**不适用于安全脱敏**（README 明确"模糊或像素化内容可能仍可辨认，敏感区域请用实心填充"）；扫描增强会**生成无文本层的图像页**；拆分单页与区域导出会**丢失标注 / 表单的可编辑性与链接**；水印 / 跨页图章只是图像、**不提供数字签名或签名验证**。**这种"先讲清不能做什么"的写法，是这类综合工具箱少见的诚实之处。**

**个人判断（未验证）**：它功能面很宽（读 / 整理 / 批注 / 对比 / 扫描 / OCR / 转换 / 打印 / 水印），适合"想用一个本机工具替代多款在线 PDF 站点"的 Windows 用户；但**功能越多、边界越需要逐条确认**，且**许可上捆绑 AGPL / GPL 组件**，若涉及商用或再分发必须先读许可说明，不能只看仓库那颗 MIT 标记。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [d8349565/pdf-manage](https://github.com/d8349565/pdf-manage) |
| 描述 | "ZhiXu PDF: a free, open-source Windows PDF toolbox for reading, organizing, annotating, comparing, printing and converting PDFs, with optional offline OCR. Local processing, no sign-in.｜纸序 PDF：免费开源的 Windows PDF 工具箱，支持阅读、页面整理、批注、对比、打印、格式转换及可选离线 OCR。本机处理，无需登录。" |
| 归属 | 账号 `d8349565`（**个人用户**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false`、`isTemplate: false` |
| 许可证 | **MIT（仅项目自有代码）**（页面 `spdxId: MIT`；`LICENSE` 文件首行 `MIT License`，署名 `Copyright (c) 2026 d8349565`）。**但 README 明确：Windows 发行版捆绑 PyMuPDF / MuPDF（AGPL-3.0）与 PyQt6（GPL-3.0）等组件，整个发行版并不只受 MIT 覆盖**——**发行版的许可边界以 README 的 `License` 段与 `packaging/licenses/` 为准** |
| 主语言 | **Python 98.8%**（shields.io，2026-11-13） |
| 技术栈 | **Python 桌面应用（PyQt6 界面）**；`app/` 下含 `ocr/`（OCR 子模块）、`pdf2excel/`（表格导出）、`locales/`（8 种界面语言：英 / 简中 / 繁中 / 日 / 韩 / 德 / 法 / 西）；`packaging/` 为打包（Inno Setup `.iss`）与第三方许可清单（`licenses/` 含 AGPL-3.0、GPL-3.0、LGPL-3.0、MuPDF-NOTICE、THIRD-PARTY 等）；根目录 `requirements.txt`、`build.bat`、`run.bat`、`pdf-manage.spec` |
| 关注度 | **212 Star / 16 Fork / 1 Watcher / 0 开放 Issue**（**页面值**，2026-11-13；**与 ungh.cc 镜像一致，均 212 Star / 16 Fork / 1 Watcher**。Issue 数取自 shields.io） |
| 维护状态 | **2026-10-10 创建**；页面 `updatedAt` **2026-11-12**、ungh `pushedAt` **2026-11-11**（距核验日约 1–2 天，**活跃**）；已累计 **27 Release / 58 tags** |
| 默认分支 | `main` |
| 版本 | 已发布多个版本（README 提到 v0.7.5 的 75 秒演示视频），Release 页提供**安装版** `ZhiXuPDF-Setup.exe` 与**绿色便携版** `ZhiXuPDF-Portable-vX.Y.Z.zip`，OCR 为**单独下载的附加包** |
| 话题标签 | 无（页面 `topics` 为空） |
| 体积 | `main` 文件树约 **328 个文件**（ungh.cc files API） |
| 仓库结构 | `app/`（约 120 个 Python 模块）· `docs/`（`USAGE.md`、`OCR.md`、`COMPRESSION.md`、`PDFCOMPARE.md`、`DEVELOPMENT.md`、`REPOSITORY.md`、`LOCALIZATION.md` 等，多为中文）· `packaging/`（打包脚本 + 第三方许可）· `README.md`（英文）与 `README.zh-CN.md`（简中） |
| 官网/文档 | 仓库内文档为主（用户指南 / OCR / 对比等指南目前为中文） |

## 主要功能（README 自述）

| 类别 | 能做什么 |
| --- | --- |
| 阅读 | 标签页打开 PDF 与 OFD；恢复最近文档；单页 / 双页 / 四页布局；搜索、书签导航、调整阅读配色、全屏 / 无边框模式 |
| 页面整理 | 合并文件或标签；拆分、提取、插入、拖动重排、复制粘贴页面；调整纸张尺寸、拆分单页 |
| 批注与提取 | 高亮、便签、手绘与形状；插入文本框与图片；单行文字与样式修正；遮挡区域、分享批注图、提取页面区域 |
| PDF 对比（实验性） | 对比两份已保存 PDF、查看文本变更、跳回原页；可选检查版式与图形；扫描页可配离线 OCR |
| 扫描 | 清理背景、裁切纸张、拉直页面、矫正倾斜、去除彩色笔迹；装离线 OCR 后可搜索、复制文本、导出可编辑文档 |
| 转换 | Word、图片、Markdown → PDF；PDF → Word、Excel、TXT、图片 |
| 打印 | 每张多页、灰度、单双面、奇偶页；带左右装订的折页拼版与正反面分次打印 |
| 文档工具 | 压缩 PDF 与图片；管理密码与权限；检查签名与保护状态；页码、文字 / 图片水印、跨页图章、逐页图章 |

**OCR 与 Word 的依赖（README 自述）**：默认的"保持版式" PDF→Word **不需要 Word**；**扫描页需离线 OCR**；**额外的 Word reflow 选项与 Word→PDF 需要本机安装 Microsoft Word**，**PDF 导入需 Word 2013 或更高**。

## 使用前需要注意

- **⚠️ 许可不是单纯的 MIT**：仓库 `LICENSE` 为 MIT，但 **Windows 发行版捆绑 PyMuPDF / MuPDF（AGPL-3.0）与 PyQt6（GPL-3.0）等**，**整个发行版不只受 MIT 覆盖**。再分发（无论付费或免费、修改与否）须遵守相应 AGPL / GPL（保留许可与版权声明、提供对应源码与构建材料），若无法满足需另行取得权利人授权。新构建在 `_internal/licenses/` 下附带完整许可文本、上游声明与 `SOURCES.txt`。**涉及商用或再分发，请先读 README 的 `License` 段与 `packaging/licenses/THIRD-PARTY.txt`。**
- **⚠️ 平台绑定 Windows**：README 只面向 **Windows x64**；Windows 11 上资源管理器右键命令可能位于"**显示更多选项**"下；**未提及 macOS / Linux 支持**。
- **⚠️ 需额外下载 / 额外依赖**：**OCR 是独立的附加包**（从 Release 下载 `ZhiXuPDF-OCR-win-x64.zip` 后在 OCR 对话框导入）；**Word reflow 与 Word→PDF 需本机安装 Microsoft Word**。
- **⚠️ 有联网出口**：**自动更新检查默认开启**（≤ 每 24 小时一次，可在 About 关闭）；手动检查 / 下载组件需联网；README 明确**文档不上传**。离线诉求强的用户应先关闭自动更新。
- **⚠️ 功能边界要逐条读**（README "Before You Use It"）：**PDF 对比是实验性**，"未发现差异不等于两份文件相同"；**涂黑 / 马赛克不适用于安全脱敏**（模糊内容可能仍可辨认，敏感信息请用实心填充并另行检查其他页、附件与元数据）；**扫描增强生成无文本层的图像页**；**拆分单页与区域提取只保留标注 / 表单的外观、不可再编辑，链接不再可点**；**水印 / 跨页图章是图像、不提供数字签名或签名验证**；只管理"由纸序 PDF 添加的水印"；带签名 PDF 只读，重要文件请先另存副本。
- **⚠️ 缓存与设置位置**：绿色便携版把设置、缓存与默认 OCR 组件存于自身 `Data` 文件夹，需连同 `_internal` 与 `portable.ini` 一起放在可写目录；**直接复制单个 EXE 或在 ZIP 内运行会启动失败**。
- **我未做任何运行验证**：**未下载安装包、未运行、未从源码构建、未测试任何 PDF 处理 / OCR / 对比 / 打印功能**；上述功能、依赖、许可边界与注意事项**均来自 README、`LICENSE`、文件树与元数据自述**。

## 什么时候会再看

- 需要一个**本机处理的 Windows PDF 工具箱**（阅读 + 整理 + 批注 + 对比 + 扫描 OCR + 转换 + 打印），且不愿用在线站点、不想登录时。
- 想参考它**把"每个功能不能做什么"写进 `Before You Use It`** 的文档做法（尤其 PDF 对比、涂黑、页面处理这类容易被误解的能力）。
- 研究**桌面应用如何把"项目自有代码 MIT + 捆绑 AGPL/GPL 组件"的许可边界讲清楚**（含 `packaging/licenses/`、`SOURCES.txt` 的做法）。
- 想借鉴**离线 OCR 作为独立可选包**（主程序与模型组件分离下载）的分发设计。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为 PDF 处理方向的工具（定位互补）**：[pdf-inspector：判断 PDF 类型并转 Markdown 的 Rust 库](pdf-inspector-判断PDF类型并转Markdown的Rust库.md)——一个是可嵌入的 Rust 库、一个是成品 Windows 桌面工具箱，关注点不同，可对照"库 vs 应用"的处理边界。
- **同为"把文档转成其他格式"的方向**：[markitdown：文件转 Markdown 工具](markitdown-文件转Markdown工具.md)——纸序 PDF 也提供 PDF↔Word/Excel/Markdown/图片 的转换，两篇合看是"轻量转换库"与"本地一站式工具箱"的对照。

## 参考来源

- 项目仓库与 README（英文）：https://github.com/d8349565/pdf-manage
- README（简体中文）：https://github.com/d8349565/pdf-manage/blob/main/README.zh-CN.md
- 许可（MIT，项目自有代码）：https://github.com/d8349565/pdf-manage/blob/main/LICENSE
- 第三方许可与分发说明：https://github.com/d8349565/pdf-manage/blob/main/packaging/licenses/THIRD-PARTY.txt
- 用户指南（中文）：https://github.com/d8349565/pdf-manage/blob/main/docs/USAGE.md
- OCR 说明（中文）：https://github.com/d8349565/pdf-manage/blob/main/docs/OCR.md
- PDF 对比说明（中文）：https://github.com/d8349565/pdf-manage/blob/main/docs/PDFCOMPARE.md
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 语言 / 默认分支 / Release 数 / 文件名）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未下载安装包、未运行、未从源码构建、未测试任何功能**；功能、依赖、许可边界均来自 README、`LICENSE`、文件树与元数据自述。
