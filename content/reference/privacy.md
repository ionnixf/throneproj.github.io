+++
title = "Privacy & Network Requests"
description = "What Throne sends to subscription providers, every network request the app makes by itself, and who can use its local proxy port."
weight = 40
toc = true
+++

Throne sends your traffic where your profiles and routing rules say. This page covers what Throne sends on its own: device information for subscriptions (HWID), the network requests the app makes by itself, and who can use its local proxy port and the Throne API. Throne does not send usage statistics, and it does not upload logs or crash dumps.

## HWID and device information {#hwid}

Some providers want to know which device requests a subscription. Throne can send four headers with subscription requests. This is off by default.

| Header | Windows | Linux | macOS |
| --- | --- | --- | --- |
| `x-hwid` | The Windows `MachineGuid` | The content of `/etc/machine-id` (or `/var/lib/dbus/machine-id`). Without either, an ID that Throne creates once (since 1.4.0) | The hardware UUID of the Mac |
| `x-device-os` | `Windows` | `Linux` | `macOS` |
| `x-ver-os` | The Windows version, for example `10.0.26100` | The kernel version | The macOS version, for example `14.5` |
| `x-device-model` | The computer model (and motherboard) reported by Windows | The name of the distribution, for example `Ubuntu 24.04.1 LTS` | The macOS name and version, for example `macOS Sonoma (14.5)` |

To turn it on:

1. Open `Settings` → `Basic Settings` → `Subscription`.
2. Turn on `Enable sending HWID, device model, and OS version when updating subscription`. This and the other settings below appear only in Advanced mode (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).
3. Click `OK`.

Hover over the checkbox to see the values that Throne found on your computer.

