# Free-APIs.github.io：把免费 API 清单做成一个 React 站点

> 它是 [public-apis](public-apis-人工策展的免费API清单.md) 那份巨型 Markdown 清单的**网页版外壳**——README 自述 "derived from Public APIs"。**最值得记的一点是它"没有数据"**：仓库里不放任何 API 清单文件，页面打开时**直接向第三方服务 `api.publicapis.org` 拉数据**、缓存在浏览器 `sessionStorage` 里，所以站点是否可用**取决于那个外部服务是否活着**。技术上是一份小而完整的 **Create React App + CRACO + Tailwind** 样本（React 17 / react-router v5 / gh-pages 部署），另有 **Firebase Analytics + Microsoft Clarity** 双重埋点。**代码推送停在 2025-02-08（约一年九个月前），基本处于停更状态**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README、`LICENSE`、`package.json`、`src/App.js`、`src/pages/Browse`、`src/pages/Category`、`src/pages/Categories`、`src/components/Tracking`、完整文件树与元数据；**未 clone、未 `npm install`、未构建、未运行站点、未访问 `api.publicapis.org` 验证其当前可用性、未核验站点内条目与上游清单是否一致**）
- 适用范围：只做前端展示的小型静态站点（GitHub Pages）；想参考"**纯前端 + 外部数据源 + 免费托管**"这种最省事的做法时。仓库语言为 **JavaScript**。

## 核心结论

**一句话定位（README 自述）**："A collection of free APIs for new and experienced developers, **derived from Public APIs**." 把上游那份人工策展清单搬成一个"**能搜、能按分类翻**"的静态网站，官网 <https://free-apis.github.io>。

**三个最值得记的点：**

1. **仓库里没有 API 数据，数据是运行时现拉的**（这是它最需要知道的一点）。`src/pages/Browse/index.js` 与 `src/pages/Category/index.js` 都执行 `fetch('https://api.publicapis.org/entries')`，`src/pages/Categories/index.js` 拉 `https://api.publicapis.org/categories`。拉回来去重（按 `API` 名用 `Map` 去重）后写进 `sessionStorage`，下次直接读缓存；`canStore` 为假（浏览器禁 `sessionStorage`）时退化到内存里的 `backup`。**所以它的"内容"其实托管在别人的免费 API 服务上**（该服务是 public-apis 生态里常见的公共端点），**站点能不能用、数据新不新，都取决于这个第三方服务**。
2. **技术栈是很标准、甚至有点"旧"的 CRA 样本**：**React 17 + Create React App（`react-scripts` 4.0.3）+ CRACO**（`craco.config.js` 覆盖默认配置）+ **Tailwind CSS 2**（走 `@tailwindcss/postcss7-compat`，配合 PostCSS 7）+ `react-router` **v5**（`Switch` / `Route` 老 API）；部署用 **`gh-pages`**（`npm run deploy` = 先 build 再 `gh-pages -d build`，推到 GitHub Pages）。构建脚本里带 `export SET NODE_OPTIONS=--openssl-legacy-provider`——**这是为绕开新版 Node 与老 CRA/Webpack 的 OpenSSL 报错**，也侧面说明这套依赖偏旧。
3. **站点带两套访问埋点**：`src/components/Tracking/index.js` 同时初始化 **Firebase Analytics**（`REACT_APP_API_KEY` / `REACT_APP_PROJECT_ID` / `REACT_APP_MEASUREMENT_ID` 等环境变量，记录 `page_view`）和 **Microsoft Clarity**（注入 `https://www.clarity.ms/tag/<REACT_APP_CLARITY_ID>`）。**换句话说：你在这个免费站点上的浏览行为会被上报到 Google Firebase 与 Microsoft**（该组件在 `App.js` 里被无参调用，全站生效）。埋点的具体单号在环境变量里，**仓库中看不到实际值**。

