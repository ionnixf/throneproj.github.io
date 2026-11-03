+++
title = "FAQ"
description = "Short answers to common questions about Throne: Nekoray, antivirus alerts, kill switch, headless servers, scripts, TUN rights, packages and old systems."
weight = 10
toc = true
+++

Short answers to common questions about Throne desktop. Each answer links to the page with the details. If something does not work, go to [Troubleshooting](@/help/troubleshooting.md).

## How is Throne different from Nekoray? {#nekoray}

Throne continues Nekoray. Nekoray is no longer developed, and its repository is archived. Throne started from the Nekoray code and has changed a lot since then:

- The core is ThroneCore, which is based on sing-box. Xray runs inside it only for Xray profiles, such as `VLESS (Xray)`.
- There are many new features, for example the Auto Selector, Cloudflare WARP, OpenVPN and OpenConnect profiles, backup and restore, and an Android app.
- Old features that were no longer useful were removed or simplified.

Throne cannot read Nekoray's configuration. To move your servers and settings, see [Coming from Nekoray / NekoBox](@/help/migrating.md).

## A menu item or setting from these docs is missing. Where is it? {#missing-option}

Since 1.4.0, a new installation of Throne starts in Simple mode, which hides advanced menu items and settings. Hidden settings keep their values and still apply.

- To see everything, open `Settings` → `Basic Settings` → `Style`, set `Interface` to `Advanced` and click `OK`.
- To find one setting, press `Ctrl+K` in the main window and type its name. The search also lists hidden settings, and choosing one switches Throne to Advanced mode and opens the setting.
- In Simple mode, some tabs are sections of other tabs. For example, the `Subscription` and `Security` options of `Basic Settings` are at the bottom of the `Common` tab.

