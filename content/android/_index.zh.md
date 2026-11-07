+++
title = "Throne for Android"
description = "Throne for Android 快速入门，以及它与 Throne 桌面版的区别。"
weight = 3
sort_by = "weight"
toc = true
+++

Throne for Android（前身为 NekoBox for Android）运行与 Throne 桌面版相同的核心，因此分组、订阅、路由配置、DNS、预设、自动选择器、WARP 和备份在两个应用中的工作方式相同。这些功能在以下指南中说明：[订阅与分组](@/guides/subscriptions.zh.md)、[路由](@/guides/routing.zh.md)、[DNS](@/guides/dns.zh.md)、[测试与自动选择器](@/guides/testing.zh.md)、[Cloudflare WARP](@/advanced/warp.zh.md) 和[在桌面版与 Android 之间迁移](@/guides/backup.zh.md#desktop-android)。本部分介绍 Android 特有的内容。

## 快速入门 {#quick-start}

1. 为你的设备安装对应的 APK。大多数手机需要 `arm64-v8a`。参见[选择合适的 APK](@/android/installation.zh.md#choose-apk)。
2. 打开 Throne。允许通知；当 Throne 询问是否保持在后台运行时，点按 `允许`（Allow）。参见[权限与后台运行](@/android/permissions.zh.md)。
3. 复制你的订阅 URL。在 `配置`（Profiles）界面，点按 `添加服务器配置`（Add profile）（一个带加号的页面）→ `从剪切板导入`（Import from clipboard），或者点按 `扫描二维码`（Scan QR code）扫描二维码。
4. 点按 `创建新订阅分组`（Create new subscription group）。Throne 会添加该分组并下载其中的配置。
5. 打开新分组的标签页，点按 ⋮ → `测试`（Test）→ `URL 测试`（URL test）。测试完成后，点按 `选择最快的`（Select fastest），或点按一个可用的配置。
6. 点按屏幕底部的圆形连接按钮，并允许 VPN 请求。
7. 点按屏幕底部的横栏以检查连接。显示“HTTP：… 毫秒”表示流量已经经由该配置通过。

如果你的服务商提供的是 `throne://addsub/…` 链接，请在手机上打开它，以代替第 3、4 步。

分享文本链接时，Throne 不会出现在 Android 的分享菜单中。请复制链接，改用 `从剪切板导入`（Import from clipboard）。

## 与桌面版的区别 {#differences}

| 方面 | Throne 桌面版 | Throne for Android |
| --- | --- | --- |
| 流量如何进入 Throne | `Tun 模式`（Tun Mode）和 `系统代理`（System Proxy） | `设置`（Settings）→ `通用`（General）→ `运行模式`（Service mode）：`VPN`（默认）或 `仅代理`（Proxy only）。参见 [VPN 与代理模式](@/android/modes.zh.md)。 |
| 选择应用 | 按进程名或路径匹配的路由规则 | `分应用代理`（Apps VPN mode）以及按应用匹配的路由规则 |
| 备份 | `.thrbackup` 文件，也可以存放在 WebDAV 上 | 同样如此，但只包含配置、路由配置和设置。参见 [WebDAV 备份](@/guides/backup.zh.md#webdav)。 |
| 默认 TUN MTU | 1500 | 9000 |
| 原始（raw）路由配置 | 支持 | 不支持。来自桌面版备份的原始路由配置会以只读形式保留。 |
| sing-box 仪表板 | `工具`（Tools）→ `打开网络仪表盘`（Open Web dashboard）。sing-box API 默认关闭。 | 侧边菜单 → `sing-box 仪表板`（sing-box dashboard）。该 API 默认开启，并且只在本设备上监听。 |
| 断网保护 | [Kill Switch（断网保护）](@/advanced/kill_switch.zh.md) | Android 的“屏蔽未使用 VPN 的所有连接”（Block connections without VPN）最为接近。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。 |

TLS 伪装、Tailscale 和额外核心（Extra Core）配置、OTP 管理器、路由配置中的 OpenVPN 和 OpenConnect 端点、Throne API、`thronectl` 以及无界面模式在 Android 上均不可用。

长按屏幕底部的横栏，可以打开 `实时图表`（Live graphs）（核心的速度、CPU 占用、内存和连接数）和 `测试当前连接的速度`（Speed test current connection）。

## 配置列表布局 {#layout}

- 横屏时，工具栏中的 `双列`（Double column）按钮会把较小的卡片并排显示。竖屏时，列表始终只有一列。
- `设置`（Settings）→ `外观`（Appearance）→ `紧凑配置行`（Compact profile rows）将每个配置显示为一行，仅含名称和延迟。
- `设置` → `外观` → `紧凑分组卡片`（Compact group cards）会缩短 `分组`（Groups）界面上的卡片。订阅分组以用量进度条、剩余时间和刷新按钮代替订阅链接与 `更新订阅`（Update subscription）。

双列卡片和紧凑行没有编辑和分享按钮。请改用该行的 ⋮ → `编辑`（Edit）或 `分享`（Share）。

## Android 上的桌面版菜单 {#menus}

共用指南中展示的是桌面版的菜单。在 Android 上，相同的选项位于：

| Throne 桌面版 | Throne for Android |
| --- | --- |
| `分组`（Groups）→ `管理分组`（Manage Groups） | 侧边菜单 → `分组`（Groups） |
| `路由`（Routing）→ `路由设置`（Routing Settings）→ `路由`（Route）标签页 | 侧边菜单 → `路由`（Routing） |
| `路由设置` → `通用`（Common）标签页 | `设置`（Settings）→ `路由`（Routing） |
| `路由设置` → `DNS` 标签页 | `设置` → `DNS` |
| `路由设置` → `Warp` 标签页 | `设置` → `路由` → `WARP` |
| `设置`（Settings）→ `Tun 设置`（Tun Settings） | `设置` → `TUN / VPN` |
| `设置` → `预设设置`（Preset Settings） | `设置` → `预设`（Presets） |
| `设置` → `基本设置`（Basic Settings）→ `订阅`（Subscription） | `设置` → `订阅`（Subscriptions） |
| `设置` → `基本设置` → `备份和恢复`（Backup and Restore） | 侧边菜单 → `工具`（Tools） |
| `设置` → `基本设置` → `诊断`（Diagnostics） | `设置` → `诊断`（Diagnostics） |
