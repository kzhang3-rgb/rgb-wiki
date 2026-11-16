# travel-roamradar：自托管的个人旅行中心

> 一句话定位（README 自述）：**"A free, open-source, self-hosted personal travel app"**——把**每一次航班、酒店、接送、倒计时、心愿单与去过的旅程**收进一条**持续更新的时间线**，预订信息可**从 Google Calendar 与 Gmail 自动流入**；**整套东西是你自己的**：单个 Cloudflare Worker 跑在**你自己的 Cloudflare 账号**上，数据存在**你自己的 KV 命名空间 `TRIPS`**、挡在**你自己的密码**后面（README 原话："Nobody logs into anyone else's instance; there is no shared server"）。它是**单屏 PWA**（可"加到主屏幕"、可离线），**无框架、无构建步骤**（vanilla JavaScript 前端 + 一个 Worker）；README 还自述这个应用是作者**用 AI agent 从零构建**的（"His AI development agent built the app from scratch"）。最值得记的是三点：① **部署成本极低**（一键 Deploy to Cloudflare 按钮 ≈ 5 分钟、免费额度足够、**部署阶段不需要任何密钥**）；② 自动导入是**可选**的，手工用也完整可用，邮件解析走**你自己的 Anthropic API Key**；③ **许可证是 PolyForm Noncommercial 1.0.0——只能非商业使用**。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（263 行）、`LICENSE` 全文、仓库元数据；**未 clone、未部署、未创建任何 Cloudflare 资源、未登录、未连接 Google / Gmail / Anthropic、未安装到手机主屏幕**）
- 适用范围：**自托管**——用户在**自己的 Cloudflare 账号**上跑**自己的副本**；部署需要 GitHub 与 Cloudflare 账号（免费额度足够），命令行方式需 `git` + `npm i -g wrangler`；客户端是**浏览器 PWA**，在 iPhone / iPad（需 Safari）与 Android（Chrome）上可"加到主屏幕"

## 核心结论

1. **定位**（README 自述）：一个**自我托管的个人旅行中心**——不是 SaaS、**没有共享服务器**，"**每人跑自己的副本、管自己的数据**"，连作者都看不到你的行程（"not even the repo author"）。
2. **技术形态极简**（README 事实）：`travel-app.html` / `public/index.html`（**单文件、vanilla JS 前端**）+ `worker.js`（**一个 Cloudflare Worker**：托管应用、用密码设门槛、跑可选的 Google Calendar + Gmail 导入）+ `wrangler.toml`（Worker 配置：KV 绑定、**每天一次全量同步（18:00 UTC）**、**每小时一次仅邮件的检查**）。数据模型只有一条：`Trip: { id, from, to, start, end, label, notes, segments[] }`。**不用框架、不用构建步骤。**
3. **功能面**（README 自述）：按行程挂**航班 / 酒店 / 接送 / 自由文本备注**（**手工添加的计划永不被同步覆盖或删除**）；**日历总览（3 / 6 / 12 个月）**、**按年统计在外天数**、**"去过"世界地图**（每国带**到访次数**）；**Upcoming / Past / Wishlist** 三个标签页与"fare-watch 心愿单"；每段行程自动补**天气、货币（对 € 与 $ 实时汇率）、插座类型与电压、当地紧急电话、目的地照片**。
4. **数据主权与隐私**（README 自述）：行程在**你的 KV**、密钥在**你的 Worker**、挡在**你的密码**之后；**唯一的例外**是一个**默认关闭**的匿名安装计数 ping（首次设置密码时，可选地发一个 `{ "id": "<随机 128 位数字>" }`，不含任何其它数据；`TELEMETRY_URL` 在 `wrangler.toml` 中**默认为空**，即"零网络调用"）。
5. **自动导入（可选）**（README 自述）：**Google Calendar + Gmail** 需在 `console.cloud.google.com` 做一次性设置（建项目、开 Calendar / Gmail API、建 OAuth client），之后在**应用内 Settings → Connect Google**；**邮件 → 行程解析走 Claude**（粘贴**自己的 Anthropic API Key**），把 Booking.com / Airbnb / 航司 / Uber 等确认邮件解析成计划（含门锁 PIN、航班号与时刻、接送信息），**每小时后台检查**即使应用关闭也会归档；还可把任意确认邮件转发到**带 `+trip` 的地址**（如 `you+trip@yourdomain.com`），一小时内归档，**无人匹配时"酒店入住或返程票会自动创建行程"**；**Calendly** 作为 Worker secret 支持；**所有凭证也可用 `wrangler secret put` 设置，且 dashboard secret 永远优先于应用内副本**。
6. **许可证**（`LICENSE` 事实）：**PolyForm Noncommercial 1.0.0**——可自由运行、复制、修改、自托管，**仅限非商业目的**（个人使用、兴趣项目、学习）；**不得出售、不得对访问收费、不得商用**；无任何担保。

