# epublib：读写 EPUB 的 Java 库

> 记录它"核心库能跑 Android、工具只能在标准 Java 上跑"的分层方式，以及已停止维护、无许可证文件这两个使用前必须知道的事实。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README、仓库根目录与 GitHub API 元数据，未编译、未运行）
- 适用范围：Java（核心库可跑 Android 与标准 Java 环境；命令行工具与 Swing 阅读器仅标准 Java）

## 核心结论

一个用于**读取、写入与操作 EPUB 文件**的 Java 库，结构上分成两块：

- **epublib-core**：读写 EPUB 的核心，**能同时跑在 Android 与标准 Java 环境**——也就是说在 Android 上读写 EPUB 是官方支持的用法。
- **epublib-tools**：一组工具，只在标准 Java 环境运行。包含 EPUB 清理工具、把 HTML 文件打包成 EPUB、把单个未压缩 HTML 转成 EPUB，以及一个基于 Swing 的 EPUB 阅读器。

API 走的是很直白的一套领域模型：`Book` + `Metadata` + `Author` + `Resource` + `TOCReference`，加上 `EpubWriter` 写文件、`EpubReader` 读文件；章节与目录通过 `book.addSection(...)` 逐级挂上去。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [psiegman/epublib](https://github.com/psiegman/epublib) |
| 作者 | Paul Siegmann（psiegman） |
| 语言 / 构建 | Java；Maven 多模块（`epublib-parent` / `epublib-core` / `epublib-tools`） |
| 许可证 | **API 未检测到（`/license` 返回 404，根目录无 LICENSE 文件）** |
| 关注度 | 1,124 Star、326 Fork、68 关注、87 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2009-11-18 创建，**最近推送 2024-01-19**；4 个分支，**无 Tag 发布** |
| 体积 | 约 2.9 MB（`size` 字段，不含 Git 历史） |

## 用法要点

**Android 接入**（README 给出的方式）需要**排除传递依赖**：

```groovy
repositories {
    maven { url 'https://github.com/psiegman/mvn-repo/raw/master/releases' }
}
dependencies {
    implementation('nl.siegmann.epublib:epublib-core:4.0') {
        exclude group: 'org.slf4j'
        exclude group: 'xmlpull'
    }
    implementation 'org.slf4j:slf4j-android:1.7.25'
}
```

两个排除项都是在避开 Android 平台已有的实现：`xmlpull` 与 Android 自带的 XML 解析冲突，`slf4j` 换成 Android 版本。

**命令行工具**（打包成 one-jar 后）：

```bash
java -jar epublib-3.0-SNAPSHOT.one-jar.jar --in input.epub --out result.epub --author Tester,Joe
java -jar epublib-3.0-SNAPSHOT.one-jar.jar --in input.epub --out result.epub --cover-image my_cover.jpg
```

能直接看出它的定位：命令行层只做了"改作者、换封面"这类小修改，程序化构建才是主要用法。

## 使用前需要注意

- **没有许可证文件**：GitHub API 的 license 字段为空，`/license` 端点返回 404，仓库根目录也只有 `.github / .gitignore / .travis.yml / CREDITS / README.md` 与三个模块目录——**不存在 LICENSE**。这意味着默认按"保留所有权利"处理，**商用或闭源分发前需要自行联系作者取得授权**，不要因为它是公开仓库就默认可用。
- **已停止维护**：最近一次推送是 2024-01-19，距今约一年九个月；同期 87 个开放 Issue 无人清理。作为"能用的稳定库"没问题，但**不要指望问题被修**。
- **CI 徽章已经失真**：README 顶部的构建状态徽章指向 `travis-ci.org`，而 Travis CI 面向开源项目的免费服务早已关停——**这个徽章不能作为"当前构建正常"的依据**。
- **没有 Tag / Release**：仓库没有任何版本标签，但 README 引用了 `epublib-core:4.0` 与 `3.0-SNAPSHOT` 这类版本号。**依赖是通过作者的个人仓库 `psiegman/mvn-repo` 分发的，不在 Maven Central**——这既影响可用性（该仓库若删改，构建就断了），也是典型的依赖供应链风险点，生产环境应改为自行构建并内部托管。
- **只读了 README 与目录，没有编译**：`EpubWriter` / `EpubReader` 的实际行为、对 EPUB 3 的支持范围、以及 Android 上的兼容性均未验证；README 的示例代码本身也没有标注适用的核心库版本。
- **第三方图片是外链**：README 里阅读器截图引用 `www.siegmann.nl` 上的图片，若站点失效截图就会变红——正是本库"图存本地"规则的反面案例。

## 什么时候会再看

- 需要在 **Android 或 Java 侧读写 EPUB** 时：这套 `Book` + `Resource` + `TOCReference` 的模型比直接拼 OPF/NCX 省事。
- 想参考**"核心库跨平台、工具限定桌面"**的分层方式时：这个划分很清楚地说明了哪些代码依赖了 Android 没有的东西。
- 需要做 **EPUB 批量小修改**（改作者、换封面、清理）时：先看它的命令行工具是否够用，不够再写代码调核心库。
- 想参考**"一个仍在被使用的老库如何暴露老化信号"**时：Travis 徽章、无 Tag、无 LICENSE、Issue 积压，这四个信号放在一起看非常典型。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 目前收藏夹里还没有其他 EPUB / 电子书相关的笔记，本站点暂无同类互链对象。

## 参考来源

- 项目仓库与 README：https://github.com/psiegman/epublib
- 依赖分发用的个人 Maven 仓库：https://github.com/psiegman/mvn-repo
- 元数据（Star / Fork / 时间 / 许可证状态）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未编译、未运行，库的实际行为与 EPUB 兼容范围均未验证。