**个人判断（未验证）**：它是个"**能快速上线、但很轻**"的项目——好处是几乎零后端成本、顺着 public-apis 的现成清单就能做出一站；代价是**把可用性押在一个免费第三方服务上**（该服务一旦限流 / 改接口 / 下线，站点就只剩 "Error occurred" 分支），而且 **代码推送停在 2025-02-08**、贡献者只有 2 人，**长期维护意愿存疑**。适合当"最小可行样本"读，不适合当"稳定数据源"依赖。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Free-APIs/Free-APIs.github.io](https://github.com/Free-APIs/Free-APIs.github.io) |
| 描述 | "A collection of free APIs for new and experienced developers" |
| 归属 | 组织账号 `Free-APIs`（组织名 "Free APIs"，ungh 的 `orgs` 接口可查到；`isFork: false`、`isArchived: false`；页面内嵌数据里 `isOrg` 返回 `null`，故按组织账号处理并另经 ungh 确认） |
| 官网 | <https://free-apis.github.io>（`package.json` 的 `homepage` 同此） |
| 许可证 | **Apache-2.0**（`LICENSE` 11,339 字符，`Copyright 2021 Free-APIs`）——**注意：这只是站点代码的许可，站点展示的清单内容派生自 public-apis（该项目为 MIT）**，README 以链接形式致谢上游 |
| 主语言 | **JavaScript 97.9%**（shields.io，2026-11-13） |
| 技术栈 | React 17 · Create React App（`react-scripts` 4.0.3）· **CRACO**（`craco.config.js`）· **Tailwind CSS 2**（`@tailwindcss/postcss7-compat`，PostCSS 7）· `react-router` / `react-router-dom` 5.2 · `lodash`（防抖搜索）· `smoothscroll-polyfill` · **`firebase` 9.x**（Analytics）· `gh-pages`（部署）；见 `package.json` |
| 关注度 | **1,331 Star / 150 Fork / 15 Watcher**（**页面值**，2026-11-13）；开放 Issue **12**、贡献者 **2**（shields.io，2026-11-13）。**页面值与 ungh.cc 镜像一致（同为 1,331 / 150 / 15）** |
| 维护状态 | **2021-07-02 创建**；ungh 报 `pushedAt` **2025-02-08**（距核验日约 **1 年 9 个月前，实质停更**）；页面 `updatedAt` **2026-11-10**（多为 Star / 徽章刷新，**不代表代码有更新**） |
| 默认分支 | `main` |
| 版本 | **0 Release**（页面 `sections.releases: false`）；无 tag |
| 话题标签 | `api` · `categories` · `development` · `free` · `free-apis` · `list` · `public` · `public-apis` · `react` · `software` |
| 体积 | `main` 分支 **49 个条目**（ungh.cc files API）——**没有任何数据文件（JSON / YAML / Markdown 清单都不在仓库里）** |

## 能做什么（README 自述）

- **两种浏览模式**：
  1. **全部一次看**（All）——适合找项目灵感和内容源，可全量搜索并查看每条 API 的**授权方式、CORS、HTTPS**；
  2. **按分类浏览**（By category）——进一步收窄，便于控制搜索范围。
- **点击卡片**跳到对应 API 的官网或文档——README 把这一点当作"终极目标"：一个快速找 API 及其链接的资源。

## 代码结构（本笔记实读）

```
src/
  App.js                     # 路由表 + 无参调用 Tracking()（全站埋点）
  components/
    Tracking/index.js        # Firebase Analytics + Microsoft Clarity
    ListDisplay/{Card,OptionsRow}/
    Jumbotron/ Nav/ Footer/ Button/ Help/ ScrollToTop/ ScrollUp/ Subtitle/
  pages/
    Home / About / Browse / Categories / Category / HelpPage / LoadingScreen / NotFound / Template
craco.config.js              # 覆盖 CRA 默认配置
package.json                 # react 17 / react-scripts 4.0.3 / tailwind(postcss7-compat) / firebase 9
public/                      # favicon / logo192 / logo512 / manifest.json / preview.png / robots.txt
tailwind.config.js / .prettierrc / .gitignore
```

- **路由**（`App.js`）：`/`（Home）· `/about` · `/browse`（全部）· `/categories` · `/categories/:category` · `/help` · `*`（404）。
- **数据流**：`Browse` / `Category` / `Categories` 各自拉 `api.publicapis.org` 的 `entries` / `categories`，去重后存 `sessionStorage`（键名 `data` / `categories`），`Refresh` 会清缓存重拉。

## 使用前需要注意

- **站点可用性完全依赖第三方服务**：数据不在仓库里，页面打开时去 `https://api.publicapis.org/...` 拉。**该服务限流、改接口或下线，站点就会走到 "Error occurred" / "API categories weren't able to be loaded" 分支**。本笔记**未访问该服务验证其当前状态**，使用前请自行确认。
- **数据新鲜度 = 上游服务的新鲜度**：站点自己不做数据维护，**清单里有什么、是否过期，取决于 `api.publicapis.org` 返回什么**，未必与本笔记收录的 [public-apis](public-apis-人工策展的免费API清单.md) 仓库当前内容一致（本笔记未比对二者）。
- **全站带埋点**：`Tracking` 在 `App.js` 中无参调用，**每次浏览都会向 Firebase Analytics 与 Microsoft Clarity 上报**（含路径）。**如果在乎浏览隐私，请知悉**；埋点 ID 来自构建时环境变量，仓库中不可见。
- **许可只覆盖代码**：仓库是 **Apache-2.0**，但**页面展示的清单内容派生自 public-apis（MIT）**——引用 / 二次分发内容时，应同时遵循上游项目的许可。
- **看起来已停更**：`pushedAt` 停在 **2025-02-08**（约一年九个月），贡献者仅 2 人，0 Release。**是一个"放着能用、但别指望有人修"的静态站点。**
- **工具链偏旧**：CRA 4 + Webpack + React 17 + Tailwind 2，构建脚本还带 `--openssl-legacy-provider`。**想按现代方式重建不建议照抄依赖版本**，可只借鉴"纯前端 + 外部数据源 + Pages 托管"的结构。
- **我只读了源码与元数据，没有跑起来**：**未安装依赖、未构建、未部署、未访问站点与 `api.publicapis.org`**；站点实际渲染效果、数据条目数、与上游清单的一致性**均未独立验证**。

## 什么时候会再看

- 想做一个"**把某份清单 / 数据集合搬到网页上**"的最小静态站，需要一个**现成的 CRA + Tailwind + gh-pages 骨架**时。
- 想对照"**数据不放仓库、运行时从外部 API 拉**"这种省事但脆弱的做法，与其他"把清单编进仓库"的项目（如 public-apis 本身）比较取舍时。
- 想参考"**纯前端项目如何用环境变量挂 Firebase / Clarity 埋点**"的写法时（`src/components/Tracking/index.js`）。
- 想核验"**免费第三方 API 端点的长期可用性风险**"时——这是一个"把可用性押在别人身上"的现实样本。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **它就是本篇的直接上游**：[public-apis：人工策展的免费 API 清单](public-apis-人工策展的免费API清单.md)——那一份是**仓库本体**（巨型 Markdown 清单 + CI 校验），本篇是**把那份清单搬到网页上的展示壳**，且**数据也不取自上游仓库、而取自第三方服务 `api.publicapis.org`**。两篇对照可看"**原始清单**"与"**展示层**"的分离，以及"数据到底放哪"的三种选择。
- **同属"围绕免费资源的轻量站点 / 工具"**可对照：[everything-ai-coding：聚合编程 AI 扩展资源的索引与一键安装库](everything-ai-coding-聚合编程AI扩展资源的索引与一键安装库.md)——那是**多上游采集 + LLM 评分 + 一键安装**的重型流水线，本篇是**零后端 + 运行时拉取的轻量前端**，两者是"聚合"这条路上的两个极端。

## 参考来源

- 仓库主页与 README（1,508 字符，核验日期 2026-11-13）：https://github.com/Free-APIs/Free-APIs.github.io
- 官网：https://free-apis.github.io
- 许可证：`LICENSE`（Apache-2.0，11,339 字符，`Copyright 2021 Free-APIs`）
- 依赖与脚本：`package.json`（React 17 / `react-scripts` 4.0.3 / CRACO / Tailwind postcss7-compat / firebase 9 / gh-pages）
- 数据来源与路由（实读源码）：`src/App.js`、`src/pages/Browse/index.js`、`src/pages/Category/index.js`、`src/pages/Categories/index.js`（`fetch('https://api.publicapis.org/entries')`、`.../categories`）
- 埋点实现（实读源码）：`src/components/Tracking/index.js`（Firebase Analytics + Microsoft Clarity）
- 完整文件树（49 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档 / topics）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**与页面值一致**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号经 ungh `orgs` 接口确认为组织

以上信息核验于 2026-11-13。**未 clone、未安装依赖、未构建、未部署、未运行站点、未访问 `api.publicapis.org` 验证其可用性、未比对站点数据与上游清单**；技术栈、数据来源、埋点方式与路由结构均来自 README、`package.json` 与实读源码，"已停更"的判断基于 ungh 的 `pushedAt`（2025-02-08）。
