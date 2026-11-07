+++
title = "术语表"
description = "对 Throne 中常见术语的简要解释。"
weight = 50
toc = true
+++

## 配置档与分组 {#profiles-and-groups}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Profile（配置档） | 列表中的一个服务器或代理条目，包含其协议、地址和设置。 | [快速入门](@/get_started/configuration.zh.md#add-servers) |
| Group（分组） | 主窗口中存放配置档的一个标签页。 | [订阅与分组](@/guides/subscriptions.zh.md#group-options) |
| Subscription（订阅） | 服务商提供的一个 URL，它返回一份配置档列表，Throne 会将其下载到一个分组中。 | [订阅与分组](@/guides/subscriptions.zh.md#add-subscription) |
| Share link（分享链接） | 代表一个配置档的单行链接，例如 `vless://…`，其他应用也能识别。 | [协议与导入格式](@/reference/protocols.zh.md#share-links) |
| Deep link（深度链接） | 一种 `throne://` 链接，用于让 Throne 执行某项操作，例如添加订阅。 | [深度链接](@/advanced/deeplinks.zh.md#format) |
| Throne link（Throne 链接） | 携带一个任意类型配置档的 `throne://add/…` 深度链接。 | [深度链接](@/advanced/deeplinks.zh.md#add) |
| Chain（代理链） | `链式代理`（Chain Proxy）配置档，它让流量依次经过多个配置档。 | [代理链与自定义配置](@/advanced/chains.zh.md#chains) |
| Front / landing proxy（前置代理 / 落地代理） | 分组选项，用于在该分组的每个配置档之前（前置）或之后（落地）额外添加一个配置档。 | [代理链与自定义配置](@/advanced/chains.zh.md#front-landing) |
| Auto selector（自动选择器） | 一种配置档类型，它会测试分组中的服务器，并自动使用表现最好的那些服务器。 | [测试与自动选择器](@/guides/testing.zh.md#auto-selector) |
| URL test（URL 测试） | 一种延迟测试，测量通过某个配置档打开测试 URL 所需的时间。 | [测试与自动选择器](@/guides/testing.zh.md#url-test) |

## 界面 {#interface}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Simple mode（简洁模式） | 全新安装使用的界面模式，它会隐藏高级菜单项和设置。 | [简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode) |
| Advanced mode（高级模式） | 显示所有菜单项和设置的界面模式。 | [简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode) |
| Command palette（命令面板） | 按名称查找菜单命令和设置的搜索窗口。 | [命令面板](@/reference/shortcuts.zh.md#command-palette) |

## 核心与连接模式 {#core-and-modes}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Core（核心） | 即 ThroneCore，负责承载你的流量的后台程序。 | [Throne 是什么](@/get_started/_index.zh.md#what-is-throne) |
| sing-box | ThroneCore 所基于的开源引擎。 | [sing-box 与 Xray](@/advanced/xray.zh.md) |
| Xray | 在核心内部运行的第二个引擎，用于 `VLESS (Xray)` 配置档和 Xray 配置。 | [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference) |
| Inbound（入站） | 流量进入 Throne 的途径，例如混合端口或 TUN 网卡。 | [入站设置](@/guides/proxy_modes.zh.md#inbound-settings) |
| Outbound（出站） | 流量离开 Throne 的途径：经由配置档（`proxy`）、直连（`direct`）或被拦截（`block`）。 | [路由](@/guides/routing.zh.md#how-routing-works) |
| Mixed port（混合端口） | 同时接受 SOCKS5 和 HTTP 代理连接的本地端口。 | [入站设置](@/guides/proxy_modes.zh.md#inbound-settings) |
| System proxy（系统代理） | 即 `系统代理`（System Proxy）模式，它把系统的代理设置指向混合端口。 | [系统代理](@/guides/proxy_modes.zh.md#system-proxy) |
| TUN（TUN 模式） | 即 `Tun 模式`（Tun Mode），它让所有应用的流量经过一个虚拟网卡。 | [TUN 模式](@/guides/tun_mode.zh.md) |
| Strict route（严格路由） | TUN 的一个选项，用于防止流量绕过隧道。 | [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route) |
| MTU（最大传输单元） | TUN 网卡的最大数据包大小。 | [TUN 设置](@/guides/tun_mode.zh.md#settings) |

## 路由与 DNS {#routing-and-dns}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Routing profile（路由配置档） | 一组有名称的规则加上一个默认出站；同一时间只有一个处于活动状态。 | [路由](@/guides/routing.zh.md#profiles) |
| Rule（规则） | 一个条件（例如域名或程序）以及匹配连接所用的出站。 | [路由](@/guides/routing.zh.md#advanced-rules) |
| Rule-set（规则集） | 供规则使用的现成域名列表（`geosite-…`）或 IP 范围列表（`geoip-…`）。 | [规则集](@/guides/routing.zh.md#rule-sets) |
| Default outbound（默认出站） | 没有任何规则匹配时连接的去向。 | [路由](@/guides/routing.zh.md#how-routing-works) |
| warp-bypass | 经过你的配置档但绕过 WARP 的出站。 | [Cloudflare WARP](@/advanced/warp.zh.md#warp-bypass) |
| Remote routing profile（远程路由配置档） | Throne 从某个 URL 下载的路由配置档。 | [远程配置档](@/guides/routing.zh.md#remote-profiles) |
| Sniffing（嗅探） | 从连接的开头读取域名，使域名规则对直接连接 IP 地址的流量也能生效。 | [路由](@/guides/routing.zh.md#advanced-rules) |
| DNS routing（DNS 路由） | 即 `启用 DNS 路由`（Enable DNS Routing）选项，它用直连 DNS 解析与 `direct` 规则匹配的域名。 | [DNS](@/guides/dns.zh.md#how-dns-works) |
| Remote DNS / direct DNS（远程 DNS / 直连 DNS） | 经由代理（远程）和不经代理（直连）使用的 DNS 服务器。 | [DNS 设置](@/guides/dns.zh.md#settings) |
| FakeIP | 一种 DNS 模式，它用临时的虚假地址应答查询，再将这些地址映射回真实的域名。 | [FakeIP](@/guides/dns.zh.md#fakeip) |

## 协议与反审查 {#protocols}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Reality | VLESS 的一种安全选项，它让连接看起来像是在访问一个真实存在、但与之无关的网站。 | [sing-box 与 Xray](@/advanced/xray.zh.md#reality) |
| XHTTP | Xray 的一种传输方式，它把连接承载在普通的 HTTP 请求中。 | [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference) |
| uTLS | 让 TLS 握手看起来像常见浏览器的握手。 | [反审查预设](@/advanced/presets.zh.md#utls) |
| ECH | 即 Encrypted Client Hello（加密的 Client Hello），用于在 TLS 握手中隐藏服务器名称。 | [反审查预设](@/advanced/presets.zh.md#ech) |
| TLS fragment（TLS 分片） | 将第一个 TLS 消息拆分成小块，以绕过某些审查过滤。 | [反审查预设](@/advanced/presets.zh.md#tls-fragment) |
| Multiplex (mux)（多路复用） | 在通往服务器的一条连接中承载多条连接。 | [反审查预设](@/advanced/presets.zh.md#multiplex) |
| WARP | Cloudflare 的免费 VPN 服务，Throne 可以将其添加为你的配置档之后的最后一跳。 | [Cloudflare WARP](@/advanced/warp.zh.md) |
| MASQUE | 一种基于 HTTP/3 或 HTTP/2 的隧道协议，可用作配置档类型，也是 WARP 的一种模式。 | [Cloudflare WARP](@/advanced/warp.zh.md) |
| DNS tunnel（DNS 隧道） | 把你的流量放在 DNS 查询中传输，适用于只放行 DNS 的网络。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md) |
| Resolver（解析器） | 替你回答 DNS 查询的 DNS 服务器；对 DNS 隧道而言，它把查询转交给隧道服务器。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md#resolvers) |
| mKCP | Xray 的一种传输方式，通过 UDP 承载连接。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md#xdns) |
| XDNS | `VLESS (Xray)` 配置档中的 DNS 隧道，它把 mKCP 数据包作为 DNS 查询发送。 | [DNS 隧道](@/advanced/dns_tunnels.zh.md#xdns) |

## 隐私、文件与安全 {#files-and-security}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| HWID（硬件 ID） | 某些订阅服务商要求提供的硬件标识。 | [隐私与网络请求](@/reference/privacy.zh.md#hwid) |
| OTP（一次性密码） | 某些 OpenVPN 和 OpenConnect 服务器要求输入的一次性密码（TOTP 或 HOTP 代码）。 | [OTP 管理器](@/advanced/vpn_profiles.zh.md#otp-manager) |
| .thrbackup | Throne 的备份文件，桌面版和 Android 通用。 | [备份与恢复](@/guides/backup.zh.md#backup-restore) |
| Portable mode（便携模式） | Throne 将数据保存在程序旁边，而不是你的用户文件夹中。 | [数据文件夹](@/reference/files.zh.md#data-folder) |
| Kill switch（断网保护） | 一项设置，用于拦截所有不经过 Throne 的流量。 | [Kill Switch（断网保护）](@/advanced/kill_switch.zh.md) |

## 自动化与无界面 Throne {#automation}

| 术语 | 含义 | 了解更多 |
| --- | --- | --- |
| Throne API | 一种加密接口，脚本和其他设备可以凭密钥通过它控制 Throne。 | [Throne API 与 thronectl](@/advanced/api.zh.md) |
| thronectl | Throne API 的命令行客户端。 | [Throne API 与 thronectl](@/advanced/api.zh.md#commands) |
| Admin request（管理请求） | 敏感的 Throne API 请求；除非你允许局域网访问，否则 Throne 只响应来自本机的此类请求。 | [管理请求](@/advanced/api.zh.md#admin-requests) |
| Headless Throne（无界面 Throne） | 没有窗口的 Throne，适用于服务器、路由器和容器。 | [无界面 Throne](@/headless/_index.zh.md) |
