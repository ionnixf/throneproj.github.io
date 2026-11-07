+++
title = "Get Started"
description = "What Throne is, which systems it runs on, and where to begin."
weight = 1
sort_by = "weight"
aliases = ["/introduction/"]
+++

## What is Throne {#what-is-throne}

Throne (formerly Nekoray) is a free, open-source (GPL-3.0) proxy client for Windows, Linux and macOS. Its core, ThroneCore, is based on sing-box and also runs Xray for the profiles that need it. You add servers, choose which traffic goes through them, and connect in system proxy or TUN mode.

- [Throne for Android](@/android/_index.md) (formerly NekoBox for Android) uses the same core, settings and [backup format](@/guides/backup.md), so you can move your setup between computer and phone.
- [Headless Throne](@/headless/_index.md) runs without a window on a server, a router or in a container, controlled with `thronectl`.
- If you used Nekoray or NekoBox before, read [Coming from Nekoray / NekoBox](@/help/migrating.md).

## Platforms {#platforms}

| System | Requirements | Package types |
|---|---|---|
| Windows | Windows 10 1809 or newer (x64, ARM64). Windows 7 SP1 or newer with the legacy builds (x64, 32-bit x86). | Installer, portable ZIP |
| Linux | x64 or ARM64, glibc 2.28 or newer, for example Debian 10, Ubuntu 20.04 or RHEL 8. The `-system-qt` packages need glibc 2.34 or newer. | Portable ZIP, `.deb`, `.rpm`, install script, [APT and RPM repositories](@/get_started/installation.md#apt-repository), [AUR](@/get_started/installation.md#aur) |
| macOS | macOS 14.4 or newer (Apple Silicon and Intel). macOS 10.15 or newer with the legacy build (Intel). | ZIP, [Homebrew](@/get_started/installation.md#homebrew) |
| Android | Android 7.0 or newer | APK |

Get the files from [Downloads](@/downloads.md).

## Highlights {#highlights}

- Many [protocols](@/reference/protocols.md), including VLESS with REALITY and XHTTP, Shadowsocks, Trojan, Hysteria, TUIC, WireGuard, AmneziaWG, NaïveProxy, OpenVPN, OpenConnect and [DNS tunnels](@/advanced/dns_tunnels.md).
- [Subscriptions](@/guides/subscriptions.md) that update on a schedule.
- [System proxy, TUN mode and LAN sharing](@/guides/proxy_modes.md).
- [Routing](@/guides/routing.md) rules, with ready-made profiles for China, Iran and Russia.
- Latency and speed [tests](@/guides/testing.md), and an Auto Selector that keeps you on a working server.
- [Proxy chains and custom configs](@/advanced/chains.md), [anti-censorship options](@/advanced/presets.md) such as TLS fragment, and built-in [Cloudflare WARP](@/advanced/warp.md).
- A [kill switch](@/advanced/kill_switch.md), and [control with `thronectl`](@/advanced/api.md) from scripts and other devices.
- A [Simple mode](@/get_started/configuration.md#interface-mode) for new users, and `Ctrl+K` search for commands and settings.

## Where to start {#where-to-start}

1. Install Throne: [Installation](@/get_started/installation.md).
2. Add your servers and connect: [Quick Start](@/get_started/configuration.md).
3. Read the [Guides](@/guides/_index.md) for the features you need.

On Android, start with [Throne for Android](@/android/_index.md). If something does not work, see [Troubleshooting](@/help/troubleshooting.md) and the [FAQ](@/help/faq.md).

## Credits {#credits}

Throne is built on [sing-box](https://github.com/SagerNet/sing-box), [Xray-core](https://github.com/xtls/xray-core), [Qv2ray](https://github.com/Qv2ray/Qv2ray), [Qt](https://www.qt.io/), [simple-protobuf](https://github.com/tonda-kriz/simple-protobuf), [fkYAML](https://github.com/fktn-k/fkYAML), [quirc](https://github.com/dlbeer/quirc) and [SQLiteCpp](https://github.com/srombauts/sqlitecpp).

To support Throne, click `Support Throne` in `Program` → `About`, or on the `About` screen of Throne for Android.
