+++
title = "小部件、图块与自动化"
description = "通过主屏幕小部件、通知、快捷设置图块、快捷方式以及 Tasker 等自动化应用控制 Throne for Android。"
weight = 40
toc = true
+++

## 小部件 {#widgets}

| 小部件 | 作用 |
| --- | --- |
| `开关`（Toggle）（1×1） | 显示连接状态；点按它可启动或停止连接。把它调宽后，还会显示配置名称。 |
| `状态`（Status）（4×1） | 显示配置、所属分组和状态，连接期间则显示代理速度。点按中间部分可启动或停止连接，点按箭头可切换到上一个或下一个配置。 |

添加小部件：长按主屏幕上的空白区域，打开启动器的小部件列表，然后将 `开关` 或 `状态` 拖到主屏幕上。

箭头会按配置列表中的顺序，在当前配置所属的分组内移动。Throne 已连接时，箭头会切换连接；已停止时，只会更改所选配置。

## 通知 {#notification}

Throne 运行时会显示一条通知，其中包含配置名称和当前速度。点按该通知即可打开 Throne。它的选项位于 `设置`（Settings）→ `外观`（Appearance）：`在通知中显示组名`（Show group name in notification）、`显示直连速度`（Show direct speed）（默认开启）和 `通知按钮`（Notification buttons）。

最多可以选择三个按钮。默认按钮为 `停止`（Stop）、`下一个`（Next）和 `切换…`（Switch…）。

| 按钮 | 作用 |
| --- | --- |
| `停止` | 停止连接。 |
| `上一个`（Previous）、`下一个` | 切换到当前分组中的上一个或下一个配置，与 `状态` 小部件的箭头相同。 |
| `切换…` | 打开你的配置列表以便切换。 |
| `重置连接`（Reset connections） | 关闭所有已打开的连接，使应用重新连接。 |

如果更改按钮时 Throne 已连接，请在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply）。如果你看不到通知，请参见[通知](@/android/permissions.zh.md#notifications)。

## 快捷设置图块 {#quick-settings-tile}

要添加 `Throne` 图块，请打开快捷设置，点按编辑按钮（在大多数手机上是一个铅笔图标），然后将该图块拖到已启用的图块中。点按图块可启动或停止连接；如果手机已锁定，Android 会先要求你解锁。长按图块可打开 Throne。

## 快捷方式 {#shortcuts}

在 Android 7.1 及更高版本上，长按 Throne 图标即可看到 `切换`（Toggle）、`启用`（Enable）、`禁用`（Disable）和 `扫描二维码`（Scan QR code）快捷方式。你可以把它们拖到主屏幕上。首次打开 Throne 后，这些快捷方式才会出现。

要把单个配置放到主屏幕上（Android 8.0 及更高版本）：

1. 用配置所在行的 `编辑`（Edit）按钮打开该配置；在双列布局和紧凑行中，请使用该行的 ⋮ → `编辑`。
2. 在配置编辑器中，点按 ⋮ → `创建快捷方式`（Create shortcut），并在启动器的对话框中确认。

该快捷方式会以此配置启动 Throne；如果正在运行其他配置，则切换到此配置；如果正在运行的就是此配置，则停止 Throne。

## 自动化 {#automation}

Tasker 或 MacroDroid 等自动化应用可以启动 Throne（包名 `com.nb4a.throne`）的以下 Activity。请输入完整的类名：`.QuickToggleShortcut` 这样的简写无效。

| 类 | 作用 |
| --- | --- |
| `io.nekohasekai.sagernet.QuickToggleShortcut` | 启动所选配置；正在运行时停止连接。 |
| `io.nekohasekai.sagernet.ui.QuickEnableShortcut` | Throne 已停止时启动所选配置。 |
| `io.nekohasekai.sagernet.ui.QuickDisableShortcut` | 正在运行时停止连接。 |
| `io.nekohasekai.sagernet.ui.ScannerActivity` | 打开二维码扫描器；在没有相机的设备上改为打开图片选择器。 |

在自动化应用中，使用通过 Intent 启动 Activity 的操作，它通常叫做“Send Intent”（目标为 Activity）或“Launch activity”。填写包名和类名，操作（Action）留空或填写 `android.intent.action.MAIN`。

`QuickToggleShortcut` 接受一个名为 `profile` 的可选 extra，其值为配置 ID；带有它时，其作用与该配置的[配置快捷方式](#shortcuts)相同。该 extra 的类型必须为 long；如果作为普通整数或文本发送，则会被忽略。应用中没有任何界面会列出配置 ID，因此对于单个配置，使用配置快捷方式更简单。

在电脑上用 adb 测试（`--el` 用于发送 long 类型的 extra；请将 `12` 替换为配置 ID）：

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.QuickEnableShortcut
adb shell am start -a android.intent.action.MAIN -n com.nb4a.throne/io.nekohasekai.sagernet.QuickToggleShortcut --el profile 12
```

- 在进行自动化之前，先在 Throne 应用中连接一次，以便 Android 已经授予 VPN 权限。
- 如果没有任何反应，自动化应用可能需要从后台启动 Activity 的权限；在许多手机上，这就是“显示在其他应用的上层”权限。
- 要在手机启动时连接，请改用[自动连接](@/android/permissions.zh.md#auto-connect)（Auto connect）或始终开启的 VPN。
- 自动化应用也可以打开 `throne://` 链接，例如用于添加订阅。参见[深度链接](@/advanced/deeplinks.zh.md#android)。
- Throne 的内部广播（例如 `io.nekohasekai.sagernet.SWITCH_NEXT`）受签名权限保护，其他应用无法发送它们。
