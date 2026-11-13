# 词光 Word Atlas：带插画与发音的雅思背词应用，外加一份可复用的开放词库

> 一个雅思背词应用（词光 · Word Atlas），**7,298 个词条、一词一插画、英美发音、FSRS 间隔复习**，同时把整份词库、插画和录音**按 CC BY 4.0 开放**，代码按 MIT 开放。它值得记的点在于"**软件与内容分离授权**"的清晰做法，以及它**如实标注内容由 AI 生成、未逐条人工审校**的边界说明——既能当成品用，也能把数据拿去做自己的 Anki 牌组或研究。

- 状态：草稿
- 最近核验：2026-11-13（阅读 GitHub 页面内嵌数据、ungh.cc 元数据与 `main` 文件树、README 全文、`LICENSE` 与 `CONTENT_LICENSE.md`；**未 clone、未 `npm ci`、未本地运行、未访问在线站、未下载音频或图片、未核查任何词条内容准确性**）
- 适用范围：**Web / PWA**；本地运行需 **Node.js 20+ 与 npm**；技术栈 **React 19 · TypeScript · Vite · ts-fsrs · WebGL2**（星图需 WebGL2，不支持时降级显示）。另有 iOS App 测试版（通过仓库外的外部链接分发）。**词库为雅思方向、非官方词表。**

## 核心结论

**一句话定位（README 自述）**："把单词变成画面，让复习有迹可循。"——**开源基础版应用 + 配套数据集**。

**三个最值得记的点：**

1. **代码与内容分离授权，边界写得很清楚**：应用代码 **MIT**（`LICENSE`），而**词库文本、插画、词条录音**按 **CC BY 4.0**（`CONTENT_LICENSE.md`），署名 `Word Atlas contributors` 并附授权链接、说明是否修改；第三方依赖与字体（如 Fraunces 为 SIL OFL 1.1）各自授权。**这让同一份数据能被外部工具复用（Anki / 研究），而代码仍可自由改造**——比单一许可证更适合"应用 + 开放数据"的组合。
2. **本地优先、无需账号**：进度保存在本机（无注册、无账号），看过的词条与媒体可缓存离线使用；但**换设备需自行导出进度备份**。仓库**自带完整图片与录音**，所以文件树达 3.7 万余项、首次 clone 较慢。
3. **对内容来源与局限相当坦诚**：README 与 `CONTENT_LICENSE.md` 都写明——文字（大语言模型）、插画（图像模型）、录音（ElevenLabs 合成语音）由 AI 生成，**经过自动校验但未逐条人工审校**，可能含释义 / 例句 / 插画 / 发音错误；**不是官方雅思词表、不承诺提分**；录音授权**只覆盖成品音频文件，不涉底层声音模型、声音身份或任何人的肖像与人格权**。

