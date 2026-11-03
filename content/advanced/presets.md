+++
title = "Anti-Censorship Presets"
description = "What TLS fragment, TLS tricks, uTLS, Multiplex, ECH and TLS spoof do, when they help, what they cost, and how presets and profile settings combine."
weight = 30
toc = true
+++

Some networks block or slow down proxies by inspecting how each encrypted connection starts. Throne has several options that change how this start looks. This page explains what each option does, when it can help and what it costs.

In Simple mode, `Preset Settings`, the `Fragment`, `Enable TLS Record Fragment` and `TLS Tricks` options of the profile editor, its `Advanced Settings` button and the `Custom` choice of `Multiplex` are hidden (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). Hidden settings keep their values and still apply.

## Presets and profile settings {#presets}

Global defaults are in `Settings` → `Preset Settings`:

| Tab | Options (default) |
| --- | --- |
| `Multiplex` | sing-box: `Protocol` (`smux`), `Mode` (`Max Streams`), `Max Streams` (8), `Max Connections` (4), `Min Streams` (4), `Padding` (off), `TCP Brutal` (off), `Default On` (off). Xray: `Concurrency` (8), `Max Connections` (empty, no limit), `XUDP Concurrency` (16), `UDP/443` (`Reject`), `Default Mux On` (off). |
| `TLS` | `TLS Fragment`: `Implementation` (`built-in`), `Size` (`10-100`), `Sleep` (`2-5`), `Default On` (off). `TLS Tricks`: `Default On` (off). `uTLS`: `Default Fingerprint` (empty). `TLS Spoof`: `Spoof SNI` (empty), `Method` (empty), `Default On` (off). |
| `HTTP/2 & QUIC` | Tuning for QUIC-based profiles. Empty fields keep the core's defaults. |

Each profile can override the presets in its editor. Fragment, TLS Tricks, Multiplex and TLS Spoof offer three choices:

| Profile choice | Result |
| --- | --- |
| `Keep Default` | Follows the preset's `Default On`. New profiles use this. |
| `On` | Always on for this profile. |
| `Off` | Always off for this profile. |

So `Default On` turns an option on only for the profiles that are left on `Keep Default`. Since 1.4.0, `Multiplex` has a fourth choice, `Custom`, for settings of the profile's own. See [Multiplex](#multiplex).

