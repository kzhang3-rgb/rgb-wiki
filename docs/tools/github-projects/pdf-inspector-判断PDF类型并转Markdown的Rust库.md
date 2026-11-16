# pdf-inspector：判断 PDF 是否需要 OCR、并把文本型 PDF 本地转 Markdown 的 Rust 库

> 它解决的不是"怎么把 PDF 转成文字"，而是**"先判断这份 PDF 值不值得走 OCR"**——用毫秒级分类把文本型 PDF（README 自述约 54%）在本地直接抽成带版式感知的 Markdown，只把真正需要 OCR 的页面送去走重管道。当你面对"成千上万份 PDF 要转文字、又不想每份都掏 OCR 成本"时值得再看。

- 状态：草稿
- 最近核验：2026-11-12（阅读 README、`LICENSE`、仓库文件树与元数据；**未 clone、未 `cargo build`、未安装任何语言包、未运行 benchmark、未跑任何测试**）
- 适用范围：Rust 库 + CLI（`pdf2md` / `detect-pdf`），并提供 Python、Node.js（napi-rs）、浏览器 WebAssembly 三套绑定；**默认构建是纯提取、不含 OCR**，只有显式启用 `--features ocr`（Rust/CLI）或使用原生 Python/Node 包时才涉及 OCR，且**仍需另行安装 PDFium 与 ONNX Runtime**。

## 核心结论

**一句话定位（README 自述）**：面向**大规模 PDF 处理管道**的快速 Rust 库——先分类（`TextBased` / `Scanned` / `ImageBased` / `Mixed`），再抽取，再转 Markdown。

**它最值得记的三点：**

1. **价值在"路由"而非"转换"**：README 把它定位成"给管道做分流"的组件——PDF 进来先花约 20ms 分类，是文本型就本地抽取（约 150ms），否则才送 OCR 服务（2–10s）。**这是它区别于普通 PDF 转 Markdown 工具的定位。**
2. **默认轻量、不背 OCR 依赖**：默认 Rust 与浏览器构建**纯提取**；PDFium、ONNX Runtime 与模型文件都是**外部依赖，只在有页面被路由到 OCR 时才触及**（README 自述）。**换句话说，纯文本 PDF 的使用者不必装 OCR 那一套。**
3. **一份文档只解析一次**：文档经 `load_document_from_path` / `load_document_from_mem` **加载一次**，在检测与抽取阶段共享，避免重复 I/O 与重复解析（README 自述）。

