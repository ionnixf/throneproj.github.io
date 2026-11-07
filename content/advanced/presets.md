+++
title = "Anti-Censorship Presets"
description = "What TLS fragment, TLS tricks, uTLS, Multiplex, ECH and TLS spoof do, when they help and what they cost."
weight = 30
toc = true
+++

Some networks block or slow down proxies by inspecting how each encrypted connection starts. These options change how that start looks. `Preset Settings`, the profile editor's `TLS Camouflage Settings` and `Advanced Settings`, and `Custom` multiplex are Advanced mode only (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

## Presets and profile settings {#presets}

`Settings` → `Preset Settings` holds the global defaults. In a profile's editor, Fragment, TLS Tricks, Multiplex and TLS Spoof offer `Keep Default` (follow the preset's `Default On`; new profiles use this), `On` and `Off`, so `Default On` only affects profiles left on `Keep Default`.

The presets apply only to profiles that run on sing-box, except the uTLS `Default Fingerprint`, which applies to both cores, and the Xray multiplex presets, which `VLESS (Xray)` profiles use. See [sing-box vs Xray](@/advanced/xray.md#two-cores).

## Change one thing at a time {#one-at-a-time}

Each option works against one blocking method. None helps everywhere, and each can break a profile that worked before.

1. Turn on one option in one profile that fails or is slow.
2. Run a [URL test](@/guides/testing.md#url-test) and open a few websites. If nothing improves, set the option back to `Keep Default` and try the next one.
3. When an option helps most of your profiles, turn on its `Default On` in `Preset Settings`.

## TLS fragment {#tls-fragment}

A censor often reads the server name (SNI) in the first message of a TLS connection, the ClientHello. TLS fragment splits that message, so a simple filter cannot read the name from one packet. It only helps against such simple filters.

Set `Fragment` in the profile editor under `TLS Camouflage Settings`. Try `Enable TLS Record Fragment` next to it first: it is lighter and splits the ClientHello into TLS records instead of packets.

`Preset Settings` → `TLS` → `TLS Fragment` → `Implementation` chooses how:

- `built-in` (default): sing-box splits the ClientHello into TCP packets. The profile's `Fallback Delay`, such as `500ms`, is the wait between pieces when the system cannot measure it.
- `custom`: pieces of random `Size` (bytes, `10-100`) with `Sleep` pauses (milliseconds, `2-5`). Naive profiles support only this one.

**Cost:** new connections start more slowly, and `custom` turns off TCP Fast Open for the profile.

A routing rule can also fragment the connections it matches, such as direct connections to a site that a filter blocks by name. See `tls_fragment` in [Advanced rules](@/guides/routing.md#advanced-rules).

## TLS tricks {#tls-tricks}

TLS tricks write the server name in mixed case, such as `ExAmPlE.com`. Servers ignore the case, but a filter that looks for the exact name can miss it. Set `TLS Tricks` in the profile editor under `TLS Camouflage Settings`.

**Cost:** almost none, but it does not help against filters that ignore the case.

## uTLS fingerprint {#utls}

uTLS makes the ClientHello look like that of a common browser or system, instead of a plain proxy client that is easy to recognize by its fingerprint.

- **Per profile:** `Fingerprint` in the profile editor. Share links often set it with `fp=`.
- **Global:** `Preset Settings` → `TLS` → `uTLS` → `Default Fingerprint`, for profiles without their own. While it is empty (the default), such profiles use no uTLS on sing-box, `random` for sing-box REALITY, and `chrome` on Xray. Hysteria, TUIC, Juicity and Naive profiles do not use uTLS.
- **Choices:** `chrome`, `firefox`, `edge`, `safari`, `360`, `qq`, `ios`, `android`, `random`, `randomized` and `unsafe`. `unsafe` turns uTLS off even when a `Default Fingerprint` is set, and only then does the profile's `Cipher Suites` field apply (TLS 1.2 suites, separated by colons; leave it empty unless your server needs it).

**Cost:** little. If a server stops working with one fingerprint, try another one or clear the field.

## Multiplex {#multiplex}

Multiplex (mux) carries many connections inside one connection to the server, so pages need fewer handshakes and an observer sees fewer connections. The server must support mux with the same protocol, and padding if you use it.

Set `Multiplex` in the profile editor (`VLESS (Xray)` profiles say `Enabled` and `Disabled` instead of `On` and `Off`). `Custom` → `Configure` gives the profile its own copy of the preset fields; profiles from links or subscriptions with their own mux settings open with `Custom`. Mux is disabled for VLESS profiles with the `xtls-rprx-vision` flow and required for [XDRIVE](@/advanced/xray.md#xdrive).

sing-box presets (`Preset Settings` → `Multiplex` → `Sing-box`):

- `Protocol`: `smux`, `yamux` or `h2mux`.
- `Mode`: `Max Streams` opens another connection once one carries that many streams. `Max Connections` keeps at most that many and opens a new one only while the least busy one carries `Min Streams` streams.
- `Padding`: pads the multiplexed traffic.
- `TCP Brutal`: sends at the fixed `Upload (Mb/s)` and `Download (Mb/s)` you enter, even when packets are lost. Enter both. It needs server support, and all streams then share one connection.

Xray presets (`Preset Settings` → `Multiplex` → `Xray`):

- `Concurrency`: streams per connection before another one opens. `0` uses Xray's default of 8, `-1` sends TCP outside the mux.
- `Max Connections`: at most this many connections, for TCP and XUDP each. Empty means no limit.
- `XUDP Concurrency`: UDP streams per connection, on connections of their own. `0` carries UDP on the TCP connections, `-1` sends it outside the mux.
- `UDP/443`, mostly QUIC: `Reject` (default) makes browsers fall back to TCP, `Allow` carries it through the mux, `Skip` sends it outside the mux.

**Cost:** everything on one connection stalls together. Many parallel connections, such as a torrent client's, can make Throne use a lot of CPU ([#1090](https://github.com/throneproj/Throne/issues/1090)), so turn Multiplex off for that profile. The first request over a new mux connection is slower, so a short latency-test timeout can report a working profile as failed.

## ECH {#ech}

Encrypted Client Hello (ECH) encrypts the server name, so an observer cannot read it. It works only when the server supports it, and is set per profile:

1. Open the profile, click `Advanced Settings` and tick `Enable ECH`.
2. Optional: paste the server's ECH configuration under `ECH Config`. Left `Not Set`, it is looked up in DNS, for the domain in `ECH Query Name / Resolver` or else the server name. Add a DNS server after a `+` to ask it instead of Direct DNS, for example `cloudflare-ech.com+https://1.1.1.1/dns-query`.
3. Click `OK` in both windows.

Xray profiles use `ECH Config List` in their TLS settings instead: the configuration in Base64, or a domain and a DNS-over-HTTPS server, such as `cloudflare-ech.com+https://8.8.8.8/dns-query`. Share links carry ECH in `ech=`, see [ECH and certificate pins in links](@/reference/protocols.md#ech-links).

**Cost:** the profile fails if the server does not support ECH or the configuration cannot be found.

## TLS spoof {#tls-spoof}

Some filters let a few allowed names through and block the rest. TLS spoof sends a fake ClientHello with an allowed name just before the real one. The fake message is invalid on purpose, so the server ignores it while the filter lets the connection pass.

- **Presets** (`Preset Settings` → `TLS` → `TLS Spoof`): `Spoof SNI` is the allowed name, and `Method` how the fake message is made invalid: `wrong-sequence`, `wrong-checksum`, `wrong-ack`, `wrong-md5` or `wrong-timestamp` (empty: the core's default).
- **Per profile:** `Advanced Settings` → `TLS Spoof`, `Spoof SNI` and `Method`. Empty fields use the presets. A profile with its own `Spoof SNI` spoofs even on `Keep Default`.
- **Per site:** an advanced routing rule with `tls_spoof` and `tls_spoof_method`, see [Advanced rules](@/guides/routing.md#advanced-rules).

TLS spoof sends raw packets, so the core needs the rights that [TUN mode](@/guides/tun_mode.md#privileges) sets up: Administrator on Windows (not on Windows ARM64), root or `CAP_NET_RAW` and `CAP_NET_ADMIN` on Linux, and root on macOS, where `wrong-timestamp` does not work.

**Cost:** it needs elevated rights and only helps against filters that allow some names.

## HTTP/2 and QUIC tuning {#http2-quic}

`Preset Settings` → `HTTP/2 & QUIC` sets defaults such as `Idle Timeout`, `Keep Alive Period` and `Initial Packet Size` for Hysteria, TUIC and MASQUE profiles. A profile overrides them in `Advanced Settings` → `QUIC Parameters`. Leave them empty, which keeps the core's defaults, unless your server's documentation asks for other values.

## Android {#android}

- The same presets are in `Settings` → `Presets`, with `On by default` instead of `Default On`. ECH is set per profile.
- `Enable multiplexer` in the profile editor also offers `Custom`, which shows the multiplex fields right below it.
- Routing rules can fragment the connections they match, with `TLS fragment`, `TLS fragment fallback delay` and `TLS record fragment`.
- TLS spoof is not available.
