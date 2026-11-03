+++
title = "Subscriptions & Groups"
description = "Add and update subscriptions, set group options, User-Agent and HWID, front and landing proxies, and fix update problems."
weight = 10
toc = true
+++

A subscription is a link from your provider that returns a list of servers. Throne keeps each subscription in its own group and can update it for you. This page explains how to add and update subscriptions, which options a group has, and what to do when an update fails.

## Groups and subscriptions {#groups}

Every tab above the profile list is a group. A group has one of two types:

- **Basic.** Holds profiles that you add yourself, for example from links, files, QR codes or `Program` → `New profile`.
- **Subscription.** Also has a `URL`. Throne downloads the profile list from this address and keeps the group in line with it.

You choose the type when you create the group. You cannot change it later.

{% alert_info() %}
An update makes a subscription group match the provider's list. It removes profiles that are not in the list and undoes changes that you made to the others. Keep your own profiles in a Basic group.
{% end %}

## Add a subscription {#add-subscription}

### From the clipboard {#from-clipboard}

1. Copy the subscription address. It starts with `https://` or `http://`.
2. In Throne, press `Ctrl+V`, or choose `Program` → `Add profile from clipboard`.
3. Throne shows the address and asks "How to update?". Choose `Create new subscription group` and click `OK`.

Throne creates a group named after the host name in the address and downloads its profiles right away. Since 1.4.0, it also switches to the tab of the new group. The same question appears when the address comes from a file, a QR code or text that you drop on the window. The three choices do this:

| Choice | Result |
| --- | --- |
| `Add profiles to this group` | Downloads the list once and adds the profiles to the current group. Throne does not save the address, so these profiles are never updated. |
| `Create new subscription group` | Creates a subscription group and downloads it. |
| `Import HTTP proxy profile` | Treats the address as an HTTP proxy server and adds it as one profile. |

### Create the group yourself {#create-group}

1. Choose `Groups` → `Add new Group`.
2. Enter a `Name`.
3. Set `Type` to `Subscription`.
4. Paste the address into `URL`.
5. Click `OK`.
6. Right-click the new tab and choose `Update subscription`.

Creating a group does not download it. The group stays empty until the first update in step 6.

