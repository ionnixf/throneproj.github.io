+++
title = "DNS"
description = "How Throne resolves domain names, what each DNS setting does, when FakeIP helps, and how to read a DNS leak test."
weight = 50
toc = true
+++

Before an app can open a site, a DNS lookup turns the site's name into an IP address. This page explains which DNS server Throne uses for which lookup, what the settings on the `DNS` tab do, and how to read a DNS leak test. The default settings work for most people.

## How DNS works in Throne {#how-dns-works}

Throne has its own DNS resolver inside the core. It uses three DNS servers:

| Server | How queries travel | What it is used for |
| --- | --- | --- |
| `Remote DNS` | Through the proxy | Answers the DNS queries of apps, unless a rule below sends them elsewhere. |
| `Direct DNS` | Directly, without the proxy | Answers queries for domains that your routing profile sends `direct`. Throne also uses it to look up the address of your proxy server, and the address of a site when it connects to that site directly by name. |
| `Local Override` | Directly | Looks up the names of the two servers above, for example `dns.google`. Empty means your system's resolver. |

When an app's query reaches Throne, Throne answers it in this order:

1. `Predefined Answers` and `Respect Hosts File` answer first.
2. With `Enable FakeIP`, address queries get a fake address (see [FakeIP](#fakeip)).
3. With `Enable DNS Routing` (on by default), domains that your routing profile sends `direct` go to Direct DNS. This covers `domain:`, `suffix:`, `keyword:` and `regex:` rules and built-in `geosite-*` rule-sets. It does not cover `geoip-*` rule-sets, IP rules or your own `.srs` files.
4. Everything else goes to the `Default DNS server`, which is `remote` by default. If you set it to `direct`, domains that your routing profile sends to `proxy` still use Remote DNS, under the same conditions as in step 3.

Where the queries come from depends on the mode:

- **TUN mode.** The DNS queries of your apps go into the tunnel and reach Throne. Throne adds a DNS step to every structured routing profile (the `Route DNS` or `dns-hijack` rule in the list does the same), so it answers these queries as described above. A raw routing profile only gets this step if you write it yourself; see [Raw profiles](@/guides/routing.md#raw-profiles). On macOS, since 1.4.0, the core adds the TUN adapter to macOS as a network service of its own while TUN mode runs, with a DNS server inside the tunnel. It does not change the DNS servers of your network services, such as `Wi-Fi`. See [TUN Mode](@/guides/tun_mode.md#side-effects-dns).
- **System proxy mode.** Apps that use the proxy send the site's name to Throne. For proxied connections, the proxy server looks the name up. For direct connections, Throne looks it up with Direct DNS. Apps can also look names up themselves with your system's resolver. Those lookups do not pass through Throne.

## Settings {#settings}

Open `Settings` → `Routing Settings` → `DNS`. Changes apply after you click `OK` and restart the connection. The active routing profile can replace some of these settings with its own; see [DNS per routing profile](#route-dns).

In Simple mode, there is no `DNS` tab: `Remote DNS`, `Direct DNS` and `Default DNS server` are in the `Simple DNS Settings` section of the `Common` tab, and the other settings are hidden but keep their values. If `Use Custom DNS Object` is on, the `DNS` tab stays, so that you can turn it off. See [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

| Setting | Default | What it does |
| --- | --- | --- |
| `Remote DNS` | `https://8.8.8.8/dns-query` | The server used through the proxy. The list also offers `tls://8.8.8.8`, `tls://1.1.1.1`, `8.8.8.8` and `1.1.1.1`. |
| `Direct DNS` | `localhost` | The server used without the proxy. `localhost` means your system's resolver. Keep it unless it does not work; then choose a server that works on your network without the proxy. The list offers public resolvers in China (`223.5.5.5`, `119.29.29.29`), Iran (`178.22.122.100`) and Russia (`77.88.8.8`). |
| `Disable IPv6` (next to each server) | off | Answers IPv6 (AAAA) queries sent to that server with an empty result. |
| `Local Override` | empty | A DNS server used to look up the names of your DNS servers. Set an IP address here when your system's resolver cannot be used. |
| `Default DNS server` | `remote` | The server for queries that no other rule handles: `remote` or `direct`. |
| `Enable DNS Routing` | on | Sends queries for domains that go `direct` to Direct DNS. |
| `Respect Hosts File` | off | Answers A and AAAA queries from your system's hosts file first. Only domains listed in the file are affected. |
| `Predefined Answers` | on | Fixed answers in hosts-file syntax. The default entry is `127.0.0.1 localhost`. |
| `Enable FakeIP` | off | See [FakeIP](#fakeip). |
| `FakeIP Disable IPv6` | off | Hands out no fake IPv6 addresses; AAAA queries get an empty answer. |

Since 1.4.0, the lists of `Remote DNS` and `Direct DNS` also offer the last 8 servers that you typed there, newest first, above the built-in examples.

`Remote DNS`, `Direct DNS` and `Local Override` accept these formats:

| Format | Example |
| --- | --- |
| Plain DNS | `8.8.8.8` or `8.8.8.8:53` |
| DNS over TCP | `tcp://8.8.8.8:53` |
| DNS over TLS | `tls://1.1.1.1` |
| DNS over HTTPS | `https://1.1.1.1/dns-query` |
| DNS over HTTP/3 | `h3://dns.example.com/dns-query` |
| DNS over QUIC | `quic://dns.example.com` |
| The server your network hands out | `dhcp://auto` |
| Your system's resolver (for `Direct DNS`) | `localhost` |

When the running profile uses Xray and `Remote DNS` is plain DNS or DNS over QUIC, Throne uses DNS over HTTPS instead. For example, `1.1.1.1` becomes `https://1.1.1.1/dns-query`; a server that Throne does not know becomes `https://8.8.8.8/dns-query`.

`Predefined Answers` opens an editor with one entry per line:

```text
127.0.0.1 localhost
10.0.0.5 nas.lan files.lan
```

A domain that has only an IPv4 address in this list gets "no such domain" for IPv6 queries, and the other way round. This way apps cannot go around your entry.

## FakeIP {#fakeip}

With `Enable FakeIP`, Throne answers address queries at once with a fake address from `198.18.0.0/15` (IPv6: `2001:db8::/32`), without asking a real DNS server. When the app connects to that address, Throne knows which domain it stands for and routes the connection by name. The real lookup happens later: on the proxy server for proxied connections, or with Direct DNS for direct ones.

This has two benefits:

- Connections start faster, because apps do not wait for a real DNS answer.
- No DNS query leaves your device before the connection is routed.

FakeIP only affects queries that reach Throne, so it is useful in TUN mode. It changes nothing for apps in system proxy mode.

Things to know:

- Answers from `Predefined Answers` and the hosts file stay real.
- Apps keep fake addresses in their own caches. After you stop Throne, an app that still holds a fake address cannot connect until it looks the name up again. Restart the app if it stays offline.
- `Advanced Settings` → `Save Cache To File` keeps the fake addresses when Throne restarts.
- Before 1.4.0, the fake IPv6 addresses came from `fc00::/18`. In TUN mode, `Private Range Bypass` sent them past the tunnel, so apps that tried IPv6 first could not connect. Since 1.4.0, they come from `2001:db8::/32`, a range that is never used on the internet.

## Advanced {#advanced}

`Advanced Settings` on the `DNS` tab controls the DNS cache:

| Setting | Default | What it does |
| --- | --- | --- |
| `Cache Capacity` | `65536` | How many answers the cache keeps. |
| `Query Timeout` | `10s` | How long to wait for a DNS server. |
| `Optimistic Cache` | off | Keeps serving an expired answer while it is refreshed in the background. Cannot be combined with `Disable Cache` or `Disable Expire`. |
| `Optimistic Timeout` | `3d` | How long an expired answer may still be served. |
| `Disable Cache` | off | Turns the DNS cache off. |
| `Disable Expire` | off | Keeps cached answers after they expire. |
| `Save Cache To File` | off | Writes cached answers and FakeIP addresses to the core's cache file, so they survive a restart. Each entry costs a disk write. |
| `Reverse Mapping` | off | Remembers which domain an answered IP address belongs to, so domain rules can match connections to that address. |

Durations are a number followed by `ns`, `us`, `ms`, `s`, `m`, `h` or `d`, for example `5s` or `3d`.

`Use Custom DNS Object` replaces everything on this tab with a complete sing-box `dns` object that you write with `Edit DNS Object`. The simple settings and `Advanced Settings` are greyed out while it is on, and features such as DNS routing, FakeIP and predefined answers only work if your object sets them up. In the editor, `Format` tidies the JSON and `Document` shows the address of the [sing-box DNS documentation](https://sing-box.sagernet.org/configuration/dns/).

{% alert_warning() %}
Keep a server with the tag `dns-direct` in a custom DNS object. Throne's routing uses it to look up server addresses.
{% end %}

## DNS per routing profile {#route-dns}

Since 1.4.0, a routing profile can use its own DNS servers. While that profile is the active routing profile, they replace the global ones from the `DNS` tab. This is useful when a profile for one country needs a `Direct DNS` that works there, or when a provider hands out a routing profile together with its DNS servers.

1. Open `Settings` → `Routing Settings` → `Route`, select the profile and click `Edit`. Or right-click the profile at the bottom of the `Routing` menu.
2. Open the `DNS` tab and choose `Use custom DNS settings`.
3. Change the settings, then click `OK`.

The tab has these settings of the [DNS tab](#settings): `Remote DNS`, `Direct DNS`, their `Disable IPv6` boxes, `Default DNS server`, `Enable DNS Routing`, `Enable FakeIP` and `FakeIP Disable IPv6`. When you switch to `Use custom DNS settings` for the first time, they start with the global values. `Use the global DNS settings`, the default, shows the global values greyed out. `Local Override`, `Respect Hosts File`, `Predefined Answers` and `Advanced Settings` always come from the `DNS` tab.

Things to know:

- Choosing another routing profile in the `Routing` menu restarts the connection, so the DNS servers change with it.
- Since 1.4.0, a group can use its own routing profile (see [Routing profile per group](@/guides/routing.md#group-routing)). Then the DNS settings of that routing profile apply while a profile of the group runs. Tests always use the DNS settings of the routing profile chosen in the `Routing` menu, because they can test profiles of several groups.
- A routing profile that a [subscription provides](@/guides/routing.md#subscription-routing) never takes DNS settings from the subscription. Give it its own on its `DNS` tab if it needs them; updates of the subscription keep them.
- A profile with its own DNS settings also replaces a [custom DNS object](#advanced): while it is active, Throne ignores `Use Custom DNS Object`. When a profile follows the global settings and `Use Custom DNS Object` is on, its `DNS` tab says that the object applies.
- Structured, remote and raw routing profiles all have the tab. A remote profile keeps its DNS settings when it updates; Throne never takes them from the URL.
- The profile's `throne://route/` link carries its DNS settings. Before you import such a link with `Import` in `Routing Settings`, Throne names the servers: "It uses its own DNS servers: remote …, direct …". Check them: these servers see every name that you look up while the profile is active. See [Share profiles](@/guides/routing.md#share-profiles).
- In Simple mode, the `DNS` tab of a profile appears only when the profile already uses its own DNS settings.

## DNS server for other devices {#dns-server}

While a profile runs, Throne's core also runs a DNS server. It answers with the DNS settings on this page, including those of the routing profile. Its port is `Settings` → `Basic Settings` → `Miscellaneous` → `Sing-box` → `DNS Server Port`, `5533` by default, for TCP and UDP (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). By default it listens only on `127.0.0.1`, so only programs on this computer can use it.

Since 1.4.0, other devices on your network can use it too, for example a router that sends its DNS queries through this computer:

1. Tick `Allow other devices to connect` below `DNS Server Port` (off by default), then click `OK`.
2. If a profile is running, click `Restart` in the "Settings changed, restart to apply" notice. The DNS server then listens on all network interfaces.
3. Allow incoming TCP and UDP connections to the port in your firewall, as for [LAN sharing](@/guides/proxy_modes.md#lan-sharing). On Linux with ufw, for example: `sudo ufw allow 5533`.
4. On the other device, set the DNS server to your computer's LAN address and the port, for example `192.168.1.20` and `5533`.

Things to know:

- Many phones and computers accept only an address for DNS and always use port 53. The setting suits devices that also let you enter the port, such as many routers.
- Anyone who can reach the port can look up names through your DNS settings, and so through your proxy. Use it only on a network that you trust.
- The server answers only DNS queries. Other connections to the port are refused.
- With `Enable FakeIP`, other devices get fake addresses too. These work only for connections that pass through Throne.
- This setting is separate from `Program` → `Allow other devices to connect`, which shares the proxy port.
- With [thronectl](@/advanced/api.md#settings), for example on [headless Throne](@/headless/_index.md), the setting is `dns_in_lan` and the port is `dns_in_port`.

## DNS leak tests {#leak-tests}

A DNS leak test is a website that shows which DNS servers looked up its test names. The result depends on your mode and your routing profile.

These results are expected:

- **Your Remote DNS provider** (Google with the default setting). Your queries went through Remote DNS and the proxy. This is the normal result for sites you proxy. If the active routing profile uses [its own DNS settings](#route-dns), you see the servers of that profile instead.
- **Your internet provider, for sites that go direct.** Throne looks up domains that go direct with Direct DNS, which is your system's resolver by default and usually belongs to your internet provider. In system proxy mode, or with FakeIP, this also happens for sites that go direct only because the routing profile's default outbound is `direct`, as in the "Proxy … Blocked" profiles. Direct traffic reaches the site without the proxy anyway, so your provider sees it either way.
- **Your internet provider, in system proxy mode.** Browsers and apps can look up names themselves, outside Throne. Use TUN mode if this matters to you.
- **Your browser's own provider,** when the browser uses secure DNS (DNS over HTTPS). Those lookups are ordinary HTTPS connections that follow your routing rules.
- **Your internet provider for most names,** when `Default DNS server` is `direct`.

A real problem looks like this: you use TUN mode and a routing profile that sends the test site through the proxy (for example `Default`), and the test still shows your internet provider. Then some queries go around the tunnel. On Windows, check that `Settings` → `Tun Settings` → `Strict Route` is on (the default on Windows 10 and later), and follow [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route).

WebRTC tests check a different thing: your IP address, not your DNS. A WebRTC test that shows no public IP address is fine ([#1267](https://github.com/throneproj/Throne/issues/1267)). If it shows your real public IP address for a site you proxy, UDP traffic went around the proxy. This is common in system proxy mode; use TUN mode instead.

## Hijack and System DNS (removed) {#deprecated}

Up to 1.3.2, the `Routing Settings` window had a `Hijack` tab, and Windows had a `System DNS` option. Hijack ran a local DNS server that answered chosen domains with fixed addresses, and `System DNS` pointed Windows at that server. Both served apps that ignore the system proxy.

Since 1.4.0, both are gone, and Throne ignores their old settings. Use `Tun Mode` for such apps instead; see [TUN Mode](@/guides/tun_mode.md#enable).

## On Android {#android}

Throne for Android has the same DNS settings in `Settings` → `DNS`. FakeIP is called `Enable FakeDNS` there. In VPN mode, Throne always handles the DNS queries inside the VPN. The differences:

- **`localhost` as Direct DNS.** Since 2.0.1, it asks the DNS servers of the current Wi-Fi or mobile network directly, as on Windows. Android's own resolver is used only when the network names no DNS server. Before, Throne always used Android's resolver, which also applies Private DNS and could stall every lookup. If the network's DNS servers do not answer when you connect, Throne shows "Could not connect: the network's DNS (…) did not answer. Set a working Direct DNS." Its `DNS` button opens the DNS settings.
- **Fake IPv6 addresses** come from `2001:db8::/32` since 2.0.2, as on the desktop.
- **DNS per routing profile** (since 2.0.4). The editor of a routing profile has a `DNS` card with `Use the global DNS settings` and `Use custom DNS settings`, as on the desktop (see [DNS per routing profile](#route-dns)). While the active routing profile uses its own servers, `Settings` → `DNS` says so at the top: the settings there then apply only to other routing profiles.
- **A local DNS server for other apps.** Throne runs a DNS server on `127.0.0.1` that other apps on the device can use. Since 2.0.2, you can change its port in `Settings` → `Inbound` → `DNS server port` (default `5533`).
