+++
title = "Recipes"
description = "Step-by-step setups for common tasks, with links to the pages that explain each feature."
weight = 80
toc = true
+++

## Proxy only blocked sites {#proxy-only-blocked-sites}

**China or Russia:** download the profiles with `Routing` → `Download Profiles`, then choose `Proxy China Blocked`, `Proxy Russia Blocked`, `Proxy Antizapret` or `Proxy Refilter` in the `Routing` menu. See [Download ready-made profiles](@/guides/routing.md#download-profiles).

**Any country:** in `Settings` → `Routing Settings` → `Route`, click `New` → `Structured profile`, set `Default outbound` to `direct` and put the blocked sites in the `Proxy` box on the `Basic` tab, for example:

```text
ruleset:geosite-youtube
ruleset:geosite-telegram
ruleset:geoip-telegram
suffix:example.com
```

Then click `OK` and activate the profile from the `Routing` menu. If a site still does not load, add its other domains from the [Connections tab](@/guides/routing.md#connections-tab) (right-click → `Proxy`). Apps that connect to IP addresses, such as Telegram, also need their `geoip-` rule-set and `Tun Mode`. See [Simple rules](@/guides/routing.md#simple-rules).

## Share the proxy with other devices {#share-with-devices}

For phones, TVs, consoles and other computers on your network:

1. Choose `Program` → `Allow other devices to connect` and restart the running profile.
2. On the other device, set an HTTP or SOCKS5 proxy with the address and port shown after `Mixed:` in the status bar, for example `192.168.1.20:2080`.

Their traffic follows your active routing profile. Set a password and allow `ThroneCore` through the firewall, see [Share the proxy with other devices](@/guides/proxy_modes.md#lan-sharing). On Android, see [LAN sharing](@/android/modes.md#lan-sharing).

## Windows Mobile Hotspot with TUN mode {#hotspot}

If Windows Mobile Hotspot or Internet Connection Sharing shares your normal network adapter while `Tun Mode` is on, TUN mode fails and Throne warns "IPv4 forwarding breaks Tun mode". Share Throne's TUN adapter instead, a workaround also reported in [#1916](https://github.com/throneproj/Throne/issues/1916):

1. Turn on `Tun Mode` and start a profile.
2. In Windows, open `Settings` → `Mobile hotspot` → `Share my internet connection from` and choose `throne-tun`, which exists only while `Tun Mode` is on.
3. Turn on the hotspot.

If this does not work, turn the hotspot off while you use `Tun Mode`, or let the devices use the [proxy port](#share-with-devices).

## Voice calls and games {#calls-and-games}

Voice calls in Discord and Telegram, and many games, use UDP and often ignore the system proxy.

- Use `Tun Mode`, so that this traffic reaches Throne.
- Use a server whose protocol carries UDP; an HTTP proxy cannot, and some servers turn UDP off. If calls fail, try another server.
- To send a game direct, put its program in the `Direct` box of your routing profile, for example `processName:game.exe`.
- For games and peer-to-peer apps that go direct, turn on `L3 Bridge Bypass` in the [TUN settings](@/guides/tun_mode.md#settings) (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

## Docker, WSL and virtual machines {#docker-wsl-vms}

Point them at Throne's proxy port:

1. Turn on `Program` → `Allow other devices to connect`.
2. In the guest, set the proxy to `http://<address>:2080`, with an address of your computer that the guest can reach, such as the one in the status bar. The port also accepts SOCKS5.

Many Linux command-line tools read these variables:

```bash
export http_proxy=http://192.168.1.10:2080
export https_proxy=http://192.168.1.10:2080
```

If you use `Tun Mode` instead and guests lose their connection, see [TUN Mode](@/guides/tun_mode.md#side-effects-vms).

## Corporate VPN side by side {#corporate-vpn}

Keep company traffic away from the proxy in one of these ways.

**Send company traffic direct.** Add the company's domains and networks to the `Direct` box of your routing profile:

```text
suffix:corp.example.com
ip:10.20.0.0/16
```

In TUN mode, private ranges such as `10.0.0.0/8` already bypass the tunnel; for company networks in public ranges, also turn on `Settings` → `Tun Settings` → `Enable Tun Routing`.

**Bind to the VPN's adapter**, if direct traffic leaves through the wrong adapter. With `Program` → `New profile`, create a profile of the type `Direct` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)), for example `Corporate`, and enter the VPN adapter's name in its `Advanced Settings` → `Bind Interface`. Then add an [advanced rule](@/guides/routing.md#advanced-rules) above your other rules that sends the company's `domain_suffix` and `ip_cidr` to `[group] Corporate` with the action `route`.

**Run the VPN inside Throne.** Throne can run an OpenVPN or OpenConnect profile next to your proxy, only for the networks that it announces. See [Split tunnel](@/advanced/vpn_profiles.md#split-tunnel).

If problems appear only in TUN mode, use system proxy mode while the corporate VPN is connected.

## Per-app routing {#per-app}

On the desktop, per-app rules work reliably only in `Tun Mode`.

1. Turn on `Tun Mode`, start the program and let it connect.
2. In the `Connections` tab, right-click one of its connections and choose `Proxy`, `Direct`, `Block` or `Specific server`.
3. Tick the program, for example `Process Telegram.exe`, click `OK`, then click `Restart` in the notice.

To proxy only some programs, set `Default outbound` to `direct` and put them in the `Proxy` box, for example `processName:Telegram.exe`. To proxy all programs except some, put the exceptions in the `Direct` box. See [Connections tab](@/guides/routing.md#connections-tab) and [Simple rules](@/guides/routing.md#simple-rules).

**Android:** choose the apps in `Settings` → `TUN / VPN` → `Apps VPN mode`, or use the `Apps` field of a routing rule. See [Per-app proxy](@/android/modes.md#per-app-proxy).

## Find a working CDN address {#cdn-address}

If your provider's servers are behind a CDN such as Cloudflare, profiles can fail because their CDN addresses are blocked on your network while other addresses of the same CDN work. To find such an address for a whole group (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)):

1. Open `Tools` → `IP Scanner`, click `New scan`, keep `Generic scan` and click `OK`.
2. On the `Targets` tab, choose your CDN's list as `Base IP list`, for example `Cloudflare CDN`, enter your profiles' port in `Ports` (usually `443`) and set `Stop after (results)` to a small number such as `20`.
3. On the `Phases` tab, tick `Config test` and choose one of the group's profiles as `Profile`.
4. Click `Start`. When the scan completes, click `Save as IP list` on the `Results` tab and enter a name.
5. In the group's options, click `Advanced`, set `Server Address` to `IP list`, choose the new list and click `OK` twice.
6. Run a URL test of the group.

This works only for profiles whose server really is behind that CDN, usually WebSocket, gRPC, HTTPUpgrade or XHTTP over TLS with a domain name. See [Server address](@/guides/subscriptions.md#server-address).
