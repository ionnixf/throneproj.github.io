+++
title = "Coming from Nekoray / NekoBox"
description = "Where Nekoray and NekoBox features are in Throne, and how to move your data."
weight = 40
toc = true
+++

## Where did it go? {#where-did-it-go}

| In Nekoray / NekoBox you used… | In Throne you use… |
| --- | --- |
| The `Server` menu | Right-click the profile list. |
| The `Preferences` menu | The `Settings` button in the toolbar. |
| `Tun Settings` → `Bypass Process Name` | `processName:` rules in the `Direct` box of your routing profile, for example `processName:game.exe`. |
| `Tun Settings` → `Whitelist mode` | A routing profile with `Default outbound` set to `direct`, and the programs or sites in the `Proxy` box. |
| `Tun Settings` → `Bypass CIDR` | `Tun Settings` → `Private Range Bypass`, or `ip:` rules in the `Direct` box. |
| A routing `Preset` for your country | `Routing` → `Download Profiles`. |
| `Default Outbound` set to `bypass` | `Default outbound` set to `direct` in the routing profile. |
| Themes such as FlatGray, LightBlue and BlackSoft | `Basic Settings` → `Style` → `Theme`. |
| `Copy links of selected (Neko Links)` | Standard share links. Throne cannot read `nekoray://` or `sn://` links. |
| `Hijack` or `System DNS` (Throne 1.3.2 and older) | `Tun Mode`. |
| NekoBox for Android | Throne for Android; see [below](#from-nekobox-android). |

Simple mode hides some of these; see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode). For per-app routing, turn on `Tun Mode`: apps that ignore the system proxy never reach Throne. See [Routing](@/guides/routing.md#simple-rules).

## Moving your data {#moving-data}

### From Nekoray {#from-nekoray}

Throne cannot read Nekoray's configuration. Move your data by hand:

1. In Nekoray, copy the URL of each subscription from its group settings. In Throne, press `Ctrl+V` and choose `Create new subscription group`.
2. Select the servers you added by hand in Nekoray, use `Server` → `Share` → `Copy links of selected`, and press `Ctrl+V` in Throne.
3. Set up routing again ([Routing](@/guides/routing.md)), and the settings you changed, such as the listen port, DNS servers and hotkeys.

Do not run Nekoray and Throne at the same time with TUN mode or the system proxy on.

### From Throne 1.0.x {#from-1-0}

Throne 1.1.0 moved from Nekoray-style JSON files to a database (`throne.db`) and cannot read the old files: after an in-place update from 1.0.x, your profiles, groups and settings do not appear ([#1765](https://github.com/throneproj/Throne/issues/1765)). Keep a copy of the old folder and move your data by hand as above; [#1202](https://github.com/throneproj/Throne/issues/1202) lists manual steps and an unofficial community script. Between newer versions, use `Basic Settings` → `Backup and Restore` ([Backup, Updates & Migration](@/guides/backup.md#backup-restore)).

### From NekoBox for Android {#from-nekobox-android}

Throne for Android 2.0.0, formerly NekoBox for Android, is rebuilt on the desktop core and cannot update an older version:

- It is signed with a new key, so uninstall the old version first.
- It restores only `.thrbackup` files, including desktop backups, not the backups or WebDAV `.zip` files of version 1.6.x and older.

Before you uninstall, copy your subscription URLs and the share links of servers you added by hand, or restore a desktop backup on the phone instead ([Backup, Updates & Migration](@/guides/backup.md#desktop-android)). Details: [Installation & Upgrade](@/android/installation.md#upgrading).
