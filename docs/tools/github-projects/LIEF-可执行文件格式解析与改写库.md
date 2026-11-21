# LIEF：解析与改写 ELF/PE/Mach-O 的可执行文件格式库

> 当你想用同一套 API 读、改、或抽象多种可执行文件格式（ELF / PE / Mach-O，以及 Android 的 OAT/DEX/VDEX/ART）时，来查这篇；它解决的是"格式很多、解析各自为政"的问题。

- 状态：草稿
- 最近核验：2026-11-09（阅读了仓库 README 全文与根 LICENSE 元信息；**未安装、未运行、未 clone**）
- 适用范围：跨平台库。官方称支持 C++、Python、Rust 三种 API；另有非官方 Node.js 绑定。适用于逆向工程、二进制分析、恶意样本研究、Android 格式解析等场景；不面向前端或普通应用开发。

## 核心结论

**事实**：LIEF 是一个跨平台库，用于解析（parse）、修改（modify）和抽象（abstract）可执行文件格式。README 明确列出可解析 ELF、PE、MachO、COFF、OAT、DEX、VDEX、ART，并提供 API 访问其内部结构；"修改"指能改动格式的某些部分（如新增 section、改符号名）；"抽象"指把各格式共有的 section、symbol、entry point 等特性统一起来。

**判断**：它的定位更像是"可执行文件格式的操作底座"——上层工具（反汇编器、逆向框架、样本分析流水线）可以借它省去自己写格式解析器。作者 Romain Thomas 曾任职 Quarkslab，README 中的 BibTeX 也以 2017 年 Quarkslab 时期信息登记。是否适合你的具体场景，取决于你要处理的格式与语言绑定是否在支持列表内。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [lief-project/LIEF](https://github.com/lief-project/LIEF) |
| 描述定位 | LIEF - Library to Instrument Executable Formats（C++、Python、Rust） |
| 许可证 | Apache-2.0（根 `LICENSE`，约 11,381 字符；README 中指向 `LICENSE` 链接） |
| 主语言 | C++ 60.7% |
| 关注度 | 5,647 Star / 129 关注 / 761 Fork（核验日 2026-11-09） |
| 维护状态 | 2017-03-16 创建；最近推送 2026-11-08；**仍活跃**；开放 Issue 24；默认分支 `main` |
| 发行版 | 43 个 Release / 43 tags；README 指向 v1.0.0 稳定版与 nightly 构建 |
| 话题标签 | android, art, binary-analysis, dex, elf, executable-formats, lief, macho, malware-analysis, modification, oat, parser, parsing, pe, python, reverse-engineering, rust, sdk, vdex |

> 以上关注度、创建/推送时间、Release 与 Issue 数均为易变信息，核验于 2026-11-09，来源为 ungh.cc 镜像与 shields.io（GitHub API 匿名配额用尽）。

## 怎么用

**格式解析**：README 给出的首选用法是 `lief.parse(...)`。Python 示例里对 ELF 遍历 `binary.sections`，对 PE 读取 `binary.rich_header`，对 Mach-O 读取 `binary.dyld_chained_fixups`。

**安装渠道**（均来自 README）：

- Python：`pip install lief`（最新发行版）；nightly 用 `pip install --force-reinstall --index-url https://lief.s3-website.fr-par.scw.cloud/latest lief==2.0.0.dev0`。
- Rust：Cargo.toml 中声明 `lief = "1.0.0"`。
- Homebrew：`brew install lief`。
- C++：CMake 中 `find_package(LIEF REQUIRED)` 后 `target_link_libraries(my-project LIEF::LIEF)`。

**语言绑定**：C++（Doxygen 文档）、Python、Rust 为官方；Node.js 绑定为**非官方且 README 明确标注 "AI-generated"**（指向 Piebald-AI/node-lief）。

**扩展能力**（README 单列的 Extended features）：Runtime、DWARF/PDB 调试信息、Objective-C 元数据、Dyld Shared Cache（支持提取 Dylib，即从共享缓存中剥离动态库）、反汇编器（AArch64、x86/x86-64、ARM、RISC-V、Mips、PowerPC、eBPF）、汇编器（AArch64、x86/x86-64、RISC-V）。

**插件**：Ghidra 与 BinaryNinja 各有官方插件文档。

**资源**：主文档、Doxygen、Rust 文档托管在 lief.re；另有博客与 Discord 社群；README 列出多个教程（解析与操作格式、ELF 符号、PE 资源、把 ELF 可执行文件转成库、在非 root 设备上用 frida、Android 格式、Mach-O 修改、ELF coredump、PE Authenticode）。

## 使用前需要注意

- **许可**：Apache-2.0，允许商业使用与修改，但需遵守许可证条款（保留声明、标明变更等）。使用前建议自行阅读根 `LICENSE` 原文；本笔记仅记录其存在与篇幅。
- **Node.js 绑定非官方**：README 自己标注该绑定为 AI 生成、非官方，生产环境慎用。
- **部分扩展能力可能收费或受限**：README 的 Packages 里单列 "LIEF Extended: https://extended.lief.re（GitHub OAuth）"，暗示存在独立的 Extended 版本；但 README 未详细说明其与开源版的差异，**此点仅凭 README 描述，未验证**。
- **PyPI 版本与 nightly 并存**：稳定版与 nightly 用不同索引地址，混用可能出问题。
- **未验证项**：本笔记未安装、未编译、未运行任何示例代码，也未验证 API 行为是否与 README 示例一致；版本号、tag 数等随发布变化。

## 什么时候会再看

- 需要写脚本批量读取或改某类可执行文件的 section / symbol / 头部字段时。
- 需要评估"自己解析还是用现成库"时，比对它支持的格式清单与语言绑定。
- 需要处理 Android 的 DEX / OAT / VDEX / ART 或 Mach-O 的 dyld 相关结构时。

## 相关笔记

- **同为逆向/二进制分析工具，负责交互式反编译**：[ghidra](ghidra-NSA开源的软件逆向工程框架.md)——LIEF 提供 Ghidra 官方插件，两者常配合使用。
- **同为二进制格式分析，偏"拆包提取"**：[unblob](unblob-从任意容器格式中提取文件.md)——LIEF 关注单一可执行格式的解析与改写，unblob 关注从容器中递归提取。
- **固件/镜像分析里的格式识别与提取**：[binwalk](binwalk-固件与二进制镜像分析工具.md)——与 LIEF 的格式解析能力互补。
- **识别文件类型与加壳的工具**：[Detect-It-Easy](Detect-It-Easy-文件类型与加壳识别工具.md)——常与 LIEF 出现在同一条逆向分析流程里。
- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"处理 Mach-O"，场景不同**：[fairplay-decrypt：离线解密 App Store IPA 的 FairPlay 工具](fairplay-decrypt-离线解密App-Store-IPA的FairPlay工具.md)——它解密 iOS IPA 时会处理 **arm64 Mach-O**（解密加密页并清除 `cryptid`），可视为"**面向 Mach-O 的一种特定改写**"；LIEF 提供通用的 Mach-O / PE / ELF 解析与改写能力，两者在"处理 Mach-O"上互补（⚠️ 前者涉及 DRM 规避，见该篇的法律与授权提示）。

## 参考来源

- 项目仓库与 README：https://github.com/lief-project/LIEF ，README 原文：https://raw.githubusercontent.com/lief-project/LIEF/main/README.md （默认分支 `main`）
- 官方文档站：https://lief.re/doc/latest/index.html
- 元数据兜底：https://ungh.cc/repos/lief-project/LIEF 与 https://img.shields.io/github/... （核验日 2026-11-09）

以上信息核验于 2026-11-09。
