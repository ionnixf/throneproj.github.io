+++
title = "Routing"
description = "Choose which traffic goes through the proxy, which goes direct and which is blocked, with ready-made profiles or your own rules."
weight = 40
toc = true
+++

Routing decides where each connection goes: through your proxy, directly to the internet, or nowhere. This page shows how to download a ready-made routing profile for your country, write your own rules, and share profiles. If you want everything to go through the proxy, you do not need to change anything.

## How routing works {#how-routing-works}

A routing profile is a list of rules plus a default outbound. When a connection starts, Throne checks the rules from top to bottom. The first rule that matches decides where the connection goes. If no rule matches, the connection uses the default outbound.

An outbound is a place where traffic can go:

| Outbound | Where the traffic goes |
| --- | --- |
| `proxy` | Through the profile you started. When WARP is on, WARP is added as the last hop. |
| `direct` | Straight to the internet, without the proxy. |
| `block` | Nowhere. The connection is refused. |
| `warp-bypass` | Through the profile you started, but without WARP. When WARP is off, the same as `proxy`. See [WARP](@/advanced/warp.md#warp-bypass). |
| A saved profile | Through that profile instead of the one you started. Only in advanced rules. |

Exactly one routing profile is active at a time. The built-in `Default` profile has only a DNS rule (`Route DNS`) and sends everything to `proxy`.

To change the active routing profile, use one of these:

- The list at the bottom of the `Routing` menu. The active profile has a check mark.
- Tray menu → `Select Routing`.
- `Settings` → `Routing Settings` → `Common` → `Routing Profile`, then `OK`.

Choosing a routing profile in the `Routing` menu or the tray restarts the running connection, so the new rules apply at once. The window title shows the active routing profile when it is not `Default`.

Since 1.4.0, a group can use its own routing profile instead of the one you choose here. See [Routing profile per group](#group-routing).

## Download ready-made profiles {#download-profiles}

Throne can download routing profiles that are maintained for China, Iran and Russia.

1. Open the `Routing` menu → `Download Profiles` and choose `China`, `Iran` or `Russia`.
2. Throne lists the profiles it will add ("Add these remote routing profiles?"). Keep `Auto update` ticked and click `OK`.
3. Open the `Routing` menu again and choose one of the new profiles at the bottom. Downloading alone does not change your active profile.

| Country | Profile | What it does |
| --- | --- | --- |
| China | `Bypass China` | Proxies everything except Chinese sites, Chinese IP addresses and your local network, which go direct. Sites on an anti-censorship list always use the proxy. |
| China | `Proxy China Blocked` | Sends everything direct, except sites known to be blocked in China. |
| Iran | `Bypass Iran` | Proxies everything except Iranian sites, Iranian IP addresses and your local network. |
| Russia | `Bypass Russia` | Proxies everything except Russian sites and IP addresses, sites that work only inside Russia, and your local network. |
| Russia | `Proxy Russia Blocked` | Sends everything direct, except sites and IP addresses blocked in Russia. |
| Russia | `Proxy Antizapret` | Sends everything direct, except sites on the Antizapret block list. |
| Russia | `Proxy Refilter` | Sends everything direct, except sites and IP addresses on the Re:filter block list. |

A "Bypass" profile suits you when most sites you visit are abroad. A "Proxy … Blocked" profile sends less traffic through your server, which helps when the server is slow or has a data limit. The downside: a blocked site that is missing from the list does not load.

These profiles are alternatives. Activate the one you want and leave the others; there is no need to delete them. They are [remote profiles](#remote-profiles), so they can update themselves. The lists are maintained in the [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) repository and can change over time.

If the download fails with "Requesting profile error", choose another `Remote Rule-set Mirror` (see below) and try again.

## Routing profiles {#profiles}

`Settings` → `Routing Settings` (also the first item of the `Routing` menu) opens the Routes window:

| Tab | What it contains |
| --- | --- |
| `Common` | The active routing profile, domain strategies and the rule-set mirror. |
| `Warp` | Cloudflare WARP; see [WARP](@/advanced/warp.md#generate). |
| `DNS` | DNS servers and options; see [DNS](@/guides/dns.md#settings). |
| `Route` | Your routing profiles. |

In Simple mode, the window has only the `Common` and `Route` tabs. `Common` then shows `Routing Profile`, and below it the WARP settings and the `Simple DNS Settings` as sections. The domain strategies and `Remote Rule-set Mirror` are hidden, but keep their values. See [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

Up to 1.3.2, there was also a `Hijack` tab. It was removed in 1.4.0; see [DNS](@/guides/dns.md#deprecated).

Settings on the `Common` tab:

| Setting | Default | What it does |
| --- | --- | --- |
| `Routing Profile` | `Default` | The active routing profile. |
| `Default Domain Strategy` | empty | IPv4 or IPv6 preference when Throne looks up the address of a server or of a site that goes direct. |
| `Resolve Domain Strategy` | empty | When set, Throne looks up the IP address of every requested domain before the rules run, so IP rules can match domains too. |
| `Remote Rule-set Mirror` | `jsDelivr(Cloudflare)` | Where rule-sets and profiles stored on GitHub are downloaded from. `GitHub` downloads them directly. |

The strategies are `ipv4_only`, `ipv6_only`, `prefer_ipv4` and `prefer_ipv6`. Do not choose `ipv6_only` on a network without IPv6.

Buttons on the `Route` tab:

| Button | What it does |
| --- | --- |
| `New` | Creates a `Structured profile`, a `Raw profile` or a `Remote profile`. |
| `Clone` | Copies the selected profile. |
| `Export` | Copies the selected profile as a `throne://route/` link (`Ctrl+C`). |
| `Import` | Adds a profile from the clipboard (`Ctrl+V`). |
| `Edit` | Opens the selected profile. Double-clicking it does the same. |
| `Delete` | Deletes the selected profile (`Del`). The last profile cannot be deleted. |
| `Update` | `Update selected` or `Update all` downloads remote profiles again. |

Changes in the Routes window are saved when you click `OK`. If a connection is running, the main window then shows "Settings changed, restart to apply". Click `Restart` there to use the new rules.

Since 1.4.0, you can also right-click a routing profile at the bottom of the `Routing` menu. This opens the profile in the editor, without `Routing Settings` and without making the profile active. When you save a change to the active profile while a connection runs, the same notice appears.

There are three kinds of routing profiles: structured profiles, which you edit in Throne with [simple rules](#simple-rules) and [advanced rules](#advanced-rules); [remote profiles](#remote-profiles), which are downloaded from a URL; and [raw profiles](#raw-profiles), written as sing-box JSON. Most people only need structured ones.

To create a structured profile:

1. On the `Route` tab, click `New` → `Structured profile`.
2. Enter a `Name` and choose the `Default outbound`.
3. Add rules on the `Basic` tab or the `Advanced` tab.
4. Click `OK`. On the `Common` tab, choose the profile as `Routing Profile`, then click `OK`.

A new profile starts with a `dns-hijack` rule. Throne adds the same DNS step to every structured profile anyway (see [DNS](@/guides/dns.md#how-dns-works)), so you can leave the rule as it is.

Since 1.4.0, the `DNS` tab of a profile can give it its own DNS servers, which replace the global ones while the profile is active. See [DNS per routing profile](@/guides/dns.md#route-dns).

The `Endpoints` tab appears when you have OpenVPN or OpenConnect profiles. It runs them next to your proxy for the networks they announce; see [split tunnel](@/advanced/vpn_profiles.md#split-tunnel). In Simple mode, it appears only for a profile that already has endpoints.

## Simple rules {#simple-rules}

The `Basic` tab of a profile has four boxes: `Direct`, `Proxy`, `Block` and `Warp-bypass`. Each line in a box sends matching traffic there. `How to use` shows a short summary of the syntax.

| Line | Matches |
| --- | --- |
| `domain:example.com` | Only `example.com`. |
| `suffix:example.com` | `example.com` and all its subdomains, such as `www.example.com`. |
| `keyword:example` | Every domain that contains `example`. |
| `regex:^cdn[0-9]*\.example\.com$` | Domains that match a regular expression (Go RE2 syntax). |
| `ruleset:geosite-youtube` | Everything in a [rule-set](#rule-sets): a built-in name or the URL of an `.srs` file. |
| `ip:10.0.0.0/8` | IP addresses in a range. A single address such as `1.2.3.4` also works. |
| `processName:Telegram.exe` | Connections of a program, by file name. |
| `processPath:C:\Program Files\App\app.exe` | Connections of a program, by full path. |

Rules for writing lines:

- Write one entry per line. Empty lines are ignored.
- Write the prefix exactly as shown, including upper and lower case (`processName`, not `processname`).
- `domain:`, `suffix:` and `keyword:` values are saved in lower case.
- A process name must match exactly, including upper and lower case and `.exe` on Windows. The [Connections tab](#connections-tab) shows the exact name.
- Throne checks the lines when you switch tabs or click `OK`, and lists the lines it cannot use under "Some rules could not be added". Switching tabs drops those lines. `OK` does not save until you fix them. Since 1.4.0, the editor also marks such lines while you type.

{% alert_info() %}
Process rules only see traffic that reaches Throne. In system proxy mode, programs that ignore the proxy never reach it, so use process rules together with `Tun Mode`. See [TUN Mode](@/guides/tun_mode.md#enable).
{% end %}

**Example 1: proxy everything except local sites, and block ads.** Set `Default outbound` to `proxy`. In `Direct`:

```text
suffix:example.com
ruleset:geosite-ir
ruleset:geoip-ir
```

In `Block`:

```text
ruleset:geosite-category-ads-all
```

Replace `ir` with your country code, for example `cn` or `ru`.

**Example 2: only one program and one site through the proxy.** Set `Default outbound` to `direct`. In `Proxy`:

```text
processName:Telegram.exe
suffix:example.org
```

### Order of simple rules {#simple-rules-order}

Each box becomes ordinary rules on the `Advanced` tab, named after the box, such as `Simple Address Bypass` (addresses in `Direct`) or `Simple Process Name Proxy` (programs in `Proxy`). In a new profile they are added in this order: `Direct`, `Block`, `Proxy`, `Warp-bypass`. Because the first match wins, a line in `Proxy` has no effect when a broader line in `Direct` already matches the same traffic.

To make an exception, move it up on the `Advanced` tab. For example, to proxy `example.ir` in a profile that sends `geosite-ir` direct:

1. Add `suffix:example.ir` to the `Proxy` box.
2. Open the `Advanced` tab and select `Simple Address Proxy`.
3. Click `Move Up` until it is above the rule that contains `geosite-ir`.

## Advanced rules {#advanced-rules}

The `Advanced` tab shows every rule of the profile in order, with `New`, `Move Up`, `Move Down` and `Delete`. The right side shows the rule's `Name` and a `Preview` of the rule in sing-box JSON. In Simple mode, the tab appears only when the profile has rules that the `Basic` tab cannot show, such as your own advanced rules or disabled rules.

Since 1.4.0, each rule in the list has a check box. Untick it to disable the rule: the rule stays in the profile, greyed out, but Throne leaves it out of the config, as if it were not there. Tick it again to turn the rule back on. This also works for the rules that the boxes of the `Basic` tab make, such as `Simple Address Bypass`: their lines stay in the box, but do not apply while the rule is disabled. Rules of the `Endpoints` tab cannot be disabled.

To add a rule:

1. Click `New`.
2. Choose an `Action`.
3. Open the `+` tab and tick the attributes you need. Each attribute becomes a tab. Unticking an attribute clears its value.
4. Fill in the tabs. Lists take one entry per line.

| Action | What it does |
| --- | --- |
| `route` | Sends matching traffic to the rule's `outbound`: `proxy`, `direct`, `warp-bypass` or a saved profile. A new rule sends to `direct` until you tick `outbound` and choose another one. |
| `reject` | Blocks matching traffic. `method` and `no_drop` change how. |
| `hijack-dns` | Answers DNS queries with Throne's DNS. |
| `route-options` | Sets options such as `override_address`, `override_port` or `tls_spoof`, then continues with the next rules. |
| `sniff` | Detects the protocol and the domain of a connection, then continues. |
| `resolve` | Looks up the IP address of the domain (with `strategy`), then continues. |
| `bypass` | Linux only, for advanced setups. See the [sing-box documentation](https://sing-box.sagernet.org/configuration/route/rule_action/). |

To block traffic in an advanced rule, use `reject`. The name `block` exists only as a default outbound and as the `Block` box.

Since 1.4.0, the `route`, `route-options` and `bypass` actions also offer `tls_fragment`, `tls_fragment_fallback_delay` and `tls_record_fragment`. They split the TLS handshake of the matching connections, like the [TLS fragment](@/advanced/presets.md#tls-fragment) options of a profile. This helps for connections that go `direct`, for example to a site that a simple filter blocks by its name. `tls_fragment_fallback_delay` works only together with `tls_fragment`.

| Attributes | What they match |
| --- | --- |
| `domain`, `domain_suffix`, `domain_keyword`, `domain_regex` | The requested domain. |
| `ip_cidr`, `ip_is_private` | The destination address; `ip_is_private` covers private and local ranges. |
| `rule_set` | Built-in rule-set names or `.srs` URLs. |
| `port`, `port_range` | The destination port, for example `443` or `1000:2000`. |
| `source_ip_cidr`, `source_ip_is_private`, `source_port`, `source_port_range` | Where the connection comes from, for example another device on your network. |
| `process_name`, `process_path`, `process_path_regex` | The program on this computer. |
| `network`, `protocol`, `ip_version` | `tcp`, `udp` or `icmp`; a detected protocol such as `tls`, `quic` or `bittorrent`; `4` or `6`. |
| `inbound` | Where the connection entered Throne: `mixed-in` (the proxy port) or `tun-in` (TUN mode). |
| `wifi_ssid`, `wifi_bssid` | The Wi-Fi network you are connected to. Not available on macOS. |
| `package_name` | Android apps. On the desktop, a rule with this attribute never matches. |
| `invert` | `true` makes the rule match when its conditions do not match. |

Inside one rule, the domain, IP and `rule_set` attributes match when any one of them matches. Other attributes, such as `port` or `process_name`, must match as well.

Tips:

- **Exceptions go first.** Put a rule for one site above the rule for the whole country or list.
- **Process rules** only see programs whose traffic reaches Throne. Use TUN mode to include programs that ignore the system proxy; see the note under [Simple rules](#simple-rules).
- **Send traffic to a specific server.** Tick `outbound` and choose a saved profile, shown as `[group] name`. Throne runs that profile next to the one you started. Extra Core profiles and full custom configs cannot be used here.
- **Rules you cannot edit.** A rule named "… route prefer" belongs to the `Endpoints` tab. You can only move it.

## Rule-sets {#rule-sets}

A rule-set is a list of domains or IP ranges stored in a separate file. Use it in a simple rule (`ruleset:<name>`) or in the `rule_set` attribute of an advanced rule.

- **Built-in names.** Throne knows more than 2,000 rule-sets by name. Names that start with `geosite-` hold domains, for example `geosite-youtube`, `geosite-cn` or `geosite-category-ads-all`. Names that start with `geoip-` hold IP ranges, for example `geoip-ir`, `geoip-telegram` or `geoip-private`. When you type `ruleset:` in a simple-rule box, or in the `rule_set` tab, Throne suggests matching names.
- **Your own files.** The URL of any sing-box binary rule-set works, for example `ruleset:https://example.com/lists/my-sites.srs`. The file name in the URL must contain `.srs`.

Throne downloads the rule-sets of a profile when the profile starts and keeps them in the core's cache. `Routing` → `Update Rule-Sets` downloads fresh copies. It is available while a connection runs and reports how many rule-sets it refreshed.

Built-in rule-sets are stored on GitHub. If GitHub is blocked or slow on your network, change `Routing Settings` → `Common` → `Remote Rule-set Mirror`. The mirror also applies to the AdBlock list and to routing profiles stored on GitHub. Your own `.srs` URLs are always used as written.

## Remote profiles {#remote-profiles}

A remote profile is a structured profile that Throne downloads from a URL. The profiles from [Download Profiles](#download-profiles) are remote profiles. A provider can also give you one with a [`throne://remoteroute/` link](@/advanced/deeplinks.md#remoteroute).

To add one yourself:

1. On the `Route` tab, click `New` → `Remote profile`.
2. Enter a `Name` and the `URL`. The address can serve a `throne://route/` link, its Base64 text, or the profile's JSON.
3. Click `Preview` to look at the profile without changing anything, or `Fetch` to load its rules now.
4. Tick `Auto update` to keep the profile up to date, then click `OK`.

The URL must serve a structured profile. Raw profiles cannot be remote profiles.

Automatic updates need two things: the profile's `Auto update` box, and `Settings` → `Basic Settings` → `Subscription` → `Routing profiles auto update` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). The second one is off by default. Its interval is in minutes (default 1440, once a day); values under 30 turn it off. The `Runtime Stats` tab shows `Next remote route update`. To update at once, use `Update` on the `Route` tab.

An update replaces the rules and the default outbound with the downloaded ones, so your own changes to the profile are lost. To change a remote profile safely, `Clone` it, open the copy and untick `Auto update`. An update does not restart a running connection: the new rules are used the next time you start a profile. Since 1.4.0, when an automatic update changes the routing profile that the running connection uses, the main window shows "Settings changed, restart to apply". Click `Restart` there to use the new rules at once.

Since 1.4.0, two things survive an update:

- A rule that you [disabled](#advanced-rules) stays disabled, as long as the downloaded rules have a rule with the same name. This way you can switch off a rule of a remote profile and keep `Auto update` on.
- The profile's own [DNS settings](@/guides/dns.md#route-dns). Throne never takes DNS settings from the URL of a remote profile.

## Routing profile per group {#group-routing}

Since 1.4.0, a group can connect with its own routing profile instead of the one you choose in the `Routing` menu. For example, the servers of one provider can use `Bypass Iran`, while all other groups use `Default`.

1. Right-click the group's tab and choose `Edit selected Group`, or choose `Groups` → `Edit current Group`.
2. Choose a profile in `Routing Profile`. `Default (…)`, the default, follows the routing profile of the `Routing` menu and shows its name.
3. Click `OK`.

How Throne picks the routing profile:

- The group of the profile that you start decides. For a chain or an auto selector, this is the group of the chain or the auto selector, not the groups of the profiles in it. Front and landing proxies, and saved profiles that rules send traffic to, use the routing profile of the profile that you started.
- The `Routing` menu, the tray menu and `Routing Settings` → `Common` → `Routing Profile` choose the routing profile of the groups that use `Default (…)`. The check mark in the `Routing` menu shows this choice.
- The window title and the tray tooltip show the routing profile that the running profile uses. The [Connections tab](#connections-tab) changes the rules of this routing profile.
- If the running profile's group uses its own routing profile, choosing another one in the `Routing` menu does not restart the connection. The log says "The group "…" uses its own routing profile, so the running profile keeps it."
- When you change the `Routing Profile` of a group while one of its profiles runs, the main window shows "Settings changed, restart to apply". Click `Restart` there.
- When you delete a routing profile that a group uses, the group goes back to `Default (…)`, and the log says so.
- The group's routing profile also brings its own [DNS settings](@/guides/dns.md#route-dns), if it has them.

### From the subscription {#subscription-routing}

Some providers send a routing profile with the subscription, in the format of the Happ app. Since 1.4.0, a subscription group can keep it as its own routing profile:

1. Open the group options as described above.
2. Under `Subscription`, tick `Use the subscription's routing profile`.
3. Click `OK` and update the subscription.

Throne reads the routing profile from the `routing` header of the provider's answer, or from a `happ://routing/…` line in the subscription. At the first update, it creates a structured routing profile from it, with the name that the provider gives it, or for example "Provider routing" for a group named Provider. If the group's `Routing Profile` is `Default (…)`, the new profile becomes the group's routing profile. In the group's list it shows as "… (from this subscription)". Every later update replaces its rules and its default outbound. The `Logs` tab shows a line such as "[Routing] Provider: updated the routing profile "…" from the subscription (120 entries, 2 skipped)".

Things to know:

- Throne takes the lists of sites and IP addresses that go direct, through the proxy or are blocked, and whether all other traffic goes through the proxy. The lists become the `Direct`, `Proxy` and `Block` boxes of the profile. Entries that Throne cannot use are skipped and counted in the log line.
- DNS settings from the subscription are never used. The profile follows the global DNS settings, unless you give it its own on its `DNS` tab. Updates keep these, and also its name and the rules that you [disabled](#advanced-rules).
- If the provider sends a `throne://route/` link instead, Throne leaves out rules that use a saved profile or `warp-bypass` or that change the destination, as well as VPN endpoints and DNS settings. It refuses raw profiles.
- In `Routing Settings` → `Route`, the profile shows as "… (updated by subscription "…")". You can edit it, but the next update overwrites its rules. The Connections tab does not add rules to it ("The current routing profile is updated by the subscription "…"."). To change it for good, `Clone` it and choose the clone as the group's `Routing Profile`. The clone is your own; the subscription keeps updating only the original.
- The profile belongs to the group. Deleting the group deletes the profile, also after you untick the option. Deleting the profile in `Routing Settings` unticks the option.
- If the subscription sends no routing profile, the group keeps the one it has.
- While `Routing Settings` is open, an update leaves the profile unchanged and says so in the log. The next update tries again.
- When an update creates or changes the routing profile that the running profile uses, the main window shows "Settings changed, restart to apply".

## Raw profiles {#raw-profiles}

A raw profile is a complete sing-box `route` object in JSON. Use it when you need sing-box features that the rule editor does not offer.

On the `Route` tab, click `New` → `Raw profile`. The editor checks the JSON and has a `Format JSON` button. Outbounds are written as numbers:

| Number | Outbound |
| --- | --- |
| `-1` | `proxy` |
| `-2` | `direct` |
| `-5` | `warp-bypass` |
| A profile ID | A saved profile. After `"outbound":` or `"final":`, the editor suggests your profiles by name and inserts the number. |

If `final` is missing, Throne uses `proxy`. Built-in rule-set names are not defined for you: add your own `rule_set` entries to the JSON. Throne also does not add the DNS step that structured profiles get automatically. For TUN mode, start your rules like this:

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

`Prevent modifications` uses the object exactly as written; Throne only turns the outbound numbers into tags. Throne then adds nothing of its own, so DNS, Xray profiles, chains and other features may stop working. Use it only if you know sing-box well.

`Enable AdBlock`, `Enable DNS Routing` and the rule actions of the Connections tab do not work with raw profiles. Throne for Android cannot use raw profiles.

## Connections tab {#connections-tab}

The `Connections` tab at the bottom of the main window lists open connections, grouped by program. The groups start collapsed; click a program to see its connections. For each connection, the tab shows the destination, the protocol, the outbound it used and its traffic. It is the quickest way to see where a site goes and to add a rule for it.

Since 1.4.0, the `Outbound` column shows the name of the profile that carried the connection, instead of an internal name such as `proxy`. For an auto selector, it shows the profile that the selector picked; for the WARP hop, it shows `WARP`. Long names are shortened, and the column gets narrower when a destination needs the room.

Since 1.4.0, the tab also shows which rule each connection matched. Hover over the `Outbound` column to see it, together with the full name of the outbound, for example `Matched: domain_suffix=github.com → My server (proxy)`. It names the entry of the rule that matched, such as `domain_suffix=github.com`, `rule_set=geosite-cn` or `process_name=Telegram.exe`. A rule that has no single entry to name, for example an inverted rule, shows as a whole. `final` means that no rule matched, so the connection used the default outbound.

The buttons at the top right of the tab: `Enable Filter` shows filter fields above the columns, `Expand All` or `Collapse All` opens or closes all program groups, and `Close every connection listed below` closes the listed connections.

Right-click a connection:

| Menu item | What it does |
| --- | --- |
| `Matched: … → …` | Greyed out. The rule that the connection matched, and where it sent the connection (since 1.4.0). |
| `Modify rules in "…"` | A heading with the name of the active routing profile. |
| `Direct` / `Proxy` / `Block` | Opens the targets for that box of the active routing profile (since 1.4.0, see below). |
| `Specific server` | Sends the targets through a server that you choose (since 1.4.0, see below). |
| `Copy Destination (…)`, `Copy Process Name (…)` | Copies the value. |
| `Close connection` | Closes the connection. On a program, `Close all connections for "…"` closes all of its connections. |
| `Expand All`, `Collapse All` | Opens or closes all program groups. |

`Direct`, `Proxy` and `Block` open a list of targets with check boxes:

- **Every domain level** of the host, as a `suffix:` rule. For `tile.openstreetmap.org`, the targets are `*.tile.openstreetmap.org`, `*.openstreetmap.org` and, below a line, the top-level domain `*.org`.
- **The name of the site** as a `keyword:` rule, such as `*openstreetmap*`. It also catches the site's other domains. It is offered only for names with 4 or more letters.
- **An IP address** as an `ip:` rule, when the connection has no domain.
- **The program**, below a line, as a `processName:` rule (`Process …`). On a program group, this is the only target.

A ticked target is already in that box; untick it to remove its rule. A target marked "(in Proxy)" or similar is in another box: ticking it moves the rule, so that it is never in two boxes at once. "(covered by … in …)" means that a broader rule in another box matches first, so this rule would never apply. Hover over a target to see its rule.

Click `OK`, then `Restart` in the "Settings changed, restart to apply" notice. A new line joins the existing simple rule of that box. If the box was empty, its rule is added at the end of the list, where a broader rule above it can still match first. Check the order on the `Advanced` tab.

`Specific server` (since 1.4.0) opens the same list of targets, with a server box above it: `Server (type part of its name to search):`. Choose a server there. The box lists your profiles as `[group] name`, except Extra Core profiles, full custom configs and chains that contain them. Then tick the targets that should go through that server, and click `OK`:

- Throne puts the targets into an advanced `route` rule whose `outbound` is that server. The rule is named after the server, for example `My server (address)` for domains and IP addresses, or `My server (process)` for programs. A new rule is added at the end of the list.
- As with the boxes, a target is never in two places at once. Ticking it moves it out of a box or out of the rule of another server. Unticking it removes it, and a rule that has no targets left is deleted.
- When the dialog opens, it selects a server that already has one of the targets. When you choose another server, the check boxes show the targets of that server.
- Throne runs the chosen server next to the profile you started, as with any rule that sends traffic to a saved profile (see [Advanced rules](#advanced-rules)).

`Direct`, `Proxy`, `Block` and `Specific server` are disabled when the active routing profile is raw, is locked with `Prevent modifications`, is a remote profile with `Auto update` on, or is updated by a [subscription](#subscription-routing) (since 1.4.0). Hover over the item to see the reason. For a remote profile, clone it first as described in [Remote profiles](#remote-profiles).

Since 1.4.0, these items change the routing profile that the running profile uses, which can be its [group's own](#group-routing).

Up to 1.3.2, the menu had `Append "…" to` and `Append process "…" to` instead. They added only the exact host or program to a box.

The tab needs `Settings` → `Basic Settings` → `Style` → `Connection statistics` → `Enable`, which is on by default. In Simple mode, this setting is hidden, but keeps its value (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

## AdBlock {#adblock}

`Routing` → `Enable AdBlock` blocks ads in whichever routing profile is active. Throne adds a block rule for a public ad-blocking rule-set (`adblocksingbox` from the 217heidai/adblockfilters project). The rule runs before your own `route` rules, so ads are blocked for direct and proxied traffic alike. Turning AdBlock on or off restarts the running connection.

- AdBlock works with structured and remote profiles, not with raw profiles.
- If a site stops working, turn AdBlock off to check whether the list blocks it.
- To block ads in one profile only, put `ruleset:geosite-category-ads-all` in its `Block` box instead.

## Share profiles {#share-profiles}

To share a routing profile, select it on the `Route` tab and click `Export` (or press `Ctrl+C` in the list). Throne copies a `throne://route/` link. Send the link to anyone.

To import a profile:

- **In Routing Settings.** Copy the link, open the `Route` tab and click `Import` (or press `Ctrl+V` in the list). Throne asks "Import routing profile … from the clipboard?". If the clipboard holds no profile, a box opens where you can paste a Throne route link, a remote route link, Base64 text or a JSON rule array. The imported profile is also selected on the `Common` tab, so it becomes active when you click `OK`.
- **From anywhere.** Click the link, or copy it and press `Ctrl+V` in the main window. Throne asks "Add this routing profile?" and adds it without making it active.

What the link carries:

- The name, the default outbound and the rules.
- Rules that send traffic to a saved profile refer to it by name. On the other computer, Throne uses a profile with the same name, or `proxy` if there is none, and tells you.
- VPN endpoints, shared without their credentials. When you import such a link, Throne creates the OpenVPN or OpenConnect profiles before it asks "Add this routing profile?", and keeps them if you cancel.
- Disabled rules, marked as disabled (since 1.4.0). Throne 1.3.2 and older, and Throne for Android 2.0.3 and older, leave such a rule out when they import the link.
- The profile's own [DNS settings](@/guides/dns.md#route-dns), if it uses them (since 1.4.0). When you import the link with `Import` in `Routing Settings`, the question names the servers: "It uses its own DNS servers: remote …, direct …". These servers see every name that you look up while the profile is active. If the settings in the link cannot be used, Throne leaves them out, and the profile uses the global DNS settings.

Since 1.3.2, Throne leaves out a rule that uses a condition it cannot store, for example an Android-only condition such as a network type or a package name regex. The question before the import lists such rules ("rule … dropped: unsupported field …"). Throne drops the whole rule, because without that condition it would match more connections. If no rule is left, the import fails.

For the link format, see [Deep Links](@/advanced/deeplinks.md#route).

## On Android {#android}

Throne for Android has the same routing profiles, rules and rule-sets. Open `Routing` from the drawer; tap a profile to make it active. The differences:

- **App rules.** The rule editor has an `Apps` field that matches Android apps. Its list starts with `Unknown apps`, for connections whose app cannot be found (since 2.0.2). Under `Advanced`, `Package name regex` (since 2.0.2) takes regular expressions, one per line in Go RE2 syntax, for example `^com\.google\.`. An app whose package name matches one of them counts as listed in `Apps`. An expression can match any part of the name; use `^` and `$` to match the whole name. The desktop cannot see Android apps, so there a rule with an app condition never matches.
- **Network rules** (since 2.0.2). `Network type` matches `Wi-Fi`, `Mobile data`, `Ethernet` or `Other`. `Metered network`, under `Advanced`, matches only while the current network is metered, such as mobile data or a Wi-Fi marked as metered.
- **Export and import.** Android shows a note when you export a profile with app or network rules. The desktop leaves out rules with a network type, a metered network or a package name regex when it imports the link (see [Share profiles](#share-profiles)). In the other direction, Android leaves out rules with conditions that it does not support, and warns about them (since 2.0.2).
- **Wi-Fi rules.** `Wi-Fi SSID` and `Wi-Fi BSSID` need precise location access set to "Allow all the time", and location turned on, because Android hides the Wi-Fi name otherwise. Throne asks when you save such a rule. If access is missing, the notification "Wi-Fi rules are inactive" appears.
- **Raw profiles.** Android refuses raw `throne://route/` links ("raw routing profiles are not supported on Android"). Raw profiles from a desktop backup are kept but cannot be used ("Raw (Throne desktop) · read-only").
- **VPN endpoints** in an imported link are dropped with a warning.
- **Disabled rules and DNS per profile** (since 2.0.4). Each rule in the editor has a check box that disables it, as on the desktop. The editor also has a `DNS` card with `Use the global DNS settings` and `Use custom DNS settings`; see [DNS per routing profile](@/guides/dns.md#route-dns). Remote profiles keep both across updates, and links carry both.
- **Clone** (since 2.0.4). Cloning a remote profile gives a local copy that never updates, so you can change its rules.
- **Routing profile per group** (since 2.0.5). The group editor has `Routing profile`, and subscription groups also have `Use the subscription's routing profile`. Both work as on the desktop; see [Routing profile per group](#group-routing). Raw profiles are not offered. On the `Routing` screen, a profile that a subscription updates shows "Updated by subscription "…""; touch and hold it to see what this means. When you choose another routing profile while the running profile's group uses its own, Throne says "The group "…" uses its own routing profile, so the running profile keeps it." After you change the routing profile of the running profile's group, tap `Apply` when Throne shows "Reload proxy service to apply changes".
- **Quick switch.** `Settings` → `Routing` → `Current routing profile` opens a dialog that lists your routing profiles. The dialog also has an `Enable WARP` / `Disable WARP` button. On the `Routing` screen of the drawer, tapping a profile makes it active without the dialog. In 2.0.4 and older, the dialog was in the menu (⋮) of the Profiles screen, as `Routing profile`.
- **Links.** A `throne://route/` link adds the profile but does not make it active.

`Download profiles`, `Update rule-sets` and remote profiles work as on the desktop. `AdBlock` and `Auto update remote profiles` are in `Settings` → `Routing`.

Since 2.0.2, a connection can start even when a rule-set cannot be downloaded. Throne shows "Could not download the rule-sets: …" with a `Connect anyway` button. If you confirm, Throne connects without the missing rule-sets. Rules that use them match nothing until Throne has downloaded them through the proxy, right after connecting. Throne keeps them for later connections.
