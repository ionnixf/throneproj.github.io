+++
title = "Testing & Auto Selector"
description = "Test profiles, sort and clean up the list, let the auto selector pick the best server, and scan for working IP addresses and DNS resolvers."
weight = 60
toc = true
+++

Tests do not stop the running profile. They skip auto selectors; test the groups that an auto selector uses instead.

## URL test {#url-test}

The URL test checks that a profile works and measures its latency. Throne requests the `Latency Test URL` through the profile twice and shows how long the second request took.

- **Selected profiles:** right-click them and choose `Url Test Selected` (`Ctrl+Shift+S`).
- **The whole group:** choose `Groups` → `Url Test Group` (`Ctrl+Shift+G`).
- **The running profile:** click its name at the left of the status bar. The result appears in the status bar.

`Stop Testing` (`Ctrl+.`) stops a running test.

The `Test Result` column shows the latency (green up to 100 ms, yellow up to 300 ms, red above), `Unavailable` when the test failed (the log has a `test error` line with the reason), or `Connect OK` when an OpenVPN or OpenConnect profile connected but the test URL did not answer through it.

The settings are in `Settings` → `Basic Settings` → `Common`, under `Testing`. In Simple mode, only `Latency Test URL` and `Look up exit country` are shown (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

| Setting | Default | What it does |
| --- | --- | --- |
| `Latency Test URL` | `http://cp.cloudflare.com/` | The address requested through each profile. |
| `Timeout` (next to `Concurrency`) | `3000` ms | The time limit for each request. Too low a value fails profiles with multiplexing, whose first request takes longer. |
| `Concurrency` | `10` | How many profiles are tested at the same time. |
| `Direct Test URL` | empty | An address that works without a proxy on your network. The auto selector fetches it directly to tell a dead Internet connection from dead servers. Empty uses the network state of the operating system. |

## IP and country test {#ip-test}

This test shows the exit IP address of each profile and its country: the address that websites see. Right-click profiles and choose `Resolve Selected Out IP`, or choose `Groups` → `Resolve out IP for group` (Advanced mode only). The `Test Result` column then shows the country flag, and the exit IP if `Out IP` is included (see [Sort and filter](#sort-filter)). The country filter and the auto selector's `Only countries` use this country.

After a profile starts, Throne also shows the country and city of its exit at the bottom left of the window and in the window title. In the `Testing` settings, `Look up exit country` turns this off and `Lookup service` chooses the service; `Automatic` starts with IP2Location.io, which the IP test also uses. For the services Throne contacts, see [Privacy](@/reference/privacy.md#network-requests).

## Speed test {#speed-test}

The speed test measures the download and upload speed of a profile.

- **Selected profiles:** right-click them and choose `Speedtest Selected` (`Ctrl+Shift+P`).
- **The whole group:** choose `Groups` → `Speedtest Group` (`Ctrl+Alt+P`).
- **The live connection:** while a profile runs, choose `Tools` → `Speedtest Current` (`F6`).

Throne tests the profiles one at a time. The `Test Result` column shows the download speed after `↓` and the upload speed after `↑`, if `Speed` is included.

`Settings` → `Basic Settings` → `Common` → `Speedtest mode` chooses what the test does: `Download + Upload` (default), `Only Download`, `Only Upload`, `Simple Download` (fetches the `Simple Download URL` without a server search) or `Only Country`, which only finds the nearest Speedtest server and shows its country: fast, with almost no data, many profiles at once. The `Timeout` next to it (default `5000` ms) limits each part of the test.

{% alert_warning() %}
A speed test transfers as much data as it can until the timeout, for every profile. Testing a large group can use a lot of data.
{% end %}

## Sort and filter {#sort-filter}

Click a column header to sort by it, and click it again to reverse the order. Sorting by latency puts the fastest profiles first, then failed ones, then untested ones. The group stays sorted, also after tests, subscription updates and imports, until you right-click a column header and choose `Stop Sorting`, or drag a row to another place.

Right-click the `Test Result` header to choose what the column shows (`Out IP`, `Speed`) and what it sorts by (`Latency`, `Download Speed`, `Upload Speed`, `IP Out`).

To filter the list, press `Ctrl+F` and type in the field under `Type`, `Address` or `Name`, or in `Filter by country...` under `Test Result`. In the `Address` field, `port=443` shows the profiles on port 443 and `port=1000:2000` those on ports 1000 to 2000. The country field matches the country code from the IP or speed test, such as `DE`. Press `Ctrl+F` again to hide and clear the fields.

## Clean up {#clean-up}

`Groups` → `Clean up`, also in the right-click menu of the group's tab, removes profiles from the current group:

| Command | Shortcut | What it removes |
| --- | --- | --- |
| `Remove Duplicates` | `Ctrl+Shift+D` | Exact copies of an earlier profile, including the name. |
| `Remove Unavailable` | `Ctrl+Shift+R` | Profiles whose last test failed. Never the running profile. |
| `Remove Invalid` | `Ctrl+Alt+I` | Profiles that the core rejects as invalid. |
| `Remove Insecure Configs` | – | Profiles without encryption, with TLS that does not check certificates, or with old Shadowsocks ciphers. |

The other commands stop the running profile if they remove it. `Groups` → `Clear Group test result` (`Ctrl+Shift+C`) clears only the test results.

To see which profiles count as insecure, turn on `Settings` → `Basic Settings` → `Style` → `Show Config Security` (Advanced mode only): insecure profiles then get a warning sign in the `Type` column.

To clean up automatically, use the group option `Auto Clear Unavailable Profiles`, which deletes failed profiles after every URL test of the group, or the [After each update](@/guides/subscriptions.md#after-update) options of a subscription group, which can also test and sort it.

## Auto selector {#auto-selector}

An auto selector is a profile that picks the best working profile from one or more groups, keeps checking the others, and switches when the current one gets worse or fails. New servers from a subscription update join it by themselves.

1. Select the tab of the group it should use.
2. Choose `Program` → `New profile` (`Ctrl+N`). The editor opens with `Auto Selector` as the `Type`.
3. Enter a `Name`.
4. In `Servers from`, check the groups to pick from. The current group is already checked.
5. Click `OK`, then start the new profile like any other.

| Setting | What it does |
| --- | --- |
| `Servers from` | The groups whose profiles are used. Profiles from all checked groups are ranked together. |
| `Only names matching` | An optional regular expression; only profiles whose name matches are used. It matches anywhere in the name and ignores case: `germany\|nl` uses every name that contains `germany` or `nl`, and `^(?!.*(germany\|🇩🇪)).*$` every name that contains neither `germany` nor 🇩🇪. |
| `Share traffic between the best profiles` | Off (default): one profile carries all traffic and the other ready profiles stand by. On: traffic is spread over the working profiles. |
| `Preferred profile` | Appears after you chose a profile with `Use this profile` in the stats window. `Use automatic` returns the choice to the auto selector. |

Each profile goes through the `Front Proxy` and `Landing Proxy` of its own group, as if you started it on its own; those of the auto selector's group do not apply.

`Advanced…` has more options, each with a working default and a tooltip. The ones most often changed:

| Setting | Default | What it does |
| --- | --- | --- |
| `Only countries` | empty | Country codes such as `DE,NL,FR`. `!` leaves a country out: `!RU,!IR` uses every country except these two. Uses the country found by the IP test or the speed test, so profiles without one are skipped while this is set. |
| `Run the best` | 300 profiles | How many profiles are loaded into the running config. The auto selector switches between them without reconnecting. |
| `Switch tolerance` | 300 ms | How much faster another profile must be before the auto selector switches to it. |
| `Maximum latency` | no limit | Profiles slower than this are never chosen. |
| `Drop connections on switch` | on | After a switch caused by a problem, on moves all connections to the new profile at once; off lets open downloads finish on the old one. |
| `Mode` | `Rotate on a timer (keeps sessions stable)` | Used with `Share traffic between the best profiles`. With `Per connection (widest spread)`, your exit IP can change during a session. |

With more usable profiles than `Run the best`, the auto selector first URL-tests those without a result from the last `Trust results for` minutes (default 1440) and loads the best. It starts as soon as a few of them work, finishes the tests in the background and restarts once if the ranking changed. If all running profiles fail, it rebuilds from the next best ones. It pauses while your computer is offline. Its log lines start with `[Auto selector]`.

While an auto selector runs, `Tools` → `Auto Selector Stats` shows the status and latency of each profile. `Use this profile` keeps the selected profile in use, `Back to automatic` lets the ranking choose again, and `Check all now` measures all running profiles at once.

Limits:

- An auto selector cannot be a hop in a chain, or a `Front Proxy` or `Landing Proxy`.
- It skips chains, other auto selectors, OpenVPN, OpenConnect, Tailscale, Extra Core, sing-box full configs, custom configs that cannot be read, and Xray full configs that cannot be combined with the front or landing proxy of their group.

## IP scanner {#ip-scanner}

`Tools` → `IP Scanner` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)) checks lists of IP addresses and keeps the ones that work from your network: an unblocked address of the CDN your servers are behind, a working Cloudflare WARP endpoint, or DNS resolvers for a DNS tunnel. The results form an IP list, which a group or a profile can use as its [server address](@/guides/subscriptions.md#server-address).

1. Click `New scan`, enter a `Name` and choose the kind of scan:
   - `Generic scan` checks addresses with ping, TCP, TLS or HTTP, optionally through one of your profiles.
   - `WARP scan` finds WARP endpoints that complete a WireGuard, AmneziaWG or MASQUE connection.
   - `DNS resolver scan` finds resolvers for a DNS tunnel profile; see [DNS resolver scans](#dns-scan).
2. Click `OK`. The scan's window opens.
3. Set the targets on the `Targets` tab and the checks on the `Phases` tab.
4. Click `Start`. The `Results` tab fills as addresses pass.

A scan keeps running when you close its window.

On the `Targets` tab:

- `Base IP list` holds the addresses to scan; `Manage` opens the [IP lists](#ip-lists). In a WARP scan, `Use built-in WARP ranges` picks the list and ports for the selected WARP mode.
- `Ports` are merged with the ports stored in the list, unless you choose otherwise next to them. Addresses left without a port are scanned on port 443, or 80 for plain HTTP.
- `Stop after (results)` completes the scan after that many results. `Concurrency` sets how many addresses are checked at the same time.
- IPv6 addresses are skipped unless you tick `Scan IPv6 addresses`.

The `Phases` tab has the checks. An address must pass every check that is on, in this order:

| Phase | Default | What it checks |
| --- | --- | --- |
| `ICMP (ping)` | off | The address answers ping. |
| `TCP connect` | on | The port accepts a TCP connection. Generic scans only. |
| `TLS / HTTP` | off | A TLS handshake succeeds and, unless `Method` is `None (TLS handshake only)`, an HTTP request gets the answer 200 OK. Generic scans only. |
| `Config test` | off | A copy of the chosen `Profile`, pointed at the address, loads the `Test URL`. This finds addresses that really work for your server, such as CDN addresses. Generic scans only. |
| `WARP test` | on | A WARP connection through the address works, in the chosen `Mode`: `WireGuard`, `AmneziaWG` or `MASQUE`. WARP scans only. |

The `WARP test` needs a WARP identity. The default, `Dedicated for this scan (recommended)`, needs one click on `Register`, which registers a new WARP device with Cloudflare.

On the `Results` tab, `Save as IP list` copies the results into a new IP list.

When a scan has results, `Start` asks what to do: `Scan from the beginning` clears the results and scans the base list again; `Rescan current results` checks only the results and removes the ones that stopped working.

### DNS resolver scans {#dns-scan}

A DNS resolver scan finds resolvers that relay the tunnel of one of your [DNS tunnel](@/advanced/dns_tunnels.md) profiles: DNSTT (also in VayDNS mode), MasterDNS, or VLESS (Xray) with XDNS. It tries each address of the base list as the only resolver of that profile and keeps the ones that pass.

To create one, choose `DNS resolver scan` in `New scan`, or click `Find resolvers…` next to `IP list` in the editor of a saved DNS tunnel profile, which opens or creates a scan for it. The scan tests the profile as it was last saved.

On the `Targets` tab, choose the resolvers to try as the `Base IP list`, such as `Iran DNS resolvers` or `Russia DNS resolvers`. There is no `Ports` field: each address is tried on the port stored in the list, or on port 53.

The `Phases` tab has one check, `DNS tunnel test`, with the `Profile`, the `Test` and a `Timeout (ms)` for the whole test of one resolver. Each test also does the checks of the tests above it for the same kind of profile:

| Test | Profiles | What it checks |
| --- | --- | --- |
| `Poll: relays one tunnel query` | DNSTT, XDNS | The resolver passes one tunnel query to the tunnel server and returns a valid answer. |
| `Query names: also relays names as long as the profile sends` (default) | DNSTT, XDNS | The resolver also relays query names as long as the profile's. The scan finds the longest name it relays. |
| `Handshake: also opens a tunnel session` | DNSTT | A tunnel session with the server opens through the resolver. |
| `Quick: relays the smallest packets` (default) | MasterDNS | The resolver relays queries and answers of the smallest size the profile needs. |
| `Full: also measures the upload and download MTU` | MasterDNS | The scan also finds the largest query and answer that the resolver relays. |

On the `Results` tab, `Latency` is how long the resolver took to answer. `Max QNAME` (DNSTT and XDNS) is the longest query name it relays, in the units of `Max QNAME Length` for DNSTT and of `lenLimit` for XDNS. `Upload MTU` and `Download MTU` (MasterDNS) are the largest payloads per query and per answer. Resolvers that fail are not kept; the log shows where each one stopped.

Click `Use as resolvers` on the `Results` tab to make the scan's result list the profile's `IP list`. The resolvers typed in the profile stay and are used first. The profile reads the list each time it starts, so it follows the scan: `Scan from the beginning` empties the list, and `Rescan current results` removes resolvers that stopped working. For a fixed set, click `Save as IP list` and choose that list as the profile's `IP list` instead.

### IP lists {#ip-lists}

Click `IP Lists` in the `IP Scanner` window to manage the lists that scans read and that groups and profiles use. Throne comes with `Cloudflare CDN`, `Fastly CDN`, `Gcore CDN`, `Cloudflare WARP (WireGuard)`, `Cloudflare WARP (MASQUE)`, `Iran DNS resolvers` and `Russia DNS resolvers`. `Import` → `Add default lists` restores them.

- `New` creates a list. Its `Source` is `Manual` (you write the entries), `URL` (an address that Throne downloads) or `Rule-set` (the IP ranges of a rule-set such as `geoip-ir`, or a rule-set URL). A `URL` or `Rule-set` list can update itself with `Auto update`, at most once every 30 minutes.
- Write one entry per line, for example `1.2.3.4`, `1.2.3.0/24`, `1.2.3.4:443`, `[2606:4700::1]:443`, `1.2.3.0/24 443` or `1.2.3.1-1.2.3.9`.

The results of each scan are a list too, named "Result of scan" and the scan's name. A group or a profile can use it directly, but `Scan from the beginning` empties it; use `Save as IP list` for a list that stays as it is.

## On Android {#android}

Throne for Android has the same tests and the same auto selector. The differences:

- **Menus.** On the Profiles screen, the `⋮` menu has `Test` (`URL test`, `IP & country test`, `Speed test`, `Stop testing`), `Remove` and `Sort`. For selected profiles, use `Test` in the selection menu; for one profile, its `⋮` button.
- **Test panel.** A panel shows the progress and results of a test, with actions such as `Sort by latency`, `Remove unavailable` and `Connect to fastest`.
- **Live connection.** While connected, tap the statistics bar to test the connection. Touch and hold it for `Live graphs` and `Speed test current connection`. The bar also shows the exit country; `Settings` → `Testing` has `Look up exit country` and `Lookup service`.
- **Auto selector.** Create it with `Add profile` → `Manual settings` → `Auto selector`. Tap its status line on the Profiles screen to open `Auto selector stats`.
- **IP scanner.** Not available on Android.
