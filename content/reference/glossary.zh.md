+++
title = "术语表"
description = "对 Throne 中常见术语的简要解释，涵盖配置档、路由、TUN 模式、DNS、反审查选项以及 Throne API。"
weight = 50
toc = true
+++

本页用一句话解释你在 Throne 中遇到的每个术语。点击最后一列中的链接可以了解更多。

## 配置档与分组 {#profiles-and-groups}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Profile（配置档） | 列表中的一个服务器或代理条目，包含其协议、地址和设置。 | [快速入门](@/get_started/configuration.zh.md#add-servers) |
| Group（分组） | 主窗口中存放配置档的一个标签页；分组分为 `基本`（Basic）和 `订阅`（Subscription）两种，前者由你自行添加配置档，后者的内容从某个 URL 获取。 | [订阅与分组](@/guides/subscriptions.zh.md#group-options) |
| Subscription（订阅） | 服务商提供的一个 URL，它返回一份配置档列表，Throne 会将其下载到一个分组中，之后还可以更新。 | [订阅与分组](@/guides/subscriptions.zh.md#add-subscription) |
| Share link（分享链接） | 代表一个配置档的单行链接，例如 `vless://…` 或 `ss://…`，其他应用也能识别。 | [协议与导入格式](@/reference/protocols.zh.md#share-links) |
| Deep link（深度链接） | 一种 `throne://` 链接，用于让 Throne 执行某项操作，例如添加订阅或路由配置档。 | [深度链接](@/advanced/deeplinks.zh.md#format) |
| Throne link（Throne 链接） | 携带一个配置档的 `throne://add/…` 深度链接；可以通过 `复制选定项的链接 (深度链接)`（Copy links of selected (Deep Links)）获得（仅在高级模式下可用）。 | [深度链接](@/advanced/deeplinks.zh.md#add) |
| Chain（代理链） | 类型为 `链式代理`（Chain Proxy）的配置档，它让流量依次经过多个配置档。 | [代理链与自定义配置](@/advanced/chains.zh.md#chains) |
| Front / landing proxy（前置代理 / 落地代理） | 分组选项，用于在该分组的每个配置档之前（前置）或之后（落地）额外添加一个配置档（仅在高级模式下可用）。 | [代理链与自定义配置](@/advanced/chains.zh.md#front-landing) |
| Auto selector（自动选择器） | 一种配置档类型，它会测试分组中的服务器，并自动使用表现最好的那些服务器。 | [测试与自动选择器](@/guides/testing.zh.md#auto-selector) |
| URL test（URL 测试） | 一种延迟测试：通过某个配置档打开 `延迟测试 URL`（Latency Test URL），并测量所需的时间。 | [测试与自动选择器](@/guides/testing.zh.md#url-test) |

## 界面 {#interface}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Simple mode（简洁模式） | 自 1.4.0 起全新安装默认使用的界面模式；它会隐藏高级菜单项和设置，被隐藏的设置会保留原值并继续生效。 | [简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode) |
| Advanced mode（高级模式） | 显示所有菜单项和设置的界面模式；从旧版本更新的安装会保留此模式。 | [简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode) |
| Command palette（命令面板） | 用 `Ctrl+K` 打开的搜索窗口，可以按名称查找菜单命令和设置，并运行或打开它们。 | [命令面板](@/reference/shortcuts.zh.md#command-palette) |

## 核心与连接模式 {#core-and-modes}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Core（核心） | 即 ThroneCore，负责承载你的流量的后台程序；Throne 窗口只负责控制它。 | [Throne 是什么](@/get_started/_index.zh.md#what-is-throne) |
| sing-box | ThroneCore 所基于的开源引擎；它负责 TUN 模式、路由、DNS 和大多数协议。 | [sing-box 与 Xray](@/advanced/xray.zh.md) |
| Xray | 在核心内部运行的第二个引擎，用于 `VLESS (Xray)` 配置档和 Xray 配置。 | [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference) |
| Inbound（入站） | 流量进入 Throne 的途径：混合端口、TUN 网卡或自定义入站。 | [入站设置](@/guides/proxy_modes.zh.md#inbound-settings) |
| Outbound（出站） | 流量离开 Throne 的途径：经由配置档（代理）、直接发出，或完全不发出（阻止）。 | [路由](@/guides/routing.zh.md#how-routing-works) |
| Mixed port（混合端口） | 同时接受 SOCKS5 和 HTTP 代理连接的本地端口，默认为 `127.0.0.1:2080`。 | [入站设置](@/guides/proxy_modes.zh.md#inbound-settings) |
| System proxy（系统代理） | 即 `系统代理`（System Proxy）模式，它把系统的代理设置指向混合端口，从而让遵循该设置的应用使用 Throne。 | [系统代理](@/guides/proxy_modes.zh.md#system-proxy) |
| TUN（TUN 模式） | 即 `Tun 模式`（Tun Mode），它会创建一个虚拟网卡，使所有应用的流量都经过 Throne。 | [TUN 模式](@/guides/tun_mode.zh.md) |
| Stack（协议栈） | TUN 模式处理数据包的方式；旧版本允许你选择 `system`、`gvisor` 或 `mixed`，但 Throne 1.4.0 和 Throne for Android 2.0.2 已没有此设置。 | [TUN 设置](@/guides/tun_mode.zh.md#settings) |
| Strict route（严格路由） | TUN 的一个选项，用于防止流量绕过隧道；在 Windows 10 及更高版本上默认开启。 | [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route) |
| MTU（最大传输单元） | TUN 网卡的最大数据包大小：桌面版默认为 1500，Android 上默认为 9000。 | [TUN 设置](@/guides/tun_mode.zh.md#settings) |

## 路由与 DNS {#routing-and-dns}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Routing profile（路由配置档） | 一组有名称的规则加上一个默认出站；同一时间只有一个路由配置档处于活动状态。 | [路由](@/guides/routing.zh.md#profiles) |
| Rule（规则） | 一个条件（例如域名、IP 范围、规则集或程序）以及对匹配连接的处理方式；规则按从上到下的顺序检查。 | [路由](@/guides/routing.zh.md#advanced-rules) |
| Rule-set（规则集） | 供规则使用的现成域名列表（`geosite-…`）或 IP 范围列表（`geoip-…`），以二进制 `.srs` 文件的形式下载。 | [规则集](@/guides/routing.zh.md#rule-sets) |
| Default outbound（默认出站） | 没有任何规则匹配时连接的去向：`proxy`、`direct`、`block` 或 `warp-bypass`。 | [路由](@/guides/routing.zh.md#how-routing-works) |
| Direct（直连） | 连接通过你平常的互联网连接发出，不经过代理。 | [路由](@/guides/routing.zh.md#how-routing-works) |
| Block（阻止） | 连接被拒绝。 | [路由](@/guides/routing.zh.md#how-routing-works) |
| warp-bypass | 在开启 WARP 时，连接经过你的配置档，但绕过 WARP。 | [Cloudflare WARP](@/advanced/warp.zh.md#warp-bypass) |
| Remote routing profile（远程路由配置档） | Throne 从某个 URL 下载、并且可以自动更新的路由配置档。 | [远程配置档](@/guides/routing.zh.md#remote-profiles) |
| Sniffing（嗅探） | Throne 从连接的开头读取域名，这样即使应用直接连接 IP 地址，域名规则也能生效。 | [路由](@/guides/routing.zh.md#advanced-rules) |
| DNS routing（DNS 路由） | 即 `启用 DNS 路由`（Enable DNS Routing）选项：与你的 `direct` 规则匹配的域名使用直连 DNS 解析，其他所有查询则发往 `默认 DNS 服务器`（Default DNS server），它默认为远程 DNS。 | [DNS](@/guides/dns.zh.md#how-dns-works) |
| Remote DNS / direct DNS（远程 DNS / 直连 DNS） | 远程 DNS（默认为 `https://8.8.8.8/dns-query`）经由代理使用，并且是默认服务器；直连 DNS（默认为 `localhost`，即系统的解析器）不经代理使用，负责解析与 `direct` 规则匹配的域名，并查询你的服务器地址。 | [DNS 设置](@/guides/dns.zh.md#settings) |
| FakeIP | 一种默认关闭的 DNS 模式，它用临时的虚假地址应答查询，再将这些地址映射回真实的域名。 | [FakeIP](@/guides/dns.zh.md#fakeip) |

## 协议与反审查 {#protocols}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Reality | VLESS 的一种安全选项，它让连接看起来像是在访问一个真实存在、但与之无关的网站。 | [sing-box 与 Xray](@/advanced/xray.zh.md#reality) |
| XHTTP | Xray 的一种传输方式，它把连接承载在普通的 HTTP 请求中。 | [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference) |
| uTLS | 让 TLS 握手看起来像常见浏览器（例如 Chrome）的握手。 | [反审查预设](@/advanced/presets.zh.md#utls) |
| ECH | 即 Encrypted Client Hello（加密的 Client Hello），用于在 TLS 握手中隐藏服务器名称。 | [反审查预设](@/advanced/presets.zh.md#ech) |
| TLS fragment（TLS 分片） | 将第一个 TLS 消息（Client Hello）拆分成小块，以绕过某些审查过滤。 | [反审查预设](@/advanced/presets.zh.md#tls-fragment) |
| Multiplex (mux)（多路复用） | 使用 smux、h2mux 或 yamux，在通往服务器的一条连接中承载多条连接。 | [反审查预设](@/advanced/presets.zh.md#multiplex) |
| WARP | Cloudflare 的免费 VPN 服务，Throne 可以将其添加为你的配置档之后的最后一跳。 | [Cloudflare WARP](@/advanced/warp.zh.md) |
| MASQUE | 一种基于 HTTP/3 或 HTTP/2 的隧道协议；Throne 有 `MASQUE` 配置档类型，WARP 也可以使用它。 | [Cloudflare WARP](@/advanced/warp.zh.md) |
| DNS tunnel（DNS 隧道） | 把你的流量放在 DNS 查询中传输，适用于只放行 DNS 的网络。Throne 提供 `DNSTT / VayDNS` 和 `MasterDNS` 配置档类型，以及 XDNS。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md) |
| Resolver（解析器） | 替你回答 DNS 查询的 DNS 服务器。DNS 隧道把查询发给解析器，再由解析器转交给隧道服务器。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md#resolvers) |
| mKCP | Xray 的一种传输方式，通过 UDP 承载连接。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md#xdns) |
| XDNS | 在最终掩码（Finalmask）中带有 `xdns` 掩码的 mKCP，它把 mKCP 数据包作为 DNS 查询发送：即 `VLESS (Xray)` 配置档中的 DNS 隧道。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md#xdns) |

## 隐私、文件与安全 {#files-and-security}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| HWID（硬件 ID） | 某些服务商要求提供的硬件标识；只有在你开启相应设置后，Throne 才会发送它。 | [隐私与网络请求](@/reference/privacy.zh.md#hwid) |
| OTP（一次性密码） | 某些 OpenVPN 和 OpenConnect 服务器要求输入的一次性密码（TOTP 或 HOTP 代码）；Throne 将它们保存在 `工具`（Tools）→ `OTP 管理器`（OTP Manager）中（仅在高级模式下可用）。 | [OTP 管理器](@/advanced/vpn_profiles.zh.md#otp-manager) |
| .thrbackup | Throne 的备份文件；桌面版和 Android 应用都可以创建和恢复它。 | [备份与恢复](@/guides/backup.zh.md#backup-restore) |
| Portable mode（便携模式） | Throne 将数据保存在程序旁边的 `config` 文件夹中，而不是你的用户文件夹中。 | [数据文件夹](@/reference/files.zh.md#data-folder) |
| Kill switch（断网保护） | 一项设置，用于拦截所有不经过 Throne 的流量，即使没有配置档运行时也是如此（自 1.4.0 起）。 | [Kill Switch（断网保护）](@/advanced/kill_switch.zh.md) |

## 自动化与无界面 Throne {#automation}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Throne API | 一种加密接口，在桌面版中默认关闭；脚本和其他设备可以凭密钥通过它控制和管理 Throne（自 1.4.0 起）。 | [Throne API 与 thronectl](@/advanced/api.zh.md) |
| thronectl | Throne API 的命令行客户端，适用于终端、脚本和热键工具。 | [Throne API 与 thronectl](@/advanced/api.zh.md#commands) |
| Admin request（管理请求） | 用于导入、删除、更改设置、备份、恢复、重启或退出的 Throne API 请求；除非你允许局域网访问，否则 Throne 只响应来自本机的此类请求。 | [管理请求](@/advanced/api.zh.md#admin-requests) |
| Headless Throne（无界面 Throne） | 以 `--headless` 启动的 Throne：没有窗口、托盘图标或对话框，适用于服务器、路由器和容器，通过 thronectl 控制（自 1.4.0 起）。 | [无界面 Throne](@/headless/_index.zh.md) |
