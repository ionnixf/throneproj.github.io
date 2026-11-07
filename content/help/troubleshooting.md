+++
title = "Troubleshooting"
description = "Fixes for TUN mode, DNS, subscription, system proxy, performance and startup problems in Throne desktop."
weight = 20
toc = true
+++

Find your problem and go through the checks in order. Settings named here may be hidden in Simple mode: press `Ctrl+K` and type the name ([Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). For Android, see [Android Troubleshooting](@/android/troubleshooting.md). If nothing helps, [report a bug](@/help/bug_reports.md).

## First checks {#first-checks}

1. Update Throne with `Program` → `Check For Update`, or from the [downloads](@/downloads.md) page if that item is greyed out.
2. Read the `Logs` tab at the bottom of the main window.
3. Test the profile with `Url Test Selected` (`Ctrl+Shift+S`). If the test fails, fix the server or the profile first.
4. If a profile works with `System Proxy` but not with `Tun Mode`, the problem is in the TUN setup.

## TUN mode {#tun}

### TUN is on, but nothing loads {#tun-no-traffic}

1. Close other VPN apps. They can take over the routes.
2. **Windows:** allow `ThroneCore.exe` in Windows Firewall. If you denied it when Windows asked, Windows keeps blocking it without asking again ([#1433](https://github.com/throneproj/Throne/issues/1433)).
3. **Windows:** antivirus network protection, for example Avast or ESET, can block the TUN adapter without any message ([#1687](https://github.com/throneproj/Throne/issues/1687), [#1357](https://github.com/throneproj/Throne/issues/1357)). Add the Throne folder as an exception.
4. **Windows:** turn off `Strict Route` in `Tun Settings` for a test. Without it, DNS can leak; see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route).
5. **Linux:** turn off DNS over TLS for the whole system in systemd-resolved ([#1146](https://github.com/throneproj/Throne/issues/1146)), and keep `Auto Redirect` on in `Tun Settings`.

### TUN does not start {#tun-start}

- **No rights:** accept Throne's request for administrator or root rights ([FAQ](@/help/faq.md#tun-admin)). With `Disable Privilege request` on, you must give the rights yourself.
- **"Strict routing unavailable"** (Windows): turn off `Strict Route` in `Tun Settings` and start the profile again.
- **"Tun device misbehaving":** click `Reset` in the message, or `Restart Core` in `Tun Settings`, and start the profile again. If it still fails, restart the computer.
- **The start hangs** (Windows): a very large HOSTS file can make the DNS cache flush hang ([#1906](https://github.com/throneproj/Throne/issues/1906)). Keep it small.
- **"A background Throne (PID …) owns Tun on this computer; stop it or use thronectl."** See [Throne is running in the background](#background-throne).

### TUN breaks other things {#tun-side-effects}

A mobile hotspot, virtual machines, Docker or a work VPN can stop working while TUN mode is on. See [TUN Mode](@/guides/tun_mode.md#side-effects) and [Recipes](@/guides/recipes.md).

## DNS {#dns}

### Sites do not open, or the log shows DNS errors {#dns-errors}

1. In `Settings` → `Routing Settings` → `DNS`, `Remote DNS` must be reachable through your proxy, and `Direct DNS` must work without it. Try one of the preset servers.
2. If your network has no IPv6, do not use `ipv6_only` in `Default Domain Strategy` or `Resolve Domain Strategy` on the `Common` tab ([#1743](https://github.com/throneproj/Throne/issues/1743)). Leave them empty, or use `prefer_ipv4`.

### A leak test shows my ISP's DNS {#dns-leak}

This is usually expected; see the [FAQ](@/help/faq.md#dns-leak-test).

## Subscriptions {#subscriptions}

### The update fails {#sub-update-fails}

The log shows `Requesting subscription … error: …` with the reason.

- **"Request with proxy but no profile started."** With `Use proxy` (`Basic Settings` → `Miscellaneous`) or `System Proxy` on, Throne sends its own requests through its proxy. Start a profile first, or turn both off. If `Disable Mixed Inbound` is on, turn `Use proxy` off ([#1689](https://github.com/throneproj/Throne/issues/1689)).
- **The provider's site is blocked in your country:** start a working profile, turn on `Use proxy`, and update again.
- **The server answers `403`, or sends nothing useful:** many providers answer only apps they know. Set the `User Agent` your provider expects, in `Basic Settings` → `Subscription` for all groups, or in `Groups` → `Edit current Group` → `Advanced` for one. The default is `Throne/<version>`.
- **"Device fingerprint required"** or a similar message: the provider needs your [HWID](@/help/faq.md#hwid).

### The update finds no profiles {#sub-no-profiles}

The log says "No profiles found in the subscription". Check that the link is a subscription URL, not a web page or an app link: `happ://crypt` and `v2raytun://crypt` links are encrypted for those apps, and Throne cannot read them. Also try another `User Agent`.

### The group does not update {#sub-no-update}

- Profiles added with `Add profiles to this group` have no subscription URL. Paste the URL again with `Ctrl+V` and choose `Create new subscription group`.
- Automatic updates need `Basic Settings` → `Subscription` → `Subscription auto update`, which is off by default, and skip groups with `Skip automatic update`.

Profiles changed or disappeared after an update? See the [FAQ](@/help/faq.md#profile-after-update) and [Subscriptions & Groups](@/guides/subscriptions.md#problems).

## System proxy {#system-proxy}

### Some apps ignore the system proxy {#apps-ignore-proxy}

Terminals such as PowerShell, Windows Store apps, and Discord and Telegram calls usually ignore it ([#370](https://github.com/throneproj/Throne/issues/370), [#1631](https://github.com/throneproj/Throne/issues/1631)). Use `Tun Mode` for them; see [Which mode to use](@/guides/proxy_modes.md#which-mode).

### The system proxy is not set {#proxy-not-set}

1. Start a profile. Throne sets the system proxy only while a profile runs.
2. **"Cannot set system proxy when mixed inbound is disabled."** Turn off `Disable Mixed Inbound` in `Basic Settings` → `Common`.
3. **Linux:** Throne sets the system proxy only on GNOME and KDE. On other desktops, enter the `Mixed: …` address from the status bar in your system settings, or use TUN mode. Do not run Throne as root ([#864](https://github.com/throneproj/Throne/issues/864)).

### No internet after Throne was closed {#no-internet-after-exit}

If Throne was force-closed while `System Proxy` was on, the system proxy still points at Throne. See the [FAQ](@/help/faq.md#force-quit).

## Performance and crashes {#performance}

### High CPU or memory use {#high-cpu}

1. Keep fewer than about 10,000 profiles in total. After a URL test, remove dead ones with `Groups` → `Clean up` → `Remove Unavailable`.
2. Torrent clients with Multiplex on can use a lot of CPU ([#1090](https://github.com/throneproj/Throne/issues/1090)). Multiplex is off by default; if you turned it on, turn it off in the profile, or turn off `Default On` in `Settings` → `Preset Settings` → `Multiplex`.
3. Untick `Basic Settings` → `Style` → `Connection statistics` → `Enable`. The `Connections` tab then stays empty.
4. **Windows:** `Enable Tun Routing` with large rule-sets can cause very high CPU use. Turn it off in `Tun Settings`.

### Throne or the core crashes {#crashes}

- **"Core exits too frequently, stop automatic restart this profile."** The core keeps crashing, so Throne stopped restarting it with your profile.
- **"Throne did not shut down cleanly last time"** at the next start: Throne crashed. The log of that session is kept as `logs/crashed-<date>-<time>.log` in the config folder; on Windows, a crash report is also saved in the `crashes` folder.
- To rule out your configuration, exit Throne, rename the `config` folder and start Throne again; see [Command Line, Files & Logs](@/reference/files.md#reset).
- Report the crash with these files: [Reporting a Bug](@/help/bug_reports.md#logs).

## Startup and desktop problems {#startup}

### Throne does not start {#no-start}

- **"Incompatible processor. This Qt build requires the following features: sse4.2 popcnt"**: use `windowslegacy64.zip` on Windows, or a `-system-qt` package on Linux ([FAQ](@/help/faq.md#old-systems)).
- **Linux: `Could not load the Qt platform plugin "xcb"`**: with a Throne that uses the Qt of your system, install `libxcb-cursor0` (Debian, Ubuntu) or `xcb-util-cursor` (Fedora, RHEL) ([#1096](https://github.com/throneproj/Throne/issues/1096)). If the ZIP or a normal package shows it, use a `-system-qt` package, which also helps with other Qt errors.
- **macOS: "Throne is damaged and can't be opened":** Throne is not signed with an Apple certificate. Move `Throne.app` to `/Applications`, remove the quarantine flag in Terminal, and open Throne again:

  ```bash
  xattr -d com.apple.quarantine /Applications/Throne.app
  ```

On Linux, start Throne from a terminal to see the error: `/opt/Throne/Throne -appdata` for the packages, or `./Throne` in a ZIP copy. Do not use `sudo`; only the core needs root rights ([FAQ](@/help/faq.md#linux-suid)).

### No tray icon on GNOME {#gnome-tray}

Install and enable an AppIndicator extension. If the window is hidden, start Throne again to show it, or turn on `Basic Settings` → `Style` → `Disable tray` so that closing the window exits Throne.

### Throne does not reconnect after a restart {#reconnect}

Turn on `Program` → `Remember last profile`. At the next start, Throne starts the profile that was running when you quit, with `System Proxy` and `Tun Mode` as they were. `Program` → `Start with system` starts Throne when you log in; on Windows, it also gets administrator rights without asking if Throne runs as administrator ([FAQ](@/help/faq.md#tun-admin)).

### `throne://` links do nothing {#deeplinks}

On Windows and Linux, ZIP copies do not register `throne://` links by default. Turn on `Basic Settings` → `Common` → `Register throne:// links at startup`, or click `Install` next to it. More checks: [Deep Links](@/advanced/deeplinks.md#troubleshooting).

### Throne is running in the background {#background-throne}

A [headless Throne](@/headless/_index.md), for example a systemd service, can run on the same computer as the Throne window:

- **"Throne is running in the background (PID …). Stop the service or use thronectl."** and the window does not open: the headless Throne uses the same data folder. Stop it (for example `systemctl --user stop throne`) and start Throne again, or manage it with [thronectl](@/advanced/api.md).
- **"A background Throne (PID …) is running on this computer"**: a headless Throne with another data folder runs. The window works, but without TUN mode.
- **"A background Throne (PID …) owns Tun on this computer; stop it or use thronectl."** The window cannot use TUN mode while any headless Throne runs, even one without TUN. Stop the headless Throne.
- **"Update is unavailable while a background Throne (PID …) runs from this installation; stop it first."** Stop it, update, and start it again.
- **"Another Throne instance (PID …) is using this data directory"**: another Throne holds the data folder but does not answer. Quit it, or end that process, and start Throne again.
