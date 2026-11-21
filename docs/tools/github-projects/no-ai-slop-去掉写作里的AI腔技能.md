# no-ai-slop：去掉写作里"AI 腔"的技能

> 一句话定位（README 自述）：**"Remove 20+ patterns of AI slop from your writing without flattening your personal voice."**——一个帮你去掉"AI 味"、同时**保住你个人文风**的 Agent 技能。它最值得记的是三点：① 它把"AI 腔"**列成了 20+ 条可指认的具体句式**（二元对比、清嗓子式开头、伪洞见铺垫、冒号揭晓、戏剧化碎句、空洞拔高、含糊归因、同义替换循环、伪深刻的结尾……），而不是只给一句"写得更自然"；② 它有**三种用法**——**改稿**（去 slop 并列出改了什么）、**检测**（逐条引用命中的 pattern，**不猜这段是不是 AI 写的**）、以及**故意生成**（把最尴尬的 AI 腔写出来当讽刺）；③ 它是**技能（skill）而非应用**，安装走 `npx skills add`，同时也是 ChatGPT 里的插件。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`LICENSE`、文件树与元数据；**未安装技能、未在任何助手内运行、未跑 `eval.md` 里的检查、未审计 `SKILL.md` 全文、未使用其 ChatGPT 插件**）
- 适用范围：ChatGPT / Claude Code / Codex 等**支持 Agent Skills 的助手**；也可用 `npx skills add ... --global` 全局安装；依赖 Node（`npx`）走安装流程，技能本身是文本

## 核心结论

**① 它解决什么问题。** README 的"Problem"一节说得很直白：**AI 让"干净但千篇一律"的文字变得廉价**，而且**用 AI 改稿会把你的词汇、节奏、幽默感和那些让人认得出是你的"不完美"一起抹平**。所以它同时做两件事——**去掉 AI 的模板腔**，以及**不要改成"去人味"的标准答案**。

**② 它怎么做。** 它把"AI 腔"具体化成一张可核对的清单（README 列了 10 条示例，宣称覆盖 **20+ 种**）：

| # | 模式 | 典型句式 |
| --- | --- | --- |
| 1 | 二元对比（Binary contrasts） | "It's not X. It's Y." |
| 2 | 清嗓子式开头（Throat-clearing openers） | "Here's the thing," "Let me be clear" |
| 3 | 伪洞见铺垫（Faux-insight setups） | "What nobody tells you," "The part everyone misses" |
| 4 | 冒号揭晓（Colon reveals） | "The best part: it learns." |
| 5 | 戏剧化碎句（Dramatic fragments） | "That's it. That's the whole thing." |
| 6 | 表面分析（Superficial analysis） | "highlighting the team's commitment to innovation" |
| 7 | 重要性拔高（Importance puffery） | "marks a pivotal moment," "a testament to" |
| 8 | 含糊归因（Weasel attribution） | "experts agree," "studies show" |
| 9 | 同义替换循环（Synonym cycling） | "The agent handles your email. The assistant drafts replies." |
| 10 | 伪深刻的结尾（Fake-profound endings） | "The future isn't coming. It's already here." |

除句式外，它还检查基本盘：**该先给结论就先给结论、用主动语态、拆开难读的长句、少用抽象多给具体**。

**③ 三种使用方式（README）。**

- **改稿**：`/no-ai-slop (你的文字)`——去 slop、保留个人声音、**并列出改了什么**。
- **检测**：`/no-ai-slop is this slop? (你的文字)`——**逐条引用命中的 pattern，但不判断这段是不是 AI 写的**（README 强调 "without guessing whether AI wrote the text"）。
- **故意生成**：`Draft an AI slop post about (topic)`——**生成最尴尬的 AI 腔**当讽刺用。

