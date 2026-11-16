# edgeKey：一键部署到 Cloudflare Workers 的开源发卡（卡密）商城

> 它最值得记的不是"又一个发卡站"，而是**把一整套全栈电商压进了 Cloudflare Workers + D1 的免费额度里**：Vike 做 SSR 前端、Hono/telefunc 做后端入口、Prisma 接 D1，同一仓同一份代码，靠一个 `Deploy to Cloudflare` 按钮起站。**但先记住两件事**：作者已明确把主线转到新项目 **CFFK**、EdgeKey 只保留维护；而**默认管理员账号 `admin / admin123456` 是公开写在 README 里的**——上线前先改密、先配 `AUTH_SECRET`。

- 状态：草稿
- 最近核验：2026-11-12（阅读中英 README 全文、`LICENSE` 首行、`package.json`、`main` 分支完整文件树与元数据；**未 clone、未 `bun install`、未本地跑、未部署、未接任何支付/邮件/S3 服务、未审计源码、未跑任何测试**）
- 适用范围：**部署到 Cloudflare Workers + D1**（一键 / Git 自动 / 手动三种方式）；本地开发推荐 **Bun**（也可换 npm/pnpm/yarn）；面向个人或小团队自建**数字商品（卡密）**售卖站

## 核心结论

**一句话定位（README 自述）**：基于 **Vike 框架**开发、可直接部署到 **Cloudflare** 的**一体化全栈卡密商城系统**——同一套代码同时包含前端页面、SSR 渲染、后端 API / 数据变更入口，并由 **Cloudflare Workers** 运行。

**它主打的三件事：**

1. **零服务器、零成本起站**：不用买服务器和域名，跑在 Cloudflare 全球边缘网络，一键部署；运维靠 **Workers + D1** 的免费额度。
2. **数字商品（卡密）交易闭环**：分类 / 商品 → 下单 → 支付 → 自动发卡 → 订单管理与邮件通知。
3. **多支付网关 + 多邮件通道**，且**插件式可扩展**。

**三个最值得记的点：**

