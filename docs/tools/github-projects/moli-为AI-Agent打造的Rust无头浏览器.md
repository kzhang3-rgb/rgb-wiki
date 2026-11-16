# moli：为 AI Agent 打造的 Rust 无头浏览器

> 一句话定位（README 自述）：**"Structure first. Pixels on demand."**——一个**用 Rust 从零写的独立浏览器内核**（"not a Chromium wrapper"），给 AI Agent 提供**完整的网页运行时而只按需做布局与绘制**。最值得记的三点：① **默认 DOM-first**——提取 HTML / Markdown、查 DOM、跑 JS、看网络与存储**不触发布局与绘制**，只有真要几何 / 截图 / 坐标输入时才现建一次布局；② **一个二进制同时说三种协议**（CDP、WebDriver Classic、WebDriver BiDi），**不需要**单独的 ChromeDriver / geckodriver / 浏览器；③ **双许可（Apache-2.0 或 MIT 二选一）**，README 自述在混合爬取测试中以远低的内存取得与 Chrome Headless 相近的成功率。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（417 行）、`LICENSE-APACHE`（11,357 字符）与 `LICENSE-MIT`（1,074 字符）与已核实元数据；**未 clone、未编译、未下载预构建二进制、未运行 `moli fetch` / `moli serve`、未跑任何 benchmark**）
- 适用范围：**Linux / macOS / Windows**；经 **CLI、CDP、WebDriver Classic、WebDriver BiDi** 使用；`--layout` 才开启真实布局 / 几何 / 截图 / 截屏

## 核心结论

1. **它不是 Chromium 的封装**（README 架构节）：由 Rust 自建，有自己的所有权与生命周期规则，依赖 `libcurl`（网络）、`html5ever`（HTML 解析）、`rusty_v8` / V8（JS）、Servo/Stylo（选择器、级联、计算样式）、Taffy + Parley（盒模型与文本布局）、AnyRender/Vello CPU + `usvg` + Rust 图像生态（软件渲染）。
2. **成本模型是它的核心差异化**：DOM 与样式是**唯一事实来源**；每次真实刷新才从该源建一棵临时工作树、按需产出一帧绘制快照，把规范几何冻结进**不可变、DOM 无关**的 `FrozenLayoutTree`，随后丢弃工作树、样式借用、布局缓存、诊断与绘制状态。README 明说它**没有**增量维护的布局树、损伤图（damage graph）、保留显示列表、GPU 合成器或常驻窗口。
3. **一个二进制、三种协议**：CDP、WebDriver Classic、WebDriver BiDi **共用同一内核与调度器**；Playwright 可经 `chromium.connectOverCDP("http://127.0.0.1:9222")` 直连。
4. **开源与托管分离**：moli 是 Lexmount 的**开源**无头浏览器，Lexmount Browser 是围绕它的**托管云运行时与控制面**；README 强调"**开源无头浏览器无需 Lexmount Browser 即可完整使用**"。

