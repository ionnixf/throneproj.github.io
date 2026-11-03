+++
title = "VPN 与代理模式"
description = "在 VPN 模式和仅代理模式之间做出选择，指定哪些应用使用 Throne，与其他设备共享代理，并调整 TUN 和入站设置。"
weight = 30
toc = true
+++

Throne for Android 有两种运行模式。`VPN` 会将应用的流量经由 Throne 发送。`仅代理`（Proxy only）会运行一个本地代理，只有你配置过的应用才会使用它。本页介绍这两种模式、分应用代理、与其他设备共享代理，以及 TUN 和入站设置。

## 选择运行模式 {#service-mode}

在 `设置`（Settings）→ `通用`（General）→ `运行模式`（Service mode）中设置模式。

| 模式 | 作用 | 适用场景 |
| --- | --- | --- |
| `VPN`（默认） | Android 会创建一个 VPN。Throne 通过虚拟网络接口（TUN）接收所有应用的流量，或仅接收你所选应用的流量。 | 你希望所有应用都使用 Throne。这是通常的选择。 |
| `仅代理`（Proxy only） | Throne 只在 `127.0.0.1` 上开启一个本地代理（SOCKS5 和 HTTP），默认端口为 `2080`。不会创建 VPN。 | 另一个 VPN 应用必须保持连接；你只需要在带有代理设置的应用中使用 Throne；或者你的设备无法授予 VPN 权限。 |

更改模式会停止正在运行的连接。再次点按连接按钮即可以新模式启动。

{% alert_info() %}
如果 Throne 在 Android 设置中被设为始终开启的 VPN，Android 会以 VPN 模式启动它。在 Android 10 及更高版本上，Throne 随后会把 `运行模式`（Service mode）切换回 `VPN`。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。
{% end %}

## VPN 模式 {#vpn-mode}

在 VPN 模式下，Android 会把应用的网络流量发送给 Throne。Throne 随后按照路由配置的规定，将每个连接经由代理发送、直接发送或阻止。

