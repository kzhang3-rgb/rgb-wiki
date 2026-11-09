# Detect-It-Easy：跨平台文件类型与加壳识别工具

> 当你拿到一个来路不明的可执行文件，想先弄清"它是什么类型、用什么编译、是不是被加壳或保护"时，来查这篇；它解决的是逆向工程与恶意样本分析里最靠前的一步——静态摸底。

- 状态：草稿
- 最近核验：2026-11-09（阅读了仓库 README（约 30,733 字符）的主要章节与功能列表，以及根 LICENSE 元信息；**未安装、未运行、未 clone**）
- 适用范围：桌面与命令行工具。README 称支持 Windows、Linux、MacOS；面向恶意软件分析、逆向工程、数字取证。**README 明确声明它不是杀毒软件**。

## 核心结论

**事实**：Detect It Easy（简称 DiE）是一个跨平台的文件识别与静态检查工具，面向恶意软件分析师、安全专家与逆向工程师。它把"原生格式解析器"和"可扩展的 DiE-JS 分析层"结合起来：原生核心负责解析文件格式并提供受限的读取、寻址、搜索、反汇编原语；DiE-JS 模块则在之上实现签名匹配与多阶段分析算法。桌面版还集成了多个独立分析引擎（Nauz File Detector、YARA、PEiD）。

