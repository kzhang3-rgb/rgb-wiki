# deepseek-harness：DeepSeek 开源的 Agent 框架

> 记录 dsh 是什么、怎么跑起来，以及 developer preview 阶段要先确认的前提。未安装运行。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、文件列表与 GitHub API 元数据，未安装运行）
- 适用范围：需要 Node.js 环境；默认自带本地 Web UI

## 核心结论

DeepSeek Harness（命令名 `dsh`）是 DeepSeek AI 开源的 agent harness，架构主张是"一切皆插件"（everything is a plugin），插件体系由 [Cordis](https://github.com/cordiverse/cordis) 支撑。

官方明确标注它处于 **developer preview**，并写明"会有破坏性变更"，所以它更适合用来了解设计和试用，不适合作为稳定依赖。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness) |
| 归属 | DeepSeek AI 官方组织 |
| 许可证 | MIT（第三方依赖见 `THIRD_PARTY_NOTICES.md`） |
| 主语言 | TypeScript 97.1%，其余为 CSS、Python、JavaScript、Shell |
| 关注度 | 约 4.5k Star、258 Fork（2026-10-30 的 API 数据，会变动） |
| 提交规模 | 12,293 次提交，19 位贡献者，默认分支为 `master` |
| 维护状态 | 最近一次推送在 2026-08-13，此后约两个半月没有新提交 |
| 发行版 | 未发布 Release，提交记录显示版本处于 `0.1.0-rc` 阶段 |
| 反馈渠道 | Issues 已关闭，走 GitHub Discussions 和 Discord |

## 它解决什么问题

- 提供一个可插拔的 agent 运行框架，把模型、工具和界面都当作插件组合。
- 自带 Web UI，也支持从源码运行，便于本地试用。
- 插件生态用 GitHub topic [`dsh-plugin`](https://github.com/topics/dsh-plugin) 汇总，方便发现第三方插件。

## 运行方式

通过 npm 直接启动：

```bash
npx @deepseek-ai/dsh web
```

从源码运行：

```bash
git clone https://github.com/deepseek-ai/deepseek-harness.git
cd deepseek-harness
pnpm install
pnpm run build
pnpm dsh web
```

两条路径都会启动 Web UI，默认地址是 `http://127.0.0.1:3080`。以上命令在 PowerShell 中同样可以直接执行。

## 值得注意的设计

- **一切皆插件**：设计思路来自论文 [A Programming Paradigm for Spatiotemporal Composability](https://github.com/cordiverse/paper)，不是简单的插件注册表。
- **仓库自身用 agent 参与开发**：根目录带 `AGENTS.md`、`CLAUDE.md` 以及 `.agents/`、`.claude/` 目录，可当作"如何给 agent 写协作规范"的实例参考。
- **文档结构清晰**：`docs/architecture.md`、`docs/development.md`、`docs/user/guide/`。
- **工程化完整**：pnpm workspace、vitest、tsdown、lefthook、knip，另有 `pytest.ini` 用于 Python 侧测试。
- README 与 CONTRIBUTING 都提供中英双份，并配 `.i18n.yaml` 文案文件。

## 使用前需要注意

- **developer preview**：官方明示会有破坏性变更，依赖它的插件和配置随时可能失效。
- **没有正式版本**：未发布 Release，版本仍在 `0.1.0-rc` 阶段。
- **活跃度需要自行确认**：最近一次推送是 2026-08-13，距今约两个半月没有新提交，选型前建议先看上游是否仍在推进。
- **反馈渠道有限**：Issues 关闭，只能通过 Discussions 或 Discord。
- 我未安装运行，实际可用性、资源占用和本地环境兼容性均未验证。

## 什么时候会再看

- 需要评估或对比开源 agent 框架、特别是插件化架构时。
- 想借鉴它的插件设计，或参考它给 agent 写的协作规范文件时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/deepseek-ai/deepseek-harness
- 中文 README：https://github.com/deepseek-ai/deepseek-harness/blob/master/README.zh.md
- 架构文档：https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/architecture.md
- 底层框架 Cordis 与设计论文：https://github.com/cordiverse/cordis 、https://github.com/cordiverse/paper

以上信息核验于 2026-10-30，Star 数与维护状态可能变化。未安装运行，实际效果未经验证。
