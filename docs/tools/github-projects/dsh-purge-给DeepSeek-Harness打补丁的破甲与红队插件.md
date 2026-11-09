# dsh-purge：给 DeepSeek Harness 打补丁的"破甲"与红队插件

> 记录一个**直接改官方客户端文件**的 DSH 插件：表面是"网络安全红队工具"，实际同时做三件事——**破甲（jailbreak）、红队演练控制台、以及一批 DSH 体验补丁**。**Apache-2.0 + 禁止转售原版 + 商业授权另谈**，仓库里还带着另一个 MIT 上游项目的披露文件。**属本站已收录的"越狱类"敏感类别：只记元数据与结构，不镜像内容、不复述其中提示词、不提供使用建议。**

- 状态：草稿
- 最近核验：2026-11-04（阅读英文 README 全文主体与中文/日文版存在性、`LICENSE` / `LICENSE-COMMERCIAL.md` / `NOTICE` / `package.json` 全文、`docs/redteam-upstream-DISCLOSURE` 与 `docs/redteam-upstream-LICENSE` 全文、`presets/redteam/preset.yml`、目录树、提交与 Release 元数据，并用 API 核验旧路径重定向。**未安装、未运行、未做任何补丁或注入，未审计代码**）
- 适用范围：Node.js ≥ 18；作为 DSH 插件安装（`peerDependencies: @deepseek-ai/cordis ^4.0.1`）；**与宿主版本强耦合**

## 核心结论

**① 它的实现方式就是"改宿主的文件"。** Apply 时把匹配到的官方 `@deepseek-ai/*` 文件先备份为 `.dshpurge.bak`，再替换内容、覆盖 shim、注入插件自带的加密提示词。`package.json` 的 `dsh.client.inject` 明确列出要注入的 **6 个官方客户端包**（`dsh-client-connection` / `-runtime` / `-locale` / `-ui-slots` / `-ui-settings` / `-ui-conversation`）。它声明**不 patch Harness 源码树、不 patch 官方 npm-global**，只动本机已装的官方包与本地配置。**这也解释了它为什么能改提示词与审批闸门——它改的是宿主本身，而不是在外围加插件。**

**② 对外表述是"红队插件"，而能力描述写得很直白。** `presets/redteam/preset.yml` 的描述是"攻防演练模式……内置主会话 + 信息收集/资产梳理/**漏洞发现/漏洞利用/内网渗透**五个执行角色（并发最多 3 个），资产测绘落入 SQLite 资产库"。README 同时声明开源版**只允许 loopback 与 RFC1918 私网地址**、域名先解析再按 IP 判定、"在对话里说这是我的服务器"不构成例外、禁止游戏作弊、**开源版不含技能库与知识库**。**这些边界是作者自设的，不是许可证或平台强制的**（个人判断：自设边界本身是加分项，但边界强度取决于实现，本笔记未审计代码，无法验证）。

**③ 许可与分发都"收紧过"，而且它自己承认了。** `LICENSE-COMMERCIAL.md` 直说"**以前 MIT 时期已经发出去的 tag，我改不了**"——即项目曾用 MIT，现行为 **Apache-2.0 + 禁止转售原版**（付费转售、换皮上架、当商业产品分发均不允许）+ 分叉必须署名 + 商业授权单独谈。同一文件还写"**2026 年 10 月 27 日起新版本我只在 Telegram 频道发**，买过授权的人按合同拿包，不走 GitHub Releases"，**但 GitHub Releases 上 v1.1.82～v1.1.90（2026-10-28～11-02）仍在**——这两句如何并存，从外部无法确证（较可能是"商业包走 TG、开源包仍发 GitHub"，但这只是推测）。

