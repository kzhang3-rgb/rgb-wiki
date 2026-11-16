# postcat：终端里的键盘优先 HTTP 调试 TUI

> 这是一个用 Rust 写的、跑在终端里的 Postman 替代品——**单二进制、键盘优先、不注册账号、工作区存本机**。值得记住的是它把"HTTP 客户端"压缩进 TUI 时做的一串具体取舍：JSON 保留原始键序、剪贴板走 OSC 52（可跨 ssh 复制）、流式响应边到边渲染，以及**端到端测试直接起真实 socket 而不 mock 网络**。注意：它**不是**那个中文的 API 工具 "Postcat"，同名的只是巧合。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`LICENSE`、`Cargo.toml`、仓库文件树全部 27 项、GitHub 页面内嵌元数据、shields.io 徽章；**未 clone、未 `cargo install`、未编译、未运行、未实测其 HTTP 调试与流式渲染**）
- 适用范围：**终端 TUI 工具**；Rust 实现，编译需 **Rust 1.85+**（`Cargo.toml` 标 `rust-version = "1.85"`、`edition = "2024"`）；提供 macOS / Linux / Windows 的预编译安装脚本与 Homebrew cask、crates.io 安装。工作区数据落在 `~/Library/Application Support/postcat`（macOS）或 `~/.config/postcat`（Linux）。

## 核心结论

