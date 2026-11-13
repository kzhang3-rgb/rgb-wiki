# universal-modder：把「改任何 PC 游戏」做成一整套 Agent 技能

> 一句话定位（README 自述）：*"Skills, tools and a shared knowledge base that let any AI coding agent mod almost any PC game you own."*——**把 AI 编码 Agent 指向你拥有的 PC 游戏**，它就能：识别引擎与路线、读真实代码、做出 Mod、用 [fal](https://fal.ai) 生成美术/3D/音效、**在运行中的游戏里测试**、剪出展示视频，并把学到的东西**写成给下一个 Agent 看的笔记**。**最值得记的三点**：① 它把"**Agent 改游戏**"这条野路子做成了带**硬安全规则**的工程包——**拒绝注入有反作弊的在线游戏、不写针对其他玩家的外挂、不绕过反作弊/DRM/所有权校验、不随包分发游戏文件或反编译代码**；② 体量惊人——**924 个文件**，其中 `knowledge/`(366) 是"由 Agent 写、给 Agent 看"的实战笔记库，`examples/`(303) 是**真做出来的 Mod 案例**；③ 它**距创建仅约 6 周**（2026-09-30 建），却已 9.6k Star / **171 位贡献者**，增长极快。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（257 行）、`LICENSE`、完整文件树与元数据；**未 clone、未安装任何 Agent 插件、未跑 `um` CLI、未配置 fal key、未做任何 Mod、未在游戏里测试**）
- 适用范围：**Claude Code · Codex · Gemini CLI · VS Code/Copilot · Cursor · OpenCode** 等主流编码 Agent（Agent Skills 格式），或直接 `git clone` 后在仓库内启动 Agent。**生成美术/3D/音频需 [fal](https://fal.ai) API key**（`um fal key set`）。仓库语言为 **Python**。

## 核心结论

**一句话定位（README 自述）**：*"Point Claude at any game."*——用 **Skills + tools + 共享知识库**，让**任何** AI 编码 Agent 改**几乎任何**你拥有的 PC 游戏。

**三个最值得记的点：**

1. **"硬安全规则"是它最该被记住的部分**（README `## Rules it follows` 原文要点）：
   - **只改你拥有的游戏**（单机、多人、你自建的服务器都行），**但拒绝注入带反作弊的在线游戏、拒绝写针对其他玩家的外挂、拒绝绕过反作弊 / DRM / 所有权校验**；
   - **从不随包分发游戏文件或反编译代码**——Mod 以**代码、你自己的素材、补丁或转换器**形式交付；
   - **动存档前先备份**，只按 PID 结束进程；
   - **动手前会先问**：驱动你的鼠标键盘、往游戏目录装 loader、以及发布（含发 PR）之前都要确认。
   - 完整推理见 `skills/mod-any-game/references/safety.md`。
2. **"知识库 + 引擎手册"是它的知识资产**：`skills/mod-any-game` 内置**引擎 playbook**，覆盖 Unity、Unreal、.NET/XNA（Terraria / Stardew / Celeste）、Godot、Source 1/2、Bethesda、Minecraft、AoE2/Genie、Bannerlord、Binding of Isaac、RE Engine / MT Framework / FromSoft / GTA / Hedgehog Engine / Black Rock / Cyberpunk / BG3 / Avalanche、原生 C++、GameMaker / RPG Maker / Ren'Py / Paradox / Doom / HTML5 / LÖVE / Java、以及**老游戏的逆向工程（decomp）**。`knowledge/INDEX.md` 是**"由 Agent 写、给 Agent 看"的战地笔记**；`um scan` 会**找 Steam/Epic/Xbox 安装、指纹识别引擎与版本、.NET 还是原生、反作弊、已装 loader、存档目录，并排出候选路线**。
3. **它是"AI 生成素材"与"改游戏"的缝合体**：美术/3D/音频走 **fal**（GPT Image 2、Nano Banana 2、FLUX、Trellis 2、ElevenLabs…），也可用 `um comfy` 接**本地 ComfyUI**；另有 **`um sprixen`（2D 精灵/动画表/贴图）** 与 **`um voidless`（2D 精灵、8 向转向、动画组）** 两个**需另购 API key 的独立付费服务**。

**个人判断（未验证）**：它把过去零散的"Agent 改游戏"零工，整理成了**带安全约束、可复用、有知识沉淀**的工程包，且给出了"**Mod 本身如何合规交付**"的答案（代码/补丁/转换器，不分发游戏资源）。**风险面同样明显**：终究是在**动商业游戏**——即便它自我设限，**反作弊误判、EULA 冲突、账号封禁、二次分发的版权问题**都由使用者承担；**README 未给出"请自行核对各游戏 EULA/当地法规"的显式提示**（我只读到它自家的安全规则）。此外**距创建仅约 6 周**，一切都在早期。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [rehan-remade/universal-modder](https://github.com/rehan-remade/universal-modder) |
| 描述 | "Point Claude at any game. Skills, tools and the fal MCP that let Claude Code mod almost any PC game you own: recon, reverse engineering, fal-generated art/3D/audio, in-game testing, showcase videos." |
| 归属 | 个人账号 `rehan-remade`（ungh `orgs` 查询非组织）；`isFork: false`、`isArchived: false` |
| 许可证 | **MIT**（`LICENSE` 1,096 字符，`Copyright (c) 2026 Rehan and universal-modder contributors`）。**品牌字体**（Geist Pixel、Sometype Mono、Instrument Sans、Space Grotesk、JetBrains Mono）**为 SIL OFL**；**fal 等第三方服务与素材各有其条款** |
| 主语言 | **Python 94.5%**（shields.io，2026-11-13） |
| 技术栈 | Agent Skills 格式技能包 + **`um` CLI**（Python，`uv tool install` / `pipx install`）+ **fal MCP server** + 各客户端配置（`.mcp.json` / `.codex/config.toml` / `.cursor/mcp.json` / `.vscode/mcp.json` / `opencode.json` / `.claude-plugin`）；`AGENTS.md` 为总入口（`CLAUDE.md` / `GEMINI.md` 指向它） |
| 关注度 | **9,656 Star / 1,080 Fork / 51 Watcher**（**页面值**，2026-11-13）；开放 Issue **2**、贡献者 **171**（shields.io，2026-11-13）。**ungh 镜像 9,640 / 1,074 / 50，略有差异，以页面值记录** |
| 维护状态 | **2026-09-30 创建**；ungh 报 `pushedAt` **2026-11-12**、页面 `updatedAt` 同日（**活跃，但仅约 6 周**）；**0 Release**（页面 `sections.releases` 为 0） |
| 默认分支 | `main` |
| 版本 | **0 Release / 0 tag**（以 git 分支与插件安装为主） |
| 话题标签 | `age-of-empires` · `claude-code` · `claude-code-plugin` · `fal` · `game-assets` · `game-modding` · `mcp` · `modding` · `reverse-engineering` · `tmodloader` |
| 体积 | `main` 分支 **924 个条目**（ungh.cc files API）；顶层以 `knowledge`（366）、`examples`（303）、`docs`（46）、`.agents`（44）、`.claude`（44）、`skills`（43）、`um`（27）、`tests`（12）为主 |
| 官网 | <https://universalmodder.com>（README 称"下一个"上线的 **Mod Hub**，尚未核对是否已上线） |

## 安装与用法（README 自述）

- **按 Agent 选装法**：Claude Code（`/plugin marketplace add ...` → `/plugin install universal-modder@universal-modder`）· Codex（`codex plugin marketplace add ...`）· Gemini CLI（`gemini extensions install <repo>`）· VS Code/Copilot · Cursor（市场或 clone）· OpenCode · 通用 `npx skills add https://github.com/rehan-remade/universal-modder` · 或直接 `git clone` 后在目录里启动 Agent。
- **技能存放位置**：clone 后各 Agent 在自己习惯的位置找到技能（`.agents/skills` 给 Codex/Gemini/Copilot/Cursor/OpenCode，`.claude/skills` 给 Claude Code，二者是 `skills/` 的副本）。
- **`um` CLI**：Claude Code 插件会通过 SessionStart 钩子把它放进 PATH；其他 Agent 需自行 `uv tool install git+https://github.com/rehan-remade/universal-modder`（或 `pipx`）。
- **fal key 的存放**：`um fal key set` —— Linux 存进桌面钥匙串（libsecret 的 `secret-tool`），其他平台存进**仅属主可读**的 `~/.secrets/fal-key`；`FAL_KEY` 环境变量仍然生效且**优先**。没有 key 时，fal 的 OAuth 端点 `https://mcp.fal.ai/mcp-relay` 会**通过浏览器登录**（省去手动配 key）。
- **素材命令**：`um fal`（fal）· `um comfy`（本地 ComfyUI）· `um sprixen` / `um voidless`（**各自独立付费服务，需各自的 key**）。
- **实做案例**（README 举例）：*San Franciscans*（AoE2 DE 新文明，含逆向出的 `.sld` 精灵写入器）· 真 Minecraft 装进 GTA V 故事模式（Fabric mod + ScriptHookV/ReShade 通过本地 WebSocket 交换相机/地面/事件）· Terraria 的 tModLoader 内容等。

## 使用前需要注意

- **它动的是商业游戏**：即便有"只改你拥有的游戏""不碰反作弊在线游戏""不写外挂"的自律，**你仍要自行承担**——与各游戏 **EULA / 用户协议**的冲突、**账号封禁**、以及**二次分发或发布 Mod 时的版权**问题。**README 只给了它自己的规则，没有给"请核对 EULA/当地法规"的通用提示。**
- **反作弊与联机风险不是它一句话能免除的**：任何注入/内存操作都有被反作弊判定为异常的可能；**联机、官方服务器场景尤其敏感**。README 说它拒绝注入带反作弊的在线游戏，**但这依赖它的判断，不等于平台不会误判。**
- **会调用付费第三方服务**：生成美术/3D/音频需 **fal**（按量计费）；`um sprixen` / `um voidless` 是**另外两个付费服务**，各自要 key。**不配 key 就走不了 AI 素材。**
- **它会驱动你的鼠标键盘、往游戏目录装 loader**：README 说**动手前会先问**，但这些操作本身会改动你的系统与游戏安装，**建议在可回滚的环境里用**。
- **极新的项目**：**距创建约 6 周**、0 Release、9.6k Star / 171 贡献者。**API、技能结构与用例都在快速变动**（README 的 Star 曲线图也印证增长很陡）。
- **知识库是"Agent 写给 Agent"的**：`knowledge/` 里的笔记来自 Agent 的实战会话，**其准确性未经过人工复核**，当作线索而非权威事实来用更稳妥。
- **我只读了文档与元数据**：**未 clone、未装插件、未跑 `um`、未配 key、未做任何 Mod、未在游戏里测试**；所有能力描述与案例均来自 README。

## 什么时候会再看

- 想让编码 Agent **在自己拥有的 PC 游戏上做改动**（识别引擎、读代码、做 Mod、生成素材、在游戏里测）时，先来查它的技能清单、引擎 playbook 与安装方式。
- 想参考"**怎么给'有风险的能力'写硬约束**"时——它的 `## Rules it follows` + `safety.md`（不分发游戏资源、不碰反作弊、先备份、动手前先问）是很好的范本。
- 想对照"**AI 改软件 / 造素材**"的几个近邻：[craft-anything](craft-anything-看清软件如何工作再用AI重建自己的版本.md)（看清软件再用 AI 重建）· [ghidra-mcp](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)（把逆向工具接给 AI）· [agent-sprite-forge](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)（给 Codex 生成 2D 游戏精灵）。
- 想研究"**AI 生成素材 + 现成工具链**"如何拼成一条生产线时（fal / ComfyUI / 专用精灵服务）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属"用 Agent 处理真实世界事务、并把边界写进规则"**：[jobflow-for-codex：面向 Codex 的本地求职工作流](jobflow-for-codex-面向Codex的本地求职工作流.md)——那篇让 Agent **辅助求职流程**（不绕验证码 / 风控、关键判断留给人）；本篇让 Agent **改你拥有的游戏**。可对照它们**如何把敏感边界（不越权、先问、不绕风控）写成规则**。
- **同属"用 AI 逆向 + 重做软件"**：[craft-anything：看清软件如何工作，再用 AI 重建自己的版本](craft-anything-看清软件如何工作再用AI重建自己的版本.md)——那篇是**通用软件**的"学习 → 重建"流水线（clean-room 不读目标内部），本篇是**游戏**的"侦察 → 改 → 测 → 发"流水线（**会读真实代码**）。**两者的 clean-room 边界正好相反**，对照看很能说明"合规路线怎么选"。
- **逆向工具接 AI 的另一条路**：[ghidra-mcp：把 Ghidra 接给 AI 的 MCP 服务器](ghidra-mcp-把Ghidra接给AI的MCP服务器.md)——它给 AI 一个**现成的反汇编/反编译器**当工具；本篇则把逆向**编进技能与引擎手册**里、并按需调用 Ghidra（README 的 Credits 列了 Ghidra/ILSpy 等）。
- **AI 生成游戏素材的近邻**：[agent-sprite-forge：给 Codex 的 2D 游戏精灵生成技能](agent-sprite-forge-给Codex的2D游戏精灵生成技能.md)——那篇专做 **2D 精灵**；本篇的素材面更宽（图/3D/音频，走 fal / ComfyUI / Sprixen / Voidless），可对照"**素材生成该自建还是借服务**"。
- **同样"AI 生成 3D 素材"的另一篇**：[dioramas：用 AI 生成素材做电影感 3D 网站](dioramas-用AI生成素材做电影感3D网站.md)——两篇都用 **fal** 生成资产（一个做游戏 Mod，一个做 3D 网站），可对照**同一条素材管线在不同产物上的用法**。

## 参考来源

- 仓库主页与 README（24,216 字符 / 257 行，核验日期 2026-11-13）：https://github.com/rehan-remade/universal-modder
- 许可证：`LICENSE`（MIT，1,096 字符，`Copyright (c) 2026 Rehan and universal-modder contributors`）
- 安全规则全文与推理：`skills/mod-any-game/references/safety.md`（README 指向）
- 知识库索引：`knowledge/INDEX.md`（README 指向）
- 素材服务：<https://fal.ai>（fal key 页 <https://fal.ai/dashboard/keys>）· fal MCP 说明 <https://fal.ai/docs/documentation/setting-up/mcp> · Sprixen <https://sprixen.com/settings/api> · Voidless <https://voidless.dev/api-docs>
- 完整文件树（924 条目）：ungh.cc files API（2026-11-13）
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / 许可证字段 / 是否归档 / topics）：GitHub 页面内嵌数据（2026-11-13）；镜像对照取自 ungh.cc（同日，**与页面略有差异**）；Issue 数与贡献者数取自 shields.io（2026-11-13）；归属账号经 ungh `orgs` 接口确认为个人账号

以上信息核验于 2026-11-13。**未 clone、未安装插件、未运行 `um` CLI、未配置任何 key、未制作任何 Mod、未在游戏中测试**；技能清单、引擎 playbook、安全规则、安装方式与案例均来自 README 与其指向的文件路径，"早期项目"的判断基于创建时间（2026-09-30）与 0 Release。
