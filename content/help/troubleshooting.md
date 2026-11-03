+++
title = "Troubleshooting"
description = "Step-by-step checks for TUN mode, DNS, subscriptions, the system proxy, performance and startup problems in Throne desktop."
weight = 20
toc = true
+++

Find your problem below and go through the checks in order. Each area ends with a link to the guide that explains the feature in detail. For Throne for Android, see [Android Troubleshooting](@/android/troubleshooting.md). If nothing helps, [report a bug](@/help/bug_reports.md).

Since 1.4.0, a new installation starts in Simple mode, which hides many of the settings that this page names. To find one, press `Ctrl+K` and type its name, or switch to Advanced mode. See [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

## First checks {#first-checks}

1. Update Throne with `Program` → `Check For Update` (`Tools` → `Check For Update` before 1.4.0). If this item is greyed out, download the new version from the [downloads](@/downloads.md) page. Many problems are fixed in newer versions.
2. Read the `Logs` tab at the bottom of the main window. Most errors are shown there.
3. Test the profile: select it and press `Ctrl+Shift+S` (`Url Test Selected`). If the test fails, check the server and the profile before you change TUN or system proxy settings.
4. Try the other mode. If a profile works with `System Proxy` but not with `Tun Mode`, the problem is in the TUN setup, not in the server.

## TUN mode {#tun}

### TUN is on, but nothing loads {#tun-no-traffic}

1. Turn `Tun Mode` off and test the same profile with `System Proxy`. If that also fails, fix the profile or the server first.
2. Close other VPN apps. Other VPNs and virtual network adapters can take over the routes.
3. Check whether a firewall or an antivirus blocks the TUN adapter; see the notes for your system below.

**Windows:**

1. Allow `ThroneCore` through Windows Firewall. Windows asks the first time you use TUN mode. If you denied it, Windows blocks the core and does not ask again ([#1433](https://github.com/throneproj/Throne/issues/1433)). Allow `ThroneCore.exe` in the firewall settings.
2. Antivirus programs with network protection, for example Avast or ESET, can block the TUN adapter without any message ([#1687](https://github.com/throneproj/Throne/issues/1687), [#1357](https://github.com/throneproj/Throne/issues/1357)). Add the Throne folder as an exception, or turn the network protection off for a test.
3. Turn off `Strict Route` in `Tun Settings` for a test. Without it, DNS leaks are possible; see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route).

**Linux:**

1. If you turned on DNS over TLS for the whole system in systemd-resolved, TUN mode stops working. Turn it off ([#1146](https://github.com/throneproj/Throne/issues/1146)).
2. Keep `Auto Redirect` on in `Tun Settings` (Advanced mode only). It is on by default and improves TUN routing and performance with nftables.

### TUN does not start {#tun-start}

- **No rights:** TUN mode needs administrator or root rights. Accept Throne's request, or see [Why does TUN mode need administrator or root rights?](@/help/faq.md#tun-admin). If `Disable Privilege request` (Advanced mode only) is on, Throne does not ask, and you must give the rights yourself.
- **"Strict routing unavailable"** (Windows): Windows could not turn on strict routing. Open `Tun Settings`, turn off `Strict Route`, and start the profile again.
- **"Tun device misbehaving":** the virtual adapter is in a bad state, for example after Throne was force-closed. Click `Reset` in the message, or click `Restart Core` in `Tun Settings`, and start the profile again. If it still fails, restart the computer.
- **The start hangs**, and Throne suggests restarting the software: on Windows, a very large HOSTS file can make the DNS cache flush hang ([#1906](https://github.com/throneproj/Throne/issues/1906)). Keep the HOSTS file small.
- **"A background Throne (PID …) owns Tun on this computer; stop it or use thronectl."** A headless Throne runs on this computer. See [Throne is running in the background](#background-throne).

### TUN breaks other things {#tun-side-effects}

A mobile hotspot, virtual machines, Docker or a work VPN can stop working while TUN mode is on. See [TUN Mode](@/guides/tun_mode.md#side-effects) and [Recipes](@/guides/recipes.md).

Details: [TUN Mode](@/guides/tun_mode.md#troubleshooting).

## DNS {#dns}

### Sites do not open, or the log shows DNS errors {#dns-errors}

1. Open `Settings` → `Routing Settings` → `DNS`. In Simple mode, these fields are in the `Simple DNS Settings` section of the `Common` tab. `Remote DNS` must be reachable through your proxy, and `Direct DNS` must work without it. Try one of the preset servers.
2. On the `Common` tab, do not use `ipv6_only` in `Default Domain Strategy` or `Resolve Domain Strategy` (Advanced mode only) if your network has no IPv6 ([#1743](https://github.com/throneproj/Throne/issues/1743)). Leave them empty, or use `prefer_ipv4`.
3. **Linux:** DNS over TLS for the whole system in systemd-resolved breaks TUN mode; see [TUN is on, but nothing loads](#tun-no-traffic).

### A leak test shows my ISP's DNS {#dns-leak}

- Domains that match your `direct` rules are looked up with `Direct DNS`, which is usually your ISP's. This is expected; see [DNS leak tests](@/guides/dns.md#leak-tests).
- In system proxy mode, apps can do their own DNS lookups, which never reach Throne. Use TUN mode.
- A WebRTC test that shows no IP address is not a leak. Only your real IP address is a leak ([#1267](https://github.com/throneproj/Throne/issues/1267)).
- **Windows:** see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route).

Details: [DNS](@/guides/dns.md).

## Subscriptions {#subscriptions}

### The update fails {#sub-update-fails}

The log shows `Requesting subscription … error: …` with the reason.

1. **"Request with proxy but no profile started."** Throne sends its own requests through its proxy when `Use proxy` is on (`Basic Settings` → `Miscellaneous`; in Simple mode, the `Network Settings` section of the `Common` tab), and also whenever `System Proxy` is on. Start a profile first, or turn both off. If you turned on `Disable Mixed Inbound`, turn `Use proxy` off ([#1689](https://github.com/throneproj/Throne/issues/1689)).
2. **The provider's site is blocked in your country:** start a working profile, turn on `Use proxy`, and update again.
3. **The server answers `403`, or sends nothing useful:** many providers answer only apps they know. Set the `User Agent` that your provider expects: for all groups in `Basic Settings` → `Subscription`, or, since 1.3.1, for one group in `Groups` → `Edit current Group` → `Advanced` (both Advanced mode only). By default, Throne sends `Throne/<version>`.
4. **"Device fingerprint required"** or a similar message: the provider needs your HWID. Turn on HWID sending; see the [FAQ](@/help/faq.md#hwid).

### The update finds no profiles {#sub-no-profiles}

The log says "No profiles found in the subscription", and the group stays unchanged. The provider's answer contains nothing that Throne can read.

1. Check that the link is a subscription URL, not a web page or an app link. Links that start with `happ://crypt` or `v2raytun://crypt` are encrypted for those apps, and Throne cannot read them. Ask your provider for a normal subscription URL.
2. Try another `User Agent`. Providers often send a different format, or nothing, to apps they do not know.

### The group does not update {#sub-no-update}

- Update a group by right-clicking its tab → `Update subscription`, or with `Groups` → `Update subscription` (`Ctrl+U`).
- Profiles imported with `Add profiles to this group` were added once, without a subscription URL. To get updates, paste the URL again with `Ctrl+V` and choose `Create new subscription group`.
- Automatic updates need `Basic Settings` → `Subscription` → `Subscription auto update` (in Simple mode, at the bottom of the `Common` tab), which is off by default. Groups with `Skip automatic update` are not updated automatically.

Profiles changed or disappeared after an update? See [Why did my profile stop, or keep running, after a subscription update?](@/help/faq.md#profile-after-update)

Details: [Subscriptions & Groups](@/guides/subscriptions.md#problems).

## System proxy {#system-proxy}

### Some apps ignore the system proxy {#apps-ignore-proxy}

Apps may follow the system proxy or ignore it. Terminals such as PowerShell, Windows Store apps, Discord and Telegram calls usually ignore it ([#370](https://github.com/throneproj/Throne/issues/370), [#1631](https://github.com/throneproj/Throne/issues/1631)). Routing rules apply only to traffic that reaches Throne, so use `Tun Mode` for these apps. See [Which mode to use](@/guides/proxy_modes.md#which-mode)

### The system proxy is not set {#proxy-not-set}

1. Start a profile. Throne sets the system proxy only while a profile runs.
2. Turn off `Disable Mixed Inbound` in `Basic Settings` → `Common`. While it is on, Throne shows "Cannot set system proxy when mixed inbound is disabled."
3. **Linux:** Throne can set the system proxy only on GNOME and KDE. On other desktops, enter the address from the status bar (`Mixed: …`) in your system settings, or use TUN mode. Run Throne as your normal user, not as root ([#864](https://github.com/throneproj/Throne/issues/864)).
4. **Windows:** if an app needs another proxy address format, change `Proxy Format` in `Basic Settings` → `Common` (Advanced mode only).

### No internet after Throne was closed {#no-internet-after-exit}

If Throne was force-closed while `System Proxy` was on, the system proxy still points at Throne. See [the FAQ](@/help/faq.md#force-quit).

Details: [System Proxy, TUN & LAN sharing](@/guides/proxy_modes.md#system-proxy).

## Performance and crashes {#performance}

### High CPU or memory use {#high-cpu}

1. Keep the number of profiles low, for example below 10,000 in total. Many thousands of profiles make Throne slow and use a lot of memory. After a URL test, remove dead profiles with `Groups` → `Clean up` → `Remove Unavailable`, or let each group clean up after updates in its `Advanced` settings (`Run URL test`, then `Remove unavailable profiles`; Advanced mode only).
2. Torrent clients open very many connections. With Multiplex on, this can use a lot of CPU ([#1090](https://github.com/throneproj/Throne/issues/1090)). Multiplex is off by default. If you turned it on, turn it off in the profile, or turn off `Default On` in `Settings` → `Preset Settings` → `Multiplex` (Advanced mode only).
3. Untick `Basic Settings` → `Style` → `Connection statistics` → `Enable` (Advanced mode only). Throne then stops looking up the program behind each connection, which saves CPU when there are many connections. The `Connections` tab stays empty while it is off.
4. **Windows:** `Enable Tun Routing` with large rule-sets can cause very high CPU use. Turn it off in `Tun Settings`.

### Throne or the core crashes {#crashes}

1. When the core stops, Throne restarts it together with your profile. If the core stops again within 10 seconds, Throne gives up and logs "Core exits too frequently, stop automatic restart this profile."
2. After Throne itself crashes, the next start shows "Throne did not shut down cleanly last time" in the log. The log of the crashed session is kept as `logs/crashed-<date>-<time>.log` in the config folder. On Windows, Throne also saves a crash report (a `.txt` and a `.dmp` file) in the `crashes` folder.
3. Test with a fresh configuration: exit Throne, rename the `config` folder (`Settings` → `Open Config Folder` shows it in Advanced mode), and start Throne. It creates a new, empty configuration. Add one profile and test again. If the crash stops, your old configuration causes it. To go back, exit Throne, delete the new `config` folder and rename the old one back. See [Command Line, Files & Logs](@/reference/files.md#reset).
4. Report the crash and attach these files: [Reporting a Bug](@/help/bug_reports.md#logs).

## Startup and desktop problems {#startup}

### Throne does not start on Linux {#linux-start}

Start Throne from a terminal to see the error. For the `.deb` and `.rpm` packages:

```bash
/opt/Throne/Throne -appdata
```

For a ZIP copy, run `./Throne` in its folder. Then check the message:

- `Could not load the Qt platform plugin "xcb"`, with a note that `libxcb-cursor0` is needed: install `libxcb-cursor0` (Debian, Ubuntu) or `xcb-util-cursor` (Fedora) ([#1096](https://github.com/throneproj/Throne/issues/1096)). The ZIP and the normal packages include this library, so you see this note only with a Throne that uses the Qt of your system.
- `This Qt build requires the following features: sse4.2 popcnt`: install the `system-qt` package instead ([FAQ](@/help/faq.md#old-systems)).
- Other Qt errors: try the `system-qt` package, which uses the Qt of your distribution.

Do not start Throne with `sudo`. Only the core needs root rights ([FAQ](@/help/faq.md#linux-suid)).

### No tray icon on GNOME {#gnome-tray}

GNOME does not show tray icons unless an AppIndicator extension is installed and enabled. Install one to see Throne's tray icon. If the Throne window is hidden, start Throne again: the running copy shows its window. You can also turn on `Basic Settings` → `Style` → `Disable tray`, so that closing the window exits Throne.

### macOS says Throne cannot be opened {#macos-open}

Throne is not signed with an Apple certificate, so macOS blocks the downloaded app.

1. Move `Throne.app` to `/Applications`.
2. Remove the quarantine flag in Terminal:

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

3. Open Throne again.

### Throne does not reconnect after a restart {#reconnect}

Turn on `Program` → `Remember last profile`. At the next start, Throne starts the last profile again, and turns `System Proxy` and `Tun Mode` back on if they were on when you quit. To start Throne together with the system, turn on `Program` → `Start with system`.

**Windows:** after Throne has run as administrator once, it asks for administrator rights at every start. `Start with system` uses a Task Scheduler task that starts Throne with these rights without asking. To start without administrator rights, turn on `Basic Settings` → `Security` → `Always Start as Standard User` (Advanced mode only).

### `throne://` links do nothing {#deeplinks}

- **Windows and Linux:** ZIP copies do not register `throne://` links by default (on Linux since 1.4.0). Turn on `Basic Settings` → `Common` → `Register throne:// links at startup`, or click `Install` next to it (Advanced mode only).
- More checks: [Deep Links](@/advanced/deeplinks.md#troubleshooting).

### Throne is running in the background {#background-throne}

Since 1.4.0, Throne can also run as [headless Throne](@/headless/_index.md), without a window, for example as a systemd service or a systemd user unit. The Throne window notices a headless Throne on the same computer:

- **Throne does not start and says "Throne is running in the background (PID …). Stop the service or use thronectl."** A headless Throne uses the same data folder, for example a systemd user unit that uses `~/.config/Throne`. Stop it, for example with `systemctl --user stop throne`, and start the Throne window again. Or leave it running and manage it with [thronectl](@/advanced/api.md).
- **At start, the log shows "A background Throne (PID …) is running on this computer", and a notice says "Throne is running in the background".** A headless Throne with another data folder runs on this computer. The Throne window works, but without TUN mode.
- **`Tun Mode` cannot be turned on: "A background Throne (PID …) owns Tun on this computer; stop it or use thronectl."** Only one Throne at a time can use TUN mode on a computer. While a headless Throne runs, the Throne window refuses TUN mode, even when the headless one does not use TUN itself. Stop the headless Throne to use TUN mode in the window.
- **`Check For Update` has no `Update` button and says "Update is unavailable while a background Throne (PID …) runs from this installation; stop it first."** The headless Throne runs from the same program files that the update would replace. Stop it, update, and start it again.

If Throne says "Another Throne instance (PID …) is using this data directory", another Throne holds the data folder but did not answer within 10 seconds, for example because it hangs. Quit it, or end the process with that PID, and start Throne again.
