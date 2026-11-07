+++
title = "Android TV"
description = "在电视盒子、电视棒和智能电视上安装和使用 Throne for Android。"
weight = 50
toc = true
+++

## 在电视上安装 {#install-tv}

许多电视盒子和电视棒即使使用 64 位处理器，也需要 `armeabi-v7a` APK。参见[选择合适的 APK](@/android/installation.zh.md#choose-apk)。如果已安装 Throne for Android 1.x，请先阅读[从 1.x 或 NekoBox 升级](@/android/installation.zh.md#upgrading)。

- **文件管理器：** 将 APK 复制到 U 盘（或直接在电视上下载），在文件管理器应用中打开它，并允许该应用安装未知应用。
- **adb：** 在电视的开发者选项中开启 USB 调试（在许多电视上，需要在“关于”（About）中的“版本号”（Build）上按 OK 键七次才会显示开发者选项）。在同一网络中的电脑上运行以下命令，并在电视上允许调试请求：

```bash
adb connect 192.168.1.50
adb install Throne-2.0.6-armeabi-v7a.apk
```

## 使用遥控器 {#navigation}

| 遥控器按键 | 作用 |
| --- | --- |
| 在屏幕左边缘按左键 | 打开侧边菜单（抽屉式导航栏）。在从右到左书写的语言中为右键。 |
| 在列表末尾按下键 | 将焦点移到连接按钮，然后移到底部的连接栏。在连接栏上按 OK 键可测试连接。 |
| 在配置上按 OK 键 | 选择该配置；如果 Throne 已连接，则切换到该配置。在已选择的配置上按 OK 键可启动或停止连接。 |
| 播放/暂停键 | Throne 处于打开状态时，启动或停止连接。 |

配置列表的工具栏中有 `上一个服务器`（Previous server）和 `下一个服务器`（Next server）按钮。Throne 已连接时，它们会把连接切换到当前分组中的上一个或下一个配置；Throne 已停止时，它们只会更改所选配置。

要调整配置或分组的顺序，请使用其 ⋮ 菜单中的 `上移`（Move up）和 `下移`（Move down）。使用搜索过滤时，这些菜单项会被隐藏。

## 在没有相机的情况下添加配置 {#import}

- **订阅 URL：** 打开 `分组`（Groups）→ `新建分组`（New group），将 `类型`（Type）设为 `订阅`（Subscription），输入 `URL` 并保存。然后在该分组上按 `更新订阅`（Update subscription），因为新分组不会自动更新。
- **文本文件：** 将分享链接或订阅 URL 保存到 U 盘上的文本文件中，然后使用 `添加服务器配置`（Add profile）→ `从文件中导入`（Import from file）。
- **备份：** 在电视上用文件管理器打开来自手机或电脑的 `.thrbackup` 文件，或使用 `工具`（Tools）→ `恢复`（Restore）。参见[在桌面版与 Android 之间迁移](@/guides/backup.zh.md#desktop-android)。
- **WebDAV：** 从手机（`工具` → `备份到 WebDAV`（Back up to WebDAV））或电脑（`Upload to WebDAV`）上传备份，在电视上的 `工具` → `WebDAV 设置`（WebDAV settings）中填写同一个服务器和同一个文件夹，然后使用 `从 WebDAV 恢复`（Restore from WebDAV）。参见 [WebDAV 备份](@/guides/backup.zh.md#webdav)。

使用 adb 时，还可以在 Throne 中打开 `throne://` 链接或分享链接。如果链接中包含 `&`，请在双引号内再用单引号把它括起来。

```bash
adb shell am start -a android.intent.action.VIEW -d "throne://addsub/<base64>"
```

在没有相机的设备上，`扫描二维码`（Scan QR code）会被隐藏。要从图片中读取二维码，请用 adb 打开扫描器，它会打开图片选择器：

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.ScannerActivity
```

## 电视上的 VPN 权限 {#vpn-consent}

某些电视和 AOSP 版本没有用于 VPN 请求的界面，连接时 Throne 会显示“无法请求 VPN 确认”。请用 adb 一次性授予该权限，或者使用 `仅代理`（Proxy only）模式。参见 [VPN 权限](@/android/permissions.zh.md#vpn-permission)。

在 `仅代理` 模式下，如果把电视网络连接的代理设为主机 `127.0.0.1`、端口 `2080`（即 `代理端口`（Proxy port）），遵循系统代理设置的应用就会使用 Throne。停止使用 Throne 时请删除此设置，否则这些应用将无法连接。

## 保存和打开文件 {#files}

在没有用于选择文件保存位置的界面的电视上，Throne 会把备份、导出的配置和日志保存到 `Download/Throne`（在 Android 9 及更早版本上为 `Android/data/com.nb4a.throne/files`），并显示完整路径。

打开文件需要文件选择器。如果 Throne 提示你的设备缺少 Android 标准文件选择器，请安装一个提供文件选择器的文件管理器，例如 Material Files。要用 adb 把文件复制到电视：

```bash
adb push Throne-backup-20260925-120000.thrbackup /sdcard/Download/
```
