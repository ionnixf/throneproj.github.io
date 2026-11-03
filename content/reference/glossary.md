+++
title = "Glossary"
description = "Short explanations of the terms you meet in Throne, from profiles and routing to TUN mode, DNS, anti-censorship options and the Throne API."
weight = 50
toc = true
+++

This page explains the terms you meet in Throne, one sentence each. Follow the link in the last column to learn more.

## Profiles and groups {#profiles-and-groups}

| Term | Meaning | More |
| --- | --- | --- |
| Profile | One server or proxy entry in the list, with its protocol, address and settings. | [Quick Start](@/get_started/configuration.md#add-servers) |
| Group | A tab of the main window that holds profiles; a group is either `Basic` (you add the profiles) or `Subscription` (filled from a URL). | [Subscriptions & Groups](@/guides/subscriptions.md#group-options) |
| Subscription | A URL from your provider that returns a list of profiles, which Throne downloads into a group and can update later. | [Subscriptions & Groups](@/guides/subscriptions.md#add-subscription) |
| Share link | A one-line link for one profile, such as `vless://…` or `ss://…`, that other apps also understand. | [Protocols & Import Formats](@/reference/protocols.md#share-links) |
| Deep link | A `throne://` link that tells Throne to do something, such as add a subscription or a routing profile. | [Deep Links](@/advanced/deeplinks.md#format) |
| Throne link | A `throne://add/…` deep link that carries one profile; you get it with `Copy links of selected (Deep Links)` (Advanced mode only). | [Deep Links](@/advanced/deeplinks.md#add) |
| Chain | A profile of the type `Chain Proxy` that sends traffic through several profiles one after another. | [Proxy Chains & Custom Configs](@/advanced/chains.md#chains) |
| Front / landing proxy | Group options that add one extra profile before (front) or after (landing) every profile of the group (Advanced mode only). | [Proxy Chains & Custom Configs](@/advanced/chains.md#front-landing) |
| Auto selector | A profile type that tests the servers of a group and uses the best ones automatically. | [Testing & Auto Selector](@/guides/testing.md#auto-selector) |
| URL test | A latency test that opens the `Latency Test URL` through a profile and measures how long it takes. | [Testing & Auto Selector](@/guides/testing.md#url-test) |

## Interface {#interface}

| Term | Meaning | More |
| --- | --- | --- |
| Simple mode | The interface mode that a new installation starts in since 1.4.0; it hides advanced menu items and settings, which keep their values and still apply. | [Simple and Advanced mode](@/get_started/configuration.md#interface-mode) |
| Advanced mode | The interface mode that shows every menu item and setting; installations updated from an older version keep it. | [Simple and Advanced mode](@/get_started/configuration.md#interface-mode) |
| Command palette | A search window, opened with `Ctrl+K`, that finds menu commands and settings by name and runs or opens them. | [Command palette](@/reference/shortcuts.md#command-palette) |

## Core and connection modes {#core-and-modes}

| Term | Meaning | More |
| --- | --- | --- |
| Core | ThroneCore, the background program that carries your traffic; the Throne window only controls it. | [What is Throne](@/get_started/_index.md#what-is-throne) |
| sing-box | The open-source engine that ThroneCore is built on; it handles TUN mode, routing, DNS and most protocols. | [sing-box vs Xray](@/advanced/xray.md) |
| Xray | A second engine that runs inside the core for `VLESS (Xray)` profiles and Xray configs. | [sing-box vs Xray](@/advanced/xray.md#vless-preference) |
| Inbound | A way for traffic to enter Throne: the mixed port, the TUN adapter, or a custom inbound. | [Inbound settings](@/guides/proxy_modes.md#inbound-settings) |
| Outbound | A way for traffic to leave Throne: through a profile (proxy), directly, or not at all (block). | [Routing](@/guides/routing.md#how-routing-works) |
| Mixed port | The local port that accepts both SOCKS5 and HTTP proxy connections, `127.0.0.1:2080` by default. | [Inbound settings](@/guides/proxy_modes.md#inbound-settings) |
| System proxy | The `System Proxy` mode, which points the proxy setting of your system to the mixed port, so apps that follow this setting use Throne. | [System proxy](@/guides/proxy_modes.md#system-proxy) |
| TUN | The `Tun Mode`, which creates a virtual network adapter so that the traffic of all apps passes through Throne. | [TUN Mode](@/guides/tun_mode.md) |
| Stack | How TUN mode handles packets; older versions let you choose `system`, `gvisor` or `mixed`, but Throne 1.4.0 and Throne for Android 2.0.2 have no such setting. | [TUN settings](@/guides/tun_mode.md#settings) |
| Strict route | A TUN option that stops traffic from going around the tunnel; on Windows 10 and later it is on by default. | [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route) |
| MTU | The largest packet size of the TUN adapter: 1500 by default on the desktop, 9000 on Android. | [TUN settings](@/guides/tun_mode.md#settings) |

## Routing and DNS {#routing-and-dns}

| Term | Meaning | More |
| --- | --- | --- |
| Routing profile | A named set of rules plus a default outbound; exactly one routing profile is active at a time. | [Routing](@/guides/routing.md#profiles) |
| Rule | A condition, such as a domain, an IP range, a rule-set or a program, and what to do with matching connections; rules are checked from top to bottom. | [Routing](@/guides/routing.md#advanced-rules) |
| Rule-set | A ready-made list of domains (`geosite-…`) or IP ranges (`geoip-…`) for rules, downloaded as a binary `.srs` file. | [Rule-sets](@/guides/routing.md#rule-sets) |
| Default outbound | Where a connection goes when no rule matches: `proxy`, `direct`, `block` or `warp-bypass`. | [Routing](@/guides/routing.md#how-routing-works) |
| Direct | The connection goes out through your normal internet connection, without a proxy. | [Routing](@/guides/routing.md#how-routing-works) |
| Block | The connection is refused. | [Routing](@/guides/routing.md#how-routing-works) |
| warp-bypass | The connection goes through your profile but skips WARP, when WARP is on. | [Cloudflare WARP](@/advanced/warp.md#warp-bypass) |
| Remote routing profile | A routing profile that Throne downloads from a URL and can update automatically. | [Remote profiles](@/guides/routing.md#remote-profiles) |
| Sniffing | Throne reads the domain name from the start of a connection, so domain rules also work when an app connects to an IP address. | [Routing](@/guides/routing.md#advanced-rules) |
| DNS routing | The option `Enable DNS Routing`: domains that match your `direct` rules are resolved with direct DNS, and all other queries go to the `Default DNS server` (remote by default). | [DNS](@/guides/dns.md#how-dns-works) |
| Remote DNS / direct DNS | Remote DNS (by default `https://8.8.8.8/dns-query`) is used through the proxy and is the default server; direct DNS (by default `localhost`, your system's resolver) is used without the proxy, answers for domains that match `direct` rules and looks up the addresses of your servers. | [DNS settings](@/guides/dns.md#settings) |
| FakeIP | A DNS mode, off by default, that answers with temporary fake addresses and maps them back to the real domain names. | [FakeIP](@/guides/dns.md#fakeip) |

## Protocols and anti-censorship {#protocols}

| Term | Meaning | More |
| --- | --- | --- |
| Reality | A security option for VLESS that makes the connection look like a visit to a real, unrelated website. | [sing-box vs Xray](@/advanced/xray.md#reality) |
| XHTTP | An Xray transport that carries the connection inside ordinary HTTP requests. | [sing-box vs Xray](@/advanced/xray.md#vless-preference) |
| uTLS | Makes the TLS handshake look like the handshake of a common browser, such as Chrome. | [Anti-Censorship Presets](@/advanced/presets.md#utls) |
| ECH | Encrypted Client Hello, which hides the server name inside the TLS handshake. | [Anti-Censorship Presets](@/advanced/presets.md#ech) |
| TLS fragment | Splits the first TLS message (the Client Hello) into small pieces to get past some censorship filters. | [Anti-Censorship Presets](@/advanced/presets.md#tls-fragment) |
| Multiplex (mux) | Carries many connections inside one connection to the server, with smux, h2mux or yamux. | [Anti-Censorship Presets](@/advanced/presets.md#multiplex) |
| WARP | The free VPN service of Cloudflare, which Throne can add as the last hop after your profile. | [Cloudflare WARP](@/advanced/warp.md) |
| MASQUE | A tunnel protocol over HTTP/3 or HTTP/2; Throne has a `MASQUE` profile type, and WARP can use it. | [Cloudflare WARP](@/advanced/warp.md) |
| DNS tunnel | Carries your traffic inside DNS queries, for networks where only DNS gets through. Throne has the `DNSTT / VayDNS` and `MasterDNS` profile types, and XDNS. | [DNS Tunnels](@/advanced/dns_tunnels.md) |
| Resolver | A DNS server that answers DNS queries for you. A DNS tunnel sends its queries to resolvers, which pass them on to the tunnel server. | [DNS Tunnels](@/advanced/dns_tunnels.md#resolvers) |
| mKCP | An Xray transport that carries the connection over UDP. | [DNS Tunnels](@/advanced/dns_tunnels.md#xdns) |
| XDNS | mKCP with an `xdns` mask in Finalmask, which sends the mKCP packets as DNS queries: a DNS tunnel in a `VLESS (Xray)` profile. | [DNS Tunnels](@/advanced/dns_tunnels.md#xdns) |

## Privacy, files and security {#files-and-security}

| Term | Meaning | More |
| --- | --- | --- |
| HWID | A hardware ID that some providers ask for; Throne sends it only if you turn it on. | [Privacy & Network Requests](@/reference/privacy.md#hwid) |
| OTP | One-time passwords (TOTP or HOTP codes) that some OpenVPN and OpenConnect servers ask for; Throne keeps them in `Tools` → `OTP Manager` (Advanced mode only). | [OTP Manager](@/advanced/vpn_profiles.md#otp-manager) |
| .thrbackup | The backup file of Throne; both the desktop and the Android app can create and restore it. | [Backup and restore](@/guides/backup.md#backup-restore) |
| Portable mode | Throne keeps its data in a `config` folder next to the program instead of in your user folder. | [Data folder](@/reference/files.md#data-folder) |
| Kill switch | A setting that blocks all traffic that does not go through Throne, also while no profile runs (since 1.4.0). | [Kill Switch](@/advanced/kill_switch.md) |

## Automation and headless {#automation}

| Term | Meaning | More |
| --- | --- | --- |
| Throne API | An encrypted interface, off by default in the desktop app, through which scripts and other devices control and manage Throne with a secret key (since 1.4.0). | [Throne API & thronectl](@/advanced/api.md) |
| thronectl | The command-line client of the Throne API, for terminals, scripts and hotkey tools. | [Throne API & thronectl](@/advanced/api.md#commands) |
| Admin request | A Throne API request that imports, deletes, changes settings, backs up, restores, restarts or quits; Throne answers it only from its own computer unless you allow the local network. | [Admin requests](@/advanced/api.md#admin-requests) |
| Headless Throne | Throne started with `--headless`: no window, tray icon or dialogs, for servers, routers and containers, controlled with thronectl (since 1.4.0). | [Headless Throne](@/headless/_index.md) |
