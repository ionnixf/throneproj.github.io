+++
title = "小部件、图块与自动化"
description = "通过主屏幕小部件、通知、快捷设置图块、启动器快捷方式以及 Tasker 等自动化应用控制 Throne for Android。"
weight = 40
toc = true
+++

无需打开应用，你就可以启动、停止和切换 Throne。本页介绍主屏幕小部件、通知、快捷设置图块、启动器快捷方式，以及 Tasker 或 MacroDroid 等自动化应用。

## 小部件 {#widgets}

Throne 有两个主屏幕小部件：

| 小部件 | 默认尺寸 | 显示内容 | 可执行的操作 |
| --- | --- | --- | --- |
| `开关`（Toggle） | 1×1 | 连接状态。把小部件调宽后，还会显示配置名称。 | 点按它可启动或停止连接。 |
| `状态`（Status） | 4×1 | 配置名称、所属分组和状态。连接期间，代理的上传和下载速度会取代状态显示。 | 点按中间部分可启动或停止连接。点按箭头可切换到上一个或下一个配置。 |

添加小部件：

1. 长按主屏幕上的空白区域。
2. 打开启动器的小部件列表。
3. 找到 Throne，将 `开关` 或 `状态` 拖到主屏幕上。

`状态` 小部件的箭头会按配置列表中的顺序，在当前配置所属的分组内移动。到达最后一个配置后，会从第一个重新开始。分组中只有一个配置时，箭头会隐藏。Throne 已连接时，箭头会把连接切换到另一个配置。Throne 已停止时，箭头只会更改所选配置。

`状态` 小部件上的速度最多每 3 秒更新一次，并且只在屏幕亮起时更新。如果没有选择配置，小部件会显示“无配置”，而不是配置名称。

## 通知 {#notification}

Throne 运行时会显示一条通知。点按该通知即可打开 Throne。

- 标题是配置名称。开启 `在通知中显示组名`（Show group name in notification）后，标题为 `[分组名] 配置名`。
- 正文显示当前速度。开启 `显示直连速度`（Show direct speed）时（默认开启），会显示两行：`代理： …↑ …↓`（Proxy: …↑ …↓）和 `直连： …↑ …↓`（Direct: …↑ …↓）。关闭时只显示代理速度。
- 小字显示自连接开始以来的代理流量。

这些选项位于 `设置`（Settings）→ `外观`（Appearance）的 `通知`（Notification）部分。

### 通知按钮 {#notification-buttons}

在 `设置`（Settings）→ `外观`（Appearance）→ `通知按钮`（Notification buttons）中选择按钮。最多可以选择 3 个按钮。默认按钮为 `停止`（Stop）、`下一个`（Next）和 `切换…`（Switch…）。按钮始终按下表中的顺序显示：

| 按钮 | 作用 |
| --- | --- |
| `停止`（Stop） | 停止连接。 |
| `上一个`（Previous） | 切换到当前分组中的上一个配置。 |
| `下一个`（Next） | 切换到当前分组中的下一个配置。 |
| `切换…`（Switch…） | 打开你的配置列表。选择一个即可切换到它。 |
| `重置连接`（Reset connections） | 关闭所有已打开的连接，使应用重新连接。 |

