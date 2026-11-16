# aimux：用 Rust 写的统一 LLM 接入层

> 一句话定位（README 自述）：**"A unified LLM access layer written in Rust. One API for 327 provider names."**——把各家 AI 服务商的 HTTP API **收进一个 `dyn LanguageModel` 接口**，上游换 provider 不改调用点。最值得记的三点：① 它**明确只做"接入层"不做"编排层"**（README 直接对比 rig / langchain："aimux is an access layer, those are orchestration layers"）——不做 agent loop、RAG、编排；② 覆盖面广且**多模态**（文本 / 流式 / 工具调用 / 嵌入 / 图像 / 语音 / 转写 / 视频 / 重排 / 文件，另有**实时转写会话**），并内置**录制回放、会话归组、缓存命中追踪**与**复合模型（Router / MoA）**；③ **一个 Rust 核心 + 8 种语言绑定**（Node / Python / Swift / Kotlin / Flutter / Go / Java / C），已发到 crates.io / npm / PyPI / Maven Central / pub.dev。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README（英文 434 行）、`LICENSE`（MIT，1,065 字节）与已核实元数据；**未 clone、未装 Rust 工具链、未 `cargo add`、未 npm / pip 安装、未编译、未调用任何 provider、未复现 benchmark**）
- 适用范围：**Rust**（核心库，需 stable 工具链）或 **8 种语言绑定**之一的运行时；要真正调模型需自备对应 provider 的 API key（本地引擎如 Ollama / vLLM 作为 preset 覆盖，免 key）；**当前主线为 0.6.0，是破坏性版本**，从 0.5 迁移须先读升级指南

## 核心结论

1. **定位是"接入层"而非"编排层"**（README 原文）：aimux 把**每个 provider 的 HTTP API 收进一个 `dyn LanguageModel` 接口**，上游可互换 provider 而不改调用点。它**刻意不做** agent loop、RAG 或编排——README 把这条当作与 rig / langchain 的分界线。
2. **覆盖面：description 说 325、README 正文与徽章说 327。** README 的拆解是 **327 = 282 个 registry 驱动的 OpenAI 兼容 preset + 45 个 vendor 包**；**本地引擎（Ollama / vLLM / SGLang / Llama.cpp / LiteLLM Proxy）是 preset**。**两处数字不一致（325 vs 327），引用时以 README 正文口径为准并注明该差异。**
3. **多模态 + 不止"聊天"**（README）：文本、流式、工具调用、嵌入（embeddings）、图像、语音、转写、视频、重排（reranking）、文件，另有 **0.3.0 起的实时转写会话**（WebSocket 推音频 / 取转写片段 / 可重试超时）。
4. **可观测性内建（0.3.0 起）**：把每对请求 / 响应**录成 JSONL 并离线回放**（写法一行开关，**落盘前先脱敏**）、把调用**按 `session_id` 归组**、并**从响应头识别 provider 的 prompt-cache 命中**。
5. **复合模型也是普通 `LanguageModel`**（0.3.0 起）：`RouterModel` 每次调用按规则选一个模型并在失败时回退；`MoaModel` **并行扇出到多个参考模型再聚合**（mixture-of-agents）。**两者都仍是 `dyn LanguageModel`，所以所有 API 与绑定不改就能用。**
6. **一个核心、多语言绑定**（README 绑定表）：Node（napi-rs v3，原生库随包）、Python（PyO3 + maturin，随 wheel）、Go（cgo 静态链接，`.a` 从 GitHub Releases 自动下载）、Swift（SPM，本地包）、Kotlin / Java（JNA，Maven Central）、Flutter（dart:ffi，pub.dev）、C/C++（`.so`/`.dylib`/`.dll` + `aimux.h` 从 Releases 取）。**非原生绑定走同一条 C ABI**（`aimux-ffi`）。
7. **测试不联网、不要 key**：**2,800+ cassettes 回放真实 API 响应**（`cargo test -p aimux-providers --tests` 即可跑）。