`Groups` → `Manage Groups` opens a window with all groups. You can also create groups there with `New group`. Each row shows the type, the number of profiles, the address and the time of the last update. If the provider sends quota information, the row also shows `Used`, `Remain` and `Expire`, the time left and, while automatic updates are on, the update interval. See [Provider information](#provider-info). Each row has `Edit` and `Remove` buttons, and subscription rows also have `Update Subscription`.

### From a throne:// link {#from-link}

A `throne://addsub/…` link, for example on your provider's website, opens the `Add subscription` window with the name and address. Keep `Auto update` ticked if the group should take part in automatic updates, and click `OK`. Throne then creates the group, switches to its tab (since 1.4.0) and downloads it. See [Deep Links](@/advanced/deeplinks.md#addsub).

## Update a subscription {#update}

To update one group, use one of these:

- Right-click the group's tab and choose `Update subscription`.
- Select the tab and press `Ctrl+U`, or choose `Groups` → `Update subscription`.
- Click `Update Subscription` next to the group in `Groups` → `Manage Groups`.

`Groups` → `Update all subscriptions` updates every subscription group, including groups with `Skip automatic update`.

During an update, Throne:

- Downloads the list and compares it with the profiles in the group. Profiles that did not change keep their test results. Changed profiles are updated. New profiles are added, and profiles that are no longer in the list are deleted.
- Puts the profiles in the same order as the provider's list. If you sorted the group by a column, Throne sorts it again after the update (since 1.4.0). See [Sort and filter](@/guides/testing.md#sort-filter).
- Changes nothing if the response contains no profiles at all. The log then says "No profiles found in the subscription: *group* was left unchanged." This protects your group from an empty or blocked response. If the provider sent an announcement with the empty list, Throne shows it too.
- Gives up if the server sends no data for 10 seconds, or if the response is larger than 64 MB.

With `Settings` → `Basic Settings` → `Subscription` → `Clear servers before updating subscription` (off by default), Throne deletes the profiles of the group before each update, so their test results are lost.

In Simple mode, the `Subscription` tab is a section of the `Common` tab that shows only `Clear servers before updating subscription` and `Subscription auto update`; its other options are shown in Advanced mode only (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

### Change report {#change-report}

Every update writes a report to the `Logs` tab. It starts with `Change of` and the group name. Each line starts with `[+]` (added), `[~]` (updated), `[-]` (deleted) or `[=]` (kept). If nothing changed, the report says `Nothing`.

After you update one group by hand, Throne also shows the report in a window. To turn the window off, untick `Settings` → `Basic Settings` → `Subscription` → `Show the changes window after a manual subscription update`. Automatic updates and `Update all subscriptions` only write to the log.

Since 1.4.0, the report also lists problems: how many entries could not be imported or were imported with warnings, and why, for example an invalid port. When a manual update or an import from the clipboard or a file has problems, Throne shows them in a window, with the full list in its details.

### The profile you are using {#running-profile}

An update does not stop or delete the profile you are connected to. If the provider removed it, Throne keeps it and lists it in the report under "Still in use, so kept instead of deleted". To let Throne stop and delete such a profile instead, turn on `Settings` → `Basic Settings` → `Subscription` → `Allow stopping the active profile`.

Since 1.4.0, when an update stops and deletes the running profile this way, for example with `Clear servers before updating subscription`, Throne looks for its replacement in the group: a profile of the same type with the same name. If exactly one profile matches, Throne connects to it, and the log says "… was replaced by the update; connecting to the profile with the same name." If none or several match, the connection stays stopped: "… was removed by the update and stays stopped: no single profile with the same name replaces it." If you start a profile yourself in the meantime, Throne keeps yours.

If the provider changed the settings of the running profile, Throne saves the new settings, but the connection keeps the old ones. Start the profile again to use the new settings. A running [auto selector](@/guides/testing.md#auto-selector) restarts by itself when an update replaces profiles that it uses.

## Provider information {#provider-info}

Many providers send details about your plan with the subscription: the traffic you used and your total, the expiry date, a title, links to their website and support, an announcement, and how often to update. Throne reads them from the response headers `Subscription-UserInfo`, `Profile-Title`, `Profile-Web-Page-Url`, `Support-Url`, `Announce` and `profile-update-interval`, or from `#` comment lines with the same names at the top of the subscription.

Since 1.3.2, a card above the profile list shows these details for the current group:

- **Traffic:** a bar with the used and total traffic and the percentage, or `∞` when there is no total. The bar turns red at 90 % and when the plan has expired.
- **Expiry:** the time left, for example `12d left`, or `Expired`. It turns red when 3 days or less are left. Hover over it to see the date.
- **Update interval:** for example `Every 12h`, while automatic updates are on. Hover over it to see where the interval comes from.
- **`Portal` and `Support`:** open the provider's links in your browser.
- **Announcement:** the provider's message. Click it to read the whole text.

The card appears only when the provider sends at least one of these details. Hovering over the group's tab shows the same details, and `Groups` → `Manage Groups` shows the traffic and the expiry.

## Automatic updates {#auto-update}

Automatic updates are off by default. To turn them on:

1. Open `Settings` → `Basic Settings` → `Subscription`.
2. Next to `Subscription auto update`, tick `Enable`.
3. Set `Interval (minute, invalid if less than 30)`. The default is 30.
4. Click `OK`.

How automatic updates work:

- Each subscription group has its own schedule. Throne updates a group when its interval has passed since the group's last update. Groups with `Skip automatic update` are left out.
- By default every group uses the interval above. Since 1.3.2, a group can set its own `Update Interval` in its advanced options; see below.
- An interval of less than 30 minutes counts as off. The `Enable` switch above must be on for any automatic update, including groups with their own interval.
- Throne checks once a minute, and 10 seconds after it starts. An update that became due while Throne was closed runs soon after you open it.
- The `Runtime Stats` tab shows the time left until the next group update in `Next sub update`.
- Automatic updates never open the changes window. They write the same log lines as a manual update.

Since 1.3.2, you can also follow the interval that the provider suggests in the `profile-update-interval` header, in hours. Tick `Use the update interval sent by the server` on the `Subscription` tab (off by default). Groups whose provider sends no interval keep using the normal interval.

For one group, open the group options, click `Advanced`, and use the `Update` part:

| Setting | What it does |
| --- | --- |
| `Update Interval` | Minutes between automatic updates of this group, at least 30. `Keep Default` uses the interval from the `Subscription` tab. |
| `Server Interval` | `Use` or `Ignore` the interval that the provider sends. `Keep Default` follows `Use the update interval sent by the server`. When the provider sends none, `Update Interval` applies. |

## Group options {#group-options}

To change a group, right-click its tab and choose `Edit selected Group`, or choose `Groups` → `Edit current Group`.

| Setting | Default | What it does |
| --- | --- | --- |
| `Name` | – | The name on the tab. |
| `Type` | `Basic` | `Basic` or `Subscription`. You cannot change it after the group is created. |
| `Front Proxy` | `None` | A profile that every profile of this group connects through first. See [Front and landing proxy](#front-landing-proxy). |
| `Landing Proxy` | `None` | A profile that traffic leaves through after the group's profile. |
| `Routing Profile` | `Default (…)` | Since 1.4.0. The routing profile that the group's profiles connect with. `Default (…)` follows the `Routing` menu. See [Routing profile per group](@/guides/routing.md#group-routing). |
| `Auto Clear Unavailable Profiles` | off | After a URL test of this group's profiles, deletes the profiles that failed, without asking. |
| `URL` | – | The subscription address. Only for subscription groups. |
| `Skip automatic update` | off | Leaves this group out of automatic updates. Manual updates still work. |
| `Use the subscription's routing profile` | off | Since 1.4.0. Only for subscription groups. See [After each update](#after-update). |
| `Advanced` | – | Opens `Advanced Group Settings` (since 1.3.1). See [Automatic updates](#auto-update), [After each update](#after-update), [User-Agent and HWID](#user-agent-and-hwid), [TLS and HTTP version](#tls-http-version) and [Server address](#server-address). Since 1.4.0, basic groups have it too, with only the `Server Address` part. |

If the group has profiles, the window also has `Copy profile share links` and `Copy profile share links (Deep Links)`. They copy the links of all profiles in the group.

In Simple mode, the group editor hides `Front Proxy`, `Landing Proxy` and `Advanced` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). Their settings still apply.

## After each update {#after-update}

Since 1.3.1, a subscription group can clean itself up after each update. Open the group options, click `Advanced`, and use the `Update` part. All options are off by default.

| Setting | What it does |
| --- | --- |
| `Keep working profiles` | Profiles whose last test succeeded are kept when the subscription no longer lists them. |
| `Remove duplicate profiles` | Removes profiles that are exact copies of another profile. |
| `Remove insecure profiles` | Removes profiles whose traffic is not properly protected, the same as `Groups` → `Remove Insecure Configs`. |
| `Remove invalid profiles` | Removes profiles that the core rejects. Skipped while the core is unreachable. |
| `Run URL test` | Tests the group's profiles when the update is finished, after any test that is already running. |
| `Remove unavailable profiles` | Needs `Run URL test`. Removes the profiles that failed the test. |
| `Sort by latency` | Needs `Run URL test`. Sorts the group, fastest first. |

The change report lists every profile these options removed. They do not delete the profile you are connected to, unless `Allow stopping the active profile` is on.

Since 1.4.0, a group can also take the routing profile that the provider sends with the subscription, in the format of the Happ app. Tick `Use the subscription's routing profile` in the group options (not under `Advanced`). Every update then saves that routing profile as the group's own and replaces its rules. The routing profile belongs to the group and is deleted with it. DNS settings from the subscription are never used. See [From the subscription](@/guides/routing.md#subscription-routing).

## User-Agent and HWID {#user-agent-and-hwid}

When Throne downloads a subscription, it sends a `User-Agent` header that names the app. Some providers send a different list for each app, or refuse apps they do not know. You can change the value:

- For all groups: `Settings` → `Basic Settings` → `Subscription` → `User Agent`. If it is empty, Throne sends `Throne/` and its version, for example `Throne/1.3.1`.
- For one group (since 1.3.1): group options → `Advanced` → `Request` → `User Agent`. If it is empty, the group uses the global value.

Some providers also require a device ID (HWID). Throne sends it only if you turn it on:

- For all groups: tick `Enable sending HWID, device model, and OS version when updating subscription` on the `Subscription` tab. It is off by default. Hover over it to see the values that Throne would send.
- For one group: set `Send HWID` to `Keep Default` (follow the global setting), `On` or `Off`. The `HWID`, `OS`, `OS Version` and `Device Model` fields override the values for this group. You can edit them only while HWID sending is on for the group.

To send other values for all groups, fill in `Custom System Parameters (optional)` on the `Subscription` tab. Use this format, and leave out the values that you do not want to change:

```text
hwid=value,os=value,osVersion=value,model=value
```

Throne sends these values in the headers `x-hwid`, `x-device-os`, `x-ver-os` and `x-device-model`. For what they contain, see [Privacy](@/reference/privacy.md#hwid).

## TLS and HTTP version {#tls-http-version}

Some subscription servers answer Throne's normal connection with a CAPTCHA, a block page or an error. Since 1.3.2, you can change how Throne connects to them:

- For all groups: `Settings` → `Basic Settings` → `Subscription` → `TLS Version` (`Auto`, `TLS 1.2` or `TLS 1.3`) and `HTTP Version` (`Auto` or `HTTP/1.1`). Both are `Auto` by default. With `Auto`, Throne uses HTTP/2 when the server offers it.
- For one group: group options → `Advanced` → `Request` → `TLS Version` and `HTTP Version`. `Keep Default` follows the global setting.

`TLS 1.2` often gets through when the default handshake gets a CAPTCHA. `TLS 1.3` is greyed out if your system's TLS library does not support it.

## Front and landing proxy {#front-landing-proxy}

A group can send the traffic of all its profiles through two extra profiles:

**Your device → `Front Proxy` → a profile of this group → `Landing Proxy` → WARP (if enabled) → Internet**

- Use a `Front Proxy` when your device cannot reach the group's servers directly.
- Use a `Landing Proxy` when websites should see the address of the landing proxy instead of the group's servers.

You can choose any saved profile from any group; type in the field to search. Auto selectors are not offered, because they change servers on their own. For chains and the rules for mixing cores, see [Proxy Chains & Custom Configs](@/advanced/chains.md#front-landing).

## Server address {#server-address}

Since 1.4.0, a group can make all its profiles connect to another address than the one in their settings. This helps when the provider's servers are behind a CDN and you want to use a CDN address that works on your network, for example one that the [IP scanner](@/guides/testing.md#ip-scanner) found.

Open the group options, click `Advanced`, and choose under `Server Address`:

| Choice | What it does |
| --- | --- |
| `Profiles' own addresses` (default) | Each profile connects to its own server. |
| `Fixed address` | Every profile connects to the host name or IP address that you enter. Enter it without a port. |
| `IP list` | Every profile connects to the first address of an IP list. The window shows which address that is. Greyed out while you have no IP lists. |

The profiles keep their own ports. If a profile's server was a domain name, Throne still sends that name as the TLS server name (SNI) and as the `Host` of WebSocket, HTTPUpgrade, HTTP and XHTTP transports, unless the profile sets them itself. The `Address` column shows the address that each profile really uses. If the IP list is empty or was deleted, the profiles use their own addresses, and the log says so.

A profile can override its group: in the profile editor, click `Advanced Settings` and set `Server Address` to `Inherit from group` (default), `Own address` or `IP list`. Profiles without one fixed server address, such as chains, auto selectors, `Direct`, `Tailscale`, `Extra Core` and custom configs, do not have this setting and are not changed by the group.

When you change the server address of a group while one of its profiles runs, Throne restarts the profile.

## Supported formats {#formats}

A subscription can return:

- Share links such as `vless://`, `vmess://`, `trojan://`, `ss://`, `hysteria2://` or `tuic://`, one per line, as plain text or Base64-encoded.
- Clash or Mihomo YAML with a `proxies:` list.
- sing-box JSON: a full config or a list of outbounds. Each supported outbound becomes a profile. A single outbound object becomes one custom outbound profile.
- Xray JSON. The outbounds of a config become profiles. A list of complete Xray configs becomes one `Custom Xray Config` profile per config.
- SIP008 (Shadowsocks JSON), WireGuard and AmneziaWG `.conf` files, OpenVPN `.ovpn` files, AnyConnect XML profiles and AmneziaVPN `vpn://` links.

Whether a `vless://` link becomes a sing-box or an Xray profile depends on `Settings` → `Basic Settings` → `Core` → `Xray VLESS Preference`. Since 1.4.0, VLESS entries with REALITY in Clash YAML follow this setting too. Throne applies it when it imports, so update your subscriptions again after you change it. See [sing-box vs Xray](@/advanced/xray.md#vless-preference). The full list of formats is in [Protocols & Import Formats](@/reference/protocols.md#import-formats).

## Problems {#problems}

Open the `Logs` tab first. Each update writes lines that start with `>>>>>>>>` and `<<<<<<<<`, including the error if there is one.

### The provider refuses Throne or sends no profiles {#problem-user-agent}

Some providers only answer apps that they know. The log then shows an error such as status code 403, or "No profiles found in the subscription". Set the group's `User Agent` (group options → `Advanced`) to the value your provider expects. Values that users needed include `ClashMeta`, `sing-box` and `Happ/1.0`.

### The server returns a web page or a CAPTCHA {#problem-web-page}

Since 1.4.0, Throne says "The server returned a web page instead of a subscription" when the answer is a web page, for example a CAPTCHA or a bot check. Try `TLS 1.2` or `HTTP/1.1` in the group's advanced options (see [TLS and HTTP version](#tls-http-version)), or another `User Agent`.

When you add a subscription and its first download fails, Throne keeps the new group and says so (since 1.4.0). Change its settings and update it again; you do not need to add it again.

### The provider requires a device ID {#problem-hwid}

If the provider asks for a device ID or fingerprint, turn on HWID sending: set `Send HWID` to `On` for the group, or turn it on for all groups on the `Subscription` tab.

### "Request with proxy but no profile started." {#problem-no-profile}

Throne sends its own requests, for example subscription updates, through its local proxy port when `Settings` → `Basic Settings` → `Miscellaneous` → `Use proxy` is on. It also does this whenever `System Proxy` is ticked. If no profile is running, there is no proxy and the request fails with this message. Start a profile first, or turn off `Use proxy` and `System Proxy`. In Simple mode, `Use proxy` is in the `Network Settings` section of the `Common` tab.

### The subscription address is blocked in your country {#problem-blocked}

1. Start a profile that works.
2. Turn on `Settings` → `Basic Settings` → `Miscellaneous` → `Use proxy`.
3. Update the subscription.

`Use proxy` sends the request to the local proxy port. Up to 1.3.2, it did not work while `Disable Mixed Inbound` was on. Since 1.4.0, Throne then sends the request directly, and TUN mode carries it through the running profile. If TUN mode is off too, the update fails with "Request with proxy but the mixed inbound is disabled and Tun Mode is off."

### Happ and v2RayTun "crypt" links {#problem-crypt}

Links that start with `happ://crypt` or `v2raytun://crypt` are encrypted for those apps, and Throne cannot read them. Web pages such as `…/dl/happ-link/…` are not subscriptions either. Ask your provider for a normal subscription address that starts with `https://`.

### Profiles show up as `Custom Xray Config` {#problem-custom-config}

If the subscription returns a list of complete Xray configs, Throne imports each config as one profile of the type `Custom Xray Config` (called `Custom (Xray config)` in the profile editor). It removes the `inbounds` of each config and runs the rest as written. This is by design: such configs can contain load balancers and chains that only work as a whole. A sing-box config, in contrast, is split into one profile per outbound.

For more help, see [Troubleshooting](@/help/troubleshooting.md#subscriptions).

## On Android {#android}

Throne for Android has the same groups, options and update behavior.

- Open `Groups` from the side menu. Use `New group` and `Update all subscriptions` at the top. Each group shows its type, the number of profiles, the last update and, if the provider sends it, the quota ("Used … · … left · expires …"). Since 2.0.5, `Settings` → `Appearance` → `Compact group cards` shows a usage bar and the time left instead of the subscription link and these details; see [Profile list layout](@/android/_index.md#layout).
- The group editor has the same basic options, written in sentence case: `Front proxy`, `Landing proxy`, `Auto clear unavailable profiles`, `Skip automatic update` and `Advanced`. Since 2.0.3, `Advanced` also has `TLS version`, `HTTP version`, `Update interval (minutes)` and `Server interval`, and `Settings` → `Subscriptions` has `Use the update interval sent by the server`, `Subscription TLS version` and `Subscription HTTP version`. The app then also says when the server returns a web page, and shows problems of an import or update in a report window. Since 2.0.5, the editor also has `Routing profile` and, for subscription groups, `Use the subscription's routing profile`; see [Routing profile per group](@/guides/routing.md#group-routing). Throne for Android has no `Server Address` setting.
- Since 2.0.2, a card above the profile list shows the [provider information](#provider-info): the traffic, the time left, `Portal` and `Support`, and the announcement. Tap the announcement to read all of it. To hide the card for one group, turn off `Subscription info card` in the group editor.
- Since 2.0.2, a subscription group has a `Filter` category with two regular expressions. `Only names matching` keeps only the profiles whose name matches, for example `HK|SG|Japan`. `Remove names matching` removes the profiles whose name matches, for example `expire|traffic|website`. Case does not matter. The filters apply at every update, and the change report says how many profiles they filtered out. If they would leave no profile, the update stops and the group stays unchanged.
- Since 2.0.2, entries that the Android core cannot run are skipped. See [Android differences](@/reference/protocols.md#android-import).
- `URL` also accepts a `content://` address, so a subscription can come from a file that another app provides. For a plain `http://` address, the editor warns "Cleartext HTTP traffic is insecure".
- The global options are in `Settings` → `Subscriptions`. If `User agent` is empty, the app sends `Throne/Android/` and its version.
- With `Settings` → `Subscriptions` → `Use proxy` on, updates go through the running profile and fail with "Request with proxy but no profile started." while no profile runs. In `Proxy only` mode, updates also go through the running profile. Since 2.0.4, `Proxy only` mode alone no longer makes them fail: while no profile runs, they go out directly.
- Since 2.0.5, when an update deletes the running profile because `Settings` → `Subscriptions` → `Allow stopping the active profile` is on, Throne connects to its replacement, as on the desktop: the one profile of the group with the same name and type. See [The profile you are using](#running-profile).
- `Add profile` → `Import from clipboard` with an address asks the same "How to update?" question.
- The changes window appears only after a manual update of one group. Automatic updates only write to the log.

For Android-specific problems, see [Android Troubleshooting](@/android/troubleshooting.md#subscriptions).
