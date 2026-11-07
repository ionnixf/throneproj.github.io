+++
title = "VPN 与代理模式"
description = "在 VPN 模式和仅代理模式之间做出选择，指定哪些应用使用 Throne，与其他设备共享代理，并调整 TUN 和入站设置。"
weight = 30
toc = true
+++

## 选择运行模式 {#service-mode}

在 `设置`（Settings）→ `通用`（General）→ `运行模式`（Service mode）中设置模式。更改模式会停止正在运行的连接；再次点按连接按钮即可重新启动。

| 模式 | 作用 | 适用场景 |
| --- | --- | --- |
| `VPN`（默认） | Android 通过虚拟网络接口（TUN）把所有应用的流量，或仅把你所选应用的流量，发送给 Throne。 | 你希望所有应用都使用 Throne。这是通常的选择。 |
| `仅代理`（Proxy only） | Throne 只在 `127.0.0.1` 上开启一个本地 SOCKS5 和 HTTP 代理，默认端口为 `2080`。不会创建 VPN。 | 另一个 VPN 应用必须保持连接；你只需要在带有代理设置的应用中使用 Throne；或者你的设备无法授予 VPN 权限。 |

如果在 Throne 已连接时更改本页上的设置，请在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply），或者重新连接。

## VPN 模式 {#vpn-mode}

在 VPN 模式下，Throne 按照路由配置的规定，将应用的每个连接经由代理发送、直接发送或阻止。

