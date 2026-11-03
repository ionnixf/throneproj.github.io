+++
title = "安装与升级"
description = "选择合适的 APK，安装 Throne for Android，从 1.x 或 NekoBox 升级，并让应用保持最新。"
weight = 10
toc = true
+++

本页说明应下载哪个 APK、如何安装、如何从旧版本迁移，以及更新如何进行。Throne for Android 需要 Android 7.0 或更高版本，可在手机、平板和 Android TV 设备上运行。

## 选择合适的 APK {#choose-apk}

每个版本都为每种处理器类型（ABI）各提供一个 APK。没有能在所有设备上运行的通用 APK，因此请选择与你的设备匹配的文件：

| APK | 典型设备 |
| --- | --- |
| `Throne-<version>-arm64-v8a.apk` | 近几年的几乎所有手机和平板 |
| `Throne-<version>-armeabi-v7a.apk` | 较旧的 32 位手机，以及许多 Android TV 盒子和电视棒 |
| `Throne-<version>-x86_64.apk` | 模拟器、部分 Chromebook 和 PC |

没有面向 32 位 x86 设备的版本。

起决定作用的是 Android 系统，而不是处理器。许多电视盒子使用 64 位处理器，但运行的是 32 位 Android，它们需要 `armeabi-v7a`。

要查看设备的 ABI，请打开一个设备信息类应用，查找 ABI 或指令集。如果你有一台装有 adb 的电脑，可以用以下命令输出 ABI：

```bash
adb shell getprop ro.product.cpu.abi
```

如果不确定，请先尝试 `arm64-v8a`。如果 Android 提示该应用不兼容，或者不安装它，请改用 `armeabi-v7a`。

## 下载与校验 {#download}

