+++
title = "DNS Tunnels"
description = "Reach your server through DNS queries on networks where only DNS gets through: DNSTT, VayDNS, MasterDNS and Xray XDNS profiles, their resolvers, and how to find resolvers that work."
weight = 45
toc = true
+++

Since 1.4.0, Throne can carry your traffic inside DNS queries. A DNS tunnel works on networks that block everything except DNS. It is much slower than a normal profile, and the first connection takes a while. Use it only when your other profiles cannot connect.

## How a DNS tunnel works {#how-it-works}

Throne does not connect to the tunnel server. It sends DNS queries for names under the tunnel domain, for example `t.example.com`, to resolvers: DNS servers such as the ones of your network or public ones like `8.8.8.8`. The domain is delegated to the tunnel server, so the resolvers pass the queries on to it. The server sends its data back in the DNS replies.

This means:

- A DNS tunnel profile has no server address or port. It needs the tunnel domain, the key of the server, and [resolvers](#resolvers) that pass the queries on.
- The speed depends mostly on the resolvers. Many resolvers limit the size or the number of queries, or drop unusual ones. [Find working resolvers](#find-resolvers) helps you pick good ones.
- The server needs a domain whose subdomain is delegated to it, and the server program that matches the profile type.

Throne supports three kinds of DNS tunnels:

| Kind | `Type` in `New profile` | Server | Engine |
| --- | --- | --- | --- |
| [DNSTT and VayDNS](#dnstt) | `DNSTT / VayDNS` | `dnstt-server` or `vaydns-server` | sing-box |
| [MasterDNS](#masterdns) | `MasterDNS` | MasterDnsVPN or StormDNS | sing-box |
| [XDNS](#xdns) | `VLESS (Xray)` with the `kcp` network and an `xdns` mask | Xray with mKCP and xdns | Xray |

To create a profile by hand, open `Program` → `New profile` (`Ctrl+N`) and choose the type. You can also import links and configs, see [Import and share](#import). DNS tunnels are available on the desktop only, see [Android](#android).

## DNSTT and VayDNS {#dnstt}

`DNSTT / VayDNS` profiles work with dnstt and VayDNS servers. `Mode` selects the protocol:

| `Mode` | Server |
| --- | --- |
| `DNSTT` (default) | `dnstt-server`, or `vaydns-server -dnstt-compat` |
| `VayDNS` | `vaydns-server` |
| `VayDNS (dnstt-compatible)` | The same servers as `DNSTT`. The client works like `vaydns-client -dnstt-compat` and offers `Max QNAME Length`. |

The tunnel settings:

| Field | Default | What it does |
| --- | --- | --- |
| `Domain` | required | The tunnel domain that is delegated to the server, for example `t.example.com`. |
| `Public Key` | required | The public key of the server: 64 hexadecimal digits, as the server prints it. |
| `Record Type` | `Default` (TXT) | `VayDNS` only. The DNS record type that carries the data from the server: TXT, NULL, CNAME, A, AAAA, MX, NS, SRV or CAA. Use the type of the server. The other modes always use TXT. |
| `Client ID Size` | 2 | `VayDNS` only. The size of the client ID in bytes. It must match `-clientid-size` of the server. |
| `Max QNAME Length` | 101 (`VayDNS`), 253 (`VayDNS (dnstt-compatible)`) | Both VayDNS modes. The longest query name, at most 253. Longer names carry more data, but some resolvers drop them. |
| `Payload per Query` | | Shows how many bytes of tunnel data each query carries. A longer domain leaves less room. The tunnel needs at least 25 bytes; below that, the editor shows an error. Use a shorter domain or a longer `Max QNAME Length`. |

The `Resolvers` group holds the resolvers, see [Resolvers](#resolvers), and `Strategy`, see [How resolvers are picked](#strategy). `Retry truncated replies over TCP` asks each UDP resolver again over TCP when a reply comes back cut off.

The `Advanced` group (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)):

| Field | Default | What it does |
| --- | --- | --- |
| `Idle Timeout` | `2m`, `10s` with `VayDNS` | The session closes when nothing arrives for this long. Keep it in line with the server. |
| `Keepalive Interval` | `10s`, `2s` with `VayDNS` | How often the client sends a keepalive. It must be shorter than `Idle Timeout`. |
| `Queries per Second` | empty, no limit | The most queries the tunnel sends per second. |

### Backend {#dnstt-backend}

The `Backend` group says what runs behind the tunnel on the server. Choose the `Type` that the server uses:

| `Type` | What it does |
| --- | --- |
| `None (chain only)` (default) | The server passes the tunnel on to another proxy server. The editor warns that only a profile chained over this one can use the tunnel. Create a [chain](@/advanced/chains.md#create-chain) with the DNS tunnel profile on top and the profile of the proxy server behind the tunnel below it. |
| `SOCKS5` | A SOCKS5 proxy behind the tunnel. Fill in `Username` and `Password` if it needs them. Tick `UDP over TCP` to carry UDP. |
| `Shadowsocks` | A Shadowsocks server behind the tunnel: `Encryption` and `Password`. Tick `UDP over TCP` to carry UDP. |
| `SSH` | An SSH server behind the tunnel: `User`, and `Password` or `Private Key` (or `Private Key Path` with `Choose File`), `Private Key Password`. `Host Keys`, `Host Key Algorithms` and `Client Version` work as in an `SSH` profile. |

## MasterDNS {#masterdns}

`MasterDNS` profiles work with MasterDnsVPN and StormDNS servers. They carry TCP only. The settings must match the configuration of the server:

| Field | Default | What it does |
| --- | --- | --- |
| `Domains` | required | The tunnel domains of the server (its `DOMAIN` setting), one per line. All of them must lead to the same server. Throne spreads the queries over every pair of domain and resolver. |
| `Mode` | `SOCKS5` | Must match `PROTOCOL_TYPE` of the server. `SOCKS5`: the server connects to the website for you. `TCP / custom chain`: the server sends every connection to its own forward target, so chain the profile of that target below this one, as with `None (chain only)` above. |
| `Encryption` | `xor` | Must match `DATA_ENCRYPTION_METHOD` of the server: `none` (0), `xor` (1), `chacha20` (2), `aes-128-gcm` (3), `aes-192-gcm` (4) or `aes-256-gcm` (5). |
| `Key` | required, except with `none` | The key of the server, the text in its `ENCRYPTION_KEY_FILE`. |

{% alert_warning() %}
MasterDNS encrypts only the queries. The replies of the server are never encrypted or authenticated, `xor` only obfuscates, and `chacha20` is not authenticated. Send only encrypted traffic through the tunnel, for example HTTPS websites, or chain an encrypted proxy below the MasterDNS profile. With `Settings` → `Basic Settings` → `Style` → `Show Config Security` on (Advanced mode only), the `Type` column shows `Weak Cipher`, `Plaintext Replies` (AES-GCM) or `Raw` (`none`) for these profiles.
{% end %}

The `Transfer` group:

| Field | Default | What it does |
| --- | --- | --- |
| `Duplication` | 3 | How many copies of each packet Throne sends, each through another pair of resolver and domain, at most 10. The server may lower it. |
| `Setup Duplication` | 4 | Copies of the packets that open a connection, from `Duplication` up to 12. |
| `Upload Compression`, `Download Compression` | `Default` (off) | `Off`, `zstd`, `lz4` or `zlib`. The server may refuse it. |
| `Base64-encoded replies` | off | Asks the server for Base64 replies, for resolvers that damage binary TXT records. Base64 replies carry a quarter less data. |

Before the session starts, Throne tests every pair of domain and resolver to find out how much data one query (upload) and one reply (download) can carry. The session uses the lowest values of the resolvers that pass. The `MTU` group sets the limits of this test:

| Field | Default | What it does |
| --- | --- | --- |
| `Min Upload`, `Min Download` | 38, 200 | Resolvers below these sizes in bytes are left out. |
| `Max Upload`, `Max Download` | 150, 4000 | The largest sizes that Throne tries, in bytes. The server can also limit them. |

The `Advanced` group (Advanced mode only) holds `MTU Test Retries` (default 2 attempts for each size), `MTU Test Timeout` (default `2s` for each attempt), `MTU Test Parallelism` (default 32 resolvers at a time) and `Drop resolvers below the minimum MTU` (on by default). With that option on and at least 5 resolvers passing, Throne leaves out the few resolvers whose low sizes would slow down the whole session.

The session starts with the first connection, after these tests. If no resolver passes, Throne repeats the tests every 5 seconds. Resolvers that fail are tested again in the background.

## XDNS {#xdns}

Since 1.4.0, `VLESS (Xray)` profiles can use the mKCP transport, which carries the connection over UDP. XDNS is mKCP with an `xdns` mask in Finalmask: Xray sends the mKCP packets as DNS queries through resolvers. The server must run Xray with an mKCP inbound and an `xdns` mask for the same domain.

The easiest way to get an XDNS profile is to import it: a `vless://` link with `type=kcp` and the finalmask in `fm`, the Xray JSON configuration of the server, or a Throne link. `vless://` links with `type=kcp` always become `VLESS (Xray)` profiles, whatever the [Xray VLESS Preference](@/advanced/xray.md#vless-preference) is.

To set up XDNS by hand:

1. Open `Program` → `New profile`, set `Type` to `VLESS (Xray)`, and enter the VLESS settings that the server gives you.
2. Set `Network` to `kcp`. The mKCP fields appear.
3. Click the `Finalmask` button and add an `xdns` mask to the `udp` list, with the tunnel domain and, optionally, resolvers:

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

4. The `Resolvers` row appears below `MTU` and `TTI`. Resolvers that you enter there replace the ones in the mask, see [Resolvers](#resolvers).
5. Click `OK`.

The editor checks the mask. The `xdns` mask must be the last mask in `udp`, and it needs at least one domain. Without resolvers in the mask or in the `Resolvers` row, the editor warns that Xray cannot connect. A DNS-over-HTTPS resolver (`https://…`) in the mask needs a `tlsConfig` with `serverName`, for example `"tlsConfig": {"serverName": "dns.google"}`.

The mKCP fields. Leave a field empty to use Xray's default:

| Field | Default | What it does |
| --- | --- | --- |
| `MTU` | 1350 | The largest packet that mKCP sends, in bytes, from 21 to 8192. |
| `TTI` | 50 | How often mKCP sends, in milliseconds, from 10 to 1000. |
| `Uplink Capacity` | 5 | The most data sent per second, in MB/s. |
| `Downlink Capacity` | 20 | The most data received per second, in MB/s. |
| `CWND Multiplier` | 1 | Multiplies how many packets may be in flight at once. |
| `Max Sending Window` | 2097152 | The most data that waits to be sent or acknowledged, in bytes. At least the `MTU`. |

The last four are in the `Tuning` group (Advanced mode only). In Simple mode, the `Finalmask` button is shown for `kcp` only.

What else to know:

- With `Multiplex` set to `Keep Default`, Throne turns mux on for XDNS profiles, even when `Default Mux On` is off. Without mux, every connection would be its own mKCP session with its own sockets to all resolvers.
- Older mKCP links and configs set a header type (`headerType`) or a `seed`. Newer Xray versions dropped these settings, so Throne turns them into `mkcp-legacy` masks in Finalmask when you import them. It also updates older `xdns` mask formats.
- With `Show Config Security` on, the `Type` column shows `XDNS` for these profiles.

mKCP also works without the `xdns` mask, as a plain UDP transport to an Xray server.

## Resolvers {#resolvers}

Every DNS tunnel profile has resolver settings: the `Resolvers` group for DNSTT and MasterDNS, the `Resolvers` row for XDNS. They have three parts:

- A text box with one resolver per line. Lines that start with `#` are comments.
- `IP list`: an IP list of the IP scanner, for example the results of a resolver scan. Its addresses are used after the lines in the text box. An entry without a port uses port 53.
- `Find resolvers…`, see [Find working resolvers](#find-resolvers).

What a line can contain:

| Line | DNSTT / VayDNS | MasterDNS | XDNS |
| --- | --- | --- | --- |
| `system`: the DNS servers of your system | Yes | Yes | No |
| An IP address, optionally with a port: `8.8.8.8`, `1.1.1.1:53`, `[2001:db8::1]:53` | Yes | Yes | Yes |
| A range: `192.0.2.0/24` | Up to 256 addresses (`/24` or longer, `/120` for IPv6) | Up to 65536 addresses (`/16` or longer, `/112` for IPv6) | Only from an IP list, up to 256 addresses |
| `udp://` or `tcp://` and an address | Yes | No | Yes |
| `tls://` (DNS over TLS) | Yes | No | No |
| `https://` (DNS over HTTPS), with an optional path | Yes | No | Yes |

The default port is 53, and for DNSTT 853 for `tls://` and 443 for `https://`. DNSTT `tls://` and `https://` resolvers use the uTLS `Default Fingerprint`, see [uTLS fingerprint](@/advanced/presets.md#utls). Throne uses each resolver once, and at most 4096 resolvers for DNSTT and 256 for XDNS.

When neither the text box nor the IP list adds a resolver:

- DNSTT and MasterDNS use `system`. Throne reads the system's DNS servers again when the network changes, and skips servers inside the address range of the TUN adapter.
- XDNS keeps the resolvers in the `xdns` mask. As soon as you enter resolvers, they replace the resolvers of the mask.

Throne reads the IP list each time the profile starts, so the next start uses the newest entries. If the IP list is deleted, the profile adds no resolvers from it: the editor shows "This IP list no longer exists, so it adds no resolvers." and the log says so when the profile starts.

### How resolvers are picked {#strategy}

DNSTT and VayDNS: `Strategy` in the `Resolvers` group.

| `Strategy` | Queries go to |
| --- | --- |
| `Default`, `round_robin` | Each resolver in turn. |
| `failover` | The first resolver that works. |
| `fanout` | Every resolver that works, at the same time. |

A resolver that leaves 8 queries unanswered for 2 seconds is skipped for 1 second. Each time this happens again, the pause doubles, up to 30 seconds. After a failure, the session starts again, with pauses from 1 to 30 seconds between attempts.

MasterDNS: `Strategy` in the `Resolvers` group.

| `Strategy` | Queries go to |
| --- | --- |
| `Default`, `least_loss` | The resolver with the least loss. |
| `round_robin` | Each resolver in turn. |
| `random` | A random resolver. |
| `lowest_latency` | The resolver with the lowest latency. |
| `hybrid` | The resolver with the best score of loss and latency. |
| `loss_then_latency` | The lowest latency among the resolvers with the least loss. |
| `least_loss_top_random` | A random resolver among the best 10% by loss. |
| `least_loss_top_round_robin` | Each resolver among the best 10% by loss, in turn. |

A connection whose packets keep being resent moves to another resolver, at most once every 2.5 seconds. Resolvers that keep timing out are left out until they pass the test again.

XDNS: Xray spreads the queries over all resolvers in turn.

## Find working resolvers {#find-resolvers}

Many resolvers do not pass tunnel queries on, or pass them on slowly. A resolver scan tries addresses as resolvers of your profile and keeps the ones that work:

1. Save the profile. `Find resolvers…` stays disabled until you do, because the scan tests the saved profile.
2. Open the profile again and click `Find resolvers…` next to `IP list`. Throne opens the DNS resolver scan of this profile in the IP scanner, or creates one named "Resolvers for" and the name of the profile.
3. On the `Targets` tab, choose the addresses to try in `Base IP list`, for example the default list `Iran DNS resolvers` or `Russia DNS resolvers`, then start the scan. It tries each address as the only resolver of the profile.
4. On the `Results` tab, click `Use as resolvers`. The scan's result list becomes the `IP list` of the profile. The lines that you typed stay and are used first.

If the editor of the profile is open, the editor gets the list instead. Click `OK` there to save it.

For the tests and the result columns, see [DNS resolver scans](@/guides/testing.md#dns-scan).

## Import and share {#import}

Throne imports these formats from the clipboard, files, QR codes and subscriptions:

| Format | Result |
| --- | --- |
| `dnst://` links | A `DNSTT / VayDNS` profile, or a `MasterDNS` profile when the tunnel type in the link is `masterdns`. |
| `slipnet://` links of SlipNet versions 16 to 28 | A `DNSTT / VayDNS` profile. NoizDNS profiles become plain DNSTT, which their server accepts, but without NoizDNS's DPI evasion. |
| `masterdns://` and `stormdns://` links | A `MasterDNS` profile. Throne reads the `v1/…` form with a client configuration, the `v1?domains=…` form, and the profile format of WhiteDNS and Hidden-Node. |
| sing-box JSON with `dnstt` or `masterdns` outbounds | `DNSTT / VayDNS` and `MasterDNS` profiles. |
| `vless://` links with `type=kcp`, and Xray JSON with the `kcp` network | `VLESS (Xray)` profiles. With an `xdns` mask in the finalmask, they are XDNS profiles. |
| Throne links (`throne://add/…`) | Any of these, with all settings. |

What to know about the import:

- A `dnst://` link names the service behind the tunnel: `socks`, `ssh`, `shadowsocks`, or `custom` for `None (chain only)`. MasterDNS takes only `socks` (`SOCKS5`) and `custom` (`TCP / custom chain`).
- Encrypted SlipNet links (`slipnet-enc://`, `slipnet-bundle-enc://`) cannot be read. Export the profile from SlipNet without encryption.
- If a link also carries a proxy to run over the tunnel, Throne warns about it. Import that proxy separately and chain it below the DNS tunnel profile.
- An IP list that a Throne link names belongs to another installation, so Throne does not take it over.

To share a profile, right-click it → `Share`. DNSTT and MasterDNS profiles give a `dnst://` link, XDNS profiles a `vless://` link. These links do not contain your resolvers or your IP list, and for MasterDNS only the first domain. The Throne link keeps every setting except the IP list. See [Share links](@/reference/protocols.md#share-links).

## Testing {#testing}

URL tests and IP tests work with DNS tunnel profiles. The tunnel opens its session on the first request, which takes many slow queries. Throne therefore gives the first request 30 more seconds for DNSTT and XDNS profiles, and 90 more seconds for MasterDNS profiles. A chain with several tunnels gets the sum. The latency shown comes from the second request, over the open session. See [URL test](@/guides/testing.md#url-test).

If a test fails:

- Check the `Logs` tab for the reason.
- Check `Domain` and `Public Key` (DNSTT) or `Domains`, `Encryption` and `Key` (MasterDNS) against the server.
- Try other resolvers, or run a [resolver scan](#find-resolvers).

## Android {#android}

Throne for Android has no `DNSTT / VayDNS` or `MasterDNS` profile types, and its `VLESS (Xray)` profiles have no `kcp` network. DNS tunnels and their links work on the desktop only.
