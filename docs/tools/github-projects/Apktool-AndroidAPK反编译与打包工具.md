# Apktool：Android APK 反编译与重打包工具

> 需要把 APK 拆成资源和 smali、改完再打包回 APK，或想搞清楚它到底能不能、该不该用来改别人 App 时，看这篇。

- 状态：草稿
- 最近核验：2026-11-09（读取了 `README.md` 全文、`LICENSE.md` 首部，以及 ungh.cc 镜像返回的仓库元数据；未下载发行版、未安装 Java、未执行 `apktool d/b`，也未反编译任何 APK）
- 适用范围：Android 应用的**资源与 smali 层**逆向工程。README 强调其针对"第三方、闭源、二进制 Android 应用"，可解码资源并在修改后重建；不适用于需要 Java 源码级逆向的场景（那是 dex→Java 反编译工具的范畴）。

## 核心结论

Apktool 是一个第三方 Android 闭源二进制应用的反编译工具（事实，来自 README 首段与仓库描述）。它的核心能力是：**把资源解码到接近原始形态，修改后能重新打包**；并能**逐步调试 smali 代码**；它用类似工程的目录结构组织结果，并自动化诸如"构建 apk"之类的重复任务（均为 README 明述）。

README 明确声明：Apktool **不**用于盗版及其他非法用途，其正当用途举例包括**本地化、添加功能、增加对自定义平台的支持**等（事实）。判断：它属于"改包/汉化/研究"类工具，能力强但边界敏感，用之前应先想清楚授权与法律问题。

另有一个实操层面的判断：README 列出两条分支——`main` 是 **Apktool 3.x** 分支，`2.x` 是 2.x 系列的维护分支。因此不同大版本的命令行行为可能有差异，使用文档（apktool.org）时要对上是哪个版本。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool) |
| 描述定位 | "A tool for reverse engineering Android apk files" |
| 许可证 | Apache-2.0（**根目录文件名是 `LICENSE.md`**，不是 `LICENSE`；约 10,765 字符，开头为标准 Apache License 2.0 正文） |
| 主语言 | Java 98.5% |
| 关注度 | 27,080 Star / 671 关注 / 4,241 Fork（ungh.cc 镜像，核验日 2026-11-09） |
| 维护状态 | 2012-03-19 创建；最近推送 2026-11-02；开放 Issue 73 |
| 发行版 | 30 个 Release / 54 个 tag |
| 话题标签 | 9patch, android, apk, apktool, arsc, reverse-engineering |

（Star/Fork/Issue/推送时间等为易变信息，以上取 2026-11-09 的 ungh.cc 镜像值；GitHub API 匿名配额已用尽，未取到 `size`。）

## 能做什么

均来自 README：

- **反编译资源**：把第三方闭源二进制 Android 应用的资源解码到接近原始形态。
- **修改后重打包**：在改动的基础上把应用重建出来。
- **逐步调试 smali**：使 smali 代码可以单步调试。
- **工程化的工作方式**：采用类似工程的文件结构，并自动化诸如构建 apk 等重复任务。
- **正当用途举例**：本地化、添加功能、支持自定义平台。

## 使用前需要注意

- **合规红线**：README 明确写 "Apktool is **NOT** intended for piracy and other non-legal uses"，并呼吁对 App 作者保持公平。改动、分发他人 App 前务必确认授权与法律边界（事实 + 判断）。
- **许可证文件名特殊**：根目录是 `LICENSE.md`；做依赖清单或扫描工具时对文件名不要想当然。
- **版本分支要分清**：`main` = 3.x，另有 `2.x` 维护分支；查找资料与命令时先确定对应版本。
- **下载渠道**：README 指向的下载页是 Bitbucket（`bitbucket.org/iBotPeaches/apktool/downloads`），另有镜像 `connortumbleson.com/apktool`；说明该项目源码托管在 GitHub 但发行包发布渠道不完全在 GitHub。
- **安全报告途径**：README 指明安全漏洞请邮件联系 Connor Tumbleson（`connor.tumbleson(at)gmail.com`）。
- **未验证项**：我没有安装 Java、没有下载 jar 或运行 `apktool d/b`，README 所述能力与分支信息均按文档转述，未在真实 APK 上复核。
- **README 含赞助商内容**：README 末尾有 Sourcetoad、Emerge Tools 两家赞助商介绍，属推广位，不构成功能或质量评价。

## 什么时候会再看

- 要反编译/回编译 APK 的资源与 smali、或对 9patch、`arsc`、`apktool.yml` 之类的工程结构不熟时。
- 需要确认当前该用 `main`(3.x) 还是 `2.x` 分支的命令与行为时。
- 研究"改包/汉化/自定义平台支持"这一整条链路，需要把工具接力串起来时。

## 相关笔记

- **把 JADX 反编译接给 AI**：[jadx-ai-mcp](jadx-ai-mcp-JADX的AI反编译插件.md)——JADX 侧偏向 dex→Java 源码，与 Apktool 的 smali/资源路线互补。
- **JADX 的 MCP 服务器**：[jadx-mcp-server](jadx-mcp-server-给JADX反编译接AI的MCP服务器.md)——另一种让 AI 参与反编译的接法。
- **二进制格式的库级解析**：[LIEF](LIEF-可执行文件格式解析与改写库.md)——需要以代码方式解析/改写可执行文件格式时的选择。
- **原生库层的逆向**：[ghidra](ghidra-NSA开源的软件逆向工程框架.md)——APK 里带 `.so` 原生库时，资源/smali 之外常要落到 Ghidra 做汇编级分析。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/iBotPeaches/Apktool/blob/main/README.md
- 许可证文件：https://github.com/iBotPeaches/Apktool/blob/main/LICENSE.md
- 仓库元数据（Star/Fork/创建与推送时间/默认分支）：https://ungh.cc/repos/iBotPeaches/Apktool
- 官方站点与文档：https://apktool.org

以上信息核验于 2026-11-09。
