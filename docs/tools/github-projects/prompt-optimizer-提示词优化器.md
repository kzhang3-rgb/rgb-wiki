# prompt-optimizer：提示词优化器（网页/桌面/扩展/Docker 四形态）

> 一句话值得记住的取舍：它把"提示词优化"做成了**四种使用形态 + 纯客户端直连**（数据留在本地、不经中间服务器），但同时**主仓库已从 MIT/ISC 迁移到 AGPL-3.0**——自建部署前这条必须先看清楚。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、仓库页与 GitHub API 元数据，未部署、未使用）
- 适用范围：想改进提示词质量、需要系统/用户提示词双模式、或想把优化能力接进 MCP 客户端的人

## 核心结论

prompt-optimizer 是一个**帮你把提示词写得更好**的工具，官方支持四种使用方式：**网页应用、桌面应用、Chrome 扩展、Docker 部署**。

它的核心功能可以归成几类：

- **智能优化**：一键优化 + 多轮迭代，提高回答准确度；
- **双模式**：**系统提示词**与**用户提示词**分别优化；
- **分析与对比评估**：不只是"改一版"，而是**评估这次改动是否真的更好**（分析 / 单结果评估 / 多结果对比评估）；
- **多模型接入**：OpenAI、Gemini、DeepSeek、智谱、SiliconFlow 等，也支持自定义兼容 OpenAI 的接口；
- **图像生成**：文生图（T2I）与图生图（I2I），接入 Gemini、Seedream 等；
- **高级测试模式**：上下文变量管理与批量替换、多轮对话测试、Function Calling（OpenAI 与 Gemini）；
- **安全架构（作者口径）**：**纯客户端处理，直接与模型服务商交互，不经中间服务器**；
- **部署侧**：访问密码保护；支持 MCP，可接 Claude Desktop 等 MCP 客户端。

README 举了三个演示场景：**硬核审稿人**（把"一味附和"变成有结构的批评）、**二手市场砍价回复**（同一模板换变量就换策略）、**文生图**（把一句话扩成可执行的关键视觉提示）。

