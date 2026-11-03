# videodl：纯 Python 的轻量级多平台视频下载器

> 记录一个"按平台写客户端"的下载器框架，两个容易看走眼的点：它的许可证是**禁止商用的 PolyForm Noncommercial**（不是 MIT/AGPL），以及它 `pushed_at` 天天更新但其实**代码已停更四个月**——那些提交来自每日自测的 CI bot。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README 全文 776 行、LICENSE、目录树、提交记录与 GitHub API 元数据；未安装、未运行、未解析或下载任何视频）
- 适用范围：Python 包（PyPI 名 `videofetch`）与命令行工具，支持 Python 3；默认分支 `master`

## 核心结论

它是一个**把"平台"抽象成插件的中文生态视频下载器**：`videodl/modules/sources/` 下**一个平台一个 `.py`**（`douyin.py`、`bilibili.py`、`iqiyi.py`…），README 的能力表列了 **106 个 VideoClient**，另有若干"通用解析接口"兜底。用起来是一行命令 `videodl -i "<url>"` 自动匹配客户端解析并下载。

**第一个必须写清的点是许可证**：根目录 `LICENSE` 是 **PolyForm Noncommercial License 1.0.0**（Copyright (c) 2021–2026 CharlesPikachu，76 行）。这**不是 OSI 认可的开源许可**——它允许非商业用途，但**明确禁止商业使用**。这也解释了为什么 GitHub API 对它的 `license` 字段返回 `NOASSERTION`（API 识别不了非标准许可，必须以 LICENSE 文件为准）。

**第二个是活跃度陷阱**：API 的 `pushed_at` 是 **2026-11-03（核验当天）**，看着天天在更新；但用 `commits?path=videodl` 过滤代码目录后会发现，**代码的最后一次真实提交是 2026-06-26**（`bump to videofetch v0.9.1`）。近几个月的所有提交都是 `github-actions[bot]` 的 `chore: update videodl status & preview videos`——**每日自测的状态更新，不是功能更新**。

## 项目信息