**个人判断（未验证）**：它的吸引力是"**5 分钟、免费、数据全在自己账号里**"这件事本身——比功能更像一个**可抄的自托管范式**（单 Worker + 单 KV + PWA + 一键部署）。但要**真正用起来**，代价是**把 Gmail 与 Google Calendar 的 OAuth 权限交给自己的 Worker、并把邮件内容交给 Anthropic 解析**，这里存在无法回避的隐私权衡（README 的免责口径是"数据只在你账号里"，但**邮件解析这一步确实离开了你的账号**）。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [giovannibrees/travel-roamradar](https://github.com/giovannibrees/travel-roamradar) |
| 描述 | "A free, open-source, self-hosted personal travel app... Runs as a single Cloudflare Worker on your own account. Built with AI agents by Giovanni Brees." |
| 许可证 | **PolyForm Noncommercial 1.0.0**（`LICENSE` 正文的 `Software` 段与 `Required Notice` 仍引用**旧仓库名** `giovannibrees/travelapp`；shields 报 `not identifiable by github`）；README 明确"free to run, modify, and self-host for any **noncommercial** purpose... You may not sell it or use it commercially" |
| 主语言 | **vanilla JavaScript**（无框架、无构建步骤）+ Cloudflare Worker；存储为单 KV 命名空间 `TRIPS` |
| 关注度 | **34 Star / 2 Fork / 0 Watcher**（**页面值**，2026-11-13）；开放 Issue **0**、贡献者 **1**（同日） |
| 维护状态 | **2026-07-24 创建**；**页面 `updatedAt` 2026-11-06，但 ungh `pushedAt` = 2026-07-24（即创建日，其后无代码推送）**——页面更新多为徽章/描述刷新；默认分支 `main`；**0 Release** |
| 体积 | `main` 分支 **35 个文件**（ungh files API，2026-11-13） |
| 部署 | 一键 **Deploy to Cloudflare** 按钮（把仓库复制进**你的** GitHub、在**你的** Cloudflare 建 Worker、**自动创建所需的 KV namespace**）；或命令行 `git clone` → `npm i -g wrangler` → `wrangler login` → `wrangler kv namespace create TRIPS`（把 id 贴进 `wrangler.toml`）→ `wrangler deploy` |
| 作者 | Giovanni Brees（<https://www.giovannibrees.com>）；播客 *The Zero-Employee Company* |

## 能力结构

### 1. 行程与计划（README）

- **Per-trip plans**：给任意行程挂航班、酒店、接送与自由文本备注；**手工计划是"神圣的"**——任何自动同步都不会改动或删除你自己加的计划。
- **手工添加或自动填充**：几秒手打一条，或连上 Google 后让预订自己流进来。
- **Upcoming / Past / Wishlist** 三标签 + 针对还在幻想的行程的 **fare-watch 心愿单**。

### 2. 规划与总览（README）

- **日历总览**：3 / 6 / 12 个月视图，标出每一个在外的日子。
- **按年统计在外天数**：按自然年（1 月 1 日–12 月 31 日）统计，点某年可下钻逐行程明细。
- **"去过"世界地图**：每到一个国家就点亮，**每国带到访次数**（含小岛国）。

### 3. 逐行程的目的地情报（README，自动填充）

天气（行程日期内的预报，太远则给典型气候）· 货币（对 **€ 与 $ 实时汇率**）· 插座类型与电压 · 当地紧急电话 · 目的地**真实照片**。

### 4. 手机端与"只属于你"（README）

- **分享单个行程**：一键复制只读私有链接，同行者能看到实时行程（**连酒店门锁密码都在内**），**无需登录**。
- **年度回顾（Year wrapped）**：把分享卡翻到这一年——行程、在外天数、国家数、距离。
- **加到任何日历**：一键导出 `.ics`（Apple / Google / Outlook），另有**私有 subscribe-by-URL 订阅源**持续更新。
- **每周快照**：每周日自动全量备份（保留 4 周，另外每次破坏性操作前再存一份），一键恢复。
- **像原生 App 一样安装**：iPhone / Android"加到主屏幕"，全屏、可离线（**PWA**）。
- **你自己的密码**：设一次，**每台设备保持登录 30 天**。
- **备份与导入**：把所有行程导出为 JSON，随时重新导入。

### 5. 部署流程（README）

| 步骤 | 说明 |
| --- | --- |
| 1. 点按钮 | 一键 Deploy to Cloudflare；完成后得到 `https://travel-roamradar.<you>.workers.dev` |
| 2. 打开并设密码 | **第一个**登录的人设邮箱 + 密码——所以**要立刻做**（邮箱随便填，只是登录名） |
| 3. 开始加行程 | 填个日期，天气 / 货币 / 地图 / 日历会自动补齐 |
| 4. 加到主屏幕 | iOS 必须用 **Safari**（分享 → 加到主屏幕）；Android 用 **Chrome**（⋮ → 加到主屏幕 / 安装应用） |

README 还列了常见报错"An unknown error occurred"的三个原因：**同名残留仓库**、**Cloudflare GitHub 应用未授权**、**新 Cloudflare 账号尚未注册 `workers.dev` 子域**。

## 使用前需要注意

- **许可证是硬约束**：**PolyForm Noncommercial 1.0.0**——可自托管自用，**但不得出售、不得对访问收费、不得用于商业目的**。想商用须另行联系作者。
- **`LICENSE` 与仓库名不一致**：`LICENSE` 里 `Software` 段与 `Required Notice` 指向**旧仓库名** `giovannibrees/travelapp`。这不影响许可效力，但**说明仓库改过名而许可文本未同步**，引用时应注明。
- **仓库处于"创建后未再推送"状态**：ungh `pushedAt` = **创建日 2026-07-24**，**0 Release**、贡献者 1、开放 Issue 0。**页面 `updatedAt` 2026-11-06 与代码推送时间是两回事**（多为徽章/描述刷新）。这意味着它更像**一次成型的作品**，而非活跃迭代的项目，**长期维护与安全修复没有保障**。
- **README 自述由 AI agent 构建**：作者明说应用是"AI 开发 agent 从零构建、Claude Fable 与 Codex 审阅代码、Claude 做视觉设计"。**代码质量、安全性与可持续维护性均未经验证**，自托管前应当自己审计（尤其鉴权与 KV 读写）。
- **隐私权衡要点明**：行程与密钥确实只在你账号里，但**邮件→行程解析会把确认邮件内容发给你自己的 Anthropic API Key 对应的 Claude 服务**——这一步**离开了你的账号**；接通 Google 还会把 **Calendar + Gmail 的 OAuth 权限**授予你的 Worker。
- **鉴权很"薄"**：所有访问共用**一个密码**（第一个登录者设定），README 未描述多用户、2FA 或密码强度策略；分享链接是**读过即见**的只读链接（含门锁密码）。
- **依赖外部平台与账号**：Cloudflare（Worker + KV，免费额度足够但**受其服务条款与限额约束**）、Google（Calendar / Gmail API）、Anthropic（邮件解析）、可选 Calendly；**任一环节的条款或 API 变化都可能影响功能**。
- **安装 / 部署条件**：一键部署需要**你自己的 GitHub 与 Cloudflare 账号**；命令行方式需 `wrangler`。README 提醒部署阶段**不要填任何变量、Google 与 Claude 都在应用内连接**。
- **我只读了 README 与 `LICENSE`**：**未 clone、未部署、未创建任何 Cloudflare 资源、未连接 Google / Gmail / Anthropic、未验证任何功能**；上述能力均来自文档自述。

## 什么时候会再看

- 想快速自建一个 **"数据只在自己账号里"的个人小 App**，并参考"**单 Worker + 单 KV + PWA + 一键部署**"这种低成本范式时。
- 想做**旅行时间线**（航班 / 酒店 / 接送 / 去过地图 / 年度统计）并接受"非商业自用"时。
- 想研究"**从 Gmail / Calendar 反向生成结构化行程**"这条链路（含 `+trip` 转发地址、地理感知归档、门锁 PIN 抽取）时。
- 想参考"**遥测默认关闭、并把那唯一的 ping 写清楚**"这种隐私声明写法时。
- 想对照**其它自托管 / 本地优先个人应用**的做法时（见下方相关笔记）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同样"一键部署到 Cloudflare Workers"**：[edgeKey：零成本部署到 Cloudflare Workers 的开源发卡商城](edgeKey-零成本部署到CloudflareWorkers的开源发卡商城.md)——那篇是**发卡商城**（有支付/订单），本篇是**个人旅行中心**（单 KV、无支付）；两篇可对照"**同一平台上的两种自托管形态**"。
- **同属"自托管的个人应用"**：[openclaw：自托管个人 AI 助手](openclaw-自托管个人AI助手.md)——那篇是**本地 Gateway + 可替换模型/执行器**的助手，偏"自托管基础设施"；本篇是**在云平台上的单 Worker 小应用**，偏"自托管个人工具"。
- **同属"本地优先 / 数据归自己"**：[wealthfolio：本地优先个人财务追踪器](wealthfolio-本地优先个人财务追踪器.md)——那篇把个人财务数据留在本机（桌面应用）；本篇把你的行程数据留在**你自己账号的云 KV**里。可对照"**本地优先**"与"**自托管云**"两种数据主权取向，以及各自的隐私边界。

## 参考来源

- 仓库主页与 README（263 行；核验日期 2026-11-13）：<https://github.com/giovannibrees/travel-roamradar>
- 许可证：`LICENSE`（PolyForm Noncommercial 1.0.0，150 行；`Software` 段与 `Required Notice` 引用旧仓库名 `giovannibrees/travelapp`）
- 元数据（Star / Fork / Watcher / 创建与页面更新时间 / 默认分支 / Release 数 / 贡献者与开放 Issue）：GitHub 页面内嵌数据与 ungh.cc（2026-11-13，**页面 `updatedAt` 与 ungh `pushedAt` 差异明显**）；`main` 分支 35 个文件取自 ungh files API（同日）
- 作者：Giovanni Brees <https://www.giovannibrees.com> · LinkedIn <https://www.linkedin.com/in/giovannibrees/>

以上信息核验于 2026-11-13。**仅阅读 README、`LICENSE`、仓库文件树与元数据；未 clone、未部署、未创建任何 Cloudflare 资源、未登录、未连接 Google / Gmail / Anthropic、未安装到手机主屏幕。** 能力描述均来自文档自述。
