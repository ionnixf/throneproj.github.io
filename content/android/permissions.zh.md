+++
title = "权限与后台运行"
description = "Throne for Android 需要哪些权限，以及如何让 VPN 在后台保持运行。"
weight = 20
toc = true
+++

| 权限 | Throne 何时请求 | 如果拒绝 |
| --- | --- | --- |
| VPN 连接 | 首次以 `VPN` 模式连接时 | 无法启动 VPN 模式 |
| 通知（Android 13+） | 首次启动时 | VPN 可以工作，但其通知被隐藏 |
| 电池优化 | 首次启动时 | Android 可能会在后台停止 VPN |
| 位置 | 使用 Wi-Fi 规则时 | Wi-Fi 规则无法匹配 |
| 已安装应用 | 大多数设备上不会请求 | 应用列表为空 |
| 相机 | 扫描二维码时 | 扫描器会关闭。请改用 `从剪切板导入`（Import from clipboard）或 `从文件中导入`（Import from file）。 |
| 安装应用 | 首次进行[应用内更新](@/android/installation.zh.md#updater)时 | 改为在浏览器中下载更新 |

之后可以在 Android 中 Throne 的应用设置里更改这些权限。

## VPN 权限 {#vpn-permission}

首次以 `VPN` 模式连接时，Android 会询问是否允许 Throne 建立 VPN 连接。请允许。如果你拒绝了，再次点按连接按钮即可重新看到请求，或者改用 [`仅代理`（Proxy only）模式](@/android/modes.zh.md#proxy-only)。

如果另一个 VPN 应用被设为始终开启的 VPN，Android 会直接拒绝而不询问。请先为该应用关闭始终开启的 VPN。

某些 Android TV 和 AOSP 设备没有用于此请求的界面，此时 Throne 会显示“无法请求 VPN 确认”。点按 `使用代理模式`（Use proxy mode）即可以 `仅代理` 模式连接；或者用 adb 授予一次权限（`复制命令`（Copy command）会复制该命令），然后重新连接：

```bash
adb shell appops set com.nb4a.throne ACTIVATE_VPN allow
```

在电视上，另请参见[电视上的 VPN 权限](@/android/tv.zh.md#vpn-consent)。

## 通知 {#notifications}

VPN 运行时，Throne 会显示一条通知，其中包含配置名称、速度，以及停止 VPN 和切换服务器的按钮。在 Android 13 及更高版本上，Throne 会在首次启动时请求一次通知权限。没有该权限时，VPN 仍然可以工作，但通知及其按钮会被隐藏。

`设置`（Settings）→ `外观`（Appearance）→ `通知`（Notifications）会显示通知是否已允许。点按它即可打开 Android 中 Throne 的通知设置。要选择通知中的按钮，请参见[通知](@/android/widgets.zh.md#notification)。

## 电池优化 {#battery}

首次启动时，Throne 会请求免除电池优化，以便熄屏时 VPN 保持连接，并且通知、图块和小部件上的按钮继续可用。点按 `允许`（Allow）并在 Android 对话框中确认。

`设置`（Settings）→ `通用`（General）→ `电池优化`（Battery optimization）会显示当前状态，应为“不受限”。如果不是，请点按该项：允许 Throne 不受电池优化限制地运行；如果显示“受系统限制”，请在 Android 中 Throne 的应用信息里打开电池设置，选择不受限制的选项（在 Android 12 及更高版本上为 `不受限制`（Unrestricted））。

## 厂商设置 {#oem}

一些厂商会加入自己的后台限制，即使关闭了电池优化，这些限制也可能停止 Throne。`设置`（Settings）→ `通用`（General）→ `后台与自启动`（Background & auto-start）会在小米、Redmi、POCO、华为、荣耀、OPPO、realme、vivo、三星、一加和华硕设备上打开厂商自己的设置界面，在其他设备上则打开 [dontkillmyapp.com](https://dontkillmyapp.com) 上与你的设备厂商对应的页面。请允许 Throne 自动启动并在后台运行。

## 始终开启的 VPN {#always-on}

开启始终开启的 VPN（Always-on VPN）后，Android 会在设备启动时自行启动 Throne，并保持其连接。它只在 `VPN` 模式下有效。

1. 打开 `设置`（Settings）→ `通用`（General）→ `始终开启的 VPN`（Always-on VPN）。Throne 会打开 Android 的 VPN 设置。
2. 在列表中打开 Throne 的设置（通常是一个齿轮图标），然后开启“始终开启的 VPN”。

Throne 需要一个已选择的配置。在 Android 10 及更高版本上，如果没有选择配置，Throne 会显示通知“始终开启的 VPN 没有配置”；当 Android 启动 Throne 时，它会把 `仅代理`（Proxy only）模式切换为 `VPN`。在 Android 7 至 9 上，请将 `运行模式`（Service mode）保持为 `VPN`。

Android 的“屏蔽未使用 VPN 的所有连接”（Block connections without VPN）选项会在 Throne 未连接时阻止所有流量。Throne for Android 没有自己的断网保护（kill switch），因此这个选项是最接近的功能。开启后，当 Throne 停止或无法连接时，应用将无法上网，并且 Android 会忽略[允许应用绕过 VPN](@/android/modes.zh.md#allow-bypass)（Allow apps to bypass VPN）。

## 自动连接 {#auto-connect}

`设置`（Settings）→ `通用`（General）→ `自动连接`（Auto connect）（默认关闭）会在设备启动后你解锁时，以及 Throne 更新后，启动所选配置。与该设置的说明不同，即使之前没有连接，它也会启动所选配置。它使用当前的 `运行模式`，因此在 `VPN` 模式下，请在依赖此功能之前先允许一次 VPN 请求。

在 `VPN` 模式下，始终开启的 VPN 是更好的选择，因为由 Android 本身保持 VPN 运行。`自动连接` 适用于 `仅代理` 模式。如果开机时启动失败，请检查[电池优化](#battery)。

## 用于 Wi-Fi 规则的位置权限 {#location}

匹配 Wi-Fi 名称（SSID）或接入点（BSSID）的路由规则需要位置权限，因为没有该权限时 Android 会向应用隐藏已连接的 Wi-Fi。Throne 绝不读取你的位置。这些规则需要：

- 精确位置权限；
- 在 Android 10 及更高版本上选择 `始终允许`（Allow all the time），因为 Throne 的界面关闭后 VPN 仍在运行；
- 在 Android 9 及更高版本上开启位置信息服务。

当你保存带有 Wi-Fi 条件的规则时，Throne 会请求该权限。如果 Throne 运行时缺少该权限，会出现通知“Wi-Fi 规则未启用”，点按该通知即可修复。其他规则（包括 `网络类型`（Network type）和 `按流量计费网络`（Metered network））无需位置权限即可工作。

## 已安装应用 {#installed-apps}

分应用代理和路由规则的 `应用`（Apps）字段需要已安装应用的列表。某些系统（例如小米设备上的系统）为此设有单独的权限。如果该权限被拒绝，列表会为空，Throne 会显示“无法读取已安装的应用。”点按 `打开系统设置`（Open system settings），并允许 Throne 读取应用列表。
