+++
title = "DNS"
description = "Throne 如何解析域名、各项 DNS 设置的作用、FakeIP 何时有用，以及如何解读 DNS 泄漏测试。"
weight = 50
toc = true
+++

应用在打开网站之前，需要先通过 DNS 查询把网站的名称转换为 IP 地址。本页说明 Throne 在哪种查询中使用哪个 DNS 服务器、`DNS` 标签页上各项设置的作用，以及如何解读 DNS 泄漏测试。默认设置适用于大多数人。

## Throne 中的 DNS 工作方式 {#how-dns-works}

Throne 在核心中内置了自己的 DNS 解析器。它使用三个 DNS 服务器：

| 服务器 | 查询的传输方式 | 用途 |
| --- | --- | --- |
| `远程 DNS`（Remote DNS） | 经过代理 | 应答应用的 DNS 查询，除非下文的某条规则将查询发往别处。 |
| `直连 DNS`（Direct DNS） | 直接发送，不经过代理 | 当路由配置档将某个域名发往 `direct` 时，由它应答对该域名的查询。Throne 还用它查询代理服务器的地址，以及在按名称直接连接某个网站时查询该网站的地址。 |
| `本地覆盖`（Local Override） | 直接发送 | 查询上面两个服务器的名称，例如 `dns.google`。留空表示使用系统的解析器。 |

当应用的查询到达 Throne 时，Throne 按以下顺序应答：

