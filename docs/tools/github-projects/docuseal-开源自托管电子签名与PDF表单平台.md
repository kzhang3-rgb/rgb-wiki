# DocuSeal：自托管的开源电子签名与 PDF 表单平台

> 它是把"上传 PDF → 拖出可填字段 → 发给多人签署 → 收签名件"这条链路搬到自己服务器上的开源方案，定位是 DocuSign 的自托管替代。值得记住的是它的**授权边界**：核心是 **AGPL-3.0**，但叠加了一条 Section 7(b) 附加条款（交互界面必须保留 DocuSeal 署名），且白标、SSO/SAML、短信验证、批量发送等能力被列为**付费 Pro Features**——所以"开源"到底开到哪一层，是使用前必须先看清的问题。

- 状态：草稿
- 最近核验：2026-11-13（阅读 README 全文、`LICENSE`、`LICENSE_ADDITIONAL_TERMS`、仓库文件树前 200 项与顶层目录分布、GitHub 页面内嵌元数据、shields.io 徽章；**未 clone、未 `docker run`、未部署、未运行任何命令、未实测签署流程**）
- 适用范围：自托管部署；后端为 **Ruby on Rails** 应用（页面/徽章口径 Ruby **37.0%**，非纯 Ruby 仓库），自带 `Dockerfile` 与 `docker-compose.yml`；默认 **SQLite** 存储，可用 `DATABASE_URL` 切 PostgreSQL / MySQL；文件可落磁盘或 S3 / Google / Azure 云存储。

## 核心结论

**一句话定位（README 自述）**："Open source document filling and signing"——GitHub 描述为 **Open source DocuSign alternative**，用来创建、填写、签署数字文档。

**三个最值得记的取舍：**

1. **许可证不是"纯 AGPL"**：仓库同时放了 `LICENSE`（GNU AGPL-3.0 全文）与 `LICENSE_ADDITIONAL_TERMS`，后者明确写着"依据 AGPL 第 7(b) 条，衍生作品**必须在交互式用户界面中保留 DocuSeal 的原始署名**"。这属于 AGPL 允许的附加条款，但对二次开发（尤其是想去掉品牌做白标）有直接约束——**去署名需要看 Pro/商业授权**。
2. **功能被明确分成"开源核心"与"付费 Pro"两层**：README 用 `## Features` 与 `## Pro Features` 两块并列列出。开源块是表单构建、12 种字段、多人签署、SMTP 邮件、PDF 电子签名与验签、用户管理、API/Webhook；Pro 块是**公司 Logo 与白标、用户角色、自动提醒、短信邀请与身份验证、条件字段与公式、CSV/XLSX 批量发送、SSO/SAML、HTML/DOCX 建模板 API、嵌入式签署与表单构建器**（对应 `docuseal.com/pricing`）。
3. **内置了一个 MCP 服务器**（这一点 README 没写，是从源码看到的）：`app/controllers/mcp_controller.rb` 与 `app/controllers/mcp/` 实现了一个 MCP 协议端，暴露 `search_templates`、`load_template`、`create_template`、`send_documents`、`search_documents` 五个工具——即把"用模板批量发签署件"接给 AI Agent。

