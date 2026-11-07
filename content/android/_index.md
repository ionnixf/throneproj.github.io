+++
title = "Throne for Android"
description = "A quick start for Throne for Android and how it differs from Throne desktop."
weight = 3
sort_by = "weight"
toc = true
+++

Throne for Android (formerly NekoBox for Android) runs the same core as Throne desktop, so groups, subscriptions, routing profiles, DNS, presets, the auto selector, WARP and backups work the same way in both apps. The guides explain them: [Subscriptions & Groups](@/guides/subscriptions.md), [Routing](@/guides/routing.md), [DNS](@/guides/dns.md), [Testing & Auto Selector](@/guides/testing.md), [Cloudflare WARP](@/advanced/warp.md) and [Moving between desktop and Android](@/guides/backup.md#desktop-android). This section covers what is specific to Android.

## Quick start {#quick-start}

1. Install the APK for your device. Most phones need `arm64-v8a`. See [Choose the right APK](@/android/installation.md#choose-apk).
2. Open Throne. Allow notifications, and tap `Allow` when Throne asks to keep running in the background. See [Permissions & Background](@/android/permissions.md).
3. Copy your subscription URL. On the `Profiles` screen, tap `Add profile` (the page with a plus sign) → `Import from clipboard`, or `Scan QR code` for a QR code.
4. Tap `Create new subscription group`. Throne adds the group and downloads its profiles.
5. Open the tab of the new group and tap ⋮ → `Test` → `URL test`. When the test finishes, tap `Select fastest`, or tap a profile that works.
6. Tap the round connect button at the bottom of the screen and allow the VPN request.
7. Tap the bar at the bottom of the screen to check the connection. "HTTP: … ms" means that traffic passes through the profile.

If your provider gives you a `throne://addsub/…` link, open it on the phone instead of steps 3 and 4.

Throne does not appear in the Android share menu for text links. Copy the link and use `Import from clipboard`.

## Differences from desktop {#differences}

| Area | Throne desktop | Throne for Android |
| --- | --- | --- |
| How traffic reaches Throne | `Tun Mode` and `System Proxy` | `Settings` → `General` → `Service mode`: `VPN` (default) or `Proxy only`. See [VPN & Proxy Modes](@/android/modes.md). |
| Choosing apps | Routing rules by process name or path | Per-app proxy (`Apps VPN mode`) and routing rules by app |
| Backups | `.thrbackup` files, also on WebDAV | The same, with only profiles, routing profiles and settings. See [WebDAV backups](@/guides/backup.md#webdav). |
| Default TUN MTU | 1500 | 9000 |
| Raw routing profiles | Supported | Not supported. Raw profiles from a desktop backup are kept read-only. |
| sing-box dashboard | `Tools` → `Open Web dashboard`. The sing-box API is off by default. | Drawer → `sing-box dashboard`. The API is on by default and listens only on the device. |
| Kill switch | [Kill Switch](@/advanced/kill_switch.md) | Android's "Block connections without VPN" comes closest. See [Always-on VPN](@/android/permissions.md#always-on). |

TLS spoof, Tailscale and Extra Core profiles, the OTP Manager, OpenVPN and OpenConnect endpoints in routing profiles, the Throne API, `thronectl` and headless mode are not available on Android.

Touch and hold the bar at the bottom of the screen for `Live graphs` (speed, CPU, memory and connections of the core) and `Speed test current connection`.

## Profile list layout {#layout}

- In landscape, the `Double column` button in the toolbar shows smaller cards side by side. In portrait, the list always has one column.
- `Settings` → `Appearance` → `Compact profile rows` shows each profile on one line with only its name and latency.
- `Settings` → `Appearance` → `Compact group cards` shortens the cards on the `Groups` screen. Subscription groups show a usage bar, the time left and a refresh button instead of the link and `Update subscription`.

Double-column cards and compact rows have no edit and share buttons. Use ⋮ → `Edit` or `Share` on the row.

## Desktop menus on Android {#menus}

The shared guides show the desktop menus. On Android, the same options are here:

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
| `Settings` → `Basic Settings` → `Diagnostics` | `Settings` → `Diagnostics` |
