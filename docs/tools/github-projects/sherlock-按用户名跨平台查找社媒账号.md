# sherlock：按用户名跨平台查找社媒账号

> 记录这个 2018 年就存在的 OSINT 老工具：给一个用户名，它到 **400+ 社交网络**逐站探测该用户名是否存在，并按平台汇总结果（txt / csv / xlsx / json）。第一句先记清三件事：**结果只代表"这个用户名在该站点存在"，不代表是同一个自然人**，这是它最容易被误用的地方；**它的定位是信息搜集/渗透/取证**，能不能用、怎么用取决于使用者目的与当地法律；**取它的 README 别只信单一通道**——`raw.githubusercontent.com` 直取返回的是 2019 年前后的旧快照，仓库根目录实际已无 `README.md`。

- 状态：草稿
- 最近核验：2026-11-03（阅读仓库说明页（实际来自 `docs/README.md`，版本 0.16.0）、仓库文件清单与 `docs/` 目录、GitHub API 元数据与默认分支 `master` 的 commits/tree 接口、Release 列表；未安装、未运行、**未对任何真实用户名发起查询**）
- 适用范围：命令行工具（Python）；官方分发方式为 pipx / pip / uv / Docker / dnf，另有社区维护的发行版包

## 核心结论

**它是一个"用户名存在性探测"工具，不是人肉搜索工具，但常被当后者用。**输入一个或多个用户名，Sherlock 会用内置的站点清单逐个发请求，把"该用户名在此站点存在"的结果落到文件里。它做的是**把公开信息里可枚举的部分自动化**，本身不入侵任何账号。

三点先看清：

**① 结果的语义边界极重要：同名 ≠ 同一人。**工具本身的机制（站点清单 + 存在性判定 + 排除列表 + `--ignore-exclusions` 会"返回更多误报"的官方说明）就决定了它会产出大量同名误报。**把结果当"这些账号都属于某个人"来用，是这个工具最典型的误用**。要下结论必须再做交叉验证。

**② 它的主要用途场景是授权范围内的安全与取证。**仓库 topics 里直接标着 `osint`、`reconnaissance`、`information-gathering`、`pentesting`、`redteam`、`cti`、`forensics`、`cybersecurity`。用于自己的账号盘点、授权的渗透与红队、取证调查是常见正当用法；**用于追踪特定自然人的实际身份，是否合法要看所在地法律与目的**。本笔记只作信息记录，不构成使用建议，也不作合规定性。

