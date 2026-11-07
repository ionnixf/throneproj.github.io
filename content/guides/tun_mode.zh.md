+++
title = "TUN 模式"
description = "开启 TUN 模式，在 Windows、Linux 和 macOS 上授予 Throne 所需的权限，调整 TUN 设置，并解决常见的 TUN 问题。"
weight = 30
toc = true
+++

在 TUN 模式下，Throne 会创建一个虚拟网卡，并让整台电脑的流量都经过它，包括 DNS 查询和忽略代理设置的应用。如何在 TUN 模式和系统代理之间选择，请参见[该用哪种模式](@/guides/proxy_modes.zh.md#which-mode)。

## 开启 TUN 模式 {#enable}

1. 在主窗口勾选 `Tun 模式`（Tun Mode），或在托盘菜单中使用 `操作模式`（Operation Mode）→ `Tun 模式`（Tun Mode）。
2. 第一次使用时，Throne 会请求 TUN 模式所需的权限。请接受该请求，参见[权限](#privileges)。
3. 如果没有配置档在运行，请启动一个配置档。

开启或关闭 TUN 模式都会重启正在运行的配置档。TUN 模式只在配置档运行时生效；要在没有配置档运行时拦截流量，请开启[断网保护（kill switch）](@/advanced/kill_switch.zh.md)。

## 权限 {#privileges}

TUN 模式需要系统权限，才能创建网卡并更改电脑的路由。如果你自行授予这些权限（例如使用下文的 Linux 能力），`设置`（Settings）→ `基本设置`（Basic Settings）→ `安全`（Security）→ `禁止权限请求`（Disable Privilege request）可让 Throne 不再请求权限（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

### Windows {#privileges-windows}

- Throne 必须以管理员身份运行。第一次勾选 `Tun 模式`（Tun Mode）时，点击 `是`（Yes）并接受 Windows 的提示：Throne 会以管理员身份重新启动，并开启 TUN 模式。如果随后 Windows 防火墙询问是否允许 `ThroneCore`，请允许。
- 此后 Throne 每次启动时都会请求管理员权限。要停止这种行为，请勾选 `设置`（Settings）→ `基本设置`（Basic Settings）→ `安全`（Security）→ `始终以标准用户身份启动`（Always Start as Standard User）（仅高级模式）；之后 Throne 会在你开启 TUN 模式时请求权限。
- 如果你在 Throne 以管理员身份运行时开启 `程序`（Program）→ `随系统启动`（Start with system），Throne 会在登录时以管理员权限启动，不会弹出提示。

另请参见[常见问题](@/help/faq.zh.md#tun-admin)。

### Linux {#privileges-linux}

- 只有核心 `ThroneCore` 需要 root 权限。第一次勾选 `Tun 模式`（Tun Mode）时，点击 `是`（Yes）并输入你的密码：Throne 会通过 `pkexec`（polkit 的一部分）把 `ThroneCore` 的所有者设为 root，并设置 SUID 位。然后再次勾选 `Tun 模式`（Tun Mode）。
- 切勿用 `sudo` 启动 Throne 本身。
- 除了使用 SUID 位，你也可以为核心授予以下五项能力（capabilities）。只有同时具备这五项时，Throne 才会接受该核心：

```bash
sudo setcap cap_net_admin,cap_net_raw,cap_net_bind_service,cap_sys_ptrace,cap_dac_read_search+ep /path/to/Throne/ThroneCore
```

有些第三方软件包无法自行修改核心，会改为显示该软件包提供的说明。另请参见[常见问题](@/help/faq.zh.md#linux-suid)。

### macOS {#privileges-macos}

- 在开启 TUN 模式之前，请把 `Throne.app` 移到 `/Applications`。当应用位于 `Downloads` 中时，设置权限可能会失败。
- 第一次勾选 `Tun 模式`（Tun Mode）时，点击 `是`（Yes）。终端（Terminal）会打开，并为核心运行一条 `sudo` 命令。在终端中输入你的密码，然后再次勾选 `Tun 模式`（Tun Mode）。
- `sudo` 需要管理员账户。在标准账户下，请改用系统代理。

## TUN 设置 {#settings}

打开 `设置`（Settings）→ `Tun 设置`（Tun Settings）。如果 TUN 模式处于开启状态，请将其关闭后再重新开启，以应用更改。在简洁模式下，只显示 `Tun 启用 IPv6`（Tun Enable IPv6）、`严格路由`（Strict Route）、`启用 Tun 路由`（Enable Tun Routing）和 `重启核心`（Restart Core）（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `MTU` | `1500` | 网卡的最大数据包大小，范围为 1000 到 10000。开启 `Tun 启用 IPv6` 时，请至少设为 1280，否则 IPv6 可能无法工作。 |
| `Tun 启用 IPv6`（Tun Enable IPv6） | 关闭 | 让 IPv6 流量经过隧道。仅当你的服务器支持 IPv6 时才开启。 |
| `严格路由`（Strict Route） | Windows 10 及更高版本上开启，其他系统上关闭 | 在 Windows 上，拦截绕过隧道的 DNS 查询；参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route)。 |
| `启用 Tun 路由`（Enable Tun Routing） | 关闭 | 把路由设为 `direct` 的 IP 范围和 `geoip-*` 规则集排除在隧道之外。大型规则集可能会在 Windows 上导致非常高的 CPU 占用。 |
| `自动重定向`（Auto Redirect） | 开启 | 仅限 Linux。借助 nftables 改善路由和速度。开启期间，这台电脑不能用作其他设备的网络网关。 |
| `L3 桥接绕过`（L3 Bridge Bypass） | 关闭 | 把直连的 UDP 和 ICMP 流量直接从网卡发出：速度更快，并能让游戏和 P2P 的 NAT 正常工作。在 ARM 版 Windows 上不可用。 |
| `IPv4 CIDR`、`IPv6 CIDR` | `172.19.0.1/24`、`fdfe:dcba:9876::1/96` | 网卡的地址。仅当它们与你使用的某个网络冲突时才需要更改。 |
| `私有地址范围绕过`（Private Range Bypass） | 开启 | 不经过隧道、直接走你的网卡的地址范围，每行一个。 |
| `重启核心`（Restart Core） | – | 停止正在运行的配置档并重启核心，用于 TUN 模式无法启动的情况。 |

默认的 `私有地址范围绕过`（Private Range Bypass）列表为 `10.0.0.0/8`、`172.16.0.0/12`、`192.168.0.0/16`、`169.254.0.0/16`、`224.0.0.0/4`、`fc00::/7`、`fe80::/10` 和 `ff00::/8`。

## 副作用 {#side-effects}

### 局域网与虚拟机 {#side-effects-vms}

开启 `私有地址范围绕过`（Private Range Bypass）后，打印机、网络驱动器、路由器和其他本地设备会在隧道之外直接访问，除非某条路由规则把它们的地址范围发往代理或将其拦截。Docker、WSL 和虚拟机同样会绕过隧道；如果它们的网络使用的地址范围不在列表中，请将其添加进去。参见[常用方案](@/guides/recipes.zh.md#docker-wsl-vms)。

### Windows 上的移动热点 {#side-effects-hotspot}

如果 Windows 移动热点或 Internet 连接共享共享的是你的普通网卡，TUN 模式会失败，Throne 会警告“IPv4 转发导致 Tun 模式失效”（IPv4 forwarding breaks Tun mode）。请在移动热点设置中将“Share my internet connection from”（从以下位置共享我的 Internet 连接）设为 `throne-tun`，或者在使用 TUN 模式时关闭热点。参见[常用方案](@/guides/recipes.zh.md#hotspot)。

### 其他 VPN {#side-effects-vpn}

同一台电脑上的两个 VPN 会争夺路由和 DNS。开启 TUN 模式时，请关闭其他 VPN 应用。如果需要同时访问公司网络，请参见[常用方案](@/guides/recipes.zh.md#corporate-vpn)。

### DNS {#side-effects-dns}

所有 DNS 查询都会到达 Throne，但对于直连的网站，DNS 泄漏测试仍可能显示你的运营商；参见 [DNS](@/guides/dns.zh.md#leak-tests)。

在 macOS 上，TUN 模式运行期间，Throne 会把你的网络服务（例如 `Wi-Fi`）的 DNS 服务器设为 `127.0.0.53`，由核心在那里按照 Throne 的 DNS 设置进行应答。TUN 模式停止时，Throne 会恢复你原先的服务器。

## 故障排除 {#troubleshooting}

打开 `日志`（Logs）标签页，查找 TUN 模式启动时的错误。通用步骤请参见[故障排除](@/help/troubleshooting.zh.md#tun)。

### Windows {#troubleshooting-windows}

**已连接，但什么都打不开。** Windows 防火墙可能拦截了 `ThroneCore`。请在 `Windows 安全中心`（Windows Security）→ `防火墙和网络保护`（Firewall & network protection）→ `允许应用通过防火墙`（Allow an app through firewall）中，为专用网络和公用网络允许它。

**杀毒软件。** 某些杀毒软件（例如 Avast 和 ESET）会拦截网卡或核心。请为 Throne 文件夹添加例外。

**“严格的路由不可用”（Strict routing unavailable）。** 在 `Tun 设置`（Tun Settings）中取消勾选 `严格路由`（Strict Route），然后重新启动配置档。没有它，DNS 查询可能会泄漏；参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md)。

**“Tun 设备运行异常”（Tun device misbehaving）。** 点击 `重置`（Reset）重启核心，然后重新启动配置档。

**“剩余 Tun 适配器”（Leftover Tun adapter）。** 崩溃或强制退出之后，Windows 可能仍保留着上一次会话的网卡。点击 `再次启动`（Start Again）。如果仍然失败，请重启 Windows。

**TUN 模式启动非常慢。** TUN 模式启动时，Throne 会清除 Windows 的 DNS 缓存；如果 HOSTS 文件非常大（例如广告拦截列表），这一步会非常慢。请缩小 HOSTS 文件。

### Linux {#troubleshooting-linux}

**“Please install "pkexec" first.”（请先安装 "pkexec"。）** 使用包管理器安装 polkit，或者设置 [Linux 权限](#privileges-linux)中所示的能力。

**TUN 模式启动时出现“file exists”。** 在 `Tun 设置`（Tun Settings）中关闭 `自动重定向`（Auto Redirect）。

**使用 systemd-resolved 时没有 DNS。** 如果 `/etc/systemd/resolved.conf` 中为所有接口开启了 `DNSOverTLS`，TUN 模式就会失效。请在那里将其关闭，或者在 Throne 网卡存在期间为该网卡关闭它：

```bash
sudo resolvectl dnsovertls throne-tun no
```

**TUN 模式停止后 DNS 无法使用（本地 DNS 代理）。** `127.0.0.1` 上的本地 DNS 代理（例如 `dnscrypt-proxy`、`unbound` 或 `dnsmasq`）按 IP 与上游服务器建立的连接可能会被隧道捕获；重启该代理后 DNS 才会恢复。要避免此问题，请把上游地址添加到 `Tun 设置`（Tun Settings）中的 `私有地址范围绕过`（Private Range Bypass），例如对于 `https://1.1.1.1/dns-query` 添加 `1.1.1.1/32`（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。然后将 TUN 模式关闭再重新开启，并重启该代理。

**来自 AUR、Nix 或其他软件源的软件包。** 它们由社区维护。如果 TUN 模式在这些软件包中无法工作，请试用官方构建版本；参见[常见问题](@/help/faq.zh.md#third-party-packages)。

### macOS {#troubleshooting-macos}

**TUN 模式启动时出现关于“the system DNS”的错误。** 核心无法把系统 DNS 指向自己（参见 [DNS](#side-effects-dns)）；消息的后半部分说明了原因。点击 `Tun 设置`（Tun Settings）中的 `重启核心`（Restart Core），然后重新启动配置档。

**Throne 被强制退出或崩溃后没有 DNS。** 如果某个网络服务的 DNS 服务器仍是 `127.0.0.53`，请启动一次 Throne：它的核心会恢复你原先的服务器。你也可以在 macOS 的网络设置中，自己从该服务中删除 `127.0.0.53`。

## Android 版 {#android}

在 `VPN` 模式下，Throne for Android 的工作方式类似 TUN 模式。其设置位于 `设置`（Settings）→ `TUN / VPN`；参见 [VPN 与代理模式](@/android/modes.zh.md#tun-settings)。
