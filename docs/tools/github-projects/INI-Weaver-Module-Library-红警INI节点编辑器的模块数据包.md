# INI-Weaver-Module-Library：红警 INI 节点编辑器的模块数据包

> 一个没有 README 的数据仓库——它本身不能被"使用"，只能被别的工具读取，所以这篇笔记的重点是搞清它到底是什么、里面装了什么。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库文件列表、提交记录与同组织其它仓库的元数据，未下载任何文件、未配合工具运行）
- 适用范围：红警 2 / 尤里的复仇（RA2/YR）的 INI 修改生态；需配合"INI 织网者"（INIWeaver）这类工具使用

## 核心结论

这是 ra2diy 组织下的一个**纯数据仓库**：把红警 2 及其主流扩展（Ares、Phobos、Kratos、凛冬将至 WIC）的 INI 字段名、类型、模块结构整理成机器可读的映射表与词典，供 INI 编辑工具在"节点化"编辑时查表和校验。

**它不是一个能独立运行的项目**。仓库里没有 README、没有可执行代码、没有许可证，语言也未被 GitHub 识别（纯 CSV/JSON/INI/TXT）。所有提交信息都是同一句"同步数据包内容"，说明仓库内容是从上游生成流程导出的产物，而不是人工维护的源码。

这个定位很关键：**你无法从仓库自身学到用法**，只能通过文件结构反推它服务的工具需要什么数据。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [ra2diy/INI-Weaver-Module-Library](https://github.com/ra2diy/INI-Weaver-Module-Library) |
| 所属组织 | ra2diy（红警 2 DIY 社区组织） |
| 许可证 | **未提供（根目录无 LICENSE 文件，API 未检测到）** |
| 语言 | 未被 GitHub 识别（数据文件为主） |
| 关注度 | 15 Star、2 Fork、0 关注、1 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2026-10-01 创建，最近推送 2026-11-01（**仍在同步**） |
| 体积 | 约 1,300 KB（`size` 字段） |
| Git 标签 | 三个，名称为 `data` / `app` / `FullVersion`——不像语义化版本，更像自动化发布留下的标记 |

> README 缺失已核实：`/readme` 端点返回失败，仓库无描述字段。

## 仓库里到底有什么

根目录的文件分三类，都围绕"字段"这件事：

**① 字段别名映射表（体积最大的部分）**

| 文件 | 大小 | 推测用途（依据文件名） |
| --- | --- | --- |
| `TypeAlt2Ares.csv` | 约 296 KB | Ares 引擎的字段别名映射 |
| `TypeAlt2Unit.csv` | 约 228 KB | 单位（Infantry/Vehicle/Building）相关字段 |
| `TypeAlt2PhobosB48.csv` | 约 203 KB | Phobos 引擎 **B48 版本**的字段集 |
| `TypeAlt3Kratos.csv` | 约 149 KB | KratosAE 扩展的字段 |
| `TypeAlt2Art.csv` | 约 103 KB | Art（美术）段字段 |
| `TypeAlt2Global.csv` | 约 84 KB | 全局段字段 |
| `TypeAlt2Weapon.csv` | 约 60 KB | 武器段字段 |
| `TypeAlt9Links.csv`、`TypeAltVector.csv`、`TypeAlt8Compound.csv`、`TypeAltSpecial.csv`、`TypeAltWIC.csv` | 数百字节到数十 KB | 链接、向量、复合、特殊与 WIC 相关类型 |

文件名里的 `Alt2` / `Alt3` / `Alt8` / `Alt9` 应该是同一套编号规则下的不同字段类别，`Ares` / `PhobosB48` / `Kratos` / `WIC` 指明字段来源的引擎扩展。**"PhobosB48" 把引擎版本号写进了文件名**，这是后面要提的失效风险的根源。

**② 编辑器运行时需要的元数据**

`RegisterTypes.json`（约 30 KB）、`RegisterTypesInputType.json`、`RegisterTypesSpecial.json`、`RegisterTypesStringList.json` 看起来是类型注册表；`判类规则.csv`、`名称词典.ini`（约 16 KB）、`选项.csv`、`输出格式.csv`、`序列动作表.csv` 则更像编辑器的判定与输出配置。

`输入器.csv`（约 94 KB）与 `输入器导出格式.csv`、`导出器.csv` 对应"输入器/导出器"这套概念，而根目录那个 `！！！如何新建一个复杂输入器！！！.txt`（686 字节）说明**作者知道这块需要解释**，只是把说明写成了单个提示文件。

**③ 模块与模板**

- `Modules/`：按功能切分的 **11 个模块目录**加一个示例文件——`0.语句包`、`1.Rules杂项和注册表`、`2.单位Techno`、`3.武器Weapon`、`4.Art模块`、`5.超武SW`、`6.KratosAE`、`7.KratosAE不可叠加状态`、`8.Phobos`、`9.aimd`、`凛冬将至WIC`，以及 `剪贴板样本.ini`。**这个编号顺序本身就是一份"上手路径"**：从语句包和注册表开始，再进入单位、武器、美术、超武，最后是各引擎扩展。
- `ImageModules/`：导入素材时按格式启用，含 `DefaultSHPVehicle.ini`、`DefaultSHPInfantry.ini`、`DefaultSHPBuilding.ini`、`DefaultVXL.ini`、`DefaultPCX.ini`、`DefaultEVA.ini`、`DefaultVOX.ini`、`DefaultSound.ini`、`DefaultIproj.ini` 等，并配一个"这里储存着导入素材时启用的模块.txt"。
- `SpecialModules/系统模块`、`默认ini/`：后者提供 `rulesmd.ini`、`artmd.ini`、`aimd.ini`、`uiext.ini`、`uimd.ini`、`soundmd.ini`、`extenginerules.ini`、`extengineart.ini` 八份空白/模板 INI——正好覆盖原版与 Ares/Phobos 系扩展的规则文件命名。

## 它是给谁用的（判断）

**判断：本仓库是 ra2diy 出品的 INI 节点化编辑器"INI 织网者"的模块数据包，而非独立工具。** 依据有三点，但都不是仓库自身的说明：

1. 同组织的 [INIWeaver-1.0](https://github.com/ra2diy/INIWeaver-1.0) 描述为"一个 INI 节点化编辑器"，28 Star，LGPL-2.1，最近推送 2026-09-15；
2. 同组织的 `LibIProj` 自述为"**INI 织网者**的 `.iproj` 文件处理库"——"INI 织网者"就是该工具的中文名；
3. 组织内还有一个 2025-06-16 的最后推送的旧 `module-library`，**本仓库（2026-10-01 创建）看起来是它的替代位置**，"INI-Weaver-" 前缀也直接对应工具名。

需要说明的是：我把 `INIWeaver-1.0` 的根 README 也取下来看过，**其内容竟是 Dear ImGui 的文档说明**（"See BACKENDS and EXAMPLES files..."），与该仓库的中文描述完全不匹配。这说明该工具的仓库结构或 README 配置有问题，**也提醒我们不能靠"同名组织 + 描述"就确认工具与数据包之间的绑定关系**——这条推断请当作待验证。

## 使用前需要注意

- **没有 README，没有许可证**：仓库根目录不存在 LICENSE，**默认按"保留所有权利"处理**。想在自己的工具里复用这些映射表，需要先联系作者确认授权；也不要因为它是公开仓库就默认可商用。
- **它是生成物，不是源码**：所有提交都是"同步数据包内容"，Git 历史里看不到设计过程，**改动的可审计性很差**——你看不出某个字段为什么被增删。
- **字段映射会随引擎版本过期**：`TypeAlt2PhobosB48.csv` 把 Phobos 的 **B48 版本**写死在文件名里。Phobos 继续迭代后，**这份映射表不会自动跟着更新**，用旧映射表去编辑新版本 MOD 的 INI，**可能出现字段名对不上却被当成合法字段**的情况。引用前必须核对所用引擎（Ares / Phobos / Kratos / WIC）的具体版本。
- **同名/相近仓库造成混淆**：组织内既有旧的 `module-library`，又有本仓库；工具仓库 `INIWeaver-1.0` 的 README 还是错的。**定位资料时容易进错仓库**，务必用创建时间与提交信息交叉判断。
- **`data` / `app` / `FullVersion` 三个 Git 标签没有版本语义**，不要当成版本号引用。
- **15 Star、2 Fork、1 个开放 Issue**：受众极小，遇到问题基本只能自己解决。**我只读了文件列表与提交记录，没有下载文件、也没有配合工具运行**，上述"用途推测"全部基于文件名与目录结构。

## 什么时候会再看

- 需要**查某个 INI 字段属于哪个段、在哪个引擎扩展里生效**时：这些映射表比翻各引擎的开发文档快。
- 想**写一个 RA2 INI 编辑/校验/转换工具**时：类型注册表、判类规则、名称词典的结构，是现成的设计参考。
- 想了解**红警 2 的 INI 生态有多复杂**（原版 + Ares + Phobos + Kratos + WIC 各有自己的字段体系）时：看这张映射表清单就够了。
- 需要**能让编辑器导入素材的默认配置模板**时：`ImageModules/` 与 `默认ini/` 是现成起点。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 同为红警 2 题材但方向相反：[ra2](ra2-浏览器运行红色警戒2.md)——那是"把原版游戏跑起来"（v86 模拟），本篇是"改游戏规则"的数据基础；一个改不了规则，一个跑不起来游戏，正好互为对照。
- 另一个 RA2/YR 引擎方向：[ConcordRTS](ConcordRTS-用C++20重写红警2引擎.md)——从零重写引擎，需要按原版规则与地图格式解析 INI/规则数据，与本仓库服务的是同一套规则体系，但走的是 C++ 引擎路线而非编辑器路线。

## 参考来源

- 项目仓库与文件列表：https://github.com/ra2diy/INI-Weaver-Module-Library
- 同组织的工具仓库：https://github.com/ra2diy/INIWeaver-1.0 、https://github.com/ra2diy/LibIProj
- 元数据（Star / Fork / 时间 / 标签）：GitHub REST API 与 org repos API，2026-10-30

以上信息核验于 2026-10-30。未下载任何数据文件、未配合工具运行，仓库用途的判断基于同组织其它仓库的信息，属于推断而非事实。