**个人判断（未验证）**：它的热度很高（约 1.9 万 Star，见项目信息），定位清晰、部署门槛低（一条 `docker run`）。**但真正决定能否商用的是那两层功能的切分与附加署名条款**，评估前务必先对照 pricing 页确认你要的功能落在哪一层。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [docusealco/docuseal](https://github.com/docusealco/docuseal) |
| 描述 | "Open source DocuSign alternative. Create, fill, and sign digital documents ✍️" |
| 归属 | 账号 `docusealco`（**组织**，页面 `isOrg: true`）；`isFork: false`、`isArchived: false`、`isPrivate: false` |
| 许可证 | **AGPL-3.0 + Section 7(b) 附加条款**（页面 `spdxId: AGPL-3.0`；`LICENSE` 为 GNU AGPLv3 全文，`LICENSE_ADDITIONAL_TERMS` 要求保留交互界面署名；README 亦写明 "AGPLv3 License with Section 7(b) Additional Terms"） |
| 主语言 | **Ruby 37.0%**（shields.io，2026-11-13；页面 `languages: true`，未取到完整占比明细） |
| 技术栈 | **Ruby on Rails + Hotwire/Turbo**（topics 含 `hotwired-turbo`、`ruby-on-rails`）+ **Vue** + **TailwindCSS** + **Webpack**；`app/` 下有 `controllers`（含 `api/`、`mcp/`）、`javascript/elements/`（大量自定义 Web Elements）、`models`、`views`；另有 `db/`、`lib/`、`spec/`（RSpec） |
| 关注度 | **19,045 Star / 1,947 Fork / 63 Watcher / 101 开放 Issue**（页面 Star/Fork/Watcher 与 shields Issue 数，2026-11-13；**页面值与 ungh.cc 镜像一致，无差异**） |
| 维护状态 | **2023-07-03 创建**；页面 `updatedAt` **2026-11-12**、ungh 报 `pushedAt` **2026-11-09**（距核验日约 1–4 天，**活跃**）；累计 **178 Release / 178 tag** |
| 默认分支 | `master` |
| 版本 | **178 Release / 178 tag**；页面 `packages: true`（发布镜像）、`usedBy: true`（有下游依赖方）；Docker 镜像 `docuseal/docuseal` |
| 话题标签 | `document-signing` · `documents` · `e-signature` · `hotwired-turbo` · `open-source` · `pdf` · `pdf-sign` · `pdf-signature` · `ruby-on-rails` · `self-hosted` · `tailwindcss` · `vue` · `webpack` |
| 体积 | `master` 文件树约 **1,142 个文件**（ungh.cc files API）；顶层分布：`app` 762、`db` 111、`lib` 97、`spec` 66、`config` 32、`docs` 22、`public` 12、`bin` 7、`tmp` 2、`.github` 2 |
| 官网 | <https://www.docuseal.com> ・ 演示：<https://demo.docuseal.tech> |

## 功能边界：开源 vs Pro（README 自述）

**开源（Features）：** PDF 表单字段构建器（所见即所得）、12 种字段类型（签名、日期、文件、复选框等）、单文档多签署人、SMTP 自动邮件、文件存磁盘或 AWS S3 / Google Storage / Azure Cloud、PDF 电子签名与**签名验证**、用户管理、移动端优化的签署页、7 种界面语言（签署支持 14 种语言）、API 与 Webhook、分钟级部署，另有 iOS / Android App。

**付费（Pro Features）：** 公司 Logo 与白标、用户角色、自动提醒、短信邀请与身份验证、条件字段与公式、CSV/XLSX 批量发送、SSO / SAML、HTML API 与 PDF/DOCX 字段标签建模板、嵌入式签署表单、嵌入式表单构建器。

**未核实之处**：README 列出的 Pro 功能，其**实现是否全部在本开源仓库内、还是部分在闭源仓库 / 云服务中，我没有逐一定位**。仅从文件树能看到仓库里确有 `app/controllers/sso_settings_controller.rb`、`esign_settings_controller.rb`、`mcp/` 等，但**不能据此断定 Pro 能力就完全开源**。引用"开源 / 闭源"结论时请以 pricing 页与官方为准。

## 部署方式（README 自述）

| 方式 | 命令 / 入口 |
| --- | --- |
| Docker（单容器） | `docker run --name docuseal -p 3000:3000 -v.:/data docuseal/docuseal`（默认 SQLite，可用 `DATABASE_URL` 切库） |
| Docker Compose | `curl https://raw.githubusercontent.com/docusealco/docuseal/master/docker-compose.yml > docker-compose.yml`；`sudo HOST=your-domain-name.com docker compose up`（配合 Caddy 自动签发 SSL） |
| 一键平台 | 提供 Heroku / Railway / DigitalOcean / Render 的部署按钮（各自模板仓库） |
| 云版 | `docuseal.com/sign_up`（官方托管） |

## 内置 MCP 服务器（源码所见，非 README 自述）

`app/controllers/mcp_controller.rb` 用 `ActionController::Metal` 实现了一个 MCP 端：按 `method` 分派 `initialize` / `notifications/initialized` / `ping` / `tools/list` / `tools/call`，工具表映射到 `Mcp::` 下五个控制器——`search_templates`、`load_template`、`create_template`、`send_documents`、`search_documents`。即"查模板 / 建模板 / 发签署件"可以被 AI Agent 调用。**我未实际启动该端点、未验证其协议兼容性与鉴权方式**。

## 使用前需要注意

- **⚠️ 许可证含附加条款，不是"自由 MIT"**：核心是 **AGPL-3.0**（强 copyleft，且**通过网络提供服务也算分发，须向使用者提供源码**）；额外叠加 **Section 7(b) 附加条款要求交互界面保留 DocuSeal 署名**。**二次开发 / 商用前必须读 `LICENSE` 与 `LICENSE_ADDITIONAL_TERMS`**；想去品牌（白标）需另议商业授权。本笔记**不构成法律意见**。
- **⚠️ 功能分两层，先确认你要的在哪层**：白标、SSO/SAML、短信验证、批量发送、嵌入式签署等属 **Pro Features（付费）**。**"开源"不等于"全部免费可用"**——落地前请对照 `docuseal.com/pricing`。
- **⚠️ 这是联网服务端应用，不是本地小工具**：它是 Rails Web 应用，**需要常驻服务器、需要联网发签署邮件**；部署中要自备域名（Compose 方案依赖 Caddy 签发证书）、数据库与存储。**未配置好邮件与存储时，签署链接无法送达或附件丢失**。
- **⚠️ 我未做任何运行验证**：**未 clone、未 docker 起服务、未跑签署流程、未验证官方文档描述的功能**；上述功能清单、部署命令与授权条款均**来自 README、`LICENSE`、`LICENSE_ADDITIONAL_TERMS` 与仓库文件自述**。
- **维护活跃但版本很多（事实）**：创建于 2023-07-03，核验日已 **178 Release / 178 tag**，`pushedAt` 在 4 天内——**迭代快意味着升级时要注意数据库迁移与破坏性变更**（我未核对其升级/迁移策略）。

## 什么时候会再看

- 需要一个**可自托管、可 Docker 一键起**的电子签名 / PDF 表单方案，且能接受 AGPL 与附加署名条款时。
- 评估**"开源核心 + 付费 Pro"如何切分功能**：它的 `Features` / `Pro Features` 分层是可参考的商业化切法。
- 想抄它**把 Rails 应用本身做成 MCP 服务器**（`app/controllers/mcp/`）的做法，让自己的业务能力直接被 Agent 调用。
- 对比同类 **DocuSign 替代 / PDF 表单工具**的功能与授权差异时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"处理 PDF"但层次不同**：[pdf-inspector：判断PDF类型并转Markdown的Rust库](pdf-inspector-判断PDF类型并转Markdown的Rust库.md)——一个做 PDF 的**解析/分类/转文本**，一个做 PDF 的**填字段与签署**；对照可看清"接 PDF"这件事的两端。
- **同为"开源替代 + 自托管部署"的取舍样本**：[gitea：自托管一体化Git服务平台](gitea-自托管一体化Git服务平台.md)——两者都要自己起服务、自己管数据库与存储、自己担运维；放在一起便于比较"自托管换数据主权"的成本。

- **同为"开源替代 + 自托管"的取舍样本**：[tymeslot：Calendly / Cal.com 的开源自托管替代](tymeslot-开源自托管的日程预约平台.md)——那是日程预约平台（AGPL + 商标条款、云版另有付费档），本篇是电子签名平台；可对照两者的许可策略与自托管打包形态。

## 参考来源

- 项目仓库与 README：https://github.com/docusealco/docuseal
- 许可（AGPL-3.0）：https://github.com/docusealco/docuseal/blob/master/LICENSE ・ 附加条款：https://github.com/docusealco/docuseal/blob/master/LICENSE_ADDITIONAL_TERMS
- MCP 端实现（源码）：https://github.com/docusealco/docuseal/blob/master/app/controllers/mcp_controller.rb
- 官网与定价：https://www.docuseal.com/pricing
- 元数据（Star / Fork / Watcher / 创建与推送时间 / 语言 / 默认分支 / Release 数 / topics）：GitHub 页面内嵌数据与 ungh.cc 镜像，2026-11-13；Issue 数与主语言取自 shields.io，2026-11-13

以上信息核验于 2026-11-13，Star 数与仓库内容可能变化。**未 clone、未部署、未运行、未实测签署流程**；功能、部署命令与授权条款均来自 README、`LICENSE`、`LICENSE_ADDITIONAL_TERMS` 与仓库文件自述；**Pro 功能的实现是否在本开源仓库内未核实**。