| 项 | 内容 |
| --- | --- |
| 仓库 | [CharlesPikachu/videodl](https://github.com/CharlesPikachu/videodl) |
| 归属 | 个人开发者 CharlesPikachu（提交邮箱 `blwx96@connect.hku.hk`） |
| 许可证 | **PolyForm Noncommercial License 1.0.0**（非商业；API 返回 `NOASSERTION`，已核对 `LICENSE` 文件首行） |
| 主语言 | Python |
| 关注度 | 3,196 Star、524 Fork、**25 订阅者**（`subscribers_count`；`watchers_count` 与 Star 数相同，属 GitHub 历史遗留）；**16 个开放 Issue** |
| 活跃度 | 2018-07-10 创建；`pushed_at` **2026-11-03**（由每日 CI bot 刷新）；**`videodl/` 代码目录最后真实提交 2026-06-26**；`updated_at` 2026-11-03 |
| 仓库体积 | API 报约 **698 MB**（`size` 714,511 KB）——每日自测产物持续提交进 Git 是成因之一 |
| 包名 / 文档 | PyPI：`videofetch`；文档：<https://videofetch.readthedocs.io/>；状态看板：<https://charlespikachu.github.io/videodl/> |
| 默认分支 | `master` |
| topics | 20 个，含 `acfun` / `bilibili` / `douyin` / `kuaishou` / `tiktok` / `youtube` / `crawler` / `video-downloader` / `youtube-dl` 等 |
| 其他 | `has_wiki` / `has_pages` / `has_discussions` 均为 `true`；根目录含 `.github/`、`docs/`、`daily_test_results/`、`videodl/` |

## 它能做什么

- **一行命令解析下载**：`videodl -i "https://www.acfun.cn/v/ac36491489"`，自动匹配平台客户端并开始下载；也支持交互模式。
- **两种工作流**：①解析 + 下载一步完成；②先解析看结果、确认后再下载。
- **作为库调用**：`pip install videofetch`（或从 GitHub 装），在 Python 里接进自己的处理管线。
- **覆盖平台**（README 能力表，共 106 个 VideoClient）：国内含 AcFun、百度贴吧、B站、56 视频、央视网 / 央视新闻、CCtalk、中国日报网、度小视、抖音、懂车帝、开眼、好看视频、虎牙、环球网、爱奇艺、酷6、快手、酷狗音乐 MV、看看新闻、乐视、美拍、芒果 TV、1905 电影网、明报、新浪绿洲、网易公开课、腾讯视频、优酷、咪咕、PPTV、搜狐、微博、今日头条、全民 K 歌、酷狗、新片场、知乎、贴吧、TED 等；海外含 TikTok、YouTube、Facebook、Twitter(X)、FIFA+ 等。
- **每日有效性自测**：CI 工作流 `check_videodl.yml` 每天跑一次，产出 `daily_test_results/videodl_status.json`（385 KB）与约 3 个随机抽样预览视频，并**提交回仓库**，同时更新 GitHub Pages 看板——用来公开"哪些平台还活着"。

## 使用前需要注意

- **禁止商用**：PolyForm Noncommercial 1.0.0 只授予非商业用途。若打算用在公司内部工具、SaaS、内容生产的商业流程里，需先看条款或联系作者，**不要默认它是 MIT**。
- **版权与平台条款由使用者承担**：README 的 Statement 自陈"仅用于学习目的、禁止商业使用""如需下载付费视频请先确保自己是该平台会员"。但工具本身无法判断你的访问权限与授权状态，责任在使用者。
- **解析必然持续失效**：作者专门做了每日状态监测，本身就说明抓取接口脆弱；而**代码已四个月未更新**（最后真实提交 2026-06-26），部分平台客户端可能已经失效或需要自行修。
- **仓库体积约 698 MB**：每日测试产物（含预览 mp4）被提交进 Git 历史，clone 成本不低；这也是"CI 产物直接入库"这一做法的典型副作用。
- **README 含赞赏码与公众号导流**：底部有公众号"Charles的皮卡丘"与收款二维码（`.github/pictures/wechat_reward.jpg`）。
- **同作者还有一套工具矩阵**：`musicdl`（无损音乐下载）、`imagedl`、`paperdl`、`freeproxy`（免费代理采集）、`musicsquare`、`FreeGPTHub`（自称"真正免费的 GPT 统一接口"）等，收录与评估时需逐个单独核验其许可证与合规性，不能因同源就一并背书。
- **本笔记只记录其结构与边界**：未安装、未运行、未下载任何内容；对具体的平台解析行为与法律边界不作判断。

## 什么时候会再看

- 需要按平台批量取视频、且想要"Python 原生、可当库用、平台覆盖面广"的现成方案时。
- 想参考**"一个平台一个适配器文件"的下载器架构**（`modules/sources/*.py` + 统一基类 + 通用兜底解析）时。
- 想参考**用 CI 每日自测第三方接口有效性、并把状态看板与原始结果一并公开**的运维做法时。
- 想对比**许可证谱系**（MIT / AGPL / 自定义非开源 / 非商业许可）对同类下载工具的实际约束差异时。

## 相关笔记

- **同为"视频下载器"，实现路线完全不同**：[videdown](videdown-开源视频下载工具.md) 是 **Electron 套壳 yt-dlp**（跨平台 GUI，MIT），本篇是**纯 Python 自研多平台客户端**（无 GUI，非商业许可）——一个借成熟引擎、一个自己写解析层。
- **同为"多平台媒体工具"，分发形态不同**：[galaxy-downloader](galaxy-downloader-多平台媒体下载器.md) 是**网页版、解析依赖作者自建后端**，本篇是**本地库/CLI、解析在你自己的机器上**——数据流向与许可类型都不同。
- **同为"取平台内容的聚合工具"，许可相反**：[go-music-dl](go-music-dl-多平台音乐搜索与下载工具.md) 是**音频、AGPL-3.0 强传染（鼓励开源共享）**，本篇是**视频、PolyForm Noncommercial（禁止商用）**——同为下载器，许可取向正好相对。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 仓库主页：<https://github.com/CharlesPikachu/videodl>
- 文档：<https://videofetch.readthedocs.io/>
- 状态看板（每日自动更新）：<https://charlespikachu.github.io/videodl/>
- PyPI：<https://pypi.org/project/videofetch/>
- 核验时间：2026-11-03（GitHub API 元数据、`git/trees/master`、`commits?sha=master`、`commits?path=videodl`、`LICENSE` 与 raw `README.md`）
