# capcut-cli：直接读写剪映草稿的命令行剪辑工具

> 一句话定位（README 自述）：一个**独立、非官方**的命令行工具，能**直接读写 CapCut / 剪映（JianYing）的本地草稿**——"JSON 进、JSON 出"，**不需要上传、API、MCP 服务器或 HTTP 守护进程**，成片在 CapCut / 剪映里打开后**每条轨道都仍可编辑**。最值得记的三点：① 它把草稿库（`draft_content.json` / `draft_info.json`）当成**确定性边界**，任何 LLM agent（Claude、DeepSeek、GLM、Kimi）都能用它做字幕、变速、音量、模板、长转短等操作；② 一份二进制**四种用法**（CLI / Library / 队列 runner / 实验性只读 Wasm MCP）；③ 有**必须升级的安全公告**——旧版本的 fixture 包可能含稳定设备标识、旧版本还有命令 / 过滤器注入路径，均在 0.18.0 / 0.17.1 修复。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（204 行）、`LICENSE`（MIT，1,068 字符）与元数据；**未 clone、未安装、未运行 `capcut`、未编辑任何草稿、未用 `caption --cloud`、未构建 `capcut-core.wasm`**）
- 适用范围：**Node ≥ 18**（仅用内置模块、**零原生依赖**）；可选工具解锁部分命令——**Whisper**（`caption`）、**FFmpeg**（`render`）、**ffprobe**（自动媒体元数据）、**`ANTHROPIC_API_KEY`**（`translate`）；草稿库在 **macOS 与 Windows** 上路径不同（由 skill 说明）

## 核心结论

1. **它不通过官方接口，而是直接改本地 JSON**（README 自述）：CapCut / 剪映把每个工程存成**本地 JSON**，capcut-cli 加载草稿库 → 按**版本感知的 schema** 校验 → 应用改动 → **原子写回（带 `.bak` 备份）**。**工程文件不上传任何地方**，只有 `caption --cloud` 例外会把音频发出去。
2. **新版本会同步所有可读时间线**：在较新的 CapCut 版本上，它"检测并同步**每一个可读的时间线目标**，而不是假定 `draft_content.json` 是唯一事实来源"。
3. **正式"独立项目"声明**：README 明确"**不隶属、未获赞助、未获背书于** CapCut、剪映或字节跳动"，商标仅作**指称性使用**描述互操作性。官网 <https://renezander.com/projects/capcut-cli>。
4. **交付物是"可编辑草稿"而不是"扁平导出"**：`quickstart` 出来的工程里视频和字幕都在**可编辑轨道**上，打开 CapCut / 剪映就能复核、调整、渲染——"**发布这一次点击留给人类**"。

**四种用法（README 自述）：**

| 形态 | 用法 |
| --- | --- |
| **CLI** | `npm install -g capcut-cli`，然后 `capcut <command> <project>` |
| **Library** | `import { loadDraft, lintDraft, saveDraft } from "capcut-cli"`（带类型、零依赖） |
| **Queue runner** | `capcut serve` 从 stdin 读 JSONL 作业，供 n8n / Make / Coze 使用 |
| **Agent sandbox（实验）** | 构建 `capcut-core.wasm`，得到三个**只读** MCP 工具，零文件系统 / 网络 / 环境 / 时钟 / 随机 / stdio / 进程导入 |

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | https://github.com/renezander030/capcut-cli |
| 描述（原文） | "Independent, unofficial CLI to edit CapCut and JianYing (剪映) projects — subtitles, timing, speed, volume, templates, cut long-form to shorts. No API needed, reads draft_content.json and draft_info.json (剪映草稿) directly. Not affiliated with ByteDance." |
| 归属 | **个人账号 renezander030** |
| 许可证 | **MIT**（`LICENSE` 1,068 字符，"MIT License / Copyright (c) 2026 Rene Zander"；页面 spdxId = MIT） |
| 默认分支 | **`master`**（注意：不是 `main`） |
| 关注度 | **页面 Star 1,180 / Watcher 6 / Fork 150**；ungh 镜像 1,173 / 6 / 150；shields 另报 **stars 1.2k、forks 150、开放 Issue 1、贡献者 13**（2026-11-13） |
| Release | 页面 releaseCount **44** / tagCount **49**（ungh 单页最多返回 30 条，故不等同） |
| 维护状态 | **2026-04-13 创建**；ungh `pushedAt` 2026-11-08、页面 `updatedAt` 2026-11-12（活跃） |
| 官网 | <https://renezander.com/projects/capcut-cli> |
| 分发 | npm：`npm install -g capcut-cli`（也可 `npx capcut-cli <command>` 免安装）；Python：`pip install capcut` 包同一二进制 |
| topics | agent-skills, ai-agent, automation, capcut, capcut-api, capcut-automation, capcut-editor, capcut-subtitles, capcut-template, claude-code-plugin, cli, draft-content-json, jianying, python, srt-export, subtitle-editor, video-editing, video-production, video-to-shorts |

