+++
title = "DNS Tunnels"
description = "Reach your server through DNS queries on networks where only DNS gets through: DNSTT, VayDNS, MasterDNS and Xray XDNS profiles."
weight = 45
toc = true
+++

A DNS tunnel carries your traffic inside DNS queries, so it works on networks that block everything except DNS. It is much slower than a normal profile, and the first connection takes a while. Use it only when your other profiles cannot connect.

## How a DNS tunnel works {#how-it-works}

Throne does not connect to the tunnel server. It sends DNS queries for names under the tunnel domain, for example `t.example.com`, to resolvers: your network's DNS servers or public ones such as `8.8.8.8`. The domain is delegated to the tunnel server, so the resolvers pass the queries on, and the server answers in the DNS replies.

So a DNS tunnel profile has no server address or port. It needs the tunnel domain, the server's key and [resolvers](#resolvers) that pass the queries on. The speed depends mostly on the resolvers: many limit the size or number of queries, or drop unusual ones.

| Kind | `Type` | Server |
| --- | --- | --- |
| [DNSTT and VayDNS](#dnstt) | `DNSTT / VayDNS` | `dnstt-server` or `vaydns-server` |
| [MasterDNS](#masterdns) | `MasterDNS` | MasterDnsVPN or StormDNS |
| [XDNS](#xdns) | `VLESS (Xray)` with the `kcp` network and an `xdns` mask | Xray with mKCP and an `xdns` mask |

Create a profile with `Program` → `New profile` (`Ctrl+N`), or [import](#import) a link or config. For Throne for Android, see [Android](#android).

## DNSTT and VayDNS {#dnstt}

`Mode` selects the protocol: `DNSTT` (default) for `dnstt-server` or `vaydns-server -dnstt-compat`, `VayDNS` for `vaydns-server`, and `VayDNS (dnstt-compatible)` for the same servers as `DNSTT`, with `Max QNAME Length`.

| Field | What it does |
| --- | --- |
| `Domain` | The tunnel domain delegated to the server, for example `t.example.com`. |
| `Public Key` | The server's public key: 64 hexadecimal digits. |
| `Record Type`, `Client ID Size` | `VayDNS` only. Must match the server (`-clientid-size` for the ID size). |
| `Max QNAME Length` | Both VayDNS modes. Default 101 (`VayDNS`) or 253 (dnstt-compatible), at most 253. Longer names carry more data, but some resolvers drop them. |
| `Payload per Query` | The tunnel data that each query carries. It must be at least 25 bytes: use a shorter domain or a longer `Max QNAME Length`. |

In the `Advanced` group (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)), keep `Idle Timeout` in line with the server; `Keepalive Interval` must be shorter than it.

`Backend` → `Type` says what runs behind the tunnel on the server:

- `None (chain only)` (default): the server passes the tunnel on to another proxy server. Create a [chain](@/advanced/chains.md#create-chain) with the DNS tunnel profile on top and that server's profile below it.
- `SOCKS5` or `Shadowsocks`: enter its credentials. Tick `UDP over TCP` to carry UDP.
- `SSH`: enter `User` and `Password` or `Private Key`, as in an `SSH` profile.

## MasterDNS {#masterdns}

`MasterDNS` profiles work with MasterDnsVPN and StormDNS servers and carry TCP only. These fields must match the server's configuration:

| Field | Server setting | What to enter |
| --- | --- | --- |
| `Domains` | `DOMAIN` | One per line, all leading to the same server. |
| `Mode` | `PROTOCOL_TYPE` | `SOCKS5` (default): the server connects to the website for you. `TCP / custom chain`: the server sends every connection to its own forward target, so chain that target's profile below this one. |
| `Encryption` | `DATA_ENCRYPTION_METHOD` | `none` (0), `xor` (1, default), `chacha20` (2), `aes-128-gcm` (3), `aes-192-gcm` (4) or `aes-256-gcm` (5). |
| `Key` | `ENCRYPTION_KEY_FILE` | The text in that file. Not needed with `none`. |

{% alert_warning() %}
MasterDNS encrypts only the queries. The server's replies are never encrypted or authenticated, `xor` only obfuscates, and `chacha20` is not authenticated. Send only encrypted traffic through the tunnel, for example HTTPS websites, or chain an encrypted proxy below the MasterDNS profile.
{% end %}

In the `Transfer` group, `Duplication` (default 3, at most 10) sends each packet through that many pairs of resolver and domain. The server may refuse compression. `Base64-encoded replies` helps with resolvers that damage binary TXT records, but carries a quarter less data.

Before the session starts, Throne tests every pair of domain and resolver for how much data a query and a reply can carry. Resolvers below `Min Upload` or `Min Download` in the `MTU` group (38 and 200 bytes) are left out. If none passes, Throne repeats the test every 5 seconds.

## XDNS {#xdns}

XDNS is Xray's mKCP transport with an `xdns` mask in Finalmask: Xray sends the mKCP packets as DNS queries through resolvers. The server must run Xray with an mKCP inbound and an `xdns` mask for the same domain.

Importing is easiest: a `vless://` link with `type=kcp` and the mask in `fm`, the server's Xray JSON, or a Throne link. `vless://` links with `type=kcp` always become `VLESS (Xray)` profiles, whatever the [Xray VLESS Preference](@/advanced/xray.md#vless-preference).

To set it up by hand:

1. Open `Program` → `New profile`, set `Type` to `VLESS (Xray)` and enter the server's VLESS settings.
2. Set `Network` to `kcp`.
3. Click `Finalmask` and add an `xdns` mask to the `udp` list, with the tunnel domain and, optionally, resolvers:

   ```json
   {
     "udp": [
       {
         "type": "xdns",
         "settings": {
           "domains": [{ "names": ["t.example.com"] }],
           "resolvers": [{ "addrs": ["udp://8.8.8.8:53"] }]
         }
       }
     ]
   }
   ```

4. Optionally, enter resolvers in the `Resolvers` row. They replace the ones in the mask.
5. Click `OK`.

The `xdns` mask must be the last mask in `udp` and needs at least one domain. Without resolvers in the mask or the `Resolvers` row, Xray cannot connect. A DNS-over-HTTPS resolver (`https://…`) in the mask needs a `tlsConfig` with `serverName`, for example `"tlsConfig": {"serverName": "dns.google"}`.

Leave the mKCP fields, such as `MTU` and `TTI`, empty to use Xray's defaults. With `Multiplex` set to `Keep Default`, Throne turns mux on for XDNS profiles; without mux, every connection would open its own mKCP session to all resolvers.

## Resolvers {#resolvers}

Every DNS tunnel profile has a resolver box (the `Resolvers` group for DNSTT and MasterDNS, the `Resolvers` row for XDNS) with one resolver per line. Lines that start with `#` are comments. `IP list` adds the addresses of an IP scanner list after the typed lines, for example the results of a [resolver scan](#find-resolvers).

| Line | DNSTT / VayDNS | MasterDNS | XDNS |
| --- | --- | --- | --- |
| `system`: your system's DNS servers | Yes | Yes | No |
| An IP address, optionally with a port: `8.8.8.8`, `1.1.1.1:53`, `[2001:db8::1]:53` | Yes | Yes | Yes |
| A range such as `192.0.2.0/24` | Up to 256 addresses (`/24` or longer) | Up to 65536 addresses (`/16` or longer) | Only from an IP list, up to 256 addresses |
| `udp://` or `tcp://` and an address | Yes | No | Yes |
| `tls://` (DNS over TLS) | Yes | No | No |
| `https://` (DNS over HTTPS), with an optional path | Yes | No | Yes |

The default port is 53; DNSTT uses 853 for `tls://` and 443 for `https://`. Throne uses at most 4096 resolvers for DNSTT and 256 for XDNS. Without any resolvers, DNSTT and MasterDNS use `system`, and XDNS keeps the resolvers of the `xdns` mask.

`Strategy` decides where the queries go:

- DNSTT and VayDNS: `round_robin` (default) uses each resolver in turn, `failover` the first one that works, `fanout` every working one at the same time.
- MasterDNS: `least_loss` (default) uses the resolver with the least loss. The other strategies pick in turn, at random, by latency or by a mix of loss and latency.
- XDNS: Xray uses all resolvers in turn.

## Find working resolvers {#find-resolvers}

Many resolvers do not pass tunnel queries on. A resolver scan tries addresses as the resolvers of your profile and keeps the ones that work:

1. Save the profile and open it again. `Find resolvers…` stays disabled until the profile is saved.
2. Click `Find resolvers…` next to `IP list`. Throne opens the profile's DNS resolver scan in the IP scanner.
3. On the `Targets` tab, choose the addresses to try in `Base IP list`, for example `Iran DNS resolvers` or `Russia DNS resolvers`, and start the scan.
4. On the `Results` tab, click `Use as resolvers`. The results become the profile's `IP list`. The lines that you typed stay and are used first.

See [DNS resolver scans](@/guides/testing.md#dns-scan).

## Import and share {#import}

Throne imports these from the clipboard, files, QR codes and subscriptions:

| Format | Result |
| --- | --- |
| `dnst://` links | A `DNSTT / VayDNS` profile, or a `MasterDNS` profile when the link's tunnel type is `masterdns`. |
| `slipnet://` links of SlipNet 16 to 28 | A `DNSTT / VayDNS` profile. NoizDNS profiles become plain DNSTT, without NoizDNS's DPI evasion. Encrypted SlipNet links cannot be read. |
| `masterdns://` and `stormdns://` links | A `MasterDNS` profile. |
| sing-box JSON with `dnstt` or `masterdns` outbounds | `DNSTT / VayDNS` and `MasterDNS` profiles. |
| `vless://` links with `type=kcp`, Xray JSON with the `kcp` network | `VLESS (Xray)` profiles, XDNS with an `xdns` mask. |
| `throne://add/` links | Any of these, with all settings. |

- A `dnst://` link names the service behind the tunnel: `socks`, `ssh`, `shadowsocks`, or `custom` for `None (chain only)`. MasterDNS takes only `socks` and `custom`.
- If a link also carries a proxy to run over the tunnel, Throne warns about it. Import that proxy separately and chain it below the DNS tunnel profile.

To share a profile, right-click it → `Share`. DNSTT and MasterDNS profiles give a `dnst://` link, XDNS profiles a `vless://` link. These leave out your resolvers and IP list, and for MasterDNS all domains but the first. A Throne link keeps every setting except the IP list. See [Share links](@/reference/protocols.md#share-links).

## Testing {#testing}

URL tests and IP tests work with DNS tunnel profiles. The first request opens the tunnel, which takes many slow queries, so Throne gives it 30 more seconds for DNSTT and XDNS and 90 more for MasterDNS; a chain gets the sum. The latency shown comes from the second request. See [URL test](@/guides/testing.md#url-test).

If a test fails, look for the reason on the `Logs` tab, check `Domain` and `Public Key` (DNSTT) or `Domains`, `Encryption` and `Key` (MasterDNS) against the server, and try other resolvers or a [resolver scan](#find-resolvers).

## Android {#android}

Throne for Android has the same DNS tunnels and imports the same links, also when you tap them in another app. The differences:

- Create a profile with `Add profile` → `Manual settings` → `DNSTT / VayDNS` or `MasterDNS`. For XDNS, choose `VLESS (Xray)`, set `Network` to `mKCP` and add the `xdns` mask in `Finalmask`.
- There are no IP lists and no resolver scans: only the resolvers typed in the profile are used.
- URL tests get no extra time for the first request.
