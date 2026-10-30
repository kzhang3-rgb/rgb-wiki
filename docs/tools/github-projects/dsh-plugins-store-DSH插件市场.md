# dsh-plugins-store：DSH 插件市场

> 记录这个第三方插件市场的收录口径、验证机制和公开 API，以及"已验证"标记的真实含义。未安装使用。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、文件列表与 GitHub API 元数据，未安装插件）
- 适用范围：DeepSeek Harness（dsh）生态，站点为 dshmk.com

## 核心结论

这是社区为 DeepSeek Harness 做的插件市场，自动收录同时带有 `dsh-plugin` 与 `deepseek-harness` 两个 Topic 的公开仓库，提供分类目录、排行榜和公开只读 API。

它有一套"固定源码 SHA 结构检查 + Linux 隔离沙箱"的验证流程，但文档明确写了：**收录不代表已通过安装、兼容性、安全性或质量验证，"已验证"也不等于完整安全审计或官方背书。** 这两句话是判断它可信度时最该记住的部分。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [ZASENJC/dsh-plugins-store](https://github.com/ZASENJC/dsh-plugins-store) |
| 站点 | [dshmk.com](https://dshmk.com/) |
| 归属 | 个人开发者 ZASENJC，非 DeepSeek 官方 |
| 许可证 | MIT |
| 主语言 | TypeScript |
| 关注度 | 74 Star、6 Fork（2026-10-30 的 API 数据，与页面显示一致） |
| 维护状态 | 2026-08-13 创建，817 次提交，最近一次提交在 2026-10-30，由 GitHub Actions 机器人提交，持续活跃 |
| 协作情况 | 7 个开放 Issue、2 个开放 PR |

## 它提供什么

- 目录页、已验证页和排行榜页，支持搜索、分类、筛选和排序。
- 排行榜维度包括 24 小时 Star 增速、最多 Star、最新发布和最近更新。
- 每 30 分钟同步一次 GitHub 仓库数据。
- 配套的 DSH Web 插件可通过 `/store`、设置页和 Agent 会话浏览市场，并在用户明确请求和确认后安装、更新或移除插件。

## 收录与验证机制

这部分是它相对同类"awesome 列表"更有价值的地方：

- **收录口径**：同时带上述两个 Topic 的公开仓库，排除归档仓库、Fork 和宿主应用。
- **分类方式**：优先使用仓库公开的 GitHub Topics，并与站内词典比对；标签不足时保留为"其他"或"待识别"，不按仓库名强行推断。
- **验证流程**：先对固定 SHA 做源码特征分类（这一步不执行第三方代码），再让待验证目录进入一次性 Linux 沙箱。
- **沙箱条件**：无商店密钥、非 root、资源受限，安装、激活、入口导入和 `apply` 调用都在隔离环境完成。
- **结果判定**：只有绑定当前仓库 ID、SHA、DSH 版本、平台和验证器版本的通过结果才会显示"已验证"。
- **失败处理**：带错误码进入人工复核，不会直接进排除名单。

## 公开 API

只读接口，无需鉴权：

```text
GET https://api.dshmk.com/
```

- 一次返回整个目录，不提供服务端搜索、筛选或分页，调用方需本地处理。
- 约每 30 分钟刷新，用 `schemaVersion` 判断兼容性，不要依赖数组顺序。
- 允许跨域读取，浏览器插件、命令行和 Node.js 均可直接请求。
- 关键字段：`schemaVersion`（当前为 `1`）、`generatedAt`、`stats`、`repositories[]`，仓库项包含 `repositoryId`、`fullName`、`projectType`、`category`、`validation`、`install`。
- 判断验证状态只应看 `repositories[].validation.overall === "verified"`，历史记录或曾经通过不能替代当前结果。

文档还特别提醒：`install.command` 是给普通展示和旧消费者保留的，**不要把它当作 shell 文本直接执行**，应读取 `action` 与 `specifier`，再由本地 DSH 环境决定 profile。

## 安装配套插件

```bash
dsh plugin --profile web add npm:dsh-plugins-store
```

安装后需要重启 DSH Web 并刷新浏览器。配套插件跟随 DSH 版本适配（提交记录里出现过适配 DSH `0.2.1-alpha.2`），安装前先确认自己的 dsh 版本。

## 使用前需要注意

- 属于第三方项目，"已验证"只是该项目的流程标记，不是官方认证。
- 市场的主要作用是引导安装插件，而插件本身是第三方代码，装之前建议自己看源码；文档也强调安装需要用户明确确认。
- 我未安装使用，验证流程的实际效果和插件质量均未核实。
- 项目迭代很快，API 字段和页面行为可能变化。

## 什么时候会再看

- 需要给 dsh 找插件，想知道社区有哪些可用插件时。
- 需要参考"目录 + 分类 + 隔离验证 + 定时同步"这条流水线的实现方式时。

## 相关笔记

- [deepseek-harness：DeepSeek 开源的 Agent 框架](deepseek-harness-DeepSeek开源Agent框架.md)：本市场服务的宿主项目。
- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/ZASENJC/dsh-plugins-store
- 站点：https://dshmk.com/
- 公开 API：https://api.dshmk.com/

以上信息核验于 2026-10-30，Star 数与收录规则可能变化。未安装使用，效果未经验证。
