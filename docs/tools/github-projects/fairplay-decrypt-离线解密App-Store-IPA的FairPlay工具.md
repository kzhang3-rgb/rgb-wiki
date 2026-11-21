# fairplay-decrypt：离线解密 App Store IPA 的 FairPlay 工具

> 一句话定位（README 自述）：**"Offline FairPlay App Store IPA decryption with independent key derivation"**——用**拥有该 IPA 的 Apple 账号所对应的 16 字节凭证**，在本机**离线**解密 App Store 应用的 IPA。三件事先讲清：① 它做的是**移除 FairPlay 加密保护（DRM 规避）**——用 IPA 自身的 **SINF 与 SUPF 记录推导页密钥、解密加密的 arm64 页、清除 `cryptid`**；② 结构上分**两大块**——纯 Python 的**解密器**（`decrypt.py` + `core.py`）与一套**凭证取回**组件（`credential-retrieval/`，从**受支持的越狱设备**取回可复用的 16 字节账号凭证、产出 `credential.bin`）；③ **⚠️ 涉及 DRM 规避，存在明确的法律与授权风险**，按本仓库准则，**本笔记只记录元数据与文件结构，不复制任何操作步骤、命令或技术细节，也不对任何使用场景作背书**。

- 状态：草稿
- 最近核验：2026-11-19（阅读 `README.md` 与 `credential-retrieval/README.md`、`requirements.txt`、文件清单（44 项）与 ungh 镜像 + shields 元数据；**未 clone、未安装依赖、未构建任何 C 组件、未连接任何设备、未解密任何 IPA、未审计源码**）
- 适用范围：**持有对应 Apple 账号与应用合法授权**的使用者；解密器可跑在 **macOS 与 Linux**；**凭证取回**部分另需**越狱设备 + root SSH** 与 **macOS + Xcode + `ldid`** 构建环境

## 核心结论

**事实：README 自述的定位是"离线 + 独立密钥推导"。** 描述原文为 `Offline FairPlay App Store IPA decryption with independent key derivation`。README 说明：用"**拥有该 IPA 的账号的 16 字节凭证**"解密；凭证由 `credential-retrieval/` 从**受支持的越狱设备**取回并写成 `credential.bin`；**解密在 macOS 与 Linux 上运行**，且"**解密器本身是纯 Python**"。

**事实：它把"取凭证"与"解密"拆成了两步、两个组件。**

| 部分 | 语言 / 形态 | 作用（README 自述） | 前置条件 |
| --- | --- | --- | --- |
| `decrypt.py` + `core.py` | 纯 Python（依赖仅 `cryptography>=41`，Python 3.9+） | 读每个 IPA 的 **SINF 与 SUPF 记录推导页密钥**、解密其**加密的 arm64 页**、**清除 `cryptid`**；输出到指定路径（该路径须不存在） | 已有 `credential.bin` |
| `credential-retrieval/` | C 组件为主 + 少量 Python（`retrieve.py` / `native_ssh.py`）+ Makefile | 从**一台已配对、越狱且可 root SSH** 的设备上取回**可复用的 16 字节账号凭证**，写入 `credential.bin`；支持 iOS 10+ | 主机需 Python、`idevice_id`、`iproxy`、USB SSH；构建需 **macOS + Xcode + `ldid`** |

**事实：支持的形态（README 自述）**——**version-3 与 version-5 的 SUPF**、**448 字节与 440 字节的 SINF `priv` 记录**（440 字节记录须以 8 个零字节结尾）、**加密的 arm64 Mach-O slice**。**Apple Arcade 应用需要单独一套凭证**：普通凭证解普通 App、Arcade 凭证解 Arcade 应用，要解两类就得两套——且每套凭证只对**它来自的那个 Apple Account** 的应用有效。

