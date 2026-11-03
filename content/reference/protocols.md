+++
title = "Protocols & Import Formats"
description = "Profile types Throne supports on desktop and Android, the links and files it can import, and how to share a profile as a link or QR code."
weight = 10
toc = true
+++

This page lists every profile type in Throne desktop and Throne for Android, the links and files Throne can import, and the ways to share a profile. Use it to check whether Throne supports your server before you import it.

## Profile types {#protocols}

To create a profile by hand, use `Program` → `New profile` (`Ctrl+N`) on the desktop, or `Add profile` → `Manual settings` on Android, and choose the type. You cannot change the type of an existing profile. In Simple mode, the desktop list leaves out `Direct`, `Chain Proxy`, `Extra Core` and the `Custom` types (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

| Type | Desktop | Android | Notes |
|---|---|---|---|
| `Auto Selector` | Yes | Yes | Picks the best working profile of a group and switches automatically. See [Auto Selector](@/guides/testing.md#auto-selector). |
| `Socks` | Yes | Yes | SOCKS version 5 or 4. |
| `HTTP` | Yes | Yes | HTTP proxy, optionally over TLS (HTTPS). |
| `Shadowsocks` | Yes | Yes | Includes the 2022 ciphers and the `obfs-local` and `v2ray-plugin` plugins. |
| `VMess` | Yes | Yes | |
| `Trojan` | Yes | Yes | |
| `VLESS` | Yes | Yes | sing-box implementation, with REALITY. |
| `VLESS (Xray)` | Yes | Yes | Xray implementation: XHTTP, REALITY, VLESS encryption, Finalmask, the [XDRIVE](@/advanced/xray.md#xdrive) transport (desktop since 1.4.0, Android since 2.0.4), and the mKCP transport with [XDNS](@/advanced/dns_tunnels.md#xdns) (desktop since 1.4.0). See [sing-box vs Xray](@/advanced/xray.md#vless-preference). |
| `Hysteria` | Yes | Yes | Hysteria 1 and Hysteria 2 in one type: choose the `Protocol Version`. Supports port hopping and obfuscation. Since 1.4.0 (Android: since 2.0.5), Hysteria 1 uses 10 Mbps up and 50 Mbps down when `Up Mbps` or `Down Mbps` is empty. Set your real line speed for the best results. |
| `TUIC` | Yes | Yes | |
| `Juicity` | Yes | Yes | |
| `Naive` | Yes | Yes | NaïveProxy over HTTPS or QUIC. On Android: `Naïve`. |
| `TrustTunnel` | Yes | Yes | |
| `AnyTLS` | Yes | Yes | |
| `Mieru` | Yes | Yes | |
| `Snell` | Yes | Yes | Snell version 4 or 6. |
| `ShadowTLS` | Yes | Yes | |
| `WireGuard` | Yes | Yes | For AmneziaWG, tick `Enable Amnezia` (Android: `Enable AmneziaWG`). Since 1.4.0 (Android: since 2.0.3), `Amnezia Options` → `Generate QUIC Signature...` (Android: `Generate QUIC signature…`) fills the signature packets I1–I5 so that the handshake looks like an HTTP/3 connection to a domain you choose. Can create a Cloudflare WARP account, see [WARP](@/advanced/warp.md#generate). |
| `MASQUE` | Yes | Yes | HTTP/3 or HTTP/2. Can create a Cloudflare WARP identity. |
| `OpenVPN` | Yes | Yes | Imports `.ovpn` files. One-time passwords (OTP) work only on the desktop. See [OpenVPN](@/advanced/vpn_profiles.md#openvpn). |
| `OpenConnect` | Yes | Yes | AnyConnect, GlobalProtect, Fortinet, F5, Pulse and Juniper (`nc`) servers. OTP works only on the desktop. See [OpenConnect](@/advanced/vpn_profiles.md#openconnect). |
| `Tailscale` | Yes | No | Joins your Tailscale network and can use an exit node. See [Tailscale](@/advanced/vpn_profiles.md#tailscale). |
| `SSH` | Yes | Yes | |
| `DNSTT / VayDNS` | Yes | No | DNS tunnel to a dnstt or VayDNS server, since 1.4.0. See [DNSTT and VayDNS](@/advanced/dns_tunnels.md#dnstt). |
| `MasterDNS` | Yes | No | DNS tunnel to a MasterDnsVPN or StormDNS server, since 1.4.0. See [MasterDNS](@/advanced/dns_tunnels.md#masterdns). |
| `Direct` | Yes | Yes | No proxy. Set a bind interface in the profile to send traffic out through that network adapter. |
| `Custom (sing-box outbound)`, `Custom (sing-box config)` | Yes | Yes | Your own sing-box JSON: one outbound, or a complete config that runs unchanged. On Android: `Custom config`. See [Custom configs](@/advanced/chains.md#custom-config). |
| `Custom (Xray outbound)`, `Custom (Xray config)` | Yes | Yes | The same for Xray JSON. |
| `Extra Core` | Yes | No | Runs an external program that you choose and connects to it through its local SOCKS port. See [Extra Core](@/advanced/chains.md#extra-core). |
| `Chain Proxy` | Yes | Yes | Sends traffic through several profiles in a row. On Android: `Proxy chain`. See [Chains](@/advanced/chains.md#chains). |

Some Android labels differ only in capitalization, for example `SOCKS` and `Auto selector`.

`AnyTLS`, `Hysteria`, `Juicity`, `MASQUE`, `ShadowTLS`, `TrustTunnel` and `TUIC` always use TLS, so their editors have no switch to turn TLS off. In Throne for Android up to 2.0.3, a profile of these types that you created by hand lost its TLS settings, such as the SNI, when you saved it. Since 2.0.4, the editors keep them.

### Which engine runs a profile {#engines}

ThroneCore, the core of Throne, runs every profile type with sing-box, with two exceptions:

- `VLESS (Xray)`, `Custom (Xray outbound)` and `Custom (Xray config)` run in the Xray engine that is built into the core.
- `Extra Core` runs the external program that you choose.

The DNS tunnel types `DNSTT / VayDNS` and `MasterDNS` run in sing-box. XDNS is a `VLESS (Xray)` profile, so it runs in Xray. See [DNS Tunnels](@/advanced/dns_tunnels.md#how-it-works).

Throne for Android uses the same core, so the same rules apply there. A chain can mix both engines, but only in this order: sing-box hops, then Xray hops, then sing-box hops.

When you import a `vless://` link, `Settings` → `Basic Settings` → `Core` → `Xray VLESS Preference` (Advanced mode only) decides which type it becomes. With the default, `XHTTP And Reality`, links that use XHTTP or REALITY become `VLESS (Xray)`, and the others become `VLESS`. Links that need a feature only Xray has, such as VLESS encryption or, since 1.4.0, the mKCP transport (`type=kcp`), always become `VLESS (Xray)`. Since 1.4.0, VLESS entries with REALITY in Clash YAML follow the same setting. See [sing-box vs Xray](@/advanced/xray.md#vless-preference).

## Import formats {#import-formats}

### Where to import {#where-to-import}

| Source | Desktop | Android |
|---|---|---|
| Clipboard | `Ctrl+V`, or `Program` → `Add profile from clipboard` | `Add profile` → `Import from clipboard` |
| Files | `Program` → `Add profile from File(s)` (`Ctrl+O`), or drag files onto the main window | `Add profile` → `Import from file` |
| QR codes | `Program` → `Scan QR Code` (`Ctrl+Shift+Q`) reads QR codes on your screens. QR images also work as files. | `Add profile` → `Scan QR code` (camera, or `Select image`) |
| Subscriptions | A group of type `Subscription`, see [Subscriptions & Groups](@/guides/subscriptions.md#add-subscription) | The same, in `Groups` |
| Links | `throne://` links, see [Deep Links](@/advanced/deeplinks.md) | `throne://` links and protocol links that you open in other apps |

Imported profiles go into the current group. If the clipboard holds a single `http://` or `https://` URL, Throne asks how to use it: as a new subscription group, as a one-time import into the current group, or as an HTTP proxy.

### Supported formats {#formats}

Throne desktop and Throne for Android read the same formats in pasted text, files, QR codes and subscription responses.

| Format | Result |
|---|---|
| Share links, one per line: `vless://`, `vmess://`, `ss://`, `trojan://`, `socks://`, `socks4://`, `socks4a://`, `socks5://`, `http://`, `https://`, `hysteria://`, `hysteria2://`, `hy2://`, `tuic://`, `juicity://`, `anytls://`, `mieru://`, `mierus://`, `snell://`, `tt://` (TrustTunnel), `shadowtls://`, `wg://`, `wireguard://`, `awg://` (desktop, since 1.4.0), `ssh://`, `naive+https://`, `naive+quic://` | One profile per link |
| Throne links: `throne://add/…` and the older `json://…` | One profile of any type |
| AmneziaVPN links: `vpn://…` | The configs inside the link |
| DNS tunnel links: `dnst://`, `slipnet://`, `masterdns://`, `stormdns://` (desktop, since 1.4.0) | One `DNSTT / VayDNS` or `MasterDNS` profile per link, see [DNS Tunnels](@/advanced/dns_tunnels.md#import) |
| sing-box JSON: a full config, or an array of outbounds | One profile for each supported outbound, on the desktop also `dnstt` and `masterdns` (since 1.4.0) |
| sing-box JSON: one outbound object | One `Custom (sing-box outbound)` profile |
| Xray JSON: a config, an array of outbounds, or one outbound | `VLESS (Xray)` for VLESS outbounds, `Custom (Xray outbound)` for the others |
| Xray JSON: an array of complete configs | One `Custom (Xray config)` profile for each config |
| SIP008 JSON (Shadowsocks) | `Shadowsocks` profiles |
| Clash or Mihomo YAML with a `proxies:` list | Profiles for the `socks5`, `http`, `ss`, `vmess`, `vless`, `trojan`, `anytls`, `snell`, `hysteria`, `hysteria2`, `tuic`, `masque` and `ssh` entries |
| WireGuard or AmneziaWG `.conf` file | One `WireGuard` profile, named after a `# Name =` or `# remarks:` comment, or after the comment above `[Peer]` (desktop since 1.4.0, Android since 2.0.2) |
| OpenVPN `.ovpn` file | One `OpenVPN` profile |
| AnyConnect XML profile, an `openconnect` command line, or a file with a `protocol=` line | `OpenConnect` profiles |
| Any of the above, encoded in Base64 | Decoded first, then imported |

`tt://` covers both kinds of TrustTunnel links: `tt://user:password@server:port?…`, and the `tt://?…` deep link that the TrustTunnel apps create. Throne reads version 1 of the deep link, and since 1.4.0 (Android: since 2.0.4) also version 2. The link must contain the server address, the server name, the username and the password, so a link that carries only a subscription address is refused. If the link lists several server addresses, Throne imports the first one and warns about the others.

Entries that are not proxy servers are skipped, for example sing-box `direct`, `block`, `selector` and `urltest` outbounds, or Xray `freedom` and `blackhole` outbounds. To run a complete sing-box or Xray config, create a `Custom (sing-box config)` or `Custom (Xray config)` profile and paste the config into it. See [Full configs](@/advanced/chains.md#full-configs).

Both apps refuse import files larger than 50 MB (on Android, `.zip` files are not limited) and subscription responses larger than 64 MB.

### Values that cannot be used {#unusable-values}

Since 1.4.0 (Android: since 2.0.5), some values that Throne cannot use no longer stop the import of a profile. Throne leaves the value out, imports the rest, and lists a warning in the [change report](@/guides/subscriptions.md#change-report):

- A `flow` that the `VLESS` or `VLESS (Xray)` profile cannot use, for example Vision on a transport that does not support it.
- An invalid XHTTP setting. An invalid XHTTP mode becomes `auto`.
- An invalid REALITY `spx` (spider X).
- An invalid Mieru port or port range.

In the same versions, Throne also reads these values:

- `upmbps` and `downmbps` in Hysteria links with a unit, for example `55Mbps`.
- Several WireGuard `local_address` entries separated by commas.
- An empty `encryption` in `VLESS (Xray)` links, which means `none`.

Since 1.4.0, a Hysteria 2 link, JSON or Clash entry with the obfuscation type `none`, `plain` or empty imports without obfuscation. In the editor, set `Obfuscation` to `None` for the same result.

### Android differences {#android-import}

- A subscription URL can also be a `content://` address that points to a file on the device.
- `Import from file` also accepts a `.zip` file and imports every file inside it, for example several WireGuard or OpenVPN configs.
- Links that start with `clash://install-config?url=…` open Throne and add the URL as a subscription.
- When you tap a protocol link (for example `vless://…`) or a `throne://` link in another app, Throne opens and asks before it imports the profile.
- The Android share sheet offers Throne only for binary files (`application/octet-stream`), such as backups. To import a link from a chat app, copy it and use `Import from clipboard`. For other files, use `Import from file`.
- `Tailscale`, `Extra Core`, `DNSTT / VayDNS` and `MasterDNS` profiles cannot be used on Android, and it does not import DNS tunnel links.
- Since 2.0.2, Throne for Android skips entries that its core cannot run, for example XHTTP on a profile type other than VLESS, when it imports or updates. The log lists each one as `Skipped`, with the type, the name and the reason.

## Share links {#share-links}

### Link types {#link-types}

| Link | Looks like | Who can read it |
|---|---|---|
| Standard link | `vless://…`, `ss://…`, `hysteria2://…` and so on | Throne and most other clients |
| Throne link | `throne://add/…` | Throne desktop and Throne for Android |
| Old Throne link | `json://…` | Throne, for import only |

A **standard link** uses the usual link format of the protocol. Some types have no standard link: `Auto Selector`, `Chain Proxy`, the `Custom` types, `Direct`, `Extra Core`, `MASQUE`, `OpenVPN` and `OpenConnect`. Neither do `VLESS (Xray)` profiles that use the XDRIVE transport. For these, Throne gives you the Throne link instead. If such a profile has advanced connection options, such as a bind interface, Throne shows a partial link that contains only those options instead of the Throne link. For these profiles, tick `Deep Link` or use `Copy links of selected (Deep Links)`. For `Tailscale`, share the Throne link too: Throne cannot import the `ts://` link that it shows for these profiles. `DNSTT / VayDNS` and `MasterDNS` profiles give a `dnst://` link, see [DNS Tunnels](@/advanced/dns_tunnels.md#import).

A **Throne link** holds the settings of the profile as Base64-encoded JSON. It works for every profile type, including the types without a standard link, but only Throne can read it. See [Deep Links](@/advanced/deeplinks.md#add). `json://` links are the Throne link format used before 1.2.0; Throne still imports them.

A `Chain Proxy` or `Auto Selector` link stores only the internal numbers of the profiles or the group it uses, not the servers themselves. It is useful only in the same Throne installation.

### ECH and certificate pins in links {#ech-links}

Since desktop 1.4.0 and Android 2.0.2, Throne reads and writes these TLS parameters in standard links:

| Parameter | Meaning |
|---|---|
| `ech=1` or `ech=true` | Turns ECH on. The core looks up the ECH configuration in DNS. |
| `ech=` a domain, or a domain, `+` and a DNS server URL | Turns ECH on and names the domain to look up, and optionally the DNS server to ask, for example `cloudflare-ech.com+https://1.1.1.1/dns-query`. It fills `ECH Query Name / Resolver`. |
| `ech=` any other value | Turns ECH on with this Base64 ECH configuration. |
| `pcs=` | SHA-256 hashes of the server certificate, in hex and separated by commas. A matching certificate is accepted instead of the usual CA check. It fills `Certificate SHA256`. |

For `VLESS (Xray)` profiles, the `ech` value goes unchanged into `ECH Config List`. Links that Throne creates also keep the older `ech_enabled`, `ech_config` and `ech_server_name` parameters. For what ECH does, see [ECH](@/advanced/presets.md#ech).

### Share on the desktop {#share-desktop}

Right-click a profile in the list and open `Share`:

| Action | What you get |
|---|---|
| `QR Code and link` | A window with the standard link and its QR code. Tick `Deep Link` to show the Throne link instead. Types without a standard link usually open with `Deep Link` already ticked (see above). |
| `Copy links of selected` (`Ctrl+C`) | The standard links of all selected profiles, one per line. Types without a standard link are usually copied as Throne links (see above). |
| `Copy links of selected (Deep Links)` (`Ctrl+Alt+C`) | The Throne links of all selected profiles. |

To copy the links of a whole group, open `Groups` → `Edit current Group` and click `Copy profile share links` or `Copy profile share links (Deep Links)`.

`Share` → `Export config` (`Ctrl+E`) shows the core configuration that Throne generates for the profile, with a `sing-box` tab and, for profiles that use Xray, an `Xray` tab. `Copy` copies the tab you see. This is a config file for other clients, not a share link. Before 1.4.0, these were `Export Sing-box config` and `Export Xray config`.

In Simple mode, `Copy links of selected (Deep Links)` and `Export config` are hidden (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

To import a QR code that is shown on your screen, use `Program` → `Scan QR Code` (`Ctrl+Shift+Q`). To import a QR code from an image file, drop the file on the main window or open it with `Program` → `Add profile from File(s)`.

### Share on Android {#share-android}

Tap the share button of a profile. Double-column cards and, since 2.0.5, `Compact profile rows` have no share button: tap ⋮ → `Share` on the row instead. Since 2.0.5, the `Double column` layout is available only in landscape, from the toolbar. See [Profile list layout](@/android/_index.md#layout).

The menu offers:

- `QR code` → `Standard` or `Throne link`
- `Export to clipboard` → `Standard` or `Throne link`
- `Configuration` → `Export to clipboard` or `Export to file`: the core configuration that Throne generates, not a share link

`Standard` is missing when the profile type has no standard link. Chains cannot be shared on Android. To copy the links of a whole group, open `Groups`, tap `More options` on the group, and choose `Copy profile share links` or `Copy profile share links (deep links)`.

To import a shared profile, use `Add profile` → `Scan QR code` or `Import from clipboard`. A Throne link from the clipboard or a QR code is imported without a question.
