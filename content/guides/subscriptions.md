+++
title = "Subscriptions & Groups"
description = "Add and update subscriptions, set group options and fix update problems."
weight = 10
toc = true
+++

A subscription is a link from your provider that returns a list of servers. Throne keeps each subscription in its own group, a tab above the profile list, and can update it for you. A `Basic` group holds profiles you add yourself; a `Subscription` group also has a `URL`. The type cannot be changed later.

{% alert_info() %}
An update makes a subscription group match the provider's list: it deletes profiles that are not in the list and undoes your changes to the others. Keep your own profiles in a Basic group.
{% end %}

`Front Proxy`, `Landing Proxy`, the group's `Advanced` options and most settings on the `Subscription` tab appear only in Advanced mode, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

## Add a subscription {#add-subscription}

1. Copy the subscription address. It starts with `https://` or `http://`.
2. In Throne, press `Ctrl+V` (`Program` → `Add profile from clipboard`).
3. Choose `Create new subscription group` and click `OK`.

Throne names the group after the host in the address and downloads it. `Add profiles to this group` instead adds the profiles once and does not keep the address, so they are never updated.

To name the group and set its options first, choose `Groups` → `Add new Group`, set `Type` to `Subscription`, paste the address into `URL` and click `OK`. Creating the group does not download it: right-click its tab and choose `Update subscription`.