TLS Fragment, TLS Tricks, TLS Spoof and the sing-box Multiplex presets apply only to profiles that run on sing-box. `VLESS (Xray)` profiles have their own Multiplex choice (`Keep Default`, `Enabled`, `Disabled` and, since 1.4.0, `Custom`) that follows the Xray presets and `Default Mux On`. The uTLS `Default Fingerprint` applies to both cores. See [sing-box vs Xray](@/advanced/xray.md#two-cores).

## Change one thing at a time {#one-at-a-time}

Each option works against a specific blocking method. None of them helps everywhere, and each one can break a profile that worked before.

1. Pick one profile that fails or is slow.
2. In its editor, turn on one option.
3. Test the profile: run a URL test, then open a few websites.
4. If nothing improves, set the option back to `Keep Default` and try the next one.
5. When an option helps most of your profiles, turn on its `Default On` in `Preset Settings`.

Multiplex, its padding, TCP Brutal and ECH also need support on the server. See [Testing](@/guides/testing.md#url-test) for how to test profiles.

## TLS fragment {#tls-fragment}

A censor often reads the server name (SNI) in the first message of a TLS connection, the ClientHello. TLS fragment splits this message into small pieces, so a simple filter cannot read the name from one packet. It helps only against such simple filters.

In the profile editor, under `TLS Camouflage Settings`, set `Fragment` to `Keep Default`, `On` or `Off`. `Preset Settings` → `TLS` → `TLS Fragment` → `Implementation` chooses how the splitting is done:

| `Implementation` | How it works | Settings |
| --- | --- | --- |
| `built-in` (default) | sing-box splits the ClientHello into several TCP packets. | `Fallback Delay` in the profile, for example `500ms`: the wait between pieces when the system cannot measure it. |
| `custom` | The ClientHello is sent in pieces of random size, with short pauses between them. | `Size`: bytes per piece, as a range (`10-100`). `Sleep`: milliseconds between pieces, as a range (`2-5`). |

`Enable TLS Record Fragment`, in the same part of the editor, is a separate and lighter option. It splits the ClientHello into several TLS records instead of several packets. Try it before `Fragment`.

**Cost:** new connections start more slowly. With the `custom` implementation, TCP Fast Open is turned off for the profile. Naive profiles support only the `custom` implementation.

Since 1.4.0, a routing rule can also fragment the connections it matches, for example direct connections to a site that a filter blocks by its name. Use an advanced rule with the `route` or `route-options` action and set `tls_fragment` (with `tls_fragment_fallback_delay`, if needed) or `tls_record_fragment`. See [Routing](@/guides/routing.md#advanced-rules).

## TLS tricks {#tls-tricks}

TLS tricks write the server name with mixed upper- and lower-case letters, for example `ExAmPlE.com` instead of `example.com`. Servers normally ignore the case, but a filter that looks for the exact name can miss it.

Set `TLS Tricks` to `On` in the profile (under `TLS Camouflage Settings`), or tick `Preset Settings` → `TLS` → `TLS Tricks` → `Default On` for all profiles that are left on `Keep Default`.

**Cost:** almost none. It does not help against filters that ignore the case.

## uTLS fingerprint {#utls}

Every TLS client has a typical fingerprint: the choice and order of the options in its ClientHello. The fingerprint of a plain proxy client is easy to recognize. uTLS copies the fingerprint of a common browser or system instead.

- **Per profile:** `Fingerprint` in the profile editor. Share links often set it with `fp=`.
- **Global:** `Preset Settings` → `TLS` → `uTLS` → `Default Fingerprint`. Profiles without their own fingerprint use it, on both cores (`VLESS (Xray)` profiles since 1.4.0). When it is empty (the default), sing-box profiles without a fingerprint use no uTLS, and Xray profiles without a fingerprint use `chrome`. Hysteria, TUIC, Juicity and Naive profiles do not use uTLS.
- **Choices:** `chrome`, `firefox`, `edge`, `safari`, `360`, `qq`, `ios`, `android`, `random`, `randomized`, and since 1.4.0 `unsafe`.
- **`unsafe`** turns uTLS off for the profile, even when a `Default Fingerprint` is set. The core then uses its own TLS client. Only then does the profile's `Cipher Suites` field (since 1.4.0) apply: TLS 1.2 cipher suite names, separated by colons. Leave it empty unless your server needs it.

A sing-box REALITY profile without any fingerprint uses `random`.

**Cost:** little. If a server stops working with one fingerprint, try another one or clear the field.

## Multiplex {#multiplex}

Multiplex (mux) carries many connections inside one connection to the server. Opening a page then needs fewer new connections and handshakes, and an observer sees fewer connections.

- **Per profile:** `Multiplex` in the profile editor: `Keep Default`, `On`, `Off` or, since 1.4.0, `Custom` (`VLESS (Xray)` profiles: `Keep Default`, `Enabled`, `Disabled` or `Custom`). `Keep Default` and `On` use the presets. With `Custom`, click `Configure` next to the list: the `Custom Multiplex` window (Xray: `Custom Mux`) has the same fields as the presets and starts from their values. A profile from a link or subscription that carries its own mux settings opens with `Custom`.
- **sing-box presets** (`Preset Settings` → `Multiplex` → `Sing-box`):
  - `Protocol`: `smux`, `yamux` or `h2mux`.
  - `Mode`: with `Max Streams`, a connection is reused until it carries `Max Streams` streams, then another one opens. With `Max Connections`, Throne keeps at most `Max Connections` connections and opens a new one only while the least busy connection already carries `Min Streams` streams.
  - `Padding`: pads the multiplexed traffic.
  - `TCP Brutal`, `Upload (Mb/s)` and `Download (Mb/s)`: see [TCP Brutal](#tcp-brutal).
  - `Default On`.
- **Xray presets** (`Preset Settings` → `Multiplex` → `Xray`):
  - `Concurrency`: the streams that one connection carries before another one opens. `0` uses Xray's default of 8, and `-1` sends TCP outside the mux.
  - `Max Connections` (since 1.4.0): at most this many connections, for TCP and for XUDP each. Once every connection carries `Concurrency` streams, new streams join the least busy one. Empty means no limit.
  - `XUDP Concurrency` (since 1.4.0): UDP streams per connection, on connections of their own. `0` carries UDP on the TCP connections, and `-1` sends it outside the mux.
  - `UDP/443` (since 1.4.0): UDP to port 443, which is mostly QUIC. `Reject` (Xray's default) makes browsers fall back to TCP, `Allow` carries it through the mux, and `Skip` sends it outside the mux.
  - `Default Mux On`.

Before 1.4.0, the sing-box presets were only `Protocol`, `Concurrency` (now `Max Streams`), `Padding` and `Default On`, and the preset `Padding` applied to every multiplexed profile. Now a `Custom` profile uses its own `Padding`.

The server must support mux with the same protocol. Padding also needs server support. The profile editor disables Multiplex for VLESS profiles with the `xtls-rprx-vision` flow. XDRIVE profiles need mux, see [XDRIVE](@/advanced/xray.md#xdrive).

### TCP Brutal {#tcp-brutal}

`TCP Brutal` makes the multiplexed connection send at the fixed speeds that you enter in `Upload (Mb/s)` and `Download (Mb/s)`, even when packets are lost. It needs a server with TCP Brutal support. With TCP Brutal, all streams share one connection, so `Mode` and its counts do not apply.

Since 1.4.0, TCP Brutal is part of the sing-box multiplex settings: tick it in `Preset Settings` for every profile that uses the presets, or in the `Custom Multiplex` window of one profile. Enter both speeds; a missing one is sent as 1 Mb/s. Up to 1.3.2, the profile editor had `Enable TCP Brutal`, `Brutal Download Speed` and `Brutal Upload Speed`. A profile that has TCP Brutal of its own but whose `Multiplex` is not `Custom` does not use it, and Throne warns "TCP Brutal only works with a custom multiplex. Set Multiplex to Custom."

### When mux hurts {#mux-costs}

- All traffic shares one connection. If that connection stalls, every page and download on it stalls.
- Many parallel connections, for example from a torrent client, can make Throne use a lot of CPU with mux on ([#1090](https://github.com/throneproj/Throne/issues/1090)). Turn Multiplex off for that profile.
- The first request over a new mux connection takes longer. A short latency-test timeout can then report a working profile as failed.

## ECH {#ech}

Encrypted Client Hello (ECH) encrypts the server name inside the ClientHello, so an observer cannot read it. It works only when the server supports ECH. There is no preset for ECH; you turn it on per profile.

1. Open the profile and click `Advanced Settings`.
2. Tick `Enable ECH`.
3. Optional: click `ECH Config` and paste the server's ECH configuration. If it stays `Not Set`, the core looks the configuration up in DNS.
4. Optional: in `ECH Query Name / Resolver`, enter the domain to look the configuration up for, if it is not the server name. Since 1.4.0, you can add a DNS server after a `+`, for example `cloudflare-ech.com+https://1.1.1.1/dns-query`. Throne then asks that server.
5. Click `OK`, then `OK` in the profile editor.

Without a DNS server in that field, Throne looks the configuration up with Direct DNS since 1.4.0. Before, the lookup went through the proxy that it was needed for, and could fail.

Xray profiles have their own field since 1.4.0: `ECH Config List` in the TLS settings of the profile editor. It takes the server's ECH configuration in Base64, or a domain and a DNS-over-HTTPS server, such as `cloudflare-ech.com+https://8.8.8.8/dns-query`, so that Xray looks it up itself. Share links can carry ECH in the `ech=` parameter; see [ECH and certificate pins in links](@/reference/protocols.md#ech-links).

**Cost:** the profile fails if the server does not support ECH or the configuration cannot be found.

## TLS spoof {#tls-spoof}

Some filters let connections to a few allowed names through and block the rest. TLS spoof sends a fake ClientHello with an allowed name just before the real one. The fake message is made invalid on purpose, so the server ignores it, while the filter reads it and lets the connection pass.

Global settings are in `Preset Settings` → `TLS` → `TLS Spoof`:

- `Spoof SNI`: the allowed name to put in the fake message.
- `Method`: how the fake message is made invalid: `wrong-sequence`, `wrong-checksum`, `wrong-ack`, `wrong-md5` or `wrong-timestamp`. Empty uses the core's default.
- `Default On`: spoof for every profile that is left on `Keep Default`. You can tick it only after you enter a `Spoof SNI`.

Per profile, open `Advanced Settings` and set `TLS Spoof` (`Keep Default`, `On` or `Off`), `Spoof SNI` and `Method`. An empty field uses the preset value. A profile that has its own `Spoof SNI` spoofs even on `Keep Default`.

To spoof only for some sites, use an advanced routing rule with the `route` or `route-options` action and set `tls_spoof` and `tls_spoof_method`. See [Routing](@/guides/routing.md#advanced-rules).

TLS spoof sends raw packets, so the core needs elevated rights:

- **Windows:** Throne must run as Administrator, for the WinDivert driver. TLS spoof is not available on Windows ARM64.
- **Linux:** the core needs root, or the `CAP_NET_RAW` and `CAP_NET_ADMIN` capabilities.
- **macOS:** the core needs root. The `wrong-timestamp` method does not work on macOS.

The privilege setup of TUN mode gives the core these rights. See [TUN Mode](@/guides/tun_mode.md#privileges).

**Cost:** it needs elevated rights, and it helps only against filters that allow some names.

## HTTP/2 and QUIC tuning {#http2-quic}

`Preset Settings` → `HTTP/2 & QUIC` sets defaults for Hysteria, TUIC and MASQUE profiles: `Idle Timeout`, `Keep Alive Period`, `Stream Receive Window`, `Connection Receive Window`, `Max Concurrent Streams`, `Initial Packet Size` and `Disable Path MTU Discovery`. Empty fields keep the core's defaults. A profile can override them in `Advanced Settings` → `QUIC Parameters`.

Leave these fields empty unless your server's documentation asks for other values.

## Android {#android}

- The same presets are in `Settings` → `Presets`: Multiplex (sing-box and Xray), TLS Client Hello fragment, TLS tricks, uTLS, and HTTP/2 and QUIC. `Default On` is called `On by default` there.
- Profiles have the same `Keep default` choice, and ECH is set per profile.
- Since 2.0.4, the multiplex presets have the same fields as on the desktop, and `Enable multiplexer` in the profile editor also offers `Custom`, which shows the multiplex fields right below it.
- Since 2.0.3, routing rules can also fragment the connections they match, with `TLS fragment`, `TLS fragment fallback delay` and `TLS record fragment`. Throne for Android 2.0.2 does not have these options.
- TLS spoof is not available on Android.
