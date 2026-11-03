+++
title = "sing-box vs Xray"
description = "Which core runs your profiles, how the Xray VLESS Preference works, and how to fix Reality errors with newer Xray servers."
weight = 10
toc = true
+++

Throne's core is built on sing-box, and it also contains Xray. Most profiles run on sing-box. Some VLESS profiles and all Xray custom configs run on Xray. This page explains how Throne chooses the core, how to change that choice for VLESS links, and how to fix Reality connection errors.

In Simple mode, most settings on this page are hidden: the `Logging` and `Core` tabs, `Xray Geo Assets` and `Trust System Certificates in Xray` in `Basic Settings`, the `Tuning` groups of XDRIVE and mKCP, and `Share` → `Export config`. See [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

## Two cores in one {#two-cores}

sing-box always runs. It handles the local proxy port, TUN mode, routing rules and DNS. Xray runs as a helper inside the same core. It starts only when the running profile needs it, for example when the profile, a hop of its chain or the outbound of a routing rule runs on Xray. sing-box hands that traffic to Xray over an internal local connection, so TUN mode, routing profiles and DNS settings work the same with both cores.

These profiles run on Xray:

| `Type` column | Where it comes from |
| --- | --- |
| `VLESS (Xray)` | `vless://` links, depending on the [Xray VLESS Preference](#vless-preference); VLESS outbounds in Xray JSON; the `VLESS (Xray)` type in the profile editor. |
| `Custom Xray … Outbound` | The `Custom (Xray outbound)` type; other outbounds in imported Xray JSON. |
| `Custom Xray Config` | The `Custom (Xray config)` type; imported lists of complete Xray configs. |

All other profiles, including `VLESS`, run on sing-box.

Some VLESS features exist only in Xray. A `vless://` link that uses one of them is always imported as `VLESS (Xray)`, whatever the preference is set to:

- the XHTTP transport (`type=xhttp`) and its `extra` settings,
- VLESS encryption (an `encryption` value other than `none`),
- Finalmask,
- the mKCP transport (`type=kcp`, since 1.4.0),
- a TCP (`raw`) transport with an HTTP header together with TLS.

The XDRIVE transport (since 1.4.0) also exists only in Xray, but it has no link format. See [XDRIVE](#xdrive).

mKCP carries the connection over UDP. To use it, choose `kcp` in `Network` in the profile editor. With an `xdns` mask in Finalmask, mKCP becomes XDNS, a DNS tunnel. See [XDNS](@/advanced/dns_tunnels.md#xdns) for the mKCP fields.

Xray messages appear in the same `Logs` tab. Their detail level is set in `Settings` → `Basic Settings` → `Logging` → `Xray Log level`.

## Choose the core for VLESS links {#vless-preference}

`Settings` → `Basic Settings` → `Core` → `Xray VLESS Preference` decides which core a `vless://` link gets when you import it:

| Option | Links imported as `VLESS (Xray)` |
| --- | --- |
| `XHTTP Only` | Only links that need an Xray-only feature (see the list above). Reality links stay on sing-box. |
| `XHTTP And Reality` (default) | The links above, plus every Reality link. |
| `All VLESS` | Every `vless://` link. |

All other `vless://` links become `VLESS` profiles that run on sing-box.

The preference applies only to `vless://` links:

- **Clash / Mihomo YAML:** a VLESS entry gets Xray when it uses XHTTP or VLESS encryption. Since 1.4.0, a Reality entry over TCP, WebSocket or gRPC gets Xray too, unless the preference is `XHTTP Only`. Other VLESS entries stay on sing-box, also with `All VLESS`.
- **Xray JSON:** every VLESS outbound becomes `VLESS (Xray)`.
- **sing-box JSON:** VLESS outbounds stay on sing-box.
- **Profile editor:** you choose `VLESS` or `VLESS (Xray)` in the `Type` list yourself.

{% alert_info() %}
`XHTTP And Reality` became the default in 1.2.0, when `All VLESS` was also added. Updating Throne keeps your saved value. If you have used Throne since before 1.2.0, you may still have `XHTTP Only`.
{% end %}

### After you change the preference {#after-change}

The preference affects only profiles that you import after the change. Existing profiles keep their core. To move them to the other core:

- **Subscription group:** update the subscription (`Groups` → `Update subscription`, or right-click the group tab → `Update subscription`). Throne replaces the affected profiles with new ones of the other type, so their old test results are gone.
- **Profile added from a link:** add the link again, then delete the old profile.
- **Profile you created by hand:** its type cannot be changed. Create a new profile with the other type.

If `Keep working profiles` is on in the group's `Advanced` subscription settings, old working profiles can stay next to the new ones. Delete them yourself.

### Which core does a profile use? {#which-core}

Look at the `Type` column of the profile list. `VLESS` runs on sing-box. `VLESS (Xray)`, `Custom Xray … Outbound` and `Custom Xray Config` run on Xray.

To also see the security layer, such as Reality, in that column, turn on `Settings` → `Basic Settings` → `Style` → `Show Config Security`. To see the configs that Throne generates for a profile, right-click it → `Share` → `Export config` (since 1.4.0). The window has a `sing-box` tab and, when the profile uses Xray, an `Xray` tab. `Copy` copies the tab you see. Up to 1.3.2, there were separate `Export Sing-box config` and `Export Xray config` items.

## Reality errors with newer Xray servers {#reality}

Newer Xray servers reject the Reality client of sing-box. The profile then does not connect, and the log shows `reality verification failed` ([#1780](https://github.com/throneproj/Throne/issues/1780)).

- Xray 26.7.11 to 26.7.28 accept only clients that report a recent Xray version.
- Xray 26.9.9 and newer require a newer key exchange (X25519MLKEM768) that the Reality client of sing-box does not offer.

The fix is to run the profile on Xray:

1. Open `Settings` → `Basic Settings` → `Core`.
2. Set `Xray VLESS Preference` to `XHTTP And Reality` or `All VLESS`.
3. Click `OK`.
4. Update the subscription, or add the link again (see [After you change the preference](#after-change)).
5. Check that the `Type` column now shows `VLESS (Xray)`.

Up to 1.3.2, this did not help for Reality entries from a Clash / Mihomo YAML subscription, because they always stayed on sing-box. Since 1.4.0, they follow the preference when they use TCP, WebSocket or gRPC. For other transports, use the subscription as a list of links, if your provider offers it.

If you run an Xray server from 26.7.11 to 26.7.28 yourself, you can also add `"minClientVer": "0.0.0"` to the `realitySettings` of its VLESS inbound. This lets older clients connect. It does not help with Xray 26.9.9 and newer.

## Old Xray settings {#old-settings}

Newer Xray versions refuse some old settings, and a profile or config that uses them does not start. Since 1.4.0, the Xray in Throne accepts them again:

- VLESS and Trojan without TLS or other encryption to a server on the internet. Newer Xray allows this only for private addresses.
- `allowInsecure` in the TLS settings of an Xray config.
- The old Shadowsocks ciphers `aes-128-cfb`, `aes-256-cfb`, `chacha20` and `chacha20-ietf`, and `none` or `plain`, in Xray configs.
- The VMess security types `none` and `zero` in Xray configs.

Xray's warnings about deprecated features appear only when `Xray Log level` is `debug`. These settings are less safe or easier to detect. Use them only for servers that need them.

## Trust system certificates {#system-ca}

Xray checks server certificates only against the root certificates built into it. A server whose certificate comes from a root that you installed yourself, for example a company root or a self-signed one, then fails this check. Since 1.4.0, tick `Settings` → `Basic Settings` → `Security` → `Trust System Certificates in Xray` (off by default) to make Xray also trust the certificates installed in your system.

The option applies to the TLS settings of every Xray profile and of every outbound in a `Custom (Xray outbound)` or `Custom (Xray config)` profile, including the download connection of XHTTP. An outbound that sets `useSystemCA` itself keeps its own value. sing-box profiles do not use this option.

## XDRIVE {#xdrive}

Since 1.4.0, `VLESS (Xray)` profiles can use the XDRIVE transport. With XDRIVE, Throne and the server do not connect to each other. They exchange the traffic as files in a storage that both of them can reach: a Google Drive folder, an S3 bucket, another storage service with an HTTP API, or a folder on this computer. Use it only with a server that is set up for XDRIVE, with the settings that its operator gives you.

XDRIVE has no link format, because a link would carry the storage secrets. Import the server's Xray JSON configuration instead, or a Throne link (`throne://add/…`). A `vless://` link with `type=xdrive` is refused with "XDRIVE has no share-link format. Import the server's JSON configuration instead." When you share an XDRIVE profile, Throne gives you its Throne link.

To set up XDRIVE in the profile editor, choose `xdrive` in `Network`. XDRIVE works only with these VLESS settings, and the editor shows an error for any other value:

- `Encryption`: the VLESS encryption string that the server gives, starting with `mlkem768x25519plus`.
- `Flow`: empty.
- `Multiplex`: `Keep Default`, `Enabled` or `Custom`, not `Disabled`. With `Keep Default`, Throne turns mux on for XDRIVE even when `Default Mux On` is off. It also ignores mux settings that would send traffic outside the mux, such as a `Concurrency` of `-1`. See [Multiplex](@/advanced/presets.md#multiplex).

The `Storage` group:

| Field | What it does |
| --- | --- |
| `Service` | `google-drive` (default): a Google Drive folder. `s3`: a bucket of any S3-compatible storage, for example AWS S3, MinIO or Cloudflare R2. `template`: any storage service whose HTTP API `Template` describes. `local`: a folder on this computer. |
| `Folder` | `google-drive`: the ID of the folder that both ends share. `s3`: the key prefix in the bucket that both ends share. `local`: the path of the folder. `template`: the value that the template reads as `{folder}`. Required for `google-drive`, `s3` and `local`. |
| `Client ID`, `Client Secret`, `Refresh Token` | `google-drive` only, all three required: the OAuth client, and the refresh token of the Google account that owns the folder. |
| `Endpoint` | `s3` only, required: the URL of the S3 API, for example `https://s3.us-east-1.amazonaws.com` or a MinIO or R2 endpoint. It must start with `http://` or `https://` and contain no credentials, query or fragment. |
| `Region` | `s3` only: the region that requests are signed for. Empty means `us-east-1`. |
| `Bucket` | `s3` only, required: the bucket that both ends share. |
| `Path-Style Addressing` | `s3` only, on by default: puts the bucket in the URL path. When it is off, the bucket becomes part of the host name of the endpoint. This needs an endpoint with a domain name, not an IP address, and a bucket name of lowercase letters, digits, dots and hyphens. |
| `Access Key ID`, `Secret Access Key`, `Session Token` | `s3` only: the S3 credentials. The first two are required. `Session Token` is optional, for temporary credentials. |
| `Secrets` | `template` only: values that the template reads as `{secret0}`, `{secret1}` and so on, separated by commas. |
| `Template` | `template` only, required: the auth, put, get, list and delete operations of the storage API, as JSON. Click the button to edit it. |
| `Front Address`, `Front Port` | Optional, not for `local`: Throne connects to the storage API at this address while TLS still names the API host, a form of domain fronting. `Front Port` defaults to 443 and is used only together with `Front Address`. |

The `Tuning` group holds timing and size settings. Leave a field empty to use Xray's default, which the field shows:

| Field | Xray default | What it sets |
| --- | --- | --- |
| `Segment Bytes` | 524288 | The largest upload per file, in bytes. Xray allows at most 16 MiB. |
| `Flush Interval` | 20 | How long writes are gathered before an upload, in milliseconds. |
| `Poll Interval` | 50 | The shortest wait between checks for new files, in milliseconds. |
| `Max Poll Interval` | 500 | The longest wait between checks while the connection is idle, in milliseconds. |
| `Eager Window` | 2000 | How long after traffic the checks stay at the shortest wait, in milliseconds. |
| `Hole Timeout` | 30000 | How long a missing segment is waited for before the connection fails, in milliseconds. |
| `Session TTL` | 300 | How long an idle session lives before its files are cleaned up, in seconds. |
| `Concurrency` | 8 | Parallel storage requests per connection. Xray allows at most 64. |

## Xray in chains {#xray-in-chains}

A chain can mix sing-box and Xray profiles, with two limits:

- All Xray hops must be next to each other. Traffic can switch from sing-box to Xray and back only once: sing-box hops, then Xray hops, then sing-box hops. Otherwise the profile does not start, and the log shows "Too many core transitions".
- A `Custom (Xray config)` profile must be the top hop, and it cannot share a chain with other Xray hops, because a chain can use only one Xray instance.

Group front and landing proxies, and WARP, count as hops too. See [Proxy Chains & Custom Configs](@/advanced/chains.md#chains).

## Xray geo files {#geo-files}

A `Custom (Xray config)` profile can have its own `routing` rules with `geoip:` or `geosite:` values. For these, Xray needs the files `geoip.dat` and `geosite.dat`. Other profiles and Throne's routing profiles do not use these files; they use rule-sets.

When the files are missing, Throne shows "Geo asset files required" and offers to download them. Click `Yes`, wait for "Geo assets installed", then start the profile again.

The download addresses are in `Settings` → `Basic Settings` → `Miscellaneous` → `Xray Geo Assets`, in the fields `GeoIP Asset URL` and `GeoSite Asset URL`. Their lists offer the addresses of four sources: Loyalsoldier (global and China, the default), Chocolate4U (Iran), runetfreedom (Russia) and v2fly. Take both files from the same source. Each `Download` button downloads that file again and replaces the old one.

If a rule names a category that the file does not contain, Throne shows "Geo asset missing category". Choose a source that has this category, then click `Download` for that file.

## Android {#android}

- The same setting is in `Settings` → `Core` → `Xray VLESS preference`, with `XHTTP only`, `XHTTP and Reality` (default) and `All VLESS`. It also applies only when you import, so update the subscription after you change it.
- The profile types are `VLESS` and `VLESS (Xray)`, and the limits for Xray in chains are the same.
- Since 2.0.4, `VLESS (Xray)` profiles can use XDRIVE too: choose `XDRIVE` in `Network`. The fields are in `XDRIVE settings` and `XDRIVE tuning`, and `Secrets` takes one value per line. The `s3` service is desktop only.
- The mKCP transport and XDNS are desktop only.
- Since 2.0.4, `Settings` → `Core` → `Trust system certificates in Xray` does the same as on the desktop. It trusts the root certificates that come with Android, not the ones that you installed yourself.
- Since 2.0.2, Throne for Android downloads `geoip.dat` and `geosite.dat` by itself when an Xray config with `geoip:` or `geosite:` rules starts or is tested, and the notification shows the progress. Choose where they come from in `Settings` → `Core` → `Xray geo assets`: `Source` offers the same four sources as the desktop app, or `Custom` addresses. Tap a file under `Files` to download it again, or tap `Download now` for both.