- Android 同一时间只运行一个 VPN。Throne 连接后，其他任何应用的 VPN 都会断开。
- 连接到手机热点的设备不会使用该 VPN。要让它们使用代理，请使用[局域网共享](#lan-sharing)。
- 要让某些应用不走 VPN，或只让某些应用走 VPN，请使用[分应用代理](#per-app-proxy)。
- 如果 Throne 被设为始终开启的 VPN，Android 会以 VPN 模式启动它。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。

### VPN 的 HTTP 代理 {#http-proxy}

在 Android 10 及更高版本上，`设置`（Settings）→ `入站`（Inbound）→ `为 VPN 附加 HTTP 代理`（Append HTTP proxy to VPN）（默认关闭）还会将 Throne 的本地代理设为 VPN 网络的 HTTP 代理。遵循系统代理设置的应用（例如大多数浏览器）随后会将其网页流量发送到该代理，而不是经由 VPN 接口。这部分流量仍然遵循你的路由配置。

开启 `启用授权`（Enable authorization）时，该设置不可用，因为 Android 无法把用户名和密码传给应用。`HTTP 代理绕过列表`（HTTP proxy bypass list）中的域名（每行一个，例如 `*example.com`）不经过该代理连接。

### 绕过 VPN 的应用 {#allow-bypass}

`设置`（Settings）→ `通用`（General）→ `允许应用绕过 VPN`（Allow apps to bypass VPN）（默认关闭）允许请求绕过的应用不经过 VPN 连接，例如 Google 推送通知（FCM）。这些连接会直接发出，不遵循你的路由配置。当始终开启的 VPN 屏蔽未使用 VPN 的连接时，Android 会忽略该设置。

## 仅代理模式 {#proxy-only}

在 `仅代理`（Proxy only）模式下，Throne 只运行一个本地代理，不做其他事情。Android 不会请求 VPN 权限，只有你设置为使用该代理的应用才会使用 Throne。TUN 设置和分应用代理不起作用。

1. 将 `设置`（Settings）→ `通用`（General）→ `运行模式`（Service mode）设为 `仅代理`，然后连接。
2. 在需要使用 Throne 的应用中设置 SOCKS5 或 HTTP 代理，服务器为 `127.0.0.1`，端口为 `2080`（或你设置的 `代理端口`（Proxy port））。如果开启了 `启用授权`（Enable authorization），还需要输入用户名和密码。

在此模式下，Throne 自己的请求（例如订阅更新）在有配置运行时经由本地代理发送，没有配置运行时直接连接，除非开启了 `设置` → `订阅`（Subscriptions）→ `使用代理`（Use proxy）。

## 分应用代理 {#per-app-proxy}

分应用代理决定哪些应用使用 VPN。它只在 VPN 模式下有效。

1. 开启 `设置`（Settings）→ `TUN / VPN` → `分应用代理`（Apps VPN mode），应用列表随即打开。
2. 在列表顶部选择模式：`代理`（Proxy）只让所选应用走 VPN，`绕过`（Bypass）让所选应用不走 VPN。
3. 点按应用以选中它们。`搜索…`（Search…）可按名称或包名查找应用。共享同一用户 ID 的应用会被一起选中。

如果没有选择任何应用，所有应用都会使用 VPN。在 `代理` 模式下，如果所选应用都未安装，则没有任何应用使用 VPN。要关闭分应用代理，请关闭 `分应用代理` 开关，或在列表中选择 `关闭`（Off）；Throne 会保留你的选择，供下次使用。如果列表一直为空，请参见[已安装应用](@/android/permissions.zh.md#installed-apps)。

`自动选择需要代理的应用`（Auto select proxy apps）会从一个包含约 390 个常需要代理的应用（例如 Google 应用、YouTube、Instagram 和 Netflix）的内置列表中选择应用，使这些应用使用 VPN，其他应用则不使用。它会替换你对已安装应用的选择。

`添加包名…`（Add package name…）可添加列表无法显示的应用。`导出到剪切板`（Export to clipboard）和 `从剪切板导入`（Import from clipboard）以纯文本形式复制模式和选择：第一行为 `true` 表示 `绕过`，为 `false` 表示 `代理`，之后每行一个包名。

```text
false
com.google.android.youtube
org.telegram.messenger
```

如果没有 `true` 或 `false` 这一行，导入时会保留当前模式，只替换所选应用。只要有一行不是包名，Throne 就会拒绝整个列表。

要把某个应用发送到特定出站，请改用带有 `应用`（Apps）条件的路由规则。参见 [Android 上的路由](@/guides/routing.zh.md#android)。这样的规则只能看到使用 VPN 的应用：如果分应用代理让某个应用不走 VPN，它的规则就永远不会匹配。在路由规则的应用列表中，⋮ → `复制到分应用代理`（Copy to Apps VPN mode）会把这些应用添加到分应用代理，并在更改任何内容之前先说明各个选项。

## 局域网共享 {#lan-sharing}

局域网共享可以让你网络中或你手机热点上的其他设备把手机上的 Throne 用作代理。它在两种运行模式下都有效。

1. 打开 `设置`（Settings）→ `入站`（Inbound），开启 `允许来自局域网的连接`（Allow connections from the LAN）。
2. 开启 `启用授权`（Enable authorization），并设置 `用户名`（Username）和 `密码`（Password）。
3. 记下 `代理端口`（Proxy port），默认是 `2080`。
4. 在 Android Wi-Fi 设置中所连接网络的详细信息里找到手机的 IP 地址。连接到你热点的设备使用它们在连接信息中显示的路由器（网关）地址。
5. 在另一台设备上设置 SOCKS5 或 HTTP 代理，填写该地址、端口、用户名和密码。

{% alert_warning() %}
如果不启用认证，同一网络中的任何设备都可以使用你的代理。
{% end %}

## TUN / VPN 设置 {#tun-settings}

这些设置位于 `设置`（Settings）→ `TUN / VPN`，只在 VPN 模式下生效。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `MTU` | `9000` | VPN 接口的最大数据包大小。如果某些应用或网站无法访问而其他的正常，请设为 `1500` 并重新连接。长按该设置项可以输入 1000 到 10000 之间的任意值。 |
| `Tun IPv6` | 关闭 | 通过 VPN 接口路由 IPv6。关闭时，IPv6 连接会立即失败，因此应用会使用 IPv4。仅当你的服务器支持 IPv6 时才开启。 |
| `Tun 路由`（Tun routing） | 关闭 | 将路由配置发往 `direct` 的 IP 范围排除在 VPN 接口之外。 |
| `绕过私有网段`（Bypass private ranges） | 开启 | 将 `私有网段`（Private ranges），即本地网络，排除在 VPN 接口之外，以便直接访问本地网络中的设备。 |
| `IPv4 CIDR`、`IPv6 CIDR` | `172.19.0.1/24`、`fdfe:dcba:9876::1/96` | VPN 接口的地址。 |

如果某条路由规则将私有网段中的地址发往 `direct` 以外的出站，或拒绝这些地址，Throne 会把该网段保留在 VPN 接口之内，以便该规则生效。严格路由（strict route）始终开启。

恢复桌面版备份中的设置时，桌面版的 MTU 也会随之恢复；除非你更改过，否则该值为 `1500`。

## 入站设置 {#inbound-settings}

`设置`（Settings）→ `入站`（Inbound）控制 Throne 的本地代理，即混合入站（mixed inbound），它在一个端口上同时接受 SOCKS5 和 HTTP。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `禁用混合入站`（Disable mixed inbound） | 关闭 | 在 VPN 模式下，Throne 不开启本地代理。这也会移除 [VPN 的 HTTP 代理](#http-proxy)和局域网共享。在 `仅代理` 模式下不起作用。 |
| `代理端口`（Proxy port） | `2080` | 本地代理的端口。在工作资料或其他 Android 用户中，默认值为 2080 加上该用户的编号。 |
| `启用授权`（Enable authorization） | 关闭 | 每个客户端都必须提供 `用户名`（Username）和 `密码`（Password）。 |
| `DNS 服务器端口`（DNS server port） | `5533` | Throne 本地 DNS 服务器在 `127.0.0.1` 上使用的端口。如果另一个应用或另一个 Android 用户已经在使用该端口，请选择其他端口。参见 [Android 上的 DNS](@/guides/dns.zh.md#android)。 |
| `自定义入站`（Custom inbound） | `{"inbounds": []}` | 以 JSON 形式提供的额外 sing-box 入站，会原样添加到配置中。 |