## 命令与用法

### 快速开始

```bash
npm install -g capcut-cli
capcut doctor
capcut quickstart my-first --video clip.mp4 --srt captions.srt
capcut info ./my-first/ -H
```

结果是一个**真实本地工程**：视频和字幕都在可编辑轨道上，打开 CapCut / 剪映即可复核、调整、渲染。

### 命令分组（README 表格）

| 分组 | 命令 |
| --- | --- |
| **Inspect（检查）** | `info` · `tracks` · `materials` · `version` · `lint` |
| **Browse / drill in（浏览）** | `segments` · `texts` · `segment` · `material` |
| **Create（创建）** | `init` · `quickstart` · `compile`（从 JSON spec 构建草稿） |
| **Preview（预览）** | `render`（低分辨率 ffmpeg 代理，**不是** CapCut 的最终渲染） |
| **Add（添加）** | `add-video` · `add-audio` · `add-text`（支持 Wikimedia URL，带许可检查） |
| **Edit / animate（编辑 / 动画）** | trim · speed · volume · transitions · masks · 文本 / 图片动画 · 缓动曲线 |
| **Templates（模板）** | 应用与提取可复用布局 · `make-preset`（可移植的文本样式预设） |
| **Subtitles & i18n（字幕 / 多语言）** | `caption` · `import-srt` · `export-srt`（行 / 词级 SRT + VTT）· `translate`（多语言草稿克隆） |
| **Effects（特效）** | `sfx` · `chroma`（抠像）· `matting`（智能背景移除） |
| **Long-form → short（长转短）** | `cut` · `detect-scenes`（ffmpeg 场景切分检测）· `detect-silence` · `detect-retakes`（重复镜次） |
| **Automation（自动化）** | `serve`（无状态 JSONL runner）· `migrate` · `doctor` · `sync-timelines`（8.7 镜像修复） |

默认输出 JSON（可管道给 `jq`），加 `-H` 输出人类可读表格，加 `--jianying` 使用剪映的枚举命名空间；`capcut describe --compact` 可列出命令名、摘要、用法与写状态。

### 给 agent 装 skill

```bash
npx skills add renezander030/capcut-cli
```

一行命令把 `capcut-edit` skill 装进 Claude Code、Codex、Cursor、OpenCode 等支持 `skills` 安装器的 agent；Claude Code 也能按插件加载（`/plugin marketplace add renezander030/capcut-cli`）。该 skill 会教 agent 每条命令、"**先检查、绝不整份 dump 草稿**"的习惯、草稿库在 macOS 与 Windows 的位置，以及淡入淡出 / Ken Burns / 长转短的确定性脚本，并**同时响应中英文请求**（剪映、字幕、草稿）。

### 往 CapCut 已拥有的工程里写（需谨慎）

```sh
capcut compile spec.json --into /path/to/app-created-project --active-timeline --check
capcut compile spec.json --into /path/to/app-created-project --active-timeline
```

要求在 CapCut 里**先建一个空工程并退出应用**再执行；每个 root 与 active mirror 都必须为空；导入的媒体会暂存到工程下，注册信息与时间线备份会一并提交。README 提醒"**在你自己的 app 构建上做桌面验收仍属未验证**"，建议"重开、保存、关闭、再重开"确认改动持久化。

## 使用前需要注意

