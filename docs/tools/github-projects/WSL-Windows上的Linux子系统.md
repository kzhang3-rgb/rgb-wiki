# WSL：Windows 上的 Linux 子系统

> 记录官方仓库的定位与边界：它是 WSL 的开源协作入口，不只是发行渠道。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README 与元数据，未安装）
- 适用范围：Windows 10 / 11

## 核心结论

微软官方仓库，对应 Windows Subsystem for Linux：在 Windows 上**直接运行未经修改的 Linux 命令行工具、实用程序和应用**，不需要传统虚拟机的开销，也不用双系统。

安装就是一条命令：

```powershell
wsl --install
```

它的定位偏向"以 Linux 方式使用 Windows"，而不是反过来——把 Linux 工具链搬进现有 Windows 工作流，避免为了跑几个命令行工具就切系统或维护一台虚拟机。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [microsoft/WSL](https://github.com/microsoft/WSL) |
| 许可证 | MIT |
| 技术栈 | C++ 69.2%、C 26.8%，另有少量 C#、CMake、PowerShell、Python |
| 关注度 | 34,262 Star、1,875 Fork、591 关注（2026-10-30 的 API 数据）；页面显示 32.3k Star、885 Issue，与 API 的 1,036 略有出入 |
| 活跃度 | 880 次提交、467 个分支、114 个 Tag、100 个 Release（最新 2.7.3，2026-04-25）、123 位贡献者 |
| 历史 | 2016-04-06 创建，已维护约十年 |
| 站点 | https://wsl.dev/ ，文档在 https://aka.ms/wsldocs |

## 相关仓库

WSL 不止一个仓库，README 列了三个配套的：

| 仓库 | 用途 |
| --- | --- |
| [microsoft/WSL2-Linux-Kernel](https://github.com/microsoft/WSL2-Linux-Kernel) | WSL 随附的 Linux 内核 |
| [microsoft/wslg](https://github.com/microsoft/wslg) | 在 WSL 里支持 Linux GUI 应用 |
| [microsoftdocs/wsl](https://github.com/microsoftdocs/wsl) | 官方文档源码 |

## 使用前需要注意

- **这个仓库是协作与发布入口，不等于全部实现**：它承载源码、Issue、文档与 Release，安装本身走 `wsl --install` 或 Microsoft Store，不必从仓库构建。
- **应用带遥测**。README 明确写了程序会记录基础诊断数据并发送给微软，可在仓库说明的方式下关闭，细节在 `DATA_AND_PRIVACY.md`；相关段落也提到使用该软件视为同意这些做法。这是需要在部署前知道的一条。
- **商标有单独条款**：修改版本中使用微软商标或标识，不能造成混淆或暗示微软背书；第三方标识另受第三方政策约束。
- 我未安装，兼容性与性能未验证。

## 什么时候会再看

- 需要在 Windows 上用 Linux 工具链、又不想装虚拟机时——先看它的 setup 最佳实践文档。
- 需要 Linux GUI 应用支持时，对照 `wslg` 那个仓库。
- 排查 WSL 自身问题时，直接去 [Issues](https://github.com/microsoft/WSL/issues)（当前开放约 1,000 个）和 Discussions。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/microsoft/WSL
- 官方文档：https://learn.microsoft.com/windows/wsl/
- 遥测说明：https://github.com/microsoft/WSL/blob/master/DATA_AND_PRIVACY.md

以上信息核验于 2026-10-30。未安装。
