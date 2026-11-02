# java-sdk：MCP 官方 Java SDK

> 记录它的技术选型说明——为什么用 Reactor 而不是 CompletableFuture、为什么内置 Jackson 却把鉴权留成空钩子。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README、架构决策章节、Release 与 GitHub API 元数据，未编译、未运行）
- 适用范围：Java 17+；Maven 项目；同时覆盖 MCP 客户端与服务端两种角色

## 核心结论

Model Context Protocol 的**官方 Java SDK**，由 MCP 项目与 **Spring AI 团队协作维护**，同时支持把 Java 应用接成 MCP 客户端与服务端，覆盖同步与异步两种通信模式。

README 里最有价值的部分不是 API 用法（那部分在外部文档站），而是 **"架构与设计决策"一节**：作者明确写了 "Our goal is not to prescribe 'the one true way'"，然后在四个领域逐个交代选了什么、为什么、怎么暴露给使用者。四个决策分别是——JSON 序列化、编程模型、可观测性、远程客户端与服务端。**这一节本质上是一份"Java 生态里做通用 SDK 如何取舍"的公开说明，即使不用这个 SDK 也值得读。**

产出物走标准渠道：Maven Central 的 `io.modelcontextprotocol.sdk:mcp`，另有 `mcp-bom` 统一版本。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [modelcontextprotocol/java-sdk](https://github.com/modelcontextprotocol/java-sdk) |
| 维护方 | MCP 项目 + Spring AI 团队协作维护（团队成员 Christian Tzolov、Dariusz Jędrzejczyk、Daniel Garnier-Moiroux） |
| 许可证 | MIT |
| 语言 / 版本要求 | Java 17+；Maven 构建（含 `mvnw`） |
| 关注度 | 3,760 Star、1,111 Fork、71 关注、303 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2025-01-20 创建，最近推送 2026-11-01（**非常活跃**） |
| 体积 | 约 10.3 MB（`size` 字段，不含 Git 历史） |
| 文档站 | https://java.sdk.modelcontextprotocol.io/latest/ ・ https://modelcontextprotocol.github.io/java-sdk/ |

## 四条设计决策（README 自述）

**1. JSON 序列化：选 Jackson，但藏在抽象后面**

Jackson 是 Java 生态里最通用的选择、性能与注解模型成熟。关键在于**不把它写进公共 API**：公共 API 使用自带的抽象（`mcp-core` 里的 `io.modelcontextprotocol.json` 包），Jackson 只是默认实现（`mcp-json-jackson3`），**可以换成别的 JSON 库**。

**2. 编程模型：公共 API 用 Reactive Streams，Reactor 做实现，另给同步门面**

理由是 MCP 建立在 JSON-RPC 的异步特性之上，且需要支持多请求并发在途、不需要回执的通知、STDIO 管道、SSE 与 Streamable HTTP 这类流式传输——这些要求超出 `CompletableFuture` 这种"单结果 future"能表达的范围。

具体做法是：**公共 API 以 Reactive Streams 接口定义**（Reactive Streams 只定义了 Publisher / Subscriber / Subscription / Processor 四个接口，是 Java 异步非阻塞管线的事实标准），**内部实现用 Project Reactor**（成熟、算子丰富、能通过 context 传递可观测性数据），同时**在响应式核心之上再叠一层同步门面**，让"发个请求阻塞等结果"的简单场景不必被迫写响应式代码。README 提到计划把公共 API 收敛到只暴露 Reactive Streams 接口，以保持"标准接口 + 实用实现"的组合。未来还会受益于 JDK 的虚拟线程与结构化并发。

**3. 可观测性：只出 SLF4J，不带后端**

公共 API 只用 SLF4J 打日志、**不打包任何日志后端**；可观测性元数据（如 correlation ID、追踪状态）经 Reactor Context 在异步边界间传递。SDK 自己**不提供 metrics 与 tracing 实现**，交给 Micrometer、OpenTelemetry 等对接。

**4. 远程客户端与服务端：默认用 JDK 自带件，鉴权只留钩子**

| 领域 | 选择 | 理由 |
| --- | --- | --- |
| 客户端传输 | **JDK HttpClient**（Java 11+，核心模块内置）；Spring AI 2.0+ 另提供 Spring WebClient 传输 | 内置、可移植、支持流式响应，默认零额外依赖 |
| 服务端传输 | **Jakarta Servlet**（核心模块内置）；Spring AI 2.0+ 另提供 WebFlux / WebMVC 传输 | Servlet 是 Java 里部署最广的服务端 API，阻塞与非阻塞模型都能覆盖 |
| 鉴权 | **只提供可插拔钩子，不内置任何实现** | 鉴权需求随环境差异大（Spring Security、MicroProfile JWT、自研方案），内置会造成绑定；钩子挂在服务端传输层 |

作者的定位很清楚：**SDK 不做"唯一正确的方式"，而是提供务实、可互操作、可插拔的参考实现**，技术选型也坦诚说明包含"团队熟悉度"这一因素。

## 模块结构与接入方式

| 模块 | 作用 |
| --- | --- |
| `mcp-bom` | 统一依赖版本 |
| `mcp-core` | 参考实现（STDIO、JDK HttpClient、Servlet），以及 JSON 绑定接口定义 |
| `mcp-json-jackson2` | Jackson 2 的 JSON 绑定实现 |
| `mcp-json-jackson3` | Jackson 3 的 JSON 绑定实现（默认） |
| `mcp` | 便捷包（core + Jackson 3） |
| `mcp-test` | 共享测试工具与集成测试 |

**最小接入只需要依赖 `mcp`**；Spring 项目则用 Spring AI 2.0+ 提供的 `mcp-spring-webflux` / `mcp-spring-webmvc`（组 `org.springframework.ai`）做更深集成。Spring AI 还额外提供基于注解的 MCP 方法处理，以及 OAuth 2.0 / API Key 的鉴权支持——**也就是说"内置鉴权"这件事是由 Spring AI 层补上的，不在这个 SDK 里**。

一个值得注意的工程细节：`mcp-test` 之所以存在，是因为 `mcp-core` 跑完整集成测试需要 JSON 实现，而实现又反过来依赖 `mcp-core`，**不能反向 import 造成循环**，所以集成测试整体挪到 `mcp-test`，默认用 Jackson 3，另给一个 `jackson2` profile 切换：

```bash
./mvnw -pl mcp-test -am -Pjackson2 test
```

## 一致性验证（自述）

SDK 会拿官方的 [MCP conformance test suite](https://github.com/modelcontextprotocol/conformance) 0.1.15 做一致性校验，README 给出的最近结果：

| 套件 | 结果 |
| --- | --- |
| Server | ✅ 40/40 通过（100%） |
| Client | 🟡 3/4 场景、9/10 项检查通过 |
| Auth（Spring） | 🟡 12/14 场景完全通过（98.9% 检查项） |

**服务端满分、客户端与鉴权仍有未通过场景**——这也是 README 自己如实标注的状态，不是"全部通过"。

## 使用前需要注意

- **需要 Java 17+**：不是 Java 8/11 项目可以随便引入的库。
- **跑测试要预装 Docker 与 npx**：`./mvnw test` 依赖这两样（集成测试会拉起真实进程），CI 上也因此有额外前置条件；只想构建则用 `./mvnw clean install -DskipTests`。
- **三条版本线并行**：核验当天（2026-10-16）同时发布了 `v1.1.5`、`v2.1.0`、`v2.2.0-M1`——**1.x 与 2.x 是并行的维护线，其中 2.2.0 还是里程碑预览版**。引入依赖时务必先确认自己要走哪条线，不要看到"最新"就升。
- **编程模型有学习成本**：公共 API 是响应式的，虽然提供了同步门面，但排查问题、自定义算子、处理背压时**仍需要理解 Reactive Streams / Reactor 的一套概念**。团队若没有响应式经验，同步门面是真能省事的路径，但要清楚它只是门面。
- **鉴权得自己接**：SDK 明确不内置鉴权实现，**如果直接暴露 MCP 服务端而不接鉴权钩子，等于把端点和它背后的工具链敞开**。这是本 SDK（以及 MCP 生态整体）最容易被忽视的安全边界。
- **`mcp-core` 默认不带 JSON 实现**：需要 JSON 绑定时要显式引入 `mcp-json-jackson2/3`，或直接依赖便捷包 `mcp`。
- **Spring AI 相关链接指向 2.0-SNAPSHOT**：README 里多处 Spring AI 文档链接是快照版地址，**这类链接会随版本推进失效**，查阅时以 Spring AI 正式文档为准。
- **我只读了 README 与元数据，没有编译、没有跑一致性测试**：API 行为、文档站内容、以及上表的验证结果均未独立复核。

## 什么时候会再看

- 需要**用 Java 把现有系统接成 MCP 客户端或服务端**时：这是官方 SDK，且与 Spring AI 打通，Spring 项目几乎必然走这条。
- 想参考**"通用 SDK 的技术选型如何向使用者交代"**时：JSON、编程模型、可观测性、传输、鉴权五个点各自说明"选了什么 / 为什么 / 怎么暴露 / 留了什么扩展口"，是一份可直接套用的写法样本。
- 想参考**"可插拔而不内置"的边界怎么划**时：鉴权只留钩子、日志不带后端、metrics/tracing 全交给生态，这三个决定背后的取舍讲得很清楚。
- 想参考**多框架传输适配的组织方式**时：核心只带 JDK HttpClient 与 Servlet，把 WebFlux/WebMVC/WebClient 交给 Spring AI 承载，核心保持零额外依赖。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 同属 MCP 官方体系：[servers](servers-MCP官方参考服务器合集.md)——那是官方参考服务器合集（7 个实现，含 TypeScript 与 Python 版本），本篇是 Java 侧的 SDK，两者配合可自建"Java 服务端 + 官方参考服务器对照"。该仓库列出的 10 个官方 SDK 中，Java 就是这一个。
- 同属 AI 助手生态的笔记：[deepseek-harness](deepseek-harness-DeepSeek开源Agent框架.md)（Agent 框架）、[dsh-plugins-store](dsh-plugins-store-DSH插件市场.md)（插件市场）、[openclaw](openclaw-自托管个人AI助手.md)（自托管助手）。
- 涉及 MCP 接入的实践笔记：[AiToEarn](AiToEarn-AI内容变现与多平台发布.md)。

## 参考来源

- 项目仓库与 README：https://github.com/modelcontextprotocol/java-sdk
- SDK 文档站：https://java.sdk.modelcontextprotocol.io/latest/
- Spring AI MCP 文档：https://docs.spring.io/spring-ai/reference/2.0-SNAPSHOT/api/mcp/mcp-overview.html
- 一致性测试套件：https://github.com/modelcontextprotocol/conformance
- 元数据（Star / Fork / 时间 / 许可证）：GitHub REST API 与 Releases API，2026-10-30

以上信息核验于 2026-10-30。未编译、未运行、未执行一致性测试，全部结果指标均为作者自述。
