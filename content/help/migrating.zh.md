+++
title = "从 Nekoray / NekoBox 迁移"
description = "Nekoray 和 NekoBox 的功能在 Throne 中的位置，以及如何迁移你的数据。"
weight = 40
toc = true
+++

## 功能去哪儿了？ {#where-did-it-go}

| 在 Nekoray / NekoBox 中你使用的是… | 在 Throne 中你使用的是… |
| --- | --- |
| `服务器`（Server）菜单 | 右键点击配置档列表。 |
| `首选项`（Preferences）菜单 | 工具栏中的 `设置`（Settings）按钮。 |
| `Tun 设置` → `Bypass Process Name` | 路由配置档中 `直连`（Direct）输入框里的 `processName:` 规则，例如 `processName:game.exe`。 |
| `Tun 设置` → `Whitelist mode` | 一个 `默认出站`（Default outbound）设为 `direct` 的路由配置档，并将程序或网站放在 `代理`（Proxy）输入框中。 |
| `Tun 设置` → `Bypass CIDR` | `Tun 设置` → `私有地址范围绕过`（Private Range Bypass），或 `直连` 输入框中的 `ip:` 规则。 |
| 适用于你所在国家的路由 `Preset` | `路由`（Routing）→ `下载配置档`（Download Profiles）。 |
| `默认出站` 设为 `bypass` | 在路由配置档中将 `默认出站` 设为 `direct`。 |
| FlatGray、LightBlue 和 BlackSoft 等主题 | `基本设置`（Basic Settings）→ `样式`（Style）→ `主题`（Theme）。 |
| `Copy links of selected (Neko Links)` | 标准分享链接。Throne 无法读取 `nekoray://` 或 `sn://` 链接。 |
| `劫持`（Hijack）或 `系统 DNS`（System DNS）（Throne 1.3.2 及更早版本） | `Tun 模式`（Tun Mode）。 |
| NekoBox for Android | Throne for Android；参见[下文](#from-nekobox-android)。 |

简洁模式会隐藏其中部分项目；参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。要按应用进行路由，请开启 `Tun 模式`：忽略系统代理的应用永远不会到达 Throne。参见[路由](@/guides/routing.zh.md#simple-rules)。

## 迁移你的数据 {#moving-data}

### 从 Nekoray 迁移 {#from-nekoray}

Throne 无法读取 Nekoray 的配置。请手动迁移你的数据：

1. 在 Nekoray 中，从每个订阅的分组设置里复制其 URL。在 Throne 中按 `Ctrl+V`，然后选择 `创建新的订阅分组`（Create new subscription group）。
2. 在 Nekoray 中选中你手动添加的服务器，使用 `服务器` → `分享`（Share）→ `复制选定项的链接`（Copy links of selected），然后在 Throne 中按 `Ctrl+V`。
3. 重新设置路由（[路由](@/guides/routing.zh.md)）以及你修改过的设置，例如监听端口、DNS 服务器和热键。

开启 TUN 模式或系统代理时，不要同时运行 Nekoray 和 Throne。

### 从 Throne 1.0.x 迁移 {#from-1-0}

Throne 1.1.0 从 Nekoray 风格的 JSON 文件改为使用数据库（`throne.db`），并且无法读取旧文件：直接在原位置更新 1.0.x 后，你的配置档、分组和设置不会出现（[#1765](https://github.com/throneproj/Throne/issues/1765)）。请保留旧文件夹的副本，并按上述方法手动迁移数据；[#1202](https://github.com/throneproj/Throne/issues/1202) 列出了手动操作步骤和一个非官方的社区脚本。在较新的版本之间，请使用 `基本设置` → `备份和恢复`（Backup and Restore）（[备份、更新与迁移](@/guides/backup.zh.md#backup-restore)）。

### 从 NekoBox for Android 迁移 {#from-nekobox-android}

Throne for Android 2.0.0 以前名为 NekoBox for Android，它基于桌面版的核心重新构建，无法从旧版本直接升级：

- 它使用新的密钥签名，因此请先卸载旧版本。
- 它只能恢复 `.thrbackup` 文件（包括桌面版的备份），无法恢复 1.6.x 及更早版本的备份文件和 WebDAV `.zip` 文件。

卸载之前，请复制你的订阅 URL 以及你手动添加的服务器的分享链接，或者改为在手机上恢复桌面版的备份（[备份、更新与迁移](@/guides/backup.zh.md#desktop-android)）。详细信息：[安装与升级](@/android/installation.zh.md#upgrading)。