1. `预定义回答`（Predefined Answers）和 `尊重 Hosts 文件`（Respect Hosts File）最先应答。
2. 开启 `启用 FakeIP`（Enable FakeIP）后，地址查询会得到一个虚假地址（参见 [FakeIP](#fakeip)）。
3. 开启 `启用 DNS 路由`（Enable DNS Routing，默认开启）后，路由配置档设为 `direct` 的域名会交给直连 DNS。这适用于 `domain:`、`suffix:`、`keyword:` 和 `regex:` 规则以及内置的 `geosite-*` 规则集。它不适用于 `geoip-*` 规则集、IP 规则或你自己的 `.srs` 文件。
4. 其余查询都交给 `默认 DNS 服务器`（Default DNS server），其默认值为 `remote`。如果你将其设为 `direct`，路由配置档发往 `proxy` 的域名仍会使用远程 DNS，条件与第 3 步相同。

查询的来源取决于所用的模式：

- **TUN 模式。** 应用的 DNS 查询会进入隧道并到达 Throne。Throne 会为每个结构化路由配置档添加一个 DNS 步骤（列表中的 `Route DNS` 或 `dns-hijack` 规则起同样的作用），因此它会按上述方式应答这些查询。原始路由配置档只有在你自行编写这一步骤时才会包含它；参见[原始配置档](@/guides/routing.zh.md#raw-profiles)。在 macOS 上，自 1.4.0 起，TUN 模式运行期间，核心会把 TUN 网卡作为一个独立的网络服务添加到 macOS，其 DNS 服务器是隧道内的一个地址。它不会更改你的网络服务（例如 `Wi-Fi`）的 DNS 服务器。参见 [TUN 模式](@/guides/tun_mode.zh.md#side-effects-dns)。
- **系统代理模式。** 使用代理的应用会把网站名称发送给 Throne。对于经过代理的连接，由代理服务器查询该名称。对于直连的连接，由 Throne 使用直连 DNS 查询。应用也可以自行通过系统的解析器查询名称，这些查询不会经过 Throne。

## 设置 {#settings}

打开 `设置`（Settings）→ `路由设置`（Routing Settings）→ `DNS`。点击 `确定`（OK）并重启连接后，更改才会生效。活动的路由配置档可以用自己的设置替换其中一部分；参见[按路由配置档设置 DNS](#route-dns)。

在简洁模式下没有 `DNS` 标签页：`远程 DNS`（Remote DNS）、`直连 DNS`（Direct DNS）和 `默认 DNS 服务器`（Default DNS server）位于 `通用`（Common）标签页的 `简易 DNS 设置`（Simple DNS Settings）分区中，其他设置会被隐藏，但保留其值。如果开启了 `使用自定义 DNS 对象`（Use Custom DNS Object），`DNS` 标签页会保留，以便你将其关闭。参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `远程 DNS`（Remote DNS） | `https://8.8.8.8/dns-query` | 经过代理使用的服务器。列表中还提供 `tls://8.8.8.8`、`tls://1.1.1.1`、`8.8.8.8` 和 `1.1.1.1`。 |
| `直连 DNS`（Direct DNS） | `localhost` | 不经过代理使用的服务器。`localhost` 表示系统的解析器。除非它无法正常工作，否则请保留此设置；如果无法工作，请选择一个在你的网络中无需代理即可使用的服务器。列表中提供了中国（`223.5.5.5`、`119.29.29.29`）、伊朗（`178.22.122.100`）和俄罗斯（`77.88.8.8`）的公共解析器。 |
| `禁用 IPv6`（Disable IPv6，位于每个服务器旁边） | 关闭 | 对发往该服务器的 IPv6（AAAA）查询返回空结果。 |
| `本地覆盖`（Local Override） | 空 | 用于查询 DNS 服务器名称的 DNS 服务器。当系统的解析器无法使用时，请在此处填写一个 IP 地址。 |
| `默认 DNS 服务器`（Default DNS server） | `remote` | 用于其他规则都不处理的查询的服务器：`remote` 或 `direct`。 |
| `启用 DNS 路由`（Enable DNS Routing） | 开启 | 将对设为 `direct` 的域名的查询发送到直连 DNS。 |
| `尊重 Hosts 文件`（Respect Hosts File） | 关闭 | 优先使用系统 hosts 文件应答 A 和 AAAA 查询。只影响文件中列出的域名。 |
| `预定义回答`（Predefined Answers） | 开启 | 采用 hosts 文件语法的固定应答。默认条目为 `127.0.0.1 localhost`。 |
| `启用 FakeIP`（Enable FakeIP） | 关闭 | 参见 [FakeIP](#fakeip)。 |
| `FakeIP 禁用 IPv6`（FakeIP Disable IPv6） | 关闭 | 不分配虚假的 IPv6 地址；AAAA 查询会得到空应答。 |

自 1.4.0 起，`远程 DNS`（Remote DNS）和 `直连 DNS`（Direct DNS）的列表还会在内置示例之上列出你最近输入过的 8 个服务器，最新的排在最前面。

`远程 DNS`、`直连 DNS` 和 `本地覆盖` 接受以下格式：

| 格式 | 示例 |
| --- | --- |
| 普通 DNS | `8.8.8.8` 或 `8.8.8.8:53` |
| DNS over TCP | `tcp://8.8.8.8:53` |
| DNS over TLS | `tls://1.1.1.1` |
| DNS over HTTPS | `https://1.1.1.1/dns-query` |
| DNS over HTTP/3 | `h3://dns.example.com/dns-query` |
| DNS over QUIC | `quic://dns.example.com` |
| 你的网络分配的服务器 | `dhcp://auto` |
| 系统的解析器（用于 `直连 DNS`） | `localhost` |

当正在运行的配置档使用 Xray，且 `远程 DNS`（Remote DNS）为普通 DNS 或 DNS over QUIC 时，Throne 会改用 DNS over HTTPS。例如，`1.1.1.1` 会变为 `https://1.1.1.1/dns-query`；Throne 不认识的服务器会变为 `https://8.8.8.8/dns-query`。

`预定义回答`（Predefined Answers）会打开一个编辑器，每行一个条目：

```text
127.0.0.1 localhost
10.0.0.5 nas.lan files.lan
```

如果某个域名在此列表中只有 IPv4 地址，那么它的 IPv6 查询会得到“域名不存在”的应答，反之亦然。这样，应用就无法绕过你设置的条目。

## FakeIP {#fakeip}

开启 `启用 FakeIP`（Enable FakeIP）后，Throne 会立即用 `198.18.0.0/15`（IPv6：`2001:db8::/32`）中的虚假地址应答地址查询，而不询问真实的 DNS 服务器。当应用连接到该地址时，Throne 知道它代表哪个域名，并按名称对连接进行路由。真正的查询稍后才会进行：经过代理的连接在代理服务器上查询，直连的连接则使用直连 DNS 查询。

这样做有两个好处：

- 连接建立得更快，因为应用无需等待真实的 DNS 应答。
- 在连接完成路由之前，不会有任何 DNS 查询离开你的设备。

FakeIP 只影响到达 Throne 的查询，因此它在 TUN 模式下才有用。在系统代理模式下，它对应用没有任何影响。

注意事项：

- 来自 `预定义回答` 和 hosts 文件的应答仍是真实的。
- 应用会在自己的缓存中保留虚假地址。停止 Throne 后，仍持有虚假地址的应用在重新查询该名称之前将无法连接。如果应用一直处于离线状态，请重启该应用。
- `高级设置`（Advanced Settings）→ `保存缓存到文件`（Save Cache To File）可在 Throne 重启后保留这些虚假地址。
- 在 1.4.0 之前，虚假的 IPv6 地址来自 `fc00::/18`。在 TUN 模式下，`私有地址范围绕过`（Private Range Bypass）会让它们绕过隧道，因此优先尝试 IPv6 的应用无法连接。自 1.4.0 起，它们来自 `2001:db8::/32`，这个地址范围在互联网上从不使用。

## 高级设置 {#advanced}

`DNS` 标签页上的 `高级设置`（Advanced Settings）用于控制 DNS 缓存：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `缓存容量`（Cache Capacity） | `65536` | 缓存最多保留多少条应答。 |
| `查询超时`（Query Timeout） | `10s` | 等待 DNS 服务器的时长。 |
| `乐观缓存`（Optimistic Cache） | 关闭 | 在后台刷新已过期应答的同时，继续提供该应答。不能与 `禁用缓存`（Disable Cache）或 `禁用有效期`（Disable Expire）同时使用。 |
| `乐观超时`（Optimistic Timeout） | `3d` | 已过期的应答最多还能提供多长时间。 |
| `禁用缓存`（Disable Cache） | 关闭 | 关闭 DNS 缓存。 |
| `禁用有效期`（Disable Expire） | 关闭 | 缓存的应答过期后仍然保留。 |
| `保存缓存到文件`（Save Cache To File） | 关闭 | 将缓存的应答和 FakeIP 地址写入核心的缓存文件，使其在重启后仍然保留。每个条目都会产生一次磁盘写入。 |
| `反向映射`（Reverse Mapping） | 关闭 | 记住已应答的 IP 地址属于哪个域名，使域名规则能够匹配到该地址的连接。 |

时长由一个数字加上 `ns`、`us`、`ms`、`s`、`m`、`h` 或 `d` 组成，例如 `5s` 或 `3d`。

`使用自定义 DNS 对象`（Use Custom DNS Object）会用一个完整的 sing-box `dns` 对象替换此标签页上的所有设置，该对象由你通过 `编辑 DNS 对象`（Edit DNS Object）编写。开启后，简单设置和 `高级设置`（Advanced Settings）会变为灰色，DNS 路由、FakeIP 和预定义回答等功能只有在你的对象中配置了才会生效。在编辑器中，`格式化`（Format）用于整理 JSON，`文档`（Document）会显示 [sing-box DNS 文档](https://sing-box.sagernet.org/configuration/dns/)的地址。

{% alert_warning() %}
请在自定义 DNS 对象中保留一个标签（tag）为 `dns-direct` 的服务器。Throne 的路由会用它来查询服务器地址。
{% end %}

## 按路由配置档设置 DNS {#route-dns}

自 1.4.0 起，路由配置档可以使用自己的 DNS 服务器。当该配置档是活动的路由配置档时，这些服务器会取代 `DNS` 标签页上的全局服务器。当某个国家的配置档需要一个在当地可用的直连 DNS，或者服务商把路由配置档和它的 DNS 服务器一起提供时，这很有用。

1. 打开 `设置`（Settings）→ `路由设置`（Routing Settings）→ `路由`（Route），选中配置档，然后点击 `编辑`（Edit）。也可以在 `路由`（Routing）菜单底部右键点击该配置档。
2. 打开 `DNS` 标签页，选择 `Use custom DNS settings`。
3. 修改设置，然后点击 `确定`（OK）。

该标签页包含 [DNS 标签页](#settings)中的以下设置，显示为英文：`Remote DNS`、`Direct DNS`、它们各自的 `Disable IPv6` 复选框、`Default DNS server`、`Enable DNS Routing`、`Enable FakeIP` 和 `FakeIP Disable IPv6`。第一次切换到 `Use custom DNS settings` 时，这些设置会以全局值为初始值。默认选项 `Use the global DNS settings` 会以灰色显示全局值。`本地覆盖`（Local Override）、`尊重 Hosts 文件`（Respect Hosts File）、`预定义回答`（Predefined Answers）和 `高级设置`（Advanced Settings）始终取自 `DNS` 标签页。

注意事项：

- 在 `路由`（Routing）菜单中选择另一个路由配置档会重启连接，因此 DNS 服务器也会随之切换。
- 自 1.4.0 起，分组可以使用自己的路由配置档（参见[按分组设置路由配置档](@/guides/routing.zh.md#group-routing)）。此时，该分组的配置档运行期间，生效的是该路由配置档的 DNS 设置。测试始终使用在 `路由`（Routing）菜单中选择的路由配置档的 DNS 设置，因为测试可能涉及多个分组的配置档。
- 由[订阅提供](@/guides/routing.zh.md#subscription-routing)的路由配置档从不从订阅获取 DNS 设置。如果需要，请在它的 `DNS` 标签页上为它设置自己的 DNS；订阅更新时会保留这些设置。
- 带有自己 DNS 设置的配置档也会取代[自定义 DNS 对象](#advanced)：该配置档处于活动状态时，Throne 会忽略 `使用自定义 DNS 对象`（Use Custom DNS Object）。当某个配置档使用全局设置且开启了 `使用自定义 DNS 对象` 时，该配置档的 `DNS` 标签页会提示该对象生效。
- 结构化、远程和原始路由配置档都有该标签页。远程配置档在更新时会保留自己的 DNS 设置；Throne 从不从 URL 获取这些设置。
- 配置档的 `throne://route/` 链接会携带其 DNS 设置。在 `路由设置`（Routing Settings）中用 `导入`（Import）导入此类链接之前，Throne 会列出这些服务器：“It uses its own DNS servers: remote …, direct …”（它使用自己的 DNS 服务器：远程 …，直连 …）。请核对它们：该配置档处于活动状态时，你查询的每个名称都会经过这些服务器。参见[分享配置档](@/guides/routing.zh.md#share-profiles)。
- 在简洁模式下，只有已经使用自己 DNS 设置的配置档才会显示 `DNS` 标签页。

## 供其他设备使用的 DNS 服务器 {#dns-server}

配置档运行时，Throne 的核心还会运行一个 DNS 服务器。它使用本页上的 DNS 设置（包括路由配置档的 DNS 设置）进行应答。其端口为 `设置`（Settings）→ `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）→ `Sing-box` → `DNS 服务器端口`（DNS Server Port），默认 `5533`，同时用于 TCP 和 UDP（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。默认情况下它只监听 `127.0.0.1`，因此只有这台电脑上的程序可以使用它。

自 1.4.0 起，你网络中的其他设备也可以使用它，例如让 DNS 查询经过这台电脑的路由器：

1. 勾选 `DNS 服务器端口`（DNS Server Port）下方的 `允许其他设备连接`（Allow other devices to connect）（默认关闭），然后点击 `确定`（OK）。
2. 如果有配置档正在运行，请点击“设置已更改，重启进行应用”通知中的 `重启`（Restart）。之后 DNS 服务器会监听所有网络接口。
3. 在防火墙中允许到该端口的 TCP 和 UDP 入站连接，方法与[局域网共享](@/guides/proxy_modes.zh.md#lan-sharing)相同。例如在使用 ufw 的 Linux 上：`sudo ufw allow 5533`。
4. 在另一台设备上，把 DNS 服务器设为你电脑的局域网地址和该端口，例如 `192.168.1.20` 和 `5533`。

注意事项：

- 许多手机和电脑的 DNS 设置只接受地址，并且始终使用 53 端口。此设置适用于也允许输入端口的设备，例如许多路由器。
- 任何能访问该端口的人都可以通过你的 DNS 设置查询名称，也就是经过你的代理。请只在你信任的网络中使用它。
- 该服务器只应答 DNS 查询，到该端口的其他连接都会被拒绝。
- 开启 `启用 FakeIP`（Enable FakeIP）后，其他设备也会得到虚假地址。这些地址只对经过 Throne 的连接有效。
- 此设置与 `程序`（Program）→ `允许其他设备连接`（Allow other devices to connect）是分开的，后者共享的是代理端口。
- 使用 [thronectl](@/advanced/api.zh.md#settings) 时（例如在[无界面 Throne](@/headless/_index.zh.md) 上），该设置为 `dns_in_lan`，端口为 `dns_in_port`。

## DNS 泄漏测试 {#leak-tests}

DNS 泄漏测试是一类网站，它会显示是哪些 DNS 服务器查询了它的测试域名。测试结果取决于你使用的模式和路由配置档。

以下结果属于正常情况：

- **你的远程 DNS 服务商**（默认设置下为 Google）。你的查询经过了远程 DNS 和代理。对于你走代理的网站，这是正常结果。如果活动的路由配置档使用了[自己的 DNS 设置](#route-dns)，你看到的会是该配置档的服务器。
- **你的运营商（针对直连的网站）。** Throne 使用直连 DNS 查询直连的域名，而直连 DNS 默认是系统的解析器，通常属于你的运营商。在系统代理模式下或开启 FakeIP 时，对于仅因路由配置档的默认出站为 `direct` 才直连的网站（例如“Proxy … Blocked”配置档中的情况），也会出现这种结果。直连流量本来就不经过代理直接到达网站，所以你的运营商无论如何都能看到它。
- **你的运营商（系统代理模式下）。** 浏览器和应用可以绕过 Throne 自行查询名称。如果你在意这一点，请使用 TUN 模式。
- **浏览器自己的 DNS 服务商**，当浏览器使用安全 DNS（DNS over HTTPS）时。这些查询是普通的 HTTPS 连接，会遵循你的路由规则。
- **大多数名称显示为你的运营商**，当 `默认 DNS 服务器`（Default DNS server）设为 `direct` 时。

真正有问题的情况是这样的：你使用 TUN 模式和一个让测试网站走代理的路由配置档（例如 `Default`），但测试结果仍然显示你的运营商。这说明有部分查询绕过了隧道。在 Windows 上，请检查 `设置`（Settings）→ `Tun 设置`（Tun Settings）→ `严格路由`（Strict Route）是否已开启（在 Windows 10 及更高版本上默认开启），并按照 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route)中的说明操作。

WebRTC 测试检查的是另一件事：你的 IP 地址，而不是 DNS。WebRTC 测试未显示任何公网 IP 地址是正常的（[#1267](https://github.com/throneproj/Throne/issues/1267)）。如果对于你走代理的网站，它显示了你真实的公网 IP 地址，说明 UDP 流量绕过了代理。这在系统代理模式下很常见；请改用 TUN 模式。

## 劫持与系统 DNS（已移除） {#deprecated}

在 1.3.2 及更早版本中，`路由设置`（Routing Settings）窗口有一个 `劫持`（Hijack）标签页，Windows 上还有一个 `系统 DNS`（System DNS）选项。劫持功能会运行一个本地 DNS 服务器，用固定地址应答选定的域名，而 `系统 DNS` 会让 Windows 使用该服务器。两者都是为忽略系统代理的应用而设的。

自 1.4.0 起，两者都已移除，Throne 会忽略它们的旧设置。对这类应用请改用 `Tun 模式`（Tun Mode）；参见 [TUN 模式](@/guides/tun_mode.zh.md#enable)。

## Android 版 {#android}

Throne for Android 在 `设置`（Settings）→ `DNS` 中提供相同的 DNS 设置。FakeIP 在那里称为 `启用 FakeDNS`（Enable FakeDNS）。在 VPN 模式下，Throne 始终会处理 VPN 内的 DNS 查询。不同之处如下：

- **`localhost` 作为直连 DNS。** 自 2.0.1 起，它会像在 Windows 上一样，直接询问当前 Wi-Fi 或移动网络的 DNS 服务器。只有当网络没有指定 DNS 服务器时，才会使用 Android 自身的解析器。此前，Throne 始终使用 Android 的解析器，而该解析器还会应用私人 DNS（Private DNS），可能使每次查询都卡住。如果连接时网络的 DNS 服务器没有应答，Throne 会提示“无法连接：网络的 DNS (…) 未应答。请设置可用的直连 DNS。”（Could not connect: the network's DNS (…) did not answer. Set a working Direct DNS.）。提示中的 `DNS` 按钮会打开 DNS 设置。
- **虚假的 IPv6 地址**自 2.0.2 起来自 `2001:db8::/32`，与桌面版相同。
- **按路由配置档设置 DNS**（自 2.0.4 起）。与桌面版一样，路由配置档的编辑器中有一个 `DNS` 卡片，提供 `使用全局 DNS 设置`（Use the global DNS settings）和 `使用自定义 DNS 设置`（Use custom DNS settings）（参见[按路由配置档设置 DNS](#route-dns)）。当活动的路由配置档使用自己的服务器时，`设置`（Settings）→ `DNS` 页面顶部会说明这一点：此时那里的设置只对其他路由配置档生效。
- **供其他应用使用的本地 DNS 服务器。** Throne 在 `127.0.0.1` 上运行一个 DNS 服务器，设备上的其他应用都可以使用它。自 2.0.2 起，你可以在 `设置`（Settings）→ `入站`（Inbound）→ `DNS 服务器端口`（DNS server port）中更改其端口（默认 `5533`）。