- **先升级再使用**（README 的 IMPORTANT 公告）：**截至 0.17.2** 的 fixture 包可能含**稳定设备标识**，须按"未脱敏"处理（issue #59）；**截至 0.17.0** 还存在**本机命令 / 过滤器注入路径**与**不安全的临时文件 / 凭证输出**行为。这些分别在 **0.18.0** 与 **0.17.1** 修复，请运行 `npm install -g capcut-cli@latest`。
- **`caption --cloud` 是唯一的"联网"出口**：它是**实验性**命令，会上传它要转写的**音频**到字节跳动；其余命令均不联网、不作为服务运行。
- **`render` 不是最终渲染**：README 明确它是"低分辨率 ffmpeg 代理"，**不是** CapCut 的最终渲染；最终渲染仍在 CapCut / 剪映里由人完成。
- **Wasm MCP 是实验且可选**：`capcut-core` 提供 `inspect` / `diff` / `lint-portable` 三个**只读**工具，且"零 ambient 能力"；不设置就不改变任何行为，**别把实验特性当成稳定功能**。
- **字幕 / 翻译有严格校验**：v0.32.0 起"时间参数会拒绝畸形或不安全的值";"翻译响应必须每个输入**恰好一个字符串**";"字幕导入会拒绝畸形 UTF-8，而不是静默损坏字幕文本"——说明这些边界是**故意做严**的。
- **赞助与商业支持是增值项**：capcut-cli 本体 **MIT 且永久免费**；赞助（$5 / $25 / $100 每月分档）解锁的是更快发布、私有模板包、优先支持等，另有独立的商业集成对接。
- **贡献者与元数据**：shields 报贡献者 **13**、开放 Issue **1**;页面 Star（1,180）与 ungh 镜像（1,173）略有差异，**引用时注明来源与日期**。
- **我只读了 README 与 LICENSE**：**未 clone、未安装、未运行、未编辑任何草稿、未用 `caption --cloud`、未构建 wasm**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想用**命令行 / agent 批量改剪映或 CapCut 草稿**（字幕、时间、变速、音量、模板）而不手动拖时间线时。
- 想把**长视频自动切成短视频**（`cut` / `detect-scenes` / `detect-silence` / `detect-retakes`），又希望结果是**可继续编辑的草稿**时。
- 想让 agent 在**最小权限**下"只看不改"地审查草稿（`capcut-core` 只读 Wasm 工具）时。
- 想研究"**把草稿 JSON 当确定性边界**、供任意 LLM 驱动"这种 CLI 设计（JSON in / JSON out、`describe` 自描述契约、`serve` JSONL 队列）时。
- 想对照"AI 做视频"的另一条路线：[video-shotcraft](video-shotcraft-用Remotion做电影感产品宣传片的视频技能.md)（代码动画成片并**导出剪映草稿**）· [autoclip](autoclip-把一个链接剪成多平台短视频的桌面工具.md)（链接 → 多平台短视频）· [adu-motion-video](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)（模板工程包 → 可编辑工程）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **产线正好对接**：[video-shotcraft：用 Remotion 做电影感产品宣传片的视频技能](video-shotcraft-用Remotion做电影感产品宣传片的视频技能.md)——那篇把代码动画成片**导出为可编辑的剪映草稿**；本篇反过来**直接读写剪映 / CapCut 的草稿 JSON**做字幕、变速、长转短。一个"生成可编辑工程"，一个"用命令行继续改工程"。
- **都是"链接 / 长片进、短视频出"**：[autoclip：把一个链接剪成多平台短视频的桌面工具](autoclip-把一个链接剪成多平台短视频的桌面工具.md)——那篇是**桌面应用**（自动选段、字幕、封面、发布包，本机渲染）；本篇是**面向 agent 的 CLI + Library**，且产物落在**剪映草稿**里。可对照"**端到端桌面工具**"与"**可编程的草稿层工具**"。
- **同属"给编码助手一套视频技能"**：[adu-motion-video：阿杜的 AI 剪辑模板技能与 MotionGit 开放格式](adu-motion-video-阿杜的AI剪辑模板技能与MotionGit开放格式.md)——那篇是**模板工程包**、导出**可继续改的工程**；本篇是**草稿读写工具**。两者都强调"**产物是可编辑的、不是一次性成片**"，可对照各自的可编辑边界。
- **都是"产物落在可继续编辑的剪辑工程"**：[baocut：带可编辑时间轴的 AI 视频 Agent](baocut-带可编辑时间轴的AI视频Agent.md)——本篇**直接读写剪映 / CapCut 的草稿 JSON**（寄生在剪映生态），那篇**自带引擎与协议**、把结果留在 Baocut 自己的时间轴上；可对照"寄生在剪映生态"与"自带引擎与协议"。

## 参考来源

- 仓库主页与 README（204 行；核验日期 2026-11-13）：https://github.com/renezander030/capcut-cli
- 官网：<https://renezander.com/projects/capcut-cli>
- 许可证：`LICENSE`（MIT，1,068 字符；核验日期 2026-11-13）
- 文档：`docs/command-reference.md`（及简体中文 `docs/command-reference.zh-CN.md`）、`docs/quickstart.zh-CN.md`、`docs/version-support.md`、`docs/jianying-encryption.md`、`CHANGELOG.md`、`wasm/capcut-core`、`examples/`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌 JSON 与 ungh.cc、shields.io 交叉核对（2026-11-13，**页面值与镜像值存在差异**）

以上信息核验于 2026-11-13。**未 clone、未安装、未运行任何命令、未编辑任何草稿、未使用 `caption --cloud`、未构建 `capcut-core.wasm`**；能力与流程描述均来自 README，**未逐一验证**。
