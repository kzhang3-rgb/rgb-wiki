# manim：数学动画引擎

> 记录 3Blue1Brown 的动画引擎是什么、和社区版的关系，以及安装时最容易踩的两个坑。未安装运行。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README 与 API 元数据，未安装运行）
- 适用范围：Python 3.7+；需要 FFmpeg、OpenGL，LaTeX 可选

## 核心结论

3Blue1Brown 作者用来制作视频的动画引擎，用代码精确控制数学动画。这个仓库是他本人的版本，包名是 `manimgl`；2020 年社区 fork 出了更稳定、测试更完善的[社区版](https://github.com/ManimCommunity/manim)。

**装之前要先选版本**，两个版本的安装说明不能混用；这个仓库的 pip 包名是 `manimgl` 而不是 `manim`。README 里专门用警告框强调了这两点，是新手最容易踩的坑。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [3b1b/manim](https://github.com/3b1b/manim) |
| 定位 | 作者个人版本（ManimGL），社区版是另一个独立仓库 |
| 许可证 | MIT |
| 主语言 | Python |
| 关注度 | 87,853 Star、7,329 Fork（2026-10-30 的 API 数据，页面显示 87.4k） |
| 维护状态 | 2015-03-22 创建，6,369 次提交，最近一次推送在 2026-04-18 |
| 待办积压 | 496 个开放 Issue |
| 分支与标签 | 默认分支 `master`，4 个分支，21 个标签 |

## 两个版本怎么选

| 版本 | 仓库 | 特点 |
| --- | --- | --- |
| 本仓库（ManimGL） | `3b1b/manim` | 作者自用版本，随视频制作需求更新；视频代码见 `3b1b/videos` |
| 社区版 | `ManimCommunity/manim` | 2020 年 fork，目标是更稳定、测试更全、更快响应社区贡献、更容易上手 |

作者在 Contributing 一节也说明：社区版生态最活跃，有测试和持续集成；本仓库同样接受 PR。官方 FAQ 有[两个版本差异的对比页](https://docs.manim.community/en/stable/faq/installation.html#different-versions)。

## 安装

直接安装：

```bash
pip install manimgl
manimgl
```

从源码开发：

```bash
git clone https://github.com/3b1b/manim.git
cd manim
pip install -e .
manimgl example_scenes.py OpeningManimExample
```

各平台补充步骤：

- **Windows**：先装 FFmpeg 和 LaTeX 发行版（推荐 MiKTeX），再走上面的 clone 流程。
- **macOS**：`brew install ffmpeg mactex`；MacTeX 约 6GB，可用轻量的 BasicTeX 替代，再按需补装宏包。ARM 机器还需要 `arch -arm64 brew install pkg-config cairo`。
- **Conda**：建环境后 `pip install -e .` 即可。

系统依赖是 FFmpeg、OpenGL，LaTeX 为可选项（要用 LaTeX 排版才需要）；Linux 还需要 Pango 及其开发头文件。

## 常用命令行参数

| 参数 | 作用 |
| --- | --- |
| `-w` | 把场景写入文件 |
| `-o` | 写入文件并打开结果 |
| `-s` | 跳到结尾，只显示最后一帧 |
| `-so` | 保存最后一帧为图片并显示 |
| `-n <number>` | 跳到场景的第 n 个动画 |
| `-f` | 播放窗口全屏 |

配置通过仓库里的 `custom_config.yml`：可以指定视频输出目录、图片和音频的查找路径，以及样式和画质的默认值。

## 学习资料

- 官方文档（作者标注为进行中）：https://3b1b.github.io/manim/
- 中文文档：https://docs.manim.org.cn/ ，由 [manim-kindergarten](https://github.com/manim-kindergarten/) 维护
- 扩展类与视频代码合集：[manim_sandbox](https://github.com/manim-kindergarten/manim_sandbox)
- 3Blue1Brown 视频源码：[3b1b/videos](https://github.com/3b1b/videos)，但旧视频的代码可能和最新版 manim 不兼容

## 使用前需要注意

- README 明确警告：两个版本的安装说明混用会出问题，先决定装哪个再照对应说明操作。
- 包名容易搞错：`pip install manim` 装的不是这个仓库，这里要装 `manimgl`。
- 系统依赖不轻，除了 Python 包还要 FFmpeg、OpenGL，用 LaTeX 还要装发行版，Windows 和 macOS 各有额外步骤。
- 496 个开放 Issue 且最近一次推送在 2026-04-18，说明它更偏"作者自用优先"；对稳定性要求高时优先考虑社区版。
- 我未安装运行，实际渲染效果和本地兼容性均未验证。

## 什么时候会再看

- 需要用代码生成数学动画或做解释性视频时。
- 需要回忆两个版本怎么选、包名和系统依赖时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/3b1b/manim
- 社区版：https://github.com/ManimCommunity/manim
- 视频源码仓库：https://github.com/3b1b/videos
- 中文文档：https://docs.manim.org.cn/

以上信息核验于 2026-10-30，Star 数与维护状态可能变化。未安装运行，实际效果未经验证。
