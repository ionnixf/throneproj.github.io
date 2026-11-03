+++
title = "Android TV"
description = "在 Android TV 盒子和电视棒上安装 Throne，用遥控器操作它，在没有相机的情况下添加配置，并解决缺少 VPN 权限界面的问题。"
weight = 50
toc = true
+++

Throne for Android 也可以在 Android TV 设备上运行，例如电视盒子、电视棒和智能电视。它不需要触摸屏或相机，你可以用遥控器操作它。本页说明与手机不同的地方。

## 在电视上安装 {#install-tv}

从[下载](@/downloads.zh.md)页面下载 APK，然后自行安装。安装后，Throne 会出现在电视主屏幕的应用列表中。

### 选择 APK {#tv-apk}

许多电视盒子和电视棒运行 32 位 Android 系统，即使它们的处理器是 64 位的。它们需要 `armeabi-v7a` APK。运行 64 位 Android 系统的电视需要 `arm64-v8a`。

要检查你的电视，请用 adb 连接它（参见[通过 adb 安装](#install-adb)），然后运行[选择合适的 APK](@/android/installation.zh.md#choose-apk) 中的命令。如果没有 adb，请先尝试 `arm64-v8a`。如果 Android 拒绝安装，请改用 `armeabi-v7a`。

### 通过文件管理器安装 {#install-file-manager}

1. 将 APK 复制到 U 盘并把 U 盘连接到电视，或者直接在电视上下载 APK。
2. 在电视上用文件管理器应用打开该 APK。
3. Android 询问时，允许该文件管理器安装未知应用。
4. 确认安装。

### 通过 adb 安装 {#install-adb}

1. 在电视上开启开发者选项和 USB 调试。在许多设备上，你需要在电视设置中打开“关于”（About），然后在“版本号”（Build）上按 OK 键七次。之后调试开关就位于开发者选项中。不同设备上的名称有所不同。
2. 在同一网络中的电脑上运行 `adb connect`，并加上电视的 IP 地址。
3. 在电视上允许出现的调试请求。
4. 运行 `adb install`，并加上 APK 文件。

```bash
adb connect 192.168.1.50
adb install Throne-2.0.0-armeabi-v7a.apk
```

Throne 2.0.0 无法覆盖安装在旧版本之上。参见[升级](@/android/installation.zh.md#upgrading)。

## 使用遥控器 {#navigation}

| 遥控器按键 | 作用 |
| --- | --- |
| 在屏幕左边缘按左键 | 打开侧边菜单（抽屉式导航栏）。如果左侧还有按钮、标签页或工具栏项，它们会先获得焦点。 |
| 侧边菜单打开时按右键 | 关闭侧边菜单。 |
| 在列表末尾按下键 | 将焦点移到连接按钮，然后移到底部的连接栏。 |
| 在配置上按 OK 键 | 选择该配置。如果 Throne 已连接，会切换到该配置。 |
| 在已选择的配置上按 OK 键 | 启动或停止连接。 |
| 在配置上按住 OK 键 | 开始多选，以便同时对多个配置执行操作。 |
| 播放/暂停键 | Throne 处于打开状态时，启动或停止连接。 |

在从右到左书写的语言中，侧边菜单位于右侧，因此左键和右键的作用互换。

连接期间，底部的连接栏会显示“已连接 , 点击此处测试连接”。将焦点移到它上面并按 OK 键即可测试连接。

### 切换服务器 {#switch-servers}

电视设备通常没有主屏幕小部件或通知按钮。在电视上，配置列表的工具栏中有两个额外的按钮：`上一个服务器`（Previous server）和 `下一个服务器`（Next server）。

- Throne 已连接时，它们会把连接切换到当前分组中的上一个或下一个配置。
- Throne 已停止时，它们只会更改所选配置。

它们遵循配置列表中的顺序。到达最后一个配置后，会从第一个重新开始。

### 调整配置顺序 {#reorder}

用遥控器无法拖动配置。请改为打开配置的 ⋮ 菜单，选择 `上移`（Move up）或 `下移`（Move down）。自 2.0.5 起，这些菜单项只在 Android TV 设备上显示；在 2.0.4 及更早版本中，所有设备上都有。使用搜索过滤时，它们会被隐藏。在 `分组`（Groups）界面上，分组的 ⋮ 菜单中同样有 `上移` 和 `下移`。

## 在没有相机的情况下添加配置 {#import}

电视通常没有相机，而且很难把文本复制到它的剪贴板中。请改用以下方法之一：

- **输入订阅 URL。** 打开 `分组`（Groups）→ `新建分组`（New group），将 `类型`（Type）设为 `订阅`（Subscription），输入 `URL` 并保存。然后在新分组上按 `更新订阅`（Update subscription）。新分组不会自动更新。
- **导入文本文件。** 将分享链接或订阅 URL 保存到 U 盘上的文本文件中。在配置列表中，打开 `添加服务器配置`（Add profile）→ `从文件中导入`（Import from file），然后选择该文件。
- **恢复备份文件。** 在手机或电脑上创建 `.thrbackup` 备份，并将其复制到 U 盘。在电视上用文件管理器打开该文件，Throne 会提示恢复它。你也可以使用 `工具`（Tools）→ `恢复`（Restore）。参见[备份、更新与迁移](@/guides/backup.zh.md#desktop-android)。
- **从 WebDAV 恢复。** 从手机（`工具` → `备份到 WebDAV`（Back up to WebDAV））上传备份，或者自 Throne 桌面版 1.4.0 起从电脑（`Upload to WebDAV`）上传备份。在电视上，在 `工具` → `WebDAV 设置`（WebDAV settings）中填写同一个服务器和同一个文件夹，然后使用 `从 WebDAV 恢复`（Restore from WebDAV）。参见 [WebDAV 备份](@/guides/backup.zh.md#webdav)。

### 使用 adb {#import-adb}

如果你使用 adb，还有两种方法。要在 Throne 中打开 `throne://` 链接或分享链接，请将其作为链接发送：

```bash
adb shell am start -a android.intent.action.VIEW -d "throne://addsub/<base64>"
```

如果链接中包含 `&`，请在双引号内再用单引号把它括起来。

在没有相机的设备上，应用中的 `扫描二维码`（Scan QR code）会被隐藏。要从图片中读取二维码，请用 adb 打开扫描器：

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.ScannerActivity
```

Throne 会显示“没有摄像头：请选择一张含二维码的图片”，并打开图片选择器。选择一张或多张包含二维码的图片。

## 电视上的 VPN 权限 {#vpn-consent}

首次以 VPN 模式连接时，Android 会请你允许 VPN。请选择“确定”（OK）。

某些电视和 AOSP 版本没有用于此询问的界面。此时 Throne 会显示“无法请求 VPN 确认”。你可以用 adb 命令一次性允许 VPN，或者使用 `仅代理`（Proxy only）模式。参见 [VPN 权限](@/android/permissions.zh.md#vpn-permission)。

在 `仅代理` 模式下，只有你设置为使用本地代理的应用才会使用 Throne。如果你的电视在其网络连接中提供代理设置，请将主机设为 `127.0.0.1`，端口设为 `2080`（即 `代理端口`（Proxy port））。遵循系统代理设置的应用随后就会使用 Throne。停止使用 Throne 时请删除此设置，因为代理未运行时这些应用将无法连接。

## 保存和打开文件 {#files}

某些电视设备没有用于选择文件保存位置的系统界面。在这类设备上保存备份、导出配置或保存日志时，Throne 会把文件保存到 `Download/Throne`，并在“没有文件选择器”消息中显示完整路径。在 Android 9 及更早版本上，该文件夹为 `Android/data/com.nb4a.throne/files`。

打开文件（例如使用 `从文件中导入`（Import from file）或 `恢复`（Restore））需要文件选择器。如果 Throne 显示“您的设备缺少 Android 标准文件选择器, 请安装一个, 如 Material Files.”，请安装一个提供文件选择器的文件管理器应用。要用 adb 把文件复制到电视，请使用 `adb push`，例如：

```bash
adb push Throne-backup-20260925-120000.thrbackup /sdcard/Download/
```
