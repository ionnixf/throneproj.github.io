+++
title = "Testing & Auto Selector"
description = "Test profiles for latency, exit IP and speed, sort and filter the list, remove dead profiles, let the auto selector pick the best server, and scan for working IP addresses and DNS resolvers."
weight = 60
toc = true
+++

Throne can test your profiles and help you keep only the good ones. This page covers the URL test, the IP and country test and the speed test, sorting and filtering, cleaning up, the auto selector, which picks the best server for you, and the IP scanner, which finds server addresses and DNS resolvers that work on your network.

Tests do not stop the running profile. They skip auto selector profiles; test the groups that the auto selector uses instead.

## URL test {#url-test}

The URL test checks that a profile works and measures its latency. Throne requests the `Latency Test URL` through the profile two times and shows how long the second request took, in milliseconds.

To start a URL test:

- **Selected profiles:** right-click them and choose `Url Test Selected` (`Ctrl+Shift+S`).
- **The whole group:** choose `Groups` → `Url Test Group` (`Ctrl+Shift+G`), or right-click the group tab and choose `Url Test selected Group`.
- **The running profile:** click the profile name at the left of the status bar. Throne tests the live connection once and shows `Test Result:` with the time, or `Unavailable`, in the status bar.

While a test runs, `Stop Testing` (`Ctrl+.`) appears in the right-click menu and in the `Groups` menu.

The `Test Result` column shows one of these:

| Result | Meaning |
| --- | --- |
| A time such as `85 ms` | The profile works. The time is green up to 100 ms, yellow up to 300 ms and red above that. |
| `Unavailable` | The test failed. The log has a line with `test error` and the reason. |
| `Connect OK` | An OpenVPN or OpenConnect profile connected, but the test URL did not answer through it. |

The test settings are in `Settings` → `Basic Settings` → `Common`, under `Testing`:

| Setting | Default | What it does |
| --- | --- | --- |
| `Latency Test URL` | `http://cp.cloudflare.com/` | The address that Throne requests through each profile. |
| `Timeout` (next to `Concurrency`) | `3000` ms | The time limit for each request. Do not set it too low: profiles with multiplexing need more time for their first request, and a short timeout marks them as failed. |
| `Concurrency` | `10` | How many profiles are tested at the same time. |
| `Direct Test URL` | empty | Fetched without any proxy, so that the auto selector can tell a dead Internet connection from dead servers. Use an address that works on your network without a proxy. Empty means that Throne uses the network state of the operating system. |

