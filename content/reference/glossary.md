+++
title = "Glossary"
description = "Short explanations of the terms you meet in Throne."
weight = 50
toc = true
+++

## Profiles and groups {#profiles-and-groups}

| Term | Meaning | More |
| --- | --- | --- |
| Profile | One server or proxy entry in the list, with its protocol, address and settings. | [Quick Start](@/get_started/configuration.md#add-servers) |
| Group | A tab of the main window that holds profiles. | [Subscriptions & Groups](@/guides/subscriptions.md#group-options) |
| Subscription | A URL from your provider that returns a list of profiles, which Throne downloads into a group. | [Subscriptions & Groups](@/guides/subscriptions.md#add-subscription) |
| Share link | A one-line link for one profile, such as `vless://…`, that other apps also understand. | [Protocols & Import Formats](@/reference/protocols.md#share-links) |
| Deep link | A `throne://` link that tells Throne to do something, such as add a subscription. | [Deep Links](@/advanced/deeplinks.md#format) |
| Throne link | A `throne://add/…` deep link that carries one profile of any type. | [Deep Links](@/advanced/deeplinks.md#add) |
| Chain | A `Chain Proxy` profile that sends traffic through several profiles one after another. | [Proxy Chains & Custom Configs](@/advanced/chains.md#chains) |
| Front / landing proxy | Group options that add one extra profile before (front) or after (landing) every profile of the group. | [Proxy Chains & Custom Configs](@/advanced/chains.md#front-landing) |
| Auto selector | A profile type that tests the servers of a group and uses the best ones automatically. | [Testing & Auto Selector](@/guides/testing.md#auto-selector) |
| URL test | A latency test that measures how long a profile takes to open a test URL. | [Testing & Auto Selector](@/guides/testing.md#url-test) |

## Interface {#interface}

| Term | Meaning | More |
| --- | --- | --- |
| Simple mode | The interface mode of new installations, which hides advanced menu items and settings. | [Simple and Advanced mode](@/get_started/configuration.md#interface-mode) |
| Advanced mode | The interface mode that shows every menu item and setting. | [Simple and Advanced mode](@/get_started/configuration.md#interface-mode) |
| Command palette | A search window that finds menu commands and settings by name. | [Command palette](@/reference/shortcuts.md#command-palette) |

## Core and connection modes {#core-and-modes}

| Term | Meaning | More |
| --- | --- | --- |
| Core | ThroneCore, the background program that carries your traffic. | [What is Throne](@/get_started/_index.md#what-is-throne) |
| sing-box | The open-source engine that ThroneCore is built on. | [sing-box vs Xray](@/advanced/xray.md) |
| Xray | A second engine that runs inside the core for `VLESS (Xray)` profiles and Xray configs. | [sing-box vs Xray](@/advanced/xray.md#vless-preference) |
| Inbound | A way for traffic to enter Throne, such as the mixed port or the TUN adapter. | [Inbound settings](@/guides/proxy_modes.md#inbound-settings) |
| Outbound | A way for traffic to leave Throne: through a profile (`proxy`), directly (`direct`) or nowhere (`block`). | [Routing](@/guides/routing.md#how-routing-works) |
| Mixed port | The local port that accepts both SOCKS5 and HTTP proxy connections. | [Inbound settings](@/guides/proxy_modes.md#inbound-settings) |
| System proxy | The `System Proxy` mode, which points your system's proxy setting to the mixed port. | [System proxy](@/guides/proxy_modes.md#system-proxy) |
| TUN | The `Tun Mode`, which passes the traffic of all apps through a virtual network adapter. | [TUN Mode](@/guides/tun_mode.md) |
| Strict route | A TUN option that stops traffic from going around the tunnel. | [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route) |
| MTU | The largest packet size of the TUN adapter. | [TUN settings](@/guides/tun_mode.md#settings) |

## Routing and DNS {#routing-and-dns}

| Term | Meaning | More |
| --- | --- | --- |
| Routing profile | A named set of rules plus a default outbound; one is active at a time. | [Routing](@/guides/routing.md#profiles) |
| Rule | A condition, such as a domain or a program, and the outbound for matching connections. | [Routing](@/guides/routing.md#advanced-rules) |
| Rule-set | A ready-made list of domains (`geosite-…`) or IP ranges (`geoip-…`) for rules. | [Rule-sets](@/guides/routing.md#rule-sets) |
| Default outbound | Where a connection goes when no rule matches. | [Routing](@/guides/routing.md#how-routing-works) |
| warp-bypass | An outbound that uses your profile but skips WARP. | [Cloudflare WARP](@/advanced/warp.md#warp-bypass) |
| Remote routing profile | A routing profile that Throne downloads from a URL. | [Remote profiles](@/guides/routing.md#remote-profiles) |
| Sniffing | Reading the domain name from the start of a connection, so that domain rules also work for connections to IP addresses. | [Routing](@/guides/routing.md#advanced-rules) |
| DNS routing | The `Enable DNS Routing` option, which resolves domains that match `direct` rules with direct DNS. | [DNS](@/guides/dns.md#how-dns-works) |
| Remote DNS / direct DNS | The DNS servers used through the proxy (remote) and without it (direct). | [DNS settings](@/guides/dns.md#settings) |
| FakeIP | A DNS mode that answers with temporary fake addresses and maps them back to the real domain names. | [FakeIP](@/guides/dns.md#fakeip) |

## Protocols and anti-censorship {#protocols}

| Term | Meaning | More |
| --- | --- | --- |
| Reality | A security option for VLESS that makes the connection look like a visit to a real, unrelated website. | [sing-box vs Xray](@/advanced/xray.md#reality) |
| XHTTP | An Xray transport that carries the connection inside ordinary HTTP requests. | [sing-box vs Xray](@/advanced/xray.md#vless-preference) |
| uTLS | Makes the TLS handshake look like that of a common browser. | [Anti-Censorship Presets](@/advanced/presets.md#utls) |
| ECH | Encrypted Client Hello, which hides the server name inside the TLS handshake. | [Anti-Censorship Presets](@/advanced/presets.md#ech) |
| TLS fragment | Splits the first TLS message into small pieces to get past some censorship filters. | [Anti-Censorship Presets](@/advanced/presets.md#tls-fragment) |
| Multiplex (mux) | Carries many connections inside one connection to the server. | [Anti-Censorship Presets](@/advanced/presets.md#multiplex) |
| WARP | The free VPN service of Cloudflare, which Throne can add as the last hop after your profile. | [Cloudflare WARP](@/advanced/warp.md) |
| MASQUE | A tunnel protocol over HTTP/3 or HTTP/2, available as a profile type and as a WARP mode. | [Cloudflare WARP](@/advanced/warp.md) |
| DNS tunnel | Carries your traffic inside DNS queries, for networks where only DNS gets through. | [DNS Tunnels](@/advanced/dns_tunnels.md) |
| Resolver | A DNS server that answers queries for you and, for a DNS tunnel, passes them on to the tunnel server. | [DNS Tunnels](@/advanced/dns_tunnels.md#resolvers) |
| mKCP | An Xray transport that carries the connection over UDP. | [DNS Tunnels](@/advanced/dns_tunnels.md#xdns) |
| XDNS | A DNS tunnel in a `VLESS (Xray)` profile that sends mKCP packets as DNS queries. | [DNS Tunnels](@/advanced/dns_tunnels.md#xdns) |

## Privacy, files and security {#files-and-security}

| Term | Meaning | More |
| --- | --- | --- |
| HWID | A hardware ID that some subscription providers ask for. | [Privacy & Network Requests](@/reference/privacy.md#hwid) |
| OTP | One-time passwords (TOTP or HOTP codes) that some OpenVPN and OpenConnect servers ask for. | [OTP Manager](@/advanced/vpn_profiles.md#otp-manager) |
| .thrbackup | The backup file of Throne, on the desktop and on Android. | [Backup and restore](@/guides/backup.md#backup-restore) |
| Portable mode | Throne keeps its data next to the program instead of in your user folder. | [Data folder](@/reference/files.md#data-folder) |
| Kill switch | A setting that blocks all traffic that does not go through Throne. | [Kill Switch](@/advanced/kill_switch.md) |

## Automation and headless {#automation}

| Term | Meaning | More |
| --- | --- | --- |
| Throne API | An encrypted interface through which scripts and other devices control Throne with a secret key. | [Throne API & thronectl](@/advanced/api.md) |
| thronectl | The command-line client of the Throne API. | [Throne API & thronectl](@/advanced/api.md#commands) |
| Admin request | A sensitive Throne API request that Throne answers only from its own computer, unless you allow the local network. | [Admin requests](@/advanced/api.md#admin-requests) |
| Headless Throne | Throne running without a window, for servers, routers and containers. | [Headless Throne](@/headless/_index.md) |