**个人判断（未验证）**：aimux 的价值不在"支持多少家"这个数字本身，而在**"一个 `dyn` 接口 + 一份 registry JSON + 一条 C ABI"**这三件事同时成立——它让"加一家 OpenAI 兼容的 provider"变成**往 JSON 里加一行**，而"加一门语言"变成**接同一条 ABI**。**但 0.6.0 是破坏性版本**（操作式 ABI、DTO 改为生成、错误统一信封、公开 JSON 改为 AI SDK V4 形状），**它把自己定位成"仍在快速收敛 ABI 的基础设施"**——想接的话要先接受"跟着大版本改接口"这个前提（**个人判断（未验证）**）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [arcships/aimux](https://github.com/arcships/aimux) |
| 描述 | "Unified LLM access layer in Rust — one API for 325 AI providers. 统一 LLM 服务接入层：一套 API 接入 325 家 AI 服务商。" |
| 许可证 | **MIT**（`LICENSE` 1,065 字节，Copyright (c) 2026 arcships） |
| 主语言 | **Rust 78.6%**（shields `languages/top`，2026-11-13） |
| 关注度 | **页面 243 Star / 3 关注 / 17 Fork**（2026-11-13，与 ungh 镜像 **243 / 3 / 17** 一致）；shields 另报**开放 Issue 19、贡献者 6** |
| 维护状态 | **2026-07-28 创建**；pushed **2026-11-12**（活跃）；页面 `updatedAt` 2026-11-09；**默认分支 `master`（非 `main`）**；**6 Release** |
| 体积 | **5,802 个文件**（ungh `files/master`，仓库根较大：Rust workspace + 8 套绑定 + 文档 + RFC） |
| 分发 | crates.io（`aimux-core` / `aimux-providers` / `aimux-ops` / `aimux-stream` / `aimux-provider-utils` / `aimux-ffi`）、npm（`@arcships/aimux`）、PyPI（`arcships-aimux`）、Maven Central（`ai.arcships:aimux-java` / `aimux-kotlin`）、pub.dev（`aimux`，publisher `arcships.ai`）、Go（`github.com/arcships/aimux/bindings/go`） |

## 结构、用法与关键机制

### 1. 仓库结构（README）

```
aimux-core            核心抽象：LanguageModel / Provider / Message / StreamPart
aimux-providers       provider 实现（registry 驱动 + 类型化）
aimux-ops             共享操作分发器、manifest 与生成的 DTO
aimux-stream          SSE 解析
aimux-provider-utils  单次请求 HTTP 助手、响应处理器、API key 读取、流式工具调用跟踪
aimux-ffi             C ABI（不透明句柄 + JSON 结果 + 自有 aimux_error_t *）
tools/                aimux-cli（缓存探测） · aimux-replay · aimux-web（浏览器控制台）
```

两条路径：**原生路径**（绑定直接调 `aimux-core` + `aimux-providers`，用 Rust 类型与 async）与 **C ABI 路径**（`aimux-ffi` 交出不透明句柄 + JSON 结果）。

### 2. 最小用法（README）

Rust（默认 provider 会在发请求时读 `OPENAI_API_KEY`）：

```rust
use aimux_core::prelude::*;
use aimux_providers::openai::openai;

#[tokio::main]
async fn main() -> Result<(), AiMuxError> {
    let model = openai().chat("gpt-4o");
    let result = generate_text(&model, "Explain Rust ownership in one sentence.", GenerateTextOptions::default()).await?;
    println!("{}", result.text);
    Ok(())
}
```

Node（0.6 的类型化门面用 `Client`，**0.5 的顶层 `openai` / `generateText` 助手已移除**）：

```typescript
import { Client, LanguageModel } from '@arcships/aimux'
const client = new Client()
const provider = await client.provider({ package: 'openai', settings: { apiKey: process.env.OPENAI_API_KEY } })
const model = await provider.model('gpt-4o-mini', 'languageModel')
const result = await model.generate('Explain Rust ownership in one sentence.')
```

### 3. 三个值得单独记的机制（README）

- **录制与回放**：`init_recording(Some(Arc::new(JsonlRecorder::new("./recordings"))))` 一行给整个进程打开录制，产物是可离线回放的 JSONL（**落盘前脱敏**）；配合 `aimux-replay` CLI。用途写明是回归测试、事故取证与"不联网、不要 key 的 CI"。
- **复合模型**：`RouterModel`（每次调用选一个、失败回退）+ `MoaModel`（扇出参考模型再聚合），**都是 `dyn LanguageModel`**，因此所有语言绑定不用改就能用。
- **实时转写会话**：WebSocket 长连接，**推音频 → `operation.inputDone` → `operation.next`（带 `timeoutMs`）取片段**；**同样的操作名在 8 种语言里都可用**。

### 4. 0.6.0 的破坏性变化（README "Upgrading from 0.5"）

- **操作取代构造函数**：绑定改用 `provider.create` + `provider.model` 与共享操作名，**provider 专属构造函数与 0.5 顶层助手被移除**。
- **公开 JSON 改为 AI SDK V4 形状**：字段 camelCase，联合类型用 AI SDK 标签，文件 / 工具 / 用量 / 流的形状都对齐 AI SDK。
- **错误统一信封** `{kind, code, message, error}` + 原生 `cause` 链；**重试延迟从 `responseHeaders` 读，不再读 `retryMs`**。
- **DTO 改为生成**：手写的 0.5 wire 类型移除，**ABI 1 / ops 1.0 与 manifest 摘要必须与原生库匹配**。
- 录制仍停留在 **schema 2**；schema 3 与传输层回放是后续工作。

### 5. 性能（README，自述 benchmark）

对官方 OpenAI SDK 在同机、同 mock server、同抽象层（HTTP + JSON，无编排）下测得：

| | aimux | OpenAI SDK | 倍数 |
| --- | --- | --- | --- |
| Node.js（单请求） | 0.101 ms | 1.488 ms | 14.7× |
| Python（单请求） | 0.080 ms | 0.595 ms | 7.5× |

持续压测（2000 请求 / 200 KB 上下文 / 50 KB 响应）另有 rps、P99 与 RSS 增长数据，且**明确标注 Node 一列是"与 Vercel AI SDK 比、非同类对比"**（AISDK 每请求还带 Zod 校验、中间件与遥测）。**这些数字均来自 README 自述，我未复现。**

### 6. Provider 覆盖（README 概览）

- **原生协议**：OpenAI、Anthropic、Google、Bedrock、Vertex、Azure、Cohere、Mistral、Groq、DeepSeek、xAI、Anthropic-AWS、Voyage。
- **OpenAI 兼容（registry 驱动）**：Fireworks、Together、Perplexity、OpenRouter、Ollama Cloud、阿里通义、智谱、百度、腾讯、Moonshot、SiliconFlow 等（共 282 个 preset）。
- **本地 / Vertex 托管 preset**：Ollama、vLLM、SGLang、Llama.cpp、LiteLLM Proxy、Vertex 托管的 DeepSeek / Qwen / Llama。
- **语音 / 转写**、**图像 / 视频**、**嵌入 / 重排 / 搜索**各有独立厂商清单（README 分节列出，完整表在 `docs/api/providers.md`）。
- 全文另有 **29 份 RFC**（`rfc/0001`–`0029`）记录各能力的设计，从多语言绑定到缓存追踪、复合模型、录制、Web 控制台。

## 使用前需要注意

- **0.6.0 是破坏性版本，且项目仍在快速收敛 ABI。** 从 0.5 迁移必须**先读 `docs/upgrading-to-0.6.md`**；`ABI 1 / ops 1.0` 与 manifest 摘要**必须与原生库匹配**，绑定与核心版本错配会失败。**接入前应把"跟着大版本改接口"计入成本。**
- **provider 数字有两套口径（description 325 vs README 正文 / 徽章 327）。** README 的拆解是 282 + 45 = 327；**引用时说明该差异与来源**。
- **"327 家"不等于"327 家都同深度支持"。** README 自己区分了"原生协议"、"registry 驱动的 OpenAI 兼容 preset"与"本地 preset"三档，**能力（多模态 / 工具调用 / 转写等）在各 provider 之间并不均等**，需要按 `docs/api/providers.md` 逐项核对。
- **要真正调用仍需自备各家 key。** 只有本地引擎（Ollama / vLLM 等）不需要；**测试可以不联网不要 key（cassette 回放），但那不等于"能免 key 使用服务"**。
- **性能数字与"8 种绑定一致可用"均为自述。** benchmark 明确有一列是**非同口径对比**；转写等新能力在 8 种语言里的"一致"也来自 README 陈述，**我未编译、未运行、未复现**。
- **仓库默认分支是 `master` 而非 `main`**（引用文件路径时须留意）。
- **我只读了 README 与 LICENSE 及元数据**：**未 clone、未装 Rust 工具链、未 `cargo add`、未 npm / pip 安装、未编译、未调用任何 provider、未复现 benchmark**。

## 什么时候会再看

- 要给项目接**多家 LLM 服务商**、又不想为每家写一套调用层时（先看它是否已覆盖你要的那几家，以及多模态需求是否在里面）。
- 想参考"**一个 `dyn` 接口 + 一份 registry JSON + 一条 C ABI**"这种"核心一次、绑定多语言"的架构时。
- 想对照"**接入层（aimux）**"与"**编排层（rig / langchain）**"的职责边界时。
- 想研究"**请求录制 / 离线回放 / 会话归组 / 缓存命中追踪**"这类可观测性内建在接入层里怎么放时。
- 想参考"**在破坏性版本里把变更写清（migration guide + RFC 编号）**"的维护方式时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"多模型"，层级不同**：[cherry-studio：把模型、Agent 与知识库收进一个桌面工作台](cherry-studio-多模型Agent与知识库的桌面工作台.md)——那是**面向用户的桌面客户端**（一个界面里用多家模型），本篇是**面向开发者的接入层**（一个接口调多家模型）。可对照"**多模型产品**"与"**多模型基础设施**"。
- **同属"多模型客户端"，形态不同**：[chatbox：桌面 AI 客户端社区版](chatbox-桌面AI客户端社区版.md) 是**给人用的桌面聊天客户端**，本篇是**给你自己写的程序用的库 / SDK**。
- **都因为"模型放哪、怎么调"而存在，方向相反**：[colibri：把巨型 MoE 模型分层放进 GPU／内存／NVMe 的 C 语言推理引擎](colibri-在普通硬件上跑分层MoE推理的C引擎.md)——colibri 解决"**在本地把大模型跑起来**"，aimux 解决"**把云上各家模型统一起来调**"；两者都与"模型访问"有关，但一个在推理侧、一个在接入侧。
- **都涉及"把多家服务接起来"，产物不同**：[awesome-deepseek-integration：DeepSeek 官方的"实用集成"清单](awesome-deepseek-integration-DeepSeek官方实用集成清单.md) 是**官方文档索引**（列"哪些软件能接 DeepSeek"），本篇是**可安装的代码库**（自己就是接入层）。
- **都在"多 provider"这件事上用力，层不同**：[paseo：编排多个编码 Agent 的桌面与移动开发环境](paseo-编排多个编码Agent的桌面与移动开发环境.md)——paseo 在**Agent 编排层**做多 provider（原生 Claude Code / Codex / OpenCode 等 + ACP 目录），aimux 在**服务接入层**做多 provider（统一各家 HTTP API）。可对照"**编排层的多 provider**"与"**接入层的多 provider**"。

## 参考来源

- 项目仓库与 README（英文 434 行；核验日期 2026-11-13）：https://github.com/arcships/aimux
- 许可证：`LICENSE`（MIT，1,065 字节；核验日期 2026-11-13）
- 相关文件（README 指向，未逐一打开）：`docs/upgrading-to-0.6.md`、`docs/api/providers.md`、`docs/API.md`、`docs/api/reference.md`、`docs/api/rust.md`、`docs/PROJECT-OVERVIEW.md`、`docs/PERF-RESULTS.md`、`docs/error-model.md`、`rfc/0001`–`rfc/0029`、`bindings/README.md`
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 默认分支 / Release 数 / 主语言 / 文件数 / 贡献者）：GitHub 页面内嵌数据与 ungh.cc、shields.io 交叉核对（2026-11-13）

以上信息核验于 2026-11-13。**未 clone、未装 Rust 工具链、未 `cargo add`、未 npm / pip 安装、未编译、未调用任何 provider、未复现 benchmark**；能力、provider 数、性能数字与迁移说明均来自 README，**未逐一验证**。
