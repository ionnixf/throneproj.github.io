+++
title = "DNS"
description = "Which DNS servers Throne uses, what the DNS settings do, and how to read a DNS leak test."
weight = 50
toc = true
+++

Throne answers DNS lookups with its own resolver. The default settings work for most people.

## How DNS works in Throne {#how-dns-works}

Throne uses three DNS servers:

| Server | Used for |
| --- | --- |
| `Remote DNS` | Lookups of your apps, sent through the proxy. |
| `Direct DNS` | Domains that your routing sends `direct`, and the address of your proxy server. Sent without the proxy. |
| `Local Override` | Looking up the names of the two servers above, such as `dns.google`. Empty means your system's resolver. |

Throne answers a query in this order:

1. `Predefined Answers` and `Respect Hosts File`.
2. With `Enable FakeIP`, a fake address (see [FakeIP](#fakeip)).
3. With `Enable DNS Routing` (on by default), domains that your routing profile sends `direct` go to `Direct DNS`. This works for domain rules and `geosite-*` rule-sets, not for IP rules, `geoip-*` rule-sets or your own `.srs` files.
4. Everything else goes to the `Default DNS server`, `remote` by default. If you set it to `direct`, domains that such rules send to `proxy` still use `Remote DNS`.

What reaches Throne depends on the mode:

- **TUN mode:** the DNS queries of all apps. Structured routing profiles handle them automatically; a [raw profile](@/guides/routing.md#raw-profiles) needs a `hijack-dns` rule. For macOS, see [TUN Mode](@/guides/tun_mode.md#side-effects-dns).
- **System proxy mode:** only the site names that apps send to the proxy; the proxy server looks up the names of proxied sites. Apps can also look names up themselves, outside Throne.

## Settings {#settings}

Open `Settings` → `Routing Settings` → `DNS`. Changes apply after you click `OK` and restart the connection. The active routing profile can replace some of these settings, see [DNS per routing profile](#route-dns). In Simple mode, `Remote DNS`, `Direct DNS` and `Default DNS server` are under `Simple DNS Settings` on the `Common` tab, and the rest is hidden (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

| Setting | Default | What it does |
| --- | --- | --- |
| `Remote DNS` | `https://8.8.8.8/dns-query` | The server used through the proxy. |
| `Direct DNS` | `localhost` | The server used without the proxy. `localhost` is your system's resolver. If it fails, choose a server that works on your network without the proxy, such as a public resolver from the list. |
| `Disable IPv6` (next to each server) | off | Gives an empty answer to IPv6 (AAAA) queries sent to that server. |
| `Local Override` | empty | Set an IP address here when your system's resolver cannot be used to look up your DNS servers. |
| `Respect Hosts File` | off | Answers from your system's hosts file first. |
| `Predefined Answers` | on | Fixed answers in hosts-file syntax, such as `10.0.0.5 nas.lan`. A name listed with only an IPv4 address gets no IPv6 answer, and the other way round. |

Server addresses can be plain DNS (`8.8.8.8` or `8.8.8.8:53`), `tcp://8.8.8.8:53`, `tls://1.1.1.1`, `https://1.1.1.1/dns-query`, `h3://dns.example.com/dns-query`, `quic://dns.example.com`, or `dhcp://auto` for the server your network hands out.

When the running profile uses Xray, a plain or QUIC `Remote DNS` is replaced with DNS over HTTPS: `1.1.1.1` becomes `https://1.1.1.1/dns-query`, and a server that Throne does not know becomes `https://8.8.8.8/dns-query`.

## FakeIP {#fakeip}

With `Enable FakeIP`, Throne answers address queries at once with a fake address from `198.18.0.0/15` (IPv6: `2001:db8::/32`). When the app connects to it, Throne routes the connection by its domain name, and the real lookup happens later: on the proxy server, or with `Direct DNS` for direct connections. Connections start faster, and no DNS query leaves your device before the connection is routed.

FakeIP only affects queries that reach Throne, so it helps in TUN mode, not in system proxy mode.

- Apps keep fake addresses in their caches. If an app stays offline after you stop Throne, restart the app.
- `FakeIP Disable IPv6` hands out no fake IPv6 addresses; AAAA queries get an empty answer.
- `Advanced Settings` → `Save Cache To File` keeps the fake addresses when Throne restarts.

## Advanced {#advanced}

`Advanced Settings` on the `DNS` tab controls the DNS cache. The options that are not obvious:

- `Optimistic Cache` keeps serving an expired answer while it is refreshed in the background, for up to `Optimistic Timeout` (default `3d`). It cannot be combined with `Disable Cache` or `Disable Expire`.
- `Save Cache To File` keeps cached answers and FakeIP addresses across restarts, at the cost of a disk write per new entry.
- `Reverse Mapping` remembers which domain an IP address was answered for, so domain rules can match connections to that address.

`Use Custom DNS Object` replaces everything on the tab with a complete sing-box `dns` object that you write with `Edit DNS Object` (see the [sing-box DNS documentation](https://sing-box.sagernet.org/configuration/dns/)). DNS routing, FakeIP and predefined answers then work only if your object sets them up.

{% alert_warning() %}
Keep a server with the tag `dns-direct` in a custom DNS object. Throne uses it to look up server addresses.
{% end %}

## DNS per routing profile {#route-dns}

A routing profile can have its own DNS servers, which replace the global ones while it is the active routing profile, for example a `Direct DNS` that works in the profile's country.

1. Open the profile: `Settings` → `Routing Settings` → `Route` → `Edit`, or right-click it at the bottom of the `Routing` menu.
2. On its `DNS` tab, choose `Use custom DNS settings`, change the settings and click `OK`.

The tab covers the two servers and their `Disable IPv6` boxes, `Default DNS server`, `Enable DNS Routing` and the FakeIP settings. The rest comes from the global `DNS` tab, and `Use Custom DNS Object` is ignored while such a profile is active.

- A [group's own routing profile](@/guides/routing.md#group-routing) brings its DNS settings while a profile of that group runs.
- Remote profiles and [routing profiles from a subscription](@/guides/routing.md#subscription-routing) keep their DNS settings when they update. Throne never takes DNS settings from the URL or the subscription.
- A `throne://route/` link carries the DNS settings, and `Import` in `Routing Settings` names the servers before it imports. Check them: they see every name you look up while the profile is active.
- In Simple mode, a profile's `DNS` tab appears only if the profile already uses its own DNS settings.

## DNS server for other devices {#dns-server}

While a profile runs, Throne also runs a DNS server that answers with the settings on this page. It listens on `127.0.0.1`, TCP and UDP port `5533`, set in `Settings` → `Basic Settings` → `Miscellaneous` → `Sing-box` → `DNS Server Port` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

To let other devices on your network use it, for example a router:

1. Tick `Allow other devices to connect` below `DNS Server Port`, click `OK` and restart the running profile.
2. Allow incoming TCP and UDP connections to the port in your firewall, as for [LAN sharing](@/guides/proxy_modes.md#lan-sharing). On Linux with ufw: `sudo ufw allow 5533`.
3. On the other device, set the DNS server to your computer's LAN address and the port, for example `192.168.1.20` and `5533`.

- Phones and computers usually accept only port 53 for DNS; many routers let you set the port.
- Anyone who can reach the port can look up names through your proxy. Use it only on a network you trust.
- With FakeIP on, other devices get fake addresses too, which work only for connections that pass through Throne.
- `Program` → `Allow other devices to connect` is a different setting: it shares the proxy port.
- In [thronectl](@/advanced/api.md#settings) and on [headless Throne](@/headless/_index.md), the settings are `dns_in_lan` and `dns_in_port`.

## DNS leak tests {#leak-tests}

A DNS leak test shows which DNS servers looked up its test names. These results are expected:

- **Your `Remote DNS` provider** (Google by default), or the servers of the active routing profile if it has [its own](#route-dns).
- **Your internet provider, for sites that go direct.** They are looked up with `Direct DNS`, your system's resolver by default. In system proxy mode or with FakeIP, this includes sites that go direct only because of the default outbound, as in the "Proxy … Blocked" profiles. Your provider sees direct traffic anyway.
- **Your internet provider, in system proxy mode.** Apps can look names up themselves, outside Throne. Use TUN mode if this matters to you.
- **Your browser's own provider,** when the browser uses secure DNS (DNS over HTTPS).
- **Your internet provider for most names,** when `Default DNS server` is `direct`.

A real leak looks like this: in TUN mode, with a routing profile that proxies the test site (such as `Default`), the test still shows your internet provider. On Windows, check that `Settings` → `Tun Settings` → `Strict Route` is on (the default on Windows 10 and later) and see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route).

WebRTC tests check your IP address, not your DNS. No public IP address there is fine ([#1267](https://github.com/throneproj/Throne/issues/1267)). Your real IP address for a site you proxy means that UDP went around the proxy, which is common in system proxy mode; use TUN mode.

## Hijack and System DNS (removed) {#deprecated}

Throne 1.4.0 removed the `Hijack` tab of `Routing Settings` and the Windows `System DNS` option. For apps that ignore the system proxy, use [TUN mode](@/guides/tun_mode.md#enable) instead.

## On Android {#android}

Throne for Android has the same settings in `Settings` → `DNS`, where FakeIP is called `Enable FakeDNS`. In VPN mode, Throne always handles the DNS queries inside the VPN. The differences:

- **`localhost` as `Direct DNS`** asks the DNS servers of the current Wi-Fi or mobile network directly. If they do not answer, connecting fails with "Could not connect: the network's DNS (…) did not answer. Set a working Direct DNS."
- **DNS per routing profile** is the `DNS` card of the routing profile editor. While the active routing profile uses its own servers, `Settings` → `DNS` says so at the top, and its settings apply only to other routing profiles.
- **A local DNS server for other apps** runs on `127.0.0.1`. Its port is `Settings` → `Inbound` → `DNS server port` (default `5533`).
