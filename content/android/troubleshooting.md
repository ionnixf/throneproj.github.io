+++
title = "Android Troubleshooting"
description = "Fix common problems in Throne for Android: the VPN does not start or has no traffic, stops in the background, subscription and routing errors."
weight = 60
toc = true
+++

Find the message or the symptom you see, then follow the steps. Messages are quoted as the English app shows them. For problems with Throne desktop, see [Troubleshooting](@/help/troubleshooting.md).

## The VPN does not start or has no traffic {#connection}

When a start fails, Throne shows the reason in a bar at the bottom of the screen, with a `Logs` button. The message stays until you swipe it away, tap `Logs`, or start again.

### "Permission denied to create a VPN service" {#vpn-denied}

Android did not give Throne the VPN permission. Usually you declined the request. Android also refuses without asking when another VPN app is set to Always-on VPN.

1. Tap the connect button again and allow the request.
2. If no request appears, open Android's VPN settings and turn off Always-on VPN for the other app.
3. If Throne shows "VPN confirmation unavailable", your device has no screen for this request. See [VPN permission](@/android/permissions.md#vpn-permission).

### Android refuses to create the VPN {#vpn-failed}

Android gave the permission but then refused to create the VPN interface. The "Failed: …" message then usually contains "configure tun interface".

1. Disconnect other VPN apps, and check in Android's VPN settings that none of them is set to Always-on VPN.
2. Restart the device and connect again.

### Other messages when you connect {#start-errors}

| Message | Cause | What to do |
| --- | --- | --- |
| "Please select a profile" | No profile is selected. | Tap a profile, then connect. |
| "Failed: Profile … has a type this build cannot use: …" | The profile type exists only on desktop, for example Tailscale or Extra Core. | Use another profile. See [below](#desktop-vs-android). |
| "Could not connect: the network's DNS (…) did not answer. Set a working Direct DNS." or "Could not connect: the system DNS did not answer. Set a working Direct DNS." | Since 2.0.1. The `Direct DNS` did not answer while the connection started. With the default `Direct DNS` (`localhost`), Throne asks the DNS servers of the current network itself. See [DNS on Android](@/guides/dns.md#android). | Tap `DNS` in the message and set `Direct DNS` to a DNS server that answers on this network, for example `tls://1.1.1.1`. |
| "Could not download the rule-sets: …" | Since 2.0.2. A routing rule uses a rule-set that Throne has not downloaded before, and the download failed. | Tap `Connect anyway`, then `Connect`. Rules with these rule-sets match nothing until Throne has downloaded them. See [Routing on Android](@/guides/routing.md#android). |
| "Could not download geoip.dat from …: …" or "… geosite.dat …", followed by "Check the source in Settings › Core › Xray geo assets, or connect with another profile and use Download now there." | Since 2.0.2. The profile is an Xray config with `geoip:` or `geosite:` rules, and Throne could not download the file it needs. | Tap `Geo assets`. Connect with another profile, then tap `Download now`, or choose another `Source`. See [A profile works on desktop but not on Android](#desktop-vs-android). |
| A message about routing or WARP | A problem in the routing profile or in the WARP settings. | See [Routing errors](#routing) and [WARP errors](#warp). |
| Any other "Failed: …" message | The core reported an error. | Tap `Logs` and read the last lines. |

### Connected, but nothing loads {#no-traffic}

1. Tap the bar at the bottom of the screen. "Success: HTTP handshake took …ms" (since 2.0.3: "HTTP: … ms") means that the profile works. "Failed: …" means that the profile or its server does not work.
2. If it failed, tap ⋮ → `Test` → `URL test` (in 2.0.4 and older: ⋮ → `URL test`) and select a profile that works. See [Testing & Auto Selector](@/guides/testing.md).
3. Check per-app proxy: open `Settings` → `TUN / VPN` → `Apps VPN mode` to see the app list. Since 2.0.5, tapping it while it is on turns per-app proxy off; tap it again to open the list. With `Proxy`, only the selected apps use the VPN. With `Bypass`, the selected apps do not use it. If no app is selected, all apps use the VPN. See [Per-app proxy](@/android/modes.md#per-app-proxy).
4. Switch to the `Default` routing profile with `Settings` → `Routing` → `Current routing profile` (in 2.0.4 and older: ⋮ → `Routing profile`), and test again. If it works now, a rule in your own routing profile is the cause. If Throne says "The group "…" uses its own routing profile, so the running profile keeps it." (since 2.0.5), set `Routing profile` in the group's settings to `Default (…)` instead. See [Routing](@/guides/routing.md).
5. Check DNS: `Settings` → `DNS` → `Remote DNS` (default `https://8.8.8.8/dns-query`). Try another server. Since 2.0.4, a routing profile can use its own DNS servers (`Use custom DNS settings` in its `DNS` section). `Settings` → `DNS` then says that the current routing profile uses its own DNS servers; change them in the routing profile instead. See [DNS](@/guides/dns.md).
6. If you turned on `Tun IPv6` or `Tun routing` in `Settings` → `TUN / VPN`, turn them off and test again. Use `Tun IPv6` only when your server supports IPv6. If some apps or websites fail while others work, set `MTU` on the same screen to `1500` and reconnect.
7. To find out whether VPN mode causes the problem, set `Settings` → `General` → `Service mode` to `Proxy only` and connect. Then set an app that has its own proxy setting to the SOCKS5 proxy `127.0.0.1`, port `2080` (see `Settings` → `Inbound` → `Proxy port`). If that app works, the problem is in VPN mode: go back to `VPN` mode and check steps 3 and 6 again. In 2.0.1 and older, also try another `TUN implementation`. See [VPN & Proxy Modes](@/android/modes.md#tun-settings).

### No internet while Throne is off {#blocked-without-vpn}

Android's Always-on VPN is on together with "Block connections without VPN". Android then blocks all traffic while Throne is not connected. Connect Throne with a working profile, or turn this option off in Android's VPN settings. See [Always-on VPN](@/android/permissions.md#always-on).

### Some apps have no internet after you disconnect {#fakedns}

`Settings` → `DNS` → `Enable FakeDNS` gives apps fake addresses that work only through Throne. Throne warns about this: "Other apps may need a restart to reconnect to the network after the proxy stops". Restart those apps, or turn off `Enable FakeDNS`.

## Throne stops in the background {#killed-in-background}

Typical signs: the VPN disconnects while the screen is off, the notification disappears, the connection stops working after you unlock the phone, or the buttons of the notification or a widget do nothing.

1. Open `Settings` → `General` → `Battery optimization`. It must show "Unrestricted". If it does not, tap it and allow Throne to run without restrictions. See [Battery optimization](@/android/permissions.md#battery).
2. Open `Settings` → `General` → `Background & auto-start`. Allow Throne to start automatically and to run in the background. See [Manufacturer settings](@/android/permissions.md#oem).
3. Keep `Reset outbound connections when device wakes from sleep` on (the default). When the device wakes from deep sleep, Throne closes the old connections, and apps connect again. Keep `Reset outbound connections when network changes` on as well. Since 2.0.5, it also resets the connections when the network comes back after it was lost, for example after a time without signal.
4. Turn on Always-on VPN, so that Android starts Throne again by itself. See [Always-on VPN](@/android/permissions.md#always-on).
5. On some devices, swiping Throne away in the list of recent apps stops it. Leave it in the list, or lock it there if your device offers this.

## Subscription problems {#subscriptions}

When you update a subscription by hand, errors appear at the bottom of the screen as the group name followed by the message. Since 2.0.3, an update that finds no profiles opens a window "Update of …" with the reasons instead. Automatic updates write their errors only to `Logs`.

| Message | Cause | What to do |
| --- | --- | --- |
| "Request with proxy but no profile started." | `Use proxy` is on in `Settings` → `Subscriptions`, and Throne is not connected. In 2.0.3 and older, the `Proxy only` service mode has the same effect. | Connect first, or turn off `Use proxy`. In 2.0.3 and older, turning it off helps only in `VPN` mode. |
| "Error transferring … - server replied: …" | The server refused the request, for example with 403 or 404. | Check the URL. Many providers accept only some apps: set the user agent your provider names, or turn on HWID sending. |
| "No profiles found in the subscription; it was left unchanged." | The answer contains no profiles that Throne can read, for example a web page. | Often the same user agent or HWID problem. Also check the [import formats](@/reference/protocols.md#import-formats). |
| "The server returned a web page instead of a subscription. …" | Since 2.0.3. The server sent a web page, often a CAPTCHA or a bot check. | In the group's `Advanced` settings, try another `TLS version`, `HTTP version` or `User agent`. |
| "The name filters left out all … profiles of the subscription; it was left unchanged." | Since 2.0.2. The name filters of the group removed every profile. | Edit the group and check `Only names matching` and `Remove names matching` under `Filter`. |
| "Insecure redirect" | The server redirected an `https://` URL to `http://`. Throne does not follow such redirects. | Ask your provider for the correct `https://` URL. |
| "Too many redirects" | The server redirects in a loop. | Check the URL with your provider. |
| "Response larger than 64 MB" | The subscription is too large. | Ask your provider for a smaller list. |
| "Still in use, so kept instead of deleted" or "The running profile was kept." | The update, or a clean-up option after it, would remove the profile you are connected to, so Throne kept it. You see this in the change report or in `Logs`. | Nothing. To remove it anyway, turn on `Allow stopping the active profile` in `Settings` → `Subscriptions`. |

To change the user agent or HWID for one group, open `Groups` in the drawer, tap the `Edit` button of the group, and open `Advanced`. It has `User agent` and `Send HWID`, and since 2.0.3 also `TLS version` and `HTTP version`. The settings for all groups are in `Settings` → `Subscriptions`. See [User-Agent and HWID](@/guides/subscriptions.md#user-agent-and-hwid).

### The provider accepts updates only from your own connection {#direct-updates}

While Throne is connected, its own requests, such as subscription updates, also go through Throne and follow your routing profile. Some providers accept update requests only from your own internet connection. Add a rule that sends the domain of the subscription to `direct`:

1. Open `Routing` in the drawer. Open the menu of the current routing profile and choose `Edit`.
2. Choose `Add rule`.
3. Set `Outbound` to `direct`. In `Domain suffix`, enter the host name of the subscription URL, for example `sub.example.com`.
4. Save the rule and the routing profile. Place the rule above other rules that match the same domain.

If the provider wants the request to come from one of its servers, choose that server as the `Outbound` instead.

## Routing errors {#routing}

| Message | Cause | What to do |
| --- | --- | --- |
| "The routing profile is referencing outbounds that no longer exist, consider revising your settings" | A rule sends traffic to a server profile that was deleted, for example by a subscription update. | Open the routing profile (`Routing` → its menu → `Edit`). Rules with a deleted server show "Missing server (#…)". Choose another outbound. |
| "Unknown rule-set "…" in rule "…" of routing profile "…"" | A rule uses a rule-set name that Throne does not know. | In `Routing`, tap `Refresh repository list`, or use the URL of an `.srs` file. |
| "The rule-set list is not available. …" | The list of rule-set names was not downloaded. | In `Routing`, tap `Refresh repository list`. |
| "raw routing profiles are not supported on Android" | You imported a raw routing profile from Throne desktop. | Use a structured or remote routing profile. |

## WARP errors {#warp}

| Message | Cause | What to do |
| --- | --- | --- |
| "WARP is enabled but its config has not been generated. Generate it in Settings › Routing › WARP." | WARP is on, but there is no WARP config for the selected mode. | Open `Settings` → `Routing` → `WARP` and tap `Generate WARP config`. |
| "Failed: Warp is enabled but its config has not been generated. Please generate the Warp config first in Routing Settings." | The same problem, shown when you connect. | The same. |
| "Failed to generate WARP config" | The registration at Cloudflare failed. It is blocked in some networks. | Connect to a working profile, turn on `Use proxy` in `Settings` → `Subscriptions`, and generate again. |
| No message, but nothing loads while WARP is on | In `WireGuard` mode, and since 2.0.5 in `AmneziaWG` mode, WARP needs a profile that forwards UDP. | Set `Mode` to `MASQUE` and `HTTP version` to `HTTP/2`, which needs only TCP. Generate a config for the new mode. |

See [Cloudflare WARP](@/advanced/warp.md).

## Update problems {#updates}

- "The download is signed with a different key than this build, so Android cannot install it as an update. Back up, uninstall this build and install the release from GitHub." Your copy was not signed with the Throne release key. Create a backup (drawer → `Tools` → `Create backup`), uninstall Throne, install the APK from GitHub, and restore the backup.
- "This update cannot be installed from the app (…). Download it from the release page." Tap `Open in browser`, download the APK for your device and install it over the current app.
- "Could not check for updates": Throne could not reach GitHub. Connect to a working profile and try again.
- Android does not install 2.0.0 over an older Throne for Android. See [Upgrading from 1.x or NekoBox](@/android/installation.md#upgrading).

## A profile works on desktop but not on Android {#desktop-vs-android}

1. Check the profile type. Tailscale and Extra Core profiles do not work on Android. TLS spoof settings are ignored on Android.
2. If a profile of a subscription is missing on Android: since 2.0.2, Throne does not import profiles that the core cannot run on Android, for example a transport that sing-box does not have, or XHTTP on a profile type other than VLESS. `Logs` shows the reason for each skipped profile.
3. If the profile is an Xray config with `geoip:` or `geosite:` rules, it needs the files `geoip.dat` and `geosite.dat`. Since 2.0.2, Throne downloads them when such a config first starts, from the `Source` in `Settings` → `Core` → `Xray geo assets`. If Throne says that the installed file does not contain a category that the config needs, choose a source that has it, for example the one that your desktop uses.
4. Compare the settings that change how profiles connect: the presets in `Settings` → `Presets` (multiplex, TLS fragment, TLS tricks, uTLS) and `Settings` → `Core` → `Xray VLESS preference`. The VLESS preference is applied when profiles are imported, so update the subscription again after you change it. See [sing-box vs Xray](@/advanced/xray.md#vless-preference). Since 2.0.4, also compare `Settings` → `Core` → `Trust system certificates in Xray`. On Android it adds the system's root certificates, but not certificates that you installed yourself.
5. To get the same settings as on desktop, restore a desktop backup. See [Moving between desktop and Android](@/guides/backup.md#desktop-android).
6. If it still fails, report the problem with the logs of both apps.

## Logs {#logs}

Open `Logs` in the drawer. It shows the log of the core. Since 2.0.5, it also shows the log of Xray for Xray profiles, in lines marked `xray:`. The toolbar has `Update` (reloads the view), `Share logs` and `Clear logs`. The menu has `Save logs…`, `Hide sensitive data` and `Hide destinations`.

Since 2.0.3, the toolbar also has `Filter`. It shows only the lines that contain the text you type. `Match case` and `Regular expression` change how the text is compared. The menu also has `Font size`. When you scroll up, the view stops following new lines, and `Jump to latest` takes you back to the end.

`Share logs` and `Save logs…` create one text file with:

- a header with the app and core versions, the Android version, the device model, the ABI, the service mode and some settings that contain no secrets;
- the core log, and the core log of the previous connection;
- the last 2000 lines of the app's system log.

`Hide sensitive data` is on by default. Keep it on. In the exported file it replaces:

- the path and query of every URL, and user names and passwords in URLs (the host name stays);
- passwords, keys, tokens, UUIDs and similar values, since 2.0.5 also when such a value is a list or an object;
- server addresses, SNI and host names in the configuration;
- public IP addresses (private addresses stay);
- Wi-Fi names.

`Hide destinations` also hides the domain names in connection and DNS lines. You can turn it on only while `Hide sensitive data` is on, and Throne does not keep it after the app restarts. The hiding is best effort: read the file before you post it.

For more detail, set `Settings` → `Core` → `Log level` to `info` or `debug`, and tap `Apply` to restart the app. Since 2.0.5, for an Xray profile, also set `Xray log level` on the same screen to `info` or `debug` (default `warning`): Xray lines appear only when both levels allow them. Reproduce the problem, then share the logs. Set the levels back to `warn` and `warning` afterwards.

After a crash, Throne restarts and offers to share a log file with sensitive data hidden.

### Diagnostics {#diagnostics}

For freezes, high CPU use or a fast-draining battery, the developers may ask for a Diagnostics file (since 2.0.3). It records what the core does for 30 seconds.

1. Bring Throne into the state with the problem, for example connect and wait until it hangs.
2. Open `Settings` → `Diagnostics`.
3. Turn on the options the developers asked for: `Lock contention`, `Execution trace (larger file)` or `Include Throne logs`. With logs included, keep `Hide sensitive data` on.
4. Tap `Start`. The recording goes on when you leave the screen, so you can reproduce the problem meanwhile. `Stop` ends it early.
5. Tap `Share` or `Save…` and attach the file `throne-profile-<date>-<time>.zip`.

The recording contains no information about your configs. It lists the app and core versions, the Android version, the device model, the ABI and a few settings, such as the service mode and the type of the running profile. `Include Throne logs` adds the log file described above. The desktop has the same feature. See [Diagnostics](@/help/bug_reports.md#diagnostics).

## Reporting a problem {#report}

{% alert_warning() %}
Never post subscription URLs, share links, backups or configurations in public. Anyone who has them can use your subscription. A user who posted a configuration in an issue had to reset the subscription.
{% end %}

Report problems with the Android app at [github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues). Include:

- what you did, what you expected and what happened;
- the log file from `Share logs` or `Save logs…`, with `Hide sensitive data` on;
- the profile type (for example VLESS with REALITY) and the service mode.

The log file already contains the app version, the core version and the device model. For general advice, see [Reporting a Bug](@/help/bug_reports.md).
