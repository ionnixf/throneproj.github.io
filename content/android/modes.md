+++
title = "VPN & Proxy Modes"
description = "Choose VPN or Proxy only mode, pick which apps use Throne, share the proxy with other devices, and adjust the TUN and inbound settings."
weight = 30
toc = true
+++

## Choose a service mode {#service-mode}

Set the mode in `Settings` → `General` → `Service mode`. Changing it stops a running connection; tap the connect button to start again.

| Mode | What it does | Use it when |
| --- | --- | --- |
| `VPN` (default) | Android sends the traffic of all apps, or of the apps you choose, to Throne through a virtual network interface (TUN). | You want every app to use Throne. This is the normal choice. |
| `Proxy only` | Throne only opens a local SOCKS5 and HTTP proxy on `127.0.0.1`, port `2080` by default. No VPN is created. | Another VPN app must stay connected, you only need Throne in apps that have proxy settings, or your device cannot grant VPN access. |

If you change a setting on this page while Throne is connected, tap `Apply` when Throne shows "Reload proxy service to apply changes", or reconnect.

## VPN mode {#vpn-mode}

In VPN mode, Throne sends each connection of your apps through a proxy, sends it directly or blocks it, as your routing profile says.

- Android runs only one VPN at a time. Connecting Throne disconnects the VPN of any other app.
- Devices on your phone's hotspot do not use the VPN. To give them the proxy, use [LAN sharing](#lan-sharing).
- To keep some apps out of the VPN, or to send only some apps through it, use [per-app proxy](#per-app-proxy).
- If Throne is the always-on VPN, Android starts it in VPN mode. See [Always-on VPN](@/android/permissions.md#always-on).

### HTTP proxy of the VPN {#http-proxy}

On Android 10 and newer, `Settings` → `Inbound` → `Append HTTP proxy to VPN` (off by default) also sets Throne's local proxy as the HTTP proxy of the VPN network. Apps that follow the system proxy settings, such as most browsers, then send their web traffic to this proxy instead of through the VPN interface. This traffic still follows your routing profile.

The setting is not available while `Enable authorization` is on, because Android cannot pass the username and password to apps. Domains in `HTTP proxy bypass list`, one per line such as `*example.com`, connect without this proxy.

### Apps that bypass the VPN {#allow-bypass}

`Settings` → `General` → `Allow apps to bypass VPN` (off by default) lets apps that ask for it connect outside the VPN, for example Google push notifications (FCM). These connections go out directly and do not follow your routing profile. Android ignores the setting while Always-on VPN blocks connections without VPN.

## Proxy only mode {#proxy-only}

In `Proxy only` mode, Throne runs a local proxy and nothing else. Android does not ask for VPN permission, and only apps that you point at the proxy use Throne. The TUN settings and per-app proxy have no effect.

1. Set `Settings` → `General` → `Service mode` to `Proxy only` and connect.
2. In the app that should use Throne, set a SOCKS5 or HTTP proxy with server `127.0.0.1` and port `2080` (or the `Proxy port` you set). If `Enable authorization` is on, also enter the username and password.

In this mode, Throne's own requests, such as subscription updates, go through the local proxy while a profile runs, and directly while none runs, unless `Settings` → `Subscriptions` → `Use proxy` is on.

## Per-app proxy {#per-app-proxy}

Per-app proxy decides which apps use the VPN. It works only in VPN mode.

1. Turn on `Settings` → `TUN / VPN` → `Apps VPN mode`. The app list opens.
2. Choose the mode at the top: `Proxy` sends only the selected apps through the VPN, and `Bypass` keeps the selected apps out of it.
3. Tap the apps to select them. `Search…` finds an app by name or package name. Apps that share a user ID are selected together.

If no app is selected, all apps use the VPN. In `Proxy` mode, if none of the selected apps is installed, no app uses it. To turn per-app proxy off, turn off the `Apps VPN mode` switch, or choose `Off` in the list; Throne keeps your selection for the next time. If the list stays empty, see [Installed apps](@/android/permissions.md#installed-apps).

`Auto select proxy apps` selects apps from a built-in list of about 390 apps that often need a proxy, such as Google apps, YouTube, Instagram and Netflix, so that these apps use the VPN and other apps do not. It replaces your selection of installed apps.

`Add package name…` adds apps that the list cannot show. `Export to clipboard` and `Import from clipboard` copy the mode and the selection as plain text: the first line is `true` for `Bypass` or `false` for `Proxy`, then one package name per line.

```text
false
com.google.android.youtube
org.telegram.messenger
```

Without the `true` or `false` line, an import keeps the current mode and replaces only the selection. If a line is not a package name, Throne refuses the whole list.

To send an app to a specific outbound, use a routing rule with the `Apps` condition instead. See [Routing on Android](@/guides/routing.md#android). Such a rule only sees apps that use the VPN: if per-app proxy keeps an app out, its rule never matches. In the app list of a routing rule, ⋮ → `Copy to Apps VPN mode` adds these apps to per-app proxy and explains its choices before it changes anything.

## LAN sharing {#lan-sharing}

LAN sharing lets other devices on your network, or on your phone's hotspot, use Throne on your phone as a proxy. It works in both service modes.

1. Open `Settings` → `Inbound` and turn on `Allow connections from the LAN`.
2. Turn on `Enable authorization` and set a `Username` and a `Password`.
3. Note the `Proxy port` (`2080` by default).
4. Find the IP address of the phone in the Android Wi-Fi settings, in the details of the connected network. Devices on your hotspot use the router (gateway) address that they show for their connection.
5. On the other device, set a SOCKS5 or HTTP proxy with that address, the port, the username and the password.

{% alert_warning() %}
Without authorization, any device on the same network can use your proxy.
{% end %}

## TUN / VPN settings {#tun-settings}

These settings are in `Settings` → `TUN / VPN`. They apply only in VPN mode.

| Setting | Default | What it does |
| --- | --- | --- |
| `MTU` | `9000` | The largest packet size of the VPN interface. If some apps or websites fail while others work, set `1500` and reconnect. Long-press the setting to enter any value from 1000 to 10000. |
| `Tun IPv6` | Off | Routes IPv6 through the VPN interface. When it is off, IPv6 connections fail at once, so apps use IPv4. Turn it on only if your server supports IPv6. |
| `Tun routing` | Off | Keeps the IP ranges that your routing profile sends `direct` outside the VPN interface. |
| `Bypass private ranges` | On | Keeps the `Private ranges` (local networks) outside the VPN interface, so devices on your local network are reached directly. |
| `IPv4 CIDR`, `IPv6 CIDR` | `172.19.0.1/24`, `fdfe:dcba:9876::1/96` | The addresses of the VPN interface. |

If a routing rule sends addresses in a private range to an outbound other than `direct`, or rejects them, Throne keeps that range inside the VPN interface so that the rule works. Strict route is always on.

Restoring the settings of a desktop backup also restores the desktop MTU, which is `1500` unless you changed it.

## Inbound settings {#inbound-settings}

`Settings` → `Inbound` controls Throne's local proxy, the mixed inbound, which accepts SOCKS5 and HTTP on one port.

| Setting | Default | What it does |
| --- | --- | --- |
| `Disable mixed inbound` | Off | In VPN mode, Throne does not open the local proxy. This also removes the [HTTP proxy of the VPN](#http-proxy) and LAN sharing. No effect in `Proxy only` mode. |
| `Proxy port` | `2080` | The port of the local proxy. In a work profile or another Android user, the default is 2080 plus the number of that user. |
| `Enable authorization` | Off | Every client must send the `Username` and `Password`. |
| `DNS server port` | `5533` | The port of Throne's local DNS server on `127.0.0.1`. Choose another port if another app or Android user already uses it. See [DNS on Android](@/guides/dns.md#android). |
| `Custom inbound` | `{"inbounds": []}` | Extra sing-box inbounds as JSON, added to the configuration unchanged. |
