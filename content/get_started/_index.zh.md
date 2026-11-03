+++
title = "开始使用"
description = "介绍 Throne 是什么、支持哪些系统，以及从哪里开始。"
weight = 1
sort_by = "weight"
aliases = ["/zh/introduction/"]
+++

本节介绍 Throne 是什么、支持哪些系统，以及应该先阅读哪些页面。如果你是第一次使用 Throne，请从这里开始。

## Throne 是什么 {#what-is-throne}

Throne（前身为 Nekoray）是一款适用于 Windows、Linux 和 macOS 的免费开源（GPL-3.0）代理客户端。它拥有基于 Qt 的图形界面，并使用 ThroneCore 作为核心：ThroneCore 基于 sing-box，也会为需要 Xray 的配置档运行 Xray。你可以添加服务商提供的服务器或你自己的服务器，选择哪些流量经过这些服务器，然后以系统代理模式或 TUN 模式连接。

Throne for Android（前身为 NekoBox for Android）是 Throne 的 Android 应用。从 2.0.0 版本起，它与桌面版使用相同的核心、相同的设置模型和相同的备份格式，因此你可以在电脑和手机之间迁移你的整套设置。参见 [Throne for Android](@/android/_index.zh.md)。

自 1.4.0 起，同一个 Throne 程序也可以在没有窗口的情况下运行在服务器、路由器或容器中，你可以用命令行工具 `thronectl` 控制它。参见[无界面 Throne](@/headless/_index.zh.md)。

如果你以前使用过 Nekoray 或 NekoBox，请阅读[从 Nekoray / NekoBox 迁移](@/help/migrating.zh.md)。

## 平台 {#platforms}

| 系统 | 要求 | 安装包类型 |
|---|---|---|
| Windows | Windows 10 1809 或更高版本（x64、ARM64）。使用 legacy 版本时支持 Windows 7 SP1 或更高版本（x64、32 位 x86）。 | 安装程序、便携版 ZIP |
| Linux | x64 或 ARM64，glibc 2.28 或更高版本，例如 Debian 10、Ubuntu 20.04 或 RHEL 8（自 1.4.0 起）。`-system-qt` 软件包需要 glibc 2.34 或更高版本。 | 便携版 ZIP、`.deb`、`.rpm`、安装脚本、[APT 和 RPM 软件源](@/get_started/installation.zh.md#apt-repository)、[AUR](@/get_started/installation.zh.md#aur) |
| macOS | macOS 14.4 或更高版本（Apple Silicon 和 Intel）。使用 legacy 版本时支持 macOS 10.15 或更高版本（Intel）。 | ZIP、[Homebrew](@/get_started/installation.zh.md#homebrew) |
| Android | Android 7.0 或更高版本 | APK |

安装文件请从[下载](@/downloads.zh.md)页面获取。

## 功能亮点 {#highlights}

- 支持多种协议，包括带 REALITY 和 XHTTP 的 VLESS、Shadowsocks、Trojan、Hysteria、TUIC、WireGuard 和 AmneziaWG、NaïveProxy、OpenVPN 以及 OpenConnect：[协议与导入格式](@/reference/protocols.zh.md)。
- DNS 隧道，把你的流量放在 DNS 查询中传输：DNSTT/VayDNS、MasterDNS，以及用于 VLESS（Xray）的 XDNS（自 1.4.0 起）：[DNS 隧道](@/advanced/dns_tunnels.zh.md)。
- 订阅可按计划自动更新，每个分组都可以单独设置 User-Agent 和 HWID：[订阅与分组](@/guides/subscriptions.zh.md)。
- 通过系统代理或 TUN 模式为所有应用提供代理，并可与网络中的其他设备共享代理：[系统代理、TUN 与局域网共享](@/guides/proxy_modes.zh.md)。
- 带规则和规则集的路由配置档，以及适用于中国、伊朗和俄罗斯的现成配置档：[路由](@/guides/routing.zh.md)。
- 延迟测试、IP 测试和速度测试，以及让你始终使用可用服务器的自动选择器：[测试与自动选择器](@/guides/testing.zh.md)。
- 代理链，以及你自己的 sing-box 或 Xray 配置：[代理链与自定义配置](@/advanced/chains.zh.md)。
- TLS 分片、uTLS 和 ECH 等反审查选项：[反审查预设](@/advanced/presets.zh.md)。
- 内置 Cloudflare WARP：[Cloudflare WARP](@/advanced/warp.zh.md)。
- 桌面版和 Android 应用通用的备份：[备份、更新与迁移](@/guides/backup.zh.md)。
- 断网保护，用于拦截不经过 Throne 的流量（自 1.4.0 起）：[Kill Switch（断网保护）](@/advanced/kill_switch.zh.md)。
- Throne API，可通过 `thronectl` 从脚本、热键和其他设备控制和管理 Throne（自 1.4.0 起）：[Throne API 与 thronectl](@/advanced/api.zh.md)。
- 适用于服务器和容器的无界面模式（自 1.4.0 起）：[无界面 Throne](@/headless/_index.zh.md)。
- 为新用户隐藏高级选项的简洁模式，以及用 `Ctrl+K` 搜索命令和设置（自 1.4.0 起）：[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

## 从哪里开始 {#where-to-start}

1. 安装 Throne：[安装](@/get_started/installation.zh.md)。
2. 添加服务器并连接：[快速入门](@/get_started/configuration.zh.md)。
3. 阅读[使用指南](@/guides/_index.zh.md)，了解你需要的功能，例如订阅、路由和 TUN 模式。

在 Android 上，请从 [Throne for Android](@/android/_index.zh.md) 开始。如果遇到问题，请参阅[故障排除](@/help/troubleshooting.zh.md)和[常见问题](@/help/faq.zh.md)。

## 致谢 {#credits}

Throne 基于以下项目构建：

- [SagerNet/sing-box](https://github.com/SagerNet/sing-box)：核心的代理引擎
- [XTLS/Xray-core](https://github.com/xtls/xray-core)：Xray 引擎
- [Qv2ray](https://github.com/Qv2ray/Qv2ray)：部分界面代码和系统代理代码
- [Qt](https://www.qt.io/)：界面工具包
- [simple-protobuf](https://github.com/tonda-kriz/simple-protobuf)：界面与核心之间的消息通信
- [fkYAML](https://github.com/fktn-k/fkYAML)：Clash YAML 导入
- [quirc](https://github.com/dlbeer/quirc)：二维码识别
- [srombauts/sqlitecpp](https://github.com/srombauts/sqlitecpp)：配置档和设置数据库

要支持 Throne 的开发，请在 `程序`（Program）→ `关于`（About）中点击 `支持 Throne`（Support Throne）（自 1.4.0 起）。它会打开捐赠页面。Throne for Android 的 `关于`（About）页面中也有同样的选项（自 2.0.4 起）。
