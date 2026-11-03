# markitdown：把各种文件转成 Markdown 的 Python 工具

> 记录它的定位边界（给 LLM 读，不是给人看）与那条被写在最显眼处的安全提醒——它会以当前进程的权限访问资源。

- 状态：草稿
- 最近核验：2026-10-30（阅读 README、安全说明与 GitHub API 元数据，未安装、未转换任何文件）
- 适用范围：Python 3.10–3.14；命令行与 Python API；支持 Docker 运行

## 核心结论

微软开源的**轻量文件转 Markdown 工具**，用途明确：把各种文件转成 Markdown 供 LLM 和文本分析管线消费。README 把它和 `textract` 类比，区别在于**它专注保留文档结构**——标题、列表、表格、链接尽量以 Markdown 形式落下来。

作者对边界的交代很干脆：**"输出常常是像样的、人类可读的，但它是给文本分析工具消费的，不一定是对人类消费来说保真度最高的选择。"** 换句话说，别拿它当排版级转换器。

支持的范围相当广：PDF、PowerPoint、Word、Excel、**图片（EXIF 元数据 + OCR）**、**音频（EXIF 元数据 + 语音转写）**、HTML、CSV/JSON/XML 等文本格式、**ZIP（遍历内容）**、**YouTube URL**、**EPub** 等。

