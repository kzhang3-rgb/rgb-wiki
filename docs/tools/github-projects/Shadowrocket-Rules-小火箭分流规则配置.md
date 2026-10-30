# Shadowrocket-Rules：小火箭分流规则配置

> 一份开箱即用的 Shadowrocket 规则配置，记录它的分流思路和上游来源。节点需要自备，本笔记未在设备上实际导入验证。

- 状态：草稿
- 最近核验：2026-10-30（阅读仓库 README 与 GitHub API 元数据，未在 iOS 设备上导入使用）
- 适用范围：iOS 上的 Shadowrocket；另有同作者的 Clash 版本

## 核心结论

这是一份按服务分流的 Shadowrocket 配置，导入一条 Raw 链接即可使用，不自带节点或订阅，需要自己添加。它的价值主要在于已经把常见服务分好类并分配了默认出口：谷歌走日本、AI 服务走美国、香港券商走香港、国内服务和苹果服务直连，兜底走代理。

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [LingJingMaster/Shadowrocket-Rules](https://github.com/LingJingMaster/Shadowrocket-Rules) |
| 许可证 | MIT |
| 内容形态 | 纯规则与配置文件，没有程序代码 |
| 关注度 | 1302 Star、153 Fork（2026-10-30 的 API 快照，会变动） |
| 维护状态 | 2026-02-26 创建，最近一次推送在 2026-10-21，仍在更新 |
| 配套项目 | 同作者的 [Clash-Rules](https://github.com/LingJingMaster/Clash-Rules) |

## 使用方式

仓库给出的导入流程：

1. 复制配置的 Raw 链接（默认分支为 `main`，文件为 `Shadowrocket.conf`）。
2. 打开 Shadowrocket → 配置 → 右上角 `+` → 粘贴链接 → 下载。
3. 点击已下载的配置，设为使用中。
4. 在首页添加自己的节点或订阅。
5. 做连通性测试，选择可用节点连接。

```text
https://raw.githubusercontent.com/LingJingMaster/Shadowrocket-Rules/refs/heads/main/Shadowrocket.conf
```

仓库也提供了二维码，可扫码导入。

## 分流设计

配置共 19 条优先级规则，比较特别的是这几条：

- DNS 防泄露（HTTPDNS）默认 REJECT。
- 谷歌服务（含 Gemini）默认走日本节点，可手动切香港。
- AI 服务（ChatGPT、Claude、苹果智能等）默认走美国节点。
- 券商服务（富途、moomoo、长桥、老虎、雪盈、盈透等）默认走香港节点。
- 香港银行与汇丰香港默认直连，哔哩哔哩、苹果服务、私有网络也直连。
- 最后是 GEOIP CN 直连和"漏网之鱼"兜底走代理。

邮件服务默认走 PROXY，可在 DIRECT 和地区节点之间切换。

## 规则集来源

上游规则来自公开项目，而不是全部自写：

- [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)：主要规则集。
- [iab0x00/ProxyRules](https://github.com/iab0x00/ProxyRules)：AI 服务补充规则。
- 自维护的 `Mail.list`、`Apple.list`、`HK_Broker.list`、`HSBC_HK.list`、`HK_Banks_Direct.list`：补充邮件协议端点、苹果 CDN 直连域名、券商域名与交易 IP 段、香港银行域名。

这意味着规则的准确性依赖上游项目的更新。

## 使用前需要注意

- 项目只提供规则，不提供节点和订阅，也没有机场推荐，需要自己准备。
- 默认出口是按作者的使用场景设定的（日本、香港、美国节点），换成自己的节点后需要重新调整策略组。
- 这类规则集依赖上游更新，建议定期更新配置，否则新增服务的域名可能落进兜底规则。
- 我未在 iOS 设备上导入过，配置与当前 Shadowrocket 版本的兼容性、各默认策略的实际效果都没有验证。

## 什么时候会再看

- 需要在 iOS 上把 AI、谷歌、券商、香港银行分开走不同出口时。
- 想参考它的分流优先级排序方式，或需要同一套规则的 Clash 版本时。

## 相关笔记

- 索引位置：[GitHub 项目收藏](README.md)。

## 参考来源

- 项目仓库与 README：https://github.com/LingJingMaster/Shadowrocket-Rules
- Clash 版本仓库：https://github.com/LingJingMaster/Clash-Rules
- 主要上游规则集：https://github.com/blackmatrix7/ios_rule_script

以上信息核验于 2026-10-30，Star 数与规则内容可能变化。未实际导入使用，效果未经验证。