A `throne://addsub/…` link from your provider fills in the name and address; click `OK` to add the group. See [Deep Links](@/advanced/deeplinks.md#addsub).

## Update a subscription {#update}

Select the group's tab and press `Ctrl+U`, or right-click the tab and choose `Update subscription`. `Groups` → `Update all subscriptions` updates every subscription group, including those with `Skip automatic update`.

An update adds new profiles, updates changed ones and deletes the ones that are no longer listed. Unchanged profiles keep their test results. The group takes the provider's order, and a [sorted group](@/guides/testing.md#sort-filter) is sorted again. If the response holds no profiles at all, the group stays unchanged.

- `Settings` → `Basic Settings` → `Subscription` → `Clear servers before updating subscription` deletes the group's profiles before each update, so their test results are lost.
- `Settings` → `Basic Settings` → `Core` → `Xray VLESS Preference` applies when profiles are imported, so update your subscriptions after you change it. See [sing-box vs Xray](@/advanced/xray.md#vless-preference).

For the formats a subscription can return, see [Protocols & Import Formats](@/reference/protocols.md#import-formats).

### Change report {#change-report}

Every update writes a report to the `Logs` tab: profiles added `[+]`, updated `[~]`, deleted `[-]` and kept `[=]`, and the entries that could not be imported or were imported with warnings, with the reason. After a manual update of one group, the report also opens in a window; untick `Show the changes window after a manual subscription update` on the `Subscription` tab to stop that.

### The profile you are using {#running-profile}

An update never deletes the profile you are connected to, even when the provider removed it. To let Throne stop and delete it, turn on `Allow stopping the active profile` on the `Subscription` tab. Throne then connects to the profile of the group with the same name and type, if there is exactly one.

If the provider changed the running profile, the connection keeps the old settings until you start the profile again. A running [auto selector](@/guides/testing.md#auto-selector) restarts by itself when an update replaces its profiles.

## Automatic updates {#auto-update}

Automatic updates are off by default. To turn them on:

1. Open `Settings` → `Basic Settings` → `Subscription`.
2. Next to `Subscription auto update`, tick `Enable`.
3. Set the interval in minutes, at least 30 (the default).
4. Click `OK`.

Each group is updated when the interval has passed since its last update, and soon after Throne starts if an update came due while it was closed. Groups with `Skip automatic update` are left out.

- `Use the update interval sent by the server` follows the interval that your provider suggests, if it sends one.
- One group can have its own `Update Interval` and `Server Interval` in its options → `Advanced` → `Update`. `Keep Default` follows the `Subscription` tab, and `Subscription auto update` must still be enabled.

## Provider information {#provider-info}

Many providers send details about your plan with the subscription. A card above the profile list then shows them for the current group:

- **Traffic**: used and total traffic. The bar turns red at 90 % and when the plan has expired.
- **Expiry**: the time left, such as `12d left`, red with 3 days or less. Hover over it to see the date.
- **Update interval**, while automatic updates are on.
- **`Portal` and `Support`**: the provider's links.
- **Announcement**: the provider's message. Click it to read all of it.

Hovering over the group's tab shows the same details.

Throne reads these details from the response headers `Subscription-UserInfo`, `Profile-Title`, `Profile-Web-Page-Url`, `Support-Url`, `Announce` and `profile-update-interval`, or from `#` comment lines with the same names at the top of the subscription.

## Group options {#group-options}

Right-click a group's tab and choose `Edit selected Group`.

| Setting | What it does |
| --- | --- |
| `Front Proxy`, `Landing Proxy` | Extra hops for every profile of the group. See [Front and landing proxy](#front-landing-proxy). |
| `Routing Profile` | The routing profile that the group's profiles connect with. `Default (…)` follows the `Routing` menu. See [Routing profile per group](@/guides/routing.md#group-routing). |
| `Auto Clear Unavailable Profiles` | Deletes the profiles that fail a URL test of the group, without asking. |
| `Skip automatic update` | Leaves the group out of automatic updates. |
| `Use the subscription's routing profile` | Uses the routing profile that the provider sends with the subscription. See [From the subscription](@/guides/routing.md#subscription-routing). |
| `Advanced` | The [update interval](#auto-update), [cleanup after each update](#after-update), [User-Agent and HWID](#user-agent-and-hwid), [TLS and HTTP version](#problem-web-page) and [server address](#server-address). |

`Copy profile share links` copies the links of all profiles in the group.

## After each update {#after-update}

A subscription group can clean itself up after each update. Open its options → `Advanced` → `Update`. All options are off by default.

| Setting | What it does |
| --- | --- |
| `Keep working profiles` | Keeps profiles whose last test succeeded when the subscription no longer lists them. |
| `Remove duplicate profiles`, `Remove insecure profiles`, `Remove invalid profiles` | The same as the [Clean up](@/guides/testing.md#clean-up) commands. |
| `Run URL test` | Tests the group when the update is finished. |
| `Remove unavailable profiles` | Needs `Run URL test`. Removes the profiles that failed. |
| `Sort by latency` | Needs `Run URL test`. Puts the fastest profiles first. |

The [change report](#change-report) lists the removed profiles. The profile you are connected to is removed only when `Allow stopping the active profile` is on.

## User-Agent and HWID {#user-agent-and-hwid}

Some providers send a different list to each app, or refuse apps they do not know. Set the `User Agent` that your provider expects:

- For all groups: `Settings` → `Basic Settings` → `Subscription` → `User Agent`. Empty sends `Throne/` and the version.
- For one group: group options → `Advanced` → `Request` → `User Agent`. Empty uses the global value.

Some providers also require a device ID (HWID). Throne sends it only if you turn it on:

- For all groups: tick `Enable sending HWID, device model, and OS version when updating subscription` on the `Subscription` tab.
- For one group: set `Send HWID` to `On` under `Request`. The `HWID`, `OS`, `OS Version` and `Device Model` fields replace the values for this group.

To replace the values for all groups, fill in `Custom System Parameters (optional)` on the `Subscription` tab and leave out the ones you keep:

```text
hwid=value,os=value,osVersion=value,model=value
```

For what Throne sends, see [Privacy](@/reference/privacy.md#hwid).

## Front and landing proxy {#front-landing-proxy}

A group can send the traffic of all its profiles through two extra profiles:

**Your device → `Front Proxy` → a profile of this group → `Landing Proxy` → WARP (if enabled) → Internet**

Use a `Front Proxy` when your device cannot reach the group's servers directly, and a `Landing Proxy` when websites should see its address instead of the group's servers. Any saved profile except an auto selector can be chosen; type in the field to search. For chains and mixing cores, see [Proxy Chains & Custom Configs](@/advanced/chains.md#front-landing).

## Server address {#server-address}

A group can make all its profiles connect to another address, for example a CDN address that the [IP scanner](@/guides/testing.md#ip-scanner) found working on your network. Open the group options → `Advanced` → `Server Address` and choose:

- `Profiles' own addresses`: no change (default).
- `Fixed address`: a host name or IP address, without a port.
- `IP list`: the first address of an IP list.

The profiles keep their ports and still send their original domain as the TLS server name (SNI) and `Host`. The `Address` column shows the address that each profile really uses. A profile can override its group in its editor: `Advanced Settings` → `Server Address`.

## Problems {#problems}

Open the `Logs` tab first: each update writes its error there. See also [Troubleshooting](@/help/troubleshooting.md#subscriptions).

### The provider refuses Throne or sends no profiles {#problem-user-agent}

Some providers only answer apps that they know. The log then shows an error such as status code 403, or "No profiles found in the subscription". Set the group's `User Agent` to the value your provider expects; values that users needed include `ClashMeta`, `sing-box` and `Happ/1.0`. If the provider asks for a device ID, set `Send HWID` to `On`. See [User-Agent and HWID](#user-agent-and-hwid).

### The server returns a web page or a CAPTCHA {#problem-web-page}

Throne then says "The server returned a web page instead of a subscription". Change how it connects to the server: `TLS Version` and `HTTP Version` on the `Subscription` tab for all groups, or under `Request` in the group's `Advanced` options. `TLS 1.2` often gets through when the default handshake gets a CAPTCHA; `HTTP/1.1` and another `User Agent` are worth a try too.

If the first download of a new group fails, the group is kept. Fix its settings and update it again.

### "Request with proxy but no profile started." {#problem-no-profile}

While `Settings` → `Basic Settings` → `Miscellaneous` → `Use proxy` or `System Proxy` is on, Throne sends its own requests, such as subscription updates, through the running profile. With no profile running, they fail. Start a profile first, or turn off `Use proxy` and `System Proxy`.

### The subscription address is blocked in your country {#problem-blocked}

1. Start a profile that works.
2. Turn on `Settings` → `Basic Settings` → `Miscellaneous` → `Use proxy` (in Simple mode: `Common` → `Network Settings`).
3. Update the subscription.

With `Disable Mixed Inbound` on, this needs `Tun Mode`; otherwise the update fails with "Request with proxy but the mixed inbound is disabled and Tun Mode is off."

### Happ and v2RayTun "crypt" links {#problem-crypt}

Links that start with `happ://crypt` or `v2raytun://crypt` are encrypted for those apps, and Throne cannot read them. Web pages such as `…/dl/happ-link/…` are not subscriptions either. Ask your provider for a normal subscription address that starts with `https://`.

### Profiles show up as `Custom Xray Config` {#problem-custom-config}

A subscription that returns complete Xray configs gives one `Custom Xray Config` profile per config, which runs as written, without its `inbounds`. This is by design: such configs can contain load balancers and chains that only work as a whole.

## On Android {#android}

Throne for Android has the same groups, options and updates. Open `Groups` from the drawer. The differences:

- **Name filters.** The group editor's `Filter` options keep only the profiles whose name matches `Only names matching`, for example `HK|SG|Japan`, and remove those that match `Remove names matching`, for example `expire|traffic|website`. Both are regular expressions, case does not matter, and they apply at every update. If they would leave no profile, the group stays unchanged.
- **Info card.** Turn off `Subscription info card` in the group editor to hide the [provider information](#provider-info) of that group.
- **Server address.** Not available on Android.
- **Files.** `URL` also accepts a `content://` address, so a subscription can come from a file that another app provides.
- **Settings for all groups** are in `Settings` → `Subscriptions`. An empty `User agent` sends `Throne/Android/` and the version.
- **`Proxy only` mode** sends updates through the running profile, or directly while no profile runs.
- **Unsupported entries** are skipped, see [Android differences](@/reference/protocols.md#android-import).

For Android-specific problems, see [Android Troubleshooting](@/android/troubleshooting.md#subscriptions).
