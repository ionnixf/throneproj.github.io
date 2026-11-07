+++
title = "Android Troubleshooting"
description = "Fix common problems in Throne for Android."
weight = 60
toc = true
+++

Find the message or symptom you see. Messages are quoted as the app shows them in English. For Throne desktop, see [Troubleshooting](@/help/troubleshooting.md).

## The VPN does not start or has no traffic {#connection}

When a start fails, Throne shows the reason at the bottom of the screen, with a `Logs` button.

| Message | Cause | What to do |
| --- | --- | --- |
| "Permission denied to create a VPN service" | You declined the VPN request, or another VPN app is set to Always-on VPN. | Connect again and allow the request. If none appears, turn off Always-on VPN for the other app in Android's VPN settings. |
| "VPN confirmation unavailable" | The device has no screen for the VPN request. | See [VPN permission](@/android/permissions.md#vpn-permission). |
| "Failed: …" with "configure tun interface" | Android refused to create the VPN interface. | Disconnect other VPN apps, make sure none is set to Always-on VPN, and restart the device. |
| "Failed: Profile … has a type this build cannot use: …" | The profile type exists only on desktop, such as Tailscale or Extra Core. | Use another profile. |
| "Could not connect: the network's DNS (…) did not answer. …" (or "the system DNS") | `Direct DNS` did not answer. By default (`localhost`), it is the DNS server of the current network. | Tap `DNS` in the message and set `Direct DNS` to a server that answers on this network, for example `tls://1.1.1.1`. See [DNS on Android](@/guides/dns.md#android). |
| "Could not download the rule-sets: …" | A rule uses a rule-set that was never downloaded, and the download failed. | Tap `Connect anyway`, then `Connect`. These rules match nothing until the download works. |
| "Could not download geoip.dat from …" (or `geosite.dat`) | An Xray config with `geoip:` or `geosite:` rules needs the file. | Tap `Geo assets`, connect with another profile and tap `Download now`, or choose another `Source`. |

For any other "Failed: …" message, tap `Logs` and read the last lines.

### Connected, but nothing loads {#no-traffic}

1. Tap the bar at the bottom of the screen. "HTTP: … ms" means that the profile works; "Failed: …" means that the profile or its server does not.
2. If it failed, use ⋮ → `Test` → `URL test` and select a profile that works. See [Testing & Auto Selector](@/guides/testing.md).
3. If `Settings` → `TUN / VPN` → `Apps VPN mode` is on, turn it off and test again. See [Per-app proxy](@/android/modes.md#per-app-proxy).
4. Set `Settings` → `Routing` → `Current routing profile` to `Default` and test again. If that helps, a rule in your routing profile is the cause. If Throne says that the group uses its own routing profile, set the group's `Routing profile` to `Default (…)` instead.
5. Try another `Remote DNS` in `Settings` → `DNS`, or in the routing profile if it uses its own DNS servers. See [DNS](@/guides/dns.md).
6. If you turned on `Tun IPv6` or `Tun routing` in `Settings` → `TUN / VPN`, turn them off. If some apps or websites fail while others work, set `MTU` there to `1500` and reconnect.
7. To check whether VPN mode is the cause, set `Settings` → `General` → `Service mode` to `Proxy only`, connect, and set an app's own proxy setting to SOCKS5 `127.0.0.1`, port `2080`. If that app works, check steps 3 and 6 again.

### No internet while Throne is off {#blocked-without-vpn}

- Android's Always-on VPN with "Block connections without VPN" blocks all traffic while Throne is not connected. Connect Throne, or turn the option off in Android's VPN settings. See [Always-on VPN](@/android/permissions.md#always-on).
- With `Settings` → `DNS` → `Enable FakeDNS`, some apps lose their connection when Throne stops. Restart those apps, or turn off `Enable FakeDNS`.

## Throne stops in the background {#killed-in-background}

Signs: the VPN disconnects while the screen is off, the connection stops working after you unlock the phone, or the notification disappears.

1. `Settings` → `General` → `Battery optimization` must show "Unrestricted". If it does not, tap it. See [Battery optimization](@/android/permissions.md#battery).
2. In `Settings` → `General` → `Background & auto-start`, allow Throne to start automatically and to run in the background. See [Manufacturer settings](@/android/permissions.md#oem).
3. Keep `Reset outbound connections when device wakes from sleep` and `Reset outbound connections when network changes` on (the defaults) in `Settings` → `General`, so that apps connect again after deep sleep or a lost network.
4. Turn on Always-on VPN, so that Android starts Throne again by itself. See [Always-on VPN](@/android/permissions.md#always-on).
5. On some devices, swiping Throne away from the recent apps stops it. Leave it there, or lock it there.

## Subscription problems {#subscriptions}

A manual update shows its errors at the bottom of the screen. Automatic updates write them only to `Logs`.

| Message | Cause | What to do |
| --- | --- | --- |
| "Request with proxy but no profile started." | `Settings` → `Subscriptions` → `Use proxy` is on, and Throne is not connected. | Connect first, or turn off `Use proxy`. |
| "Error transferring … - server replied: …" | The server refused the request, for example with 403 or 404. | Check the URL. Many providers accept only some apps: set the user agent your provider names, or turn on HWID sending. |
| "No profiles found in the subscription; it was left unchanged." | The answer has no profiles that Throne can read. | Often the same user agent or HWID problem. Also check the [import formats](@/reference/protocols.md#import-formats). |
| "The server returned a web page instead of a subscription. …" | Often a CAPTCHA or a bot check. | Try another `TLS version`, `HTTP version` or `User agent` in the group's `Advanced` settings. |
| "The name filters left out all … profiles of the subscription; it was left unchanged." | The group's name filters removed every profile. | Check `Only names matching` and `Remove names matching` under `Filter` in the group's settings. |
| "Insecure redirect", "Too many redirects" or "Response larger than 64 MB" | The server redirected `https://` to `http://` (Throne does not follow that), redirects in a loop, or sends more than 64 MB. | Check the URL with your provider. |

The user agent and HWID are in `Settings` → `Subscriptions`, and for one group in `Groups` → the group's `Edit` button → `Advanced`. See [User-Agent and HWID](@/guides/subscriptions.md#user-agent-and-hwid).

### The provider accepts updates only from your own connection {#direct-updates}

While Throne is connected, its own requests, such as subscription updates, follow your routing profile too. If your provider accepts updates only from your own internet connection, add a rule that sends the subscription's domain to `direct`:

1. Open `Routing` in the drawer, then the menu of the current routing profile → `Edit` → `Add rule`.
2. Set `Outbound` to `direct`, enter the host name of the subscription URL (for example `sub.example.com`) in `Domain suffix`, and save the rule and the routing profile. The rule must be above other rules that match the same domain.

If the provider wants the request to come from one of its servers, choose that server as the `Outbound` instead.

## Routing errors {#routing}

| Message | Cause | What to do |
| --- | --- | --- |
| "The routing profile is referencing outbounds that no longer exist, consider revising your settings" | A rule sends traffic to a deleted server profile, for example after a subscription update. | Edit the routing profile: rules with a deleted server show "Missing server (#…)". Choose another outbound. |
| "Unknown rule-set "…" in rule "…" of routing profile "…"" or "The rule-set list is not available. …" | A rule uses a rule-set name that Throne does not know, or the list of names was not downloaded. | In `Routing`, tap `Refresh repository list`, or use the URL of an `.srs` file. |
| "raw routing profiles are not supported on Android" | You imported a raw routing profile from Throne desktop. | Use a structured or remote routing profile. |

## WARP errors {#warp}

| Message | Cause | What to do |
| --- | --- | --- |
| "WARP is enabled but its config has not been generated. …" (when you connect: "Failed: Warp is enabled but …") | There is no WARP config for the selected mode. | In `Settings` → `Routing` → `WARP`, tap `Generate WARP config`. |
| "Failed to generate WARP config" | The registration at Cloudflare failed. Some networks block it. | Connect to a working profile, turn on `Settings` → `Subscriptions` → `Use proxy`, and generate again. |
| No message, but nothing loads while WARP is on | In `WireGuard` and `AmneziaWG` mode, WARP needs a profile that forwards UDP. | Set `Mode` to `MASQUE` and `HTTP version` to `HTTP/2`, which needs only TCP, and generate a config for it. |

See [Cloudflare WARP](@/advanced/warp.md).

## Update problems {#updates}

- "The download is signed with a different key than this build, …": your copy was not signed with the Throne release key. Create a backup (drawer → `Tools` → `Create backup`), uninstall Throne, install the APK from GitHub and restore the backup.
- "This update cannot be installed from the app (…). …": tap `Open in browser`, download the APK for your device and install it over the current app.
- "Could not check for updates": Throne could not reach GitHub. Connect to a working profile and try again.
- Android cannot install Throne for Android 2.0 or newer over 1.x. See [Upgrading from 1.x or NekoBox](@/android/installation.md#upgrading).

## A profile works on desktop but not on Android {#desktop-vs-android}

1. Tailscale and Extra Core profiles do not work on Android, and TLS spoof settings are ignored.
2. Throne for Android does not import subscription profiles that its core cannot run, for example with a transport that sing-box does not have, or XHTTP on a profile type other than VLESS. `Logs` shows why each one was skipped.
3. An Xray config with `geoip:` or `geosite:` rules needs `geoip.dat` and `geosite.dat`, which Throne downloads from the `Source` in `Settings` → `Core` → `Xray geo assets`. If the installed file lacks a category that the config needs, choose a source that has it, such as the one your desktop uses.
4. Compare `Settings` → `Presets` and, in `Settings` → `Core`, `Xray VLESS preference` and `Trust system certificates in Xray`. The VLESS preference is applied at import, so update the subscription after you change it. On Android, trusting system certificates does not include certificates that you installed yourself.
5. To get the same settings as on desktop, restore a desktop backup. See [Moving between desktop and Android](@/guides/backup.md#desktop-android).

## Logs {#logs}

Open `Logs` in the drawer. It shows the core log and, for Xray profiles, the Xray log in lines marked `xray:`.

`Share logs` and `Save logs…` create one text file with the versions, the device model, the core logs of this and the previous connection, and the app's system log.

`Hide sensitive data` in the menu is on by default; keep it on. It hides URL paths, credentials, keys, server addresses, host names, public IP addresses and Wi-Fi names in the file. `Hide destinations` also hides the domain names in connection and DNS lines. The hiding is best effort: read the file before you post it.

For more detail, set `Settings` → `Core` → `Log level` to `info` or `debug` and tap `Apply` to restart the app. For Xray profiles, also set `Xray log level` on the same screen: Xray lines appear only when both levels allow them. Reproduce the problem, share the logs, then set the levels back to `warn` and `warning`.

### Diagnostics {#diagnostics}

For freezes, high CPU use or a fast-draining battery, the developers may ask for a Diagnostics file. It records what the core does for 30 seconds and contains nothing from your configs.

1. Bring Throne into the state with the problem.
2. Open `Settings` → `Diagnostics` and turn on the options the developers asked for: `Lock contention`, `Execution trace (larger file)` or `Include Throne logs`. With logs included, keep `Hide sensitive data` on.
3. Tap `Start`. The recording goes on when you leave the screen, so you can reproduce the problem meanwhile.
4. Tap `Share` or `Save…` and attach the file `throne-profile-<date>-<time>.zip`.

## Reporting a problem {#report}

{% alert_warning() %}
Never post subscription URLs, share links, backups or configurations in public. Anyone who has them can use your subscription.
{% end %}

Report problems at [github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues) with what you did, what you expected and what happened, the file from `Share logs` with `Hide sensitive data` on, the profile type (for example VLESS with REALITY) and the service mode. See also [Reporting a Bug](@/help/bug_reports.md).
