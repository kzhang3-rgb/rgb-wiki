# tchMaterial-parser：国家中小学智慧教育平台的电子课本下载工具

> 记录一个把官方电子课本"批量、成册、带书签"落地的下载器：解决的不是"能不能下"，而是"下完怎么还能当书用"。

- 状态：草稿
- 最近核验：2026-11-05（阅读 README、目录树与 GitHub API 元数据，未安装、未运行、未下载任何课本）
- 适用范围：Python 3.10+（源码方式）；另有 Windows / Linux 预编译包；macOS 需手动去隔离属性

## 核心结论

`tchMaterial-parser` 是一个**针对国家中小学智慧教育平台**（`basic.smartedu.cn/tchMaterial/`）电子课本的下载工具。它的定位不是"通用 PDF 抓取"，而是把"从该平台拿到一册完整、可用的电子课本"这件事做到底。

README 列出的能力集中在"下载之后还能不能正常用"：

- **批量下载**、**自动命名**（按课本信息生成文件名，省去手工重命名）。
- **自动添加 PDF 书签**——这是它区别于普通下载脚本的地方：电子课本页数多，没有书签目录基本没法查阅。
- **支持 Access Token**：用于访问需要登录/授权的资源。
- **资源搜索**、**高 DPI**、**深色模式**、**跨平台**。

项目在 **2025 年 5 月登上 GitHub Trending 总榜第 3 名**（README 自述单日约 +400 Star），并进入 Trendshift 的 Python 日榜第 3——这类"工具型"仓库拿到这个量级关注，通常意味着需求真实且集中。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [happycola233/tchMaterial-parser](https://github.com/happycola233/tchMaterial-parser) |
| 目标平台 | 国家中小学智慧教育平台（`basic.smartedu.cn/tchMaterial/`） |
| 许可证 | MIT |
| 语言 | Python（`pyproject.toml`） |
| 关注度 | **7,686 Star、985 Fork、58 关注、11 个开放 Issue**（GitHub API，2026-11-05） |
| 维护状态 | **2023-08-10 创建**，最近推送 **2026-10-16**（活跃）；**单分支 `main`** |
| 发行版 | 最新 **v4.4**（2026-10-07，**6 个资产**，含预编译包） |
| 体积 | 约 30 MB（`size` 字段） |
| 贡献者 | 13 人，主力 `wuziqian211`（111 次提交）、`happycola233`（64 次） |
| 根目录元文件 | `AGENTS.md`（约 2,390 B，有实质内容）、`CLAUDE.md`（仅 11 B）、`THIRD_PARTY_NOTICES.md` |
| 安装方式 | GitHub Releases 包 / `winget install tchMaterial-parser`（Windows）/ AUR `yay -S tchmaterial-parser`（Arch）/ 源码（Python 3.10+） |

## 用法要点

四种安装途径（README 给出）：

- **预编译包**：从 GitHub Releases 下载（最新 v4.4 有 6 个资产）。
- **Windows**：`winget install tchMaterial-parser`。
- **Arch Linux**：`yay -S tchmaterial-parser`。
- **源码运行**：需要 Python 3.10+。

**macOS 用户注意**：README 明确提示需要对下载的 App 执行 `xattr -cr` 去除隔离属性（quarantine），否则会被 Gatekeeper 拦截。这是未签名应用在 macOS 上的常见处理方式，**同时也意味着该包没有走 Apple 公证**。

## 使用前需要注意

- **先想清楚用途与条款**：平台的电子课本面向的是个人学习使用，批量下载与再分发是否被允许，**取决于平台的服务条款与教材版权归属，本工具不改变这一点**。工具解决的是技术可达性，不解决授权问题。
- **`Access Token` 是敏感凭据**：README 支持用 Access Token 访问资源，**这类 Token 等同登录凭证**，不要粘贴到不可信环境、不要提交进仓库。
- **`CLAUDE.md` 只有 11 B、`AGENTS.md` 约 2,390 B**：根目录两个"给 AI 助手的说明"文件体量差很大——**别看到文件名就假设里面有内容**（与 [cherry-studio](cherry-studio-多模型Agent与知识库的桌面工作台.md) 根目录 `AGENTS.md` 只有 9 字节属同一类现象）。
- **它是"目标站点专用"工具**：页面结构、接口或鉴权方式一旦调整，工具可能失效；活跃度（最近推送 2026-10-16）尚可，但**依赖单站的工具天然有"站点改版即失效"的风险**。遇到失败先看是否有新版。
- **有第三方通知文件**：仓库含 `THIRD_PARTY_NOTICES.md`，说明打包了非自有的组件，分发或再集成时应一并保留。
- 我**未安装、未运行、未下载任何课本**：上述能力均来自 README 自述。

## 什么时候会再看

- 需要在该平台**成批取电子课本**，且要求可检索（带书签）、命名规整时。
- 想参考"**面向单一站点的下载工具**"怎么做工程化——批量、命名、书签、DPI、跨平台一起考虑，而不是只给一个 URL 解析函数。
- 想对照 [ChinaTextbook](ChinaTextbook-小初高与大学PDF教材仓库.md) 那种"PDF 本体成仓库"的路线，与本文这种"工具化下载、内容不入库"的路线差别时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **内容侧的另一条路线**：[ChinaTextbook：小初高与大学 PDF 教材仓库](ChinaTextbook-小初高与大学PDF教材仓库.md)——那个是**把教材 PDF 直接放进仓库**（约 43.5 GB）；本篇是**只给工具、内容自取**，并且 ChinaTextbook 的 README 在"重新下载"一节里推荐本项目，两篇天然成对。
- **同为"面向特定平台的下载/采集工具"**：[videodl：纯 Python 的轻量级多平台视频下载器](videodl-纯Python的轻量级多平台视频下载器.md)、[galaxy-downloader：多平台媒体下载器](galaxy-downloader-多平台媒体下载器.md)——那些走多平台适配器架构，本篇是单站专用、但把"下完能用"做深。
- **"给 AI 助手的元文件"体量陷阱样本**：[cherry-studio](cherry-studio-多模型Agent与知识库的桌面工作台.md)（根目录 `AGENTS.md` 仅 9 B）；本篇是 2,390 B 有内容、但 `CLAUDE.md` 仅 11 B，可对照。

## 参考来源

- 项目仓库与 README：https://github.com/happycola233/tchMaterial-parser
- 目标平台：<https://basic.smartedu.cn/tchMaterial/>
- 元数据（Star / Fork / Release / 贡献者 / 时间）：GitHub REST API，2026-11-05

以上信息核验于 2026-11-05，Star 数与仓库内容可能变化。仅阅读 README、目录树与元数据；未安装、未运行、未下载任何课本，未核验平台服务条款。