**判断**：它的价值不在于"给文件打分判毒"，而在于把"这个文件大概由什么构建、是否像原始文件/被 dump 或重建、可能用了哪种保护、下一步该看什么"整理成一份可解释的证据图。README 反复强调启发式结论一律带 `(Heur)` 标记，不与精确签名结论混淆——这个"结论可见、可区分"的设计取向值得留意。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [horsicq/Detect-It-Easy](https://github.com/horsicq/Detect-It-Easy) |
| 描述定位 | 用于判断 Windows、Linux、MacOS 文件类型的程序（Program for determining types of files for Windows, Linux and MacOS.） |
| 许可证 | MIT（根 `LICENSE`，约 1,085 字符） |
| 主语言 | JavaScript 60.3%（**注意**：统计占比最高的是 JS，因为类型/壳签名与分析算法用 DiE-JS 编写；工具核心仍为 C++） |
| 关注度 | 11,911 Star / 170 关注 / 998 Fork（核验日 2026-11-09） |
| 维护状态 | 2014-06-01 创建；最近推送 2026-11-08；**长期活跃**；开放 Issue 31；默认分支 `master` |
| 发行版 | 4 个 Release / 4 tags |
| 话题标签 | binary-analysis, debugger, detect, detector, disassembler, elf, entropy, malware-analysis, malware-research, packer, pentest, program-analysis, reverse-engineering, scanner, static-analysis, unpacker |

> 以上关注度、创建/推送时间、Release 与 Issue 数均为易变信息，核验于 2026-11-09，来源为 ungh.cc 镜像与 shields.io（GitHub API 匿名配额用尽）。README 内嵌的 shields.io 徽章也引用了 `horsicq/DIE-engine` 仓库的 tag 与下载量，与主仓库名不同，使用时留意区分。

## 功能面

**支持的格式**（README 单列）：PE、ELF、APK、IPA、JAR、ZIP、ISO9660、DEX、MS-DOS、COM、LE/LX、MACH（Mach-O）、NPM（JavaScript 包）、Amiga、Binary（其它未分类文件）。README 称列表仍在扩充，未知格式会走启发式分析。

**三个版本**：

- `die` —— 图形界面版；
- `diec` —— 命令行版，适合批处理；
- `diel` —— 轻量 GUI 版（仅扫描）。

**引擎分层**（README "Several engines under one GUI"）：

- **Detect It Easy 本体**：格式感知的主扫描器，DiE-JS 模块从紧凑签名到有状态分析引擎不等；
- **Nauz File Detector (NFD)**：独立视角识别 linker、compiler、工具与 packer，README 称其启发式更简单、数据库更新较慢，宜作第二意见；
- **YARA**：规则匹配，DiE 自带基础版与增强版 YARA 启发式规则；
- **PEiD**：为兼容经典老式检测器及其 `userdb` 生态而内置，README 提醒其许多签名在现代标准下噪声较大、易产生"看起来很可信的误报"，建议当作次要证据。

**启发式引擎**：README 用较长篇幅介绍 PE 启发式引擎（如 `db/PE/__GenericHeuristicAnalysis_By_DosX.7.sg`，由 DosX 创建维护），覆盖 .NET 混淆与反分析、原生反分析、原生异常、加壳与保护、授权/DRM、格式完整性、调试残留、恶意相关模式、工具链/平台推断、文件名异常等方向。**重要说明**：分析过程中**文件从不被执行**，引擎只处理头部、数据目录、节区、导入导出、资源、.NET 元数据、字节码、overlay、调试记录以及从入口点可达的启动代码。

**规则写法**：数据库条目是 DiE-JS 模块，介于紧凑签名与有状态多阶段分析器之间。README 给出一段最小规则示例（`meta(...)` + `detect()` + `return result();`），并列出运行期结果字段 `bDetected`、`sName`、`sVersion`、`sOptions`、`sLang`、`sType`。规则按格式归类到目录：PE 规则放 `db/PE`，ELF 放 `db/ELF`，`db/Binary` 只放未分类或真正与格式无关的数据；`db` 为审核过的主库，未批准的规则加 `extra` 标签，`dbs_min` 为生成产物、不可手改。

**安装方式**（README）：

- Windows：Chocolatey 包 `die`、Microsoft Store；
- Linux：Parrot OS（包名 `detect-it-easy`）、Arch AUR（`detect-it-easy-git`）、openSUSE OBS、REMnux；
- Docker：克隆仓库后 `docker build . -t horsicq:diec`（README 注明用 `git clone --recursive`）；
- 另有 Telegram 机器人 `@detectiteasy_bot` 可用于快速查文件。

## 使用前需要注意

- **它不是杀毒软件**：README 明确说明 DiE 不提供实时防护或清除功能，"干净"的报告只代表启用的规则没有找到它们认识的静态证据，不代表文件安全。README 还声明启发式结果只是线索，不等于恶意行为的自动证明。
- **误报与时效**：启发式引擎更新快于 README，规则集不断变化；PEiD 规则被作者本人提醒噪声较大。用结果做判断时应保留怀疑。
- **钓鱼站点警示**：README 顶部 `[!WARNING]` 明确指出 `detectiteasy.com` 与本项目及其官方网站**无关联**，不要把它当官方来源、也不要用它下载 DiE，只从仓库提供的 release 链接下载。
- **许可证**：MIT（根 `LICENSE`，约 1,085 字符），宽松许可，可自由使用与再分发。使用前建议自行阅读 `LICENSE` 原文。
- **主语言统计易被误读**：GitHub 显示 JavaScript 占比最高，是签名/算法用 JS（DiE-JS）编写所致；不要据此以为这是前端项目。
- **仓库名差异**：README 中的 releases 与部分徽章指向 `horsicq/DIE-engine`，而本笔记记录的仓库是 `horsicq/Detect-It-Easy`，两者关系 README 未展开说明，**此点存疑、未验证**。
- **未验证项**：本笔记未下载发行版、未安装、未运行任何扫描，也未逐条核对 README 中列出的恶意家族名单与检测能力。

## 什么时候会再看

- 拿到可疑 PE/ELF/APK 等文件，需要先做一轮类型、编译器、加壳/保护的静态摸底时。
- 需要把"文件像不像原始、是否被 dump 或重建、下一步查什么"整理成证据去判断时。
- 想参考"启发式结论如何与精确签名结论分开呈现"的可解释设计，或想写 DiE-JS / YARA 规则时。

## 相关笔记

- **同为二进制/固件分析，偏镜像拆分与提取**：[binwalk](binwalk-固件与二进制镜像分析工具.md)——DiE 负责识别类型与加壳，binwalk 负责从镜像中定位与提取内容。
- **递归拆容器格式的提取工具**：[unblob](unblob-从任意容器格式中提取文件.md)——与 DiE 的识别能力互补。
- **可执行文件格式解析与改写库**：[LIEF](LIEF-可执行文件格式解析与改写库.md)——若要自己写解析/改写逻辑而非只做识别，可对照 LIEF 的能力面。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/horsicq/Detect-It-Easy ，README 原文：https://raw.githubusercontent.com/horsicq/Detect-It-Easy/master/README.md （默认分支 `master`）
- DiE API 库（面向开发者）：https://github.com/horsicq/die_library
- 元数据兜底：https://ungh.cc/repos/horsicq/Detect-It-Easy 与 https://img.shields.io/github/... （核验日 2026-11-09）

以上信息核验于 2026-11-09。
