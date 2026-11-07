+++
title = "FAQ"
description = "Short answers to common questions about Throne desktop."
weight = 10
toc = true
+++

If something does not work, see [Troubleshooting](@/help/troubleshooting.md).

## How is Throne different from Nekoray? {#nekoray}

Throne continues Nekoray, which is no longer developed. It runs on its own sing-box-based core, ThroneCore, which uses Xray only for Xray profiles, and adds features such as the Auto Selector, Cloudflare WARP, OpenVPN and OpenConnect profiles, backups and an Android app. Throne cannot read Nekoray's configuration; see [Coming from Nekoray / NekoBox](@/help/migrating.md).

## A menu item or setting from these docs is missing. Where is it? {#missing-option}

A new installation starts in Simple mode, which hides advanced items. Press `Ctrl+K` and type the name of the setting to find and open it, or switch to Advanced mode in `Settings` → `Basic Settings` → `Style` → `Interface`. If the item is still missing, your version may be older than the one these docs describe, or the item exists only on other systems. See [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

## Is there a portable mode? {#portable}

Yes. The Windows and Linux ZIP builds keep all data in the `config` folder next to the program, so you can copy the whole `Throne` folder to another place or computer. The packages, the Linux install script and the macOS app keep the data in your user profile. See [Command Line, Files & Logs](@/reference/files.md#data-folder).

## Can I use Throne on Android and move my setup there? {#android}

Yes. [Throne for Android](@/android/_index.md) uses the same core and backup format. Create a backup in `Settings` → `Basic Settings` → `Backup and Restore`, copy the `.thrbackup` file to the phone and restore it in the app; it also works the other way. See [Backup, Updates & Migration](@/guides/backup.md#desktop-android).

## Can Throne run on a server without a window? {#headless}

Yes. `Throne --headless` runs on a server, a router or in a container, with a systemd service in the Linux packages and a Docker image. You control it with `thronectl` or its [web UI](@/headless/web.md). See [Headless Throne](@/headless/_index.md).

## Can I control Throne from a script or a Stream Deck? {#scripts}

Yes. Turn on `Settings` → `Integration Settings` → `Throne API` (Advanced mode only) and use `thronectl`: for example, `thronectl toggle` on a Stream Deck key connects and disconnects. Without the API, the [global hotkeys](@/reference/shortcuts.md#global-hotkeys) `Start/Stop Profile` and `Toggle Tun Mode` work from any app. See [Throne API & thronectl](@/advanced/api.md).

## Why does my antivirus flag Throne? {#antivirus}

These are false positives caused by normal features: the updater replaces program files, Throne writes registry keys on Windows, and TUN mode changes network settings ([#1127](https://github.com/throneproj/Throne/issues/1127)). Download Throne only from the official [downloads page](@/downloads.md) and add the Throne folder as an exception. Antivirus network protection can also block TUN mode without a message; see [Troubleshooting](@/help/troubleshooting.md#tun).

## Is my device ID (HWID) sent to anyone? {#hwid}

Only if you turn it on. `Settings` → `Basic Settings` → `Subscription` → `Enable sending HWID, device model, and OS version when updating subscription` is off by default; when it is on, these values go only to subscription requests. Each group can override it with `Send HWID` in its `Advanced` subscription settings (both Advanced mode only). Some providers require it. See [Privacy & Network Requests](@/reference/privacy.md#hwid).

## Is there a kill switch? {#kill-switch}

Yes: `Settings` → `Basic Settings` → `Security` → `Kill Switch`. While Throne runs, it blocks all traffic that does not go through Throne, even when no profile runs, and it needs the same rights as TUN mode. See [Kill Switch](@/advanced/kill_switch.md). On Android, set Throne as the always-on VPN and turn on the system option that blocks connections without VPN; see [Permissions & Background](@/android/permissions.md#always-on).

## Why does TUN mode need administrator or root rights? {#tun-admin}

TUN mode creates a virtual network adapter that captures the traffic of the system, and only administrators may do that. System proxy mode needs no special rights. On Windows, Throne restarts as administrator and starts that way from then on, unless you turn on `Settings` → `Basic Settings` → `Security` → `Always Start as Standard User` (Advanced mode only). On Linux and macOS, only the core gets root rights, not the window. See [TUN Mode](@/guides/tun_mode.md#privileges).

## Is the SUID bit really needed on Linux? {#linux-suid}

The core needs root rights for TUN mode, while the Throne window runs as your normal user, so do not start Throne with `sudo`. By default, Throne uses `pkexec` (polkit) to make `ThroneCore` owned by root and set its SUID bit. Instead, you can give the core five capabilities with `setcap`. To stop Throne from asking for rights, turn on `Settings` → `Basic Settings` → `Security` → `Disable Privilege request` (Advanced mode only). See [TUN Mode](@/guides/tun_mode.md#privileges).

## Why does my internet stop working after Throne was force-closed? {#force-quit}

With `System Proxy` on, Throne points your system's proxy setting at its local port while a profile runs, and removes it when the profile stops or Throne exits. If Throne is killed, the setting stays and apps try to use a proxy that no longer runs. To fix it, start Throne, start any profile with `System Proxy` ticked, then stop it or exit with `Program` → `Exit`. You can also turn off the proxy in your system's network settings.

## Why does the system proxy turn off when the profile stops? {#system-proxy-off}

Throne sets the system proxy only while a profile runs, so apps never try to use a proxy that is not running. The `System Proxy` checkbox stays on, and Throne sets the proxy again when you start a profile. See [System Proxy, TUN & LAN sharing](@/guides/proxy_modes.md#system-proxy).

## Why does a DNS leak test show my ISP's DNS server? {#dns-leak-test}

Often this is expected. Domains that match your `direct` rules are looked up with `Direct DNS`, usually your ISP's, and in system proxy mode, apps and browsers can look up names without Throne. [DNS leak tests](@/guides/dns.md#leak-tests) lists the expected results and what to change.

## Why did my profile stop, or keep running, after a subscription update? {#profile-after-update}

An update stops the running profile only if you allow it. If the update would remove the running profile, Throne keeps it, unless `Settings` → `Basic Settings` → `Subscription` → `Allow stopping the active profile` is on (Advanced mode only). If the provider only changed its settings, Throne saves them without reconnecting; start the profile again to use them. See [Subscriptions & Groups](@/guides/subscriptions.md#running-profile).

## Why does my subscription show `Custom` profiles? {#custom-config-import}

Throne splits sing-box and Xray JSON into one profile per server, but keeps complete Xray configs (`Custom Xray Config`), Xray outbounds of protocols other than VLESS (`Custom Xray … Outbound`) and single sing-box outbounds (`Custom … Outbound`) whole. Group outbounds such as `selector` and `urltest` are not imported; use an [Auto Selector](@/guides/testing.md#auto-selector) instead. See [Protocols & Import Formats](@/reference/protocols.md#import-formats).

## Where do the downloadable routing profiles come from? {#route-profiles-source}

They come from the [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) repository, which also keeps the list of built-in `geoip-…` and `geosite-…` rule-sets. The rule-set files come from projects such as MetaCubeX meta-rules-dat, Chocolate4U Iran-sing-box-rules and runetfreedom russia-v2ray-rules-dat. See [Routing](@/guides/routing.md#download-profiles).

## What is the difference between the `.deb` packages? {#deb-variants}

The normal `.deb` includes its own Qt and runs on Debian 10, Ubuntu 20.04 or newer. The `-system-qt.deb` uses the Qt 6 of your distribution and needs Debian 12, Ubuntu 22.04 or newer. Start with the normal package, and use `system-qt` if it does not start, for example on processors without SSE4.2 ([#845](https://github.com/throneproj/Throne/issues/845)). The same pair exists for arm64 and as `.rpm`; see [Installation](@/get_started/installation.md#linux).

## Which packages are official? {#third-party-packages}

The official ones are the files on the [GitHub releases page](https://github.com/throneproj/Throne/releases), the Linux install script, the APT and RPM repositories, the `throne` package in the AUR and the Homebrew cask `throneproj/tap/throne`. The WinGet, Scoop and Nix packages and other AUR packages such as `throne-bin` are made by the community, and the developers do not support them ([#1182](https://github.com/throneproj/Throne/issues/1182), [#1622](https://github.com/throneproj/Throne/issues/1622)). If a problem happens only with such a package, report it to its maintainer. See [Package managers](@/get_started/installation.md#package-managers).

## Which build do I need for Windows 7, old macOS, an old Linux system or an old processor? {#old-systems}

- **Windows 7 SP1 and 8:** `windowslegacy64.zip` (64-bit), `windows32.zip` (32-bit), or the universal installer, which picks the legacy build by itself.
- **Intel Macs on macOS 10.15 to 14.3:** `macoslegacy-amd64.zip`. Older macOS versions are not supported.
- **Older Linux distributions** (Debian 10, Ubuntu 20.04, RHEL 8): the ZIP, the install script or the normal packages. The `-system-qt` packages and the repositories need Debian 12, Ubuntu 22.04, RHEL 9 or newer.
- **Processors without SSE4.2:** `windowslegacy64.zip` on Windows, a `-system-qt` package on Linux ([#845](https://github.com/throneproj/Throne/issues/845)).

The [downloads](@/downloads.md) table lists the minimum system for each file.