- 首次连接时，Android 会请你允许 VPN。参见 [VPN 权限](@/android/permissions.zh.md#vpn-permission)。
- Android 同一时间只运行一个 VPN。当 Throne 以 VPN 模式连接时，其他任何应用的 VPN 都会断开。
- 连接到手机热点的设备不会使用该 VPN，因为 Android 不会把它们的流量经由 VPN 应用发送。要让它们使用代理，请使用[局域网共享](#lan-sharing)。
- 要让某些应用不走 VPN，或只让某些应用走 VPN，请使用[分应用代理](#per-app-proxy)。

### VPN 的 HTTP 代理 {#http-proxy}

在 Android 10 及更高版本上，Throne 还可以将其本地代理（`127.0.0.1` 和 `代理端口`（Proxy port））设为 VPN 网络的 HTTP 代理。遵循系统代理设置的应用（例如大多数浏览器）随后会将其网页流量直接发送到该代理，而不是经由 VPN 接口。这部分流量仍然遵循你的路由配置。

自 2.0.2 起，这是一个设置项：`设置`（Settings）→ `入站`（Inbound）→ `为 VPN 附加 HTTP 代理`（Append HTTP proxy to VPN），默认关闭。在 2.0.0 和 2.0.1 中，Throne 总是会设置该 HTTP 代理。

- 开启 `启用授权`（Enable authorization）时，该设置不可用，因为 Android 无法把用户名和密码传给应用。`禁用混合入站`（Disable mixed inbound）也会关闭它。
- 要让某些域名不使用该代理，请将它们添加到 `设置` → `入站` → `HTTP 代理绕过列表`（HTTP proxy bypass list）。每行写一个条目，例如 `*example.com`。以 `#` 开头的行是注释。应用访问这些域名时不经过代理。该列表在 `为 VPN 附加 HTTP 代理` 开启时可用。

### 绕过 VPN 的应用 {#allow-bypass}

自 2.0.5 起，`设置`（Settings）→ `通用`（General）→ `允许应用绕过 VPN`（Allow apps to bypass VPN）允许请求绕过的应用不经过 VPN 直接连接，例如 Google 推送通知（FCM）。该设置默认关闭，因此使用 VPN 的应用无法离开 VPN。

开启后，这些连接会直接发出，不遵循你的路由配置。当始终开启的 VPN 与“屏蔽未使用 VPN 的所有连接”（Block connections without VPN）同时开启时，Android 会忽略该设置。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。如果更改该设置时 Throne 已连接，请在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply）。

## 仅代理模式 {#proxy-only}

在 `仅代理`（Proxy only）模式下，Throne 只运行一个本地代理，不做其他事情。Android 不会请求 VPN 权限，状态栏中也不会出现 VPN 钥匙图标。只有你设置为使用该代理的应用才会使用 Throne。在此模式下，TUN 设置和分应用代理不起作用。

1. 打开 `设置`（Settings）→ `通用`（General）→ `运行模式`（Service mode），选择 `仅代理`。
2. 返回配置列表，点按连接按钮。
3. 在需要使用 Throne 的应用中设置 SOCKS5 或 HTTP 代理，服务器为 `127.0.0.1`，端口为 `2080`（或你设置的 `代理端口`（Proxy port））。
4. 如果开启了 `启用授权`（Enable authorization），还需要输入用户名和密码。

本地代理在同一端口上同时接受 SOCKS5 和 HTTP。

在此模式下，Throne 自己的请求也会经由本地代理发送，就像开启了 `设置`（Settings）→ `订阅`（Subscriptions）→ `使用代理`（Use proxy）一样。因此，在 2.0.3 及更早版本中，订阅更新和 WARP 注册需要有正在运行的配置。没有运行的配置时，它们会失败并显示“请求使用代理但未启动配置。”自 2.0.4 起，没有运行的配置时，这些请求会直接连接，除非开启了 `使用代理`（Use proxy）。WebDAV 备份从不使用本地代理。

`禁用混合入站`（Disable mixed inbound）在 `仅代理` 模式下不起作用，因为本地代理是进入 Throne 的唯一途径。

## 分应用代理 {#per-app-proxy}

分应用代理决定哪些应用使用 VPN。它只在 VPN 模式下有效。

开启 `设置`（Settings）→ `TUN / VPN` → `分应用代理`（Apps VPN mode），应用列表随即打开。在列表顶部选择一种模式：

| 模式 | 效果 |
| --- | --- |
| `关闭`（Off） | 关闭分应用代理，所有应用都使用 VPN。选择它会关闭列表。 |
| `代理`（Proxy） | 只有所选应用使用 VPN，其他所有应用直接连接。 |
| `绕过`（Bypass） | 所选应用跳过 VPN，直接连接；其他所有应用使用 VPN。 |

打开列表即会开启分应用代理，首次打开时预选 `绕过`（Bypass）。要关闭分应用代理，请关闭 `分应用代理` 开关，或在列表中选择 `关闭`（Off）。Throne 会保留模式和所选应用，供下次使用。要再次打开列表，请先关闭再开启该开关。在 2.0.4 及更早版本中，该开关总是打开列表，无法关闭分应用代理，因此请在列表中选择 `关闭`。如果没有选择任何应用，那么在两种模式下所有应用都会使用 VPN。

选择应用：

1. 点按一个应用以选中它，再次点按则取消选中。
2. 使用 `搜索…`（Search…）按名称、包名或用户 ID 查找应用。
3. 关闭 `显示系统应用`（Show system apps）可隐藏系统应用。默认会显示系统应用。

每一行显示应用名称、包名，以及括号中的用户 ID。共享同一用户 ID 的应用会被一起选中。打开列表时，已选中的应用排在最前面。已选中但未安装的包会保留在列表中，并标记为“（未安装）”。自 2.0.5 起，在 `代理` 模式下，如果所选应用都未安装，则没有任何应用使用 VPN。在 2.0.4 及更早版本中，这种情况下所有应用都会使用 VPN。

`自动选择需要代理的应用`（Auto select proxy apps）会从一个内置列表中选择应用。该列表包含约 390 个常需要代理的应用，例如 Google 应用、YouTube、Facebook、Instagram 和 Netflix。它会替换对已安装应用的选择。你按名称添加的包或未安装的包仍保持选中。在 `代理`（Proxy）模式下，它会选中该列表中的应用；在 `绕过`（Bypass）模式下，它会选中其他所有应用。在这两种情况下，该列表中的应用都会使用 VPN，其他应用则不会。用户 ID 为 1000 的应用（Android 系统）被视为该列表中的应用。Throne 会先询问“自动选择代理应用？”。点按 `替换`（Replace）继续。

列表的工具栏和 ⋮ 菜单中有以下操作：

| 操作 | 作用 |
| --- | --- |
| `反选`（Invert selections） | 选中所有未选中的应用，并取消选中其他应用。 |
| `清空`（Clear selections） | 清除全部选择。 |
| `添加包名…`（Add package name…） | 按包名添加应用，例如列表无法显示的应用。多个包名之间可用空格、逗号或换行分隔。 |
| `导出到剪切板`（Export to clipboard） | 复制模式和所选包名。 |
| `从剪切板导入`（Import from clipboard） | 用复制的列表替换模式和选择。 |

剪贴板格式为纯文本。第一行为 `true` 表示 `绕过`（Bypass），为 `false` 表示 `代理`（Proxy）。之后每一行是一个包名：

```text
false
com.google.android.youtube
org.telegram.messenger
```

在 2.0.2 及更早版本中，导入时总是把第一行当作模式：第一行不区分大小写，第一行为 `true` 以外的任何内容都会选择 `代理`。自 2.0.3 起，如果列表的第一行既不是 `true` 也不是 `false`，则保留当前模式，只替换所选应用。因此你也可以导入一份纯包名列表，例如从路由规则的 `应用`（Apps）字段中复制的列表。自 2.0.5 起，只要有一行不是包名（例如 URL），Throne 就会拒绝整个列表，并显示“不是有效包名：…”。此时不会有任何更改。

如果 Throne 已连接，新的选择会在连接重新加载后生效。请停止并重新启动连接，或在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply）。

如果列表一直为空，请参见[已安装应用](@/android/permissions.zh.md#installed-apps)。

要把某个应用发送到特定出站、同时让它留在 VPN 中，请改用带有 `应用`（Apps）条件的路由规则。桌面版没有应用列表。在桌面版上，你需要使用按进程名匹配的路由规则。参见[路由](@/guides/routing.zh.md#advanced-rules)。

路由规则只能路由到达 Throne 的流量。如果分应用代理让某个应用不走 VPN，它的规则就永远不会匹配。针对这种情况，自 2.0.3 起，路由规则的应用列表提供 ⋮ → `复制到分应用代理`（Copy to Apps VPN mode）。它会在更改任何内容之前先说明各个选项：

- 在 `代理`（Proxy）模式下，它会把所选应用添加到列表中，或用它们替换列表。
- 在 `绕过`（Bypass）模式下，它会把它们从列表中移除，或切换到只包含这些应用的 `代理` 模式。
- 当分应用代理关闭时，它会用这些应用开启 `代理` 模式。其他所有应用随后都会跳过 VPN。

之后请重新加载连接。

## 局域网共享 {#lan-sharing}

局域网共享可以让同一网络中的其他设备（例如笔记本电脑或电视）把你手机上的 Throne 用作代理。它在两种运行模式下都有效，也适用于连接到你手机热点的设备。

1. 打开 `设置`（Settings）→ `入站`（Inbound），开启 `允许来自局域网的连接`（Allow connections from the LAN）。此后 Throne 会在所有网络接口上监听，而不仅仅是 `127.0.0.1`。
2. 开启 `启用授权`（Enable authorization），并设置 `用户名`（Username）和 `密码`（Password）。
3. 记下 `代理端口`（Proxy port），默认是 `2080`。
4. 如果 Throne 已连接，请在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply），或重新连接。
5. 查找手机的 IP 地址。它显示在 Android Wi-Fi 设置中所连接网络的详细信息里。对于连接到你热点的设备，请使用该设备在其连接信息中显示的路由器（网关）地址。
6. 在另一台设备上设置 SOCKS5 或 HTTP 代理，填写手机的 IP 地址、端口、用户名和密码。

{% alert_warning() %}
如果不启用认证，同一网络中的任何设备都可以使用你的代理。
{% end %}

当你恢复一份允许局域网连接的备份中的 `设置`（Settings）部分时，Throne 会关闭 `允许来自局域网的连接`（除非它原本就已开启），并显示警告。

在桌面版上，相同的功能叫做 `允许其他设备连接`（Allow other devices to connect）。参见[系统代理、TUN 与局域网共享](@/guides/proxy_modes.zh.md#lan-sharing)。

## TUN / VPN 设置 {#tun-settings}

这些设置位于 `设置`（Settings）→ `TUN / VPN`。它们只在 VPN 模式下生效。如果更改设置时 Throne 已连接，请在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply）。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `MTU` | `9000` | VPN 接口的最大数据包大小。可从列表中选择 `1500` 或 `9000`，也可以长按该设置项输入 1000 到 10000 之间的任意值。 |
| `Tun IPv6` | 关闭 | 为 VPN 接口分配 IPv6 地址，并通过它路由 IPv6。关闭时，Throne 运行期间 IPv6 连接会立即失败，因此应用会使用 IPv4。仅当你的服务器支持 IPv6 时才开启。 |
| `Tun 路由`（Tun routing） | 关闭 | 将路由配置发往 `direct` 的 IP 范围排除在 VPN 接口之外，使这部分流量不经过 Throne。 |
| `绕过私有网段`（Bypass private ranges） | 开启 | 将 `私有网段` 排除在 VPN 接口之外，以便直接访问本地网络中的设备。 |
| `私有网段`（Private ranges） | 8 个地址范围 | 排除在外的地址范围。每行一个地址或 CIDR。仅在开启 `绕过私有网段` 时可用。 |
| `恢复默认范围`（Restore default ranges） | — | 恢复默认的私有地址范围列表。 |
| `分应用代理`（Apps VPN mode） | 关闭 | 分应用代理。参见[分应用代理](#per-app-proxy)。 |
| `IPv4 CIDR` | `172.19.0.1/24` | VPN 接口的 IPv4 地址。 |
| `IPv6 CIDR` | `fdfe:dcba:9876::1/96` | VPN 接口的 IPv6 地址，在开启 `Tun IPv6` 时使用。 |
| `恢复默认地址`（Restore default addresses） | — | 恢复两个默认地址。 |

默认的私有地址范围为 `10.0.0.0/8`、`172.16.0.0/12`、`192.168.0.0/16`、`169.254.0.0/16`、`224.0.0.0/4`、`fc00::/7`、`fe80::/10` 和 `ff00::/8`。如果某条路由规则将某个私有地址范围中的地址发往 `direct` 以外的出站，或拒绝这些地址，Throne 会把整个私有地址范围保留在 VPN 接口之内，以便该规则生效。

严格路由（strict route）在 Android 应用中始终开启，没有对应的设置项。在桌面版上它是一个设置项。参见 [TUN 模式](@/guides/tun_mode.zh.md#settings)。

在 2.0.0 和 2.0.1 中，`TUN 实现`（TUN implementation）设置用于选择 VPN 接口的网络协议栈：`gVisor`（默认）、`System` 或 `Mixed`。自 2.0.2 起，该设置已移除，Throne 使用核心的默认协议栈。参见 [Android 上的 TUN 模式](@/guides/tun_mode.zh.md#android)。

### MTU {#mtu}

Android 版的默认 MTU 为 `9000`，桌面版的默认值为 `1500`。除非遇到问题，否则请保留默认值。如果某些应用或网站无法访问而其他的正常，请将 `MTU` 设为 `1500` 并重新连接。当你恢复桌面版备份中的设置时，桌面版的 MTU 也会随之恢复。除非你在桌面版中更改过，否则该值为 `1500`。

## 入站设置 {#inbound-settings}

`设置`（Settings）→ `入站`（Inbound）控制 Throne 的本地代理，即混合入站（mixed inbound）。它在一个端口上同时接受 SOCKS5 和 HTTP。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `禁用混合入站`（Disable mixed inbound） | 关闭 | 在 VPN 模式下，Throne 不开启本地代理。这也会移除 [VPN 的 HTTP 代理](#http-proxy)和局域网共享。在 `仅代理` 模式下不起作用。 |
| `代理端口`（Proxy port） | `2080` | 本地代理的端口。在工作资料或其他 Android 用户中，默认值为 2080 加上该用户的编号。 |
| `随机端口`（Random port） | 关闭 | 每次启动连接时选择一个空闲端口，并将其保存为 `代理端口`。 |
| `允许来自局域网的连接`（Allow connections from the LAN） | 关闭 | 在所有网络接口上监听，而不仅是 `127.0.0.1`。参见[局域网共享](#lan-sharing)。 |
| `为 VPN 附加 HTTP 代理`（Append HTTP proxy to VPN） | 关闭 | 自 2.0.2 起。将本地代理设为 [VPN 的 HTTP 代理](#http-proxy)。开启 `启用授权` 时不可用。 |
| `HTTP 代理绕过列表`（HTTP proxy bypass list） | 空 | 不使用 VPN 的 HTTP 代理的域名。每行一个条目。 |
| `启用授权`（Enable authorization） | 关闭 | 客户端必须提供 `用户名`（Username）和 `密码`（Password）。这适用于所有客户端。 |
| `DNS 服务器端口`（DNS server port） | `5533` | 自 2.0.2 起。Throne 本地 DNS 服务器在 `127.0.0.1` 上使用的端口。如果另一个应用或另一个 Android 用户已经在使用该端口，请选择其他端口。参见 [Android 上的 DNS](@/guides/dns.zh.md#android)。 |
| `自定义入站`（Custom inbound） | `{"inbounds": []}` | 以 JSON 形式提供的额外 sing-box 入站。Throne 会将 `inbounds` 数组原样添加到其配置中。 |

开启 `禁用混合入站` 时，本界面上除 `DNS 服务器端口` 和 `自定义入站` 以外的所有其他设置都会被禁用。

桌面版也有类似的设置。参见[入站设置](@/guides/proxy_modes.zh.md#inbound-settings)。
