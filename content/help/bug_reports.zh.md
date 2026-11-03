+++
title = "报告 Bug"
description = "报告 Bug 前需要检查什么、如何收集日志和诊断信息，以及 issue 中应包含哪些内容。"
weight = 30
toc = true
+++

一份好的 Bug 报告能帮助开发者迅速找到原因。本页说明需要先检查什么、如何收集日志，以及在 issue 中写些什么。

在这里报告 Bug：

- Throne 桌面版：[github.com/throneproj/Throne/issues](https://github.com/throneproj/Throne/issues)
- Throne for Android：[github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues)

关于如何使用 Throne 桌面版的问题，请使用 [GitHub Discussions 的 Q&A 分类](https://github.com/throneproj/Throne/discussions/categories/q-a)，而不是提交 issue。

{% alert_warning() %}
GitHub 上的 issue 都是公开的。切勿发布订阅 URL、令牌、密码、UUID、私钥或服务器地址。发布之前，请从日志、截图和配置中删除这些信息。Throne 桌面版不会替你删除它们，而且订阅更新失败时，完整的订阅 URL 会被写入日志。这种情况已经发生过：一位用户在公开的 issue 中发布了带有令牌的订阅链接，之后不得不重置它。如果你不小心发布了机密信息，请让服务商将其重置。
{% end %}

## 报告之前 {#before}

1. 通过 `程序`（Program）→ `检查更新`（Check For Update）（1.4.0 之前为 `工具`（Tools）→ `检查更新`）或从[下载](@/downloads.zh.md)页面更新到最新版本。许多 Bug 已经修复。
2. 在现有 issue（包括未关闭和已关闭的）中搜索你的错误信息。
3. 阅读[常见问题](@/help/faq.zh.md)和[故障排除](@/help/troubleshooting.zh.md)。
4. 尝试全新配置：退出 Throne，重命名 `config` 文件夹，启动 Throne，然后只设置重现问题所需的内容。在报告中写明问题在全新配置下是否也会出现。要恢复原状，请退出 Throne，删除新的 `config` 文件夹，再把旧文件夹改回原名。
5. 如果你是通过 WinGet、Scoop、AUR 或 Nix 安装的 Throne，也请测试官方版本。开发者不为这些软件包提供支持。
6. 如果只有一个服务器无法使用，请在其他应用中测试它。如果在其他应用中也失败，请联系你的服务商。

## 收集日志 {#logs}

### 桌面版 {#desktop-logs}

Throne 会在主窗口底部的 `日志`（Logs）标签页中显示日志，并将相同的内容写入配置文件夹中的 `logs/throne.log`。配置文件夹可以通过 `设置`（Settings）→ `打开配置文件夹`（Open Config Folder）打开（仅在高级模式下可用，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

要记录详细日志：

1. 打开 `设置` → `基本设置`（Basic Settings）→ `日志`（Logging）（仅在高级模式下可用）。
2. 将 `Sing-box 日志级别`（Sing-box Log level）设为 `debug`。对于 `VLESS (Xray)` 及其他 Xray 配置档，还需将 `Xray 日志级别`（Xray Log level）设为 `debug`。
3. 调高 `最多日志行数`（Max log lines），例如设为 `2000`。每秒超过此数量的核心日志行会被 Throne 丢弃，并且 `日志` 标签页只保留这么多行。
4. 点击 `确定`（OK），然后通过 `程序`（Program）→ `重启程序`（Restart Program）重启 Throne。
5. 重现问题。
6. 将 `logs/throne.log` 附加到 issue 中。你也可以点击 `日志` 标签页，按 `Ctrl+A`，再按 `Ctrl+C`，然后粘贴文本。如果 `日志` 标签页的筛选栏处于打开状态，请先将其关闭，使标签页显示所有行。
7. 完成后，将 sing-box 日志级别改回 `info`，将 Xray 日志级别改回 `warning`。

需要了解的几点：

- 每次启动 Throne 时，`throne.log` 都会重新开始记录，因此请在重启 Throne 之前复制它。当它超过 4 MB 时，较早的部分会保存为 `throne.log.1` 到 `throne.log.3`。
- `throne.log` 的前几行会显示你的 Throne 版本、Qt 版本、操作系统和 CPU 架构。
- 崩溃后，下次启动时会将崩溃会话的日志保存为 `logs/crashed-<date>-<time>.log`，并在日志中写入“上次 Throne 没有正常干净的关闭”（Throne did not shut down cleanly last time）。最近五次崩溃的日志会被保留。
- **Windows：** Throne 崩溃时，会显示“Throne crashed”（Throne 已崩溃），并在 `crashes` 文件夹中保存一份报告：一个 `.txt` 文件和一个 `.dmp` 文件，文件名为 `Throne_<version>_<architecture>_<date>-<time>`。请附上 `.txt` 文件；如果可以，也请附上 `.dmp` 文件。

### Android {#android-logs}

1. 打开侧边菜单，点按 `日志`（Logs）。
2. 保持 `隐藏敏感数据`（Hide sensitive data）开启。它默认开启，会隐藏 URL、凭据、UUID、密钥、公网 IP 地址和 Wi-Fi 名称。开启 `隐藏目标地址`（Hide destinations）可以同时隐藏你访问过的域名。
3. 点按 `分享日志`（Share logs）或 `保存日志…`（Save logs…），然后将文件附加到 issue 中。

如需详细日志，请在重现问题之前将 `设置`（Settings）→ `核心`（Core）→ `日志级别`（Log level）设为 `debug`。自 2.0.5 起，日志中还包含 Xray 配置的 Xray 日志；对于这些配置，还要在同一界面设置 `Xray 日志级别`（Xray log level）。崩溃后，应用会提供一份经过清理的日志。参见 [Android 故障排除](@/android/troubleshooting.zh.md#logs)。

## 诊断 {#diagnostics}

对于卡死、无响应和 CPU 占用过高的问题，开发者可能会要求你提供诊断文件。它会记录核心在 30 秒内的运行情况。

1. 让 Throne 进入出现问题的状态，例如启动配置档并等待它卡住。不要重启 Throne。
2. 打开 `设置` → `基本设置` → `诊断`（Diagnostics）。
3. 勾选开发者要求的选项：`锁竞争`（Lock contention）、`执行追踪 (大文件)`（Execution trace (larger file)）或 `包含 Throne 日志`（Include Throne logs）。
4. 点击 `启动`（Start），然后等待 30 秒。
5. 点击 `在文件夹中显示`（Show in folder），附上 `throne-profile-<date>-<time>.zip` 文件。Throne 会将这些文件保存在 `config` 文件夹旁边的 `diagnostics` 文件夹中，而不是 `config` 文件夹内部；参见[数据文件夹](@/reference/files.zh.md#data-folder)。

`启动` 只有在核心运行时才可用。录制内容不包含关于你的配置的信息，但会列出你的 Throne 版本、操作系统和少量设置，例如 TUN 模式是否开启，以及正在运行的配置档的类型。`包含 Throne 日志` 会加入你的日志，而日志中包含你访问过的域名和服务器地址。

**Android：** 自 2.0.3 起，`设置`（Settings）→ `诊断`（Diagnostics）中提供相同的功能。点按 `分享`（Share）或 `保存…`（Save…）即可获取文件。参见[诊断](@/android/troubleshooting.zh.md#diagnostics)。

## 应包含的内容 {#what-to-include}

使用 issue 模板，并填写每一个字段：

| 项目 | 在哪里找到 |
| --- | --- |
| Throne 版本 | 桌面版：`程序`（Program）→ `关于`（About）（自 1.4.0 起），它还会显示 sing-box、Xray-core、Qt 和 Go 的版本，你可以选中并复制这些内容；或者窗口标题，例如 `Throne 1.3.1`，或 `throne.log` 的前几行。Android：`关于`（About）。 |
| 操作系统及版本 | 例如 Windows 11 24H2、Ubuntu 24.04、macOS 15 或 Android 14。 |
| 安装类型 | ZIP、Windows 安装程序、`.deb`、`.rpm`、Linux 安装脚本、APT 或 RPM 软件源，或包管理器（WinGet、Scoop、AUR、Nix、Homebrew）。请说明你使用的是窗口版还是[无界面 Throne](@/headless/_index.zh.md)。 |
| 模式 | `Tun 模式`（Tun Mode）、`系统代理`（System Proxy）或两者都开启，以及[断网保护（kill switch）](@/advanced/kill_switch.zh.md)是否开启。 |
| 配置档类型 | 配置档列表中的 `类型`（Type）列，例如 `VLESS (Xray)`。不要提供链接。 |
| 重现步骤 | 从头开始展示问题的编号步骤。 |
| 预期结果与实际结果 | 你预期会发生什么，以及实际发生了什么。请以文本形式复制错误信息。 |
| 日志 | 调试日志、崩溃文件或诊断文件，如上文所述。 |

对于路由问题，请选择 `Routing Issue` 模板。它会要求你提供路由配置档：打开 `设置` → `路由设置`（Routing Settings）→ `路由`（Route），选中该配置档并点击 `导出`（Export）。这会复制一条 `throne://route/…` 链接。发布之前，请检查其中是否包含私有域名或地址。

如果开发者要求提供你的配置，请右键点击该配置档 → `分享`（Share）→ `导出配置`（Export config）（1.4.0 之前为 `导出 Sing-box 配置`（Export Sing-box config）；仅在高级模式下可用），然后在 `sing-box` 标签页上点击 `复制`（Copy）；如果开发者需要 Xray 配置，则在 `Xray` 标签页上点击。发布之前，请将密码、UUID、密钥和服务器地址替换为 `xxxx`。

对于无界面 Throne，另请参见 [Bug 报告中应包含的内容](@/headless/troubleshooting.zh.md#reporting)。
