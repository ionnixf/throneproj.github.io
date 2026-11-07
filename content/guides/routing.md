+++
title = "Routing"
description = "Choose which traffic goes through the proxy, which goes direct and which is blocked, with ready-made profiles or your own rules."
weight = 40
toc = true
+++

Routing decides where each connection goes: through the proxy, direct, or nowhere. The built-in `Default` profile sends everything through the proxy, so you only need this page if you want something else.

## How routing works {#how-routing-works}

A routing profile is a list of rules plus a default outbound. Throne checks the rules from top to bottom and the first match decides where the connection goes. If no rule matches, the default outbound is used.

| Outbound | Where the traffic goes |
| --- | --- |
| `proxy` | Through the running profile, and through WARP when it is on. |
| `direct` | Straight to the internet. |
| `block` | Nowhere. The connection is refused. |
| `warp-bypass` | Through the running profile, without WARP. See [WARP](@/advanced/warp.md#warp-bypass). |
| A saved profile | Through that profile instead. Advanced rules only. |

One routing profile is active at a time. Switch it from the list at the bottom of the `Routing` menu, or from the tray menu → `Select Routing`. A running connection restarts with the new rules. A group can also use its own routing profile, see [Routing profile per group](#group-routing).

## Download ready-made profiles {#download-profiles}

1. Open `Routing` → `Download Profiles` and choose `China`, `Iran` or `Russia`.
2. Keep `Auto update` ticked and click `OK`.
3. Choose one of the new profiles at the bottom of the `Routing` menu. Downloading alone does not activate it.

| Profile | What it does |
| --- | --- |
| `Bypass China`, `Bypass Iran`, `Bypass Russia` | Proxies everything, except that country's sites and IP addresses and your local network. |
| `Proxy China Blocked`, `Proxy Russia Blocked` | Sends everything direct, except sites blocked in that country. |
| `Proxy Antizapret`, `Proxy Refilter` (Russia) | Sends everything direct, except sites on the Antizapret or Re:filter block list. |

Use a "Bypass" profile if most sites you visit are abroad. A "Proxy … Blocked" profile sends less traffic through your server, but a blocked site that is missing from the list will not load.

The lists are maintained in [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles). If the download fails, choose another `Remote Rule-set Mirror` (see [Rule-sets](#rule-sets)) and try again.

## Routing profiles {#profiles}

Routing profiles are managed on the `Route` tab of `Settings` → `Routing Settings`, which is also the first item of the `Routing` menu. Right-clicking a profile in the `Routing` menu opens it in the editor directly. Changes apply when you click `OK`; if a connection is running, click `Restart` in the "Settings changed, restart to apply" notice.

There are three kinds:

- **Structured profiles**, edited in Throne with [simple rules](#simple-rules) and [advanced rules](#advanced-rules). Most people only need these.
- **[Remote profiles](#remote-profiles)**, downloaded from a URL.
- **[Raw profiles](#raw-profiles)**, written as sing-box JSON.

To create a structured profile:

1. On the `Route` tab, click `New` → `Structured profile`.
2. Enter a `Name` and choose the `Default outbound`.
3. Add rules on the `Basic` or `Advanced` tab and click `OK`.
4. Activate it from the `Routing` menu.

A profile's `DNS` tab can give it its own DNS servers, see [DNS per routing profile](@/guides/dns.md#route-dns). Its `Endpoints` tab, shown when you have OpenVPN or OpenConnect profiles, runs them next to the proxy, see [split tunnel](@/advanced/vpn_profiles.md#split-tunnel).

The `Common` tab also has these settings (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)):

| Setting | What it does |
| --- | --- |
| `Default Domain Strategy` | IPv4 or IPv6 preference when Throne resolves a server or a site that goes direct. |
| `Resolve Domain Strategy` | Resolves every domain before the rules run, so IP rules also match domains. |
| `Remote Rule-set Mirror` | Where rule-sets and profiles hosted on GitHub are downloaded from. Default: `jsDelivr(Cloudflare)`. |

## Simple rules {#simple-rules}

The `Basic` tab of a profile has four boxes: `Direct`, `Proxy`, `Block` and `Warp-bypass`. Each line in a box sends matching traffic there.

| Line | Matches |
| --- | --- |
| `domain:example.com` | Only `example.com`. |
| `suffix:example.com` | `example.com` and all its subdomains. |
| `keyword:example` | Every domain that contains `example`. |
| `regex:^cdn[0-9]*\.example\.com$` | Domains that match a regular expression (Go RE2 syntax). |
| `ruleset:geosite-youtube` | Everything in a [rule-set](#rule-sets), by name or by `.srs` URL. |
| `ip:10.0.0.0/8` | An IP range, or a single address such as `1.2.3.4`. |
| `processName:Telegram.exe` | A program, by file name. |
| `processPath:C:\Program Files\App\app.exe` | A program, by full path. |

- Write one entry per line. Prefixes are case-sensitive: `processName`, not `processname`.
- A process name must match exactly, including case and `.exe` on Windows. The [Connections tab](#connections-tab) shows the exact name.
- The editor marks lines it cannot use. Fix them before you switch tabs, because switching tabs drops them.

{% alert_info() %}
Process rules only see traffic that reaches Throne. In system proxy mode, programs that ignore the proxy never reach it, so use process rules together with `Tun Mode`. See [TUN Mode](@/guides/tun_mode.md#enable).
{% end %}

**Example: proxy everything except local sites, and block ads.** Set `Default outbound` to `proxy`. In `Direct`:

```text
ruleset:geosite-ir
ruleset:geoip-ir
```

In `Block`:

```text
ruleset:geosite-category-ads-all
```

Replace `ir` with your country code, for example `cn` or `ru`.

**Example: only one program and one site through the proxy.** Set `Default outbound` to `direct`. In `Proxy`:

```text
processName:Telegram.exe
suffix:example.org
```

Each box becomes rules on the `Advanced` tab, such as `Simple Address Bypass` for the addresses in `Direct` and `Simple Process Name Proxy` for the programs in `Proxy`. They are added in the order `Direct`, `Block`, `Proxy`, `Warp-bypass`, and the first match wins, so a line in `Proxy` has no effect if a broader line in `Direct` matches the same traffic. To make an exception, for example to proxy `example.ir` while `geosite-ir` goes direct, add `suffix:example.ir` to `Proxy`, then on the `Advanced` tab move `Simple Address Proxy` above the rule that contains `geosite-ir`.

## Advanced rules {#advanced-rules}

The `Advanced` tab lists every rule of the profile in order. `Move Up` and `Move Down` change the order. Untick the check box of a rule to disable it without deleting it; this also works for the rules of the `Basic` boxes. In Simple mode, the tab only appears when the profile has rules that the `Basic` tab cannot show.

To add a rule, click `New` and choose an `Action`. Then tick the attributes you need on the `+` tab and fill them in, one entry per line.

| Action | What it does |
| --- | --- |
| `route` | Sends matching traffic to the rule's `outbound`. A new rule uses `direct` until you tick `outbound` and choose another one. |
| `reject` | Blocks matching traffic. In advanced rules, use this instead of `block`. |
| `hijack-dns` | Answers DNS queries with Throne's DNS. |
| `route-options` | Sets options such as `override_address` or `tls_spoof`, then continues with the next rules. |
| `sniff` | Detects the protocol and the domain of a connection, then continues. |
| `resolve` | Looks up the IP address of the domain, then continues. |
| `bypass` | Linux only. See the [sing-box documentation](https://sing-box.sagernet.org/configuration/route/rule_action/). |

`route`, `route-options` and `bypass` also offer `tls_fragment` and `tls_record_fragment`. They split the TLS handshake like the [TLS fragment](@/advanced/presets.md#tls-fragment) options of a profile, which helps `direct` connections to sites that a filter blocks by name.

| Attributes | What they match |
| --- | --- |
| `domain`, `domain_suffix`, `domain_keyword`, `domain_regex` | The requested domain. |
| `ip_cidr`, `ip_is_private` | The destination address. |
| `rule_set` | Rule-set names or `.srs` URLs. |
| `port`, `port_range` | The destination port, for example `443` or `1000:2000`. |
| `source_ip_cidr`, `source_port` and the like | Where the connection comes from, for example another device on your network. |
| `process_name`, `process_path`, `process_path_regex` | The program on this computer. |
| `network`, `protocol`, `ip_version` | `tcp`, `udp` or `icmp`; a detected protocol such as `tls` or `bittorrent`; `4` or `6`. |
| `inbound` | `mixed-in` (the proxy port) or `tun-in` (TUN mode). |
| `wifi_ssid`, `wifi_bssid` | The Wi-Fi network you are connected to. Not available on macOS. |
| `invert` | Makes the rule match when its conditions do not. |

Within one rule, the domain, IP and `rule_set` attributes match when any of them matches. Every other attribute must match as well.

To send traffic through a specific server, tick `outbound` and choose a saved profile, shown as `[group] name`. Throne runs it next to the profile you started. Extra Core profiles and full custom configs cannot be used this way.

## Rule-sets {#rule-sets}

A rule-set is a list of domains or IP ranges in a separate file. Use it as `ruleset:<name>` in a box, or in the `rule_set` attribute of an advanced rule.

- **Built-in names**: more than 2,000. Names starting with `geosite-` hold domains, such as `geosite-youtube` or `geosite-category-ads-all`. Names starting with `geoip-` hold IP ranges, such as `geoip-ir` or `geoip-telegram`. The editor suggests names as you type.
- **Your own files**: the URL of any sing-box binary rule-set. The file name must contain `.srs`.

Rule-sets are downloaded when a profile starts. `Routing` → `Update Rule-Sets` downloads fresh copies. If GitHub is blocked or slow on your network, change `Routing Settings` → `Common` → `Remote Rule-set Mirror`. The mirror also applies to the AdBlock list and to routing profiles hosted on GitHub.

## Remote profiles {#remote-profiles}

A remote profile is a structured profile that Throne downloads from a URL, like the ones from [Download Profiles](#download-profiles). Providers can also share one as a [`throne://remoteroute/` link](@/advanced/deeplinks.md#remoteroute).

To add one, click `New` → `Remote profile` on the `Route` tab, enter the `URL`, tick `Auto update` and click `OK`. The URL can serve a `throne://route/` link, its Base64 text or the profile's JSON. Raw profiles cannot be remote.

Automatic updates also need `Settings` → `Basic Settings` → `Subscription` → `Routing profiles auto update`, which is off by default (Advanced mode only). To update right away, click `Update` on the `Route` tab.

{% alert_warning() %}
An update replaces the rules and the default outbound of the profile, so your changes to it are lost. To customize a remote profile, `Clone` it and untick `Auto update` in the copy. Rules you disabled and the profile's own DNS settings are kept.
{% end %}

## Routing profile per group {#group-routing}

A group can connect with its own routing profile instead of the one in the `Routing` menu. For example, the servers of one provider can use `Bypass Iran` while all other groups use `Default`.

1. Right-click the group's tab and choose `Edit selected Group`.
2. Choose a `Routing Profile`. `Default (…)` follows the `Routing` menu.
3. Click `OK`.

The group of the profile you start decides. For a chain or an auto selector, that is the group of the chain or the auto selector, not of the profiles in it. The window title and the tray tooltip show the routing profile in use, and the [Connections tab](#connections-tab) changes that profile. A group's routing profile also brings its own [DNS settings](@/guides/dns.md#route-dns), if it has them.

### From the subscription {#subscription-routing}

Some providers send a routing profile with the subscription, in the Happ format. To use it, open the group's settings, tick `Use the subscription's routing profile` under `Subscription`, click `OK` and update the subscription.

Throne saves it as a structured routing profile named by the provider. If the group's `Routing Profile` is `Default (…)`, the new profile becomes the group's routing profile. Every update replaces its rules. DNS settings from the subscription are never used.

- To change it for good, `Clone` it and choose the clone as the group's `Routing Profile`.
- The profile belongs to the group. Deleting the group deletes it.

## Raw profiles {#raw-profiles}

A raw profile is a complete sing-box `route` object in JSON, for features that the rule editor does not offer. Create one with `New` → `Raw profile`.

Outbounds are written as numbers: `-1` is `proxy`, `-2` is `direct`, `-5` is `warp-bypass`, and a profile ID is that saved profile. The editor suggests your profiles after `"outbound":`. Without `final`, Throne uses `proxy`.

Built-in rule-set names are not defined in raw profiles, and Throne does not add the DNS step that structured profiles get. For TUN mode, start your rules like this:

```json
{
  "rules": [
    { "action": "sniff" },
    { "protocol": "dns", "action": "hijack-dns" },
    { "domain_suffix": ["example.com"], "outbound": -2 }
  ],
  "final": -1
}
```

`Prevent modifications` uses the JSON exactly as written, which can break DNS, Xray profiles and chains. AdBlock, `Enable DNS Routing` and the Connections tab actions do not work with raw profiles, and Throne for Android cannot use them.

## Connections tab {#connections-tab}

The `Connections` tab at the bottom of the main window lists open connections grouped by program, with their destination, protocol, outbound and traffic. Hover over the `Outbound` column to see the rule a connection matched; `final` means that no rule matched.

Right-click a connection to change where such traffic goes:

- `Direct`, `Proxy` or `Block` lists targets with check boxes: each domain level such as `*.example.com`, the site name as a keyword, the IP address when there is no domain, and the program. A ticked target is added to that box of the routing profile in use, and removed from any other box.
- `Specific server` does the same, but sends the targets through a server you choose.

Then click `Restart` in the "Settings changed, restart to apply" notice. A target joins the existing rule of that box. If the box was empty, its new rule goes to the end of the list, where a broader rule above it can still match first, so check the order on the `Advanced` tab.

These actions are not available for raw profiles, for profiles with `Prevent modifications`, for remote profiles with `Auto update`, and for profiles updated by a subscription. To change a remote or subscription profile, clone it first, see [Remote profiles](#remote-profiles) and [From the subscription](#subscription-routing).

If the tab is missing, turn on `Settings` → `Basic Settings` → `Style` → `Connection statistics` (Advanced mode only).

## AdBlock {#adblock}

`Routing` → `Enable AdBlock` blocks ads in whichever structured or remote profile is active, with the `adblocksingbox` list of the 217heidai/adblockfilters project. It runs before your own rules, so ads are blocked for direct and proxied traffic alike.

If a site stops working, turn AdBlock off to check whether the list blocks it. To block ads in one profile only, put `ruleset:geosite-category-ads-all` in its `Block` box instead.

## Share profiles {#share-profiles}

Select a profile on the `Route` tab and click `Export` (`Ctrl+C`) to copy it as a `throne://route/` link.

To import one, click the link, or copy it and press `Ctrl+V` in the main window. This adds the profile without activating it. `Import` (`Ctrl+V`) on the `Route` tab also selects it, so it becomes active when you click `OK`.

The link carries the name, the default outbound, the rules (disabled ones included) and the profile's own DNS settings. Rules that send traffic to a saved profile refer to it by name; if the other device has no profile with that name, they use `proxy`. OpenVPN and OpenConnect endpoints are shared without their credentials. For the link format, see [Deep Links](@/advanced/deeplinks.md#route).

## On Android {#android}

Throne for Android has the same routing profiles, rules and rule-sets. Open `Routing` from the drawer and tap a profile to activate it. The differences:

- **App rules.** The `Apps` field matches Android apps; `Unknown apps` matches connections whose app cannot be found. `Package name regex` matches package names. These rules never match on the desktop.
- **Network rules.** `Network type` matches Wi-Fi, mobile data or Ethernet, and `Metered network` matches metered networks. The desktop leaves these rules out when it imports a link.
- **Wi-Fi rules** need location access set to "Allow all the time", with location turned on.
- **Raw profiles** and the VPN endpoints of imported links are not supported.
- `AdBlock` and `Auto update remote profiles` are in `Settings` → `Routing`.
- If a rule-set cannot be downloaded, `Connect anyway` connects without it. Its rules match nothing until it has been downloaded through the proxy.
