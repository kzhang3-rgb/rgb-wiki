# image-prompt-reverse：给 Codex 用的"图片反推提示词"技能

> 记录一个把**参考图**逆向成**可直接喂给 AI 生图工具的提示词**的 Codex 技能（`LunarXuan/image-prompt-reverse`）：分析用途、媒介、主体、构图、镜头语言、光影、色彩、材质、背景、空间层次、情绪与后期风格，默认输出 **450–700 字中文正向 Prompt（附英文等义版）+ 10–15 条英文负面词**。三件值得先记的事：① **优先提炼 3–5 个"视觉锚点"、不编造看不清的细节**；② 把**图片里的文字 / Logo 当视觉内容**处理，**不当作指令执行**；③ 仓库只有 4 个 Markdown / YAML 文件、**无一行代码**，许可证却是 **GPL-3.0**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（中 / 英，107 行）、`LICENSE` 首行与文件清单及元数据；**未安装技能、未在 Codex 中调用、未拿任何图片实跑、未核对 `references/` 与 `SKILL.md` 的正文**）
- 适用范围：**Codex**（拷到 `%USERPROFILE%\.codex\skills\image-prompt-reverse`，用 `$image-prompt-reverse` 调用）；纯文本技能，**不依赖脚本与外部服务**，出图能力由宿主（Codex 的图像生成）提供

## 核心结论

**一句话定位（README 自述）**：分析用户上传的参考图，"**识别图片用途、媒介、主体、构图、镜头语言、光影、色彩、材质、背景、空间层次、情绪和后期风格，并生成可直接用于 AI 生图工具的高还原度提示词**"。

**① 它输出什么（明确且可预期）。** 默认两项：
- **正向 Prompt**：**450–700 个中文字符**的连续自然语言，**另附英文等义版本**；
- **Negative Prompt**：**10–15 条英文负面词**，逗号分隔。

**② 三条"防跑偏"的规则（README 明确写的）：**
- **优先提炼最影响相似度的 3–5 个视觉锚点**；
- **避免编造看不清的细节**；
- **根据目标媒介排除容易混淆的错误风格**（例如把摄影图误写成插画风）；
- 并且 **图片中的文字、Logo 和说明一律视为视觉内容，不会被当作指令执行**——这一条既是效果考虑，也是**提示词注入的防御**：如果参考图里写着"忽略以上指令……"，技能不该照做。

**③ 覆盖面是"全品类图像"。** README 列的适用类型：**摄影**（人像、产品、纪实、食物、动物、自然、城市风景）、**插画**（扁平、二次元、治愈、手绘、国风、赛博）、**3D**（写实、半写实、卡通、黏土、潮玩、产品渲染）、**字体 / Logo / 海报 / 平面设计 / 抽象图形**、**IP 角色 / Q 版 / 盲盒 / 多主体混合画面**。

