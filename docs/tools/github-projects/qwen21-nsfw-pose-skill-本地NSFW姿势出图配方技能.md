# qwen21-nsfw-pose-skill：Qwen-Image 2.1 的本地姿势出图配方技能

> **本笔记只记录该项目的元数据与技术结构，不收录、不复述其任何示例图像或露骨内容。** 记录对象是一个面向 ComfyUI 的 **Qwen-Image 2.1 姿势控制出图配方技能**（`Arekejoker/qwen21-nsfw-pose-skill`）：给出可跟跑的作者案例、底模 + LoRA 配方、**136 个 3D 姿势库**与 VNCCS 人偶控制。记它的理由是**技术组织方式**（四条路线 + 分段难度的"大胆度阶梯" + 自检脚本），不是它的内容。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（中 / 英）、`LICENSE` 与文件清单及元数据；**未下载模型 / LoRA、未安装 ComfyUI、未跑 `doctor.py`、未执行任何出图脚本、未查看任何示例图**）
- 适用范围：**ComfyUI**（需较新版本与 VNCCS 相关节点）+ **NVIDIA 显卡**（详见"硬件要求"）；**该仓库内容含 NSFW（成人向）素材，工作环境不适宜者勿打开仓库页**

## 核心结论

**这是"把某类题材的出图流程固化成 agent 技能 + 脚本 + 工作流"的一个样本。** README 自述其定位是"**Qwen-Image 2.1 本地 NSFW / 姿势出图完整配方**"，由三部分拼成：**作者级提示词结构 + NSFW LoRA + 姿势控制（136 个 3D 姿势库 + VNCCS 人偶）**。复刻目标为 **Civitai 作者的样图**（README 称 2026-10-12 验证过）。

**① 它的技术看点：**把"某一类图像的稳定出图"拆成**可复用的分层配方**——**底座**是 ComfyUI + 指定底模（`qwen_image_2.1_int8_convrot`），**质感**交给 LoRA，**姿势**交给 3D 人偶 + 姿势库，**信息密度**交给提示词结构。README 自己把这个分工总结成一句：**"LoRA 负责质感，提示词负责信息密度，机位负责大胆度"**。

**② 四条路线（把"改图需求"分门别类）：**

| 需求 | 路线 | 命令（README） |
| --- | --- | --- |
| 文字出图 | ① T2I + NSFW LoRA（要求必开） | `scripts/qwen_pose_test.py --mode t2i` |
| 已有角色图换姿势（保脸 / 保服装） | ② Edit | `scripts/qwen_pose_test.py --mode edit` |
| 已有 3D 人偶渲染图，脚本换姿势（不开浏览器） | ③ 脚本姿势迁移 | `scripts/run_pose_transfer.py --char <角色图> --pose-ref <人偶渲染图> --out <出图.png>` |
| 指定姿势库里的精确姿势 | ④ VNCCS PoseStudio | `scripts/run_vnccs_ui.py --pose <name> [--prompt "<场景>"]` |

README 明确一条操作约束：**姿势参考不要用照片 / 插画**（模型会把参考人物拼进画面或直接重画），**要用无身份特征的 3D 人偶渲染图**（路线 ③ / ④ 产出，输出目录里的 `doll_*.png`）。

**③ 姿势库来源是公开数据集拼的：**136 个姿势 = Hugging Face 四个公开仓库（`Totemistyk/General_Poses`、`Totemistyk/Ballet_PoseStudio`、`MIUProject/VNCCS_PoseLibrary_Main`、`MIUProject/MySuperPoseRepo`）**+ 55 个手工精选**；姿势表示为**骨骼旋转 JSON**（MakeHuman 网格 + three.js 渲染），宣称"换姿势且保身份"。姿势控制能力基于第三方项目 **AHEKOT 的 `ComfyUI_VNCCS_Utils`**（README 致谢部分点名）。