**Linux:** since 1.4.0, on a system without a valid machine ID, such as a container, Throne creates a random ID once and keeps it in the file `hwid` in the config folder, readable only by you, so that the ID stays the same. If you delete the file, Throne creates a new ID. If it cannot write the file, it sends no ID. See also [Device ID](@/headless/_index.md#device-id) for headless Throne.

- Throne sends these headers only with subscription requests, never with any other request.
- `Custom System Parameters (optional)` replaces values that Throne sends. Use the form `hwid=value,os=value,osVersion=value,model=value` and list only the values you want to replace.
- Each subscription group can decide for itself: choose `Groups` → `Edit current Group`, and click `Advanced` in the `Subscription` section. `Send HWID` is `Keep Default` (follow the setting above), `On` or `Off`. The `HWID`, `OS`, `OS Version` and `Device Model` fields replace the values for this group only.
- Every subscription request also sends a User-Agent: `Throne/<version>`, for example `Throne/1.3.1`, unless you set another `User Agent` in the same tab or for the group.

See [User-Agent and HWID](@/guides/subscriptions.md#user-agent-and-hwid) for when a provider needs these settings.

**Android:** `Settings` → `Subscriptions` has the same switch (off by default) and `Custom HWID parameters (optional)`. Throne for Android sends the Android ID (`ANDROID_ID`) as HWID. If the Android ID is not available, it creates a random ID once and keeps sending that. It sends `Android` as OS, the Android version, and the device model. Its default User-Agent is `Throne/Android/<version>`.

## Network requests {#network-requests}

Besides the traffic of your apps, Throne sends the requests in the table below. Many of them follow the "`Use proxy` rule":

- They go out directly by default.
- If `Settings` → `Basic Settings` → `Miscellaneous` → `Use proxy` is on, or `System Proxy` is on, they go through Throne's local proxy port instead. In Simple mode, `Use proxy` is in the `Common` tab, under `Network Settings`.
- If they should use the local proxy port but no profile is running, they fail with "Request with proxy but no profile started."
- Since 1.4.0, if `Disable Mixed Inbound` is on, there is no local proxy port. While TUN mode is on, requests that should use it go out like the traffic of any other app, and TUN mode routes them. With TUN mode off, they fail with "Request with proxy but the mixed inbound is disabled and Tun Mode is off."

A request that goes through the local proxy port enters Throne like the traffic of any other app, so your routing profile decides where it goes.

| Request | When | Destination | Route |
| --- | --- | --- | --- |
| Subscription update | When you add or update a subscription, and on schedule if `Subscription auto update` is on (off by default) | The URL of your provider | `Use proxy` rule |
| Remote routing profile | When you add or update one, and on schedule if `Routing profiles auto update` is on (off by default) | The URL of the profile. Addresses on `raw.githubusercontent.com` go through the `Remote Rule-set Mirror`. | `Use proxy` rule |
| Routing profile list | When you choose `Routing` → `Download Profiles` → a country | The throneproj/routeprofiles repository on GitHub, through the mirror | `Use proxy` rule |
| Rule-sets | When a profile starts and its routing needs a rule-set (or `Enable AdBlock`) that is not in `cache.db` yet, about once a day while a profile runs, and when you choose `Routing` → `Update Rule-Sets` | GitHub through the `Remote Rule-set Mirror` (`jsDelivr(Cloudflare)` by default), or a `.srs` URL you added | Sent by the core through the default outbound of your routing profile (directly if it is `block`) |
| Exit country lookup | After every start of a profile, and when you open the `Runtime Stats` tab while a profile runs. Since 1.4.0, only while `Look up exit country` is on (the default). | The `Lookup service`. With `Automatic` (the default), `https://api.ip2location.io/` first, then `http://ip-api.com/json/`, `https://ipwho.is/`, `https://ipinfo.io/json` and `https://www.cloudflare.com/cdn-cgi/trace`, until one names a country | Always the local proxy port |
| URL test | When you run a URL test or click the status bar, when you open the `Runtime Stats` tab, while an auto selector runs, and after a subscription update if the group's `Run URL test` is on | The `Latency Test URL`, by default `http://cp.cloudflare.com/` | Through each tested profile |
| IP test | Only when you choose `Resolve Selected Out IP` or `Resolve out IP for group` | `https://api.ip2location.io/` | Through each tested profile |
| Speed test | Only when you run a speed test | The Speedtest.net server list and a nearby Speedtest server. In `Simple Download` mode, the `Simple Download URL` (by default `http://cachefly.cachefly.net/1mb.test`). | Through each tested profile |
| Connectivity check | While an auto selector runs, only if you set a `Direct Test URL` or a `Connectivity URL` (both empty by default) | That URL | Direct |
| Domain lookup | Only when you choose `Resolve Domain for group` or `Resolve Selected Domain` | DNS lookups of the domain names of your servers, sent to the `Direct DNS` server of your [DNS settings](@/guides/dns.md#settings) (`localhost`, your system's resolver, by default) | Since 1.4.0, sent by the core directly, never through a proxy. Before, Throne asked your system's DNS resolver. |
| Update check | Only when you choose `Program` → `Check For Update` (`Tools` → `Check For Update` before 1.4.0). The download starts only when you click `Update`. On macOS, in the system-Qt packages and in copies from the Linux install script, this menu item is disabled. | `api.github.com`: the list of releases and, since 1.4.0, when a newer version exists, its `CHANGELOG.md` for the release notes in the update window. The download is the release file on GitHub. | `Use proxy` rule |
| WARP registration | Only when you generate a WARP config or identity | `api.cloudflareclient.com`, or the `Registration Domains…` you set | `Use proxy` rule |
| Xray geo files | Only when an Xray config needs `geoip.dat` or `geosite.dat` and you agree to download them, when you click `Download`, or when a Throne API client asks for them | The `GeoIP Asset URL` and `GeoSite Asset URL` (GitHub by default) | `Use proxy` rule |
| IP scan | Only when you start a scan in `Tools` → `IP Scanner` (since 1.4.0) | First a TCP connection to public DNS servers, such as `1.1.1.1:443` and `8.8.8.8:443`, to check the network; then the addresses of the IP list that you scan | Direct, through your network adapter, also while TUN mode is on |
| IP list update | When you update an IP list that has a URL or rule-set source, and on schedule if you turned on automatic updates for that list (off by default) (since 1.4.0) | The URL of the list; built-in lists come from rule-sets on GitHub through the `Remote Rule-set Mirror` | `Use proxy` rule |
| Web dashboard | `Tools` → `Open Web dashboard`, only if your build has no built-in dashboard. Official builds have one. | GitHub | Always the local proxy port |
| WebDAV backup | Only when you click `Upload to WebDAV` or `Restore from WebDAV...`, restore or delete a backup in that list, or click `Test` in `WebDAV Settings...` (since 1.4.0). There are no automatic uploads. | The `Server URL` you enter, in the `Folder` (`Throne` by default). An upload sends a backup file and updates the backup list `throne_backups.json` there. | `Use proxy` rule. With `Connect through the proxy` on, always the local proxy port. |
| NTP | Only if you turn on `NTP Settings` | The NTP server you enter | The `outbound` you choose, `direct` by default |

In Simple mode, `IP Scanner`, `Open Web dashboard`, the `Resolve` menu items, the `Runtime Stats` tab and `NTP Settings` are hidden (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). Settings you made before still apply, so scheduled IP list updates and NTP keep working.

About the exit country lookup:

- Throne uses it to show the country and city of your connection in the status bar, the window title and the `Runtime Stats` tab.
- It always goes through the local proxy port. With the built-in `Default` routing profile it leaves through your proxy, so the lookup service sees the address of your proxy, not your own.
- Since 1.4.0, `Settings` → `Basic Settings` → `Common` → `Look up exit country` turns it off, and `Lookup service` chooses the service. See [IP and country test](@/guides/testing.md#ip-test).
- ip-api.com is asked over plain HTTP, the other services over HTTPS. Throne sends its User-Agent.
- After a start, Throne skips it when the exit of the running profile is an OpenVPN or OpenConnect profile.

About WebDAV backups:

- A backup is not encrypted. It holds the parts ticked under `Create Backup`, for example your server passwords and subscription links. The backup list also records the device name (your computer's name, unless you set another one), a random device ID, the platform and the Throne version.
- The `Username` and `Password` go to the server with HTTP basic authentication, which `http://` does not encrypt. Throne keeps them in its settings on this computer and never puts them in a backup.
- See [WebDAV backups](@/guides/backup.md#webdav).

Since 1.3.2, the update check and the update download always verify the TLS certificate of GitHub, even when `Basic Settings` → `Miscellaneous` → `Ignore TLS errors` is on, because the release files have no other signature.

Requests from a [Throne API](@/advanced/api.md) client, such as subscription updates, imports from a URL and geo file downloads, are the same requests as above and follow the same routes. This includes the web UI of `thronectl` (`thronectl web`): its gateway talks only to the Throne API, and its page loads nothing from other sites. [Headless Throne](@/headless/_index.md) makes no exit country lookup, and it downloads missing Xray geo files without asking.

DNS queries for the traffic of your apps go to the servers in `Routing Settings` → `DNS`, or since 1.4.0 to those of the active routing profile if it has its own (see [DNS per routing profile](@/guides/dns.md#route-dns)). See [DNS](@/guides/dns.md).

**Android:** Throne for Android makes the same kinds of requests for subscriptions, routing profiles, rule-sets, tests and WARP registration. WebDAV backups go only to the server in `WebDAV settings`, and only when you back up, restore or test the connection; since 2.0.4, also when you delete a backup. They never use the local proxy. Since 2.0.4, an upload also records the device name, a random device ID, the platform and the version in the backup list, as on the desktop. With `Settings` → `Subscriptions` → `Use proxy` on, or in `Proxy only` mode, its own requests use the local proxy while a profile runs. While no profile runs, subscription updates and WARP registration fail with "Request with proxy but no profile started." if `Use proxy` is on, and in 2.0.3 and older also in `Proxy only` mode; since 2.0.4, `Proxy only` mode alone lets them go out directly. Update checks, routing profile downloads and the rule-set list always work without a profile: they go out directly. Since 2.0.2, Throne for Android also looks up your exit location after each connect and after the connection test from the bottom bar; the request goes through the local proxy, or through the VPN when the mixed inbound is turned off. In 2.0.2 it always asks `http://ip-api.com/json/`. Since 2.0.3, `Settings` → `Testing` → `Look up exit country` turns the lookup off, and `Lookup service` offers the same services as on the desktop. `Automatic` asks ip-api.com first in 2.0.3, and IP2Location.io first since 2.0.4. Also since 2.0.2, when an Xray config with `geoip:` or `geosite:` rules starts or is tested, it downloads `geoip.dat` and `geosite.dat` from GitHub (Loyalsoldier by default). It checks GitHub for updates only when you tap `Check for updates`, or once a day if you turn on `Check for updates daily` (off by default). Since 2.0.4, the update screen also downloads the changelog of a new version from `api.github.com`. `Refresh repository list` on the `Routing` screen downloads the list of routing profiles from GitHub.

## Local proxy port {#local-proxy}

While a profile runs, Throne listens on a local proxy port that accepts SOCKS5 and HTTP connections. By default this is `127.0.0.1:2080`. Only programs on your own computer can reach `127.0.0.1`, but every one of them can use the port without a password.

The settings are in `Settings` → `Basic Settings` → `Common` → `Inbound Settings`:

| Setting | Default | What it does |
| --- | --- | --- |
| `Listen Address` | `127.0.0.1` | `::` or `0.0.0.0` opens the port to other devices. |
| `Listen Port` | `2080` | The port number. |
| `Random port` | Off | Uses a free random port at every start. |
| `Enable Authorization` | Off | Requires the `Inbound Username` and `Inbound Password`. |
| `Disable Mixed Inbound` | Off | Turns the port off. `System Proxy` then cannot be used. |

`Allow other devices to connect` in the `Program` menu and in the tray menu sets `Listen Address` to `::`. Then every device that can reach your computer can use your proxy. Turn on `Enable Authorization` when you do this. See [LAN sharing](@/guides/proxy_modes.md#lan-sharing).

Other local ports:

| Port | Default | Who can reach it |
| --- | --- | --- |
| `DNS Server Port` (`Basic Settings` → `Miscellaneous`) | `5533` | Only this computer (`127.0.0.1`). Since 1.4.0, `Allow other devices to connect` below it opens it to your network; see [DNS server for other devices](@/guides/dns.md#dns-server). |
| Clash API (`Basic Settings` → `Core`) | Off (empty `Listen Port`) | The Clash API `Listen Address`, `127.0.0.1` by default. Set a `Secret` when you turn it on. |
| sing-box API / Dashboard (`Basic Settings` → `Core`) | Off (empty `Listen Port`) | Only this computer. A random `Secret` protects it. |
| Throne API (`Integration Settings` → `Throne API`, since 1.4.0) | Off; port `9095` when on | Only this computer (`127.0.0.1`), or with `Listen on` set to `Local network`, every device that can reach your computer, limited by `Allowed addresses`. Every client needs the key. See [Throne API & thronectl](@/advanced/api.md#security). |

In Simple mode, `Listen Port` is the only one of the inbound settings above that is shown. The other inbound settings, the `DNS Server Port`, the `Core` tab and `Integration Settings` appear only in Advanced mode (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

When a profile uses Xray, the core also opens internal ports on `127.0.0.1`, each protected by a random password. Throne and its core talk to each other through a local socket, not through a network port.

[Headless Throne](@/headless/_index.md) turns the Throne API on at its first start, on `127.0.0.1:9095`, with a key in a file that only its user can read.

The web UI of `thronectl` (`thronectl web`) is a separate program with its own port, `127.0.0.1:9096` by default, and asks for a password. See [Web UI](@/headless/web.md#security).

**Android:** `Settings` → `Inbound` has the same kind of options: `Proxy port`, `Allow connections from the LAN` (off by default) and `Enable authorization`. Since 2.0.2, the sing-box API in `Settings` → `Core` is on by default, on `127.0.0.1:9091` with a random secret; it serves the `sing-box dashboard` in the side menu from files inside the app. Throne for Android has no Throne API.

## This website {#website}

This documentation site counts visits with Yandex Metrika. It records page views, the referring page, clicks on the page and on links, and how long visitors stay. Session recording is turned off. The Throne apps do not include this counter.