**判断：它的卖点是"离线"与"独立密钥推导"，即解密不依赖设备实时在线。** 这与"从越狱设备上直接 dump 已解密文件"的路线不同——后者把设备当"解密机"，本工具主张先**一次性取回凭证**，之后在本机**离线**完成解密。代价是**必须先用越狱设备取一次凭证**，且凭证与账号绑定。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [EthanArbuckle/fairplay-decrypt](https://github.com/EthanArbuckle/fairplay-decrypt) |
| 描述（原文） | "Offline FairPlay App Store IPA decryption with independent key derivation" |
| 许可证 | **GPL-3.0**（`LICENSE` 35,149 B，README 与许可证一致；**⚠️ 但"开源许可"只解决代码再分发，不改变"解密他人受 DRM 保护应用"的合法性问题**） |
| 主语言 | Python 85.7%（shields 顶层语言；但 `credential-retrieval/` 下有多份体量可观的 C 源） |
| 关注度 | **2,449 Star / 6 关注 / 378 Fork**（ungh，2026-11-19；shields 同期 2.5k / 379）、**5 开放 Issue、贡献者 12** |
| 维护状态 | **2026-10-19 创建**（核验时约一个月）；ungh `pushedAt` **2026-11-08**、`updatedAt` 2026-11-15；默认分支 **`main`** |
| 发行 | **0 Release** |
| 体积 | **44 文件**：根 7（`README.md`、`core.py` 10,826 B、`decrypt.py` 6,034 B、`Makefile`、`requirements.txt`（内容仅 `cryptography>=41`）、`.gitignore`、`LICENSE`）+ `.github/workflows/python-checks.yml`（752 B）+ **`credential-retrieval/` 36 文件** |

**`credential-retrieval/` 目录结构（仅列骨架，不展开细节）**：`README.md`、`Makefile`、`retrieve.py`（19,199 B）、`native_ssh.py`、`arcade-capture/`（`.c` + `.entitlements.plist`）、`driver/`、`trigger/`（各含 `.c` 与 entitlements）、以及 **`credential/`**——一套 C 源（自述文件名含 AES / 模幂 / 摘要 / 选择器 / finalizer 等职责）+ **两张二进制数据表**（`static-key-table.bin` 4,096 B、`core4-word-table.bin` 262,144 B）。**本笔记不再描述其原理或用法。**

## 使用前需要注意

- **⚠️ 法律与授权（最重要）**：这是**DRM（FairPlay）规避工具**——移除 App Store 应用的加密保护。此举可能违反 **Apple 服务条款**与**反规避类法律**（如美国 DMCA §1201 一类的条款）；**仓库 README 未附使用条款 / 免责声明**。**只应在你合法拥有、且有权处理的应用上，并在当地法律允许的范围内使用**；本笔记**只记录其存在与结构，不对任何使用场景作背书**。
- **⚠️ 本笔记刻意不展开**：本项目核心内容属敏感操作范畴，按本仓库编写准则，**只记录元数据与文件结构，不复制任何命令、步骤或技术细节**（正文出现的 `idevice_id` / `iproxy` / `ldid` 等仅为 README 提到的**工具名**，不构成指引）。
- **⚠️ 前置依赖不轻**：取凭证需**越狱的 iOS 设备（iOS 10+）+ root SSH**，主机需 `idevice_id` / `iproxy` / USB SSH，构建还需 **macOS + Xcode + `ldid`**；**纯 Python 的只是解密器本身**。
- **⚠️ 极新 + 0 Release**：**2026-10-19 创建**（约一个月）、**0 Release**、5 开放 issue；Star 涨得快，但仍属**新仓库**，成熟度与长期维护未经验证。
- **⚠️ 凭证与账号绑定**：README 明确"每套凭证只对它来自的那个 Apple Account 的应用有效"，且普通应用与 Apple Arcade 应用**各需一套**凭证。
- **关于许可证**：**GPL-3.0** 是**代码许可**；它**不授予**任何"解密他人受 DRM 保护内容"的权利。
- 本笔记**只读 README ×2、`requirements.txt`、文件清单与元数据；未 clone、未安装依赖、未构建、未连接设备、未解密、未审计源码**。

## 什么时候会再看

- 想了解"**把 FairPlay 加密的 iOS 应用还原成可分析的 Mach-O**"这件事**在结构上**长什么样（`SINF` / `SUPF` / 页密钥 / `cryptid` 等**概念名词**）时（仅概念层面，不涉及做法）。
- 想对照"**从越狱设备实时 dump**"与"**取回凭证后离线解密**"两条思路的**架构差异**时。
- 想研究"**平台应用包（Android APK / iOS IPA）在'加密—打包—签名'上的差异**"时（见下相关笔记）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"把平台分发的应用包还原成可分析形态"，平台相对**：[Apktool：Android APK 反编译与打包工具](Apktool-AndroidAPK反编译与打包工具.md)——那是 **Android** 侧的 APK 反编译 / 重打包；本篇是 **iOS** 侧把 App Store IPA **解密**成可分析 Mach-O。可对照 iOS 与 Android 在"加密 / 打包 / 签名"上的差异（⚠️ 本篇涉 DRM 规避，使用时须先确认授权与合法性边界）。
- **同为"解析 / 改写可执行文件"，目标格式重叠**：[LIEF：可执行文件格式解析与改写库](LIEF-可执行文件格式解析与改写库.md)——本篇解密 IPA 时会处理 **arm64 Mach-O**（解密加密页并清除 `cryptid`），可视为**面向 Mach-O 的一种特定改写**；LIEF 提供通用的 Mach-O / PE / ELF 解析与改写能力，两者在"处理 Mach-O"上互补。
- **"上手前的还原步骤"与"该用哪个技能"**：[reverse-skill：逆向与渗透技能路由包](reverse-skill-逆向与渗透技能路由包.md)——那是**技能路由层**（答"该用哪个工具"、并在动手前设范围闸门）；本篇属**前置还原步骤**（先解密才谈得上分析）。两者使用前都需确认授权与合法性边界。
- **同为"iOS 侧改动系统行为"的开源工具**：[GestaltX：给 iOS 改 MobileGestalt 设备配置的编辑器](GestaltX-给iOS改MobileGestalt设备配置的编辑器.md)——本篇处理 **IPA 的加密与签名**（还原成可分析 Mach-O），GestaltX 改 **设备配置缓存（MobileGestalt）** 以绕地区 / 功能门控。两者都触碰 iOS 的系统边界、都需在自有或获授权的设备 / 内容上使用，可对照其风险提示与免责写法。

## 参考来源

- 项目仓库与 README：https://github.com/EthanArbuckle/fairplay-decrypt
- `credential-retrieval/README.md`、`requirements.txt`：同仓库 `main` 分支
- 元数据（Star / Fork / 创建与推送时间 / 默认分支）：ungh.cc 镜像 https://ungh.cc/repos/EthanArbuckle/fairplay-decrypt 与 shields.io 徽章

以上信息核验于 2026-11-19（仓库创建约一个月）。仅阅读两份 README、`requirements.txt`、文件清单与元数据；未 clone、未安装、未构建、未连接设备、未解密、未审计源码。**本笔记为信息记录，不构成任何使用指引或背书。**