**一句话定位（README 自述）**："A modern, keyboard-first Postman alternative that lives in your terminal"（用 [ratatui](https://ratatui.rs) 构建）；GitHub 描述为 **Keyboard and mouse friendly TUI for debugging HTTP requests in terminal**。

**三个最值得记的取舍：**

1. **"终端 + 键盘优先 + 本地工作区"是它的整体取向**：不做云账号、不做团队同步，草稿 / 已存请求 / 历史 / 环境变量都落在本机文件里；编辑是 spreadsheet 式（打开已填单元格即全选该单元格文本，继续输入即替换）。**代价也在这里——没有云同步与协作，换机要自己搬工作区文件**。
2. **几个具体到位的工程选择**：响应 JSON **保留原始 key 顺序**（依赖 `serde_json` 的 `preserve_order`）；剪贴板走 **OSC 52**，所以在 **ssh 会话里也能复制**；流式响应（SSE / chunked）**边到边渲染并 tail-follow**（上滚暂停、`G` 重新跟随、`esc` 中止），带实时字节计数。
3. **测试是端到端、且不 mock 网络**（README 自述，我看的是 `tests/` 目录结构）：合成的按键与鼠标事件走真实的 `keys` 派发、`app` 状态机与 `ui` 渲染，再用 ratatui 的 `TestBackend` 断言终端缓冲；请求**发往一个绑定临时端口的真实 HTTP 服务器**（`tests/common/server.rs`），服务器记录收到的内容供断言，并能吐出 `text/event-stream` 事件来测流式。测试用 `App::ephemeral`，**不读写你真实的已存请求**。

**个人判断（未验证）**：egoist 是长期做前端/工具链的开发者，这个项目很新、体量很小（27 个文件），更像一个**打磨得比较细的单点工具**；它值不值得日常用，取决于你是否接受"终端里调试 HTTP、数据全在本地"这套取舍。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [egoist/postcat](https://github.com/egoist/postcat) |
| 描述 | "Keyboard and mouse friendly TUI for debugging HTTP requests in terminal" |
| 归属 | 账号 `egoist`（**个人**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false` |
| 许可证 | **MIT**（页面 `spdxId: MIT`；`LICENSE` 首行 `MIT License`，署名 `Copyright (c) 2026 EGOIST`；`Cargo.toml` 亦标 `license = "MIT"`） |
| 主语言 | **Rust 97.0%**（shields.io，2026-11-13） |
| 技术栈 | **Rust（edition 2024）**；TUI 依赖 **ratatui 0.29** + **crossterm 0.28** + **tui-textarea 0.7**；HTTP 用 **reqwest 0.12**（`default-features = false`，启用 `rustls-tls` / `gzip` / `brotli` / `deflate`）；异步用 **tokio**；序列化用 **serde / serde_json**；目录定位用 `dirs` |
| 关注度 | **59 Star / 2 Fork / 1 Watcher / 0 开放 Issue**（页面 Star/Fork/Watcher 与 shields Issue 数，2026-11-13；**页面值与 ungh.cc 镜像一致，无差异**） |
| 维护状态 | **2026-08-04 创建**，ungh 报 `pushedAt` **2026-08-04**（**与创建同日**）；页面 `updatedAt` **2026-11-05**（更可能是 Star/元数据活动，而非代码更新）；累计 **4 Release / 5 tag**（页面口径） |
| 默认分支 | `main` |
| 版本 | **4 Release / 5 tag**；最新版本 **v0.1.4**（`Cargo.toml` 与 shields `release` 徽章一致）；页面 `packages: true`（发布到 crates.io） |
| 话题标签 | 无（页面 `topics: []`） |
| 体积 | `main` 文件树共 **27 个文件**（ungh.cc files API）；`src/` 下 `main.rs`、`lib.rs`、`app.rs`、`http.rs`、`keys.rs`、`model.rs`、`theme.rs`、`clip.rs` 与 `ui/`（`grid` / `json` / `modal` / `request` / `response` / `sidebar`）；`tests/` 下 `e2e.rs`、`common/{harness,mod,server}.rs` |

## 它提供什么（README 自述）

- **完整请求构建器**：method、URL、query 参数、headers、四种 body（JSON / text / form / none）、Bearer 与 Basic 认证。
- **响应展示**：语法高亮的 JSON（**保留原始键序**）、状态/耗时/大小一览、header 查看器、滚动与换行。
- **实时流式**：SSE 与 chunked 随到达渲染并 tail-follow（上滚暂停、`G` 重新跟随）、实时字节计数、`esc` 中止。
- **文库与历史**：把请求按名保存、每次发送自动进历史、单击或回车即可重新打开。
- **环境变量**：定义一次，在 URL / 参数 / header / body / token 里用 `{{name}}` 引用。
- **键盘优先**：modal 编辑（normal / insert）、vim 式移动、状态栏常驻上下文提示、`?` 查看完整键位表。
- **选择与复制**：在响应或任意输入框上拖选，松手即进系统剪贴板（**OSC 52，可跨 ssh**）；无选中时 `y` 复制整个 body。密码字段是例外——可选但**永不复制**。
- **鼠标**：点击切换面板/标签/行，点哪就在哪编辑，点 method 芯片弹下拉，可滚响应；指针平时是普通箭头，只有正在编辑的字段上是文本 I 形（需终端支持 OSC 22）。
- **工作区持久化**：草稿、已存请求、历史、环境变量重启后仍在（`~/Library/Application Support/postcat` 或 `~/.config/postcat`）。

## 常用的键位（README 自述）

| 键 | 作用 |
| --- | --- |
| `⇥` / `1‑4` | 在 文库 · URL · 请求 · 响应 之间切换 |
| `i` / `u` | 编辑 URL（`⏎` 发送、`esc` 保留、`^u` 清空） |
| `m` / `M` | 循环 method；点 `GET ▾` 芯片弹菜单 |
| `s` 或 `⏎` | 发送；飞行中 `esc` 取消 |
| `[` `]` / `h` `l` | 切换标签页 |
| `⏎` `a` `d` `␣` | 编辑 · 新增 · 删除 · 切换行 |
| `t` / `f` | body 与 auth 类型 · 格式化 JSON |
| `^s` `n` `e` | 保存请求 · 新建请求 · 环境变量 |
| `j` `k` `g` `G` `^d` `^u` `w` | 滚动与换行响应 |
| 拖选 · `⇧`+`←`/`→` | 选择文本；`y` 复制（或复制整个 body） |
| `^b` | 切换文库侧栏 |
| `?` / `q` | 帮助 · 退出 |

## 安装方式（README 自述）

| 方式 | 命令 |
| --- | --- |
| 安装脚本（macOS / Linux） | `curl -fsSL https://raw.githubusercontent.com/egoist/postcat/main/install.sh \| bash`（装到 `~/.local/bin`，回退 `/usr/local/bin`） |
| 安装脚本（Windows） | `irm https://raw.githubusercontent.com/egoist/postcat/main/install.ps1 \| iex`（装到 `%USERPROFILE%\.local\bin`） |
| Homebrew（macOS） | `brew install --cask egoist/tap/postcat` |
| crates.io | `cargo install postcat`（需 Rust 1.85+；升级 `cargo install postcat --force`） |
| 源码 | `cargo install --path . --locked`，或 `cargo run --release` |

## 使用前需要注意

- **⚠️ 别和中文的 "Postcat" 混淆**：本项目是 **`egoist` 的终端 HTTP 调试 TUI**，与那个同名的中文 API 工具是两回事，无任何关系。
- **⚠️ 这不是成熟的商业级工具**：**2026-08-04 创建**，体量极小（27 文件），Star 数不高（59）。它更像个人作者的**单点小工具**，功能覆盖与长期维护都是未知数——**别当作生产依赖**。
- **⚠️ 无云同步、无团队协作（这是设计取舍，也是限制）**：工作区只在本机文件里，**跨设备要自己搬文件**；没有账号体系意味着也没有共享请求库。
- **⚠️ 命令行安装脚本"管道到底"**：`curl ... | bash` / `irm ... | iex` 会直接执行远端脚本，**运行前建议先自己看一眼脚本内容**（我未审阅其内容）。
- **⚠️ 我未做任何运行验证**：**未 clone、未编译、未运行、未实测 HTTP 调试或流式渲染**；上述功能、键位与测试结构均**来自 README、`LICENSE`、`Cargo.toml` 与仓库文件自述**。README 的安装示例里出现了 `POSTCAT_VERSION=0.3.0`，而 `Cargo.toml` 与最新 Release 为 **0.1.4**——**这处数字不一致我未核实**（可能只是示例值，也可能是未来版本）。
- **⚠️ 复制行为有例外**：拖选松手即写系统剪贴板，**但密码字段只选不复制**；终端需支持 **OSC 52**（跨 ssh 复制）与 **OSC 22**（指针形状）才能获得完整效果，老终端上体验会打折。

## 什么时候会再看

- 想要一个**不注册、数据在本机、能塞进终端**的 Postman 轻量替代时。
- 想参考 **Rust + ratatui** 做一个交互式 TUI 的工程样板（modal 编辑、鼠标命中测试、状态栏提示）。
- 想借鉴它**"端到端测试不 mock 网络、直接起真实 socket + 断言渲染缓冲"**的测试写法。
- 想抄 **OSC 52 剪贴板 / 保留 JSON 键序 / 流式 tail-follow** 等具体交互细节的实现思路时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属终端工具，取向不同**：[pebrel：给 AI CLI 用的 GPU 加速终端与 SSH 工作区](pebrel-给AI-CLI用的GPU加速终端与SSH工作区.md)——那篇是**原生桌面多会话工作台**（终端 / SSH / SFTP / 文件 / AI 会话合一）；本篇是**塞进终端的最小 TUI**。可对照"**原生多会话**"与"**纯 TUI 最小工具**"。
- **同为"终端里的 TUI 工具"**：[stock-tui：终端里看A股K线与基本面](stock-tui-终端里看A股K线与基本面.md)——两者都把交互式界面压进终端、都强调键盘操作；对照可看"TUI 承载不同领域（看盘 vs 调 HTTP）"的取舍差异。**注意：这是主题相近（终端 TUI），功能上并不相关。**
- **同为"从本机既有痕迹还原事实、不依赖外部服务"的本地 CLI**：[witr：追踪进程端口容器是怎么起来的](witr-追踪进程端口容器是怎么起来的.md)——一个复盘"本机进程/端口/容器怎么起来"，一个在终端里发 HTTP；对照可看"本地工具如何处理数据与交互"。**注意：二者功能并不重叠，仅工具形态相近。**
- **同为"面向开发者的数据 / 接口工具"，形态不同**：[LakeDB：本地优先的多数据库桌面客户端](LakeDB-本地优先的多数据库桌面客户端.md)——那篇是**GUI 数据库客户端**（多库、带 review-first AI）；本篇是**终端里的 HTTP 调试 TUI**。可对照"**数据库 GUI**"与"**接口调试 TUI**"两类开发者工具的交互形态。**注意：功能并不重叠，仅开发工具形态相近。**

## 参考来源

- 项目仓库与 README：https://github.com/egoist/postcat
- 许可（MIT）：https://github.com/egoist/postcat/blob/main/LICENSE
- 版本与依赖：https://github.com/egoist/postcat/blob/main/Cargo.toml
- 测试结构：https://github.com/egoist/postcat/tree/main/tests
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 语言 / 默认分支 / Release 数 / topics）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数、主语言与最新版本取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未编译、未运行、未实测**；功能、键位与测试结构均来自 README、`LICENSE`、`Cargo.toml` 与仓库文件自述；**README 示例版本 0.3.0 与仓库当前版本 0.1.4 的不一致未核实**。