If the item is still missing, your version may be older than the one these docs describe, or the item may exist only on other systems. See [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

## Is there a portable mode? {#portable}

Yes. The ZIP builds for Windows and Linux are portable: Throne keeps all its data in the `config` folder next to the program. To move Throne to another folder or computer, copy the whole `Throne` folder. A per-user install from the Windows installer (the default) also keeps `config` next to the program.

The `.deb` and `.rpm` packages, the Linux install script and the macOS app keep the data in your user profile instead. When the program folder is not writable, for example an all-users install in `Program Files`, Throne also uses your user profile and copies the existing data there. `Settings` → `Open Config Folder` opens the folder in use (Advanced mode only). See [Command Line, Files & Logs](@/reference/files.md#data-folder).

## Can I use Throne on Android and move my setup there? {#android}

Yes. [Throne for Android](@/android/_index.md) 2.0.0 (formerly NekoBox for Android) uses the same core and the same backup format as the desktop app. Create a backup on the desktop in `Settings` → `Basic Settings` → `Backup and Restore`, copy the `.thrbackup` file to the phone, and restore it in the Android app. This also works from Android to the desktop. See [Backup, Updates & Migration](@/guides/backup.md#desktop-android).

## Can Throne run on a server without a window? {#headless}

Yes, since 1.4.0. `Throne --headless` runs the same program without a window, tray icon or dialogs: on a server, a router, a Raspberry Pi or in a container. The `.deb` and `.rpm` packages include a systemd service for it, which they never turn on by themselves, and there is a Docker image. You control headless Throne with `thronectl` through the Throne API, or from a browser with the [web UI](@/headless/web.md) that `thronectl web` serves. The packages and the Docker image include `thronectl`. See [Headless Throne](@/headless/_index.md).

## Can I control Throne from a script or a Stream Deck? {#scripts}

Yes, since 1.4.0. Turn on the Throne API in `Settings` → `Integration Settings` → `Throne API` (Advanced mode only), and use `thronectl`. For example, `thronectl toggle` connects and disconnects with one key of a Stream Deck or a macro pad, and `thronectl connect best` starts the profile with the lowest latency. See [Throne API & thronectl](@/advanced/api.md).

Without the API, the [global hotkeys](@/reference/shortcuts.md#global-hotkeys) `Start/Stop Profile` and `Toggle Tun Mode` also work from any app.

## Why does my antivirus flag Throne? {#antivirus}

These are false positives. Some antivirus programs react to things that Throne does for normal features:

- The built-in updater downloads a new release and replaces the program files. Malware also replaces files, so this looks suspicious.
- On Windows, Throne writes registry keys: the `throne://` link registration under `HKEY_CURRENT_USER\Software\Classes`, the installer's record of the install folder, and, when Throne runs as administrator, Windows Error Reporting settings that save crash dumps to Throne's `crashes` folder. These keys are not a backdoor ([#1127](https://github.com/throneproj/Throne/issues/1127)).
- In TUN mode, the core creates a virtual network adapter and changes network settings.

Download Throne only from the official [downloads page](@/downloads.md), and add the Throne folder as an exception in your antivirus. Antivirus network protection can also block TUN mode without any message; see [Troubleshooting](@/help/troubleshooting.md#tun).

## Is my device ID (HWID) sent to anyone? {#hwid}

Only if you turn it on. `Settings` → `Basic Settings` → `Subscription` → `Enable sending HWID, device model, and OS version when updating subscription` is off by default. When it is on, Throne adds these values as HTTP headers to subscription requests, and to no other requests. Since 1.3.1, each group can override this setting with `Send HWID` in its `Advanced` subscription settings. Both settings are visible only in Advanced mode. Some providers require it. See [Privacy & Network Requests](@/reference/privacy.md#hwid).

## Is there a kill switch? {#kill-switch}

Yes, since 1.4.0, as an experimental feature. Turn on `Settings` → `Basic Settings` → `Security` → `Kill Switch`. In Simple mode, the `Security` options are at the bottom of the `Common` tab. While Throne is running, it blocks all traffic that does not go through Throne, also while no profile runs. The local network stays reachable unless `Private Range Bypass` is turned off in `Tun Settings`. The kill switch needs the same rights as TUN mode: administrator rights on Windows, and a core with root rights on Linux and macOS. See [Kill Switch](@/advanced/kill_switch.md).

Throne 1.3 and older have no kill switch. When the profile stops, or when the core stops unexpectedly, your apps connect directly, without the proxy.

**Android:** Android itself can block all traffic while the VPN is not connected. Set Throne as the always-on VPN and turn on the system option that blocks connections without VPN. See [Permissions & Background](@/android/permissions.md#always-on).

## Why does TUN mode need administrator or root rights? {#tun-admin}

TUN mode creates a virtual network adapter and sends the system's traffic into it. Operating systems allow this only for administrators. System proxy mode needs no special rights.

- **Windows:** Throne asks "Please run Throne as admin" and restarts itself with administrator rights. After that, it starts as administrator every time. To stop this, turn on `Settings` → `Basic Settings` → `Security` → `Always Start as Standard User` (Advanced mode only).
- **Linux:** only the core (`ThroneCore`) gets root rights, not the Throne window. See the [next question](#linux-suid).
- **macOS:** Throne opens Terminal and runs `sudo` to give the core root rights. This needs an administrator account.

See [TUN Mode](@/guides/tun_mode.md#privileges).

## Is the SUID bit really needed on Linux? {#linux-suid}

TUN mode needs a core with root rights, but the Throne window must run as your normal user. Do not start Throne itself with `sudo`.

When you turn on `Tun Mode`, Throne asks "Please give the core root privileges". If you agree, it uses `pkexec` to make `ThroneCore` owned by root and to set its SUID bit. `pkexec` (polkit) must be installed.

Instead of the SUID bit, you can give the core five capabilities. Throne treats the core as privileged only when it has all of them:

```bash
sudo setcap cap_net_admin,cap_net_raw,cap_net_bind_service,cap_sys_ptrace,cap_dac_read_search+ep /opt/Throne/ThroneCore
```

This path is for the `.deb` and `.rpm` packages and the Linux install script. For a ZIP copy, use the `ThroneCore` file in your Throne folder.

To stop Throne from asking, turn on `Settings` → `Basic Settings` → `Security` → `Disable Privilege request` (Advanced mode only). TUN mode and other features that need root then work only if you give the rights yourself. Some distribution packages cannot change the core; Throne then shows "This installation cannot grant the core privileges by itself." with instructions from the packager.

## Why does my internet stop working after Throne was force-closed? {#force-quit}

With `System Proxy` on, Throne points your system's proxy setting at its local port (`127.0.0.1:2080` by default) while a profile runs. It removes the setting when the profile stops and when you exit Throne. If Throne is killed, for example from Task Manager or by a power loss, the setting stays, and apps try to use a proxy that is no longer running.

To fix it:

1. Start Throne.
2. Tick `System Proxy` if it is not ticked, and start any profile.
3. Stop the profile, or exit Throne with `Program` → `Exit`.

Throne removes the proxy setting in step 3. You can also turn off the proxy in your system's network settings.

## Why does the system proxy turn off when the profile stops? {#system-proxy-off}

Since 1.1.3, Throne sets the system proxy only while a profile runs. When the profile stops, Throne removes the setting, so apps do not try to use a proxy that is not running. The `System Proxy` checkbox stays on, and Throne sets the system proxy again when you start a profile. See [System Proxy, TUN & LAN sharing](@/guides/proxy_modes.md#system-proxy).

## Why does a DNS leak test show my ISP's DNS server? {#dns-leak-test}

Often this is expected. For example, domains that match your `direct` rules are looked up with `Direct DNS`, which is your system's DNS by default and usually your ISP's. In system proxy mode, apps can also look up names themselves, and browsers may use their own DNS settings, so these lookups never reach Throne. [DNS leak tests](@/guides/dns.md#leak-tests) lists the expected results and explains what to change when a result is not expected.

## Why did my profile stop, or keep running, after a subscription update? {#profile-after-update}

A subscription update does not stop the running profile unless you allow it:

- If the new list no longer contains the running profile, or a clean-up option would remove it, Throne keeps the profile in the group, and it keeps running. The change report shows it under "Still in use, so kept instead of deleted", or says "The running profile was kept."
- If you turn on `Settings` → `Basic Settings` → `Subscription` → `Allow stopping the active profile` (Advanced mode only), Throne stops and deletes the running profile in this case.
- If the provider only changed some settings of the running profile, Throne updates the saved profile but does not restart the connection. Start the profile again to use the new settings.

An auto selector that uses the group rebuilds itself when the update replaces the profiles it runs on. See [Subscriptions & Groups](@/guides/subscriptions.md#running-profile).

## Why does my subscription show `Custom` profiles? {#custom-config-import}

Throne splits sing-box and Xray JSON into one profile per server. A few things become custom profiles on purpose. The `Type` column shows them as:

- `Custom Xray Config`: a subscription that is a list of complete Xray configs, each with its own `outbounds`, gives one such profile per config. These configs can contain balancers or chains that must run unchanged.
- `Custom Xray … Outbound`: an Xray outbound of a protocol other than VLESS.
- `Custom … Outbound`: a single sing-box outbound, that is, one JSON object with a `type` field.

Group outbounds such as `selector` and `urltest` are not imported. To pick the best server automatically, use an [Auto Selector](@/guides/testing.md#auto-selector).

Throne 1.1.0 and 1.1.1 imported a whole sing-box config that had `inbounds` as one custom config. Since 1.1.2, such a config is split into profiles again. See [Protocols & Import Formats](@/reference/protocols.md#import-formats).

## Where do the downloadable routing profiles come from? {#route-profiles-source}

From the [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) repository. `Routing` → `Download Profiles` gets the country profiles from it. The list of built-in rule-sets (`geoip-…` and `geosite-…`) is also kept there; the rule-set files come from projects such as MetaCubeX meta-rules-dat, Chocolate4U Iran-sing-box-rules and runetfreedom russia-v2ray-rules-dat. Throne downloads them through the mirror chosen in `Routing Settings` → `Common` → `Remote Rule-set Mirror` (Advanced mode only). See [Routing](@/guides/routing.md#download-profiles).

## What is the difference between the `.deb` packages? {#deb-variants}

- `Throne-<version>-debian-amd64.deb` includes its own copy of the Qt libraries. It is larger, but it does not depend on the Qt version of your system. Since 1.4.0, it also runs on older systems, from Debian 10 and Ubuntu 20.04 (glibc 2.28) on.
- `Throne-<version>-debian-amd64-system-qt.deb` does not include Qt. It uses the Qt 6 libraries of your distribution and installs them as dependencies. It needs glibc 2.34 or newer and Qt 6, as in Debian 12 and Ubuntu 22.04 or newer.

Start with the normal package. Use the `system-qt` package if the normal one does not start, for example on processors without SSE4.2 ([#845](https://github.com/throneproj/Throne/issues/845)). The same pair exists for arm64 (`debian-arm64.deb` and `debian-arm64-system-qt.deb`) and as `.rpm` packages for Fedora and RHEL (`fedora-amd64.rpm`, `fedora-amd64-system-qt.rpm` and the arm64 versions). The APT and RPM repositories of Throne have the `system-qt` packages. See [Installation](@/get_started/installation.md#linux).

## Which packages are official? {#third-party-packages}

The Throne developers publish Throne on the [GitHub releases page](https://github.com/throneproj/Throne/releases): the ZIP files, the Windows installer, and the `.deb` and `.rpm` packages (`.rpm` since 1.3.0). The Linux install script from the Throne repository installs the release ZIP.

They also run an APT and an RPM repository at `throneproj.github.io/Throne`, with the `system-qt` packages of every stable release, and the release workflow of Throne updates the `throne` package in the AUR and the Homebrew cask `throneproj/tap/throne`. See [Installation](@/get_started/installation.md#apt-repository).

The WinGet, Scoop and Nix/NixOS packages, and other AUR packages such as `throne-bin`, are made by community members, and the developers do not support them ([#1182](https://github.com/throneproj/Throne/issues/1182), [#1622](https://github.com/throneproj/Throne/issues/1622)). If a problem happens only with such a package, test an official build, and report packaging problems to the package maintainer. See [Package managers](@/get_started/installation.md#package-managers).

## Which build do I need for Windows 7, old macOS, an old Linux system or an old processor? {#old-systems}

- **Windows 7 SP1 and Windows 8:** use `windowslegacy64.zip` (64-bit) or `windows32.zip` (32-bit). The universal installer also works: on 32-bit Windows and on 64-bit Windows older than Windows 10 1809, it installs the legacy build by itself. The normal `windows64.zip` needs Windows 10 1809 or later. In Throne 1.3 and older, TUN mode on Windows 7 and 8 works only with the `gvisor` stack ([#1291](https://github.com/throneproj/Throne/issues/1291)); since 1.4.0, `Tun Settings` has no `Stack` setting.
- **Intel Macs on macOS 10.15 to 14.3:** use `macoslegacy-amd64.zip`. The normal macOS builds need macOS 14.4 or later. Older macOS versions are not supported. Throne 1.3 and older needed macOS 13 or later for the normal builds.
- **Older Linux distributions:** since 1.4.0, the ZIP files, the install script and the normal `.deb` and `.rpm` packages need glibc 2.28 or newer, so they run on Debian 10, Ubuntu 20.04, RHEL 8 and Raspberry Pi OS bullseye. The `-system-qt` packages, and so the APT and RPM repositories, need Debian 12, Ubuntu 22.04, RHEL 9 or newer. See [Linux](@/get_started/installation.md#linux).
- **Old processors on Linux:** if Throne does not start and prints `This Qt build requires the following features: sse4.2 popcnt`, install the `system-qt` package ([#845](https://github.com/throneproj/Throne/issues/845)).

The [downloads](@/downloads.md) table lists the minimum system for each file.
