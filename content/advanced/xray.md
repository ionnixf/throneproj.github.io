+++
title = "sing-box vs Xray"
description = "Which core runs your profiles, how the Xray VLESS Preference works, and how to fix Reality errors with newer Xray servers."
weight = 10
toc = true
+++

Most profiles run on sing-box. Some VLESS profiles and all Xray custom configs run on Xray, which Throne also contains. In Simple mode, the `Core` and `Logging` tabs and most other settings on this page are hidden, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

## Two cores in one {#two-cores}

sing-box always runs and handles the local proxy port, TUN mode, routing and DNS. Xray starts as a helper only when the running profile, a hop of its chain or the outbound of a routing rule needs it. sing-box hands that traffic to Xray internally, so TUN mode, routing profiles and DNS settings work the same with both cores.

Profiles whose `Type` column shows `VLESS (Xray)`, `Custom Xray … Outbound` or `Custom Xray Config` run on Xray; all others, including `VLESS`, run on sing-box. A `vless://` link that uses an Xray-only feature is always imported as `VLESS (Xray)`, whatever the [Xray VLESS Preference](#vless-preference):

- the XHTTP transport (`type=xhttp`) and its `extra` settings,
- VLESS encryption (an `encryption` value other than `none`),
- Finalmask,
- the mKCP transport (`type=kcp`),
- a TCP (`raw`) transport with an HTTP header together with TLS.

mKCP carries the connection over UDP (`kcp` in `Network` in the profile editor). With an `xdns` mask in Finalmask, it becomes the DNS tunnel [XDNS](@/advanced/dns_tunnels.md#xdns). [XDRIVE](#xdrive) is Xray-only too, but has no link format.

To see the configs that Throne generates for a profile, right-click it → `Share` → `Export config`. Xray messages appear in the `Logs` tab; set their detail in `Settings` → `Basic Settings` → `Logging` → `Xray Log level`.

## Choose the core for VLESS links {#vless-preference}

`Settings` → `Basic Settings` → `Core` → `Xray VLESS Preference` decides which core a `vless://` link gets when you import it:

| Option | Links imported as `VLESS (Xray)` |
| --- | --- |
| `XHTTP Only` | Only links that need an Xray-only feature (see above). Reality links stay on sing-box. |
| `XHTTP And Reality` (default) | Those links, plus every Reality link. |
| `All VLESS` | Every `vless://` link. |

Other sources:

- **Clash / Mihomo YAML:** a VLESS entry gets Xray when it uses XHTTP or VLESS encryption, or Reality over TCP, WebSocket or gRPC (unless the preference is `XHTTP Only`). Other VLESS entries stay on sing-box, also with `All VLESS`.
- **Xray JSON:** every VLESS outbound becomes `VLESS (Xray)`.
- **sing-box JSON:** VLESS outbounds stay on sing-box.
- **Profile editor:** you choose `VLESS` or `VLESS (Xray)` in `Type`.

{% alert_info() %}
Updating Throne keeps your saved preference. If you have used Throne since before 1.2.0, it may still be `XHTTP Only`.
{% end %}

### After you change the preference {#after-change}

The preference affects only profiles that you import after the change. To move existing profiles to the other core:

- **Subscription group:** update the subscription (`Groups` → `Update subscription`). Throne replaces the affected profiles with new ones, so their old test results are gone.
- **Profile added from a link:** add the link again, then delete the old profile.
- **Profile you created by hand:** its type cannot be changed. Create a new profile with the other type.

If `Keep working profiles` is on in the group's subscription settings, old working profiles can stay next to the new ones. Delete them yourself.

## Reality errors with newer Xray servers {#reality}

Newer Xray servers reject the Reality client of sing-box. The profile does not connect, and the log shows `reality verification failed` ([#1780](https://github.com/throneproj/Throne/issues/1780)):

- Xray 26.7.11 to 26.7.28 accept only clients that report a recent Xray version.
- Xray 26.9.9 and newer require a key exchange (X25519MLKEM768) that the Reality client of sing-box does not offer.

Run the profile on Xray instead:

1. Open `Settings` → `Basic Settings` → `Core`.
2. Set `Xray VLESS Preference` to `XHTTP And Reality` or `All VLESS` and click `OK`.
3. Update the subscription, or add the link again (see [After you change the preference](#after-change)).
4. Check that the `Type` column now shows `VLESS (Xray)`.

From a Clash / Mihomo YAML subscription, only Reality entries over TCP, WebSocket or gRPC move to Xray. For other transports, use the subscription as a list of links, if your provider offers it.

If you run an Xray server from 26.7.11 to 26.7.28 yourself, you can instead add `"minClientVer": "0.0.0"` to the `realitySettings` of its VLESS inbound so older clients can connect. This does not help with 26.9.9 and newer.

## Trust system certificates {#system-ca}

Xray checks server certificates only against its built-in root certificates, so a server whose certificate comes from a root that you installed yourself, such as a company root or a self-signed one, fails. Tick `Settings` → `Basic Settings` → `Security` → `Trust System Certificates in Xray` (off by default) to make Xray also trust your system's certificates.

## XDRIVE {#xdrive}

With the XDRIVE transport, Throne and the server do not connect to each other. They exchange the traffic as files in a storage that both can reach: a Google Drive folder, an S3 bucket, another storage service with an HTTP API, or a folder on this computer. Use it only with a server that is set up for XDRIVE, with the settings that its operator gives you.

XDRIVE has no link format, because a link would carry the storage secrets. Import the server's Xray JSON or a Throne link (`throne://add/…`) instead. Sharing an XDRIVE profile gives its Throne link.

To set it up in the profile editor, choose `xdrive` in `Network`. XDRIVE needs these VLESS settings:

- `Encryption`: the VLESS encryption string that the server gives, starting with `mlkem768x25519plus`.
- `Flow`: empty.
- `Multiplex`: anything but `Disabled`. With `Keep Default`, Throne turns mux on for XDRIVE. See [Multiplex](@/advanced/presets.md#multiplex).

In the `Storage` group, choose the `Service` and fill in what the operator gives you:

- `google-drive` (default): `Folder` (the shared folder ID), `Client ID`, `Client Secret` and `Refresh Token`.
- `s3`, any S3-compatible storage such as AWS S3, MinIO or Cloudflare R2: `Endpoint`, `Bucket`, `Folder` (the key prefix), `Access Key ID` and `Secret Access Key`, and optionally `Region` (empty means `us-east-1`) and `Session Token`. `Path-Style Addressing` (on by default) puts the bucket in the URL path instead of the host name.
- `template`, any storage with an HTTP API: `Template` (the API operations as JSON) and `Secrets` (comma-separated values read as `{secret0}`, `{secret1}` and so on).
- `local`: `Folder`, a folder on this computer.

`Front Address` and `Front Port` (default 443) are optional: Throne connects to the storage API at this address while TLS still names the API host, a form of domain fronting. Leave the `Tuning` group empty to use Xray's defaults.

## Xray in chains {#xray-in-chains}

A chain can mix sing-box and Xray profiles, with two limits:

- All Xray hops must be next to each other: sing-box hops, then Xray hops, then sing-box hops. Otherwise the profile does not start, and the log shows "Too many core transitions".
- A `Custom (Xray config)` profile must be the top hop, and it cannot share a chain with other Xray hops.

Group front and landing proxies, and WARP, count as hops too. See [Proxy Chains & Custom Configs](@/advanced/chains.md#chains).

## Xray geo files {#geo-files}

A `Custom (Xray config)` profile whose own `routing` rules use `geoip:` or `geosite:` values needs the files `geoip.dat` and `geosite.dat`. When they are missing, Throne offers to download them: click `Yes`, then start the profile again.

Choose their source in `Settings` → `Basic Settings` → `Miscellaneous` → `Xray Geo Assets`: Loyalsoldier (global and China, the default), Chocolate4U (Iran), runetfreedom (Russia) or v2fly. Take both files from the same source. If Throne shows "Geo asset missing category", choose a source that has this category and click `Download` for that file.

## Android {#android}

- The preference is in `Settings` → `Core` → `Xray VLESS preference`, with `XHTTP only`, `XHTTP and Reality` (default) and `All VLESS`.
- XDRIVE: choose `XDRIVE` in `Network`. The fields are in `XDRIVE settings` and `XDRIVE tuning`, and `Secrets` takes one value per line.
- mKCP: choose `mKCP` in `Network`. The fields are in `mKCP settings` and `mKCP tuning`. For XDNS, add the `xdns` mask in `Finalmask`.
- `Settings` → `Core` → `Trust system certificates in Xray` trusts the root certificates that come with Android, not the ones that you installed yourself.
- Throne for Android downloads `geoip.dat` and `geosite.dat` by itself when an Xray config needs them. Choose their source in `Settings` → `Core` → `Xray geo assets`.
