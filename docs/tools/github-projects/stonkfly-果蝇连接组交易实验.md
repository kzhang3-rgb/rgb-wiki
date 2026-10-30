# stonkfly：果蝇连接组交易实验

> 记录它到底做了什么（真实连接组仿真 + 真实下单权限），以及 README 里那些少见的克制声明。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README、目录结构与 GitHub API 元数据，未安装运行）
- 适用范围：Python 3.11 + C++17 编译器，macOS / Linux；需数 GB 数据集空间，建议 16GB 内存

## 核心结论

把**真实果蝇连接组**接上去做加密货币交易的一个实验项目：用 Coinbase 的公开行情生成一张 RGB 图，去刺激仿真大脑的亮度和颜色输入，再由固定的神经读出决定买、卖还是持有，最后通过 Coinbase AgentKit 真实下单。

仿真的规模是 MaleCNS v1.0 图谱：**166,700 个神经元、2,560 万条连接**，输入为 3,335 个亮度通道和 811 个 R8 颜色通道（果蝇复眼里的 R8 光感受器）。

**关键的是作者的自我声明**：README 第二行就写明"盈利性学习尚未被证明"（Profitable learning has not been demonstrated）。它明确区分了"工程化的强化信号"和"模拟的痛觉受体"，并指出突触变化不等于证明了它能学会赚钱。这种克制的写法在同类项目里很少见，也是这个仓库最值得记的地方。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [nftechie/stonkfly](https://github.com/nftechie/stonkfly) |
| 作者 | Alex Wormuth（nftechie） |
| 许可证 | MIT |
| 语言 | Python 96.1%、C++ 3.9% |
| 关注度 | 897 Star、168 Fork、8 人关注（2026-10-30 的 API 数据） |
| 维护状态 | 2026-09-10 创建，**仅 1 次提交**，此后再未推送；10 个开放 Issue |
| 其他 | 1 个分支、无 Tag、无 Release |

> 数据源说明：网页显示 0 Star / 0 Fork，但页面内容停留在 2026-09-10（创建当天）的快照；API 的 `updated_at` 是 2026-10-29。按收藏专题的约定采用 API 数据。

## 数据流

```text
Coinbase 公开行情 → RGB 图 → 3,335 亮度输入 + 811 R8 颜色输入
   → MaleCNS v1.0 连接组（166,700 神经元 / 2,560 万连接）
   → 固定神经读出 → 买 / 卖 / 持有
   → Coinbase AgentKit ActionProvider（检查限额）→ Coinbase Advanced 现货下单

盈亏反馈：盈利 → 刺激 15 个已识别的 PAM11 多巴胺细胞
          亏损 → 刺激 2 个 PPL101 厌恶型多巴胺细胞
记忆规则：候选规则修改已有的 KC-to-MBON 连接
```

README 特别澄清：这些是**工程化的强化信号，不是模拟的痛觉受体**；突触层面的变化并不能证明它学会了盈利交易。

## 怎么运行

需要 Python 3.11、C++17 编译器和 macOS / Linux：

```bash
python3.11 -m venv .venv
source .venv/bin/activate
pip install -e '.[test]'
python -m stonkfly prepare
python -m stonkfly run
```

**默认是纸上交易**：用真实的 Coinbase 公开数据，模拟余额 100 美元，不需要任何 API Key。日志、感官图像和可续跑的大脑状态写在 `runs/paper/`。Ctrl-C 停止，同一条命令可以接着跑。

## 真实下单的安全默认值

如果要接真实账户，README 要求先建一个**专用的 Coinbase Advanced 组合**，里面最多放 100 USDC，并创建仅限该组合、权限为 View + Trade（不含 Transfer）的 ECDSA API Key，然后自己执行：

```bash
python -m stonkfly run --live --preflight-only
python -m stonkfly run --live
```

| 默认限制 | 值 |
| --- | --- |
| 单笔最大下单额 | 10 美元（含预留手续费） |
| 每日下单尝试 | 最多 24 次 |
| 做空与杠杆 | 均不启用 |
| 回撤熔断 | 回撤 20 美元后停止新订单 |

**注意最后一条的实际含义**：20 美元回撤只是"停止开新仓"，README 明确写了它**不会平仓、也不构成后续亏损的上限**。持仓下跌时它不替你止损。

## 使用前需要注意

- **这是实验，不是交易工具**。作者自己声明盈利性学习尚未被证明；仓库不含任何资金，也不会连接任何人的账户，真实下单必须由你自己配置凭证并显式开启。
- **签名的 API Key 一旦泄露风险极高**：虽然 README 建议只用 View + Trade、不给 Transfer 权限，但交易权限本身足以清空组合内的资金。建议严格按它说的做法——独立组合、小额上限。
- **硬件与数据成本不低**：需要 C++ 编译环境、数 GB 空间，建议 16GB 内存，准备数据那一步耗时较长。
- **只有 1 次提交、无 Release、无更新**，且我没看到任何长期运行结果或收益数据。10 个开放 Issue 说明有人反馈问题，但没有形成维护节奏。
- **不要用"神经网络 + 真实资金"这类叙事来判断有效性**：连接组规模大、生物保真度高，和"能预测价格"是两件不同的事。作者在这一点上反而说得比大多数项目清楚。
- 我未安装运行，仿真是否可复现、下单限额是否按声明生效均未验证。

## 什么时候会再看

- 想参考"如何把生物连接组仿真接到真实外部系统"的工程做法时。
- 需要学习怎样在 README 里诚实标注"未验证""不构成证明"的写法时——这个仓库是很好的范例。
- 想了解 Coinbase AgentKit 的 ActionProvider 写法或论文交易/实盘切换设计时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。
- **同一套连接组、不同任务**：[FlyCoder](FlyCoder-果蝇连接组控制CSS.md)——同样基于 MaleCNS 的 166,700 个神经元，把它接到 CSS 布局任务上；两篇都主动声明"没有证明它能学会目标技能"，可对照这两种实验记录写法。

## 参考来源

- 项目仓库与 README：https://github.com/nftechie/stonkfly
- 模型与证据说明：https://github.com/nftechie/stonkfly/blob/main/docs/model.md
- 运行与恢复说明：https://github.com/nftechie/stonkfly/blob/main/docs/operations.md

以上信息核验于 2026-10-30，Star 数与仓库内容可能变化。未安装运行，效果与安全性均未验证。
