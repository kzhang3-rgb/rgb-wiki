# agentic-rl-lab：用 PyTRIO 复现 LLM / Agent 强化学习算法的中文实验手册

> Datawhale 的 Agentic RL 实验仓库：把 GRPO、GSPO、DAPO、OPD、OPSD、Search-R1、ReTool、ALFWorld、TEMPO 等一批论文算法，用远程 Train API（PyTRIO）压到"不用 4 卡 8 卡也能跑"的入门成本，配可运行代码与中文实验手册。想系统学 Agentic RL、或想看清"同一批 RL 算法在 loss 层到底差在哪"时值得再看。

- 状态：草稿
- 最近核验：2026-11-05
- 适用范围：只想读原理与实验结论、尚未动手复现的评估阶段；本笔记未 clone、未运行任何实验。

## 核心结论

- 这是一个**偏实验记录与教程**的仓库，不是训练框架。作者自述做三件事：讲清算法来自哪篇论文、解决什么问题；用可运行代码复现（数据 / reward / loss / 训练循环 / SwanLab 记录都在仓库里）；未来可能做一个更轻量的 Agent RL / Harness RL 训练框架。
- **核心卖点是算力门槛**：作者原话是曾被"4 卡、8 卡、训推一体复杂度、verl / Slime / Areal 高耦合工程代码"劝退，接触 Tinker 与 PyTRIO 后认为 **Train API（远程训练接口）** 能把"论文里最核心的几行公式"从工程噪声里剥出来——自述"复现这 14 篇 Agentic RL 论文中的算法只用了不到两个月"。
- **依赖两个第三方服务**：训练走 [PyTRIO](https://pytrio.com)（远程训练），实验记录走 [SwanLab](https://swanlab.cn)。也就是说，**这不是一个纯本地可跑通的教学仓库**——按 README 走，你需要 PyTRIO 侧的能力（代金券需邮件申请）。
- 组织方是 **Datawhale**（`datawhalechina` 组织），项目负责人宋志学（`KMnO4-zx`）；许可为 **Apache-2.0**，与大多数商业友好型工具一致。
- Star 数存在**镜像站与页面不一致**（见下），以页面值为准并注明来源。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | https://github.com/datawhalechina/agentic-rl-lab |
| 组织 | datawhalechina（Datawhale） |
| 一句话用途 | 复现和拆解前沿 LLM / Agent 强化学习算法的中文实验手册 |
| 许可 | **Apache-2.0**（仓库内 `LICENSE`，约 11.4 KB） |
| 主语言 | **Python 99.6%** |
| 关注度（2026-11-05） | **1,178 Star / 74 Fork / 2 关注 / 1 开放 Issue**（页面值） |
| 创建 / 最近推送 | **2026-07-06 创建，pushed 2026-11-02**（仍在活跃维护） |
| 默认分支 | `main` |
| Release / Tag | **无任何 Release、无 tag** |
| 贡献者 | **6 位**：`KMnO4-zx`（宋志学，项目负责人，Datawhale 成员）、`mrle0429`（北京交通大学，Tournament-GRPO 作者）、`Zeyi-Lin`、`NGDBZ`、`KiritoHugh`、`kites262` |
| topics | `agent-rl` / `alfworld` / `dapo` / `gspo` / `harness-rl` / `opd` / `opsd` / `pytrio` / `retool` / `search-r1` |
| 在线阅读 | 中文实验手册 <https://datawhalechina.github.io/agentic-rl-lab/>；英文 <https://datawhalechina.github.io/agentic-rl-lab/en/> |
| 环境要求 | **Python 3.13+**；`uv sync` 起步（ALFWorld / TEMPO / AgentOPSD 章节需 `uv sync --extra alfworld`） |

> 口径说明：本行数据来自 **ungh.cc 镜像 + GitHub 页面内嵌 JSON + shields.io 徽章**，核验日 GitHub API 匿名配额已用尽，未取到 `size`。**ungh.cc 镜像报 749 Star / 1 关注 / 49 Fork，与页面值的 1,178 / 2 / 74 不一致**——按本仓库收录标准以页面值记录并注明来源，两者不要当成同一份数据混用。仓库体积未取得，不臆测。

## 内容结构

仓库主体是 `experiments/` 下的 **16 个实验章节**（README 表格逐条给出"主题 / 内容 / 在线版"）：

| 主题 | 内容 |
| --- | --- |
| Loss Function | 用直觉解释 `importance_sampling`、`ppo`、`cispo` 分别在优化什么 |
| GRPO | 复现 GSM8K 上的 GRPO，并比较上述三个 loss |
| Vision GRPO | 在 GeoQA 上接入图片输入，解决几何数学题 |
| PyTrio-Jev | "一顿麦当劳早餐，人人皆可训练自己的 Jev 决策模型" |
| General OPD | 用 DeepMath-103K 跑通 Student 采样、Teacher 打分与 reverse KL 的最小闭环 |
| Medical OPD | 从 Medical SFT 出发，用 SAR-OPD 与 IDT-OPD 增强医疗能力，同时保持通用能力 |
| DAPO | 拆解四项核心改进，并记录 Dynamic Sampling 在真实训练中的时间成本 |
| GSPO | 将重要性比率与裁剪从 token 级提升到 sequence 级 |
| OPSD | 用固定的 step-0 Teacher 蒸馏 Student 自采样轨迹 |
| AgentOPSD | 用带 Skill 的 Self-Teacher 分配 turn-level credit |
| Search-R1 | 用 Qwen3.5-4B、PyTRIO 与可切换的在线搜索后端复现多轮搜索 RL |
| ReTool | 用 Qwen3.5-4B、PyTRIO 与本地代码沙箱复现代码交织的 Agentic RL |
| ALFWorld | 用 12K 长轨迹、真实 TextWorld 环境与 group-relative advantage 训练家务 Agent |
| TEMPO | 算法级复现 macro-step 优化与生成式 critic |
| Spec-o3 | 用强化学习找星星：SFT + GRPO，让模型边看光谱、边思考、边调用工具 |
| Tournament-GRPO | 让同题研究报告两两比赛，用淘汰赛胜场构造 GRPO 奖励 |

## 用法要点

- **起步只需两行**：`git clone` 后 `cd agentic-rl-lab && uv sync`；想单独把某个 demo 拎进自己项目时，README 给出基础依赖清单，关键项为 `pytrio==0.2.9`、`swanlab==0.9.2`、`torch>=2.9.1`、`datasets>=5.0.0`、`openai>=2.44.0`、`math-verify`、`modelscope`、`matplotlib` 等。
- **ALFWorld / TEMPO / AgentOPSD 需额外装环境**：`uv sync --extra alfworld`（对应 `alfworld==0.4.2`、`spacy==3.8.13`）。这三个章节涉及 TextWorld 真实环境，是唯一需要额外依赖的一组。
- **Search-R1 直接调 DeepSeek 官方 Search API**（用 `httpx`，无需额外搜索封装），详见该章节的 `start.md`。
- **Spec-o3 有独立快速开始文档**（数据下载 → SFT → GRPO → 评测 → 绘图），并给了 RL epoch 3 开发集结果：同一批 256 题，**Qwen3.5-4B 答对 175 题（68.36%）、Qwen3.5-9B 答对 191 题（74.61%），Macro F1 分别为 70.93% / 75.94%**。
- **想自己提出 Agentic-RL idea**：可邮件申请 PyTRIO 代金券（`violin@pytrio.com`），作者注明"审核会比较严格"。

## 使用前需要注意

- **不是纯本地仓库**：训练依赖 PyTRIO 远程 Train API，实验记录依赖 SwanLab。若只想要"完全离线、自己攒卡跑"的方案，这仓库的路线未必合适——它的价值主张恰恰是**避开自建多卡环境**。
- **README 内部口径小不一致**：正文自述"复现这 14 篇 Agentic RL 论文"，而文章目录实为 **16 个章节**；两者不要当成同一口径引用。
- **Star 数据双源不一致**：见上文口径说明（镜像 749 vs 页面 1,178）。
- **无 Release / 无 tag**：想固定版本只能按 commit 走；仓库 2026-07 才创建，仍在快速迭代，章节内容与结论可能随时变动。
- **Spec-o3 章节的表格链接写法与其余章节不同**（用了完整 blob URL 而非相对路径），属细节，不影响阅读。
- 本笔记**只读了 README、LICENSE 存在性与元数据**，未 clone、未安装依赖、未申请 PyTRIO、未跑任何实验；所有训练结果与"两个月复现 14 篇"均为**作者自述**，未验证。

## 什么时候会再看

- 想系统入门 Agentic RL、又不想一上来就啃 verl / Slime / Areal 这类重工程框架时。
- 想对照"同一批 RL 算法在 loss 层差在哪"（`importance_sampling` / `ppo` / `cispo`，以及 DAPO / GSPO 的裁剪粒度差异）时。
- 想把某一篇 Original 论文（Search-R1 / ReTool / ALFWorld / TEMPO / GSPO / DAPO）落到能跑的最小代码时。
- 想参考"**用远程 Train API 把多卡训练门槛降下来**"这种教学 / 复现路线的可行性时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"从零 / 低门槛 + 中文教学"的仓库，但方向互补**：[minimind：2 小时从零训练一个 64M 小 LLM 的中文项目](minimind-从零训练64M小LLM的中文项目.md)——minimind 一路走到 **Agentic RL** 章节，是从零预训练到后训练的完整链路；本篇则把 Agentic RL 这一段拆成 16 个专门实验，且用远程 Train API 回避多卡环境。想搞清"Agentic RL 在整条链路里处在哪一节、那一节的代码长什么样"，两篇对着看。
- **同为 Datawhale 系的中文可运行教程风格**：[NamiAgent：从零手写 Agent 的中文可运行教程](NamiAgent-从零手写Agent的中文可运行教程.md)——那篇是"从零手写 Agent"（循环 / 工具 / 权限 / 记忆），本篇是"给 Agent 做强化学习"；一个教"怎么驱动模型"，一个教"怎么用 RL 把模型训得更会做事"。
- **中文体系化学习资料的另一条轴**：[Foundations-of-LLMs：大模型基础教材](Foundations-of-LLMs-大模型基础教材.md)——那是按章讲原理与架构分叉的教材（PDF 分发、偏理论），本篇是能跑的实验手册（偏实现）。想补"为什么要这么设计 loss"，从教材侧入手更顺。

## 参考来源

- 项目仓库与 README：https://github.com/datawhalechina/agentic-rl-lab
- 许可证：https://github.com/datawhalechina/agentic-rl-lab/blob/main/LICENSE（Apache-2.0）
- 在线阅读（中文实验手册）：https://datawhalechina.github.io/agentic-rl-lab/
- 训练侧依赖：PyTRIO <https://pytrio.com>；实验记录：SwanLab <https://swanlab.cn>
- 元数据来源（2026-11-05）：ungh.cc 镜像、GitHub 页面内嵌 JSON、shields.io 徽章；GitHub API 匿名配额用尽，未取到仓库体积。
