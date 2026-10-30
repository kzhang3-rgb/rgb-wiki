# CCBaga：Claude 身份与浏览器环境管理工具

> 记录这个项目解决什么问题、适合谁，以及再次使用前需要先确认的风险。目前只阅读了仓库文档，未安装运行。

- 状态：草稿
- 最近核验：2026-10-30（仅阅读仓库 README、文档目录与文件列表）
- 适用范围：macOS 12 及以上（Apple Silicon）、Linux x86_64、Windows 10/11 x86_64

## 核心结论

CCBaga 是一个用 Tauri 开发的桌面工具，把"管理本地 Claude 身份"和"清理、校验本机浏览器环境"放进同一个界面。它包含两个程序：CCBaga-A 负责身份管理、系统设置和环境校验，CCBaga-B 是一个使用独立配置的 Fingerprint Chromium 浏览器。

项目自己声明：**仅供在境外居住或旅行的人管理自己的 Claude 身份，并禁止违反 Anthropic 官方服务条款的用法。** 这个定位意味着它并非对所有人适用，是否合规需要自行判断，本笔记不构成推荐。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [liseami/CCBaga](https://github.com/liseami/CCBaga) |
| 许可证 | MIT |
| 技术栈 | Tauri（Rust 后端）+ TypeScript/Vite 前端，Bun 作为包管理器 |
| 关注度 | 约 3.3k Star、342 Fork（2026-10-30 的快照，会变动） |
| 维护状态 | 157 次提交，最近一次提交在 2026-10-28，仍在活跃更新 |
| 获取方式 | GitHub Releases 提供 macOS DMG、Linux DEB/RPM/AppImage、Windows NSIS 安装包 |

## 它解决什么问题

- **本地 Claude 身份切换**：归档当前身份、恢复到已保存的身份，或从空工作区开始，并可选择哪些数据保留在工作区、哪些跟随身份移动。
- **浏览器指纹与重启**：生成新的指纹种子，应用所选地区的语言与时区，然后重启配套浏览器。
- **环境一致性检查**：把浏览器和服务器的读数与真实网络出口对比，覆盖出口国家、时区、语言、平台、CPU 数、引擎版本和定位设置。
- **系统设置调整**：应用时区、首选语言和地区设置；定位与权限开关仍需在系统界面手动操作。
- **网络配置引导**：内置三步流程，生成一段提示词交给本地编码助手（支持 Claude Code、Codex、Cursor、OpenCode、Copilot）执行。

## 记录到的实现细节

- 身份数据存于本地 SQLite，归档内容压缩进加密的 `.baga` 容器，放在 Claude 工作目录之外。
- 切换身份前先校验归档，失败可回滚，中断的切换有恢复路径。
- Rust 负责校验、任务锁、进程身份检查和执行；macOS 系统调用通过少量 Objective-C 桥接。
- 网络部分是一个独立的 Agent Skill，可用 `npx skills add liseami/CCBaga --skill ccbaga-network --global` 单独安装，不随主程序捆绑。

```powershell
# 安装网络配置 Skill（来自项目文档），需先安装 Node.js
npx skills add liseami/CCBaga --skill ccbaga-network --global
```

## 使用前需要确认的风险

以下为仓库文档已经写明的内容，不是我的推测：

- **定位有明确边界**：官方声明面向境外使用场景，并禁止违反 Anthropic 服务条款的用法。
- **校验结论有限度**：文档明确说明 DNS 与 WebRTC 泄漏检查未经独立验证，绿色结果只代表测得的项目通过，不等于匿名保证或账号一定通过验证。
- **网络方案需自行付费**：需要自己购买 VPS 和住宅代理，页面上的服务商按钮是推广链接，选择服务商时应参考其他来源。
- **安装包未签名**：Windows 安装包没有 Authenticode 签名，可能出现 SmartScreen 提示；官方版本只由作者本人发布。

仓库有 342 个 Fork，下载时应以 Releases 页面和作者账号为准，不要从非官方渠道安装。

## 什么时候会再看

- 需要在本机反复归档或切换 Claude 配置，并希望先看清会删除哪些数据时。
- 需要确认浏览器读数与本机和网络出口是否一致时。
- 想参考它的做法：本地加密容器 + 校验后再落地 + 失败回滚，这套模式对其它"覆盖用户配置"的工具同样适用。

## 相关笔记

- 本项目的索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/liseami/CCBaga
- 使用指南：https://github.com/liseami/CCBaga/blob/main/docs/usage.md
- 平台说明：https://github.com/liseami/CCBaga/blob/main/docs/linux.md 和 https://github.com/liseami/CCBaga/blob/main/docs/windows.md
- 身份存储说明：https://github.com/liseami/CCBaga/blob/main/docs/identity-storage.md

以上信息核验于 2026-10-30，Star 数、版本与功能可能变化。未安装运行，实际行为与安全性未经验证。