**③ 取 README 的通道本身就有坑，值得记成实例。**按默认分支 `master` 的 git tree 与仓库页面文件清单，**根目录没有 `README.md`**，GitHub 渲染的说明页内容来自 `docs/README.md`（版本 0.16.0）。而用 `raw.githubusercontent.com` 直取 `README.md` 时返回的是明显过期的旧内容（还在讲 `sherlock.py`、版本 0.7.9、旧组织名 `TheYahya`、已停用的 Travis CI 徽章与 Alexa 排名）；用 jsDelivr 取根 `README.md` 则直接报"文件不存在"。同一份"README"，三条通道三个结果。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [sherlock-project/sherlock](https://github.com/sherlock-project/sherlock) |
| 描述 | "Hunt down social media accounts by username across social networks" |
| 许可证 | **MIT**（API 返回 `mit`，仓库 `LICENSE` 1,073 字节，版权归 Sherlock Project） |
| 主语言 | Python（仓库页面统计 Python 97.3%，另有 Dockerfile 2.1%） |
| 关注度 | **95,461 Star、11,378 Fork、1,469 关注、356 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2018-12-24 创建；`pushed_at` **2026-11-03**；默认分支 `master` 最新提交 `c588d7c`「update banner layout」为 **2026-11-01**（作者 Siddharth Dushantha，**PGP 签名已验证**）→ **真实活跃**（近 8 年老项目，仍有实质提交与 281 位贡献者） |
| 最新版本 | **v0.16.0**（仓库共 2 个 Release；`pyproject.toml` 在 2026-05-02 有过版本变更提交） |
| 体积 | 约 19 MB（API `size` 19,099 KB） |
| 官方站点 | https://sherlockproject.xyz/ （站点列表页 `/sites`，自称覆盖 **400+** 社交网络） |
| topics | `osint`、`reconnaissance`、`information-gathering`、`pentesting`、`redteam`、`cti`、`forensics`、`cybersecurity`、`infosec`、`cli`、`python` 等 |
| 结构要点 | 代码在 `sherlock_project/`；文档在 `docs/`（含 56 KB 的 `docs/removed-sites.md`，记录被移出清单的站点）；有 `Dockerfile`、`pytest.ini`、`tox.ini`、`.actor/`（GitHub Actions 相关） |

> **注意一点**：GitHub 网页快照上显示的是 "83k stars / 82 Issues"，明显滞后于 API 的 95,461 / 356。**同一页面不同位置的数字都可能是缓存的**，取数一律以 API 为准（本仓库索引页已约定此口径）。

## 它能做什么

**安装**

| 方式 | 说明 |
| --- | --- |
| `pipx install sherlock-project` | 也可用 `pip` / `uv` 代替 `pipx` |
| `docker run -it --rm sherlock/sherlock` | 官方镜像 |
| `dnf install sherlock-project` | 官方维护的发行版包 |

另有社区维护包（Debian ≥ 13、Ubuntu ≥ 22.10、Homebrew、Kali、BlackArch），**仓库明确说明这些不由 Sherlock Project 直接支持**。README 顶部还带一条警告：ParrotOS 与 Ubuntu 24.04 的第三方包**似乎是坏的**，建议改用 `uv` / `pipx` / `pip` 或 Docker。

**基本用法**

```bash
sherlock user123                # 查一个用户名
sherlock user1 user2 user3      # 查多个
sherlock --help                 # 查看全部参数
```

结果默认**每个用户名一个 txt 文件**；也可导出 CSV / XLSX / JSON。

**常用参数（v0.16.0 `--help` 实测列表）**

- 输出控制：`--csv`、`--xlsx`、`--json JSON_FILE`、`--txt`、`--print-all`、`--print-found`、`--no-color`
- 范围控制：`--site SITE_NAME`（可多次指定）、`--nsfw`（把 NSFW 站点也纳入检查）、`--ignore-exclusions`（忽略上游排除项，官方注明**可能返回更多误报**）
- 网络：`--proxy PROXY_URL`（如 `socks5://127.0.0.1:1080`）、`--timeout TIMEOUT`（默认 60 秒）
- 其他：`--verbose`、`--dump-response`（调试用，打印 HTTP 响应）、`--browse`（在默认浏览器打开全部结果）、`--local`（强制用本地 `data.json`）

> 与网上流传的旧资料不同：**当前版本的 `--help` 里已经没有 `--tor` / `--unique-tor` / `--rank` 这几个参数**。看老教程时注意版本差异。

**站点清单的维护机制**

Sherlock 不接受成人站点进标准列表；站点是长期会被移除的（`docs/removed-sites.md` 已有 56 KB 记录，`--nsfw` 与排除列表也是为此存在）。**"支持 400+ 站点"是一个会缩水的动态清单**，别当成稳定承诺。

## 使用前需要注意

**① 结果的语义：只证明"用户名被占用"，不证明是谁。**如上。这是判断结论前必须先跨过去的坎；`--ignore-exclusions` 会进一步放大误报。

**② 用途边界取决于目的与当地法律。**用于自查、授权渗透、取证是常见正当场景；用于追踪特定个人则可能触及隐私与法律红线。**本笔记不作合规定性。**

**③ 结果可靠性受站点反爬与限流影响。**大量并发请求容易触发目标站点限流甚至封禁本机 IP；README 与文档均把"部分站点响应不稳定"当作常态，建议用 `--proxy`。**拿到"未找到"不等于账号不存在**，只是该次探测没命中。

**④ 站点清单会随上游改版持续变动。**失败率与误报率都会随时间漂移，**别把一次运行结果当成长期事实**。

**⑤ 未运行、未审计。**以上全部来自仓库说明页、`docs/` 目录、Release 列表、API 元数据与文件树；我没有安装、没有运行、**没有对任何真实用户名发起过查询**，也没有审计其探测逻辑。

## 什么时候会再看

- 需要做**自己的账号足迹盘点**（看看自己这个用户名在哪些平台被占用），或授权范围内的 **OSINT / 红队信息搜集**时。
- 想参考"**用一个数据文件（站点清单）+ 通用探测逻辑驱动数百个目标**"这种把配置与代码分离的结构时。
- 想研究"**公开信息的批量枚举**这类工具的文档该写清哪些边界"（存在性判定、误报、排除列表、NSFW 开关）。
- 想复盘"**同一份 README 在 raw / jsDelivr / 仓库渲染三条通道下结果不同**"这个核验实例时（详见核心结论第 ③ 条）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为面向安全研究，但形态不同**：[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)——那是"**给 AI 用的能力包 + 授权闸门**"，本篇是"**给人用的单点 CLI 工具**"；两者都涉及渗透/OSINT 类能力，使用前都要确认授权范围。
- **同属"安全资料/工具"，组织方式相反**：[the-book-of-secret-knowledge](the-book-of-secret-knowledge-信息安全与运维清单集.md)——那是**人查的清单索引**（无代码），本篇是**可直接执行的工具**；可对照"清单"与"工具"两类资源该怎么分别记录。
- **同为"取平台信息"，目的不同**：[bb-sites](bb-sites-把网站变成命令行指令的适配器集.md)、[galaxy-downloader](galaxy-downloader-多平台媒体下载器.md) 是**取内容/媒体**（依赖自己的登录态），本篇是**探测账号是否存在**（不需要任何账号）——三者的"授权与隐私边界"性质完全不同。

## 参考来源

- 项目仓库：https://github.com/sherlock-project/sherlock
- 仓库说明页实际内容（`docs/README.md`，版本 0.16.0）：https://raw.githubusercontent.com/sherlock-project/sherlock/master/docs/README.md
- 官方站点与站点列表：https://sherlockproject.xyz/ 、https://sherlockproject.xyz/sites
- 元数据与活跃度：GitHub REST API（`/repos`、`/commits?sha=master`、`/git/trees/master`、`/contents/docs`），2026-11-03
- Release：https://github.com/sherlock-project/sherlock/releases/tag/v0.16.0

以上信息核验于 2026-11-03（元数据取自 GitHub API，活跃度经默认分支 `master` 的 commits 接口复核，说明书内容取自 `docs/README.md`）。未安装、未运行、未对任何真实用户名发起查询、未审计探测逻辑。