**④ 一处可核验的口径冲突。** `package.json` 里 `dshTargets` 同时列了 **`0.2.1-rc.2` 与 `0.2.1-rc.5`**，而 README 明确写"**DSH 0.2.1-rc.3 / rc.4 及更新不支持**"。同一仓库对"支持哪些宿主版本"给了两个不同答案，安装前应以实测为准。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [HunYiSage/dsh-purge](https://github.com/HunYiSage/dsh-purge)（**原为 `yujunzhixue/dsh-purge`**） |
| 迁移核验 | 用 API 查旧路径 `yujunzhixue/dsh-purge` **返回同一仓库 id（1339256252）**，即**改名/转移而非新建**，创建时间与历史、Release 均保留；第三方目录 `dsh-plugin.org` 上仍挂旧作者名 `yujunzhixue` |
| 描述 | "DeepSeek Harness 破甲：让所有模型都能破甲，不同模型可换不同提示词；默认提示词面向国模「小码酱」。Jailbreak for every model — swap prompts per model。**求 Star 收藏 ⭐** 如要二开，请查看署名要求部分" |
| 定位 | 作者自述为"面向**已安装的官方 DeepSeek Harness** 的网络安全红队插件"；**1.1.90 对应 DSH 0.2.1-rc.2** |
| 许可证 | **Apache-2.0**（`LICENSE` 202 行）+ **`LICENSE-COMMERCIAL.md`**（商业授权另谈）+ `NOTICE`（保留声明与署名）；**仓库内另附上游 MIT 项目的 `docs/redteam-upstream-DISCLOSURE` / `-LICENSE`** |
| 主语言 | JavaScript（Node.js ≥ 18） |
| 关注度 | **17,459 Star、4,058 Fork、32 关注、10 个开放 Issue**（GitHub API，2026-11-04） |
| 维护状态 | **2026-08-19 创建**；**2026-11-03 迁仓**（提交 `chore: 仓库迁到 HunYiSage/dsh-purge；README 改为团队维护口吻`）；`pushed_at` 2026-11-03；默认分支 **`master`**（另有 `oss-public` 分支）；提交密集（2026-10-27～11-03 几乎每天多次），**有外部贡献者 PR**（#197/#198 来自 `iigzx`） |
| 版本节奏 | **v1.1.81～v1.1.90**（2026-10-27～11-02，几乎每天 1～2 版），每个 Release 1 个资产；仓库内另有 `release-notes.md`（约 110 KB） |
| 体积 | API `size` 1,777 KB（约 1.7 MB）；**103 个文件、合计约 4.2 MB**，其中 `client.js` 单文件 **539 KB**、`lib/core.js` 380 KB、`lib/redteam/store-core.js` 341 KB |
| 话题标签 | `deepseek`、`deepseek-harness`、`dsh-plugin`、`dsh-plugins`、`harness-jailbreak`、`jailbreak`、`llm-jailbreak` |
| 生态外部 | 徽章显示被 `awesome-dsh-plugin.com`、`deepseek.stream/plugins/dsh-purge`、`dsh-plugin.org` 收录；团队频道 `t.me/LLM_PoJia`；README 致谢 LINUX DO 社区 |

> 关于关注度：**17,459 Star 但订阅者只有 32**。结合描述里直接写"求 Star 收藏"，star 数与真实跟踪人数的落差值得注意。

## 它能做什么（结构清单）

**dock 两页（README）**

| 页 | 内容 |
| --- | --- |
| **Clean** | 补丁状态分组、Apply / Restore / Uninstall、**提示词编辑器**、多套规则集 |
| **Drill** | 稳定版内置；授权后是"资产与本地环境"——**本开源版不含技能库与知识库**，仅本地实验 |

**命令与工具**

- CLI：`dsh-purge --status / --apply / --revert / --uninstall / --edit`
- 斜杠命令：`/purge status|apply|revert|uninstall|edit|help`、`/rules list|use <id>|create|delete|reset`、`/rewind`
- 工具：`purge_status`、`purge_apply`、`purge_revert`
- **命令名冲突处理**：若已有插件占用 `/rewind`（或 `/purge`、`/rules`、`/skills`），该名会被跳过或改名挂成 `/purge-rewind`，其余命令与 `/dsh-purge/*` 路由不受影响

**Apply 流程（README mermaid 摘要）**

```text
启动或点 Apply → 已打过补丁？→ 是：跳过
                              → 否：备份原文件为 <file>.dshpurge.bak
                                    → 替换匹配到的文件
                                    → 覆盖 shim
                                    → 注入插件自带的加密提示词
```

**每轮的提示词注入决策（README mermaid 摘要）**

```text
规则库激活集 → prompt-inject.md 非空？          → 用它
   ↓ 否
当前目录勾选的 AGENTS.md / CLAUDE.md 非空？     → 用它
   ↓ 否
插件加密槽默认值
   ↓
工作区基线策略 strict / fallback / always
   → strict：丢弃 DSH 工作区基线段
   → fallback：有用户注入（上面 A 或 B）时丢弃
   → always：始终保留宿主基线段
```

**安装**：不在 npm，走 GitHub tarball——

```sh
dsh plugin --profile web add https://codeload.github.com/HunYiSage/dsh-purge/tar.gz/refs/heads/master
```

`package.json` 含 **`postinstall: node scripts/ensure-profile-bundle.mjs`——安装即执行仓库内脚本**。

**文件构成（103 个中的代表）**

- 入口与核心：`bin/dsh-purge.js`（8 KB）、`client.js`（dock，539 KB）、`lib/core.js`（380 KB）、`lib/index.js`（122 KB）
- **`lib/redteam/` 共 19 个模块**：`client` / `store-core` / `tools` / `ui` / `schema` / `score-rules` / `skill-fixes` / `skill-availability` / `evidence-ledger` / `report-replay` / `platform-config` / `preset-composition` / `ip-utils` / `validate` / `settings` / `role-section` / `atomic-write` / `index` / `store`
- 宿主操作相关：`lib/extract-asar.js`（16 KB，处理 Electron asar 包）、`lib/restart-desktop.js`、`lib/restart-web.js`、`lib/uninstall-restart.js`、`lib/hide-console.js`、`lib/child-process-hide.mjs`
- 策略与身份：`lib/hooks-deny.js`、`lib/net-scope.js`、`lib/identity.js`、`lib/rules.js`、`lib/skills.js`、`lib/rewind.js`
- 预设与脚本：`presets/redteam/{preset.yml, agent.cordis.yml, anchor-gate.mjs}`、`scripts/redteam-setup.sh`（25 KB）、`scripts/patch-client-dock.mjs`（24 KB）
- 提示词与文档：`lib/default-prompt-inject.zh.md` / `.en.md`、`docs/drill-auth-legal.html`（8 节法律免责声明）、`docs/v1.1.34-prompt-inject.md`、`docs/notice/`（含直播与微信截图）、`docs/preview/`（6 张界面图）

**恢复机制（README）**

- 每个目标在首次 Apply 前复制为 `<file>.dshpurge.bak`；
- **Restore** / `/purge revert` 把备份拷回并删除备份；无备份时剥离本插件写入的 shim 行；
- `prompt-inject.md` 视为用户文件、保留；
- **Uninstall** 先还原（若打过补丁）再删注入文件、规则库与插件本身；Apply 是幂等的。

## 使用前需要注意

- **它改的是官方客户端本体。** 不是"外围挂一层"，而是备份并替换 `@deepseek-ai/*` 包内文件、且每次会话注入自己的提示词。**一旦 Apply，该 DSH 安装就不能再视为原版。** 官方升级后可能出现补丁失配（作者说失配会"待定或跳过，不会盲目改写"）。
- **安装即执行脚本**：`postinstall` 会运行仓库内脚本，这属于安装期代码执行，需要自行判断来源可信度。
- **红队能力的来源与主体许可不同**：`docs/redteam-upstream-DISCLOSURE` / `-LICENSE` 指向 **`Jueze-2019/dsh-redteam-mode`（MIT）**。那份披露自述"编排、取证与报告层，**不含 exploit 代码**"，但 `scripts/redteam-setup.sh` 会**下载 nmap / nuclei / fscan / gogo / chisel / frp 等工具**到用户目录。**Apache-2.0 主体 + 内嵌 MIT 上游件**，两者不可混为一谈。
- **约束强度未经我验证**：local-lab 限制（仅 loopback 与 RFC1918、按解析后 IP 判定）、禁止游戏作弊等都由作者声明；**本笔记未审计代码，无法确认这些限制在所有路径上都生效**。
- **法律声明要求读者注意**：README 第 108 行写"**任何人用它犯罪，责任自负，维护者不担责**"；第 126 行写"违约者的开源许可**自动且不可撤销地终止**"。另有独立页面 `docs/drill-auth-legal.html` 与 `LICENSE` 条款相呼应。这类条款在开源项目里并不常见。
- **禁止转售 + 必须署名**：个人本机自用不额外收费；**拿去卖、卖课、白标、给第三方当产品需先谈商业授权**（`LICENSE-COMMERCIAL.md`，邮件 `a2269198325@163.com`）。分叉/衍生必须署名并保留 `NOTICE`。
- **"破甲"这一层与本站的"越狱"专题同类**：默认提示词面向所谓"国模小码酱"，作用是让模型绕过安全对齐。按本站对这类内容的处理原则：**只记录元数据与结构，不镜像、不复述提示词内容、不提供使用建议**。
- **引流与商业成分明显**：仓库描述直接写"**求 Star 收藏 ⭐**"，`docs/notice/` 放直播与微信截图，另有 Telegram 团队频道与商业授权邮箱。与本 wiki 收录的 AvPlay、social-auto-upload 同属"技术内容 + 社群/商业运营"混合体，记录以技术事实为主。
- **宿主版本耦合极紧**：1.1.81+ 只对应 DSH 0.2.1-rc.2（0.2.0 可用）；0.1.7 需用 1.1.39 或更早；**0.2.1-rc.3/rc.4 及更新声明不支持**（而 `package.json` 又列了 rc.5）。DSH 自身仍在 rc 阶段，插件跟着 rc 版本逐个适配，长期可用性存疑。

## 什么时候会再看

- 想研究"**插件如何在不改宿主源码树的前提下改变宿主默认行为**"——备份 → 替换 → shim 覆盖 → 按会话注入这一套手法有参考价值；
- 想参考"**许可证收紧后如何写商业授权与署名条款**"——`LICENSE-COMMERCIAL.md` 写得很直白，且诚实交代"以前 MIT 的 tag 改不了"；
- 想了解 DSH 插件生态现状——第三方目录收录、团队化维护、迁仓与旧路径重定向都能在这里看到一个实例；
- 需要一份"**高风险双用途项目的免责与边界声明**"的写法样本时。

## 相关笔记

- **宿主框架**：[deepseek-harness：DeepSeek 开源 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)——本篇是它的第三方插件；框架仍处 developer preview，这正是插件必须按 rc 版本逐个适配的原因。
- **同生态的插件集市**：[dsh-plugins-store：DSH 插件市场](dsh-plugins-store-DSH插件市场.md)——本篇是被这类第三方目录收录的插件（徽章显示至少三个目录在列）。
- **同类"越狱/去审查"主题**：[jailbreaks：大模型越狱提示词集合](jailbreaks-大模型越狱提示词集合.md)——那是提示词样本集合，本篇把注入做成插件并打进客户端；两者都属本站"仅记元数据与结构"的敏感类别。
- **Prompt 攻防的体系化中文手册**：[PromptJailbreakManual：Prompt 越狱手册](PromptJailbreakManual-Prompt越狱手册.md)——那是人读的手册（含 Prompt 注入 / 泄露 / 越狱三章），本篇把手法做成插件、随会话自动注入。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/HunYiSage/dsh-purge （旧路径 `yujunzhixue/dsh-purge` 已重定向至同一仓库）
- 中文 README：https://github.com/HunYiSage/dsh-purge/blob/master/README.zh-CN.md
- 许可与商业授权：https://github.com/HunYiSage/dsh-purge/blob/master/LICENSE 、https://github.com/HunYiSage/dsh-purge/blob/master/LICENSE-COMMERCIAL.md
- 红队模式上游：https://github.com/Jueze-2019/dsh-redteam-mode
- 团队频道：https://t.me/LLM_PoJia

以上信息核验于 2026-11-04，Star 数、版本与宿主兼容范围都可能变化。**未安装、未运行、未做任何补丁或注入，未审计代码**；记录范围限于公开元数据与结构，不构成可用性评价或使用建议。