对 **Agent / MCP** 方向的接入是它比较实用的一点：Docker 运行时 MCP Server 随 Web 应用一起启动，走同端口的 `/mcp` 路径；提供三个工具——`optimize-user-prompt`、`optimize-system-prompt`、`iterate-prompt`。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [linshenkx/prompt-optimizer](https://github.com/linshenkx/prompt-optimizer) |
| 描述 | "An AI prompt optimizer for writing better prompts and getting better AI results." |
| 许可证 | **实际为 AGPL-3.0**——`LICENSE` 文件的提交信息是 "feat: 将项目协议从 MIT/ISC 迁移到 AGPL-3.0"（2025-10-30）。**注意：GitHub API 因识别问题返回 `NOASSERTION`，两者不一致，以 LICENSE 文件为准**。 |
| 语言 | TypeScript（pnpm workspace） |
| 关注度 | **27,603 Star、3,258 Fork、100 关注、27 个开放 Issue**（GitHub API 口径含开放 PR；仓库页显示 Fork 3.2k、Issues 22，2026-11-02） |
| 维护状态 | 2025-02-12 创建；GitHub API 显示最后推送 **2026-04-27**；最新版本 `v2.9.6`（README 页脚与提交记录一致） |
| 规模 | 页面显示 **4 分支、37 Tag、约 815 次提交**；体积约 35,459 KB（约 34 MB） |
| 默认分支 | **`develop`**（不是常见的 `main`） |
| 在线体验 | https://prompt.always200.com/ |

## 用法要点

**最省事的是在线版**（`https://prompt.always200.com/`）：README 说这是**纯前端项目，数据都存在本地浏览器、不上传服务器**。

其余三种部署：

```bash
# Docker（默认配置）
docker run -d -p 8081:80 --restart unless-stopped --name prompt-optimizer linshen/prompt-optimizer

# Docker + API key + 访问密码
docker run -d -p 8081:80 \
  -e VITE_OPENAI_API_KEY=your_key \
  -e ACCESS_USERNAME=your_username \
  -e ACCESS_PASSWORD=your_password \
  --name prompt-optimizer linshen/prompt-optimizer
```

- **Vercel**：推荐 **fork 后导入**（这样能跟踪上游更新），用环境变量 `ACCESS_PASSWORD`、`VITE_*_API_KEY` 配置；
- **桌面应用**：从 GitHub Releases 下载（installer 支持自动更新），**最大优势是没有 CORS 限制**——可直连本机 Ollama 或安全策略严格的商业 API；
- **Chrome 扩展**：商店版可能因审核滞后。

**MCP 接入**（Claude Desktop 为例，在 `services.json` 里指到 `http://localhost:8081/mcp`），相关环境变量：

```
MCP_DEFAULT_MODEL_PROVIDER=openai   # openai / gemini / anthropic / deepseek / siliconflow / zhipu / dashscope / openrouter / modelscope / custom
MCP_LOG_LEVEL=info
```

## 使用前需要注意

- **许可证以 LICENSE 文件为准 = AGPL-3.0**：GitHub API 显示 `NOASSERTION`，但 `LICENSE` 文件的提交信息明确写着从 MIT/ISC 迁移到 **AGPL-3.0**（2025-10-30）。**这是本笔记最重要的结论**：若你把它改造成对外提供的服务，通常要按 AGPL 承担开源义务；**不要因为 API 字段是 `NOASSERTION` 就当成"随便用"**。
- **历史上修过一个 SSRF 漏洞（事实）**：`api` 目录有提交 "refactor: 完全移除 Vercel/Docker 代理功能以修复 SSRF 安全漏洞"（2025-09-30）。也就是说**它曾经提供过代理功能，后因安全原因被整体移除**——说明该项目的攻击面曾被审视过，部署旧版本时要留意。
- **API key 的存放位置随部署方式而变**：在线版把 key 存在**浏览器本地**（README 强调不上传服务器）；Docker/Vercel 部署则通过**环境变量**把 key 注入服务。**"纯客户端"是指在线版的形态，不等于所有部署方式都不接触你的 key**，自建时请按自己的信任模型评估。
- **两处 Issue 数字不同口径**：仓库页 Issues 22 是纯 Issue 口径，API `open_issues_count` 为 27（含开放 PR），按约定以 API 值为准并注明口径。
- **默认分支是 `develop`**：clone 或引用文件时默认拿到的是开发分支，不是稳定分支——**需要稳定版本请认 Tag/Release**。
- **"优化"效果属工具定位，不是保证**：README 的"提升回答准确度""多轮迭代"是产品目标；实际提升幅度取决于任务与模型，**我未评测**。
- **我只读 README、仓库页与元数据**：未部署、未使用、未接 MCP。

## 什么时候会再看

- 需要**一个可以直接用的提示词优化/评测工具**，且希望**不改代码就能部署**（在线版或 Docker）时。
- 想把**提示词优化能力接进 MCP 客户端**（如让 Claude Desktop 调用 `optimize-system-prompt`）时。
- 需要参考**"同一产品四种交付形态"**（Web / 桌面 / 浏览器扩展 / 容器）的工程组织时。
- 评估**"纯客户端 vs 服务端代理"的安全取舍**时——它经历过 SSRF 修复并移除了代理，这个演进本身值得参考。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **MCP / Agent 接入同族**：[servers](servers-MCP官方参考服务器合集.md)、[java-sdk](java-sdk-MCP官方JavaSDK.md)、[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md)、[book-to-skill](book-to-skill-把技术书转成ClaudeCode技能.md)。
- **许可形态对照（务必一起看）**：[firecrawl](firecrawl-把网站转成LLM可用数据的抓取API.md)、[indicator](indicator-Go技术指标与回测框架.md)、[reverse-skill](reverse-skill-逆向与渗透技能路由包.md)——本篇是"**API 字段与 LICENSE 文件不一致**"的典型样本。

## 参考来源

- 项目仓库与 README：https://github.com/linshenkx/prompt-optimizer
- 在线版：https://prompt.always200.com/
- 开发文档 / MCP 文档：仓库内 `dev.md`、`docs/user/mcp-server_en.md`
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02；许可证以仓库 `LICENSE` 文件提交记录为准

以上信息核验于 2026-11-02。仅阅读 README、仓库页与元数据；未部署、未使用。许可证结论（AGPL-3.0）以 `LICENSE` 文件的提交信息为依据，与 GitHub API 的 `NOASSERTION` 字段不一致，已在正文标明。"优化效果"类描述属产品定位，未评测。
