+++
title = "Privacy & Network Requests"
description = "What Throne sends to subscription providers, every request it makes by itself, and who can use its local ports."
weight = 40
toc = true
+++

Throne sends your traffic where your profiles and routing rules say. On its own, it sends only what this page lists: no usage statistics, no logs and no crash dumps.

## HWID and device information {#hwid}

If you turn it on (off by default), Throne sends these headers with subscription requests, never with other requests:

- `x-hwid`: the Windows `MachineGuid`, the Mac's hardware UUID, or on Linux `/etc/machine-id` (or `/var/lib/dbus/machine-id`; without either, a random ID that Throne keeps in the `hwid` file of the config folder).
- `x-device-os`: `Windows`, `Linux` or `macOS`.
- `x-ver-os`: the Windows or macOS version, or the Linux kernel version.
- `x-device-model`: the computer model (and motherboard) on Windows, the Linux distribution, such as `Ubuntu 24.04.1 LTS`, or the macOS name and version.

Turn it on in `Settings` → `Basic Settings` → `Subscription` → `Enable sending HWID, device model, and OS version when updating subscription` (Advanced mode only). Every subscription request also sends the User-Agent `Throne/<version>`, unless you set another one. To replace values or set them per group, see [User-Agent and HWID](@/guides/subscriptions.md#user-agent-and-hwid).

**Android:** the same switch is in `Settings` → `Subscriptions`. It sends the Android ID (or a random ID created once), `Android`, the Android version and the device model. The default User-Agent is `Throne/Android/<version>`.

## Network requests {#network-requests}

Requests marked "`Use proxy` rule" go out directly, unless `Use proxy` (`Settings` → `Basic Settings` → `Miscellaneous`) or `System Proxy` is on: then they go through the local proxy port, where your routing profile decides, and fail while no profile runs.

| Request | When | Destination | Route |
| --- | --- | --- | --- |
| Subscription update | Adding or updating a subscription, manually or on schedule | Your provider's URL | `Use proxy` rule |
| Remote routing profile | Adding or updating one, manually or on schedule | The profile's URL, through the `Remote Rule-set Mirror` for `raw.githubusercontent.com` | `Use proxy` rule |
| Routing profile list | `Routing` → `Download Profiles` | throneproj/routeprofiles on GitHub, through the mirror | `Use proxy` rule |
| Rule-sets | A profile needs one that is not cached, and about once a day while a profile runs | GitHub through the `Remote Rule-set Mirror` (`jsDelivr(Cloudflare)` by default), or a `.srs` URL you added | Through the default outbound of the routing profile (directly if it is `block`) |
| Exit country lookup | After every start of a profile (on by default) | The `Lookup service`; `Automatic` tries `api.ip2location.io`, `ip-api.com`, `ipwho.is`, `ipinfo.io` and `www.cloudflare.com/cdn-cgi/trace` in turn | Always the local proxy port |
| URL test | Manual or automatic URL tests | The `Latency Test URL` (`http://cp.cloudflare.com/` by default) | Through each tested profile |
| IP test | Out IP tests | `https://api.ip2location.io/` | Through each tested profile |
| Speed test | Speed tests | Speedtest.net servers, or in `Simple Download` mode the `Simple Download URL` (`http://cachefly.cachefly.net/1mb.test` by default) | Through each tested profile |
| Connectivity check | While an auto selector runs, only with a `Direct Test URL` or `Connectivity URL` set | That URL | Direct |
| Domain lookup | Resolving the domains of your servers | DNS queries to your `Direct DNS` server (your system's resolver by default) | Direct |
| Update check | `Check For Update`; downloads only after you click `Update` | `api.github.com` (releases and changelog), then the release file on GitHub | `Use proxy` rule |
| WARP registration | Generating a WARP config or identity | `api.cloudflareclient.com`, or the `Registration Domains…` you set | `Use proxy` rule |
| Xray geo files | An Xray config needs `geoip.dat` or `geosite.dat` and you agree, or you click `Download` | The `GeoIP Asset URL` and `GeoSite Asset URL` (GitHub by default) | `Use proxy` rule |
| IP scan | Scans in `IP Scanner` | A TCP check to public DNS servers such as `1.1.1.1:443`, then the addresses you scan | Direct, even in TUN mode |
| IP list update | Updating an IP list with a URL or rule-set source, manually or on schedule | The list's URL; built-in lists come from GitHub through the mirror | `Use proxy` rule |
| Web dashboard | `Open Web dashboard`, only in builds without a built-in one (official builds have one) | GitHub | Always the local proxy port |
| WebDAV backup | Uploading, restoring, deleting or testing; never automatically | Your `Server URL` and `Folder`: the backup file and the backup list `throne_backups.json` | `Use proxy` rule; always the local proxy port with `Connect through the proxy` on |
| NTP | Only with `NTP Settings` on | The NTP server you enter | The `outbound` you choose (`direct` by default) |

The exit country lookup sends Throne's User-Agent, over plain HTTP to ip-api.com and HTTPS to the others. With the `Default` routing profile, the service sees your proxy's address, not yours. `Settings` → `Basic Settings` → `Common` → `Look up exit country` turns it off (on Android, `Settings` → `Testing`).

**WebDAV backups** are not encrypted and hold what you ticked under `Create Backup`, such as server passwords and subscription links. The backup list records the device name, a random device ID, the platform and the Throne version. `Username` and `Password` go with HTTP basic authentication, unencrypted over `http://`, and are never put in a backup. See [WebDAV backups](@/guides/backup.md#webdav).

Update checks and downloads always verify GitHub's TLS certificate, even with `Ignore TLS errors` on.

Requests from a [Throne API](@/advanced/api.md) client are the same requests with the same routes. The web UI of `thronectl` loads nothing from other sites. [Headless Throne](@/headless/_index.md) makes no exit country lookup and downloads missing Xray geo files without asking.

DNS queries for your apps' traffic go to the servers in `Routing Settings` → `DNS`, or to those of the active routing profile, see [DNS](@/guides/dns.md).

**Android:**

- Its own requests go through the local proxy while a profile runs if `Settings` → `Subscriptions` → `Use proxy` is on, or in `Proxy only` mode. With no profile running, subscription updates and WARP registration fail if `Use proxy` is on, else go out directly. Update checks, routing profile downloads and the rule-set list always go out directly.
- `Check for updates daily` (off by default) checks GitHub once a day.
- Xray geo files are downloaded from GitHub without asking when an Xray config needs them.
- WebDAV backups never go through the local proxy.

## Local proxy port {#local-proxy}

While a profile runs, Throne listens for SOCKS5 and HTTP connections on a local proxy port, `127.0.0.1:2080` by default. Only programs on your computer can reach `127.0.0.1`, but all of them can use the port without a password. In `Settings` → `Basic Settings` → `Common` → `Inbound Settings`, a `Listen Address` of `::` or `0.0.0.0` opens the port to other devices, `Enable Authorization` (off by default) requires the `Inbound Username` and `Inbound Password`, and `Disable Mixed Inbound` turns the port off.

`Allow other devices to connect` sets `Listen Address` to `::`, so every device that can reach your computer can use your proxy. Turn on `Enable Authorization` with it, see [LAN sharing](@/guides/proxy_modes.md#lan-sharing).

| Other local port | Default | Who can reach it |
| --- | --- | --- |
| `DNS Server Port` | `5533` | Only this computer, unless you tick `Allow other devices to connect`, see [DNS server for other devices](@/guides/dns.md#dns-server). |
| Clash API | Off | Its `Listen Address` (`127.0.0.1` by default). Set a `Secret`. |
| sing-box API / Dashboard | Off | Only this computer, with a random `Secret`. |
| Throne API | Off; port `9095` when on | Only this computer; with `Listen on` set to `Local network`, every device that can reach it, limited by `Allowed addresses`. Clients need the key, see [Throne API & thronectl](@/advanced/api.md#security). |

- When a profile uses Xray, the core also opens internal ports on `127.0.0.1`, each protected by a random password. Throne and its core talk through a local socket, not a network port.
- [Headless Throne](@/headless/_index.md) turns the Throne API on at its first start, on `127.0.0.1:9095`, with a key in a file that only its user can read.
- The web UI of `thronectl` has its own port, `127.0.0.1:9096` by default, and asks for a password, see [Web UI](@/headless/web.md#security).
- **Android:** `Settings` → `Inbound` has `Proxy port`, `Allow connections from the LAN` (off by default) and `Enable authorization`. The sing-box API is on by default, on `127.0.0.1:9091` with a random secret. There is no Throne API.

## This website {#website}

This documentation site counts visits with Yandex Metrika: page views, the referring page, clicks on the page and on links, and how long visitors stay. Session recording is off. The Throne apps do not include this counter.
