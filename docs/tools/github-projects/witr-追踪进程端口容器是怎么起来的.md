# witr：追一个进程/端口/容器"为什么在跑"

> 一句话值得记住的取舍：`ps`、`lsof`、`ss`、`systemctl`、`docker ps` 各自只回答"在跑什么"，**witr 把跨工具的因果链聚成一条**——进程、端口、容器、文件都先归到 PID，再回答"谁把它拉起来的"。

- 状态：草稿
- 最近核验：2026-11-02（阅读 README 全文、仓库页与 GitHub API 元数据，未安装、未运行）
- 适用范围：Linux / macOS / Windows / FreeBSD 上的进程与服务故障排查；有交互式 TUI 与 JSON 两种输出

## 核心结论

witr 只回答一个问题：**Why is this running?（这东西为什么在运行？）**

它的判断是：现有的排查工具暴露的是**状态与元数据**，但"为什么"需要人**跨工具手动关联**（例如从端口查到 PID，再从 PID 查到 systemd 单元，再查到容器）。witr 把这段关联自动化，对某个 PID 一次给出四件事：

1. 在跑什么；
2. 它是怎么被启动的；
3. 现在是什么在维持它；
4. 它属于什么上下文。

关键设计是**端口、服务、容器、文件都先映射到 PID**，再沿因果链向上回溯。输出有三种形态：**一条人类可读的报告**、**给脚本和 AI agent 用的 JSON**、或**交互式 TUI 看板**。

常见用法：

```bash
witr node                 # 这个 node 进程为什么在跑？
witr --port 8080          # 谁占着 8080，为什么？
witr --pid 1234 --tree    # 它的祖先与子进程，按树展示
witr --container redis    # 一个容器（跨 Docker / Podman / Kubernetes）
witr --port 8080 --json   # 同一答案，给脚本与 AI agent
witr                      # 在交互式 TUI 里浏览全部
```

它还带一个**浏览器演示**（`https://pranshuparmar.github.io/witr/`）：模拟一台 Linux 机器，含引导教程与自由沙盒，**不用安装就能试**。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [pranshuparmar/witr](https://github.com/pranshuparmar/witr) |
| 描述 | "Why is this running?" |
| 许可证 | Apache-2.0 |
| 语言 | Go（发布为**单个静态二进制**） |
| 关注度 | **17,759 Star、551 Fork、39 关注、8 个开放 Issue**（GitHub API 口径含开放 PR；仓库页显示 Issues 6，为纯 Issue 口径，2026-11-02） |
| 维护状态 | 2025-12-20 创建；GitHub API 显示最后推送 **2026-06-09**；仓库页显示最新提交 2026-05-16（v0.3.2 文档更新）、约 487 次提交 |
| 规模 | 页面显示 **3 分支、20 Tag**；体积约 7,189 KB（约 7 MB） |
| 演示 | https://pranshuparmar.github.io/witr/ （仓库已开启 GitHub Pages） |

## 用法要点

安装方式极多，README 按"包管理器优先"排序（因为更好升级）：

```bash
# Unix 一键脚本
curl -fsSL https://raw.githubusercontent.com/pranshuparmar/witr/main/install.sh | bash

# Windows PowerShell
irm https://raw.githubusercontent.com/pranshuparmar/witr/main/install.ps1 | iex

# 包管理器（节选）
brew install witr          # macOS / Linux
winget install -e --id PranshuParmar.witr
conda install -c conda-forge witr
scoop install main/witr

# 从源码
go install github.com/pranshuparmar/witr/cmd/witr@latest
```

除上述外还有 APT（Ubuntu 26.04+ / Debian sid+）、MacPorts、AUR、npm、FreeBSD Ports、Chocolatey、AOSC、Guix、uniget、aqua、brioche、mise，以及 `.deb`/`.rpm`/`.apk` 预构建包。README 明确提醒：**社区包可能滞后于 GitHub release**。

平台覆盖 **Linux / macOS / Windows / FreeBSD**，Linux 侧还支持 **loong64**（龙芯）；安装脚本会做 OS 与架构探测、下载二进制与 man page，并可校验 SHA256。

README 目录里另有 **AI Agents、Flags、Output Behavior、Platforms、Success Criteria** 等章节（本篇未逐条核对，需要精确参数时应回原文）。

## 使用前需要注意

- **能拿到多完整的因果链取决于平台能力**：witr 要读进程树、容器运行时、服务管理器等系统内部信息。**在受限环境（容器内、非 root、平台权限受限）里，链路可能只能部分还原**。README 专门列了 `Platforms` 与 `Success Criteria` 章节来说明各自的成功标准——**建议先读这两节再判断它在你机器上能答到什么程度**（我未验证具体边界）。
- **它是聚合解释层，不是替代品**：`ps`/`lsof`/`docker ps` 仍是一手的底层信息源，witr 的价值在"把它们的输出串成因果"；底层工具拿不到的东西，它一样拿不到。
- **社区包版本可能落后**：apt、Homebrew 等渠道的版本通常晚于 GitHub release，**排查问题时留意你装的是哪个渠道、哪个版本**（页面记录最新为 v0.3.2 一档）。
- **版本仍早期**：2025-12 才创建，tags 已到 20 个，版本号停在 `0.3.x`，**接口与输出格式仍可能变动**。
- **两处 Issue 数字不同**：仓库页 Issues 6，API `open_issues_count` 为 8（含开放 PR），按约定以 API 值为准并注明口径。
- **我只读 README、仓库页与元数据**：未安装、未在真实机器上跑过，TUI 的实际可用性与各平台链路完整性均未验证。

## 什么时候会再看

- **排查"某个端口/进程到底谁拉起来的"**：当 `ss`+`lsof`+`systemctl` 要来回拼的时候，一条 `witr --port 8080` 更省事。
- 需要**给脚本或 AI agent 要机器可读的进程因果**时（`--json` 就是为此准备的）。
- 想参考**"把多个底层工具的输出聚成一条因果链"**这种工具体裁时。
- 需要**跨容器运行时**（Docker / Podman / Kubernetes）定位某个容器时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **系统运维与网络排查类**：[WSL](WSL-Windows上的Linux子系统.md)、[cloudflared](cloudflared-Cloudflare隧道客户端.md)。
- **"给 AI agent 用"的 CLI 设计对照**：[cli-anything-tdx-tq](cli-anything-tdx-tq-通达信TQ命令行工具.md)（同为 Go CLI、同样把"给 agent 用的 JSON"当一等输出）。

## 参考来源

- 项目仓库与 README：https://github.com/pranshuparmar/witr
- 浏览器演示：https://pranshuparmar.github.io/witr/
- 打包状态总览：https://repology.org/project/witr/versions
- 元数据（Star / Fork / 时间 / 许可证 / 体积）：GitHub REST API，2026-11-02

以上信息核验于 2026-11-02。仅阅读 README、仓库页与元数据；未安装、未运行。各平台能还原到什么程度的因果链，以 README 的 Platforms / Success Criteria 章节与作者说明为准，本笔记未独立验证。