**个人判断（未验证）**：moli 押注的是"**大多数自动化任务其实只需要页面结构，不需要持续渲染的视觉世界**"。它的 benchmark 与"按需渲染"叙事自洽，但要注意**它的强项是爬取 / browser-use / 检索 / 评测 / 强化学习这类结构优先负载**；对**高保真 Canvas / WebGL / 媒体播放 / 像素级一致**有硬需求时，README 自己承认**不追求**（见"使用前需要注意"）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [lexmount/moli](https://github.com/lexmount/moli) |
| 描述 | "Best headless browser for AI agents. Lite, Fast, High-Compatibility. Built in Rust" |
| 许可证 | **双许可，二选一**：**Apache-2.0**（`LICENSE-APACHE` 11,357 字符）**或** **MIT**（`LICENSE-MIT` 1,074 字符，Copyright (c) 2026 Moli contributors）；`license-metadata.json` 记录机器可读默认值，`licenses/` 收集额外分发声明。**shields 的 license 徽章只报 Apache-2.0，勿据此误判为单一许可** |
| 关注度 | **ungh 23,460 Star / 50 Watcher / 660 Fork**；**页面 23,613 / 50 / 666**（2026-11-13）；shields 另报**开放 Issue 75、贡献者 29** |
| 维护状态 | **2026-08-10 创建**；pushed 2026-11-12、updated 2026-11-12（活跃）；默认分支 `main`；**Release：ungh 30 条（单页上限）/ 页面 36 条** |
| 形态 | 独立 **Rust 浏览器内核** + **CLI** + **统一自动化服务器**（CDP / WebDriver Classic / WebDriver BiDi） |
| 平台 | **Linux / macOS / Windows** |
| 关联项目 | 托管版 **Lexmount Browser**；基准 [Lexbench-Headless-Browser](https://github.com/lexmount/Lexbench-Headless-Browser)；指标 [lexmount/moli-metrics](https://github.com/lexmount/moli-metrics) |

## 功能与用法

### 1. 安装与快速开始（README）

给 AI Agent 的一条提示词（README 原文思路）：安装 `main/skills` 下的技能 → 按其指引下载最新预构建二进制 → 用 `moli-webfetch` 抓取 `https://example.com`。直接安装：

```sh
# Linux / macOS
curl --proto '=https' --tlsv1.2 -fsSL \
  https://github.com/lexmount/moli/releases/latest/download/moli-installer.sh | sh
```

```powershell
# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://github.com/lexmount/moli/releases/latest/download/moli-installer.ps1 | iex"
```

### 2. CLI：抽取与截图

```bash
moli fetch --dump markdown --wait done https://example.com                        # 渲染为 Markdown
moli fetch --dump semantic_tree_text --wait-selector body https://example.com     # 紧凑语义树
moli fetch --layout --dump screenshot https://example.com > page.png              # 视口 PNG
moli fetch --layout --dump screenshot_full https://example.com > full-page.png    # 整页 PNG
moli fetch --layout --dump pdf https://example.com > page.pdf                     # 分页 PDF
moli fetch --eval 'document.querySelector("button.show-details").click()' \
  --dump markdown https://example.com    # 显式交互后再导出
```

`--eval` 的表达式在页面就绪后执行、会 await Promise、会跟随挂起的导航，**返回值不序列化**（异常则整条命令失败且不产出 dump）；不加 `--dump` 时 `--eval` 保留"输出表达式值"的旧行为。完整选项见 `fetch --help`。

### 3. 自动化服务器

```bash
moli serve                      # DOM-first 基础服务器
moli serve --layout             # 开启真实几何、坐标输入、截图/截屏
moli serve --layout --resource  # 再抓取可选的图像/字体/音频/视频/媒体/文本轨资源
```

同一端点**同时服务** CDP、WebDriver Classic、WebDriver BiDi；Playwright 可经 `connectOverCDP` 直连。另含 WebMCP playground（`moli-playground/webmcp/README.md`）：可列出并调用任意站点的原生 WebMCP 工具，含订座 / 餐厅 / 披萨 demo。

### 4. 成本控制（README 表格）

| 模式 / 选项 | 行为 |
| --- | --- |
| 默认 | `LayoutPolicy::Mock`——**兼容格式的确定性几何，不做真实布局或绘制** |
| `--layout` | `LayoutPolicy::OnDemand`——真实布局、几何、命中测试、坐标输入、截图、截屏 |
| `--resource` | 抓取**全部**可选视觉 / 媒体资源族 |
| `--image` / `--font` / `--audio` / `--video` / `--media` / `--text-track` | 只开启**某一个**可选资源族 |
| `--profile-dir` / `--http-cache-dir` / `--cookie` | 按负载**选择性**开启所需的持久化 |

### 5. 能力与边界（README）

- **完整 Web 运行时**：流式 HTML 解析、原生 DOM、V8、模块 / 定时器 / 微任务 / 事件、iframe 与 worker、CSS 级联、Fetch/XHR/WebSocket、Cookie、WebCrypto、按 profile 隔离的存储（localStorage / IndexedDB / OPFS）。
- **为抽取优化的输出**：HTML / Markdown / JSON / 语义文本树 / 帧感知序列化，带选择器 / 脚本 / 响应等待与网络追踪。
- **可控运维选项**：profile、cookie、HTTP 缓存、代理、资源族、连接数限制、超时、私网策略、UA 覆盖、诊断日志与网络诊断。
- **明确的"不做"**：无 GUI 浏览器 / 常驻窗口 / GPU 合成器 / 保留多帧绘制架构；**不追求**与 Chrome 的像素级一致，**不提供高保真 Canvas / WebGL / 媒体播放**；`--layout` 支持软件截图与原生 CDP/CLI PDF（嵌入字体），但**并非**每种 Chrome 截图或打印模式都实现。**不支持的协议路径会返回显式错误——moli 绝不假装某个浏览器动作、事件、网络观测或视觉结果发生过。**

### 6. 自述 benchmark（README，**均为项目自报，未经我复现**）

| 场景 | 关键数字（README 原文） |
| --- | --- |
| 混合公开网页爬取（192 URL） | **Moli 103 有用 / 53.6% / 中位 1.43 s / 中位 RSS 73 MiB**；Chrome Headless 101 / 52.6% / 1.43 s / **773 MiB**；Lightpanda 85 / 44.3%；Obscura 57 / 29.7% |
| 样例 agent 负载 | CDP ready **34.85 ms**（Chromium 169.37 ms）；峰值 PSS **102.46 MiB**（Chromium 348.82 MiB）；峰值进程 / 线程 **1 / 24**（Chromium 11 / 123） |
| WPT | 单次全量运行通过 **161.2 万个测试** |
| Lexbench-Headless-Browser（1,308 可比任务） | Moli 0.1.1 通过 1,071 任务、**81.88%**，高于 Kitesurf 62.08% / Lightpanda 53.29% / Obscura 44.88%，Chrome 参照 **99.85%**；另一轮 557 任务中位 CPU **100.6 ms**、中位峰值内存 **92 MiB**（Chrome 为 **687 ms / 697 MiB**） |

## 使用前需要注意

- **双许可别被徽章误导**：shields 的 license 徽章**只报 Apache-2.0**，而 README 与仓库明确是 **Apache-2.0 或 MIT 二选一**（另有第三方组件与 fixture 按各自许可，`license-metadata.json` 记机器可读默认值、`licenses/` 收集额外声明）。**引用许可时请写"双许可（Apache-2.0 / MIT）"。**
- **性能数字全是自报**：README 里的爬取成功率、RSS、CPU、WPT 与 Lexbench 结果**均未经我复现**；且 Lexbench 的 Kitesurf 是**远程服务**（k=1、未覆盖任务计为未通过、复现条件与本地二进制不同），**跨引擎比较需谨慎**。
- **有意的能力边界**：不追求与 Chrome 像素级一致，**无高保真 Canvas / WebGL / 媒体播放**，无 GUI / GPU 合成 / 多帧保留；`--layout` 的截图 / PDF 与 Chrome 的每种模式**不完全对齐**。若你的任务依赖这些，需另选方案。
- **"绝不假装"是设计承诺**：不支持的协议路径**返回显式错误**而非静默成功——这对评测 / 自动化的可信度有利，但也意味着**别指望它容错地"尽力而为"**。
- **`--layout` / `--resource` 是显式 opt-in**：昂贵操作**默认关闭**，默认 `LayoutPolicy::Mock` 给的是**确定性 mock 几何**（格式兼容但非真实布局）——**别把默认几何当成真实渲染结果**。
- **与托管服务的关系**：moli 开源可独立使用；Lexmount Browser 是**托管云运行时与控制面**，README 强调前者无需后者即可完整使用（**自述未验证**）。
- **元数据口径**：README 顶部含 **Product Hunt "#1 Product of the Day"** 与 Trendshift 徽章（**自述，未核验**）；Star / Fork 我用 ungh（23,460 / 660）与页面（23,613 / 666），**差异约 0.7%**；**Release 数**因 ungh 单页上限 30，与页面 36 不等同。引用时注明来源与日期。
- **我只读了 README 与两份 LICENSE**：**未 clone、未编译、未下载预构建二进制、未运行 `moli fetch` / `moli serve`、未跑任何 benchmark**；能力、架构与性能描述均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 需要给 **browser-use / 爬取 / 检索 / 评测 / 强化学习**这类**结构优先**负载找**低内存、快启动、完整 Web 运行时**的无头浏览器时。
- 想在一套二进制里**同时用 CDP、WebDriver Classic、WebDriver BiDi**（含 Playwright `connectOverCDP`），又不想额外装 ChromeDriver / 浏览器时。
- 想研究"**按需布局与绘制 + 冻结布局树（FrozenLayoutTree）**"这种成本模型怎么设计时。
- 想对照其它"给 Agent 用浏览器"的路线时（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"给 AI 用的浏览器自动化"，层次不同**：[browser-use：给 AI 用的浏览器自动化 Python 库](browser-use-给AI用的浏览器自动化Python库.md)——那篇是**驱动浏览器完成任务的 Python 库**（Agent 层）；本篇是**被驱动的无头浏览器内核**（执行层）。可对照"**Agent 编排**"与"**内核 / 运行时**"。
- **都通过标准协议把浏览器暴露给 LLM**：[playwright-mcp：给 LLM 用的浏览器自动化 MCP 服务器](playwright-mcp-给LLM用的浏览器自动化MCP服务器.md)——那篇是 **MCP 服务器 + Playwright**；本篇以**统一二进制同时说 CDP / WebDriver**。可对照"**MCP 封装**"与"**协议原生内核**"。
- **都强调"把真实浏览器能力交给 Agent"，入口不同**：[bb-browser：把真实浏览器登录态给 Agent 当 API 的 CLI 与 MCP](bb-browser-把真实浏览器登录态给Agent当API的CLI与MCP.md) 与 [bb-sites：把网站变成命令行指令的适配器集](bb-sites-把网站变成命令行指令的适配器集.md)——前者复用**真实浏览器登录态**、后者把**网站做成命令行**；本篇是**独立无头内核**。可对照"**复用既有浏览器**"与"**自带内核**"。
- **都做网页 → LLM 可用数据**：[firecrawl：把网站转成 LLM 可用数据的抓取 API](firecrawl-把网站转成LLM可用数据的抓取API.md)——那篇是**抓取 API / 服务**（`moli fetch --dump markdown` 的输出形态与之相近）；本篇是**本地无头浏览器 + CLI**。可对照"**托管抓取服务**"与"**本地渲染引擎**"。

## 参考来源

- 仓库主页与 README（417 行；核验日期 2026-11-13）：https://github.com/lexmount/moli
- 许可证（核验日期 2026-11-13）：`LICENSE-APACHE`（Apache-2.0，11,357 字符）、`LICENSE-MIT`（MIT，1,074 字符，Copyright (c) 2026 Moli contributors）、`license-metadata.json`、`licenses/`
- 文档：`docs/README.zh-CN.md`（另含 ja / de / fr / es / ko 多语言 README）、`moli-playground/webmcp/README.md`
- 关联：Lexmount Browser（托管版）、[Lexbench-Headless-Browser](https://github.com/lexmount/Lexbench-Headless-Browser)、[lexmount/moli-metrics](https://github.com/lexmount/moli-metrics)
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：ungh.cc 与 GitHub 页面内嵌数据、shields.io 交叉核对（2026-11-13，**镜像与页面差异很小；Release 数因单页上限不等同**）

以上信息核验于 2026-11-13。**未 clone、未编译、未下载预构建二进制、未运行任何命令、未跑任何 benchmark**；能力、架构与性能描述均来自 README，**未逐一验证**。
