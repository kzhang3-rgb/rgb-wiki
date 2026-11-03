# aerial-tv：给国产电视的 tvOS 风格桌面

> 记录这个"给国产电视换一块干净桌面"的开源 Android TV 桌面：它怎么用无障碍服务在只认自家桌面的固件上抢回主页键，以及安装它必须先打开 ADB 这件事背后要看清的权限范围。

- 状态：草稿
- 最近核验：2026-11-03（阅读 README 全文与 GitHub API 元数据，并用默认分支 `main` 的 commits 接口复核活跃度；未下载 APK、未安装到电视、未运行）
- 适用范围：完整版需 Android TV 5.0+；Android 4.0–4.4 用 Aerial Lite（测试版）；安装需一台电脑 + 开 ADB，或用 U 盘

## 核心结论

参照 Apple TV 做的 Android TV 桌面替代品：**没有广告、没有推荐流**，只有你的 App、壁纸和航拍屏保。适合小米、TCL、创维、索尼等国产电视，也能装在盒子与投影仪上。

**它解决的核心矛盾**：国产电视大多只认自家桌面，待机时还会清理第三方 App。Aerial 的做法是用一个**无障碍服务做"桌面守护"**——原桌面一出现在前台就切回来；被系统清理后再靠**通知监听**让系统把它拉起来。README 对这两项的说明是"只看哪个窗口在前台，不读屏幕内容""不读通知"。

所以它换的只是**桌面这一层**：原来的桌面、会员、小爱同学、小米账号、有线电视都还在（小米影视和 PatchWall 从"桌面"磁贴进）。**开机广告是固件播的，去不掉。**

## 项目信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [pengchujin/aerial-tv](https://github.com/pengchujin/aerial-tv) |
| 描述 | "给国产电视的一块干净桌面：参照 tvOS 设计的开源 Android TV 桌面，没有广告，开机自动接管" |
| 官网 | https://aerial.qust.me |
| 许可证 | MIT |
| 主语言 | Kotlin |
| 关注度 | **9,167 Star、781 Fork、16 关注、381 个开放 Issue**（GitHub API，2026-11-03） |
| 维护状态 | 2026-10-20 创建；`pushed_at` 2026-11-03；**默认分支 `main` 最新提交在 2026-11-03**（核验当天）→ 判定为**真实活跃** |
| 体积 | 约 11 MB（API `size` 11,284 KB） |
| 包名 | 完整版 `app.orchard.tv`，Lite 版 `app.orchard.tv.lite` |
| 无 topics | 无话题标签；无 GitHub Pages，无 Discussions |

## 它能做什么

- **首页**：常用的一排 App 放在屏幕下方的玻璃底板上，往下是全部 App；小米"应用中心""米家"这类藏起来的系统 App 也会列出。
- **整理**：长按 OK（或菜单键）移动、放进文件夹、隐藏、卸载；固定到主屏的 HDMI 可以选接的是 Apple TV / PlayStation / Switch / Xbox / 电脑，换成对应图标。
- **用手机整理**：扫电视上的二维码，手机上就是和电视一样的图标网格，可拖动排序、改名、换图标、放进文件夹或隐藏，也能把信号源加到主屏幕。
- **控制中心**：第一排按上键到右上角打开，含信号源、网络、设置、音量、亮度、个性化。
- **屏保**：Apple TV 的 114 段航拍影片，电视支持时播 4K 或 4K 杜比视界；也可换成照片、时钟或手机添加的本地视频。
- **壁纸**：必应每日精选、14 张内置壁纸、手机扫码传的照片，或"航拍"（停在主屏 10 秒后在磁贴后面播放）。
- **个性化**：单排或双排、时钟大小与位置、App 名称、焦点样式、按键声音都能改。

## 安装

**不用 root，不动系统应用，随时能还原。** 电脑和电视要在同一个网络。完整流程是"开 ADB → 装 APK → 授权 → 设为主屏幕"。

**第一步：打开电视的 ADB 调试**（README 给了分品牌步骤，摘要）：

| 电视 | 怎么打开 |
| --- | --- |
| 小米 / Redmi | 设置 → 关于 → 连按「产品型号/产品类型」进开发者模式，再到 设置 → 账号与安全 打开「ADB 调试」与「安装未知来源应用」 |
| TCL / 雷鸟 | 设置 → 关于 → 本机信息，快速按遥控器「上下左右」，在底部「更多」里打开 ADB |
| 海尔 / 模卡 | 按电视机身上的信号源键，再用遥控器按「左左上右」，在工厂菜单里打开「ADB 调试」 |
| OPPO（含 S1） | 关于本机 → 设备型号，按「上上右右」再确认；再到 开发者选项 打开「USB 调试」 |
| 其他 Android TV | 开发者选项里连按「内部版本号」7 次，再打开「USB 调试」 |

**Android 11 及以上**的无线调试要先配对（索尼 BRAVIA Android 14、Google TV 等），不能直接 `adb connect <IP>:5555`——要先 `adb pair`：

```bash
adb pair 192.168.1.20:37115      # 电视「使用配对码配对设备」屏幕上的端口
# 提示 Enter pairing code 时输入电视上的 6 位配对码
adb connect 192.168.1.20:39201   # 无线调试主页面上的另一组 IP:端口
```

