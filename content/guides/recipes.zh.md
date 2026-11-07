+++
title = "常用方案"
description = "常见任务的分步设置，并附有介绍相关功能的页面链接。"
weight = 80
toc = true
+++

## 仅代理被封锁的网站 {#proxy-only-blocked-sites}

**中国或俄罗斯：** 通过 `路由`（Routing）→ `下载配置档`（Download Profiles）下载配置档，然后在 `路由` 菜单中选择 `Proxy China Blocked`、`Proxy Russia Blocked`、`Proxy Antizapret` 或 `Proxy Refilter`。参见[下载现成的配置档](@/guides/routing.zh.md#download-profiles)。

**任何国家：** 在 `设置`（Settings）→ `路由设置`（Routing Settings）→ `路由`（Route）中点击 `新建`（New）→ `结构化配置档`（Structured profile），将 `默认出站`（Default outbound）设为 `direct`，并在 `基本`（Basic）标签页上把被封锁的网站填入 `代理`（Proxy）输入框，例如：

```text
ruleset:geosite-youtube
ruleset:geosite-telegram
ruleset:geoip-telegram
suffix:example.com
```

然后点击 `确定`（OK），并在 `路由` 菜单中启用该配置档。如果某个网站仍然无法打开，请在[连接标签页](@/guides/routing.zh.md#connections-tab)中添加它的其他域名（右键点击 → `代理`（Proxy））。会直接连接 IP 地址的应用（例如 Telegram）还需要它们的 `geoip-` 规则集和 `Tun 模式`（Tun Mode）。参见[简单规则](@/guides/routing.zh.md#simple-rules)。

## 与其他设备共享代理 {#share-with-devices}

适用于你网络中的手机、电视、游戏机和其他电脑：

1. 选择 `程序`（Program）→ `允许其他设备连接`（Allow other devices to connect），然后重启正在运行的配置档。
2. 在其他设备上设置 HTTP 或 SOCKS5 代理，地址和端口使用状态栏中 `Mixed:` 之后显示的值，例如 `192.168.1.20:2080`。

这些设备的流量会遵循你当前活动的路由配置档。请设置密码，并允许 `ThroneCore` 通过防火墙，参见[与其他设备共享代理](@/guides/proxy_modes.zh.md#lan-sharing)。Android 版请参见[局域网共享](@/android/modes.zh.md#lan-sharing)。

## 在 TUN 模式下使用 Windows 移动热点 {#hotspot}

当 `Tun 模式`（Tun Mode）开启时，如果 Windows 移动热点或 Internet 连接共享共享的是你的常规网卡，TUN 模式会失败，Throne 会警告“IPv4 forwarding breaks Tun mode”（IPv4 转发会破坏 TUN 模式）。请改为共享 Throne 的 TUN 网卡，用户也在 [#1916](https://github.com/throneproj/Throne/issues/1916) 中报告过这个变通方法：

1. 开启 `Tun 模式`（Tun Mode）并启动一个配置档。
2. 在 Windows 中打开 `设置`（Settings）→ `移动热点`（Mobile hotspot）→ `从以下位置共享我的 Internet 连接`（Share my internet connection from），然后选择 `throne-tun`。该网卡仅在 `Tun 模式` 开启时存在。
3. 开启热点。

如果这样不起作用，请在使用 `Tun 模式` 时关闭热点，或者让设备改用[代理端口](#share-with-devices)。

## 语音通话和游戏 {#calls-and-games}

Discord 和 Telegram 中的语音通话以及许多游戏都使用 UDP，并且经常忽略系统代理。

- 使用 `Tun 模式`（Tun Mode），这样这些流量才能到达 Throne。
- 使用协议能够承载 UDP 的服务器；HTTP 代理不行，有些服务器也会关闭 UDP。如果通话失败，请换一个服务器试试。
- 要让某个游戏直连，请将它的程序填入路由配置档的 `直连`（Direct）输入框，例如 `processName:game.exe`。
- 对于直连的游戏和点对点应用，请在 [TUN 设置](@/guides/tun_mode.zh.md#settings)中开启 `L3 桥接绕过`（L3 Bridge Bypass）（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

## Docker、WSL 和虚拟机 {#docker-wsl-vms}

让它们指向 Throne 的代理端口：

1. 开启 `程序`（Program）→ `允许其他设备连接`（Allow other devices to connect）。
2. 在客户机中将代理设为 `http://<address>:2080`，并使用客户机能够访问到的电脑地址，例如状态栏中显示的地址。同一端口也接受 SOCKS5。

Linux 上的许多命令行工具都会读取以下变量：

```bash
export http_proxy=http://192.168.1.10:2080
export https_proxy=http://192.168.1.10:2080
```

如果你改为使用 `Tun 模式`（Tun Mode），而客户机断网，请参见 [TUN 模式](@/guides/tun_mode.zh.md#side-effects-vms)。

## 与企业 VPN 并存 {#corporate-vpn}

请用以下方法之一让公司流量避开代理。

**让公司流量直连。** 将公司的域名和网络添加到路由配置档的 `直连`（Direct）输入框中：

```text
suffix:corp.example.com
ip:10.20.0.0/16
```

在 TUN 模式下，`10.0.0.0/8` 等私有地址范围本来就不会进入隧道；对于使用公网地址范围的公司网络，还请开启 `设置`（Settings）→ `Tun 设置`（Tun Settings）→ `启用 Tun 路由`（Enable Tun Routing）。

**绑定到 VPN 的网卡**，适用于直连流量从错误的网卡发出的情况。通过 `程序`（Program）→ `新建配置档`（New profile）创建一个类型为 `直连`（Direct）的配置档（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)），例如命名为 `Corporate`，并在它的 `高级设置`（Advanced Settings）→ `绑定接口`（Bind Interface）中填入 VPN 网卡的名称。然后在其他规则之上添加一条[高级规则](@/guides/routing.zh.md#advanced-rules)，以 `route` 动作把公司的 `domain_suffix` 和 `ip_cidr` 发送到 `[group] Corporate`。

**在 Throne 内运行 VPN。** Throne 可以在代理之外同时运行 OpenVPN 或 OpenConnect 配置档，仅用于该 VPN 所通告的网络。参见[分离隧道](@/advanced/vpn_profiles.zh.md#split-tunnel)。

如果问题只在 TUN 模式下出现，请在企业 VPN 连接期间使用系统代理模式。

## 按应用分流 {#per-app}

在桌面版上，按应用的规则只有在 `Tun 模式`（Tun Mode）下才能可靠地工作。

1. 开启 `Tun 模式`（Tun Mode），启动该程序，让它建立连接。
2. 在 `连接`（Connections）标签页中，右键点击它的某个连接，然后选择 `代理`（Proxy）、`直连`（Direct）、`阻止`（Block）或 `特定服务器`（Specific server）。
3. 勾选该程序，例如 `Process Telegram.exe`，点击 `确定`（OK），然后点击提示中的 `重启`（Restart）。

要只让部分程序走代理，请将 `默认出站`（Default outbound）设为 `direct`，并把这些程序填入 `代理`（Proxy）输入框，例如 `processName:Telegram.exe`。要让除部分程序外的所有程序走代理，请把例外的程序填入 `直连`（Direct）输入框。参见[连接标签页](@/guides/routing.zh.md#connections-tab)和[简单规则](@/guides/routing.zh.md#simple-rules)。

**Android：** 在 `设置`（Settings）→ `TUN / VPN` → `分应用代理`（Apps VPN mode）中选择应用，或者使用路由规则中的 `应用`（Apps）字段。参见[分应用代理](@/android/modes.zh.md#per-app-proxy)。

## 找到可用的 CDN 地址 {#cdn-address}

如果服务商的服务器位于 Cloudflare 等 CDN 之后，配置档可能会因为它们的 CDN 地址在你的网络中被封锁而失败，而同一 CDN 的其他地址仍然可用。要为整个分组找到这样的地址（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）：

1. 打开 `工具`（Tools）→ `IP Scanner`，点击 `New scan`，保持 `Generic scan` 并点击 `确定`（OK）。
2. 在 `Targets` 标签页上，将 `Base IP list` 选为你的 CDN 的列表，例如 `Cloudflare CDN`，在 `Ports` 中输入配置档的端口（通常是 `443`），并把 `Stop after (results)` 设为一个较小的数字，例如 `20`。
3. 在 `Phases` 标签页上，勾选 `Config test`，并将 `Profile` 选为该分组中的某个配置档。
4. 点击 `Start`。扫描完成后，在 `Results` 标签页上点击 `Save as IP list` 并输入名称。
5. 在该分组的选项中点击 `高级`（Advanced），将 `Server Address` 设为 `IP list`，选择新列表，然后点击两次 `确定`（OK）。
6. 对该分组运行 URL 测试。

这只适用于服务器确实位于该 CDN 之后的配置档，通常是基于 TLS、使用域名的 WebSocket、gRPC、HTTPUpgrade 或 XHTTP。参见[服务器地址](@/guides/subscriptions.zh.md#server-address)。
