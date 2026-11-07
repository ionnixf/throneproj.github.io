+++
title = "Protocols & Import Formats"
description = "Profile types, the links and files Throne imports, and how to share a profile."
weight = 10
toc = true
+++

## Profile types {#protocols}

Create a profile with `Program` → `New profile` (`Ctrl+N`), or on Android with `Add profile` → `Manual settings`. You cannot change the type of an existing profile. `Direct`, `Chain Proxy`, `Extra Core` and the `Custom` types are Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

| Type | Notes |
|---|---|
| `Auto Selector` | Picks the best working profile of a group and switches automatically, see [Auto Selector](@/guides/testing.md#auto-selector). |
| `Socks` | SOCKS version 5 or 4. |
| `HTTP` | Optionally over TLS (HTTPS). |
| `Shadowsocks` | With the 2022 ciphers and the `obfs-local` and `v2ray-plugin` plugins. |
| `VMess` | |
| `Trojan` | |
| `VLESS` | sing-box implementation, with REALITY. |
| `VLESS (Xray)` | Xray implementation: XHTTP, REALITY, VLESS encryption, Finalmask, the [XDRIVE](@/advanced/xray.md#xdrive) transport and mKCP with [XDNS](@/advanced/dns_tunnels.md#xdns). |
| `Hysteria` | Hysteria 1 and 2 in one type: choose the `Protocol Version`. Supports port hopping and obfuscation. With `Up Mbps` or `Down Mbps` empty, Hysteria 1 uses 10 Mbps up and 50 Mbps down, so set your real line speed. |
| `TUIC` | |
| `Juicity` | |
| `Naive` | NaïveProxy over HTTPS or QUIC. On Android: `Naïve`. |
| `TrustTunnel` | |
| `AnyTLS` | |
| `Mieru` | |
| `Snell` | Snell version 4 or 6. |
| `ShadowTLS` | |
| `WireGuard` | For AmneziaWG, tick `Enable Amnezia` (Android: `Enable AmneziaWG`). `Amnezia Options` → `Generate QUIC Signature...` makes the handshake look like HTTP/3 to a domain you choose. Can create a Cloudflare WARP account, see [WARP](@/advanced/warp.md#generate). |
| `MASQUE` | HTTP/3 or HTTP/2. Can create a Cloudflare WARP identity. |
| `OpenVPN` | Imports `.ovpn` files. One-time passwords (OTP) work only on the desktop. See [OpenVPN](@/advanced/vpn_profiles.md#openvpn). |
| `OpenConnect` | AnyConnect, GlobalProtect, Fortinet, F5, Pulse and Juniper (`nc`) servers. OTP works only on the desktop. See [OpenConnect](@/advanced/vpn_profiles.md#openconnect). |
| `Tailscale` | Desktop only. Joins your Tailscale network and can use an exit node, see [Tailscale](@/advanced/vpn_profiles.md#tailscale). |
| `SSH` | |
| `DNSTT / VayDNS` | DNS tunnel to a dnstt or VayDNS server, see [DNSTT and VayDNS](@/advanced/dns_tunnels.md#dnstt). |
| `MasterDNS` | DNS tunnel to a MasterDnsVPN or StormDNS server, see [MasterDNS](@/advanced/dns_tunnels.md#masterdns). |
| `Direct` | No proxy. With a bind interface, it sends traffic out through that network adapter. |
| `Custom (sing-box outbound)`, `Custom (sing-box config)` | Your own sing-box JSON: one outbound, or a complete config that runs unchanged. On Android: `Custom config`. See [Custom configs](@/advanced/chains.md#custom-config). |
| `Custom (Xray outbound)`, `Custom (Xray config)` | The same for Xray JSON. |
| `Extra Core` | Desktop only. Runs an external program you choose and connects to its local SOCKS port, see [Extra Core](@/advanced/chains.md#extra-core). |
| `Chain Proxy` | Sends traffic through several profiles in a row. On Android: `Proxy chain`. See [Chains](@/advanced/chains.md#chains). |

`AnyTLS`, `Hysteria`, `Juicity`, `MASQUE`, `ShadowTLS`, `TrustTunnel` and `TUIC` always use TLS, so their editors have no switch to turn it off.

### Which engine runs a profile {#engines}

Every type runs in sing-box, except `VLESS (Xray)`, `Custom (Xray outbound)` and `Custom (Xray config)`, which run in the Xray engine built into the core (XDNS too, as it is a `VLESS (Xray)` profile), and `Extra Core`, which runs the program you choose. Throne for Android uses the same core.

`Settings` → `Basic Settings` → `Core` → `Xray VLESS Preference` (Advanced mode only) decides whether an imported `vless://` link becomes `VLESS` or `VLESS (Xray)`. Links that need a feature only Xray has, such as VLESS encryption or mKCP (`type=kcp`), always become `VLESS (Xray)`. See [sing-box vs Xray](@/advanced/xray.md#vless-preference).

## Import formats {#import-formats}

### Where to import {#where-to-import}

| Source | Desktop | Android |
|---|---|---|
| Clipboard | `Ctrl+V`, or `Program` → `Add profile from clipboard` | `Add profile` → `Import from clipboard` |
| Files | `Program` → `Add profile from File(s)` (`Ctrl+O`), or drag files onto the main window | `Add profile` → `Import from file` |
| QR codes | `Program` → `Scan QR Code` (`Ctrl+Shift+Q`) reads QR codes on your screens. QR images also work as files. | `Add profile` → `Scan QR code` (camera, or `Select image`) |
| Subscriptions | A group of type `Subscription`, see [Subscriptions & Groups](@/guides/subscriptions.md#add-subscription) | The same, in `Groups` |
| Links | `throne://` links, see [Deep Links](@/advanced/deeplinks.md) | `throne://` links and protocol links that you open in other apps |

Imported profiles go into the current group. If the clipboard holds a single `http://` or `https://` URL, Throne asks whether to add it as a subscription group, import it once into the current group, or use it as an HTTP proxy.

### Supported formats {#formats}

Both apps read these formats in pasted text, files, QR codes and subscription responses.

| Format | Result |
|---|---|
| Share links, one per line: `vless://`, `vmess://`, `ss://`, `trojan://`, `socks://`, `socks4://`, `socks4a://`, `socks5://`, `http://`, `https://`, `hysteria://`, `hysteria2://`, `hy2://`, `tuic://`, `juicity://`, `anytls://`, `mieru://`, `mierus://`, `snell://`, `tt://` (TrustTunnel), `shadowtls://`, `wg://`, `wireguard://`, `awg://`, `ssh://`, `naive+https://`, `naive+quic://` | One profile per link |
| Throne links: `throne://add/…` and the older `json://…` | One profile of any type |
| AmneziaVPN links: `vpn://…` | The configs inside the link |
| DNS tunnel links: `dnst://`, `slipnet://`, `masterdns://`, `stormdns://` | One `DNSTT / VayDNS` or `MasterDNS` profile per link, see [DNS Tunnels](@/advanced/dns_tunnels.md#import) |
| sing-box JSON: a full config, or an array of outbounds | One profile for each supported outbound, including `dnstt` and `masterdns` |
| sing-box JSON: one outbound object | One `Custom (sing-box outbound)` profile |
| Xray JSON: a config, an array of outbounds, or one outbound | `VLESS (Xray)` for VLESS outbounds, `Custom (Xray outbound)` for the others |
| Xray JSON: an array of complete configs | One `Custom (Xray config)` profile for each config |
| SIP008 JSON (Shadowsocks) | `Shadowsocks` profiles |
| Clash or Mihomo YAML with a `proxies:` list | Profiles for the `socks5`, `http`, `ss`, `vmess`, `vless`, `trojan`, `anytls`, `snell`, `hysteria`, `hysteria2`, `tuic`, `masque` and `ssh` entries |
| WireGuard or AmneziaWG `.conf` file | One `WireGuard` profile, named after a `# Name =` or `# remarks:` comment, or the comment above `[Peer]` |
| OpenVPN `.ovpn` file | One `OpenVPN` profile |
| AnyConnect XML profile, an `openconnect` command line, or a file with a `protocol=` line | `OpenConnect` profiles |
| Any of the above, encoded in Base64 | Decoded first, then imported |

`tt://` covers both kinds of TrustTunnel links: `tt://user:password@server:port?…` and the `tt://?…` deep link (versions 1 and 2) of the TrustTunnel apps. The link must contain the server address, server name, username and password, so a link with only a subscription address is refused. If it lists several server addresses, Throne imports the first and warns about the others.

Entries that are not proxy servers are skipped, such as sing-box `direct`, `block`, `selector` and `urltest` or Xray `freedom` and `blackhole` outbounds. To run a complete sing-box or Xray config, paste it into a `Custom (sing-box config)` or `Custom (Xray config)` profile, see [Full configs](@/advanced/chains.md#full-configs).

Import files over 50 MB (on Android, `.zip` files have no limit) and subscription responses over 64 MB are refused.

### Values that cannot be used {#unusable-values}

These values do not stop the import. Throne leaves the value out, imports the rest of the profile and lists a warning in the [change report](@/guides/subscriptions.md#change-report):

- a `flow` the `VLESS` or `VLESS (Xray)` profile cannot use, for example Vision on a transport without Vision support;
- an invalid XHTTP setting (an invalid XHTTP mode becomes `auto`);
- an invalid REALITY `spx` (spider X);
- an invalid Mieru port or port range.

### Android differences {#android-import}

- A subscription URL can also be a `content://` address of a file on the device.
- `Import from file` also takes a `.zip` file and imports every file in it, for example several WireGuard or OpenVPN configs.
- `clash://install-config?url=…` links open Throne and add the URL as a subscription.
- When you tap a protocol link (`vless://…`) or a `throne://` link in another app, Throne opens and asks before it imports the profile.
- The Android share sheet offers Throne only for binary files, such as backups. Copy links from chat apps and use `Import from clipboard`; open other files with `Import from file`.
- `Tailscale` and `Extra Core` profiles cannot be used on Android.

## Share links {#share-links}

### Link types {#link-types}

| Link | Looks like | Who can read it |
|---|---|---|
| Standard link | `vless://…`, `ss://…`, `hysteria2://…` and so on | Throne and most other clients |
| Throne link | `throne://add/…` | Throne desktop and Throne for Android |
| Old Throne link | `json://…` | Throne, for import only |

A **standard link** uses the protocol's usual link format. `Auto Selector`, `Chain Proxy`, the `Custom` types, `Direct`, `Extra Core`, `MASQUE`, `OpenVPN`, `OpenConnect` and `VLESS (Xray)` with XDRIVE have none, so Throne usually gives the Throne link instead. `Tailscale` shows a `ts://` link that Throne cannot import, so share its Throne link. `DNSTT / VayDNS` and `MasterDNS` give a `dnst://` link, see [DNS Tunnels](@/advanced/dns_tunnels.md#import).

A **Throne link** holds the profile's settings as Base64-encoded JSON. It works for every type, but only Throne can read it, see [Deep Links](@/advanced/deeplinks.md#add). `json://` is its older format, which Throne still imports.

A `Chain Proxy` or `Auto Selector` link stores only the internal numbers of the profiles or group it uses, so it works only in the same Throne installation.

### ECH and certificate pins in links {#ech-links}

Throne reads and writes these TLS parameters in standard links:

| Parameter | Meaning |
|---|---|
| `ech=1` or `ech=true` | Turns ECH on. The core looks up the ECH configuration in DNS. |
| `ech=` a domain, or a domain, `+` and a DNS server URL | Turns ECH on and names the domain to look up, and optionally the DNS server to ask, for example `cloudflare-ech.com+https://1.1.1.1/dns-query`. It fills `ECH Query Name / Resolver`. |
| `ech=` any other value | Turns ECH on with this Base64 ECH configuration. |
| `pcs=` | SHA-256 hashes of the server certificate, in hex and separated by commas. A matching certificate is accepted instead of the usual CA check. It fills `Certificate SHA256`. |

For `VLESS (Xray)` profiles, the `ech` value goes unchanged into `ECH Config List`. Links that Throne creates also keep the older `ech_enabled`, `ech_config` and `ech_server_name` parameters. For what ECH does, see [ECH](@/advanced/presets.md#ech).

### Share on the desktop {#share-desktop}

Right-click a profile and open `Share`:

| Action | What you get |
|---|---|
| `QR Code and link` | The standard link and its QR code. Tick `Deep Link` for the Throne link. |
| `Copy links of selected` (`Ctrl+C`) | The standard links of the selected profiles, one per line. |
| `Copy links of selected (Deep Links)` (`Ctrl+Alt+C`) | The Throne links of the selected profiles. |
| `Export config` (`Ctrl+E`) | The core config Throne generates for the profile, in a `sing-box` tab and, for profiles that use Xray, an `Xray` tab. A config for other clients, not a share link. |

For the links of a whole group, use `Groups` → `Edit current Group` → `Copy profile share links` or `Copy profile share links (Deep Links)`. `Copy links of selected (Deep Links)` and `Export config` are Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

### Share on Android {#share-android}

Tap the share button of a profile, or ⋮ → `Share` on double-column cards and `Compact profile rows`:

- `QR code` → `Standard` or `Throne link`
- `Export to clipboard` → `Standard` or `Throne link`
- `Configuration` → `Export to clipboard` or `Export to file`: the core config Throne generates, not a share link

`Standard` is missing when the type has no standard link, and a chain offers only `Configuration`. For the links of a whole group, open `Groups`, tap `More options` on the group and choose `Copy profile share links` or `Copy profile share links (deep links)`.
