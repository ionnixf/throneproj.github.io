+++
title = "开始使用"
description = "介绍 Throne 是什么、支持哪些系统，以及从哪里开始。"
weight = 1
sort_by = "weight"
aliases = ["/zh/introduction/"]
+++

## Throne 是什么 {#what-is-throne}

Throne（前身为 Nekoray）是一款适用于 Windows、Linux 和 macOS 的免费开源（GPL-3.0）代理客户端。它的核心 ThroneCore 基于 sing-box，也会为需要 Xray 的配置档运行 Xray。你添加服务器，选择哪些流量经过这些服务器，然后以系统代理模式或 TUN 模式连接。

- [Throne for Android](@/android/_index.zh.md)（前身为 NekoBox for Android）与桌面版使用相同的核心、相同的设置和相同的[备份格式](@/guides/backup.zh.md)，因此你可以在电脑和手机之间迁移你的整套设置。
- [无界面 Throne](@/headless/_index.zh.md) 不需要窗口，可以运行在服务器、路由器或容器中，用 `thronectl` 控制。
- 如果你以前使用过 Nekoray 或 NekoBox，请阅读[从 Nekoray / NekoBox 迁移](@/help/migrating.zh.md)。

## 平台 {#platforms}

| 系统 | 要求 | 安装包类型 |
|---|---|---|
| Windows | Windows 10 1809 或更高版本（x64、ARM64）。使用 legacy 版本时支持 Windows 7 SP1 或更高版本（x64、32 位 x86）。 | 安装程序、便携版 ZIP |
| Linux | x64 或 ARM64，glibc 2.28 或更高版本，例如 Debian 10、Ubuntu 20.04 或 RHEL 8。`-system-qt` 软件包需要 glibc 2.34 或更高版本。 | 便携版 ZIP、`.deb`、`.rpm`、安装脚本、[APT 和 RPM 软件源](@/get_started/installation.zh.md#apt-repository)、[AUR](@/get_started/installation.zh.md#aur) |
| macOS | macOS 14.4 或更高版本（Apple Silicon 和 Intel）。使用 legacy 版本时支持 macOS 10.15 或更高版本（Intel）。 | ZIP、[Homebrew](@/get_started/installation.zh.md#homebrew) |
| Android | Android 7.0 或更高版本 | APK |

安装文件请从[下载](@/downloads.zh.md)页面获取。

## 功能亮点 {#highlights}

- 支持多种[协议](@/reference/protocols.zh.md)，包括带 REALITY 和 XHTTP 的 VLESS、Shadowsocks、Trojan、Hysteria、TUIC、WireGuard、AmneziaWG、NaïveProxy、OpenVPN、OpenConnect 以及 [DNS 隧道](@/advanced/dns_tunnels.zh.md)。
- 可按计划自动更新的[订阅](@/guides/subscriptions.zh.md)。
- [系统代理、TUN 模式与局域网共享](@/guides/proxy_modes.zh.md)。
- [路由](@/guides/routing.zh.md)规则，以及适用于中国、伊朗和俄罗斯的现成配置档。
- 延迟和速度[测试](@/guides/testing.zh.md)，以及让你始终使用可用服务器的自动选择器。
- [代理链与自定义配置](@/advanced/chains.zh.md)、TLS 分片等[反审查选项](@/advanced/presets.zh.md)，以及内置的 [Cloudflare WARP](@/advanced/warp.zh.md)。
- [断网保护（Kill Switch）](@/advanced/kill_switch.zh.md)，以及通过 [`thronectl`](@/advanced/api.zh.md) 从脚本和其他设备控制 Throne。
- 为新用户准备的[简洁模式](@/get_started/configuration.zh.md#interface-mode)，以及用 `Ctrl+K` 搜索命令和设置。

## 从哪里开始 {#where-to-start}

1. 安装 Throne：[安装](@/get_started/installation.zh.md)。
2. 添加服务器并连接：[快速入门](@/get_started/configuration.zh.md)。
3. 阅读[使用指南](@/guides/_index.zh.md)，了解你需要的功能。

在 Android 上，请从 [Throne for Android](@/android/_index.zh.md) 开始。如果遇到问题，请参阅[故障排除](@/help/troubleshooting.zh.md)和[常见问题](@/help/faq.zh.md)。

## 致谢 {#credits}

Throne 基于 [sing-box](https://github.com/SagerNet/sing-box)、[Xray-core](https://github.com/xtls/xray-core)、[Qv2ray](https://github.com/Qv2ray/Qv2ray)、[Qt](https://www.qt.io/)、[simple-protobuf](https://github.com/tonda-kriz/simple-protobuf)、[fkYAML](https://github.com/fktn-k/fkYAML)、[quirc](https://github.com/dlbeer/quirc) 和 [SQLiteCpp](https://github.com/srombauts/sqlitecpp) 构建。

要支持 Throne，请在 `程序`（Program）→ `关于`（About）中点击 `支持 Throne`（Support Throne）。Throne for Android 的 `关于`（About）页面中也有这个选项。
