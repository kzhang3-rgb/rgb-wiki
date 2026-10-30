# AiToEarn：AI 内容变现与多平台发布

> 记录它的完整变现链路（内容交易市场 + 多平台分发 + 自动互动 + AI 创作），以及把社交账号授权交给第三方带来的问题。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、目录结构与部署文档，未注册账号、未部署、未安装插件）
- 适用范围：官方 SaaS（网页可用）；自部署需 Docker，源码开发需 Node + pnpm + MongoDB + Redis

## 核心结论

一个面向内容创作者的商业化项目，四块能力构成变现链路：

1. **Monetize**：内容交易市场。创作者出售内容完成商家推广任务，按 CPS（成交额）/ CPE（互动量）/ CPM（播放量）三种模式结算。
2. **Publish**：一键分发到 12 个平台（抖音、快手、B站、小红书、视频号、TikTok、YouTube、Facebook、Instagram、Threads、X、Pinterest、LinkedIn），带日历排期。
3. **Engage**：浏览器插件做自动化互动——自动点赞/收藏/关注、AI 生成评论回复、挖掘"求链接""怎么购买"这类高转化信号、品牌讨论监测。
4. **Create**：Agent 调用视频模型（Grok、Veo、Seedance）、图片模型（Nano Banana）批量生成内容，明确面向"矩阵账号运营和大规模内容分发"。

**它是商业服务，不是纯开源项目**：主仓库 MIT 开源，但能力通过官方 SaaS（aitoearn.cn / aitoearn.ai）交付，多数使用方式都要求先注册账号并获取 API Key。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [yikart/AiToEarn](https://github.com/yikart/AiToEarn) |
| 主体 | `yikart` 组织；官网 https://aitoearn.ai/ ，另有中国版 aitoearn.cn |
| 许可证 | MIT |
| 技术栈 | TypeScript、pnpm + Nx 单仓（`aitoearn-backend` 下分 `aitoearn-ai` 与 `aitoearn-server`）、MongoDB、Redis、nginx；桌面端是另一个仓库（`yikart/AttAiToEarn`，Electron） |
| 关注度 | 15,070 Star、2,486 Fork、403 关注（2026-10-30 的 API 数据）；页面显示 12k Star / 2.1k Fork，两处不一致且差距不小 |
| 活跃度 | 2,592 次提交、13 位贡献者、52 个分支、29 个 Tag、26 个 Release（最新 v2.1.0，2026-03-28）；API 显示最近推送 2026-05-15 |
| 仓库体积 | 约 336 MB（API `size` 字段 343,636 KB） |
| 状态 | 持续迭代中，开源已近两年 |

版本节点（README 的"最新动态"）：2025-02-26 首个开源版本（仅国内四平台一键发布）→ 2025-09-16 出海版本 → 2025-11-12 "首个开源且可完全使用"的 v1.3.2 → 2026-02-07 v1.8.0 线下商户推广 → 2026-03-26 v2.1 内容交易市场上线并接入 MCP 与 OpenClaw。

## 五种使用方式

| 方式 | 面向 | 前置条件 |
| --- | --- | --- |
| ① 打开网站直接用 | 普通用户 | 无 |
| ② OpenClaw（龙虾）插件 | Agent 用户 | API Key |
| ③ MCP（Claude / Cursor 等） | Agent 用户 | API Key |
| ④ Docker 自部署 | 团队 | Docker + API Key |
| ⑤ 源码开发 | 开发者 | 开发环境 |

自部署只要三条命令（`git clone` → `docker compose up -d` → 打开 `localhost:8080`），不用手动装数据库。

接入 MCP 的配置很轻：地址 `https://aitoearn.ai/api/unified/mcp`（或 `.cn`），认证头 `x-api-key: 你的API-Key`；也支持 SSE。注意中国版与国际版的 Key 不通用，**环境与 Key 不匹配会返回 401**。

## 使用前需要注意

这部分是本篇最该看的内容：

- **社交账号的发布权限会被交给第三方**。仓库自己解释了为什么需要 Relay：在各平台发布要登录账号，而登录需要各平台的开发者凭据，配好 Relay 后即可**借用官方 aitoearn.ai 的凭据**完成授权，省去自己申请开发者账号。这意味着**即便你自部署，平台授权仍然经由官方服务器中转**——"自部署"并不等于"数据不出你的服务器"。这一点在自己部署时容易被误解。
- **API Key 等同于账号级操作凭据**。README 给的 MCP 配置示例是把 Key **明文写在配置文件里**；配上之后，这个 Agent 就拥有了你所有已授权平台的发布能力。属于本 wiki 规则里"不要提交敏感信息"的同类问题，只是这次是交给第三方服务。
- **自动互动与批量铺量触碰平台规则**。插件的自动点赞/收藏/关注、批量生成、"矩阵账号运营"这些词，指向的是各平台通常明确限制的自动化与营销行为，可能带来限流甚至封号风险。README 对这些平台规则没有任何说明。
- **结算模式的差异要看清**：CPS 按成交、CPE 按互动、CPM 按播放，收益逻辑完全不同，且平台如何核验、如何反作弊文中未提。
- **仓库体积约 336 MB**，克隆成本不低。
- 数据源不一致较明显（Star 15,070 vs 12k、Issue 13 vs 6），按量级看待即可。
- 我未注册、未部署、未安装插件，以上均为文档信息。

## 什么时候会再看

- 想参考**"开源仓库 + 商业 SaaS"这种 open-core 形态的目录怎么组织**时：同一个仓库里放后端单仓、Web 前端、nginx、演示资源，桌面端拆成独立仓库。
- 想参考**把服务包装成 MCP / 插件同时供给多个 Agent 宿主**时（OpenClaw 插件与 MCP 两种接入方式并存，配置只差地址与认证头）。
- 想做**多平台内容分发**的技术调研时——但要把上面的授权链路与平台规则风险一并考虑。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 另一份同样通过 MCP / 技能方式接入 Agent 的项目：[deepseek-harness](deepseek-harness-DeepSeek开源Agent框架.md)——宿主不同，思路相近。

## 参考来源

- 项目仓库与 README：https://github.com/yikart/AiToEarn
- Docker 部署指南：https://github.com/yikart/AiToEarn/blob/main/DOCKER_DEPLOYMENT_CN.md
- 官网（国际版）：https://aitoearn.ai/ ，（中国版）：https://aitoearn.cn/

以上信息核验于 2026-10-30。未注册账号、未部署、未安装插件，功能与结算均未验证。
