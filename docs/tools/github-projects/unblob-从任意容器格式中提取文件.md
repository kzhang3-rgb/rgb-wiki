# unblob：从任意容器格式中递归提取文件的开源提取套件

> 当你拿到一个固件镜像或未知二进制 blob，想知道里面到底打包了哪些归档、压缩流和文件系统，并一次性把它们剥出来时，看这篇就够了。

- 状态：草稿
- 最近核验：2026-11-09（阅读了 `onekey-sec/unblob` 默认分支 `main` 的 `README.md` 与元数据；未安装、未运行、未 clone，未验证实际提取效果）
- 适用范围：面向固件/镜像逆向的提取场景；需要 Python ≥ 3.10（源码安装还需 `uv` 与 Rust 工具链），命令行使用还需自行安装一批外部提取器；官方推荐 64 位环境，Docker 镜像可免去手动装依赖

## 核心结论

unblob 是一个用 Python 编写的**二进制 blob 提取套件**：它解析未知二进制数据，识别其中的归档、压缩流与文件系统格式，并把内容**递归**提取出来，同时把无法识别的部分作为"未知块"切分出来。按 README 自述，它支持 **78+ 种**格式（SquashFS、JFFS2、UBI/UBIFS、ext、CPIO、ZIP、7-Zip、gzip、XZ、LZMA、LZ4 等），默认递归深度 10 层（可配置），并会生成带偏移、大小、熵值等信息的 JSON 元数据报告。

**事实层面**：它定位为"固件镜像提取、分析与逆向的搭档（perfect companion）"，强调精确的块起止偏移识别、未知块 carve（含自动识别 null/`0xFF` 填充）、基于香农熵与卡方概率的熵分析、多进程并行、可扩展的插件系统，且普通用户权限即可运行、无需提权。**判断层面**：它更像是"批量、自动化地把固件里的层层容器拆开"的工具，适合固件分析与 CTF 硬件题中"先摸清镜像里有什么"的第一步；它本身不是反汇编/反编译工具，提取完的二进制仍需交给别的工具继续分析。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [onekey-sec/unblob](https://github.com/onekey-sec/unblob) |
| 描述定位 | "Extract files from any kind of container formats"——从任意容器格式中提取文件的提取套件 |
| 许可证 | MIT（根目录 `LICENSE`，1,068 字符） |
| 主语言 | Python 97.7% |
| 关注度 | 2,638 Star / 17 关注 / 159 Fork；开放 Issue 25（核验 2026-11-09） |
| 维护状态 | 2021-06-08 创建；最近推送 2026-11-07；默认分支 `main` |
| 发行版 | 23 个 Release / 27 个 tag |
| 话题标签 | archive, compression, extraction, filesystem, python |

## 用法要点

以下要点均来自 README 正文：

- **命令行**：`unblob firmware.bin` 默认把输出写到 `<文件名>_extract/`；`-e` 指定输出目录；`--report report.json` 生成 JSON 元数据报告。
- **递归与熵**：`-d 5` 限制递归深度（默认 10）、`-n 2` 设置熵计算深度（默认 1，`0` 表示关闭）。
- **跳过与插件**：`--skip-magic "POSIX tar archive"` 按魔数前缀跳过；`-P ./myplugins/` 在运行时加载自定义 handler/extractor 插件（`--plugins-path`）。
- **其它开关**：`-p` 指定并行 worker 进程数（默认取 CPU 核数）、`-k` 保留已提取的 chunk、`--delete-extracted-files` 删除中间文件、`-v/-vv/-vvv` 提高日志级别、`--show-external-dependencies` 列出所需外部工具及其状态。
- **安装方式**：`pip install unblob`（需另装外部提取器，如 `p7zip-full`、`unar`、`e2fsprogs`、`lz4`、`zstd` 等；SquashFS 需额外装 `sasquatch`）；Docker 用 `ghcr.io/onekey-sec/unblob:latest`（内置全部提取器，免配置）；也支持 Kali（`apt install unblob`）、Nix 与源码安装。
- **Python API**：可 `from unblob.processing import ExtractionConfig, process_file`，用 `process_file(config, Path("firmware.bin"))` 编程调用，`ExtractionConfig` 接受与 CLI 相同的选项。
- **测试与文档**：用 pytest，集成测试夹具存于 Git LFS；完整文档在 [unblob.org](https://unblob.org)（安装、用户指南、支持格式清单、API、开发指南）。

## 使用前需要注意

- **外部依赖不少**：pip 安装只装本体，真正的解压/解析依赖一串系统级工具；SquashFS 还需额外的 `sasquatch`（来自 onekey-sec 的 release）。想省事可直接用 Docker 镜像。
- **Rust 工具链**：从源码构建需要 Rust（用于编译扩展），这会劝退只想跑 CLI 的用户。
- **许可证为 MIT**：根目录 `LICENSE` 为 MIT 文本（1,068 字符）；但本笔记**未逐项核对**其捆绑/调用的各外部提取器各自的开源许可，实际合规请自行确认。
- **未验证项**：本笔记仅读 README 与元数据，**未安装、未运行、未 clone**，也没有实测其"78+ 格式"覆盖度、递归提取准确性或在具体固件样本上的表现；README 中的性能与鲁棒性表述（"battle-tested"、"fuzz tested"）属项目自述，未在本笔记中独立验证。
- **易变信息**：Star/Fork/Issue/Release/tag 等为 2026-11-09 的快照，随项目演进会变化。

## 什么时候会再看

- 拿到新固件/未知镜像，需要先"拆开看看里面是什么"时。
- 想确认某格式（如 SquashFS、UBIFS、CPIO）是否被支持，需要查 [unblob.org/formats](https://unblob.org/formats/) 时。
- 需要写自定义格式 handler/extractor 或把 unblob 集成进自动化流水线（JSON 报告 + Python API）时。
- 想对比它和 binwalk 在提取策略与准确性上的差异时。

## 相关笔记

- **同类固件提取工具对比**：[binwalk](binwalk-固件与二进制镜像分析工具.md)——同样是固件/二进制分析常用工具，常与 unblob 相互印证提取结果。
- **识别文件真实类型**：[Detect-It-Easy](Detect-It-Easy-文件类型与加壳识别工具.md)——在提取前后判断容器/打包器类型，可与 unblob 配合。
- **解析可执行文件格式**：[LIEF](LIEF-可执行文件格式解析与改写库.md)——提取出 ELF/PE 等产物后做结构化解析时使用。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：<https://github.com/onekey-sec/unblob>（README 取自 `raw.githubusercontent.com/onekey-sec/unblob/main/README.md`）
- 许可证文件：<https://raw.githubusercontent.com/onekey-sec/unblob/main/LICENSE>
- 官方文档站：<https://unblob.org>
- 元数据（Star/Fork/Issue/时间等，2026-11-09 核验）：ungh.cc 镜像 `https://ungh.cc/repos/onekey-sec/unblob` 与 shields.io

以上信息核验于 2026-11-09。
