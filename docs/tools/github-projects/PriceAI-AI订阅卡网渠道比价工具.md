# PriceAI：AI 订阅卡网渠道比价工具

> 一句话定位（README 自述）：**"以卡网订阅和中转 API 为核心，以官方订阅和官方 API 为价格基准"** 的 **AI 获取成本比价雷达**——聚合**订阅卡网、官方订阅 / 官方 API、API 中转站**四个模块，展示**有货最低价、库存状态、原站购买链接**与**中转倍率 / 来源披露**，并提供**API 中转模型检测**证据报告。最值得记的三点：① 它**不卖货、不收款、不做担保**，只做**购买前决策工具**（README「不做什么」）；② **许可证不是开源**——采用自定的 **`PriceAI Source Available License 1.0`（"源码可见"）**，**禁止商业托管与竞争性公共服务**；③ **"PriceAI" 名称、Logo、域名、生产数据、价格快照与指南内容不随源码授权**，另有 `DATA_LICENSE.md` 与 `TRADEMARKS.md` 约束。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文（298 行）、`LICENSE`（5,451 字符，`PriceAI Source Available License 1.0`）与已核实元数据；**未 clone、未安装、未运行、未部署、未配置 Supabase、未访问采集器 / 检测服务、未打开 priceai.cc 线上站**）
- 适用范围：**Next.js 应用**（`npm install` / `npm run dev`，前台 `localhost:3000`、后台 `/admin`）；数据层用 **Supabase**，**未配置时前台用内置演示数据**；采集与部署走 `npm run` 系列脚本（`collect:prices` / `collect:browser` / `deploy:production`）；**Key 检测能力由独立服务提供**（另仓）

## 核心结论

1. **它是"购买前比价雷达"，不是商店**（README 自述）：不卖货、不收款、不参与交易、**不做担保**，只整理**公开或审核通过**的价格 / 来源 / 库存 / 倍率 / 更新时间和**原站链接**；最终仍要**回原站核验并自行决策**。
2. **四模块口径严格分开**（README「核心模块」）：**官方订阅**（做价格基准）、**卡网订阅**（现货低价）、**官方 API**、**中转 API**（必须额外关注倍率、稳定性与上游披露）——README 反复强调**不把第三方渠道包装成官方渠道**。
3. **有货最低价优先**：缺货、隐藏、未审核或异常报价**不参与外层最低价**；中转站展示**充值系数 / 模型倍率 / 综合倍率 / 近 7 日可用性 / 来源披露 / 风险标签**。
4. **许可证是"源码可见"而非开源**：`LICENSE` 开宗明义"This license is a source-available license. It is **not** an open source license as defined by the Open Source Initiative"，**限制商业托管与竞争性公共服务**；商业授权须**先经 Telegram 取得书面许可**（<https://t.me/dimthink>）。