**个人判断（未验证）**：这个技能最有价值的地方不是"能去掉 AI 腔"，而是**它把"什么叫 AI 腔"写成了可指认的清单**——这一点即使不装技能也能直接用（当作自己的改稿 checklist）。它明确**不承担"AI 检测"职责**，这个边界划得比很多同类工具诚实。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [petergyang/no-ai-slop](https://github.com/petergyang/no-ai-slop) |
| 描述 | "Remove 20+ patterns of AI slop from your writing without flattening your personal voice." |
| 许可证 | **MIT**（`LICENSE` 1,067 字符；shields 报 MIT） |
| 主语言 | **Python 100%**（shields.io，2026-11-13；**实为 Markdown + Python 构建脚本**） |
| 关注度 | **13,448 Star / 941 Fork / 38 Watcher**（**页面值**，2026-11-13）；开放 Issue **11**、贡献者 **2**（shields.io，同日）。**ungh 镜像 13,425 / 939 / 38** |
| 维护状态 | **2026-07-07 创建**；ungh `pushedAt` **2026-09-02**（约两个半月前）、页面 `updatedAt` 2026-11-12（多为徽章刷新）；默认分支 **`main`**；**7 条 Release**（最新 **v1.0.6**） |
| 体积 | `main` 分支 **14 个条目**：`skills/no-ai-slop/{SKILL.md, eval.md}`、`.codex-plugin/plugin.json`、`scripts/build_plugin.py`，以及 README 等；**仓库很小** |
| 安装 | `npx skills add petergyang/no-ai-slop --skill no-ai-slop --global --yes`；或向助手粘贴 "Install the /no-ai-slop skill globally from https://github.com/petergyang/no-ai-slop" |
| 作者 | Peter Yang（[Behind the Craft](https://behindthecraft.com) · YouTube · newsletter `creatoreconomy.so`） |
| 另有一份发行版 | 同名 **ChatGPT 插件**（由 `build_plugin.py` 打包 `.codex-plugin/plugin.json`） |

## 使用前需要注意

- **它不判断"这段是不是 AI 写的"**：README 在"Detect slop"一节明确写 —— 它只**引用命中的 pattern**，"**without guessing whether AI wrote the text**"。**别把它当 AI 检测器用**。
- **"去 AI 腔"与"保个人声音"是一对张力**：技能的设计目标就是**同时**做这两件事，所以它**不是"越改越标准"的工具**；如果你的需求是"改成正式的公文腔"，它未必合适。
- **它是技能文本，效果取决于宿主模型**：`SKILL.md` 是给助手读的规则，**实际改写质量由所用模型决定**；仓库提供的 `eval.md` 是"技能对自己工作的检查"，属**自检**而非外部评测。
- **活跃度已放缓**：`pushedAt` **2026-09-02**（约两个半月前），页面 `updatedAt`（2026-11-12）主要来自徽章刷新——**"`updatedAt` 新"不等于"主线在维护"**。
- **贡献者仅 2 人**（shields.io），属**个人项目**；README 带作者其它产品（Behind the Craft）与 YouTube / newsletter 的推广链接。
- **Star 与 ungh 镜像略有差异**，**引用数字时注明来源与日期**。
- **我只读了 README、`LICENSE` 与文件树**：**未安装技能、未在任何助手里运行、未跑 `eval.md`、未审计 `SKILL.md` 全文、未用其 ChatGPT 插件**；上述能力均来自 README，**未逐一验证**。

## 什么时候会再看

- 需要**快速自查一段中文或英文稿"有没有 AI 腔"**时（把那张 20+ pattern 清单当 checklist 用）。
- 让 AI 帮自己改稿，但**担心"越改越像机器写的"**时——参考它"去模板腔 + 保个人声音"的双目标设定。
- 想学"**怎么把一个模糊的编辑要求（'别写得太 AI'）拆成 20 条可指认的规则**"时。
- 想参考"**一个技能如何同时以 Agent Skills 与 ChatGPT 插件两种形态发布**"（`SKILL.md` + `.codex-plugin/plugin.json` + 构建脚本）时。
- 想对照其它"写作 / 文本加工"方向的工具时：[easy-writing](easy-writing-易创网文写作软件.md)（网文写作软件，提示词可改为本地 md）· [Supervisor-Skills](Supervisor-Skills-把博导经验炼化成AI科研副导师技能.md)（含 `paper-polish`：去 AI 腔、中文稿改写为投稿级英文）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都关心"AI 参与写作后文字变成什么样"**：[easy-writing：易创网文写作软件](easy-writing-易创网文写作软件.md)——那篇是**本地优先的写作软件**（BYOK 接模型、提示词是可编辑的本地 md），本篇是**一枚只管文风的技能**。可对照"**写作工作台**"与"**单点编辑器**"。
- **都有"去 AI 腔"这一环，但层级不同**：[Supervisor-Skills：把博导经验炼化成 AI 科研副导师技能](Supervisor-Skills-把博导经验炼化成AI科研副导师技能.md)——其中的 `paper-polish` 技能也做"去 AI 腔、措辞分寸、可能改含义处逐条标出"，但服务于**投稿级学术英文**；本篇面向**通用写作**且把 pattern 清单本身当产物。可对照"**学术语域**"与"**通用文风**"。
- **都是"约束模型输出"的两种路数**：[prompt-optimizer：提示词优化器](prompt-optimizer-提示词优化器.md)——那篇从**输入侧**帮你把提示词写得更好；本篇从**输出侧**约束文本的写法。一个管"怎么问"，一个管"怎么写"。
- **同样给写作加规则，但做成了可跑的统计脚本**：[podcast-to-article：把播客逐字稿整理成可发长文的技能](podcast-to-article-把播客逐字稿整理成可发长文的技能.md)——那篇的 `check_style.py` 用**词法统计**报"AI 痕迹命中数 + 直接引语占比 + 段首自足判断句比例"（并给出人类基准），本篇是**把 pattern 写成清单交模型判读**。一个偏"可量化自检脚本"，一个偏"规则清单 + 改稿流程"。
- **都做"去 AI 腔"，层级不同**：[chinese-novelist-skill：让 AI 从零写出一部完整中文小说的 Agent 技能](chinese-novelist-skill-从零生成10到50章中文小说的技能.md)——那篇把"润色去 AI 味"当作**逐章生成流水线里的固定一步**并配 `03-文风基准.md`（含 AI 味负面清单）；本篇是**专门做去 AI 痕迹的单点技能**。可对照"**长篇流水线里的去味环节**"与"**专项去 AI 腔**"。
- **同为"给 AI 编码助手装的单点技能"，约束侧相反**：[Ponytail：让 AI 编码像"最懒的资深开发"一样思考的技能](ponytail-让AI编码像最懒的资深开发一样思考的技能.md)——那篇从**方案侧**约束"要不要写、写多少"（代码阶梯）；本篇从**输出侧**约束"文字怎么写"（去 AI 腔）。两篇都是**一枚只管单一维度的技能**，可对照"管体量"与"管文风"。

## 参考来源

- 仓库主页与 README（核验日期 2026-11-13）：https://github.com/petergyang/no-ai-slop
- 仓库内文件：`skills/no-ai-slop/SKILL.md`（编辑规则与工作流）· `skills/no-ai-slop/eval.md`（自检项）· `.codex-plugin/plugin.json`（ChatGPT / Codex 插件元数据）· `scripts/build_plugin.py`（构建与校验插件包）
- 许可证：`LICENSE`（MIT，1,067 字符）
- Release 列表（7 条，最新 v1.0.6）：ungh.cc releases API（2026-11-13）
- 完整文件树（14 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**略有差异**）；Issue 数 / 贡献者数 / 主语言取自 shields.io（同日）

以上信息核验于 2026-11-13。**未安装技能、未在任何助手内运行、未跑 `eval.md`、未审计 `SKILL.md` 全文**；能力描述来自 README，**未逐一验证**。