**第二步：安装**（README 把"交给 Agent 装"列为推荐路径，也提供一行命令与手动 adb）：

```bash
# macOS / Linux 一行命令（KEEP_ADB=1 让小米待机后自动重开 ADB，方便日后升级）
curl -fsSL https://aerial.qust.me/install.sh | KEEP_ADB=1 TV=192.168.1.20 bash
```

```bash
# 手动
adb connect 192.168.1.20:5555
adb install -r aerial-standard.apk
adb shell pm grant app.orchard.tv android.permission.WRITE_SECURE_SETTINGS
adb shell cmd notification allow_listener app.orchard.tv/app.orchard.tv.guard.RevivalListener
adb shell appops set app.orchard.tv SYSTEM_ALERT_WINDOW allow
adb shell am start -n app.orchard.tv/.MainActivity
```

三个 APK 的用途：`aerial-standard.apk`（Android 5.0+，先装这个）、`aerial-guard.apk`（standard 装不上或提示"不允许安装桌面类 App"时）、`aerial-lite.apk`（Android 4.0–4.4）。

**还原**：`curl -fsSL https://aerial.qust.me/install.sh | TV=<电视IP> bash -s restore`，加 `--uninstall` 会顺便卸载；或电视上 设置 → 应用 → Aerial → 清除默认设置。

**Aerial Lite**：Android 4.0–4.4 的老电视/盒子装不了完整版（界面框架最低 Android 5.0），所以有独立 Lite。**Lite 目前是第一版（1.0）、属测试版**，只在 Android 4.0.4 / 4.4.4 虚拟机和两台新电视上测过，没在真实 4.x 电视上用过。Lite 没有桌面守护（无障碍接管）、航拍壁纸与信号源磁贴，开机与主页键全靠系统默认桌面。

## 使用前需要注意

**① 它靠系统级权限工作，权限范围要先看清。** 接管桌面用到：**无障碍服务**（桌面守护，README 称只看前台窗口、不读屏幕内容）、**通知使用权**（被清理后重新拉起，README 称不读通知）、**`WRITE_SECURE_SETTINGS`**（调亮度用）、**`SYSTEM_ALERT_WINDOW`**（悬浮窗）。这些都靠上面那几条 `adb shell` 命令授予。**README 的"不读屏幕内容/不读通知"是项目自述，我没有审计源码验证。** 这类权限本身能力很大，装之前建议自己判断是否接受。

**② 它不是每台电视都能接管。** README 自带一张按品牌/机型的兼容表，标了 ✅ 能用 / ⚠️ 能用但有限制 / ❌ 接管不了（数据大多来自用户反馈）。例如创维 7T641、75A6E、55A5F Pro 是 ❌ 黑屏或卡死；雷鸟 55S595C Ultra、鹤 6 2025 Ultra 能装但接管不了主页键；小米电视 2 只能用 Lite 且当不了桌面。**买电视前先查表，装之前先确认自己的型号在不在 ✅。**

**③ Lite 是测试版。** 如上，真实 4.x 电视上没测过，可能有不少小问题。

**④ 380+ 个开放 Issue。** 项目 2026-10-20 才创建、两周涨到 9 千多 Star，Issue 积压很多——高关注度不等于问题都被解决。

**⑤ 未安装、未验证。** 我没有下载 APK、没有装到任何电视、没有运行，功能与兼容性全部来自 README 自述。

## 什么时候会再看

- 想给国产电视换一块无广告、无推荐流、开机直达的桌面时。
- 想参考"用无障碍服务在只认自家桌面的受限固件上抢回主页键"这套工程做法时。
- 想参考"分品牌 ADB 开启步骤 + 机型兼容表 + 给 Agent 的安装提示词"怎么写成一页可复制文档时。

## 相关笔记

- **同为"电视 / 客厅场景"**：[boxplayer：聚合网盘与影视的全平台播放器](boxplayer-聚合网盘与影视的全平台播放器.md)——那是装在各端的媒体播放器（含 Android TV），本篇是改造电视桌面本身；可对照两类"电视端"需求的解决方式。
- 索引位置：[GitHub 项目收藏](README.md)。
- **同为"需要系统级权限的安卓应用"**：[dsh-Go](dsh-Go-DeepSeek-Harness的安卓客户端.md)——那是靠无障碍 / Shizuku / ADB 做手机操控，本篇靠无障碍服务接管默认桌面；两篇都该先看清"权限范围 + 数据流向"再决定装不装。

## 参考来源

- 项目仓库与 README：https://github.com/pengchujin/aerial-tv
- 官网（下载与安装脚本）：https://aerial.qust.me
- 分品牌 ADB 说明：仓库 `docs/devices/adb-by-brand.md`
- 给 Agent 的安装说明：https://aerial.qust.me/agent.md
- 元数据（Star / Fork / 订阅数 / 体积 / 许可证）：GitHub REST API，2026-11-03

以上信息核验于 2026-11-03。未下载 APK、未安装、未运行，兼容性与权限行为均未实测。
