+++
title = "Kill Switch（断网保护）"
description = "拦截所有不经过 Throne 的流量（即使没有配置档运行时也是如此），并在断网保护把你锁在网络之外时恢复连接。"
weight = 65
toc = true
+++

开启断网保护（kill switch）后，你的电脑只能通过 Throne 发送流量：通过 TUN 模式、本地代理端口，或者作为 Throne 自身的连接。其他所有流量都会被拦截，即使没有配置档运行时也是如此，因此代理停止或连接中断时，应用无法退回到你的普通连接。

{% alert_warning() %}
开启断网保护之前，请先阅读[被锁在网络之外时](#recovery)。
{% end %}

## 它的作用 {#what-it-does}

| 情况 | 哪些流量能通过 |
| --- | --- |
| 配置档以 TUN 模式运行 | 全部流量，经过 TUN 网卡。 |
| 配置档仅以系统代理运行 | 使用代理的应用。忽略代理的应用会被拦截。 |
| 没有配置档运行 | 只有本地网络和[下文的例外](#allowed)。 |

请将 TUN 模式与断网保护配合使用，这样在配置档运行期间所有应用都能正常工作。Throne 退出或崩溃时，断网保护随之结束：崩溃不会让你断网，但此后它也不再保护你。

## 开启断网保护 {#enable}

1. 打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `安全`（Security）（在简洁模式下，是 `通用`（Common）标签页中的 `安全` 分区）。
2. 勾选 `Kill Switch`，然后点击 `确定`（OK）。

断网保护需要与 TUN 模式相同的权限，参见[要求](#requirements)。在 Linux 和 macOS 上，开启它会重启 Throne 的核心和正在运行的配置档。在你关闭它之前，它每次都会随 Throne 一起启动。

之后窗口标题会显示 `[Kill switch]`，启动按钮上会出现一个锁：

- **绿色：** 配置档正以 TUN 模式运行。
- **琥珀色：** 没有配置档运行或 TUN 模式已关闭，因此大部分流量会被拦截。
- **红色：** 断网保护出现了问题，参见[无法工作时](#failures)。

要关闭它，取消勾选 `Kill Switch`，或者右键点击启动按钮 → `Disable Kill Switch`。

## 始终允许的流量 {#allowed}

- **Throne 的核心：** 到你服务器的连接、被你的规则设为 `direct` 的流量、URL 测试，以及 Throne 自身的 DNS 服务器。[额外核心](@/advanced/chains.zh.md#extra-core)（Extra Core）程序也会被放行。
- **TUN 网卡和环回地址**（`127.0.0.1`、`::1`），应用通过环回地址访问本地代理端口。
- **本地网络：** `10.0.0.0/8`、`172.16.0.0/12`、`192.168.0.0/16`、`169.254.0.0/16`、`224.0.0.0/4`、`255.255.255.255`、`fc00::/7`、`fe80::/10` 和 `ff00::/8`。在 `设置`（Settings）→ `Tun 设置`（Tun Settings）中关闭 `私有地址范围绕过`（Private Range Bypass）后，这些范围也会被拦截；修改它的列表不会改变这些范围。
- **网络基础协议：** DHCP、IPv6 邻居发现和路由器发现，以及组播组消息，让你的电脑保留它的地址。
- **[Throne API](@/advanced/api.zh.md) 的回复**，当它在某个网络地址上监听时。

其他程序发往本地地址的 DNS 查询（端口 53 和 853）仍会被拦截，因为路由器会把这些查询转发到隧道之外。在 Linux 上，如果 `/etc/resolv.conf` 中指定了这样的服务器，Throne 会发出警告：在 TUN 模式下，请使用 systemd-resolved（Throne 会把它指向隧道），或者使用你的网络之外的 DNS 服务器。

其他所有流量都会被拦截，包括：

- Throne 自身的请求，例如订阅更新、更新检查和 WARP 注册。它们只有在 TUN 模式下，或者在配置档运行且开启了 `系统代理`（System Proxy）或 `设置`（Settings）→ `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）→ `使用代理`（Use proxy）时才能工作。
- 这台电脑为其他设备、容器或虚拟机转发的流量，除非它经过 TUN 网卡或留在本地网络内。
- 其他 VPN 应用。

## 要求 {#requirements}

| 系统 | 断网保护需要什么 |
| --- | --- |
| Windows | Throne 以管理员身份运行，并且 `Base Filtering Engine` 服务正在运行。 |
| Linux | 核心通过 SUID 位以 root 身份运行；只具有能力（`setcap`）是不够的。需要 Linux 5.12 或更高版本，并支持 nftables 和 conntrack 标签（`CONFIG_NFT_CT`、`CONFIG_NF_CONNTRACK_LABELS`）。 |
| macOS | 核心通过 SUID 位以 root 身份运行。`/etc/pf.conf` 必须加载 `com.apple` 锚点，默认情况下就是如此。 |

Throne 会像为 TUN 模式那样设置这些权限，参见 [TUN 模式的权限](@/guides/tun_mode.zh.md#privileges)。

## 无法工作时 {#failures}

当断网保护已开启但无法启动或停止工作时，Throne 会显示“Kill switch is not active”（断网保护未生效）及其原因。缺少权限时点击 `Restart as Administrator`（Windows）或 `Grant Privileges`（Linux、macOS），其他问题点击 `Retry`，或者点击 `Disable Kill Switch`。在此之前，不会拦截任何流量，Throne 也不会启动配置档。

日志和 [Throne API](@/advanced/api.zh.md) 中的 `kill_switch_error` 会以代码形式给出原因：

| 代码 | 含义 |
| --- | --- |
| `privilege` | 缺少权限，参见[要求](#requirements)。 |
| `unsupported` | 系统缺少[要求](#requirements)中的其他条件，例如 Linux 上的 nftables 或以 root 运行的核心。 |
| `config` | 断网保护的设置无效，例如 TUN 地址。 |
| `tampered` | 其他程序反复移除规则或关闭防火墙。 |
| `crash`、`timeout`、`internal` | 守卫进程已停止或未能及时启动。`Retry` 通常有效。 |
| `overridden` | 无界面 Throne 是以 `--no-kill-switch` 启动的。 |

在 Linux 上，在没有 `CAP_SETGID` 的容器或用户命名空间中，断网保护会继续拦截所有流量，Throne 则拒绝启动配置档（窗口标题显示 `[Kill Switch 正在阻断流量]`），直到你点击 `Disable Kill Switch`。

## 被锁在网络之外时 {#recovery}

Throne 的设置无需互联网连接即可使用，因此可以在设置中或通过启动按钮关闭断网保护。

如果 Throne 没有响应：

1. 退出 Throne，或者在任务管理器中结束它。它的守卫进程会在几秒内移除防火墙规则。
2. 如果流量仍被拦截，请自行结束守卫进程：
   - **Windows：** 在任务管理器 → `详细信息`（Details）中结束所有 `ThroneCore.exe`，或者重启 Windows。
   - **Linux：** 运行 `sudo pkill -f 'ThroneCore --guard'`。
   - **macOS：** 运行 `sudo pkill -f 'ThroneCore --guard'`。如果规则仍然存在，请再次启动 Throne（它的核心会移除这些规则）或者重启 macOS。
3. 启动 Throne，并在做其他任何事情之前先关闭断网保护。

## Android 版 {#android}

Throne for Android 没有断网保护。请使用 Android 自带的功能：将 Throne 设为始终开启的 VPN，并开启“屏蔽未使用 VPN 的所有连接”（Block connections without VPN）。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。

## 无界面 Throne {#headless}

[无界面 Throne](@/headless/_index.zh.md) 具有相同的断网保护：`thronectl settings set kill_switch=true`。在 Linux 上，它需要一个以 root 身份运行的单元（unit），或者一个带有 SUID 位的核心，参见 [Linux 上的权限](@/headless/installation.zh.md#linux-privileges)。如果断网保护让你无法访问这台电脑，请用 `--no-kill-switch` 启动 Throne 一次，参见[恢复访问](@/headless/recovery.zh.md#recovery)。