README 最上方放了一条 IMPORTANT 级别的安全提醒，而不是放在文档深处——**它像 `open()` 或 `requests.get()` 一样，以当前进程的权限做 I/O**，会访问进程自己能访问到的一切资源。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [microsoft/markitdown](https://github.com/microsoft/markitdown) |
| 组织 | `microsoft`（官方仓库） |
| 许可证 | MIT |
| 语言 / 版本要求 | Python 3.10–3.14 |
| 关注度 | **193,424 Star**、14,503 Fork、589 关注、783 个开放 Issue（GitHub API，2026-10-30） |
| 维护状态 | 2024-11-13 创建，最近推送 2026-10-28 |
| 版本 | 仓库有 Tag（核验时最新 `v0.1.9`），但**无 GitHub Release 记录，发布走 PyPI** |
| 体积 | 约 5.9 MB（`size` 字段，不含 Git 历史） |
| Topics | markdown、pdf、microsoft-office、openai、langchain、autogen、autogen-extension |

> 关注度说明：这是目前收藏夹里 Star 数最高的项目之一，数值按 GitHub API 在核验日返回，本轮未与页面显示值交叉比对。

## 安装与用法

```bash
pip install 'markitdown[all]'

markitdown path-to-file.pdf > document.md
markitdown path-to-file.pdf -o document.md
cat path-to-file.pdf | markitdown      # 支持管道
```

**按需安装可选依赖**，不必一次装全（`[all]` 会把所有格式依赖都拉下来）：

```bash
pip install 'markitdown[pdf, docx, pptx]'
```

现有可选依赖：`[all]`、`[pptx]`、`[docx]`、`[xlsx]`、`[xls]`、`[pdf]`、`[outlook]`、`[az-doc-intel]`、`[az-content-understanding]`、`[audio-transcription]`、`[youtube-transcription]`。

Docker 方式：

```sh
docker build -t markitdown:latest .
docker run --rm -i markitdown:latest < ~/your-file.pdf > output.md
```

## 三个"云端增强"档位

README 花了不少篇幅对比三条路线，这张表是全文信息密度最高的部分：

| 能力 | 内置转换器 | Azure Document Intelligence | Azure Content Understanding |
| --- | --- | --- | --- |
| 文档转换 | 离线、按格式各自解析 | 云端版面分析 | 云端多模态抽取 |
| 结构化字段 | 不支持 | 模型或查询字段 → YAML front matter | 分析器字段 → YAML front matter |
| 自定义分析器/模型 | 不支持 | 支持（`docintel_model_id`） | 支持（`cu_analyzer_id`） |
| 音频与视频 | 基础音频，**无视频** | 不支持 | 音频与视频分析器 |
| 成本 | 仅本地算力 | 按 Azure API 计费 | 按 Azure API 计费 |

要点：

- **视频只有 Content Understanding 一条路**；音频的高质量方案也只有 CU（内置只有基础转写）。
- **Document Intelligence**：`AZURE_API_KEY` 未设时回落到 `DefaultAzureCredential`；`--docintel-features` 的可用值有 `barcodes / formulas / keyValuePairs / languages / ocrHighResolution / styleFont`，**默认全不开**（想显式关掉可写 `--docintel-features none`）；其中 `formulas`、`ocrHighResolution`、`styleFont` 与 `queryFields` **会额外计费，且 queryFields 的计价方式与其它附加项不同**。
- **Content Understanding**：一个 `cu_endpoint` 自动按文件类型路由到对应 prebuilt 分析器（文档 → `prebuilt-documentSearch`，视频 → `prebuilt-videoSearch`，音频 → `prebuilt-audioSearch`）；**每次 `convert()` 命中 CU 的可计费格式就是一次计费 API 调用**，可用 `cu_file_types` 把 CU 限制在指定格式（例如只让 PDF 走 CU）；设置 `cu_analyzer_id` 后会自动按分析器的模态限定适用范围，不兼容的类型回落默认分析器。

## 插件与 OCR

**插件默认关闭**（这是刻意的，不是遗忘）：

```bash
markitdown --list-plugins
markitdown --use-plugins path-to-file.pdf
```

第三方插件在 GitHub 上用 `#markitdown-plugin` 标签聚合，模板见 `packages/markitdown-sample-plugin`。官方还提供 **`markitdown-ocr`** 插件，给 PDF/DOCX/PPTX/XLSX 加 OCR（走 LLM Vision，复用已有的 `llm_client` / `llm_model`，**不需要新增 ML 库或二进制依赖**）。

**这里有个容易踩的静默行为**：`markitdown-ocr` 在**未提供 `llm_client` 时仍会加载，但会静默跳过 OCR**，回落到内置转换器——**你不会报错，只会拿到没有 OCR 的结果**。

图像描述也用同一套 LLM 接口（目前仅 pptx 与图片文件支持）：

```python
from markitdown import MarkItDown
from openai import OpenAI

client = OpenAI(max_retries=5)
md = MarkItDown(llm_client=client, llm_model="gpt-4o", llm_prompt="optional custom prompt")
result = md.convert("example.jpg")
print(result.markdown)
```

`max_retries` 默认 2，设成 5 表示最多 6 次尝试并退避重试。**失败后的行为值得注意**：若客户端耗尽重试或遇到不可重试错误，MarkItDown 会去尝试其它可用转换器，**只有全部失败才抛 `FileConversionException`**——好处是鲁棒，代价是"最终成功的那次可能并不是你想要的那条转换路径"。

## 使用前需要注意

- **安全边界（README 用 IMPORTANT 标注的那条）**：它执行 I/O 用的是当前进程权限，等价于 `open()` / `requests.get()`。**在托管或多用户场景里绝不能把不受信任的输入直接交给它**——README 明确要求自己做校验与限制：限定文件路径、限制 URI scheme 与网络目标、**阻断对私有地址、回环地址、链路本地地址以及元数据服务地址的访问**（这是典型的 SSRF 风险面，云环境的元数据地址尤其敏感）。
- **调用最窄的转换方法**：`convert()` 是刻意宽松的（能处理本地文件、远程 URI、字节流）。只读本地文件就用 `convert_local()`；想自己控制网络请求就用 `requests.get()` 拿响应再传给 `convert_response()`；要最大控制权就用 `convert_stream()`。
- **它不是高保真转换器**：输出面向 LLM 消费，**不要用它生成给人看的正式文档**。
- **云端档位会花钱，且默认不省钱**：CU 的每次 convert 都可能计费，DI 的部分 feature 与 queryFields 也额外计费——**上线前用 `cu_file_types` / `--docintel-features` 把计费面收窄**。
- **Python 版本有上下限**：README 写的是 3.10–3.14，不是"3.10 以上"。
- **`[all]` 会拉很多依赖**：按格式装能显著减小依赖面与冲突概率。
- **贡献有硬边界**：仓库明确 **不接受** Web 服务、REST/HTTP API、Web 前端、桌面与移动应用（维持生态周边需另建独立包），并且**首次贡献需要签 CLA**。`markitdown-mcp` 包属于在范围内。
- **我只读了 README 与元数据，没有安装、没有转换任何文件**：转换保真度、依赖冲突、各云端档位的实际效果均未验证。

## 什么时候会再看

- 需要**把 PDF / Office / 图片 / 音频统一转成 Markdown 再喂给 LLM**时：这是目前最省事的入口，且能按格式装依赖。
- 需要**把本地文档资料转成 Markdown 存进知识库**（比如把教程 PDF 转成可搜索文本）时。
- 想参考**"云端增强"怎么既不牺牲离线可用、又能接云服务**时：内置 + DI + CU 三档并行、按格式开关、计费面可控，这个设计值得抄。
- 想参考**"安全提醒写在最前面 + 给出更窄的 API 替代"**的做法时：README 顶部的 IMPORTANT 与 Security Considerations 一节，把"怎么用得更安全"直接落在 API 选择上（`convert_local` / `convert_response` / `convert_stream`）。
- 想参考**"一个开源项目怎么清晰划出 in scope / out of scope 并引导周边另建包"**时：What to Contribute 一节是范本。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- 同属 MCP 体系：本仓库同时包含 `markitdown-mcp` 包（在贡献范围内），与 [servers](servers-MCP官方参考服务器合集.md)（官方参考服务器）及 [java-sdk](java-sdk-MCP官方JavaSDK.md)（MCP Java SDK）构成"文档转 Markdown → 经 MCP 提供给模型"的一条链路。
- 同为文档格式处理的笔记：[epublib](epublib-读写EPUB的Java库.md)——MarkItDown 也支持 EPub 转换，但那是"转成 Markdown 供模型读"，epublib 是"程序化读写 EPUB 文件"，用途不同可对照。
- 可作为输入素材的笔记：[Foundations-of-LLMs](Foundations-of-LLMs-大模型基础教材.md)——该仓库提供 PDF 教材，当时的笔记标注了"未阅读 PDF 正文"，用 MarkItDown 转换正好补上这个缺口。
- **同为"本地处理文件"的工具**：[squoosh](squoosh-浏览器内的图片压缩工具.md)——那是浏览器内的图片压缩（WASM 编解码器、图片不上传），本篇是服务端的文档转 Markdown；两者的坑刚好互补：本篇要防"插件静默降级"，那篇要看"本地之后还上报了什么"。

## 参考来源

- 项目仓库与 README：https://github.com/microsoft/markitdown
- PyPI 包与版本：https://pypi.org/project/markitdown/
- Azure Document Intelligence 附加能力与定价：https://learn.microsoft.com/azure/ai-services/document-intelligence/concept/add-on-capabilities 、https://azure.microsoft.com/pricing/details/document-intelligence/
- Azure Content Understanding：https://learn.microsoft.com/azure/ai-services/content-understanding/
- 元数据（Star / Fork / Tag / 时间 / 许可证）：GitHub REST API，2026-10-30

以上信息核验于 2026-10-30。未安装、未运行，转换效果、依赖情况与云端档位表现均未验证。
