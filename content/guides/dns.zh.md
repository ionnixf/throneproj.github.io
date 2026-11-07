+++
title = "DNS"
description = "Throne 使用哪些 DNS 服务器、各项 DNS 设置的作用，以及如何解读 DNS 泄漏测试。"
weight = 50
toc = true
+++

Throne 用自己的解析器应答 DNS 查询。默认设置适用于大多数人。

## Throne 中的 DNS 工作方式 {#how-dns-works}

Throne 使用三个 DNS 服务器：

| 服务器 | 用途 |
| --- | --- |
| `远程 DNS`（Remote DNS） | 应用的查询，经过代理发送。 |
| `直连 DNS`（Direct DNS） | 路由设为 `direct` 的域名，以及代理服务器的地址。不经过代理发送。 |
| `本地覆盖`（Local Override） | 查询上面两个服务器的名称，例如 `dns.google`。留空表示使用系统的解析器。 |

Throne 按以下顺序应答查询：

1. `预定义回答`（Predefined Answers）和 `尊重 Hosts 文件`（Respect Hosts File）。
2. 开启 `启用 FakeIP`（Enable FakeIP）后，返回虚假地址（参见 [FakeIP](#fakeip)）。
3. 开启 `启用 DNS 路由`（Enable DNS Routing，默认开启）后，路由配置档设为 `direct` 的域名交给 `直连 DNS`。这适用于域名规则和 `geosite-*` 规则集，不适用于 IP 规则、`geoip-*` 规则集或你自己的 `.srs` 文件。
4. 其余查询交给 `默认 DNS 服务器`（Default DNS server），默认为 `remote`。如果将其设为 `direct`，此类规则发往 `proxy` 的域名仍使用 `远程 DNS`。

哪些查询会到达 Throne 取决于模式：

- **TUN 模式：** 所有应用的 DNS 查询。结构化路由配置档会自动处理它们；[原始配置档](@/guides/routing.zh.md#raw-profiles)需要一条 `hijack-dns` 规则。macOS 的情况参见 [TUN 模式](@/guides/tun_mode.zh.md#side-effects-dns)。
- **系统代理模式：** 只有应用发送给代理的网站名称；走代理的网站由代理服务器查询其名称。应用也可以绕过 Throne 自行查询名称。

## 设置 {#settings}

打开 `设置`（Settings）→ `路由设置`（Routing Settings）→ `DNS`。点击 `确定`（OK）并重启连接后，更改才会生效。活动的路由配置档可以替换其中一部分设置，参见[按路由配置档设置 DNS](#route-dns)。在简洁模式下，`远程 DNS`（Remote DNS）、`直连 DNS`（Direct DNS）和 `默认 DNS 服务器`（Default DNS server）位于 `通用`（Common）标签页的 `简易 DNS 设置`（Simple DNS Settings）分区中，其余设置被隐藏（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `远程 DNS`（Remote DNS） | `https://8.8.8.8/dns-query` | 经过代理使用的服务器。 |
| `直连 DNS`（Direct DNS） | `localhost` | 不经过代理使用的服务器。`localhost` 表示系统的解析器。如果它无法工作，请选择一个在你的网络中无需代理即可使用的服务器，例如列表中的公共解析器。 |
| `禁用 IPv6`（Disable IPv6，位于每个服务器旁边） | 关闭 | 对发往该服务器的 IPv6（AAAA）查询返回空结果。 |
| `本地覆盖`（Local Override） | 空 | 当无法用系统的解析器查询 DNS 服务器的名称时，请在此处填写一个 IP 地址。 |
| `尊重 Hosts 文件`（Respect Hosts File） | 关闭 | 优先使用系统 hosts 文件应答。 |
| `预定义回答`（Predefined Answers） | 开启 | 采用 hosts 文件语法的固定应答，例如 `10.0.0.5 nas.lan`。只列出 IPv4 地址的名称不会得到 IPv6 应答，反之亦然。 |

服务器地址可以是普通 DNS（`8.8.8.8` 或 `8.8.8.8:53`）、`tcp://8.8.8.8:53`、`tls://1.1.1.1`、`https://1.1.1.1/dns-query`、`h3://dns.example.com/dns-query`、`quic://dns.example.com`，或表示网络分配的服务器的 `dhcp://auto`。

当正在运行的配置档使用 Xray 时，普通 DNS 或 DNS over QUIC 形式的 `远程 DNS`（Remote DNS）会改用 DNS over HTTPS：`1.1.1.1` 变为 `https://1.1.1.1/dns-query`，Throne 不认识的服务器变为 `https://8.8.8.8/dns-query`。

## FakeIP {#fakeip}

开启 `启用 FakeIP`（Enable FakeIP）后，Throne 会立即用 `198.18.0.0/15`（IPv6：`2001:db8::/32`）中的虚假地址应答地址查询。当应用连接到该地址时，Throne 按域名对连接进行路由，真正的查询稍后才进行：在代理服务器上，或对直连的连接使用 `直连 DNS`。连接建立得更快，而且在连接完成路由之前，不会有任何 DNS 查询离开你的设备。

FakeIP 只影响到达 Throne 的查询，因此它在 TUN 模式下有用，在系统代理模式下无效。

- 应用会在自己的缓存中保留虚假地址。如果停止 Throne 后某个应用一直离线，请重启该应用。
- `FakeIP 禁用 IPv6`（FakeIP Disable IPv6）不分配虚假的 IPv6 地址；AAAA 查询会得到空应答。
- `高级设置`（Advanced Settings）→ `保存缓存到文件`（Save Cache To File）可在 Throne 重启后保留这些虚假地址。

## 高级设置 {#advanced}

`DNS` 标签页上的 `高级设置`（Advanced Settings）用于控制 DNS 缓存。其中不太直观的选项：

- `乐观缓存`（Optimistic Cache）在后台刷新已过期应答的同时继续提供该应答，最长为 `乐观超时`（Optimistic Timeout，默认 `3d`）。它不能与 `禁用缓存`（Disable Cache）或 `禁用有效期`（Disable Expire）同时使用。
- `保存缓存到文件`（Save Cache To File）在重启后保留缓存的应答和 FakeIP 地址，代价是每个新条目都会产生一次磁盘写入。
- `反向映射`（Reverse Mapping）记住每个 IP 地址是为哪个域名应答的，使域名规则能够匹配到该地址的连接。

`使用自定义 DNS 对象`（Use Custom DNS Object）会用一个完整的 sing-box `dns` 对象替换此标签页上的所有设置，该对象由你通过 `编辑 DNS 对象`（Edit DNS Object）编写（参见 [sing-box DNS 文档](https://sing-box.sagernet.org/configuration/dns/)）。此时 DNS 路由、FakeIP 和预定义回答只有在你的对象中配置了才会生效。

{% alert_warning() %}
请在自定义 DNS 对象中保留一个标签（tag）为 `dns-direct` 的服务器。Throne 会用它来查询服务器地址。
{% end %}

## 按路由配置档设置 DNS {#route-dns}

路由配置档可以使用自己的 DNS 服务器，在它作为活动路由配置档期间取代全局服务器，例如一个在该配置档所针对的国家可用的 `直连 DNS`。

1. 打开配置档：`设置`（Settings）→ `路由设置`（Routing Settings）→ `路由`（Route）→ `编辑`（Edit），或在 `路由`（Routing）菜单底部右键点击它。
2. 在它的 `DNS` 标签页上选择 `使用自定义 DNS 设置`（Use custom DNS settings），修改设置后点击 `确定`（OK）。

该标签页包含两个服务器及其 `禁用 IPv6`（Disable IPv6）复选框、`默认 DNS 服务器`（Default DNS server）、`启用 DNS 路由`（Enable DNS Routing）以及 FakeIP 设置。其余设置取自全局的 `DNS` 标签页；这样的配置档处于活动状态时，`使用自定义 DNS 对象`（Use Custom DNS Object）会被忽略。

- [分组自己的路由配置档](@/guides/routing.zh.md#group-routing)会在该分组的配置档运行期间带来它的 DNS 设置。
- 远程配置档和[订阅提供的路由配置档](@/guides/routing.zh.md#subscription-routing)在更新时会保留自己的 DNS 设置。Throne 从不从 URL 或订阅获取 DNS 设置。
- `throne://route/` 链接会携带 DNS 设置，`路由设置`（Routing Settings）中的 `导入`（Import）会在导入前列出这些服务器。请核对它们：该配置档处于活动状态时，你查询的每个名称都会经过这些服务器。
- 在简洁模式下，只有已经使用自己 DNS 设置的配置档才会显示 `DNS` 标签页。

## 供其他设备使用的 DNS 服务器 {#dns-server}

配置档运行时，Throne 还会运行一个 DNS 服务器，按本页的设置进行应答。它监听 `127.0.0.1` 的 TCP 和 UDP 端口 `5533`，端口在 `设置`（Settings）→ `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）→ `Sing-box` → `DNS 服务器端口`（DNS Server Port）中设置（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

要让网络中的其他设备（例如路由器）使用它：

1. 勾选 `DNS 服务器端口`（DNS Server Port）下方的 `允许其他设备连接`（Allow other devices to connect），点击 `确定`（OK），然后重启正在运行的配置档。
2. 在防火墙中允许到该端口的 TCP 和 UDP 入站连接，方法与[局域网共享](@/guides/proxy_modes.zh.md#lan-sharing)相同。在使用 ufw 的 Linux 上：`sudo ufw allow 5533`。
3. 在另一台设备上，把 DNS 服务器设为你电脑的局域网地址和该端口，例如 `192.168.1.20` 和 `5533`。

- 手机和电脑的 DNS 设置通常只接受 53 端口；许多路由器允许设置端口。
- 任何能访问该端口的人都可以经过你的代理查询名称。请只在你信任的网络中使用它。
- 开启 FakeIP 后，其他设备也会得到虚假地址，这些地址只对经过 Throne 的连接有效。
- `程序`（Program）→ `允许其他设备连接`（Allow other devices to connect）是另一个设置：它共享的是代理端口。
- 在 [thronectl](@/advanced/api.zh.md#settings) 和[无界面 Throne](@/headless/_index.zh.md) 中，这两个设置为 `dns_in_lan` 和 `dns_in_port`。

## DNS 泄漏测试 {#leak-tests}

DNS 泄漏测试会显示是哪些 DNS 服务器查询了它的测试域名。以下结果属于正常情况：

- **你的远程 DNS 服务商**（默认为 Google），或活动路由配置档[自己的](#route-dns)服务器。
- **你的运营商（针对直连的网站）。** 这些网站使用 `直连 DNS` 查询，默认即系统的解析器。在系统代理模式下或开启 FakeIP 时，这也包括仅因默认出站才直连的网站，例如“Proxy … Blocked”配置档中的情况。运营商无论如何都能看到直连流量。
- **你的运营商（系统代理模式下）。** 应用可以绕过 Throne 自行查询名称。如果你在意这一点，请使用 TUN 模式。
- **浏览器自己的 DNS 服务商**，当浏览器使用安全 DNS（DNS over HTTPS）时。
- **大多数名称显示为你的运营商**，当 `默认 DNS 服务器`（Default DNS server）设为 `direct` 时。

真正的泄漏是这样的：在 TUN 模式下，使用让测试网站走代理的路由配置档（例如 `Default`），测试结果仍然显示你的运营商。在 Windows 上，请检查 `设置`（Settings）→ `Tun 设置`（Tun Settings）→ `严格路由`（Strict Route）是否已开启（在 Windows 10 及更高版本上默认开启），并参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route)。

WebRTC 测试检查的是你的 IP 地址，而不是 DNS。未显示公网 IP 地址是正常的（[#1267](https://github.com/throneproj/Throne/issues/1267)）。如果对于你走代理的网站显示了你真实的 IP 地址，说明 UDP 绕过了代理，这在系统代理模式下很常见；请使用 TUN 模式。

## 劫持与系统 DNS（已移除） {#deprecated}

Throne 1.4.0 移除了 `路由设置`（Routing Settings）中的 `劫持`（Hijack）标签页和 Windows 上的 `系统 DNS`（System DNS）选项。对于忽略系统代理的应用，请改用 [TUN 模式](@/guides/tun_mode.zh.md#enable)。

## Android 版 {#android}

Throne for Android 在 `设置`（Settings）→ `DNS` 中提供相同的设置，其中 FakeIP 称为 `启用 FakeDNS`（Enable FakeDNS）。在 VPN 模式下，Throne 始终会处理 VPN 内的 DNS 查询。不同之处如下：

- **`localhost` 作为直连 DNS** 会直接询问当前 Wi-Fi 或移动网络的 DNS 服务器。如果它们没有应答，连接会失败并提示“无法连接：网络的 DNS (…) 未应答。请设置可用的直连 DNS。”（Could not connect: the network's DNS (…) did not answer. Set a working Direct DNS.）
- **按路由配置档设置 DNS** 位于路由配置档编辑器的 `DNS` 卡片中。当活动的路由配置档使用自己的服务器时，`设置`（Settings）→ `DNS` 页面顶部会说明这一点，此时那里的设置只对其他路由配置档生效。
- **供其他应用使用的本地 DNS 服务器** 运行在 `127.0.0.1` 上。其端口在 `设置`（Settings）→ `入站`（Inbound）→ `DNS 服务器端口`（DNS server port）中设置（默认 `5533`）。