从[下载](@/downloads.zh.md)页面或 [GitHub 发布页](https://github.com/throneproj/ThroneForAndroid/releases)下载 APK。版本标签以 `v` 开头，例如 `v2.0.0`。

每个版本还包含 `SHA256SUMS`，即该版本各 APK 的 SHA-256 校验和。校验是可选的，它可以确认文件在下载过程中没有被更改或损坏。请把 APK 和 `SHA256SUMS` 放在电脑上的同一个文件夹中，然后运行适用于你系统的命令。

**Linux：**

```bash
sha256sum -c --ignore-missing SHA256SUMS
```

输出中你的文件必须显示 `OK`，例如 `Throne-2.0.0-arm64-v8a.apk: OK`。

**macOS：**

```bash
shasum -a 256 Throne-2.0.0-arm64-v8a.apk
```

**Windows：**

```powershell
Get-FileHash .\Throne-2.0.0-arm64-v8a.apk -Algorithm SHA256
```

在 macOS 和 Windows 上，请将输出的校验和与 `SHA256SUMS` 中对应你文件的那一行进行比较。字母大小写无关紧要。

## 安装 {#install}

如果已安装 Throne for Android 1.x，请先阅读[从 1.x 或 NekoBox 升级](#upgrading)。

1. 打开下载的 APK，例如从浏览器的下载列表或文件管理器中打开。
2. 如果 Android 阻止安装，请允许你用来打开该文件的应用安装应用。在 Android 8 及更高版本上，点按提示中的 `设置`（Settings），然后开启 `允许来自此来源的应用`（Allow from this source）。在 Android 7 上，请在 Android 的安全设置中开启 `未知来源`（Unknown sources）。
3. 返回并点按 `安装`（Install）。
4. 打开 Throne 并回答首次启动时的问题。参见[权限与后台运行](@/android/permissions.zh.md)。

关于 Android TV，请参见 [Android TV](@/android/tv.zh.md#install-tv)。

## 从 1.x 或 NekoBox 升级 {#upgrading}

Throne for Android 2.0.0 基于桌面版核心重新构建。它使用新的密钥签名，并采用新的数据格式：

- Android 无法在 Throne for Android 1.x 之上安装 2.0.0。在你卸载旧版本之前，安装都会失败。
- 2.0.0 启动时没有任何配置、分组或设置。它无法读取旧应用的数据。
- 它无法恢复旧备份，即 `throne_backup_….json` 文件以及 WebDAV 上的 `.zip` 文件。它只能恢复 `.thrbackup` 文件，也就是 Throne 桌面版的备份格式。

{% alert_warning() %}
卸载旧应用会删除它的所有数据：配置、分组、路由规则和设置。卸载前请保存你需要的内容。
{% end %}

1. 在旧应用中复制每个订阅的 URL，并保存在安全的地方，例如一条私密笔记中。要找到它，请在侧边菜单中打开 `分组`（Group），点按该分组的编辑按钮（铅笔图标），然后复制 `订阅链接`（Subscription Link）。不要使用 `分享订阅`（Share Subscription）：它复制的是 2.0.0 无法读取的 `sn://` 链接。
2. 导出不是来自订阅的配置：打开该分组的菜单，选择 `导出`（Export）→ `导出到文件`（Export to file）。该文件以分享链接的形式包含这些配置。对于某些类型，例如 WireGuard、SSH、ShadowTLS、Mieru、代理链和自定义配置类型的配置，旧应用会写出 2.0.0 无法导入的 `sn://` 链接。请记下这些配置的设置，以便重新创建它们。
3. 如果你在 Throne 桌面版中使用相同的配置，也可以改为在桌面版中创建备份（`设置`（Settings）→ `基本设置`（Basic Settings）→ `备份和恢复`（Backup and Restore）），之后在手机上恢复。参见[在桌面版与 Android 之间迁移](@/guides/backup.zh.md#desktop-android)。
4. 卸载旧应用。
5. 按照[安装](#install)中的说明安装 2.0.0。
6. 重新添加订阅（`添加服务器配置`（Add profile）→ `从剪切板导入`（Import from clipboard）→ `创建新订阅分组`（Create new subscription group）），导入导出的文件（`添加服务器配置` → `从文件中导入`（Import from file）），或恢复桌面版备份（侧边菜单 → `工具`（Tools）→ `恢复`（Restore））。

**NekoBox for Android：** 原版 NekoBox 应用的包名不同，因此 Android 会把 Throne 与它并列安装。Throne 无法读取 NekoBox 的数据或备份。请通过订阅链接迁移你的订阅。同一时间只能有一个 VPN 应用处于连接状态。

## 稳定版与预览版 {#preview}

稳定版以普通发布版本（release）的形式发布在 GitHub 上，例如 `v2.0.2`。预览版包含最新的更改，用于测试，因此可能存在 bug。

在 2.0.0 及更早版本中，预览版以 GitHub 预发布版本（pre-release）的形式发布，标签形如 `v<version>-pre.<number>`。自 2.0.1 起，预览版不再发布在发布页面上。开发者需要测试者时，会手动构建预览版。它作为附件保存在构建它的 GitHub Actions 运行中，保留 14 天，下载它需要 GitHub 账号。

这些较新的预览版有自己的包名 `com.nb4a.throne.debug`。Android 会把预览版作为第二个应用安装在稳定版旁边，它有自己的配置和设置。应用内更新程序不会提供预览版，预览版也不会自行更新。在 adb 命令和自动化应用中，对于预览版，请使用 `com.nb4a.throne.debug` 代替 `com.nb4a.throne`。

预览版在应用打开时会显示一条警告：“本应用为预览版，可能存在诸多问题。若您不愿参与测试，请前往GitHub下载正式发布版本！”在你点按 `不再显示`（Don't show again）之前，每次启动都会再次显示该警告。更新到较新的预览版后，警告会重新出现。

## 应用内更新程序 {#updater}

从 GitHub 获取的版本可以自行更新：

1. 在侧边菜单中打开 `关于`（About），点按 `检查更新`（Check for updates）。其下方的一行显示更新渠道：“更新通道：正式版”或“更新通道：正式版与预发布版”。
2. 如果有新版本，Throne 会显示“发现新版本”，并列出版本号、下载大小和更新说明。点按 `更新`（Update）。
3. 在 Android 8 及更高版本上，Throne 第一次会询问“允许安装更新”。点按 `打开设置`（Open settings），允许 Throne 安装应用，然后返回。
4. Throne 会下载并检查 APK，然后 Android 会请你确认安装。

替换应用期间连接会停止，更新完成后会重新启动。

自 2.0.4 起，更新说明会显示从你当前版本之后直到新版本的每个版本的更改，内容取自应用的更新日志。如果新版本是稳定版，其预发布版本不会单独列出。如果 Throne 无法获取更新日志，则改为显示 GitHub 发布页面上的说明。更新说明中的链接会在浏览器中打开。

更新设置位于 `设置`（Settings）→ `通用`（General）的 `更新`（Updates）部分：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `允许更新到 Beta 版本`（Allow updating to beta versions） | 关闭 | 同时提供 GitHub 发布页面上的预发布版本。较新的预览版不会发布在那里。参见[稳定版与预览版](#preview)。 |
| `每天检查更新`（Check for updates daily） | 关闭 | 每天检查一次，并显示通知“Throne … 已可用”。该通知带有 `跳过此版本`（Skip this version）按钮。 |

在安装任何内容之前，Throne 会检查下载的文件：

- 其大小和 SHA-256 校验和，需与该版本的更新清单（`throne-update.json`）中列出的一致；
- 它是 Throne，并且比已安装的版本更新；
- 它与已安装的应用使用同一个密钥签名，并且使用 Throne 发布密钥签名。

如果任何一项检查失败，都不会安装任何内容。如果你的副本不是用 Throne 发布密钥签名的（例如来自其他来源的构建），更新程序会显示“下载使用了与当前构建不同的密钥签名，Android 无法将其作为更新安装。请先备份，卸载当前构建，再从 GitHub 安装发布版。”

更新程序还有以下行为：

- 它会安装与你已安装版本相同 ABI 的 APK，例如 `arm64-v8a`。它绝不会切换到其他 ABI。
- 有些版本无法在应用内安装：没有更新清单的版本，以及没有适用于你的 ABI 的 APK 的版本。此时 Throne 会显示“此更新无法在应用内安装（…）。请从发布页下载。”，并提供 `在浏览器中打开`（Open in browser）按钮。
- 当 `设置` → `订阅`（Subscriptions）中的 `使用代理`（Use proxy）开启，或运行模式为 `仅代理`（Proxy only），并且 Throne 已连接时，检查和下载会经由 Throne 的本地代理进行。没有配置在运行时，它们会直接连接。开启 `禁用混合入站`（Disable mixed inbound）时，它们不会使用本地代理。
- 更新时绝不会忽略 TLS 证书错误，即使开启了 `忽略 TLS 错误`（Ignore TLS errors）也是如此。

## F-Droid 版本 {#fdroid}

源代码中还有第二个构建变体 `fdroid`，供 F-Droid 使用。它没有应用内更新程序：没有 `关于`（About）→ `检查更新`（Check for updates）和 `更新`（Updates）设置，应用也不会请求安装应用的权限。Throne for Android 目前尚未上架 F-Droid，已在 [#37](https://github.com/throneproj/ThroneForAndroid/issues/37) 中请求上架。

只有当更新与已安装的应用使用同一个密钥签名时，Android 才会安装它。因此，使用其他密钥签名的版本与来自 GitHub 的版本无法相互更新。要切换版本，请先备份数据（侧边菜单 → `工具`（Tools）→ `创建备份`（Create backup）），卸载应用，安装另一个版本，然后恢复备份。
