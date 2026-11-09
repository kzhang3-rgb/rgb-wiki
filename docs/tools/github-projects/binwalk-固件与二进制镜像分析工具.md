# binwalk：固件与二进制镜像分析工具

> 手上有一套固件或来路不明的二进制镜像，想知道里面藏了哪些文件、要不要先跑一遍熵分析时，看这篇。

- 状态：草稿
- 最近核验：2026-11-09（读取了 `README.md` 全文、`LICENSE` 首部，以及 ungh.cc 镜像返回的仓库元数据；未安装、未 cargo 构建、未对任何固件运行 `binwalk`）
- 适用范围：固件分析为主，兼及通用"从文件里找出内嵌数据"的场景。**注意当前仓库是重写后的 Binwalk v3，语言已从 Python 换成 Rust**；使用时不要照搬旧版（v2，Python）的命令与文档。

## 核心结论

Binwalk 是一个固件分析工具（事实，仓库描述 "Firmware Analysis Tool"）。当前 README 明确写这是 **Binwalk v3**，是用 **Rust 重写**的更新版本，目标是更快、更准（事实）。它的核心能力是：**识别、并可选地提取内嵌在其他文件中的文件与数据**；主要面向固件分析，但支持多种文件与数据类型；通过**熵分析**还能帮助识别未知的压缩或加密数据；也可作为 **Rust 库被集成**进你自己的项目（均为 README 明述）。

判断：最值得记住的一点是**版本换代带来的语言迁移**——旧版是 Python 生态里广为流传的 binwalk，v3 是 Rust 重写，安装方式（Docker / cargo / 源码编译）与集成方式（作为 Rust 依赖）都围绕新实现组织。检索资料时务必区分 v2 与 v3。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [ReFirmLabs/binwalk](https://github.com/ReFirmLabs/binwalk) |
| 描述定位 | "Firmware Analysis Tool" |
| 许可证 | MIT（根目录 `LICENSE`，约 1,065 字符；版权署名 "Copyright (c) 2024 devttys0"） |
| 主语言 | **Rust 99.4%**（项目已用 Rust 重写；旧版为 Python） |
| 关注度 | 14,631 Star / 359 关注 / 1,872 Fork（ungh.cc 镜像，核验日 2026-11-09） |
| 维护状态 | 2013-11-15 创建；最近推送 2026-08-11；开放 Issue 65 |
| 发行版 | 11 个 Release / 12 个 tag |
| 话题标签 | （空） |

（Star/Fork/Issue/推送时间等为易变信息，以上取 2026-11-09 的 ungh.cc 镜像值；GitHub API 匿名配额已用尽，未取到 `size`；license 与语言为 GitHub 页面元数据。）

## 功能面

均来自 README：

- **识别内嵌数据**：可以识别（并可选提取）嵌入在其它文件里的文件与数据。
- **以固件为主**：主要面向固件分析，但支持"广泛"的文件与数据类型（README 链接到 Wiki 的 Supported-Signatures 页查看清单）。
- **熵分析**：通过熵分析（entropy analysis），有助于识别未知的压缩或加密。
- **可库化集成**：可被定制，并作为 Rust 库集成进自己的项目。
- **安装三选一**：最省事的方式是**构建 Docker 镜像**；也可通过 Rust 包管理器 **cargo 安装**；或**从源码编译**。
- **使用极简**：README 给的示例就是 `binwalk DIR-890L_AxFW110b07.bin`，更高级选项查看 `--help` 或 Wiki。

## 使用前需要注意

- **版本迁移风险**：仓库现为 Rust 重写的 v3，与你可能在别处见到的 Python 版行为、参数、插件机制都不同；照搬旧教程可能不适用（判断）。
- **默认分支是 `master`**：拉 raw 文件、写脚本引用分支时不要按 `main` 习惯来。
- **安装依赖**：README 的三种安装方式各有前置条件——Docker 方案需 Docker 环境，cargo 方案需 Rust 工具链，源码编译同理；本笔记未逐一验证（事实 + 未验证项）。
- **提取的合规性**：分析固件/二进制常涉及固件逆向与可能的授权边界，请自行确认合法性与授权范围（判断，非项目声明）。
- **能力边界**：README 把"识别与可选提取"作为核心，是否支持某具体文件类型需查 Wiki 的签名清单，README 正文未逐条列出。
- **未验证项**：我没有安装、没有 cargo 构建、也没有对真实固件运行，示例命令与安装路径均按 README 转述。

## 什么时候会再看

- 拿到固件/镜像先做"有哪些东西、从哪切、要不要解压"的初判时。
- 需要区分是压缩还是加密、且打算用熵图辅助判断时。
- 想把"识别内嵌数据"这一步作为库集成进自己的 Rust 工具时。

## 相关笔记

- **新一代固件提取工具**：[unblob](unblob-从任意容器格式中提取文件.md)——同样面向从容器/固件中提取文件，可与 binwalk 互相印证结果。
- **先判类型再提取**：[Detect-It-Easy](Detect-It-Easy-文件类型与加壳识别工具.md)——提取前先识别文件类型与是否加壳，常与 binwalk 配合使用。
- **库级解析可执行格式**：[LIEF](LIEF-可执行文件格式解析与改写库.md)——当需要以代码方式解析/改写 PE、ELF 等格式时更合适。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/ReFirmLabs/binwalk/blob/master/README.md
- 许可证文件：https://github.com/ReFirmLabs/binwalk/blob/master/LICENSE
- 仓库元数据（Star/Fork/创建与推送时间/默认分支）：https://ungh.cc/repos/ReFirmLabs/binwalk
- 支持的签名清单与用法 Wiki：https://github.com/ReFirmLabs/binwalk/wiki

以上信息核验于 2026-11-09。
