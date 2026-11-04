+++
title = "TUN 模式"
description = "开启 TUN 模式，在 Windows、Linux 和 macOS 上授予 Throne 所需的权限，调整 TUN 设置，并解决常见的 TUN 问题。"
weight = 30
toc = true
+++

在 TUN 模式下，Throne 会创建一个虚拟网卡，并让整台电脑的流量都经过它。这样可以覆盖忽略代理设置的应用，DNS 查询也同样涵盖在内。当系统代理不够用时，请使用 TUN 模式。两种模式的对比请参见[该用哪种模式](@/guides/proxy_modes.zh.md#which-mode)。

## 开启 TUN 模式 {#enable}

1. 在主窗口勾选 `Tun 模式`（Tun Mode），或在托盘菜单中使用 `操作模式`（Operation Mode）→ `Tun 模式`（Tun Mode）。
2. 第一次使用时，Throne 会请求 TUN 所需的权限。请接受该请求（见下文）。
3. 如果没有配置档在运行，请启动一个配置档。

TUN 模式生效期间，窗口标题会显示 `[Tun]`。当 Throne 拥有 TUN 所需的权限时，标题以 `[Admin]` 开头。开启或关闭 TUN 模式都会重启正在运行的配置档。

TUN 模式只在配置档运行时生效。停止配置档后，你的应用会重新使用普通连接。要在没有配置档运行时拦截它们的流量，请开启[断网保护（kill switch）](@/advanced/kill_switch.zh.md)（自 1.4.0 起）。开启 `程序`（Program）→ `记住上次的配置档`（Remember last profile）后，Throne 启动时会再次启动上次的配置档；如果你退出时 TUN 模式处于开启状态，还会重新开启它。

第一次使用时会发生什么：

- **Windows：** Throne 会询问“请以管理员身份运行 Throne”。点击 `是`（Yes）。Throne 会关闭，并以管理员身份重新启动，同时开启 TUN 模式；在此之前，Windows 会先显示用户账户控制提示。之后 Windows 防火墙可能会询问是否允许 `ThroneCore`。请允许：如果你阻止了它，TUN 虽然能连接，但什么都打不开。
- **Linux：** Throne 会询问“请赋予核心 root 权限”。点击 `是`（Yes），并在系统提示中输入你的密码。Throne 随后会为核心授予 root 权限。再次勾选 `Tun 模式`（Tun Mode）。
- **macOS：** Throne 会询问“请赋予核心 root 权限”。点击 `是`（Yes）。终端（Terminal）会打开并运行一条 `sudo` 命令。在终端中输入你的密码，然后再次勾选 `Tun 模式`（Tun Mode）。

## 权限 {#privileges}

TUN 模式需要系统权限，才能创建网卡并更改电脑的路由。

### Windows {#privileges-windows}

- Throne 必须以管理员身份运行。第一次点击 `Tun 模式`（Tun Mode）时，Throne 会提出以管理员身份重新启动。
- Throne 以管理员身份运行过一次后，每次启动时都会请求管理员权限。要停止这种行为，请勾选 `设置`（Settings）→ `基本设置`（Basic Settings）→ `安全`（Security）→ `始终以标准用户身份启动`（Always Start as Standard User）（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。之后每次你开启 TUN 模式时，Throne 都会再次请求权限。
- 如果你在 Throne 以管理员身份运行时开启 `程序`（Program）→ `随系统启动`（Start with system），Throne 会在登录时以管理员权限启动，不会弹出提示。

另请参见[常见问题](@/help/faq.zh.md#tun-admin)。

### Linux {#privileges-linux}

- 只有核心 `ThroneCore` 需要 root 权限。Throne 通过 `pkexec` 为它授予这些权限：把 `ThroneCore` 的所有者设为 root，并设置 SUID 位（`chown root:root` 和 `chmod u+s`）。
- `pkexec` 是 polkit 的一部分。如果缺少它，Throne 会显示“Please install "pkexec" first.”（请先安装 "pkexec"。）
- 切勿用 `sudo` 启动 Throne 本身。
- 除了使用 SUID 位，你也可以为核心授予以下五项能力（capabilities）：`CAP_NET_ADMIN`、`CAP_NET_RAW`、`CAP_NET_BIND_SERVICE`、`CAP_SYS_PTRACE` 和 `CAP_DAC_READ_SEARCH`。只有当核心同时具备这五项能力时，Throne 才会认为它已获得权限。

```bash
sudo setcap cap_net_admin,cap_net_raw,cap_net_bind_service,cap_sys_ptrace,cap_dac_read_search+ep /path/to/Throne/ThroneCore
```

有些第三方软件包无法自行修改核心。它们会显示“This installation cannot grant the core privileges by itself.”（此安装无法自行为核心授予权限。），并附上该软件包提供的说明。另请参见[常见问题](@/help/faq.zh.md#linux-suid)。

### macOS {#privileges-macos}

- 在开启 TUN 模式之前，请把 `Throne.app` 移到 `/Applications`。权限是通过终端设置的，而当应用位于 `Downloads` 中时，这一步可能会失败。
- Throne 会打开终端，并为核心运行一条 `sudo chown root:wheel … && sudo chmod u+s …` 命令。在终端中输入你的密码，然后再次勾选 `Tun 模式`（Tun Mode）。
- `sudo` 需要管理员账户。在标准账户下，你可以使用系统代理，但无法使用 TUN 模式。

### 关闭权限请求 {#disable-privilege-request}

`设置`（Settings）→ `基本设置`（Basic Settings）→ `安全`（Security）→ `禁止权限请求`（Disable Privilege request）（仅高级模式）会让 Throne 不再请求权限。之后 Throne 会以它已有的权限启动 TUN 模式，日志中会显示“用户选择无权限请求，某些功能可能不起作用”。仅当你自行授予权限时才使用此选项，例如你总是以管理员身份启动 Throne，或者你已经设置了上文所示的能力。

## TUN 设置 {#settings}

打开 `设置`（Settings）→ `Tun 设置`（Tun Settings）。如果在你点击 `确定`（OK）时 TUN 模式处于开启状态，Throne 会显示“重启 Tun 以生效。”请将 `Tun 模式`（Tun Mode）关闭后再重新开启。

在简洁模式下，该窗口只显示 `Tun 启用 IPv6`（Tun Enable IPv6）、`严格路由`（Strict Route）、`启用 Tun 路由`（Enable Tun Routing）和 `重启核心`（Restart Core）。其他设置会被隐藏，但保留其值；参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `MTU` | `1500` | 网卡的最大数据包大小。可选择 `1500` 或 `9000`，也可以输入 1000 到 10000 之间的值。自 1.4.0 起，`确定`（OK）不接受其他值；当 `Tun 启用 IPv6` 开启且该值低于 1280 时，Throne 会发出警告，因为此时系统可能会丢弃 IPv6。 |
| `Tun 启用 IPv6`（Tun Enable IPv6） | 关闭 | 为网卡分配一个 IPv6 地址，使 IPv6 流量经过隧道。 |
| `严格路由`（Strict Route） | Windows 10 及更高版本上开启，其他系统上关闭 | 在 Windows 上，拦截试图绕过隧道的 DNS 查询。参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route)。 |
| `启用 Tun 路由`（Enable Tun Routing） | 关闭 | 把路由设为 `direct` 的 IP 范围和 `geoip-*` 规则集排除在隧道之外，由系统绕过核心直接路由它们。大型规则集可能会在 Windows 上导致非常高的 CPU 占用。 |
| `自动重定向`（Auto Redirect） | 开启 | 仅限 Linux。借助 nftables 改善 TUN 路由和速度。开启期间，这台电脑不能用作其他设备的网络网关。 |
| `L3 桥接绕过`（L3 Bridge Bypass） | 关闭 | 把被规则设为 `direct` 的 UDP 和 ICMP 流量直接从网卡发出，而不是为其新建一个连接。这样速度更快，并能让游戏和 P2P 的 NAT 正常工作。TCP 仍走普通的直连路径。在 ARM 版 Windows 上不可用。 |
| `IPv4 CIDR` | `172.19.0.1/24` | 网卡的 IPv4 地址。仅当它与你使用的某个网络冲突时才需要更改。`恢复默认地址`（Restore default addresses）会重置两个地址。 |
| `IPv6 CIDR` | `fdfe:dcba:9876::1/96` | 网卡的 IPv6 地址。仅在开启 `Tun 启用 IPv6` 时使用。 |
| `私有地址范围绕过`（Private Range Bypass） | 开启 | 不经过核心、直接走你的网卡的地址范围，每行一个。`恢复默认范围`（Restore default ranges）会恢复该列表。环回地址和广播地址始终绕过隧道。 |
| `重启核心`（Restart Core） | – | 停止正在运行的配置档并重启核心。当 TUN 模式无法启动时使用，然后重新启动配置档。 |

默认的 `私有地址范围绕过`（Private Range Bypass）列表为 `10.0.0.0/8`、`172.16.0.0/12`、`192.168.0.0/16`、`169.254.0.0/16`、`224.0.0.0/4`、`fc00::/7`、`fe80::/10` 和 `ff00::/8`。

自 1.4.0 起，`Tun 设置`（Tun Settings）中不再有 `Stack` 设置。Throne 始终使用核心内置的网络协议栈，并忽略你在旧版本中选择的协议栈。

## 副作用 {#side-effects}

### 局域网 {#side-effects-lan}

开启 `私有地址范围绕过`（Private Range Bypass）后，你的电脑会在隧道之外直接访问打印机、网络驱动器、路由器的设置页面以及其他本地设备。如果关闭它，本地流量会经过核心，并由你的路由规则决定其去向。如果某条路由规则把某个私有地址范围发往代理或将其拦截，该范围就会保留在隧道内，使规则得以生效。

### Windows 上的移动热点与连接共享 {#side-effects-hotspot}

如果 Windows 移动热点或 Internet 连接共享共享的是你的普通网卡，Windows 会忽略那些让 Throne 自身连接不进入隧道的设置。此时 TUN 模式会失败，Throne 会警告“IPv4 forwarding breaks Tun mode”（IPv4 转发会破坏 TUN 模式）。请改为从 `throne-tun` 网卡共享热点：在 Windows 的移动热点设置中，将“Share my internet connection from”（从以下位置共享我的 Internet 连接）设为 `throne-tun`。或者在使用 TUN 模式时关闭热点。参见[常用方案](@/guides/recipes.zh.md#hotspot)。

### Docker、WSL 与虚拟机 {#side-effects-vms}

容器和虚拟机有自己的虚拟网络。它们与你的电脑之间的流量使用私有地址，因此通常会绕过隧道。如果这类网络使用的地址范围不在 `私有地址范围绕过`（Private Range Bypass）列表中，请将其添加进去。在 Linux 上请注意，`自动重定向`（Auto Redirect）会使电脑无法用作网络网关。参见[常用方案](@/guides/recipes.zh.md#docker-wsl-vms)。

### 其他 VPN {#side-effects-vpn}

同一台电脑上的两个 VPN 会争夺路由和 DNS。开启 TUN 模式时，请关闭其他 VPN 应用。如果需要同时访问公司网络，请参见[常用方案](@/guides/recipes.zh.md#corporate-vpn)。

### IPv6 {#side-effects-ipv6}

`Tun 启用 IPv6`（Tun Enable IPv6）默认关闭。仅当你的服务器支持 IPv6 时才开启它，否则可能会出现连接问题。

### DNS {#side-effects-dns}

在 TUN 模式下，所有应用的 DNS 查询都会到达 Throne。对于被你的规则设为直连的网站，DNS 泄漏测试仍可能显示你的运营商的 DNS 服务器。这是预期行为；参见 [DNS](@/guides/dns.zh.md#leak-tests)。

**macOS：** 自 1.4.0 起，TUN 模式运行期间，核心会把 TUN 网卡作为一个独立的网络服务添加到 macOS 的网络配置中。它的 DNS 服务器是隧道内的一个地址：`IPv4 CIDR` 地址的下一个地址，默认为 `172.19.0.2`。macOS 会把 DNS 查询发送到那里，由核心按照 Throne 的 DNS 设置进行应答。

- Throne 不会更改你的网络服务（例如 `Wi-Fi`）的 DNS 服务器，macOS 的网络设置中也不会显示这个 TUN 服务。
- TUN 模式停止时，或核心退出或崩溃时，macOS 会再次移除该 TUN 服务。
- 如果核心无法添加该服务，TUN 模式就不会启动；参见 [macOS 故障排除](#troubleshooting-macos)。

在 1.4.0 之前，Throne 会把 DNS 服务器设为 TUN 网卡的地址，并在 TUN 模式停止时清空 DNS 服务器，因此你自己设置的 DNS 服务器会丢失。崩溃之后，macOS 会保留一个已不再应答的 DNS 服务器。

## 故障排除 {#troubleshooting}

打开 `日志`（Logs）标签页，查找 TUN 模式启动时的错误。通用步骤请参见[故障排除](@/help/troubleshooting.zh.md#tun)。

### Windows {#troubleshooting-windows}

**已连接，但什么都打不开。** Windows 防火墙可能拦截了 `ThroneCore`。Windows 只会在你第一次开启 TUN 模式时询问一次。如果当时你阻止了它，请在 `Windows 安全中心`（Windows Security）→ `防火墙和网络保护`（Firewall & network protection）→ `允许应用通过防火墙`（Allow an app through firewall）中，为专用网络和公用网络允许 `ThroneCore`。

**杀毒软件。** 某些杀毒软件（例如 Avast 和 ESET）会拦截网卡或核心。请为 Throne 文件夹添加例外。

**“严格的路由不可用”。** Windows 无法开启严格路由。在 `Tun 设置`（Tun Settings）中取消勾选 `严格路由`（Strict Route），然后重新启动配置档。没有它，DNS 查询可能会泄漏；参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md)。

**“Tun 设备运行异常”。** 点击 `重置`（Reset）重启核心，然后重新启动配置档。

**崩溃或强制退出之后。** Windows 可能仍保留着上一次会话的虚拟网卡。自 1.4.0 起，这种情况下 Throne 会在 TUN 模式启动时显示“残留的 Tun 适配器”（Leftover Tun adapter）。点击 `再次启动`（Start Again），通常就能解决。如果仍然失败，请重启 Windows。这种情况下，启动可能需要大约 15 秒才会出现该提示。请耐心等待，不要重启 Throne，否则可能会再残留一个网卡。

**TUN 模式启动非常慢。** TUN 模式启动时，Throne 会清除 Windows 的 DNS 缓存。非常大的 HOSTS 文件（例如包含 100,000 条记录的广告拦截列表）会使这一步变得非常慢。请缩小 HOSTS 文件。

**移动热点。** 参见[移动热点与连接共享](#side-effects-hotspot)。

### Linux {#troubleshooting-linux}

**“Please install "pkexec" first.”（请先安装 "pkexec"。）** 使用包管理器安装 polkit，或者设置 [Linux 权限](#privileges-linux)中所示的能力。

**TUN 模式启动时出现“file exists”（文件已存在）。** 此错误来自 `自动重定向`（Auto Redirect），已在 1.2.2 中修复。如果你仍然遇到它，请关闭 `自动重定向`（Auto Redirect）。

**使用 systemd-resolved 时没有 DNS。** 如果 `/etc/systemd/resolved.conf` 中为所有接口开启了 `DNSOverTLS`，TUN 模式就会失效。请在那里将其关闭，或者在 Throne 网卡存在期间，为该网卡单独关闭它：

```bash
sudo resolvectl dnsovertls throne-tun no
```

**TUN 模式停止后 DNS 无法使用（本地 DNS 代理）。** 如果 `dnscrypt-proxy`、`unbound` 或 `dnsmasq` 等本地代理在 `127.0.0.1` 上提供 DNS 服务，它按 IP 与上游服务器建立的连接可能会被隧道捕获。TUN 模式停止后，DNS 可能仍然无法使用。刷新 DNS 缓存没有帮助，只有重启网络或该代理才能恢复 DNS。

要避免此问题，请把上游地址添加到 `设置`（Settings）→ `Tun 设置`（Tun Settings）→ `私有地址范围绕过`（Private Range Bypass），每行一个地址范围。对于 `https://1.1.1.1/dns-query`，请添加 `1.1.1.1/32`。这样该代理会通过你的网卡在隧道之外访问该服务器。修改列表后，请将 TUN 模式关闭再重新开启，然后重启该代理，让它建立新连接。在简洁模式下此设置是隐藏的；参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

**来自 AUR、Nix 或其他软件源的软件包。** 它们由社区维护。如果 TUN 模式在这些软件包中无法工作，请试用官方构建版本；参见[常见问题](@/help/faq.zh.md#third-party-packages)。

**Throne 是用 `sudo` 启动的。** 请退出它，并以普通用户身份启动。只有核心需要 root 权限。

### macOS {#troubleshooting-macos}

**macOS 提示应用已损坏或无法打开。** 移除隔离标记：

```bash
xattr -d com.apple.quarantine /Applications/Throne.app
```

**权限请求失败。** 把 `Throne.app` 移到 `/Applications`，从那里启动它，然后再次勾选 `Tun 模式`（Tun Mode）。

**标准账户。** TUN 模式需要管理员账户。请使用系统代理，或者使用管理员账户登录。

**TUN 模式启动时出现“set the system DNS”。** 自 1.4.0 起，如果核心无法把 TUN 服务及其 DNS 服务器添加到 macOS，TUN 模式就不会启动（参见 [DNS](#side-effects-dns)）。消息的后半部分说明了失败的环节。点击 `Tun 设置`（Tun Settings）中的 `重启核心`（Restart Core），然后重新启动配置档。

如果 macOS 网络设置中某个网络服务的 DNS 服务器仍是 `127.0.0.53`，那是 1.4.0 早期的测试版本留下的。请启动一次 Throne：如果它的核心拥有 root 权限，就会恢复该服务原先的 DNS 服务器。你也可以自己从该网络服务的 DNS 服务器中删除 `127.0.0.53`。

关于 Windows 上的 DNS 泄漏加固，请参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md)。

## Android 版 {#android}

在 `VPN` 模式下，Throne for Android 的工作方式类似 TUN 模式。其设置位于 `设置`（Settings）→ `TUN / VPN`。在那里，默认 MTU 为 9000，并且严格路由始终开启。自 2.0.2 起，那里也无法选择协议栈。参见 [VPN 与代理模式](@/android/modes.zh.md#tun-settings)。
