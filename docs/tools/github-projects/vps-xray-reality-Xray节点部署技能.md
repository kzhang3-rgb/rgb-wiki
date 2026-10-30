# vps-xray-reality：Xray 节点部署技能

> 记录这个给 Codex / Claude Code 用的部署技能做什么、覆盖到哪一步，以及运行前该先看什么。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、目录结构与 GitHub API 元数据，未在服务器上运行）
- 适用范围：Ubuntu 22.04 / 24.04 单机 VPS；需自备服务器

## 核心结论

一个给 AI 编码助手用的**部署技能**（skill），把"在 Ubuntu VPS 上部署直连 IP 的 Xray VLESS + REALITY + Vision 节点"这件事固化成一份可重复的清单。安装方式是把仓库目录复制到 `~/.codex/skills/` 或 `~/.claude/skills/`，之后用 `$vps-xray-reality` 或 `/vps-xray-reality` 显式调用。

它解决的是重复劳动：不用每次自己拼 Reality 密钥、UUID、服务端配置和客户端链接。作者明确定位为**不需要域名、Cloudflare 或 nginx**的单机直连方案，省掉证书与域名那套配置。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [nicksung369/vps-xray-reality](https://github.com/nicksung369/vps-xray-reality) |
| 类型 | AI 助手技能包（Codex + Claude Code），非独立程序 |
| 许可证 | MIT |
| 关注度 | 153 Star、13 Fork（网页与 API 两处一致） |
| 维护状态 | 2026-05-14 创建，**仅 1 次提交**（Initial public release），此后未更新 |
| 仓库规模 | 很小，约几 KB；1 个分支、无 Tag、无 Release |
| 社区反馈 | 0 个开放 Issue、1 人关注，几乎没有使用反馈沉淀 |

## 覆盖的步骤

README 列出七步流程：

1. 预检变量（VPS_IP、NODE_NAME 等）
2. 用官方安装脚本安装 Xray
3. 生成 Reality 密钥、UUID 和 short ID
4. 写服务端 `config.json`
5. UFW 公网端口检查
6. 生成客户端 URI、二维码和 mihomo YAML
7. 验证命令与常见坑说明

仓库除 README 外，核心是 `SKILL.md` 和 `agents/` 目录，也就是技能本身的定义与执行说明。

## 适用范围

- Ubuntu 22.04 / 24.04
- 直连 IP 的 Xray REALITY 节点
- 单台 VPS、单个 inbound
- 不依赖域名、Cloudflare 或 nginx

超出这个范围的场景（多节点、多用户、需要域名伪装、非 Ubuntu 系统）不在它的覆盖内。

## 使用前需要注意

- **它会在服务器上真实执行命令**：安装软件包、写配置、改动 UFW 防火墙规则。这类技能的本质是让 AI 助手照着一份清单操作你的机器，所以**运行前应当先逐条读一遍 `SKILL.md`**，确认每一步要做什么，而不是直接让助手跑完。改防火墙那一步尤其要留意，规则写错可能把自己关在门外。
- **只有 1 次提交、无测试、无 Release、无 Issue 反馈**：既没有更新记录，也没有他人验证过的痕迹。把它当作一份可读的清单参考，比当作成熟工具更合适。
- **作者自己写了合规前提**：README 声明只在你拥有或被授权管理的服务器上使用，并确保自建代理服务符合当地法律和服务商条款。部分 VPS 服务商的条款禁止此类用途，部署前先确认。
- **服务端部署与客户端配置是两件事**：它输出客户端 URI、二维码和 mihomo YAML，客户端侧的分流规则需要另外配置，可参考[小火箭分流规则配置](Shadowrocket-Rules-小火箭分流规则配置.md)。
- 我未在服务器上运行，实际执行结果、配置有效性和验证步骤均未测试。

## 什么时候会再看

- 需要在 Ubuntu VPS 上快速搭一个不依赖域名的 Xray REALITY 节点时。
- 想参考"把部署流程写成 AI 助手技能"这种组织方式时（七步清单 + 验证步骤 + 常见坑）。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 客户端侧：[Shadowrocket-Rules](Shadowrocket-Rules-小火箭分流规则配置.md)。

## 参考来源

- 项目仓库与 README：https://github.com/nicksung369/vps-xray-reality
- 技能定义文件：https://github.com/nicksung369/vps-xray-reality/blob/main/SKILL.md

以上信息核验于 2026-10-30，Star 数与仓库内容可能变化。未实际运行，效果未经验证。
