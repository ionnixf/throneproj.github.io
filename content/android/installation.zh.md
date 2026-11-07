+++
title = "安装与升级"
description = "选择合适的 APK，安装 Throne for Android，从 1.x 或 NekoBox 升级，并让应用保持最新。"
weight = 10
toc = true
+++

Throne for Android 需要 Android 7.0 或更高版本，可在手机、平板和 Android TV 上运行。

## 选择合适的 APK {#choose-apk}

每个版本都为每种处理器类型（ABI）各提供一个 APK：

| APK | 典型设备 |
| --- | --- |
| `Throne-<version>-arm64-v8a.apk` | 近几年的几乎所有手机和平板 |
| `Throne-<version>-armeabi-v7a.apk` | 较旧的 32 位手机，以及许多 Android TV 盒子和电视棒 |
| `Throne-<version>-x86_64.apk` | 模拟器、部分 Chromebook 和 PC |

起决定作用的是 Android 系统，而不是处理器：许多电视盒子使用 64 位处理器，但运行的是 32 位 Android，因此需要 `armeabi-v7a`。使用 adb 时，以下命令会输出 ABI：

```bash
adb shell getprop ro.product.cpu.abi
```

如果不确定，请先尝试 `arm64-v8a`。如果 Android 不安装它，请改用 `armeabi-v7a`。

## 下载与校验 {#download}

从[下载](@/downloads.zh.md)页面或 [GitHub 发布页](https://github.com/throneproj/ThroneForAndroid/releases)下载 APK。要确认文件完好（可选），请把它与同一版本的 `SHA256SUMS` 文件放在同一个文件夹中，然后运行：

```bash
sha256sum -c --ignore-missing SHA256SUMS
```

输出中你的文件必须显示 `OK`。在 macOS 上运行 `shasum -a 256 Throne-2.0.6-arm64-v8a.apk`，在 Windows 上运行 `Get-FileHash .\Throne-2.0.6-arm64-v8a.apk -Algorithm SHA256`，然后将校验和与 `SHA256SUMS` 中对应你文件的那一行进行比较。

## 安装 {#install}

如果已安装 Throne for Android 1.x，请先阅读[从 1.x 或 NekoBox 升级](#upgrading)。

1. 打开下载的 APK。
2. 如果 Android 阻止安装，请点按提示中的 `设置`（Settings），然后开启 `允许来自此来源的应用`（Allow from this source）。在 Android 7 上，请改为在 Android 的安全设置中开启 `未知来源`（Unknown sources）。
3. 返回并点按 `安装`（Install）。
4. 打开 Throne 并回答首次启动时的问题。参见[权限与后台运行](@/android/permissions.zh.md)。

关于 Android TV，请参见 [Android TV](@/android/tv.zh.md#install-tv)。

## 从 1.x 或 NekoBox 升级 {#upgrading}

自 2.0.0 起，Throne for Android 使用新的密钥签名，并采用新的数据格式。Android 无法在 1.x 之上安装它；它启动时没有任何配置、分组或设置，也无法恢复旧备份（`throne_backup_….json` 文件以及 WebDAV 上的 `.zip` 文件），只能恢复 `.thrbackup` 文件。

{% alert_warning() %}
卸载旧应用会删除它的所有配置、分组、路由规则和设置。卸载前请保存你需要的内容。
{% end %}

1. 在旧应用中复制每个订阅的 URL：侧边菜单 → `分组`（Group），点按该分组的编辑按钮（铅笔图标），然后复制 `订阅链接`（Subscription Link）。不要使用 `分享订阅`（Share Subscription）：它复制的是新应用无法读取的 `sn://` 链接。
2. 导出不是来自订阅的配置：分组菜单 → `导出`（Export）→ `导出到文件`（Export to file）。WireGuard、SSH、ShadowTLS、Mieru、代理链和自定义配置类型的配置会被导出为无法导入的 `sn://` 链接，因此请记下它们的设置。
3. 如果你在 Throne 桌面版中使用相同的配置，也可以改为在桌面版中创建备份，然后在手机上恢复。参见[在桌面版与 Android 之间迁移](@/guides/backup.zh.md#desktop-android)。
4. 卸载旧应用，然后[安装](#install)新应用。
5. 重新添加订阅（`添加服务器配置`（Add profile）→ `从剪切板导入`（Import from clipboard）→ `创建新订阅分组`（Create new subscription group）），导入导出的文件（`添加服务器配置` → `从文件中导入`（Import from file）），或恢复桌面版备份（侧边菜单 → `工具`（Tools）→ `恢复`（Restore））。

原版 **NekoBox for Android** 的包名不同，因此 Throne 会与它并列安装。Throne 无法读取它的数据或备份：请通过订阅链接迁移你的订阅。

## 应用内更新程序 {#updater}

从 GitHub 获取的版本可以自行更新：

1. 在侧边菜单中打开 `关于`（About），点按 `检查更新`（Check for updates）。
2. 如果有新版本，点按 `更新`（Update）。
3. 第一次更新时，Throne 会请求安装应用的权限。点按 `打开设置`（Open settings），允许后返回。
4. Throne 会下载并检查 APK。Android 请你确认时，确认安装。

替换应用期间连接会停止，更新完成后会重新启动。

在 `设置`（Settings）→ `通用`（General）→ `更新`（Updates）中，`每天检查更新`（Check for updates daily）会通知你有新版本，`允许更新到 Beta 版本`（Allow updating to beta versions）还会提供 GitHub 发布页面上的预发布版本。两者默认关闭。

只有当更新与已安装的应用使用同一个密钥签名时，Android 才会安装它。因此，来自其他来源的版本与来自 GitHub 的版本无法相互更新。要切换版本，请先备份（侧边菜单 → `工具` → `创建备份`（Create backup）），卸载应用，安装另一个版本，然后恢复备份。关于更新程序的错误，请参见[更新问题](@/android/troubleshooting.zh.md#updates)。

Throne for Android 目前尚未上架 F-Droid（[#37](https://github.com/throneproj/ThroneForAndroid/issues/37)）。

## 预览版 {#preview}

预览版包含最新的更改，用于测试，因此可能存在 bug。开发者需要测试者时，会手动构建预览版。它作为附件保存在构建它的 GitHub Actions 运行中，保留 14 天，下载它需要 GitHub 账号。

预览版有自己的包名 `com.nb4a.throne.debug`，因此它会作为第二个应用安装在稳定版旁边，有自己的配置和设置。它不会自行更新，应用内更新程序也不会提供它。在 adb 命令和自动化应用中，请使用 `com.nb4a.throne.debug` 代替 `com.nb4a.throne`。
