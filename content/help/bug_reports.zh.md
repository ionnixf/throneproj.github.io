+++
title = "报告 Bug"
description = "需要先检查什么、如何收集日志和诊断信息，以及 issue 中应写些什么。"
weight = 30
toc = true
+++

在 GitHub 上报告 Bug：

- Throne 桌面版：[github.com/throneproj/Throne/issues](https://github.com/throneproj/Throne/issues)
- Throne for Android：[github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues)

关于如何使用 Throne 桌面版的问题，请使用 [GitHub Discussions 的 Q&A 分类](https://github.com/throneproj/Throne/discussions/categories/q-a)，而不是提交 issue。

{% alert_warning() %}
GitHub 上的 issue 都是公开的。发布之前，请从日志、截图和配置中删除订阅 URL、令牌、密码、UUID、私钥和服务器地址。Throne 桌面版不会替你删除它们，而且订阅更新失败时，完整的订阅 URL 会被写入日志。如果你不小心发布了机密信息，请让服务商将其重置。
{% end %}

## 报告之前 {#before}

1. 通过 `程序`（Program）→ `检查更新`（Check For Update）或从[下载](@/downloads.zh.md)页面更新 Throne。
2. 在未关闭和已关闭的 issue 中搜索你的错误信息，并阅读[常见问题](@/help/faq.zh.md)和[故障排除](@/help/troubleshooting.zh.md)。
3. 用只包含重现问题所需内容的[全新配置](@/reference/files.zh.md#reset)进行测试，并在报告中写明问题在全新配置下是否也会出现。
4. 如果你使用的是 WinGet、Scoop、AUR 或 Nix 软件包，也请测试官方版本；开发者不为这些软件包提供支持。
5. 如果只有一个服务器无法使用，请在其他应用中测试它。如果在其他应用中也失败，请联系你的服务商。

## 收集日志 {#logs}

### 桌面版 {#desktop-logs}

主窗口底部的 `日志`（Logs）标签页会显示日志，Throne 还会将相同的内容写入配置文件夹中的 `logs/throne.log`（`设置`（Settings）→ `打开配置文件夹`（Open Config Folder））。每次启动 Throne 时，该文件都会重新开始记录，因此请在重启之前复制它。

要记录详细日志：

1. 在 `设置` → `基本设置`（Basic Settings）→ `日志`（Logging）中（仅在高级模式下可用，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)），将 `Sing-box 日志级别`（Sing-box Log level）设为 `debug`；对于 Xray 配置档，还需将 `Xray 日志级别`（Xray Log level）设为 `debug`。
2. 调高 `最多日志行数`（Max log lines），例如设为 `2000`，以免 Throne 丢弃日志行。
3. 点击 `确定`（OK），然后通过 `程序` → `重启程序`（Restart Program）重启 Throne。
4. 重现问题，并附上 `logs/throne.log`。如果改为从 `日志` 标签页复制文本，请先关闭它的筛选栏。
5. 将日志级别改回 `info`（sing-box）和 `warning`（Xray）。

崩溃后，请附上 `logs/crashed-<date>-<time>.log`；在 Windows 上，还要附上 `crashes` 文件夹中的 `.txt` 和 `.dmp` 文件。

### Android {#android-logs}

打开侧边菜单，点按 `日志`（Logs）。保持 `隐藏敏感数据`（Hide sensitive data）开启（默认开启），然后点按 `分享日志`（Share logs）或 `保存日志…`（Save logs…）。如需详细日志，请先将 `设置`（Settings）→ `核心`（Core）→ `日志级别`（Log level）设为 `debug`（对于 Xray 配置，还要设置 `Xray 日志级别`（Xray log level））。参见 [Android 故障排除](@/android/troubleshooting.zh.md#logs)。

## 诊断 {#diagnostics}

对于卡死、无响应和 CPU 占用过高的问题，开发者可能会要求你提供诊断文件，即核心在 30 秒内运行情况的记录。

1. 让 Throne 进入出现问题的状态，例如等待它卡住。不要重启 Throne。
2. 在 `设置` → `基本设置` → `诊断`（Diagnostics）中，勾选开发者要求的选项：`锁竞争`（Lock contention）、`执行追踪 (大文件)`（Execution trace (larger file)）或 `包含 Throne 日志`（Include Throne logs）。
3. 点击 `启动`（Start），然后等待 30 秒。`启动` 只有在核心运行时才可用。
4. 点击 `在文件夹中显示`（Show in folder），附上 `throne-profile-<date>-<time>.zip` 文件。

该文件会列出你的 Throne 版本、操作系统和少量设置，但不包含你的任何配置。`包含 Throne 日志` 会加入你的日志，而日志中包含你访问过的域名和服务器地址。在 Android 上，请使用 `设置`（Settings）→ `诊断`（Diagnostics）；参见[诊断](@/android/troubleshooting.zh.md#diagnostics)。

## 应包含的内容 {#what-to-include}

使用 issue 模板，并填写每一个字段：

| 项目 | 在哪里找到 |
| --- | --- |
| Throne 版本 | `程序`（Program）→ `关于`（About），可在其中复制所有组件的版本。Android：`关于`（About）。 |
| 安装类型 | ZIP、Windows 安装程序、`.deb`、`.rpm`、安装脚本、软件源或包管理器，以及你是否使用[无界面 Throne](@/headless/_index.zh.md)。 |
| 模式 | `Tun 模式`（Tun Mode）、`系统代理`（System Proxy）或两者都开启，以及[断网保护（kill switch）](@/advanced/kill_switch.zh.md)是否开启。 |
| 配置档类型 | `类型`（Type）列，例如 `VLESS (Xray)`。不要提供链接。 |

对于路由问题，请使用 `Routing Issue` 模板并附上你的路由配置档：在 `设置` → `路由设置`（Routing Settings）→ `路由`（Route）中选中该配置档，点击 `导出`（Export）复制一条 `throne://route/…` 链接。发布之前，请检查其中是否包含私有域名或地址。

如果开发者要求提供你的配置，请右键点击该配置档 → `分享`（Share）→ `导出配置`（Export config）（仅在高级模式下可用），然后复制 `sing-box` 标签页的内容；如果开发者需要，则复制 `Xray` 标签页的内容。发布之前，请将密码、UUID、密钥和服务器地址替换为 `xxxx`。

对于无界面 Throne，另请参见 [Bug 报告中应包含的内容](@/headless/troubleshooting.zh.md#reporting)。
