# everything：把"全世界的软件"重写一遍的开源单仓

> 记录 Puter 团队的一个野心项目：**在一个 monorepo 里，用 Web 重写从 Office 到 Slack、从 Photoshop 到 AutoCAD 的一整套软件**。第一句先记清三件事：**它现在有 51 个应用子目录**；**MIT 许可、上手只要 Node 22 + `npm run dev`**；**API 报的体积（约 426 MB）与默认分支树体积（约 1.28 GB）严重不符**，看体积不要只信一个数。

- 状态：草稿
- 最近核验：2026-11-04（阅读 README（157 行，含分类、快速开始与 `#link-status` 说明）、`LICENSE` 首行、`src/` 二级目录清单、单个 app 目录内的文件构成、大文件清单、分支 / Release / 提交 / 贡献者元数据；**未 clone、未安装依赖、未运行任何应用、未访问各应用 Demo 站点、未审计源码**）
- 适用范围：Node.js **22.12 或更新**；每个应用独立 `npm install` / `npm run dev`；浏览器端应用，按 `<app>.puter.com` 子域访问

## 核心结论

**① "一个仓库，51 个应用"是它最突出的结构特征。** README 自述目标是"The Everything Project is an effort to **rebuild and open-source the world's software from scratch**…**This is a mono-repository**"。实测 `src/` 下有 **51 个子目录**，覆盖九大类：**Office**（documents、spreadsheet、presentation、pdf、records）、**Collaboration**（teamchat、meetings、email、contacts）、**Creative Tools**（photo-studio、vector-studio、video-studio、audio-studio、music-studio、whiteboard、3d）、**Engineering**（cad、parametric、building-design、circuits）、**Productivity**（notes、tasks、calendar、projects、wiki）、**Finance**（accounting、expense、invoicing、cap-table）、**Business**（crm、hiring、legal、contracts、inventory、pos、fleet、field-service、property-management、time-tracking、signatures、help-desk、childcare、shifts）、**Developer Tools**（api-client）、**Games**（chess、checkers、solitaire、sudoku、backgammon）等。**每个 app 子目录结构统一**：`README.md`、`STORE_LISTING.md`、`package.json`、`index.html`、`mock.html`、`vite.config.js`、`vite.worker.config.js`、`icon.png` / `icon-128.png`、`LICENSE`、`customHttp.yml`。

**② 上手成本很低，但"能跑"的范围要看清。** 快速开始就四步：`git clone` → `cd everything/src/[app-name]` → `npm install` → `npm run dev`，要求 **Node.js 22.12+**。域名体系是 `<app>.puter.com`；但 README 里有 `#link-status` 一节说明**部分应用标注 "Demo URL not yet provided"**——即**"仓库里有这个应用"不等于"线上有可访问的 Demo"**。

**③ 一处明显的体积口径不符。** API 报 `size` **436,743 KB（约 426 MB）**，但默认分支目录树实测 **37,381 项 / 35,154 个 blob / 1,308,365 KB（约 1.28 GB）**，两者相差约三倍。大文件包括 `src/pdf/public/fonts/NotoSansKR-Regular.ttf`（约 6.26 MB）、`src/spreadsheet/tests/fixtures/reference-lunisolar-calendar.json`（约 5.83 MB）、`src/video-studio/src/media/models/pose-landmarker.task`（约 5.78 MB）。**判断：`size` 字段与树体积本就不是同一口径**（前者偏历史仓库体积、后者是当前快照），跨仓库比体积时必须说明用的是哪一个——本项目是这条经验最直观的例子。