**个人判断（未验证）**：它的 Star 增长很快（见项目信息），"插画 + 发音 + FSRS"这套组合对形象记忆有帮助是合理的；但**内容是 AI 生成且未逐条审校**，用于正式考试备考或教学时应自行复核关键释义，别把"看起来完整"当成"已经正确"。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [designfuchen/word-atlas](https://github.com/designfuchen/word-atlas) |
| 描述 | "词光 · Word Atlas — Illustrated IELTS vocabulary with 7,298 words, UK/US audio and FSRS review. Open-source app + CC BY 4.0 dataset." |
| 归属 | 账号 `designfuchen`（**个人用户**，页面 `isOrg: false`）；`isFork: false`、`isArchived: false`、`isPrivate: false`、`isTemplate: false` |
| 许可证 | **双授权**：代码/界面 **MIT**（`LICENSE` 首行 `MIT License`，署名 `Copyright (c) 2026 Word Atlas contributors`）；词库文本、插画、词条录音 **CC BY 4.0**（`CONTENT_LICENSE.md`）。**页面 `spdxId` 显示 `NOASSERTION` / `Other`，即页面未识别出单一许可证；实际许可以 LICENSE 与 CONTENT_LICENSE.md 两个文件为准** |
| 主语言 | **TypeScript 59.1%**（shields.io，2026-11-13） |
| 技术栈 | **前端 Web 应用**：React 19 · TypeScript · Vite · `ts-fsrs`（FSRS 间隔复习）· WebGL2（词汇星图）；数据与媒体随仓库分发（`data/`、`public/atlas/`） |
| 关注度 | **4,769 Star / 1,014 Fork / 11 Watcher / 26 开放 Issue**（**页面值**，2026-11-13；Issue 数取自 shields.io。**页面值与 ungh.cc 镜像不一致：镜像报 4,738 Star / 1,007 Fork / 11 Watcher，此处记录页面值**） |
| 维护状态 | **2026-10-25 创建**；页面 `updatedAt` **2026-11-12**、ungh `pushedAt` **2026-11-08**（距核验日约 1–5 天，**活跃**）；**0 Release / 0 tags**（以网页与源码分发，未打版本标签） |
| 默认分支 | `main` |
| 规模（内容） | **7,298 个词条**（CEFR A1–C2、19 个话题）· **14,596 条例句** · **7,298 幅插画**（512 px WebP，另有缩略图）· **22,905 段录音**（英音、美音、例句朗读） |
| 话题标签 | `chinese` · `dataset` · `english-learning` · `flashcards` · `fsrs` · `ielts` · `language-learning` · `pwa` · `react` · `spaced-repetition` · `vocabulary` · `webgl` |
| 体积 | `main` 文件树约 **37,728 个文件**（ungh.cc files API；绝大多数是 `public/atlas/audio/` 下的 mp3 与 `public/atlas/img/` 下的插画） |
| 仓库结构 | `data/`（`word-atlas.json`、`word-atlas.csv` 词库）· `public/atlas/`（`img/` 插画、`audio/` 录音、`entries/`、`index.json`）· `public/sfx/`（界面音效与配乐，随应用）· `docs/`（`dataset.md`、`deployment.md`、`learning-model.md`、`pipeline.md` 与预览图）· 根目录 `index.html` / `package.json` |
| 官网 | <https://0xlei.tech/words/> |

## 词库与内容（README / CONTENT_LICENSE 自述）

| 资源 | 开源版内容 | 入口 |
| --- | --- | --- |
| 词条 | **7,298** 条，CEFR A1–C2、19 个话题 | `data/word-atlas.json` · `data/word-atlas.csv` |
| 例句 | **14,596** 条，日常与雅思语境，附中文翻译 | 字段说明见 `docs/dataset.md` |
| 插画 | **7,298** 幅，512 px WebP，另有缩略图 | `public/atlas/img/` |
| 录音 | **22,905** 段，英音、美音与例句朗读 | `public/atlas/audio/` |

**重要提示（README 明说）**：**这个仓库提供的是词光的开源基础版及数据集；在线网站独立迭代，功能与仓库可能不同。** 引用"它有哪些功能"时，要区分"仓库开源版"还是"在线站 / iOS App"。

## 使用前需要注意

- **⚠️ 双授权，别当成单一许可**：**代码 MIT，但词库文本 / 插画 / 词条录音是 CC BY 4.0**。要复用数据（做 Anki 牌组、研究、二次分发）须**署名 `Word Atlas contributors` + 附授权链接 + 说明是否修改**，并遵守 CC BY 4.0；第三方依赖与字体仍各自授权。页面把许可证显示为 `NOASSERTION` / `Other`，**不代表"无许可证"**，而是双授权未被自动识别。
- **⚠️ 录音授权有边界**：`CONTENT_LICENSE.md` 说明录音授权**只覆盖这些成品音频文件**，**不包含底层声音模型、声音身份或任何人的肖像与人格权**，也不表示声音提供方（ElevenLabs，"Alice" 英音 / "Sarah" 美音）为本项目背书。
- **⚠️ 内容由 AI 生成、未逐条人工审校**：释义、例句、插画、录音均可能出错，**按现状提供、不作准确性担保**；**非官方雅思词表**，与雅思主办方无关，**不承诺提分**。用于正式备考 / 教学请自行复核。
- **⚠️ 仓库体量很大**：约 **37,728 个文件**，绝大部分是 mp3 与插画，**首次 `git clone` 较慢**（README 自己提醒"首次下载可能较久"，并建议 `git clone --depth 1`）。不适合当作轻量依赖随取随用。
- **⚠️ 本地优先但有取舍**：进度存本机、无需账号，但**换设备要自己导出 / 恢复备份**；离线可缓存媒体，但并未承诺完全离线。
- **⚠️ 无版本标签**：**0 Release / 0 tags**，仓库以网页与源码持续迭代，缺少稳定版本号；iOS App 测试版通过**外部链接**（`api.billswise.cn/app`）分发，不在本仓库内。
- **我未做任何运行验证**：**未 clone、未 `npm ci`、未本地运行、未访问在线站、未下载任何音频或图片、未核查任何词条内容是否准确**；上述数据量、技术栈、学习流程与授权边界**均来自 README、`LICENSE`、`CONTENT_LICENSE.md`、文件树与元数据自述**。

## 什么时候会再看

- 需要一份**可自由复用（CC BY 4.0）的雅思方向词库 / 插画 / 发音数据集**（做 Anki 牌组、学习工具或研究）时。
- 想参考"**应用 + 数据集同仓、代码与内容分离授权**"的仓库组织方式（MIT 代码 + CC BY 4.0 内容 + 单独 `CONTENT_LICENSE.md` 说明）。
- 想研究**本地优先（无账号、进度存本机）背词应用 + FSRS 间隔复习 + WebGL2 星图**的前端实现时。
- 想借鉴它**如实披露"内容由 AI 生成、仅自动校验、未逐条审校"**这类内容来源声明——对 AI 生成内容的项目很有参考价值。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为英语学习方向的收藏项目（但重在书稿与训练体系，非数据集）**：[everyone-can-use-english：人人都能用英语](everyone-can-use-english-人人都能用英语.md)——一个讲"怎么学"，一个提供"可复用的雅思词库 / 插画 / 发音数据"，合看正好是方法与素材两端。
- **同为"开放教育数据 / 知识图谱"的项目**：[cn-math-taxonomy：对齐课标的中小学知识图谱与前置诊断](cn-math-taxonomy-对齐课标的中小学知识图谱与前置诊断.md)——都在把教育内容结构化为可复用数据，可对照其数据字段设计与授权说明的写法。

## 参考来源

- 项目仓库与 README：https://github.com/designfuchen/word-atlas
- 在线体验站：https://0xlei.tech/words/
- 代码许可（MIT）：https://github.com/designfuchen/word-atlas/blob/main/LICENSE
- 内容许可（CC BY 4.0 及署名、声音授权说明）：https://github.com/designfuchen/word-atlas/blob/main/CONTENT_LICENSE.md
- 数据集字段说明：https://github.com/designfuchen/word-atlas/blob/main/docs/dataset.md
- 记忆模型说明：https://github.com/designfuchen/word-atlas/blob/main/docs/learning-model.md
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 语言 / 默认分支 / Release 数 / topics / 文件名）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未安装、未本地运行、未核查任何内容准确性**；数据量、技术栈、学习流程与授权边界均来自 README、`LICENSE`、`CONTENT_LICENSE.md`、文件树与元数据自述。