Since 1.4.0, [DNS tunnel](@/advanced/dns_tunnels.md#testing) profiles get 30 more seconds for their first request, and MasterDNS profiles 90 more seconds, because the tunnel opens its session then.

In Simple mode, the `Testing` group shows only `Latency Test URL` and `Look up exit country`. The other test settings on this page, such as `Speedtest mode` and `Lookup service`, appear only in Advanced mode (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

To copy the results of selected profiles, right-click them and choose `Share` → `Copy Test Result`.

## IP and country test {#ip-test}

This test shows the exit IP address of each profile and its country. This is the address that websites see.

- **Selected profiles:** right-click them and choose `Resolve Selected Out IP`.
- **The whole group:** choose `Groups` → `Resolve out IP for group`.

Both items appear only in Advanced mode (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

Throne requests `https://api.ip2location.io/` through each profile. The `Test Result` column then shows the country flag, and the exit IP if `Out IP` is included (see [Sort and filter](#sort-filter)). The test uses the `Timeout` and `Concurrency` of the URL test.

The country is used by the country filter and by the auto selector's `Only countries` option. For the services that Throne contacts, see [Privacy](@/reference/privacy.md#network-requests).

The running profile has its own exit lookup. After a profile starts, Throne asks an IP lookup service, through the local proxy port, which IP address and country the connection exits from. It shows the country and city at the bottom left of the window, in the window title and in the `Runtime Stats` tab (Advanced mode only). This lookup does not change the `Test Result` column. Since 1.4.0, you can choose the service or turn the lookup off in `Settings` → `Basic Settings` → `Common`, under `Testing`:

| Setting | Default | What it does |
| --- | --- | --- |
| `Look up exit country` | on | Off: Throne never asks a lookup service by itself, and shows no exit country. |
| `Lookup service` | `Automatic` | The service to ask: `ip-api.com`, `ipwho.is`, `ipinfo.io`, `ipapi.co`, `IP2Location.io`, `FreeIPAPI` or `Cloudflare`. `Automatic` asks `IP2Location.io`, `ip-api.com`, `ipwho.is`, `ipinfo.io` and `Cloudflare` in this order until one names a country, and gives up after 20 seconds. It starts with IP2Location.io because the IP and country test uses it too, so both name the same country. |

## Speed test {#speed-test}

The speed test measures the download and upload speed of a profile.

- **Selected profiles:** right-click them and choose `Speedtest Selected` (`Ctrl+Shift+P`).
- **The whole group:** choose `Groups` → `Speedtest Group` (`Ctrl+Alt+P`), or right-click the group tab and choose `Speed Test selected Group`.
- **The live connection:** while a profile runs, choose `Tools` → `Speedtest Current` (`F6`).

Throne tests the profiles one at a time. The `Test Result` column then shows the download speed after `↓` and the upload speed after `↑`, if `Speed` is included.

Choose what the test does in `Settings` → `Basic Settings` → `Common` → `Speedtest mode`:

| Mode | What it does |
| --- | --- |
| `Download + Upload` (default) | Finds a nearby Speedtest server, then measures download and upload. |
| `Only Download` | Measures download only. |
| `Only Upload` | Measures upload only. |
| `Simple Download` | Downloads the file at `Simple Download URL`, by default `http://cachefly.cachefly.net/1mb.test`. No server search. |
| `Only Country` | Only finds the nearest Speedtest server and shows its country. It is fast, uses almost no data and tests many profiles at the same time. |

The `Timeout` next to `Speedtest mode` (default `5000` ms) limits each part of the test.

{% alert_warning() %}
A speed test transfers as much data as it can until the timeout, for every profile. Testing a large group can use a lot of data, which matters if your plan has a data limit. The data counts in the `Traffic` column of each profile.
{% end %}

## Sort and filter {#sort-filter}

To sort the profiles of a group:

- Click a column header to sort by that column. Click it again to reverse the order. Throne saves the new order.
- When you sort by latency with the first click, the fastest profiles come first. Failed profiles follow, and untested profiles come last.
- Since 1.4.0, the group stays sorted. A small arrow next to the name of the sorted column shows the sort and its direction. After a test run, a subscription update or an import, Throne puts the profiles back in order. To keep the current order and stop sorting, right-click a column header and choose `Stop Sorting`.
- Right-click the `Test Result` header to choose what the column shows (`Include:` `Out IP`, `Speed`) and what it sorts by (`Sort By:` `Latency`, `Download Speed`, `Upload Speed`, `IP Out`).
- Right-click the `Traffic` header to sort by `Total`, `Downloaded` or `Uploaded`.
- Drag rows to put them in any order. This does not work while the filter is on. Dragging a row also stops the sort.

A subscription update puts the profiles in the provider's order, and then Throne applies the group's sort again. To test a subscription group and sort it by latency after each update, use its `Run URL test` and `Sort by latency` options; see [After each update](@/guides/subscriptions.md#after-update).

To filter the list:

1. Press `Ctrl+F`, or click the filter button at the right end of the group tab bar.
2. Type in the field under the `Type`, `Address` or `Name` header, or in `Filter by country...` under `Test Result`.

The list then shows only the profiles that contain the text; case does not matter. In the `Address` field, `port=443` shows the profiles on port 443, and `port=1000:2000` the profiles on a port from 1000 to 2000. One end of the range can be empty, for example `port=8000:`. The country field matches the country code from the IP or speed test, for example `DE`. Press `Ctrl+F` again to hide the fields; this also clears them.

## Clean up {#clean-up}

Since 1.4.0, these commands are in `Groups` → `Clean up`. The same submenu appears when you right-click the tab of the current group. They work on the current group.

| Command | Shortcut | What it removes |
| --- | --- | --- |
| `Remove Duplicates` | `Ctrl+Shift+D` | Profiles that are exact copies of an earlier profile, including the name. The first copy stays. |
| `Remove Unavailable` | `Ctrl+Shift+R` | Profiles whose last test failed. It never removes the running profile. |
| `Remove Invalid` | `Ctrl+Alt+I` | Profiles that the core rejects as invalid. |
| `Remove Insecure Configs` | – | Profiles whose traffic is not properly protected: no encryption, TLS without certificate checks, or old Shadowsocks ciphers. |
| `Clear Group test result` | `Ctrl+Shift+C` | Only the test results. The profiles stay. This command is directly in the `Groups` menu. |

Throne asks before it deletes anything, unless `Settings` → `Basic Settings` → `Style` → `Skip confirmation When Deleting Profiles` is on. If `Remove Duplicates`, `Remove Invalid` or `Remove Insecure Configs` removes the running profile, Throne stops it.

To see which profiles count as insecure, turn on `Settings` → `Basic Settings` → `Style` → `Show Config Security` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). The `Type` column then shows the security of each profile, and insecure profiles get a warning sign.

To clean up automatically:

- The group option `Auto Clear Unavailable Profiles` deletes failed profiles after every URL test of the group, without asking. See [Group options](@/guides/subscriptions.md#group-options).
- A subscription group can remove duplicate, insecure, invalid and failed profiles after each update. See [After each update](@/guides/subscriptions.md#after-update).

## Auto selector {#auto-selector}

An auto selector is a special profile. When you start it, it picks the best working profile from one or more groups, keeps checking the others, and switches when the current one gets worse or fails. New servers from a subscription update join it by themselves.

To create one:

1. Select the tab of the group that it should use.
2. Choose `Program` → `New profile` (`Ctrl+N`). The editor opens with `Auto Selector` already chosen as the `Type`.
3. Enter a `Name`.
4. In `Servers from`, check the groups that the auto selector picks from. The current group is already checked.
5. Click `OK`, then start the new profile like any other profile.

The editor shows a summary of what will happen, for example how many profiles of the checked groups can be used. The basic options are:

| Setting | Default | What it does |
| --- | --- | --- |
| `Servers from` | the current group | The groups whose profiles the auto selector uses. Since 1.4.0, it is a list of your groups, and you can check more than one; their profiles are ranked together. At least one group must be checked. Archived groups appear only while they are checked. |
| `Only names matching` | empty | An optional regular expression. Only profiles whose name matches are used, for example to pick one country or provider. It can match anywhere in the name, and case does not matter: `germany\|nl` uses every name that contains `germany` or `nl`, and `^(?!.*(germany\|🇩🇪)).*$` every name that contains neither `germany` nor 🇩🇪. |
| `Share traffic between the best profiles` | off | Off: one profile carries all traffic, and the other ready profiles stand by. On: traffic is spread over the working profiles; see `Load balancing` below. |
| `Preferred profile` | – | Appears only after you chose a profile with `Use this profile` in the stats window. `Use automatic` returns the choice to the auto selector. |

Each profile runs as if you started it on its own: it goes through the `Front Proxy` and `Landing Proxy` of its own group. Those of the group that holds the auto selector do not apply. The `Address` column of the auto selector shows the names of its groups. If a checked group is deleted, the summary says so, and the next save removes it from the list.

`Advanced…` shows more options, in the parts `Which profiles to use`, `Health checks`, `Switching`, `Load balancing` and `Test endpoints`. All of them have working defaults.

| Setting | Default | What it does |
| --- | --- | --- |
| `Only countries` | empty | Country codes separated by commas, for example `DE,NL,FR`. Since 1.4.0, a `!` before a code leaves that country out instead: `!RU` uses every country except Russia, and `!RU,!IR` every country except these two. Uses the country that the IP test or the speed test found for each profile, so profiles without one are skipped while this is set, also with `!` codes. The exit lookup of the running profile does not count. |
| `Run the best` | 300 profiles | How many profiles are loaded into the running config. The auto selector switches between them without reconnecting. |
| `Rank at most` | 1000 profiles | How many profiles are measured and kept in the ranked list. Replacements come from this list. |
| `Trust results for` | 1440 min | URL test results this recent are reused instead of testing again. 0 means always test again. |
| `Skip failed profiles` | on | Leaves out profiles whose last test failed, while that result is still trusted. |
| `Keep ready` | 3 profiles | How many profiles are kept confirmed working, so that a failure is covered at once. |
| `Check closely` | 8 profiles | How many of the best profiles are checked at every `Check interval`. |
| `Check interval` | 300 s | How often the closely checked profiles are measured. |
| `Full sweep every` | 600 s | How long one pass over all running profiles takes. |
| `Watch profile in use` | 15 s | How often the profile that carries your traffic is checked on its own. |
| `Samples kept` | 10 | How many recent checks the ranking uses for average latency and jitter. |
| `Switch tolerance` | 300 ms | Another profile must be at least this much faster before the auto selector switches to it. |
| `Maximum latency` | no limit | Profiles slower than this are never chosen. |
| `Failover attempts` | 2 | When the chosen profile fails to connect, how many others are tried at once before the app sees an error. |
| `Drop connections on switch` | on | On: after a switch caused by a problem, all connections move to the new profile at once. Off: open downloads finish on the old profile. |
| `Mode` (load balancing) | `Rotate on a timer (keeps sessions stable)` | Used with `Share traffic between the best profiles`. The other mode is `Per connection (widest spread)`, where every new connection can take a different profile and your exit IP can change during a session. |
| `Rotate every` | 30 s | For the timer mode: how long the auto selector stays on one profile before new connections move to the next. |
| `Test URL` | empty | The address used to measure profiles. Empty means the global `Latency Test URL`. |
| `Connectivity URL` | empty | Fetched without the proxy to notice when your Internet connection is down. Empty means the `Direct Test URL` from the test settings. |

How it runs:

- When its groups have more usable profiles than `Run the best`, it URL-tests the profiles that have no result newer than `Trust results for` and ranks them. Then it loads the best of them, as many as `Run the best`, into the running config. With fewer profiles, it loads all of them at once. The log shows lines that start with `[Auto selector]`.
- Since 1.4.0, it does not wait for all these tests. It starts as soon as enough profiles work: as many as `Check closely` (8 by default). Profiles with a recent working result count too. The working profiles come first, the remaining tests go on, and the auto selector checks the profiles that are not measured yet by itself. When all tests are done, it ranks the profiles again. If the best ones changed, it restarts once with the full ranking.
- If all running profiles stop working for 20 seconds, it rebuilds from the next best profiles, and again starts as soon as enough of them work. It waits longer between repeated rebuilds, from 60 seconds up to 10 minutes.
- If your computer has no network connection, the checks pause until it comes back.

While an auto selector runs, `Tools` → `Auto Selector Stats` opens its stats window. The table shows each profile with `Profile`, `Status` (`Working`, `Unstable`, `Not checked`, `Failing` or `Paused`), `Latency`, `Jitter`, `Checks`, `Connects`, `Last OK` and `Notes`. `Only show profiles with problems` hides the working ones. The buttons are:

- `Use this profile`: keep the selected profile in use instead of the ranking's choice.
- `Back to automatic`: let the ranking choose again.
- `Check all now`: measure all running profiles now.

Limits:

- An auto selector cannot be a hop in a chain, or the `Front Proxy` or `Landing Proxy` of a group.
- It skips these profiles of its groups: chains and other auto selectors, OpenVPN and OpenConnect, Tailscale, Extra Core, custom sing-box full configs, and custom configs that cannot be read. Xray full configs are skipped when they cannot be combined with the front or landing proxy of their group.

## IP scanner {#ip-scanner}

Since 1.4.0, `Tools` → `IP Scanner` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)) checks lists of IP addresses and keeps the ones that work from your network. Use it, for example, to find a Cloudflare address that is not blocked, for profiles whose servers are behind a CDN, a working Cloudflare WARP endpoint, or DNS resolvers for a DNS tunnel. The results form an IP list, which a group or a profile can use as its [server address](@/guides/subscriptions.md#server-address).

The `IP Scanner` window lists your scans with their state and the number of results. Hover over a scan to start, pause or resume it, open it or delete it. Drag scans to reorder them. A scan keeps running when you close its window.

To create a scan:

1. Click `New scan` and enter a `Name`.
2. Choose `Generic scan` to check addresses with ping, TCP, TLS or HTTP, and optionally through one of your profiles. Choose `WARP scan` to find Cloudflare WARP endpoints that complete a WireGuard, AmneziaWG or MASQUE connection. Choose `DNS resolver scan` to find resolvers for one of your DNS tunnel profiles; see [DNS resolver scans](#dns-scan).
3. Click `OK`. The scan's window opens.
4. Set the targets on the `Targets` tab and the checks on the `Phases` tab.
5. Click `Start`. The window shows the progress and the last log lines, and the `Results` tab fills.

The `Targets` tab:

| Setting | Default | What it does |
| --- | --- | --- |
| `Base IP list` | – | The addresses to scan. `Manage` opens the [IP lists](#ip-lists). A WARP scan starts with `Cloudflare WARP (WireGuard)`, and `Use built-in WARP ranges` chooses the list and ports for the selected WARP mode. |
| `Ports` | empty; WARP: the WireGuard ports | Ports to scan, separated by commas or spaces. The list next to it decides how they combine with ports stored in the IP list: `Ignore the list's ports`, `Merge with the list's ports` (default) or `Only the list's ports`. Addresses left without a port are scanned on port 443, or 80 for plain HTTP. In a WARP scan, `WARP ports` fills in the ports of Cloudflare's endpoints. |
| `Shuffle the target order` | on | Scans the addresses in random order. |
| `Scan IPv6 addresses` | off | Off: IPv6 ranges in the list are skipped. |
| `Stop after (results)` | no limit; WARP: 10 | The scan completes when it has this many results. |
| `Concurrency` | 64; WARP: 16 | How many addresses are checked at the same time, in every phase. |
| `Spawn interval (ms)` | 2 | The delay between starting two checks. |

The `Phases` tab has the checks. An address must pass every check that is on, in this order:

| Phase | Default | What it checks |
| --- | --- | --- |
| `ICMP (ping)` | off | The address answers ping. |
| `TCP connect` | on | The port accepts a TCP connection. Generic scans only. |
| `TLS / HTTP` | off | A TLS handshake succeeds and, unless `Method` is `None (TLS handshake only)`, an HTTP request gets the answer 200 OK. You can set the `SNI`, `Host header`, `Path`, `HTTP version`, TLS versions, `uTLS fingerprint` and TLS fragmentation. Generic scans only. |
| `Config test` | off | A copy of the chosen `Profile`, pointed at the address, loads the `Test URL`. This finds addresses that really work for your server, for example CDN addresses. Generic scans only. |
| `WARP test` | on | A WARP connection through the address works, with the chosen `Mode`: `WireGuard`, `AmneziaWG` or `MASQUE`. WARP scans only. |

The `WARP test` needs a WARP identity. The default, `Dedicated for this scan (recommended)`, needs one click on `Register`, which registers a new WARP device with Cloudflare. You can also use `From a profile` or `Built-in WARP settings`.

The `Results` tab shows each address with its `Port` and `Latency`. Click a column header to sort. The buttons are:

- `Save as IP list`: copies the results into a new IP list.
- `Copy`: copies the selected entries, or all entries when none is selected.
- `Export`: saves the results to a text file.
- `Remove selected`: deletes the selected entries.

When a scan has results, `Start` asks what to do. `Scan from the beginning` clears the results and scans the base list again. `Rescan current results` checks only the results again and removes the ones that stopped working. `Pause` keeps the progress, so `Resume` continues later. If you change the targets of a paused scan, it can only start from the beginning. Settings cannot be changed while a scan runs.

### DNS resolver scans {#dns-scan}

A DNS resolver scan finds DNS resolvers that relay the tunnel of one of your [DNS tunnel](@/advanced/dns_tunnels.md) profiles: DNSTT (also in VayDNS mode), MasterDNS, or VLESS (Xray) with XDNS. It tries each address of the base list as the only resolver of that profile and keeps the resolvers that pass.

There are two ways to create one:

- In the `IP Scanner` window, click `New scan` and choose `DNS resolver scan`. Then choose the `Profile` on the `Phases` tab.
- In the editor of a DNS tunnel profile, click `Find resolvers…` next to `IP list`, under the profile's resolvers. Throne opens the newest DNS resolver scan of this profile, or creates one named "Resolvers for" and the profile's name. The button works only for a saved profile, and the scan tests the profile as it was last saved.

On the `Targets` tab, choose the resolvers to try as the `Base IP list`, for example `Iran DNS resolvers` or `Russia DNS resolvers`. A DNS resolver scan has no `Ports` field: each address is tried on the port stored in the list, or on port 53.

The `Phases` tab has a single check, `DNS tunnel test`:

| Setting | What it does |
| --- | --- |
| `Profile` | The DNS tunnel profile whose tunnel the resolvers must relay. The list shows only DNSTT, MasterDNS and XDNS profiles. |
| `Test` | What is checked. The choices depend on the type of the profile; see below. |
| `Timeout (ms)` | The time limit for the whole test of one resolver, from 1000 to 300000 ms. Empty means the default of the chosen test, which the empty field shows. |

The tests, from the quickest to the most thorough. For each type of profile, a test also does the checks of the tests before it:

| Test | Profiles | Default timeout | What it checks |
| --- | --- | --- | --- |
| `Poll: relays one tunnel query` | DNSTT, XDNS | 15000 ms | The resolver passes one tunnel query to the tunnel server and returns a valid answer. |
| `Query names: also relays names as long as the profile sends` (default) | DNSTT, XDNS | 90000 ms; XDNS: 60000 ms | The resolver also relays query names as long as the ones that the profile sends. The scan finds the longest name that the resolver relays. |
| `Handshake: also opens a tunnel session` | DNSTT | 120000 ms | A tunnel session with the server opens through the resolver. |
| `Quick: relays the smallest packets` (default) | MasterDNS | 20000 ms | The resolver relays queries and answers of the smallest size that the profile needs. |
| `Full: also measures the upload and download MTU` | MasterDNS | 90000 ms | The scan also finds the largest query and answer that the resolver relays. |

On the `Results` tab, `Latency` is how long the resolver took to answer: the first tunnel query for DNSTT and XDNS, and the average round trip at the sizes found for MasterDNS. A DNS resolver scan adds these columns, each shown only when a result has a value for it:

| Column | Meaning |
| --- | --- |
| `Max QNAME` | DNSTT and XDNS: the longest query name that the resolver relays, in the units of `Max QNAME Length` for DNSTT and of `lenLimit` for XDNS. |
| `Upload MTU`, `Download MTU` | MasterDNS: the largest payload per query and per answer that the resolver relays. |

Resolvers that fail are not kept. The log shows each one with the stage where it stopped: `poll`, `qname` or `handshake` for DNSTT and XDNS, and `timeout` (no answer), `decode` (an answer that cannot be read), `upload` or `download` (no packet size passed) for MasterDNS.

To make the results the profile's resolvers, click `Use as resolvers` on the `Results` tab. The scan's result list then becomes the `IP list` of the scan's profile. The resolvers typed in the profile stay and are used first. A running profile restarts. If the profile's editor is open, the editor gets the list instead; click `OK` there to save it.

The profile reads the list each time it starts, so it always uses the latest results. `Scan from the beginning` empties the list, and `Rescan current results` removes the resolvers that stopped working. For a set that does not change, click `Save as IP list` and choose the new list as the profile's `IP list`.

### IP lists {#ip-lists}

Click `IP Lists` in the `IP Scanner` window to manage the lists that scans read and that groups and profiles can use. Throne starts with seven lists: `Cloudflare CDN`, `Fastly CDN`, `Gcore CDN`, `Cloudflare WARP (WireGuard)`, `Cloudflare WARP (MASQUE)`, and `Iran DNS resolvers` and `Russia DNS resolvers`, which hold resolvers that are reachable inside these countries, for [DNS resolver scans](#dns-scan). If you delete them, `Import` → `Add default lists` adds them again.

- `New` creates a list. Its `Source` is `Manual` (you write the entries), `URL` (an http or https address that Throne downloads) or `Rule-set` (the IP ranges of a rule-set, such as `geoip-ir`, or a rule-set URL). For a `URL` or `Rule-set` list, `Fetch now` downloads it, and `Auto update` → `Enable` refreshes it after the number of minutes in `Every`, at least 30.
- Write one entry per line, for example `1.2.3.4`, `1.2.3.0/24`, `1.2.3.4:443`, `[2606:4700::1]:443`, `1.2.3.0/24 443` or `1.2.3.1-1.2.3.9`.
- `Import` creates lists `From text…`, `From file…` (text and CSV files, or sing-box rule-sets, up to 50 MB each), `From URL…` or `From rule-set…`.
- `Duplicate` copies a list, and `Export` saves it to a text file.

The results of each scan are a list too, marked "Result of scan" and the scan's name. A group or a profile can use it directly, but `Scan from the beginning` empties it; use `Save as IP list` for a list that stays as it is. When you delete a scan, you choose whether its result list is deleted too.

## On Android {#android}

Throne for Android has the same tests and the same auto selector.

- On the Profiles screen, the `⋮` menu has `Test` for the current group, with `URL test`, `IP & country test`, `Speed test` and `Stop testing`. Before 2.0.5, these items are directly in the `⋮` menu. In 2.0.3, the menu also has `Speed test current connection`; since 2.0.4, it is in the menu that opens when you touch and hold the bar at the bottom of the screen. The `⋮` menu also has `Resolve domains`, `Clear test results`, `Clear traffic statistics`, `Remove` (`Duplicates`, `Unavailable`, `Invalid`, `Insecure configs`) and `Sort`. For selected profiles, use `Test` in the selection menu.
- The `⋮` button of a profile has `Test` for that profile. Since 2.0.5, it also has `Clear test results` and `Clear traffic statistics`, which clear them for this profile only, without asking. Each one appears only when the profile has test results or traffic.
- Before a speed test of more than one profile, the app asks for confirmation: "Speed testing can use significant data."
- A test panel shows the progress, the counts `Working`, `Failed`, `Pending` and `Skipped`, a latency chart (≤100 ms, ≤300 ms, >300 ms, fail), the fastest profile, the countries and the number of distinct exit IPs. It also offers actions such as `Sort by latency` or `Sort by speed`, `Remove unavailable` (with the number of failed profiles), `Connect to fastest` (`Select fastest` when you are not connected) and `Dismiss test results`.
- While connected, tap the statistics bar ("Connected, tap to check connection") to test the live connection. The result looks like "Success: HTTP handshake took 120ms" or "Failed: …". Since 2.0.3, it reads "HTTP: 120 ms".
- Since 2.0.2, the statistics bar also shows the exit IP address of the connection with the country flag, and in portrait mode the country and city. In 2.0.2, touch and hold the bar to see them in full. Throne asks an IP lookup service through the proxy after it connects and after each tap on the bar; in 2.0.2 this is always `ip-api.com`. Since 2.0.3, the bar shows only the flag and the country, and touching and holding the bar opens `Live graphs` instead (since 2.0.4: a menu with `Live graphs` and `Speed test current connection`).
- Since 2.0.3, `Settings` → `Testing` has `Look up exit country` (on by default) and `Lookup service` under `Exit country`, with the same services as on the desktop. With the lookup off, the bar shows no country. In 2.0.3, `Automatic` asks `ip-api.com`, `ipwho.is`, `ipinfo.io` and `Cloudflare` in this order; since 2.0.4, it asks `IP2Location.io` first, as on the desktop.
- Throne for Android has no IP scanner. Since 2.0.3, a sort that you choose in `Sort` stays, as on the desktop, and `Manual order` stops it.
- To create an auto selector, choose `Add profile` → `Manual settings` → `Auto selector`. The options and defaults are the same as on desktop, written in sentence case, for example `Servers from`, `Only names matching` and `Run the best`. Since 2.0.3, `Servers from` opens a list in which you check one or more groups. Since 2.0.5, `Only countries` also takes `!` codes, and the auto selector starts as soon as enough profiles work, as on the desktop. Tap the auto selector's status line on the Profiles screen to open `Auto selector stats`, with `Use this profile`, `Back to automatic` and `Check all now`.