1. **"零服务器全栈"是可抄的具体样本**：把传统 Node 全栈（SSR 框架 + 服务端入口 + ORM）拆成能跑在 Workers 上的组合（Vike + vike-photon + Hono + Prisma D1 适配器）；`prisma/migrations/` 里 9 个迁移能直接看出这个站是怎么一步步长出来的（媒体库、发货类型、折扣码、后台两步验证、页头页尾代码、快递发货、站点时区、物理库存）。
2. **一键部署体验做得很满**：`Deploy to Cloudflare` 向导会**在你的 Git 账号里自动建一个仓库**、自动新建并初始化 D1 库（含管理员账号与种子数据），部署完即得 `edgekey.<你的账号>.workers.dev`。
3. **⚠️ 它已进入维护模式**（这是"要不要现在用它"的关键前提）：README 明确说作者另起新项目 **CFFK**，用全新技术栈重做且"拥有 EdgeKey 的所有功能"；**EdgeKey 之后转为轻量化版本，只做 Bug 修复与稳定性优化，不再进行大型功能迭代**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [34892002/edgeKey](https://github.com/34892002/edgeKey) |
| 描述 | 完全开源的发卡系统，一键部署到 Cloudflare Workers。\| A Vike-powered full-stack digital shop, one-click deploy to Cloudflare Workers. |
| 归属 | 账号 `34892002`（个人；页面 `isOrg: false`、`isFork: false`、`isArchived: false`、`isTemplate: false`） |
| 许可证 | **MIT**（`LICENSE` 首行 `MIT License`，署名 `Copyright (c) 2026 ggyy`） |
| 主语言 | **TypeScript 51.7%**（shields.io `languages/top`） |
| 技术栈 | **Vike**（SSR 全栈框架）+ `vike-vue` + `vike-photon` + `@photonjs/cloudflare` / `@photonjs/hono`（Workers 服务端入口），**Hono**、**Vue 3**、**TypeScript**；数据层 **Prisma + `@prisma/adapter-d1`（Cloudflare D1）**；RPC 用 **telefunc**；认证 `@auth/core` + `bcryptjs` + TOTP（两步验证）；富文本 **TipTap**；样式 Tailwind + daisyUI；S3 用 `aws4fetch`；Workers 内邮件用 `worker-mailer`；构建/部署 **Vite + Bun + Wrangler** |
| 关注度 | **292 Star / 130 Fork / 0 关注（Watch） / 1 开放 Issue**（Star/Watch/Fork 页面值与 ungh.cc 镜像一致，2026-11-12；Issue 数取自 shields.io） |
| 维护状态 | **2026-04-21 创建**；ungh 报 pushed **2026-08-17**、页面 `updatedAt` 2026-11-08（**距核验日约 3 个月无 push**，与"进入维护模式"自述相符） |
| 默认分支 | `main` |
| 版本 | **0 Release / 0 tags**（无正式发布记录）；`package.json` 中版本号为 **`1.5.4`** |
| 话题标签 | `cloudflare-workers` · `faka` · `free` · `full-stack` |
| 仓库体积 | `main` 分支文件树约 **268 个文件**；`size`（KB）**未取到**（GitHub API 匿名配额用尽） |
| 贡献者数 | 页面显示存在贡献者模块，但**具体人数未取到**（未核验） |

## 功能特性（README 原列，转述）

| 能力 | 说明 |
| --- | --- |
| 零成本运行 | 基于 Cloudflare Workers + D1，作者称免费额度足够日常运营；在"自建支付 + 个人邮箱 SMTP + 免费图床"的理想条件下，README 声称可实现 **100% 零成本**运营 |
| 商品管理 | 分类、商品上下架、库存模式（有限 / 无限）、最小 / 最大购买数量 |
| 卡密管理 | 批量导入卡密，**支付后自动发货**，支持库存实时预警 |
| 订单管理 | 订单列表、手动补发、自动关闭过期订单、详细支付日志 |
| 多支付网关 | 内置 **BEpusdt（USDT）**、**Epay（聚合支付）**，并称可"插件式"扩展更多接口 |
| 邮件通知 | 支持 **SMTP / API / Cloudflare Email** 三种通道，含邮件发送日志 |
| 站点设置 | 站点名称、Logo、公告、客服联系方式 |
| 管理后台 | 管理员账号体系（含登录验证码、两步验证等，见下文安全小节） |
| 本地查单 | 前端有 `pages/query` 与本地订单缓存（`lib/local-orders.ts`），支持"同步本地订单" |

## 部署方式（README 原表 + 步骤）

| 方式 | 适合场景 | README 标注的便捷程度 |
| --- | --- | --- |
| **一键部署**（推荐） | 首次部署，无需本地环境 | 最简单，点按钮全自动完成 |
| **Git 自动部署** | 持续迭代，代码推送自动更新 | 配置一次后全自动 |
| **手动部署** | 二开需求，细节掌控 | 需要本地环境与命令行操作 |

**一键部署要点**：点击 `Deploy to Cloudflare` 按钮后，向导会**登录并授权你的 Git 账号、自动在该账号下创建一个新仓库**，自动新建并初始化 D1 数据库（管理员账号等），完成后给出 `https://edgekey.<你的账号>.workers.dev`，后台入口 `/admin`。README 提醒**在向导中修改默认密钥 `AUTH_SECRET`**。

**手动部署要点**：`wrangler login` → `wrangler d1 create edgekey-db` → 把 `database_id` 填进 `wrangler.jsonc` → `bun run db:migrations:remote` → `bun run db:seed:remote` → `wrangler secret put AUTH_SECRET` → `bun run db:generate` → `bun run up`（= `vike build` + `wrangler deploy`）。

**本地开发**：推荐 **Bun**：`bun install` → `bun run db:generate` → `bun run db:migrations:local` → `bun run db:seed` → 准备 `env` 文件 → `bun run dev`。（README 原文把示例文件写成 `env.example` / 复制到 `env`，而仓库里实际是 **`.env.example`**，属文档笔误。）

## 支付网关与"零成本"依赖

- **支付**：README 为 **BEpusdt（USDT）**、**易支付（Epay）**、**支付宝**、**支付宝当面付**、**Stripe**、**HashPay** 六个网关分别提供了 `docs/pay/*/start.md` 文档；服务端也有对应的 `server/routes/payment-*.ts` 回调路由。
- **邮件**：SMTP / API / Cloudflare Email 三通道；README 推荐 **resend**（免费用户每日 100 封）。
- **存储 / 图床**：README 推荐 **Backblaze B2**（免费 10GB）作 S3、91 星空图床（免费 1G）。
- **README 的"零成本"原话口径**：把上面的**支付渠道、个人邮箱 SMTP、免费图床**都配成"免费/自建"状态时，才可能达到 **100% 零成本**——**这是一个有前提的理想态，不是默认开箱即免**。

## 安全与账号（README 自述）

- **生产必须配置 `AUTH_SECRET`**：README 明说未配置会抛异常并**禁止管理员登录**（`wrangler secret put AUTH_SECRET`）。
- **默认管理员账号为 `admin / admin123456`**，README 反复强调"首次登录后立即修改密码"。
- **忘记密码**：在 Cloudflare Dashboard 的 D1 Console 执行 SQL，把管理员密码重置回 `admin123456`（README 给了具体哈希）。
- **忘记两步验证**：在同一处用 SQL 把 `twoFactorEnabled` 等字段清空，之后重新绑定验证器。
- **登录验证码**：支持接入 **Cloudflare Turnstile**（`TURNSTILE_SITE_KEY` / `TURNSTILE_SECRET_KEY`）；**两个都未配置则默认关闭**，只配一个会被视为未启用（避免半配置状态）。

## 使用前需要注意

- **⚠️ 项目已进入维护模式，新功能去了另一个仓库**：README 明说因现有技术框架与 UI 组件体系限制，作者另起 **CFFK** 新项目（全新技术栈、拥有 EdgeKey 全部功能），**EdgeKey 只做 Bug 修复与稳定性优化、不再做大型功能迭代**。**要新部署或做二开，应先去看 CFFK 是否更合适**；引用时不要把它当成"持续演进"的项目。ungh 报最近 push 为 **2026-08-17（约 3 个月前）**，与该自述吻合。
- **⚠️ 默认口令公开、恢复方式也是把它重置回默认值**：`admin / admin123456` 直接写在 README 里，而"忘记密码"的正规恢复手段就是**把密码重置回同一个弱口令**。部署后若忘记改密，等于后台没有任何保护——**这条是自托管发卡站最容易被扫到的口子**。好消息是：生产未配 `AUTH_SECRET` 时会**禁止登录**（一道闸门）。
- **⚠️ 密钥与凭据面要自己守好**：`AUTH_SECRET` 用于会话签名；支付网关密钥、SMTP/邮件密钥、S3 密钥都存在部署配置里（**README 未说明这些密钥是否加密存储，我也未核验**）。Cloudflare 账号 + D1 数据库 + Workers 密钥即等价于"整店的控制权"，权限与备份要自己管。
- **⚠️ "零成本"是免费额度拼出来的，会变**：它依赖 Cloudflare Workers/D1 免费额度、resend 每日 100 封、Backblaze 10GB、第三方免费图床等多个外部免费服务。**任何一项改政策、超额或停服，"零成本"就不再成立**，README 的"100% 零成本"是有前置条件的理想态。
- **⚠️ 发卡 / 卡密类站点本身是高敏感品类**：项目内置 **USDT（BEpusdt / HashPay）、聚合支付（Epay）** 等通道，这类"自助售卡"业务在国内属**监管与合规高敏感**领域，支付通道稳定性、内容合规、账号风险都需**自行评估**（我未核验任何合规性，也不对此背书）。
- **⚠️ 一键部署会改你的 Git 账号，且与手动部署有配置冲突**：向导会**在你的 Git 账号里新建仓库**；README 也自述一键部署与手动部署在 `wrangler.jsonc` 上存在冲突（`database_id`），`npm run up` 与 `npm run db:*` 等命令需要先手动填好 `database_id`，否则报错。
- **⚠️ 无 Release、无 tag，版本只能靠 commit / CHANGELOG**：**0 Release / 0 tags**，源码里的版本号只在 `package.json`（`1.5.4`）。**没有正式发布点 = 回滚与"我该用哪个版本"都要自己读提交历史**。
- **⚠️ 依赖若干第三方开源组件**：README 鸣谢并依赖 **BEpusdt**、**HashPay**、**worker-mailer** 等外部项目；这些上游项目的维护状态与许可**我未核验**，是长期运行的实际依赖面。
- **⚠️ 我未做任何验证**：**未 clone、未安装、未本地运行、未部署到 Cloudflare、未接任何支付 / 邮件 / S3 服务、未审计源码、未跑测试**；上述架构、功能、部署步骤、支付网关清单**均来自 README 与仓库文件自述**。GitHub API 匿名配额用尽，**`size` 与贡献者数未取到**。

## 什么时候会再看

- 想找"**把整站全栈（SSR 页面 + 服务端 API + 数据层）塞进 Cloudflare Workers + D1 免费额度**"的现成工程模板（Vike + Hono + Prisma D1 适配器 + telefunc）。
- 想搭一个卖**数字商品 / 卡密**的小店，要"**下单 → 支付 → 自动发卡 → 邮件通知**"闭环，又不想买服务器。
- 想参考"**多支付网关适配 + 回调路由 + 支付日志**"的插件式写法（支付宝当面付 / 易支付 / USDT 网关 / Stripe）。
- 作为"**发卡 / 卡密类自助站点**"的工程样本，评估其**合规、支付通道与账号安全风险**。
- 想复盘"**作者放弃旧栈、另起 CFFK 重做**"这类**项目生命周期决策**（旧项目何时该进维护模式）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同属 Cloudflare 生态，一个讲"接进来"、一个讲"整站跑在上面"**：[cloudflared：Cloudflare Tunnel 客户端](cloudflared-Cloudflare隧道客户端.md)——那篇解决"不用在防火墙上开洞就能对外提供服务"，把**源站**接进 Cloudflare 网络；本篇则是**整站（SSR + API + 数据库）本就跑在 Cloudflare Workers + D1 上**，连服务器和隧道都不需要。两篇合看，正好覆盖"用 Cloudflare"的**自建源站接入**与**全托管边缘应用**两种形态。
- **都关乎"把东西卖出去 / 变现"，但对象不同**：[AiToEarn：AI 内容变现与多平台发布](AiToEarn-AI内容变现与多平台发布.md)——那篇是**内容创作者的变现链路**（内容交易市场 + 多平台分发），面向"内容"；本篇是**数字商品（卡密）的自助售卖站**，面向"商品 / 卡密"。若关注"个人开发者怎么把产出变成收入"，两篇是不同路径的样本。
- **同为"部署到 Cloudflare 的个人自托管小项目"**：[travel-roamradar：自托管的个人旅行中心](travel-roamradar-自托管的个人旅行中心.md)——那篇是**单 Worker + 单 KV 命名空间 `TRIPS` + PWA** 的旅行时间线（**PolyForm Noncommercial 1.0.0，且创建后未再推送**）；本篇是**卡密发卡商城**（MIT）。可对照同一平台上**两种小项目的许可与维护状态**——本篇 MIT 可商用，那篇只能自用。
- **同样把部署压到 Cloudflare 免费档，复杂度不同**：[talorys：部署在自己 Cloudflare 账号里的个人 AI 助手](talorys-部署在自己Cloudflare账号里的个人AI助手.md)——本篇用 **Workers + D1** 搭**发卡商城**（同一套代码含前端 SSR + 服务端 API）；talorys 用 **Pages + Workers + Durable Objects（SQLite）+ Workers AI** 做**个人助手**，可对照同一平台上"**轻后端应用**"与"**带持久状态与定时任务的助手**"。
- **都涉及"发卡 / 卡网"这一层生态，站位相反**：[PriceAI：AI 订阅卡网渠道比价工具](PriceAI-AI订阅卡网渠道比价工具.md)——本篇是**自己搭一个发卡商城**（卖方工具，下单 → 支付 → 自动发卡）；PriceAI 是**购买前的比价雷达**、顺带聚合卡网渠道（买方看价格），可对照"**卖方的工具**"与"**买方看价格**"。

## 参考来源

- 项目仓库与中文 README：https://github.com/34892002/edgeKey
- 英文 README：https://github.com/34892002/edgeKey/blob/main/README-en.md
- 许可（MIT）：https://github.com/34892002/edgeKey/blob/main/LICENSE
- 一键部署教程：https://github.com/34892002/edgeKey/blob/main/docs/fast_deploy/start.md
- 更新日志：https://github.com/34892002/edgeKey/blob/main/CHANGELOG.md
- 支付网关文档（BEpusdt / 易支付 / 支付宝 / 当面付 / Stripe / HashPay）：见仓库 `docs/pay/*/start.md`
- 元数据（Star / Fork / Watch / 时间 / 语言 / 默认分支 / Release 与 tag / 文件树）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-12
- Issue 数、主语言占比：shields.io 徽章接口，2026-11-12

以上信息核验于 2026-11-12，Star 数与仓库内容可能变化。**本次只做了静态阅读：读了中英 README 全文、`LICENSE` 首行、`package.json`、`main` 分支完整文件树，以及 GitHub 页面内嵌元数据、ungh.cc 镜像与 shields.io 徽章；未 clone、未安装、未本地运行、未部署到 Cloudflare、未接任何支付 / 邮件 / S3 服务、未审计源码、未跑测试。** 架构与功能描述、部署步骤、支付网关清单、安全说明**均来自 README 与仓库文件自述**，未经运行验证；`size` 与贡献者数为**未取到/未核验**。**发卡 / 卡密类站点涉及支付与合规风险，部署与经营前请自行评估。**