**一个需要打问号的点**：README 自带的 benchmark 显示它在 `opendataloader-bench`（200 份 PDF）上综合分 0.875、速度 0.470s，**全面优于 liteparse / opendataloader / pymupdf4llm / markitdown**；但**这是项目自述、自跑的结果（README 说结果于 2026-07-31 在 Apple M4 Pro 上刷新）**，评测脚本与结果在独立分支，**我未复核**，引用时请当作"项目自报"而非中立第三方结论。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [firecrawl/pdf-inspector](https://github.com/firecrawl/pdf-inspector) |
| 描述 | Fast Rust library for PDF inspection, classification, and text extraction. Intelligently detects scanned vs text-based PDFs to enable smart routing decisions. |
| 许可证 | **MIT**（`LICENSE` 1,066 字符，署名 `Copyright (c) 2026 Firecrawl`）。**注意**：仓库内另有 `external/bcmaps/LICENSE`（2,080 字符）与 `wasm/LICENSE`（2,707 字符），**这两份我未阅读，是否同为 MIT 或另有条款未核实。** |
| 主语言 | **Rust**（shields.io 取到 top language 为 `rust`，**95.9%**） |
| 关注度 | **页面值 20,000 Star / 1,360 Fork / 51 关注**（GitHub 页面内嵌数据，2026-11-12）；**镜像值 19,988 Star / 1,359 Fork**（ungh.cc，同日）——**两者不一致，此处记录页面值并注明镜像值** |
| 维护状态 | **2026-02-06 创建**；ungh 报 pushed **2026-10-22**（距核验日约 3 周）；页面 `updatedAt` **2026-11-10**。**活跃**。 |
| 体积 | `main` 文件树 **374 个文件**；`src/` 下多个单文件达 25 万字符级（如 `content_stream.rs` 约 264KB、`fonts.rs` 约 288KB、`lib.rs` 约 327KB），`CHANGELOG.md` 约 56KB |
| 话题标签 | `markdown` · `nodejs` · `ocr-routing` · `pdf` · `pdf-classification` · `pdf-extraction` · `pdf-parser` · `python` · `rust` · `text-extraction` |
| 仓库结构 | Cargo 工作区：根 `src/`（Rust 主库 + `bin/` 两个 CLI）、`napi/`（Node.js/Bun 绑定，napi-rs）、`wasm/`（浏览器绑定，wasm-bindgen）、`docs/`（rust-api / python / ocr-runtime / benchmarking / publishing / debugging）、`scripts/`、`tests/`（含大量 PDF fixture 与 `.md` 快照）、`external/bcmaps/`（内置一批 CJK 等 CMap 数据）、`site/`（文档站） |

## 它能做什么

- **分类**：采样内容流，约 **10–50ms** 判定 `TextBased` / `Scanned` / `ImageBased` / `Mixed`，返回 0.0–1.0 的置信度，并给出 `pages_needing_ocr`（逐页 OCR 路由），而非"整份走/整份不走"。
- **抽取**：位置感知（字体信息、X/Y 坐标）、自动多栏阅读顺序；旋转文本（页边章、图表轴标题）保留真实轴对齐框并报告 `rotation` 角度。
- **转 Markdown**：标题（按字号分层的 H1–H4）、项目符号/数字/字母列表、代码块（等宽字体识别）、表格、粗斜体、URL 链接与分页符。
- **表格识别**：双模式——基于绘图矩形的检测 + 基于文本对齐的启发式检测；覆盖财务表、脚注与跨页续表。
- **多语言/字体**：ToUnicode CMap 解码（Type0/Identity-H、UTF-16BE、UTF-8、Latin-1）、CJK、RTL 与双向文本、连字、编码损坏检测（以便回退 OCR）。
- **选择性 OCR**：Rust/CLI/Python/Node 可只渲染需要 OCR 的页面，本地跑 **PP-OCRv6 Small**，保留逐页来源(provenance)与"建议转托管管道"的提示。
- **浏览器 WebAssembly**：同一套 Rust 解析器可在浏览器 / Web Worker 本地运行，内置 CMap、无需服务端往返。

**对外入口**：`crates.io`（`cargo add pdf-inspector`，包名 `pdf-inspector`）、npm（`@firecrawl/pdf-inspector` 与 `@firecrawl/pdf-inspector-wasm`）、PyPI（`pdf-inspector`）。CLI 为 `pdf2md`（支持 `--json` / `--items-json` / `--raw` / `--compact` / `--pages` / `--select-pages`）与 `detect-pdf`（`--json` / `--analyze`）。

## 处理流程（README 自述架构）

```
PDF bytes
  ├─► detector  → PdfType（TextBased / Scanned / ImageBased / Mixed）
  └─► extractor
        ├─ fonts / content_stream / xobjects / links / layout（分栏→分行→阅读顺序）
        ├─ tables（矩形检测 union-find + 启发式 + 网格 + 格式化）
        └─ markdown（标题分层 / 预处理 / 行循环转换 / 分类 / 后处理）
```

**分类的取巧之处**：只解析 xref 表与页树（**不全量加载对象**），按策略抽样页面，检查内容流里有无 `Tj`/`TJ`（文字算子）与 `Do`（图像算子）——README 称据此可**毫秒级检测 300+ 页的 PDF**。抽样策略含 `EarlyExit` / `Full` / `Sample(n)`（默认 `Sample(8)`）/ `Pages(vec)` 四种。

## 使用前需要注意

- **⚠️ benchmark 是自报结果**：0.875 综合分、0.470s 速度、全面第一，**均来自项目自己的 README（2026-07-31 刷新）**，评测产物在独立分支。**我未复现、未独立验证**；对比对象的版本较旧（如 markitdown 0.1.5）。**别把它当权威横评。**
- **⚠️ OCR 依赖外部大件**：真要用 OCR，需**自行安装 PDFium 与 ONNX Runtime**（README 给了 `pdfium` / `onnxruntime` 的路径环境变量），模型文件亦外置。**"默认轻量"的好处，反过来说就是"要 OCR 就得自己配齐运行时"。**
- **⚠️ "约 54% PDF 不需要 OCR"是项目自述口径**：这个数字**缺乏出处说明、我未核验**，只能当作它的自我定位依据，不能当中立统计引用。
- **⚠️ 许可存在未核实部分**：主 `LICENSE` 是 MIT，但 **`external/bcmaps/LICENSE` 与 `wasm/LICENSE` 我未读**——内置的 CMap 数据集与他人代码可能带独立许可。**要再发布二进制时建议先核这两份。**
- **⚠️ `packages: false`**：页面 `sections.packages` 为 false——**该仓库本身不在 GitHub Packages 发布**（实际走 crates.io/npm/PyPI，README 与徽章可见）。
- **仓库规模与实现体量都很大（事实）**：374 个文件、单文件 20 万+字符的模块多个、`tests/` 内含大量真实 PDF fixture 与 Markdown 快照。**这说明测试投入不低，但也意味着代码审读成本高，"读过 README"远不等于"理解实现"。**
- **我未做任何验证**：**未 clone、未构建、未安装、未跑 CLI、未跑 benchmark、未跑测试**；上述功能、架构、依赖、benchmark **均来自 README 与仓库文件自述**。

## 什么时候会再看

- 需要给**大规模 PDF 管道做 OCR 路由**：先本地判类型、只对命中页走 OCR，参考它的分类策略与 `ScanStrategy` 设计。
- 需要**本地、低延迟地把文本型 PDF 转 Markdown**（含表格、多栏、RTL/CJK 场景），评估它相对 [`markitdown`](markitdown-文件转Markdown工具.md) 一类工具的取舍。
- 想参考**"一份文档只加载一次、检测与抽取共享"**这类性能纪律，以及把**Rust 库同时导出 Rust / CLI / Python / Node / WASM** 的工程做法。
- 想对比 **firecrawl 生态**：同一组织下 [`firecrawl`](firecrawl-把网站转成LLM可用数据的抓取API.md) 做网页抓取，本项目做 PDF 解析，可合看其"给 LLM 供数据"的拼图位置。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"把文档转成 LLM 可用的 Markdown"，路线相反**：[markitdown：文件转 Markdown 工具](markitdown-文件转Markdown工具.md)——markitdown 是**微软的 Python 多格式工具**（PDF/Office/图片/音频/YouTube），重覆盖面、可接 Azure 增强；本篇是**单点做 PDF 的 Rust 库**，重速度与 OCR 路由，且在这份自报 benchmark 里**分数与速度都优于 markitdown**（markitdown 0.589 / 16.165s，数据为项目自述）。
- **同一组织（firecrawl）下的互补组件**：[firecrawl：把网站转成 LLM 可用数据的抓取 API](firecrawl-把网站转成LLM可用数据的抓取API.md)——**一个是"抓网页"、一个是"解析 PDF"**，都服务于"把异构内容喂给 LLM"；本篇 README 明确标注 "Built by Firecrawl"。
- **同属 PDF 处理，定位互补**：[pdf-manage](pdf-manage-免费开源的Windows-PDF工具箱纸序PDF.md)——那是面向 Windows 的成品 PDF 工具箱（阅读 / 整理 / 批注 / 对比 / 打印 / 转换 + 可选离线 OCR），本篇是给 PDF 管道做 OCR 路由的 Rust 库；一个给人用、一个给程序用。
- **同属 PDF 处理，方向不同**：[docuseal](docuseal-开源自托管电子签名与PDF表单平台.md)——那是自托管的电子签名与 PDF 表单平台（AGPL-3.0 + Section 7(b) 附加署名条款）；本篇是解析 / 分类 PDF，那篇是填字段与签署。
- **同属"把文档转成 Markdown / 可读结构"，层次不同**：[learn-from-materials：把 PDF 与书变成可测验学习网页的技能](learn-from-materials-把PDF与书变成可测验学习网页的技能.md)——本篇是**判断 PDF 类型再转 Markdown 的底层解析库**，那篇是**端到端的学习页生成 Skill**（可追溯知识库 + 学习网页）。可对照"**解析层**"与"**产品层**"。

## 参考来源

- 项目仓库与 README：https://github.com/firecrawl/pdf-inspector
- 项目文档站：https://firecrawl.github.io/pdf-inspector/
- 许可（MIT）：https://github.com/firecrawl/pdf-inspector/blob/main/LICENSE
- 语言占比：shields.io `github/languages/top` 接口，2026-11-12
- 元数据（Star / Fork / 时间 / 默认分支 / Release 数）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12
- 对外包：<https://crates.io/crates/pdf-inspector> ・ <https://www.npmjs.com/package/@firecrawl/pdf-inspector> ・ <https://pypi.org/project/pdf-inspector/>

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**未 clone、未构建、未安装、未运行、未跑 benchmark、未跑测试**；功能、架构、依赖与性能数据均来自 README 与仓库文件自述，benchmark 为项目自报结果，**未独立复核**。