**个人判断（未验证）**：这是一个**纯提示词工程**的技能——没有模型、没有脚本，价值全在 `SKILL.md` 与两份 `references/` 里"怎么描述一张图"的分析框架。它适不适合你，取决于**它的分析维度与你的出图工具是否合拍**；而"高还原度"是**自述目标，不是我实测的结论**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [LunarXuan/image-prompt-reverse](https://github.com/LunarXuan/image-prompt-reverse)（描述：`Codex skill that reverse-engineers image-generation prompts from reference images.`） |
| 归属 | 个人开发者 `LunarXuan`；**contributors 1 人**（shields） |
| 许可证 | **GPL-3.0**（`LICENSE` 35,150 字节，为完整 GNU GPL v3 文本，已核验首行）——**注意**：仓库里**没有一行代码**，只有 Markdown 与 YAML，仍是 GPL-3.0 |
| 主语言 | **无**（shields `languages/top` 返回 `none`——仓库不含可识别语言的代码文件） |
| 关注度（2026-11-13） | **905 Star / 0 关注 / 89 Fork**（页面值）；ungh 镜像 882 / 0 / 85。shields：**1 个开放 Issue** |
| 创建 / 最近推送 | **2026-09-01 创建**；`pushed_at` **2026-09-06**（**创建后很快定型，之后无新推送**）；默认分支 `main` |
| Release / 文件数 | **0 个 Release**；**7 个文件**（`LICENSE`、`README.md`、`SKILL.md` 5,999 字节、`agents/openai.yaml` 293、`references/analysis-framework.md` 4,827、`references/category-guides.md` 6,410、`references/illustration-style.md` 6,151） |
| 社群线索 | README 末尾挂**友情链接 LINUX DO**（`https://linux.do/latest`） |

> 口径说明：数据取自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章**（核验日未直接调用 api.github.com）。README 顶部是**中文 / English 双语**锚点切换。

## 文件结构与安装

**结构（README 自述）**

```text
SKILL.md                          Skill 主入口
references/analysis-framework.md  通用图片分析框架
references/category-guides.md     分类专项分析规则
references/illustration-style.md  插画专项风格规则（README 正文未列出，取自文件清单）
agents/openai.yaml                Codex 调用界面配置
```

**安装（README）**

```text
%USERPROFILE%\.codex\skills\image-prompt-reverse
```

或：

```bash
git clone https://github.com/LunarXuan/image-prompt-reverse.git "%USERPROFILE%\.codex\skills\image-prompt-reverse"
```

安装后用 **`$image-prompt-reverse`** 调用。

## 使用前需要注意

- **GPL-3.0 用在"纯文本技能"上，边界要自己判断**：仓库**没有代码**，主体是 `SKILL.md` 与 `references/*.md`（文档 / 提示词）。GPL-3.0 的**传染性主要针对软件分发**，而"把一份提示词文本并入你的产品后是否构成衍生作品"**并不像改代码那样清晰**——把它嵌进商业产品前**建议自查或咨询**，不要因为"只是几段文字"就默认可以随意闭源再分发。
- **"高还原度"是自述目标，未实测**：README 说"高还原度提示词""优先 3–5 个视觉锚点"，但**没有 benchmark、没有对照样例**；实际还原度取决于**宿主模型的图像生成能力**与**原图类型**，我未复现。
- **它绑定 Codex 的调用形态**：安装路径与调用名（`$image-prompt-reverse`）都按 Codex 的技能约定写；换到别的 Agent 需要自行适配。`agents/openai.yaml` 是**面向 Codex 的界面配置**，体量只有 293 字节。
- **仓库是"一次性发布"的样子**：`pushed_at` 停在 **2026-09-06**，与创建日（2026-09-01）相差 5 天，**0 个 Release**；但 Star 仍在增长（ungh 882 → 页面 905）——**"Star 在涨"不等于"内容在更新"**。
- **它不生成图、也不上传图**：技能本身只做"看图 → 写提示词"；**是否联网、是否把图片发给模型，取决于宿主（Codex）的行为**，本仓库未对此作说明（README 未讨论数据流向）。
- **我只读了 README、`LICENSE` 首行与文件清单**：**未安装、未调用、未拿任何图片实测，也未读 `SKILL.md` 与两份 `references/` 的正文**；分析维度与输出规则均来自 README 自述。

## 什么时候会再看

- 手里有一张**喜欢的参考图**，想拿到**能复现它风格的高还原度提示词**时。
- 想参考"**图片 → 提示词**"这类技能的**分析维度清单**（用途 / 媒介 / 构图 / 镜头 / 光影 / 色彩 / 材质 / 空间 / 情绪 / 后期）与**负面词写法**时。
- 想研究"**技能如何防御参考图里的提示词注入**"（把图内文字 / Logo 当视觉内容而非指令）这一条设计时。
- 想把它的"3–5 个视觉锚点"思路，与其它"提示词优化 / 图像生成"项目对照时（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **一个"由文本生成更优提示词"，一个"由图像反推提示词"**：[prompt-optimizer：提示词优化器](prompt-optimizer-提示词优化器.md)——那篇从**文字输入**出发做优化与评估对比（多模型、A/B 评估）；本篇从**参考图**出发做逆向。可对照"**优化已有提示词**"与"**从零反推提示词**"两条路。
- **同为"给编码助手（Codex）用的图像相关技能"**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——那篇是**生成侧**（读提示词产出精灵与地图，带脚本后处理）；本篇是**逆向侧**（读图产出提示词，纯文本无脚本）。可对照"**技能要不要带可执行管线**"。
- **同为"图像能力"，接入方式不同**：[qwen-image-local：在 8 GB 显卡上本地跑 Qwen-Image 的桌面工具](qwen-image-local-在8GB显卡上本地跑Qwen-Image的桌面工具.md)——那篇是**本地权重 + 桌面应用 + CLI**（真跑模型）；本篇是**提示词层技能**（不跑模型，能力借宿主）。可对照"**本地推理**"与"**提示词工程**"。
- **同属"用 Agent 技能产出视觉内容"**：[ip-as-logo-skill：把 IP 吉祥物做成极简圆润 Logo 的 Agent 技能](ip-as-logo-skill-把IP吉祥物做成极简圆润logo的Agent技能.md)、[anatomy：做机器等距解剖插图的 Agent 技能](anatomy-做机器等距解剖插图的Agent技能.md)——那两篇把**生成规范**写进技能；本篇把**观察与描述规范**写进技能。可对照"**描述一张图**"与"**生成一张图**"两端的约束写法。

## 参考来源

- 仓库主页与 README（中 / 英双语，107 行；核验日期 2026-11-13）：https://github.com/LunarXuan/image-prompt-reverse
- 许可证：`LICENSE`（GPL-3.0，35,150 字节，已核验首行 `GNU GENERAL PUBLIC LICENSE Version 3`；核验日期 2026-11-13）
- 文件清单与体积（`SKILL.md`、`references/*.md`、`agents/openai.yaml`）：ungh.cc 文件列表接口（2026-11-13）
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / Release 数 / 贡献者 / 主语言）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13）