**个人判断（未验证）**：它的**核心资产是数据与口径**（渠道清单、价格快照、分类规则、指南），而**恰是这些数据 / 品牌被许可证排除在源码授权之外**。所以"**读代码、本地自用、内部非商业使用**"是被允许的，但**想照着做一个公开的同类比价站是不允许的**——用之前先把许可边界读清。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [dimthink/PriceAI](https://github.com/dimthink/PriceAI) |
| 描述（原文） | "AI 订阅卡网渠道比价工具：聚合 100+ 卡网渠道包含 ChatGPT、Claude、Gemini、Grok 等多渠道报价，展示有货最低价、库存状态和原站购买链接。" |
| 归属 | 个人 / 团队账号 **dimthink**（`LICENSE` 版权人写 "PriceAI contributors"） |
| 许可证 | **`PriceAI Source Available License 1.0`**（`LICENSE` 5,451 字符；**非 OSI 开源**）；shields 的 license 字段显示 **"not identifiable by github"**；品牌 / 数据 / 内容另受 `DATA_LICENSE.md` 与 `TRADEMARKS.md` 约束 |
| 技术栈 | **Next.js**（前台 + `/admin` 后台）；数据层 **Supabase**（未配置时用内置演示数据）；采集器 / 部署为 `npm run` 脚本 |
| 关注度 | **Star 5,397 / Watcher 6 / Fork 247**（**ungh 值**）；**页面值 5,420 / 6 / 248**（2026-11-13，**两口径略有差异**）；shields 另报**开放 Issue 2、contributors 2**（同日） |
| Release | ungh **5** / 页面 **5**（两口径一致） |
| 维护状态 | **2026-05-08 创建**；ungh `pushedAt` **2026-09-28**、页面 `updatedAt` **2026-11-13**；默认分支 **`main`** |
| 线上站 | <https://priceai.cc>（另有官方订阅 <https://priceai.cc/official-prices>、卡网订阅 `/channels`、官方 API `/official-api`、中转 API `/api-transit`、模型检测 `/api-transit/detector`） |
| 检测服务（另仓） | [physics-dimension/priceai-detector-service](https://github.com/physics-dimension/priceai-detector-service)——fork 自 [canarybyte/veridrop](https://github.com/canarybyte/veridrop)，**继续采用 AGPL-3.0-or-later**，主站通过 `NEXT_PUBLIC_TRANSIT_DETECTOR_API_BASE_URL` 调用 |

## 功能与用法

### 1. 五个模块（README「核心模块」）

| 模块 | 解决的问题 | 入口 |
| --- | --- | --- |
| **官方订阅** | 对比 ChatGPT Plus / Pro / Team、Claude Pro、Gemini Advanced、Grok 等官方订阅价、App Store / Google Play 地区价与人民币估算价 | `priceai.cc/official-prices` |
| **卡网订阅** | 聚合 AI 订阅卡网、发卡站与低价会员渠道（链动小铺、独角数卡、Kami / 卡密类自动发货等），比较代充、成品号、卡密、CDK、邮箱与低价会员的有货报价 | `priceai.cc/channels` |
| **官方 API** | 横向比较 DeepSeek / Qwen / Kimi / GLM API、免费 API、Token Plan 与 OpenAI-compatible 入口 | `priceai.cc/official-api` |
| **中转 API** | 对比中转站 / API Gateway 的充值系数、模型倍率、综合倍率与稳定性 | `priceai.cc/api-transit` |
| **API 中转模型检测** | 输入中转接口、模型名与**临时 Key**，生成协议、能力、来源线路、计费与性能**证据报告** | `priceai.cc/api-transit/detector` |

### 2. 数据处理链路（README）

```text
公开来源 / 用户提交 / 站点资料
  -> 采集、导入或人工审核
  -> 标准商品、标准模型、标准站点归类
  -> 展示价格、库存、倍率、来源和更新时间
  -> 用户回到原站核验并自行决策
```

**当前能力**（README「当前能力」）：官方订阅地区价、卡网订阅比价、官方 API 整理、中转 API 价格榜（近 7 日可用性）、API 中转模型检测、指南内容、提交与反馈、后台管理（来源审核 / 试采集 / 分类调整 / 报价隐藏 / 采集日志 / 站点资料维护）。

### 3. 本地起服务（README「快速开始」/「常用命令」）

```bash
npm install
npm run dev        # 前台 http://localhost:3000，后台 http://localhost:3000/admin
```

未配置 Supabase 时**前台用内置演示数据**，完整环境变量见 `public-docs/configuration.md`。另有 `npm run build` / `lint` / `deploy:production -- --check`（试运行）/ `collect:prices -- --all --post` / `collect:browser -- --url ... --password ... --post`。公开文档在 `public-docs/`（`architecture.md`、`configuration.md`、`deployment.md`、`collectors.md`、`data-policy.md`、`api-transit-station-admission.md` 等）。

**贡献**：欢迎 Issue / PR 提交**非商业**改进（新采集器、价格解析修复、分类规则、中转站资料、UI / 公开文档）；README 明确"**涉及验证码、登录墙、WAF 或敏感凭据的站点，不应通过绕过限制的方式采集**"。

## 使用前需要注意

- **许可证不是开源**（`LICENSE` 原文）：`PriceAI Source Available License 1.0` 明确声明**不是** OSI 认可的 open source，**因为它限制商业托管与竞争性公共服务**。**允许**学习 / 研究 / 代码阅读、个人自用与本地部署、内部非商业使用、提 Issue / PR、带署名的少量引用；**未经书面授权不允许**将其（含修改版 / 衍生版）用于**商业用途**、作为**公开托管服务（含 SaaS / 网站 / API / 爬虫 / 数据服务 / 比价 / 导航站）**、运营**竞争性服务**、在其部署上加**广告 / AFF / 赞助位 / 付费导流 / 付费收录 / 付费会员**等变现、转售 / 出租 / 再许可，或**移除版权 / 许可 / 归属与官方标识**、**把 fork / 镜像伪装成官方服务**。
- **品牌、数据与内容另有约束**（`LICENSE` 第 4 条 + `DATA_LICENSE.md` + `TRADEMARKS.md`）：`PriceAI` 名称 / Logo / 域名 / 视觉品牌、**线上生产数据、渠道清单、原始与归一化报价、价格快照 / 历史、库存状态、采集日志、人工审核分类、用户提交、指南与编辑内容、营销文案** 均**不随源码授权**。**想公开部署、展示或运营会让用户误以为是官方 PriceAI 的服务，须先取得书面授权**。
- **商业授权须先联系**（`LICENSE` 第 7 条）：商业部署、托管服务、竞争性使用、闭源使用、品牌使用、数据合作、赞助、认证收录、AFF 合作等，**须先经 Telegram <https://t.me/dimthink> 取得书面许可**。
- **"不担保"要当真**（README「不做什么」/ FAQ）：PriceAI **只帮你看到更多可核验信息**，**不担保任何渠道的售后或长期可用性**；购买前**仍要回原平台确认**商品描述、最终价、售后、退款条件与风险。**低价不等于现在能买**——过期 / 缺货 / 隐藏报价不参与最低价。
- **检测服务是另一套许可与另一套服务**：模型检测执行层在**独立仓库** [physics-dimension/priceai-detector-service](https://github.com/physics-dimension/priceai-detector-service)，**fork 自 canarybyte/veridrop 并沿用 AGPL-3.0-or-later**；使用时**要临时 Key**，其报告只是"辅助判断协议 / 能力 / 来源 / 计费的证据链"，**不代表对任何中转站或商家做担保**。
- **赞助 / 合作不与排序挂钩**（README）：README 声明赞赏 / 赞助 / 商务合作**不会影响**客观排序、最低价计算、风险提示或渠道展示；合作会**明确标注**为广告 / 赞助 / AFF / 合作关系。**但这是自述，未独立验证排序是否与商业关系完全隔离**。
- **卡网 / 中转渠道本身有风险**（README 痛点章节自述）：这些渠道存在**商品命名混乱、库存与更新时间不可靠、售后与账号风险、模型真实性风险**；PriceAI 的定位是"**把风险边界保留下来**"而非消除它。**实际购买决策风险仍由使用者承担**。
- **口径差异要留意**：GitHub **描述**写"聚合 **100+** 卡网渠道"，README 头部正文写"聚合 **200+** AI 订阅卡网"——**两处自述不一致**；Star / Fork 也存在 **ungh（5,397 / 247）与页面（5,420 / 248）**两个口径。**引用时注明来源与日期**。
- **我只读了 README、`LICENSE` 与已核实元数据**：**未 clone、未安装、未运行、未部署、未配置 Supabase、未访问采集器 / 检测服务、未打开线上站**；上述能力均来自文档自述，**未逐一验证**。

## 什么时候会再看

- 想给**自己或小团队买 AI 订阅 / 中转 API** 前，先横向看**官方地区价、卡网现货低价与中转倍率**时。
- 想研究"**把混乱的商品标题 / 模型名 / 站点整理成标准对象**再比价"这种**数据归一化 + 风险披露**产品设计时。
- 想参考一份**"源码可见（source-available）"许可**怎么把"**代码可读**"和"**品牌 / 数据 / 商业托管受限**"分开写时。
- 想搭一个**带采集器 + 后台审核 + 公开文档**的比价 / 聚合站骨架（**仅限个人自用或内部非商业**）时。
- 想对照其它"**成本 / 用量可视化**"路线时（见下方互链）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **都在"管 AI 花出去的钱"**：[codeburn：本地追踪 AI 编码 Token 用量与成本](codeburn-本地追踪AI编码Token用量与成本.md)——那篇**本地追踪你已消耗的编码 Token 与成本**（事后计量），本篇是**购买前比价雷达**（事前决策）。可对照"**已花多少**"与"**该从哪里买更便宜**"。
- **同属"聚合多家 AI 服务"的工具**：[cherry-studio：多模型 Agent 与知识库的桌面工作台](cherry-studio-多模型Agent与知识库的桌面工作台.md) 与 [chatbox：桌面 AI 客户端社区版](chatbox-桌面AI客户端社区版.md)——那两篇是**把多家模型接进一个客户端使用**，本篇是**比较这些服务的价格与渠道来源**。可对照"**用起来**"与"**买得起**"。
- **都涉及"发卡 / 卡网"这一层生态**：[edgeKey：零成本部署到 Cloudflare Workers 的开源发卡商城](edgeKey-零成本部署到CloudflareWorkers的开源发卡商城.md)——那篇是**自己搭一个发卡商城**，本篇是**比价雷达顺带聚合了卡网渠道**。一个偏"**卖方的工具**"，一个偏"**买方看价格**"。

## 参考来源

- 仓库主页与 README（298 行；核验日期 2026-11-13）：https://github.com/dimthink/PriceAI
- 线上站：<https://priceai.cc>（官方订阅 `/official-prices`、卡网订阅 `/channels`、官方 API `/official-api`、中转 API `/api-transit`、模型检测 `/api-transit/detector`、支持页 `/support`）
- 许可证：`LICENSE`（`PriceAI Source Available License 1.0`，5,451 字符；核验日期 2026-11-13）与 `DATA_LICENSE.md`、`TRADEMARKS.md`
- 仓库内文档：`public-docs/`（`README.md`、`architecture.md`、`configuration.md`、`deployment.md`、`collectors.md`、`data-policy.md`、`api-transit-station-admission.md`）、`CONTRIBUTING.md`、`assets/README.md`
- 检测服务（另仓，AGPL-3.0-or-later）：<https://github.com/physics-dimension/priceai-detector-service>（fork 自 <https://github.com/canarybyte/veridrop>）
- 商业授权联系（Telegram）：<https://t.me/dimthink>
- 元数据（Star / Watcher / Fork / 创建与推送时间 / 默认分支 / Release）：GitHub 页面内嵌数据与 ungh.cc 交叉核对（2026-11-13，**页面值与镜像值略有差异**）；开放 Issue 数 / contributors / license 字段取自 shields.io（同日）

以上信息核验于 2026-11-13。**未 clone、未安装、未运行、未部署、未配置 Supabase、未访问采集器与检测服务、未打开线上站**；能力与流程描述均来自 README 与 `LICENSE`，**未逐一验证**。
