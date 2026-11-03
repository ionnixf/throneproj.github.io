+++
title = "权限与后台运行"
description = "Throne for Android 会请求哪些权限、拒绝后会怎样，以及如何让 VPN 在后台保持运行。"
weight = 20
toc = true
+++

Throne 会请求几项 Android 权限。本页说明它为什么需要每一项权限、拒绝后会怎样，以及之后在哪里更改。本页还说明如何防止 Android 在后台关闭 Throne。

| 权限 | Throne 何时请求 | 如果拒绝 |
| --- | --- | --- |
| VPN 连接 | 首次以 `VPN` 模式连接时 | 无法启动 VPN 模式 |
| 通知（Android 13+） | 首次启动时 | VPN 可以工作，但其通知被隐藏 |
| 电池优化 | 首次启动时 | Android 可能会在后台停止 VPN |
| 位置 | 使用 Wi-Fi 规则时 | Wi-Fi 规则无法匹配 |
| 已安装应用 | 大多数设备上不会请求 | 应用列表为空 |
| 相机 | 扫描二维码时 | 扫描器会关闭 |
| 安装应用 | 首次进行应用内更新时 | 改为在浏览器中下载更新 |

其中大部分权限之后都可以在 Android 中 Throne 的应用设置里更改。安装应用的权限在[应用内更新程序](@/android/installation.zh.md#updater)中说明。

## VPN 权限 {#vpn-permission}

在默认的 `运行模式`（Service mode），即 `VPN` 模式下，Throne 使用 Android 的 VPN 服务，将应用的流量经由所选配置发送。

首次点按连接按钮时，Android 会询问是否允许 Throne 建立 VPN 连接。请允许。在你使用过其他 VPN 应用之后，Android 可能会再次询问。如果在你允许之前，小部件或快捷设置图块启动了 Throne，Throne 会打开该请求。如果屏幕已锁定，请求会在你解锁后出现。

如果你拒绝，Throne 会显示“VPN 服务权限请求被拒绝”。再次点按连接按钮即可重新看到请求。如果你不想使用 VPN，请改用 `仅代理`（Proxy only）模式。参见 [VPN 与代理模式](@/android/modes.zh.md#proxy-only)。

Android 同一时间只运行一个 VPN。启动 Throne 会断开其他 VPN 应用。如果另一个 VPN 应用在 Android 设置中被设为始终开启的 VPN，Android 不会显示请求，Throne 会报告权限被拒绝。请先为该应用关闭始终开启的 VPN。

某些 Android TV 和 AOSP 设备没有用于此请求的界面。此时 Throne 会显示“无法请求 VPN 确认”，并提供三个按钮：

- `使用代理模式`（Use proxy mode）会将运行模式切换为 `仅代理`（Proxy only）并连接。
- `复制命令`（Copy command）会复制授予该权限的 adb 命令。
- `取消`（Cancel）会关闭该消息。

要授予该权限，请用 adb 将设备连接到电脑，运行一次以下命令，然后在 Throne 中重新连接：

```bash
adb shell appops set com.nb4a.throne ACTIVATE_VPN allow
```

在电视上，`仅代理`（Proxy only）模式也可以配合电视自身的代理设置使用。参见 [Android TV](@/android/tv.zh.md#vpn-consent)。

## 通知 {#notifications}

VPN 运行时，Throne 会显示一条通知，其中包含配置名称、速度，以及停止 VPN 和切换服务器的按钮。在 Android 13 及更高版本上，应用需要权限才能显示通知。Throne 会在首次启动时请求一次。在 Android TV 上不会请求。

如果此时该权限已经被拒绝过，Throne 会先解释：“VPN 运行时 Throne 会显示带停止与切换服务器按钮的通知。请允许通知以查看它。”点按 `继续`（Continue）查看 Android 的请求，或点按 `暂不`（Not now）。之后 Throne 不会再次询问。

没有该权限时，VPN 仍然可以工作，但通知及其按钮会被隐藏。之后要查看或更改该权限，请打开 `设置`（Settings）→ `外观`（Appearance）→ `通知`（Notifications）。它会显示“已允许”，或“已阻止：服务通知及其按钮被隐藏。点按以允许。”点按它即可打开 Android 中 Throne 的通知设置。

要选择通知中的按钮，请参见[小部件、图块与自动化](@/android/widgets.zh.md#notification)。

## 电池优化 {#battery}

在询问通知权限之后，Throne 会询问一次：“保持 Throne 运行”。该消息解释说：“Android 可能为省电停止或延迟后台应用。让 Throne 免除电池优化，以便熄屏时 VPN 保持连接，并从通知、磁贴或小组件切换服务器。”

点按 `允许`（Allow）并在 Android 对话框中确认。如果点按 `暂不`（Not now），Throne 会提醒你：“之后可在「设置 › 通用 › 电池优化」中允许”。

自 2.0.5 起，重新安装后 Throne 会再次提出首次启动时的问题，即使 Android 已从其备份中恢复了应用数据。

`设置`（Settings）→ `通用`（General）→ `电池优化`（Battery optimization）会显示当前状态：

| 状态 | 含义 |
| --- | --- |
| “不受限” | Android 不限制 Throne。这正是你需要的状态。 |
| “已优化：Android 可能在后台停止 VPN” | Android 可能为了省电而暂停或停止 Throne。 |
| “受系统限制：后台启动被阻止” | Android 会在后台阻止 Throne，从通知、小部件启动或开机时启动都可能失败。 |

点按该项即可打开相应的 Android 界面。对于“已优化”，请允许 Throne 不受电池优化限制地运行。对于“受系统限制”，Throne 会打开 Android 中 Throne 的应用信息页面。请打开其中的电池设置，选择不受限制的选项。在 Android 12 及更高版本上，该选项名为 `不受限制`（Unrestricted）。

在没有这些界面的设备上（例如许多电视），Throne 会显示“此设备没有电池优化设置”。

## 厂商设置 {#oem}

一些厂商会在 Android 的限制之上再加入自己的后台限制。即使关闭了电池优化，这些限制也可能停止 Throne。

`设置`（Settings）→ `通用`（General）→ `后台与自启动`（Background & auto-start）（“可能导致 Throne 在后台被停止的设备专属设置”）会在以下设备上打开厂商自己的设置界面：

- 小米、Redmi 和 POCO
- 华为和荣耀
- OPPO 和 realme
- vivo
- 三星
- 一加
- 华硕

在其他设备上，或者无法打开该界面时，它会打开 [dontkillmyapp.com](https://dontkillmyapp.com) 上与你的设备厂商对应的页面。在那里，请允许 Throne 自动启动并在后台运行。这些选项的名称因厂商和系统版本而异。dontkillmyapp.com 为许多设备提供了分步指南。

## 始终开启的 VPN {#always-on}

始终开启的 VPN（Always-on VPN）是 Android 的一项功能。设备启动时，Android 会自行启动该 VPN 应用，并尽量保持其连接。它只在 `VPN` 模式下有效。

1. 打开 `设置`（Settings）→ `通用`（General）→ `始终开启的 VPN`（Always-on VPN）。Throne 会打开 Android 的 VPN 设置。
2. 在列表中打开 Throne 的设置（通常是一个齿轮图标）。
3. 开启“始终开启的 VPN”（Always-on VPN）。

Throne 中的该项会显示当前状态：“关闭。请在系统 VPN 设置中配置”、“已开启：Android 会自行以 VPN 模式启动 Throne”或“已开启，阻止不使用 VPN 的连接”。在 Android 9 及更早版本上，Throne 无法读取该状态，会显示“请在系统 VPN 设置中配置”。

Throne 需要一个已选择的配置。在 Android 10 及更高版本上：

- 如果没有选择配置，Throne 会显示通知“始终开启的 VPN 没有配置”：“在 Throne 中选择一个配置；在此之前始终开启的 VPN 无法连接。”
- 当 Android 启动 Throne 而运行模式为 `仅代理`（Proxy only）时，Throne 会将其切换为 `VPN`。

在 Android 7 至 9 上，Throne 不会做这两件事：没有配置时不会显示通知，并且在 `仅代理` 模式下，由始终开启的 VPN 触发的启动不会建立连接。因此在这些版本上，请将 `运行模式`（Service mode）保持为 `VPN`。

Android 的“屏蔽未使用 VPN 的所有连接”（Block connections without VPN）选项会在 Throne 未连接时阻止所有网络流量。与 [Throne 桌面版](@/advanced/kill_switch.zh.md)不同，Throne for Android 没有自己的断网保护（kill switch），因此在 Android 上，这个选项是最接近的功能。开启后，当 Throne 停止或无法连接时，应用将无法上网，并且 Android 会忽略[允许应用绕过 VPN](@/android/modes.zh.md#allow-bypass)（Allow apps to bypass VPN）。要关闭它，请再次打开 Android 的 VPN 设置。

## 自动连接 {#auto-connect}

`设置`（Settings）→ `通用`（General）→ `自动连接`（Auto connect）默认关闭。开启后，Throne 会在以下时机启动所选配置：

- 设备启动后，在你第一次解锁时；
- Throne 更新后。

它使用当前的 `运行模式`（Service mode），并且需要一个已选择的配置。在 `VPN` 模式下，请在依赖此功能之前先允许一次 VPN 请求。该设置的说明写的是“手机启动或更新后代理会自动重新连接”，但 Throne 2.0.0 即使之前没有连接，也会启动所选配置。

启用了始终开启的 VPN 时，`自动连接`（Auto connect）会显示“已被始终开启的 VPN 取代”。在 `VPN` 模式下，始终开启的 VPN 是更好的选择，因为由 Android 本身保持 VPN 运行。`自动连接` 适用于 `仅代理`（Proxy only）模式，因为始终开启的 VPN 不适用于该模式。如果开机时启动失败，请检查[电池优化](#battery)。

## 用于 Wi-Fi 规则的位置权限 {#location}

Throne 仅在路由规则需要匹配 Wi-Fi 名称（SSID）或接入点（BSSID）时才需要位置权限。正如应用中所解释的：“没有该权限时 Android 会向应用隐藏已连接的 Wi-Fi。Throne 只读取已连接的网络，绝不读取你的位置。”

Wi-Fi 规则需要：

- 精确位置权限；
- 在 Android 10 及更高版本上，需要“始终”允许访问位置信息，因为 Throne 的界面关闭后 VPN 仍在运行。Android 询问时，请选择 `始终允许`（Allow all the time）；
- 在 Android 9 及更高版本上，需要开启位置信息服务。如果它处于关闭状态，Throne 会提供 `定位设置`（Location settings）按钮。

当你保存带有 Wi-Fi 条件的规则时，Throne 会请求该权限；使用包含此类规则的路由配置连接时，它也会请求一次。如果 Throne 运行时缺少该权限，会出现通知“Wi-Fi 规则未启用”。点按该通知即可修复。如果你永久拒绝了该请求，Throne 会显示“位置权限被拒绝。请在应用设置中允许，Wi-Fi 规则才能匹配。”，并提供 `打开系统设置`（Open system settings）按钮。

其他规则无需位置权限即可工作。这也包括 `网络类型`（Network type）和 `按流量计费网络`（Metered network）条件（自 2.0.2 起）：它们检查的是当前网络的种类，例如 Wi-Fi 或移动数据，而不是具体是哪个网络。有关路由配置和规则，请参见[路由](@/guides/routing.zh.md#profiles)和 [Android 上的路由](@/guides/routing.zh.md#android)。

## 已安装应用 {#installed-apps}

Throne 会读取已安装应用的列表，用于分应用代理（`设置`（Settings）→ `TUN / VPN` → `分应用代理`（Apps VPN mode））以及路由规则的 `应用`（Apps）字段。大多数设备不会为此请求权限。

某些系统（例如小米设备上的系统）为此设有单独的权限。如果该权限被拒绝，列表会为空，Throne 会显示“无法读取已安装的应用。”点按 `打开系统设置`（Open system settings），并允许 Throne 读取应用列表。

## 相机 {#camera}

Throne 仅在扫描二维码时使用相机。如果你拒绝，扫描器会关闭。请在 Android 中 Throne 的应用设置里允许使用相机，或者使用 `从剪切板导入`（Import from clipboard）或 `从文件中导入`（Import from file）导入链接。在没有相机的设备上，`扫描二维码`（Scan QR code）会被隐藏。有关在这类设备上添加配置的其他方法，请参见 [Android TV](@/android/tv.zh.md)。