**④ 活跃但高度集中的开发模式。** 贡献者为 **`jelveh` 257 次、`Salazareo` 6 次、`tokg-venv` 1 次**；分支名带明显的"按轮次推进"节奏（`docr7-oct27/fixes`、`docr8-nov01/fixes`、`docr9-nov02/fixes`、`sheet-r9/fixes`、`docs-latency/a-docx`、`ldperf-oct27/fixes`），提交历史里还留有 `adversarial review fixes (round 8/9)` 一类痕迹。**推断（未验证）：`docr*` / `sheet-*` 像是"文档"与"表格"两条主线的迭代；`adversarial review` 提示他们在用对抗式评审推进质量**——但这只是从命名与提交信息做的推测。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [HeyPuter/everything](https://github.com/HeyPuter/everything)（组织账号） |
| 描述定位 | "从零重建并开源全世界的软件"，monorepo，一个应用一个子目录 |
| 许可证 | **MIT**（`LICENSE` 1,080 B，Copyright (c) 2026 Puter Technologies Inc.） |
| 主语言 | JavaScript |
| 关注度 | **6,964 Star、723 Fork、69 关注、59 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-10-13 创建**；`pushed_at` 2026-11-04；默认分支 **`main`**；贡献者集中在 `jelveh`（257）|
| 版本节奏 | **无任何 Release**（以分支推进为主） |
| 体积 | API `size` 436,743 KB（约 426 MB）；**默认分支树 37,381 项 / 35,154 blob / 约 1.28 GB**（口径差异见上） |
| 话题标签 | `office`、`adobe`、`microsoft`、`photoshop`、`cad`、`spreadsheet`、`saas` |
| 多语首页 | `README.md`、`README.zh-CN.md`、`README.es.md` |

## 它能做什么（结构清单）

- **九大类、51 个应用**（`src/` 下）：documents / spreadsheet / presentation / pdf / records、teamchat / meetings / email / contacts、photo-studio / vector-studio / video-studio / audio-studio / music-studio / whiteboard / 3d、cad / parametric / building-design / circuits、notes / tasks / calendar / projects / wiki、accounting / expense / invoicing / cap-table、crm / hiring / legal / contracts / inventory / pos / fleet / field-service / property-management / time-tracking / signatures / help-desk / childcare / shifts、api-client、chess / checkers / solitaire / sudoku / backgammon
- **统一的 app 目录骨架**：`package.json` + `index.html` + `mock.html` + `vite.config.js` + `vite.worker.config.js` + `README.md` + `STORE_LISTING.md` + `LICENSE` + 图标 + `customHttp.yml`
- **构建 / 部署**：根目录 `amplify.yml`、`customHttp.yml`、`dev/puter-localhost.mjs`；`.github/workflows/` 有 `ci.yml`、`deploy-meetings-worker.yml`、`e2e.yml`

## 使用前需要注意

- **"单仓 51 应用"意味着体量很大**：克隆与磁盘占用要预留空间（当前树约 1.28 GB，且仍高速增长）。
- **"有应用"≠"有可用 Demo"**：README 的 `#link-status` 标注部分应用尚无 Demo URL。
- **许可证是 MIT（宽松）**，但**各 app 目录内另带自己的 `LICENSE`**，若单独取用某个应用，应核对该子目录的许可。
- **作者集中（`jelveh` 一人主导）**：广度惊人但深度与可持续性需长期观察；本笔记**未运行任何应用、未评估其完成度与质量**。
- **别把 API `size` 当"仓库真实体积"**：本项目 API 报 426 MB，实际树约 1.28 GB，两者口径不同。

## 什么时候会再看

- 想找一个**特定办公/创作类应用的 Web 开源实现**（如在线表格、在线 PDF、在线白板）时，先来这里按 `src/` 子目录名定位；
- 想参考"**一个 monorepo 如何容纳几十个同构应用**"（统一的 Vite 配置、统一的 store listing、统一图标规范）时；
- 想找一个"**野心极大、用 AI 与多轮评审高速推进**"的项目作为观察样本时。

## 相关笔记

- **同属"图片/创作工具"的 Web 实现**：[squoosh：浏览器内的图片压缩工具](squoosh-浏览器内的图片压缩工具.md)——那是单点做精（图片压缩）的 Web 工具；本篇是"什么都做一点"的大而全仓库。两篇最能对照"**一个功能做到极致**"与"**铺开几十个功能**"两种路线。
- **文档处理这条线**：[markitdown：文件转 Markdown 工具](markitdown-文件转Markdown工具.md)——本篇的 `documents` / `pdf` / `spreadsheet` 应用属"文档生产"；那篇属"文档转换"，可对照文档类工具的两种方向。
- **浏览器里跑游戏**：[ra2：浏览器运行红色警戒 2](ra2-浏览器运行红色警戒2.md)——本篇的 Games 类（chess / solitaire / sudoku 等）是轻量小游戏；那篇是把完整的红警 2 搬进浏览器，两者对照"Web 化游戏"的难度梯度。
- 本页索引位置：[GitHub 项目收藏](README.md)

## 参考来源

- 仓库主页：https://github.com/HeyPuter/everything （核验日期 2026-11-04）
- README（含九大分类、快速开始、`#link-status`）、`LICENSE`、`src/` 二级目录清单、单 app 目录构成（核验日期 2026-11-04）
- GitHub API：`repos/HeyPuter/everything`、`git/trees/main?recursive=1`、`branches`、`releases`、`contributors`（核验日期 2026-11-04）