`上一个` 和 `下一个` 在分组中移动的方式与 `状态` 小部件的箭头相同（参见[小部件](#widgets)）。

如果更改按钮时 Throne 已连接，请在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply）。

如果你看不到通知，可能是 Throne 的通知被阻止了。参见[通知](@/android/permissions.zh.md#notifications)。

## 快捷设置图块 {#quick-settings-tile}

`Throne` 图块可以在快捷设置中启动和停止 Throne。在 2.0.2 之前，它叫做 `开关`（Switcher）。添加方法：

1. 从屏幕顶部向下滑动，打开快捷设置。
2. 点按编辑按钮。在大多数手机上，它是一个铅笔图标。
3. 将 `Throne` 图块拖到已启用的图块中。

图块的工作方式：

- 点按图块可启动或停止连接。如果手机已锁定，Android 会先要求你解锁。
- 连接期间，图块显示配置名称；否则显示“Throne”。
- 长按图块可打开 Throne。

## 快捷方式 {#shortcuts}

### 启动器快捷方式 {#launcher-shortcuts}

在 Android 7.1 及更高版本上，长按 Throne 图标即可看到这些快捷方式。你可以把快捷方式拖到主屏幕上。首次打开 Throne 后，这些快捷方式才会出现。

| 快捷方式 | 作用 |
| --- | --- |
| `切换`（Toggle） | Throne 已停止时启动所选配置；正在运行时停止连接。 |
| `启用`（Enable） | Throne 已停止时启动所选配置。 |
| `禁用`（Disable） | 正在运行时停止连接。 |
| `扫描二维码`（Scan QR code） | 打开二维码扫描器。仅在有相机的设备上出现。 |

### 配置快捷方式 {#profile-shortcuts}

在 Android 8.0 及更高版本上，你可以在主屏幕上放置单个配置的快捷方式：

1. 用配置所在行的 `编辑`（Edit）按钮打开该配置。配置运行期间，此按钮不可用。在 `双列`（Double column）布局中，以及自 2.0.5 起开启 `紧凑配置行`（Compact profile rows）时，该行没有 `编辑` 按钮，请使用该行的 ⋮ → `编辑`。参见[配置列表布局](@/android/_index.zh.md#layout)。
2. 在配置编辑器中，点按 ⋮ → `创建快捷方式`（Create shortcut）。
3. 在启动器的对话框中确认。

快捷方式以配置名称命名。点按它时：

- 如果 Throne 已停止，会以该配置启动。
- 如果正在运行其他配置，Throne 会切换到该配置。
- 如果正在运行该配置，Throne 会停止。

`创建快捷方式`（Create shortcut）仅适用于已保存的配置。

## 自动化 {#automation}

Tasker 或 MacroDroid 等自动化应用可以启动 Throne 的快捷方式 Activity。Throne 的包名是 `com.nb4a.throne`。

| 类 | 作用 |
| --- | --- |
| `io.nekohasekai.sagernet.QuickToggleShortcut` | Throne 已停止时启动所选配置；正在运行时停止连接。接受 `profile` extra（见下文）。 |
| `io.nekohasekai.sagernet.ui.QuickEnableShortcut` | Throne 已停止时启动所选配置。 |
| `io.nekohasekai.sagernet.ui.QuickDisableShortcut` | 正在运行时停止连接。 |
| `io.nekohasekai.sagernet.ui.ScannerActivity` | 打开二维码扫描器。在没有相机的设备上，会改为打开图片选择器。 |

前三个 Activity 不显示任何界面，并会立即关闭。请始终按所示输入完整的类名。这些类名并不以包名开头，因此 `.QuickToggleShortcut` 这样的简写无效。

`QuickToggleShortcut` 接受一个名为 `profile` 的可选 extra，其值为某个配置的 ID。带有该 extra 时，它的作用与该配置的[配置快捷方式](#profile-shortcuts)相同：启动该配置、切换到该配置或停止它。

该 extra 的类型必须为 long（64 位整数）。如果你的自动化应用将其作为普通整数或文本发送，Throne 会忽略它，并像没有 extra 时一样切换连接状态。

配置 ID 是 Throne 在内部为每个配置分配的编号。应用中没有任何界面会列出它；它只出现在少数消息中，例如“服务器缺失（#…）”。如果你只是想从主屏幕切换到某个配置，请改用配置快捷方式。

### 示例 {#automation-example}

在自动化应用中，添加通过 Intent 启动 Activity 的操作。它通常叫做“Send Intent”（发送 Intent，并将目标设为 Activity）或“Launch activity”（启动 Activity）。填写以下字段：

| 字段 | 值 |
| --- | --- |
| 目标（Target） | Activity |
| 包名（Package） | `com.nb4a.throne` |
| 类名（Class） | `io.nekohasekai.sagernet.QuickToggleShortcut` |
| 操作（Action） | `android.intent.action.MAIN`，或留空 |
| Extra（可选） | 名称为 `profile`，类型为 long，值为配置 ID |

你也可以在电脑上用 adb 发送相同的 Intent，例如用于测试：

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.QuickEnableShortcut
adb shell am start -a android.intent.action.MAIN -n com.nb4a.throne/io.nekohasekai.sagernet.QuickToggleShortcut --el profile 12
```

`--el` 用于发送 long 类型的 extra。请将 `12` 替换为你的配置 ID。

提示：

- 在进行自动化之前，先在 Throne 应用中连接一次，以便 Android 已经授予 VPN 权限。
- 如果没有任何反应，请查阅自动化应用中关于从后台启动 Activity 的帮助。在许多手机上，自动化应用需要“显示在其他应用的上层”权限。
- 要在手机启动时连接，请改用 `自动连接`（Auto connect）或始终开启的 VPN。参见[权限与后台运行](@/android/permissions.zh.md#auto-connect)。
- 自动化应用也可以打开 `throne://` 链接，例如用于添加订阅。参见[深度链接](@/advanced/deeplinks.zh.md#android)。

{% alert_info() %}
Throne 的内部广播（例如 `io.nekohasekai.sagernet.SWITCH_NEXT`、`io.nekohasekai.sagernet.RELOAD` 和 `io.nekohasekai.sagernet.CLOSE`）受签名权限保护，其他应用无法发送它们。请改用上面列出的 Activity。
{% end %}
