+++
title = "Throne for Android"
description = "What Throne for Android is, how it relates to Throne desktop, a quick start, and the main differences between the two apps."
weight = 3
sort_by = "weight"
toc = true
+++

Throne for Android is the Android version of Throne. It was formerly called NekoBox for Android. Since version 2.0.0 it runs the same core as Throne desktop (ThroneCore), so groups, subscriptions, routing profiles, DNS, presets, the auto selector, WARP and backups work the same way in both apps.

The pages in this section cover what is specific to Android: installation, permissions, the VPN and proxy modes, widgets, Android TV and troubleshooting. If you are new to the app, follow the [quick start](#quick-start), then read [Permissions & Background](@/android/permissions.md) so that Android does not stop the VPN.

The features that both apps share are explained in these guides:

- [Subscriptions & Groups](@/guides/subscriptions.md)
- [Routing](@/guides/routing.md)
- [DNS](@/guides/dns.md)
- [Testing & Auto Selector](@/guides/testing.md)
- [Cloudflare WARP](@/advanced/warp.md)
- [Moving between desktop and Android](@/guides/backup.md#desktop-android)

The `Documentation` item in the app's side menu (the drawer) opens this website.

## Quick start {#quick-start}

1. Install the APK for your device. Most phones need `arm64-v8a`. See [Choose the right APK](@/android/installation.md#choose-apk).
2. Open Throne. On Android 13 and newer, allow notifications.
3. When Throne asks "Keep Throne running", tap `Allow` and confirm in the Android dialog. See [Permissions & Background](@/android/permissions.md).
4. Copy your subscription URL.
5. On the `Profiles` screen, tap `Add profile` in the toolbar (the page with a plus sign), then `Import from clipboard`. To scan a QR code instead, choose `Scan QR code`.
6. Throne detects the URL and asks "How to update?". Tap `Create new subscription group`. Throne adds a group named after the host name in the URL and downloads its profiles.
7. Tap the tab of the new group.
8. Tap ⋮ (`More options`) → `Test` → `URL test`. In 2.0.4 and older, `URL test` is directly in the ⋮ menu. A panel shows the progress and a summary of the results. Each profile shows its own result in the list.
9. When the test finishes, tap `Select fastest`, or tap a profile that works.
10. Tap the round connect button at the bottom of the screen. The first time, Android asks whether Throne may set up a VPN connection. Allow it.
11. To check the connection, tap the bar at the bottom of the screen ("Connected, tap to check connection"). It shows "Success: HTTP handshake took …ms" (since 2.0.3: "HTTP: … ms") when traffic passes through the profile. Since 2.0.2, the bar also shows the location where your traffic leaves the internet. Throne asks an IP lookup service for it through the connection. Since 2.0.3, `Settings` → `Testing` → `Look up exit country` turns this off, and `Lookup service` chooses the service.

If your provider gives you a `throne://addsub/…` link, open it on the phone instead of steps 4–6. Throne asks "Add this subscription?", adds the group, downloads its profiles and opens the `Groups` screen. Then open the `Profiles` screen and continue with step 7.

Throne does not appear in the Android share menu when you share a text link from another app. Copy the link and use `Import from clipboard` instead.

## Differences from desktop {#differences}

The two apps share the core and most settings. These things work differently:

| Area | Throne desktop | Throne for Android |
| --- | --- | --- |
| How traffic reaches Throne | `Tun Mode` and `System Proxy` checkboxes | `Settings` → `General` → `Service mode`: `VPN` (default) or `Proxy only`. See [VPN & Proxy Modes](@/android/modes.md). |
| Choosing apps | Routing rules by process name or path | Per-app proxy (`Apps VPN mode`) and routing rules by app |
| Backups | `.thrbackup` files, since 1.4.0 also on a WebDAV server | `.thrbackup` files with profiles, routing profiles and settings only, also on a WebDAV server. See [WebDAV backups](@/guides/backup.md#webdav). |
| Default TUN MTU | 1500 | 9000 |
| Default core log level | `info` | `warn` |
| A `throne://add/…` link opened from another app | Imports the profile at once | Asks "Import profile" first |
| Raw routing profiles | Supported | Not supported. Raw profiles from a desktop backup are kept read-only. |
| TLS spoof, Tailscale and Extra Core profiles, OTP Manager, OpenVPN and OpenConnect endpoints in routing profiles | Available | Not available |
| sing-box dashboard | `Tools` → `Open Web dashboard`. The sing-box API that serves it is off by default. | Drawer → `sing-box dashboard` (since 2.0.2). The sing-box API is on by default and listens only on the device. |
| Kill switch | [Kill Switch](@/advanced/kill_switch.md) | Not available. Android's "Block connections without VPN" comes closest. See [Always-on VPN](@/android/permissions.md#always-on). |
| Throne API, `thronectl` and headless mode | Available. See [Throne API & thronectl](@/advanced/api.md) and [Headless Throne](@/headless/_index.md). | Not available |

Share links such as `vless://…` that you open from another app also ask "Import profile" first. Profile links that you paste with `Import from clipboard` or scan as a QR code are imported without a question.

Android also has home-screen widgets, a Quick Settings tile and the system's Always-on VPN. See [Widgets, Tile & Automation](@/android/widgets.md) and [Always-on VPN](@/android/permissions.md#always-on). Routing rules that match the connected Wi-Fi network need location access on Android. See [Location for Wi-Fi rules](@/android/permissions.md#location).

The drawer also has two screens for watching the connection:

- `sing-box dashboard` (since 2.0.2) shows the live traffic, the connections and the log of the running core. It needs a running profile and Android System WebView. The dashboard files come with the app. To hide the item, turn off `Settings` → `Core` → `Enable sing-box API`.
- `Live graphs` (since 2.0.3) shows the proxy and direct speed of the last minutes, and the CPU use, memory and connections of the core. In 2.0.3, touch and hold the bar at the bottom of the screen to open it. Since 2.0.4, touching and holding the bar opens a menu with `Live graphs` and `Speed test current connection`. The speed test needs a running connection.

## Profile list layout {#layout}

The `Profiles` screen shows a card for each profile, with buttons to edit and share it. These options change how the lists look:

- In landscape, the `Double column` button in the toolbar shows smaller cards side by side, and `Single column` switches back. In portrait, the list always has one column. In 2.0.4 and older, choose the layout with ⋮ → `Appearance`, in both orientations.
- `Settings` → `Appearance` → `Compact profile rows` (since 2.0.5) shows each profile on one short line with only its name and latency.
- `Settings` → `Appearance` → `Compact group cards` (since 2.0.5) shortens the cards on the `Groups` screen. Subscription groups show a usage bar and the time left instead of the subscription link and details, and a refresh button instead of `Update subscription`.

The double-column cards and the compact rows have no edit and share buttons. Use ⋮ → `Edit` or `Share` on the row instead.

## Desktop menus on Android {#menus}

The shared guides show the desktop menus. On Android the same options are here:

| Throne desktop | Throne for Android |
| --- | --- |
| `Groups` → `Manage Groups` | Drawer → `Groups` |
| `Routing` → `Routing Settings` → `Route` tab | Drawer → `Routing` |
| `Routing Settings` → `Common` tab | `Settings` → `Routing` |
| `Routing Settings` → `DNS` tab | `Settings` → `DNS` |
| `Routing Settings` → `Warp` tab | `Settings` → `Routing` → `WARP` |
| `Settings` → `Tun Settings` | `Settings` → `TUN / VPN` |
| `Settings` → `Preset Settings` | `Settings` → `Presets` |
| `Settings` → `Basic Settings` → `Subscription` | `Settings` → `Subscriptions` |
| `Settings` → `Basic Settings` → `Backup and Restore` | Drawer → `Tools` |
| `Settings` → `Basic Settings` → `Diagnostics` | `Settings` → `Diagnostics` (since 2.0.3) |
| `Tools` → `Open Web dashboard` | Drawer → `sing-box dashboard` (since 2.0.2) |