**个人判断（未验证）**：抛开题材，它值得记的是**这种"配方化 + 分路线 + 带自检与验证流水线"的工程写法**——`doctor.py` 先只读自检环境，`validate_package.py` 做包校验，仓库还配了 `.github/workflows/validate.yml`。**这些机制是否如其所述有效，我未复现。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [Arekejoker/qwen21-nsfw-pose-skill](https://github.com/Arekejoker/qwen21-nsfw-pose-skill) |
| 归属 | 个人开发者 `Arekejoker`；**contributors 3 人**（shields） |
| 许可证 | **Apache-2.0**（`LICENSE` 11,341 字节）——但 README **明确声明第三方素材不在覆盖范围内**（见下"许可边界"） |
| 主语言 | Python 100%（脚本 + 工作流 JSON + 提示词文本 + 素材图） |
| 关注度（2026-11-13） | **4,370 Star / 8 关注 / 750 Fork**（页面值）；ungh 镜像 4,324 / 7 / 738。shields：**2 个开放 Issue** |
| 创建 / 最近推送 | **2026-10-13 创建**；`pushed_at` **2026-10-26**；默认分支 `main` |
| Release / 文件数 | **2 个 Release**（`v1.1.0`，2026-10-20：Route ③、`doctor.py`、macOS / Windows 支持、英文 README；`v1.0.0`，2026-10-13）；**150 个文件** |
| 关键文件 | `SKILL.md`（24,502 字节，agent 技能完整规则）、`CHANGELOG.md`、`install/setup.md`（9,899）、`scripts/`（`doctor.py` 10,567、`qwen_pose_test.py` 11,964、`run_vnccs_ui.py` 14,598、`run_pose_transfer.py`、`media_lock.py`、`browser_fallback.py`、`make_custom_pose.py`、`validate_package.py`）、`workflows/`（`qwen21_t2i.json`、`qwen21_edit.json`、`VNCCS_PoseStudio_QI2.1.json` 42,964）、`prompts/`（3 篇作者原提示词 + 参数）、`pose_library/`（55 个手工精选姿势，`.json` + `.webp` 成对）、`.github/workflows/validate.yml` |

> 口径说明：数据取自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章**（核验日未直接调用 api.github.com）。README 顶部为**中文 / English 双语**。

## 配方与运行方式（仅记结构）

- **配方要点（README 摘要）**：采样器 `er_sde` + 调度 `beta`、CFG 2.5、50 步、**1152×1728 竖版**；**NSFW LoRA 永远开**（默认 `femaledVaginus v1.3b` @ 1.0）；**提示词必须按作者结构写满 7 要素、300–1500 字符、含 LoRA 触发词**；README 特别声明——**若要"弱化配方"（去 LoRA / 短提示词 / 减步数），必须先告知用户代价，不许默默减弱**。
- **自检**：先跑 `python3 scripts/doctor.py`（README 称**只读、不改动任何东西**），逐项检查 ComfyUI 连通性、底模 / LoRA、VNCCS 节点、姿势库、浏览器后端，并给缺失项修复指引。
- **难度分层**：README 用**"大胆度阶梯 L0–L3"**把"用户要求 → 姿势族"做了映射表（见 `SKILL.md` §4）；`scripts/run_vnccs_ui.py --list-poses` 可列出全部 136 个姿势。
- **Apple Silicon 说明**：README 单开一节讲 Mac——按**统一内存**选 int8 原版或 GGUF 量化；给出社区报告的**分钟级 / 张**速度预期（并注明**维护者无 Mac 硬件、数据来自社区、未经实测**），已知坑是 MPS 上编辑模式偶发 VAE 编码异常（可 `--cpu-vae` 绕过）。**这部分我未实测，且 README 已自标未验证。**

## 硬件要求（README 参考值）

- 底模为 Qwen-Image 2.1 int8 套件，模型文件合计**约 16 GB**（diffusion 6.8 GB / text encoder 8.7 GB / VAE 0.6 GB），LoRA 各自 ≤ 608 MB。
- **最低**：NVIDIA **12 GB** 显存 + 32 GB 内存（ComfyUI 自动把部分模型卸载到内存，出图更慢）。
- **推荐**：**24 GB** 显存（3090 / 4090 级）+ 32–64 GB 内存。
- **8 GB 显存**：建议改用 GGUF 量化版（社区实测 Q4 需 12–16 GB；8 GB 靠 offload 明显更慢）。
- **纯 CPU 不推荐**；**硬盘预留约 40 GB**；路线 ④ 另需一个可连接的桌面浏览器（Playwright / CDP）。

## 许可边界（README 明确划分）

- **原创代码与文档**（`scripts/`、`workflows/`、`prompts/`、`SKILL.md`、README 等）采用 **Apache License 2.0**。
- **第三方素材不在 Apache-2.0 覆盖范围内，权利归各自原作者**，README 点名：`downloads/samples/` 的**作者原图（Civitai 作者）**、`pose_library/` 的**社区姿势与预览（MIUProject / Totemistyk，其中 Ballet 仓库为 MIT）**、以及**模型与 LoRA**（HF `Comfy-Org/Qwen-Image-2.1`、Civitai 若干 ID、RunningHubAI、MIUProject 等）。
- 也就是说：**"仓库是 Apache-2.0"并不等于"仓库里的一切都能按 Apache-2.0 使用"**——第三方素材要**各自回原授权处核对**。

## 使用前需要注意

- **内容属性**：仓库公开包含 **NSFW（成人向）示例素材**，README 自述"介意图片的先收藏后阅读"。本笔记**只记元数据与结构，不复述、不链接任何露骨内容**。
- **许可不等于全部可商用**：如上一节，**第三方素材（图 / 姿势 / 模型 / LoRA）需各自核权**，不要只看到 Apache-2.0 就整体当可商用。
- **强依赖外部环境，且要"跟跑"**：需自备 ComfyUI（较新版）、底模与 LoRA、VNCCS 相关节点、浏览器后端；README 提供"复刻作者样图"的命令与元数据（`downloads/samples/phase5_meta.json`），**但我未跑通、未复现**。
- **"复刻成功"是自述**：README 称输出"与作者原图同场景同构图"（称有元数据可对）；**这是项目自述，未经我或第三方核验**。
- **社区数据自标未验证**：Mac 一节的速度与兼容数据**来自社区报告**，README 自己写明**维护者无对应硬件、未实测**。
- **我只读了 README、`LICENSE` 与文件清单及元数据**：**未下载模型 / LoRA、未安装 ComfyUI、未跑自检与任何脚本、未生成任何图像、未查看示例图**。

## 什么时候会再看

- 想参考"**把一套出图流程固化为 agent 技能 + 脚本 + ComfyUI 工作流**"的组织方式（含 `doctor.py` 只读自检、`validate_package.py` 包校验、CI 校验流水线）时。
- 想研究"**用 3D 人偶骨骼 JSON 做姿势控制、换姿势同时保身份**"这条技术路线时。
- 想研究"**底模 / LoRA / 姿势 / 提示词分层分工**"以及"**把难度做成阶梯（L0–L3）**"的产品化写法时。
- 想对照"**开源仓库里第三方素材的许可边界该怎么划**"时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"本地跑开源图像模型"的 ComfyUI 路线**：[qwen-image-local：在 8 GB 显卡上本地跑 Qwen-Image 的桌面工具](qwen-image-local-在8GB显卡上本地跑Qwen-Image的桌面工具.md)——那篇是**把小显存放进桌面应用 + CLI**（锁定版 ComfyUI 当引擎）；本篇是**挂在 ComfyUI 上的技能 + 工作流 + 脚本**。可对照"**开箱即用的单模型应用**"与"**手工搭节点图 + 技能**"两种形态。
- **同用 Qwen-Image 系列，产物不同**：[nvzhuang：拍片哥女装 AI 服装参考图集](nvzhuang-拍片哥女装AI服装参考图集.md)——那篇是**已产出的图像图集**（本地换装后端内置 Qwen-Image-Edit-2511 与 MiniMax H3）；本篇是**生产这类图像的一套配方与技能**。可对照"**产物**"与"**产线**"。
- **同属"ComfyUI 节点 / 工作流"的工程化**：[ComfyUI-MiniMaxH3：Easy-MiniMaxH3 视频节点套件](ComfyUI-MiniMaxH3-Easy-MiniMaxH3视频节点套件.md)——那篇把视频模型做成**节点套件**；本篇把图像出图做成**技能 + 工作流 + 脚本**。可对照"**节点套件**"与"**技能封装**"两种接入方式。

## 参考来源

- 仓库主页与 README（中 / 英双语；核验日期 2026-11-13）：https://github.com/Arekejoker/qwen21-nsfw-pose-skill
- 许可证：`LICENSE`（Apache-2.0，11,341 字节；README 声明第三方素材不在覆盖范围内；核验日期 2026-11-13）
- Release 列表（`v1.1.0` 2026-10-20、`v1.0.0` 2026-10-13）：https://github.com/Arekejoker/qwen21-nsfw-pose-skill/releases
- 文件清单与体积（`SKILL.md`、`scripts/`、`workflows/`、`prompts/`、`pose_library/`）：ungh.cc 文件列表接口（2026-11-13）
- 元数据（Star / Fork / 关注 / 创建与推送时间 / 默认分支 / Release 数 / 文件数 / 贡献者）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13）
