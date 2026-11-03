# squoosh：在浏览器里压缩图片的 Google 工具

> Chrome 团队做的图片压缩 Web App：用 WASM 把一串顶级编解码器搬进浏览器，图片不上传服务器。真正值得记的不是"能压图"，而是**它的隐私说明把边界划在了哪**，以及**它的 `pushed_at` 是绿的、默认分支却停在 2024-08**——这比"项目停更了"更难判断。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README、LICENSE、`codecs/` 目录与 GitHub API 元数据，并用 commits / branches 接口交叉核对活跃度；未安装、未构建、未在浏览器中实际压缩图片）
- 适用范围：日常把 PNG/JPEG 压到能上网页的体积，或需要**在本地、不把素材交给第三方**做格式转换的场景

## 核心结论

1. **压缩全程在浏览器本地完成**，图片不会上传到服务器——这是它 README 里明写的第一条，也是它在同类工具里的主要卖点。代价是首次加载要下载 WASM 编解码器。
2. **但"本地"不等于"没有上报"**：README 的 Privacy 段紧接着说明使用了 Google Analytics，会收集访问基础数据，以及**压缩前后图片的体积数值**、PWA 的安装类型与安装时间。**图片内容不外发，处理结果的元数据会**——这个区别值得看清。
3. **活跃度指标自相矛盾，必须交叉核对**：API 的 `pushed_at` 是 **2026-10-22**（看起来在更新），但默认分支 `dev` 上**最新一条提交是 2024-08-19**。这是本知识库遇到的第三种情况，既不是"真陈旧"，也不是"抓取缓存"——详见下文对照表。
4. **它把编译产物直接提交进了仓库**：`codecs/` 下每个编解码器都带构建所需脚本与 Dockerfile，提交记录里也有 "Add the fixed compilations of avif_enc.js and the wasm file" 这类改动。仓库因此约 **175 MB**，克隆会明显偏慢。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [GoogleChromeLabs/squoosh](https://github.com/GoogleChromeLabs/squoosh) |
| 描述 | Make images smaller using best-in-class codecs, right in the browser |
| 官网 | https://squoosh.app（`has_pages: false`，站点不托管在 GitHub Pages 上） |
| 许可证 | **Apache-2.0**（`LICENSE` 为标准 Apache 2.0 全文，未填写版权人占位符） |
| 主语言 | TypeScript |
| 关注度 | **26,108 Star、2,204 Fork、248 关注、262 开放 Issue**（GitHub API，2026-11-03） |
| 创建 / 最近推送 | 2018-03-07 创建；**最近推送 2026-10-22** |
| 仓库体积 | 约 179,391 KB（≈175 MB） |
| 默认分支 | **`dev`**（不是 `main`/`master`） |
| Topics | 空 |
| 归属 | `GoogleChromeLabs` 组织；`custom_properties` 中 `requires_action_scanning: true` |

> 那 262 个"开放 Issue"是 **issue 与 PR 的合计**（该仓库 `has_issues: true`）；元数据里没有分开给出两者的比例，所以不要当成 262 个纯 Issue。

## 它是怎么做的

`codecs/` 目录本身就是一份编解码器清单——每个子目录是一个独立编译的 WASM 模块：

| 目录 | 大致用途（据目录名判断，未逐个读源码） |
| --- | --- |
| `mozjpeg` | JPEG 编码（MozJPEG） |
| `oxipng`、`png` | PNG 无损优化与 PNG 编码 |
| `imagequant` | 调色板量化（压缩成索引色） |
| `avif` | AVIF 编码 |
| `jxl` | JPEG XL |
| `qoi` | QOI 格式 |
| `hqx` | 像素画放大（hqx 系列算法） |
| `resize`、`rotate` | 缩放与旋转 |
| `visdif` | 视觉差异度量（用于对比压缩前后画质） |

构建侧同样在仓库内：`codecs/build-cpp.sh`、`build-rust.sh`，以及 `cpp.Dockerfile` / `rust.Dockerfile`——**C/C++ 与 Rust 两套工具链各自容器化**，编译出 WASM 后再由 `lib/` 下的 TypeScript 加载。前端开发流程是常规三步：`npm install` → `npm run build` → `npm run dev`。

## 活跃度：pushed_at 是绿的，默认分支却是灰的

API 报 `pushed_at: 2026-10-22`，看起来是个在维护的项目。但用 commits 接口拉默认分支 `dev` 的历史，最新两条是：

| 提交 | 日期 | 说明 |
| --- | --- | --- |
| `e8d35e0` | **2024-08-19** | Replace deprecated terser plugin (#1423) |
| `5f72c2e` | 2024-08-13 | Fix single threaded avif encoding (#1409) |

再看 branches 接口：仓库里有大量 `dependabot/cargo/...` 分支与 `abortable-task`、`api`、`basis` 之类的历史特性分支。所以我的判断（**推断，非 API 明示**）是：**这段时间的推送主要发生在非默认分支上**（依赖机器人或长期未合并的分支），默认分支实际上已经约两年没有新提交。

至此本知识库已有三种"活跃度陷阱"，判据各不相同：

| 情形 | 现象 | 判据 | 结论 |
| --- | --- | --- | --- |
| 抓取缓存（见 [a-stock-data](a-stock-data-A股全栈数据工具包.md)） | 元数据与 README 都偏旧 | 加 `?cachebust=` 参数重抓即恢复 | 数据没变，**是抓取层的问题** |
| 真陈旧（见 [the-book-of-secret-knowledge](the-book-of-secret-knowledge-信息安全与运维清单集.md)） | `pushed_at` 停在两年前 | commits 接口确认最新提交确为两年前 | **确实停更** |
| **本项目的"假活跃"** | `pushed_at` 很新，但默认分支不动 | **分别查 `pushed_at` 与默认分支的 commits**，再看 branches 分布 | **有推送，但没推到主线** |

结论：**`pushed_at` 只代表"某个分支被动过"，不代表主线在更新**。判断维护状态至少要拿"默认分支的 commits 时间"来对。

## 使用前需要注意

- **我只读了文档、目录结构与元数据**：没有安装、没有构建、没有在浏览器里实际压缩过任何图片，"画质与体积表现"不在本次核验范围内。
- **隐私要按两层看**：图片不上传（README 明写），但 Google Analytics 会收到访问数据与**压缩前后体积数值**、PWA 安装信息。处理涉密素材时，"图不出本地"成立，但"没有任何上报"不成立。
- **默认分支是 `dev`**：想对比版本、抄构建配置或提 PR 时，注意别默认去 `main`（不存在）。
- **仓库体积约 175 MB 且含二进制产物**：克隆慢；如果要自己重新编译编解码器，需要 emscripten 与 Rust 两套工具链（仓库提供了 Dockerfile）。
- **维护状态需要自己确认**：默认分支已约两年无提交。对"压缩工具"这类功能完整的应用，停更不一定等于不能用，但**浏览器新格式支持、依赖安全更新与上游编解码器升级不会自动跟进来**。
- **`topics` 为空**：与 [the-book-of-secret-knowledge](the-book-of-secret-knowledge-信息安全与运维清单集.md) 那样把 19 个 topics 填满的做法相反，这个仓库基本没做 GitHub 侧的检索优化——搜"image compression"未必能命中它。

## 什么时候会再看

- 需要**不上传素材**就把图片压到网页可用体积时。
- 想找**浏览器内 WASM 编解码器**的现成实现，或参考"C/Rust 编译成 WASM + TypeScript 加载"的工程组织方式时。
- 需要复盘"**`pushed_at` 新 ≠ 主线在维护**"这个判断陷阱时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"本地处理文件"的工具，边界写法不同**：[markitdown](markitdown-文件转Markdown工具.md)——那篇的坑是 OCR 插件在未配置时会**静默降级**；本篇则是在"本地"之后主动补了"但仍会收集哪些数据"，是更值得照抄的写法。
- **同为"把重计算搬进浏览器"**：[ra2](ra2-浏览器运行红色警戒2.md)——那篇是 x86 模拟 + 图形兼容层，本篇是 WASM 编解码器；两者都不依赖后端，可对照浏览器内重型负载的两种路线。
- **同类工具的收录位置**：[the-book-of-secret-knowledge](the-book-of-secret-knowledge-信息安全与运维清单集.md) 的 "Web Tools" 一章，正是这类在线工具被集中收录的地方。
- **为正相反的目标优化**：[nibnib](nibnib-为极快解压设计的4bpp图片格式.md)——那是为"解压速度"定制的 4bpp 格式（给嵌入式显示用，编码慢几个数量级也不在乎），本篇是通用压缩工具求好用与高压缩比；可对照"压缩率优先"与"解压速度优先"两条路线。

## 参考来源

- 项目仓库与 README：https://github.com/GoogleChromeLabs/squoosh
- 编解码器目录：https://github.com/GoogleChromeLabs/squoosh/tree/dev/codecs
- 许可证文件：https://raw.githubusercontent.com/GoogleChromeLabs/squoosh/dev/LICENSE
- 提交与分支（用于核对活跃度）：`https://api.github.com/repos/GoogleChromeLabs/squoosh/commits`、`/branches`
- 元数据（Star / Fork / 时间 / 许可证 / 体积 / 默认分支）：GitHub REST API，2026-11-03

以上信息核验于 2026-11-03。未安装、未构建、未实际压缩图片；官网 https://squoosh.app 本次未访问。
