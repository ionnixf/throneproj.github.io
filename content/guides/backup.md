+++
title = "Backup, Updates & Migration"
description = "Back up and restore Throne, move your data between desktop and Android, and update the desktop app."
weight = 70
toc = true
+++

Make a backup before every update.

## Back up and restore {#backup-restore}

Open `Settings` → `Basic Settings` → `Backup and Restore`. To create a backup, tick the parts you want under `Create Backup` (all are ticked by default), click `Create Backup...` and choose where to save the `.thrbackup` file.

| Part | What it contains |
| --- | --- |
| `Profiles (groups and proxies)` | Groups, subscriptions and profiles. |
| `Routing profiles` | Routing profiles and their rules. |
| `Settings` | All settings, including DNS, TUN, WARP and inbounds. |
| `OTP profiles` | The [OTP Manager](@/advanced/vpn_profiles.md#otp-manager) entries. |
| `IP lists and scans` | The lists and results of the [IP scanner](@/guides/testing.md#ip-scanner). |
| `Custom icons` | Custom tray icons. |

A backup does not contain traffic history, logs, the core's cache, or whether `Tun Mode` or `System Proxy` was on.

{% alert_warning() %}
A backup contains your server passwords, subscription links and other secrets. Keep the file private and never attach it to a bug report.
{% end %}

To restore a backup:

1. Click `Restore from Backup...` and choose a `.thrbackup` file. Throne shows when and on which device it was created.
2. Tick the parts to restore and click `Restore`. Each part replaces your current data, which cannot be undone.
3. Throne restarts.

- Backups from older versions restore normally. A backup from a newer version can fail with "Unsupported backup format version": update Throne first.
- Routing rules with conditions that your version does not support are left out, and Throne says how many.
- If you restore only one of `Profiles (groups and proxies)` and `Routing profiles`, groups with their own routing profile go back to the default one.

For a headless Throne, see [thronectl](@/headless/thronectl.md#from-desktop).

## WebDAV backups {#webdav}

Throne desktop and Throne for Android can keep backups on a WebDAV server, such as Nextcloud, a NAS or Yandex Disk, and each app restores what the other uploaded. On the desktop, set up the server with `WebDAV Settings...` in `Backup and Restore`, then click `Upload to WebDAV` to upload the parts ticked under `Create Backup`. Throne never uploads on a schedule or deletes old backups by itself.

{% alert_warning() %}
Backups are not encrypted. Anyone who can read the WebDAV folder can read your server passwords and subscription links. Use a server you trust, over `https://`.
{% end %}

### WebDAV settings {#webdav-settings}

| Field | What it does |
| --- | --- |
| `Server URL` | The WebDAV address, starting with `http://` or `https://`, such as `https://cloud.example.com/remote.php/dav/files/me/` for Nextcloud. |
| `Username`, `Password` | Your login, sent with HTTP basic authentication, so unencrypted over `http://`. |
| `Folder` | The folder below the server URL (default `Throne`, subfolders separated with `/`). Throne creates missing folders. |
| `Device name` | The name that the backup list shows for this computer (default: the computer's name). |
| `Connect through the proxy` | Sends the requests through Throne's local proxy. A profile must be running. |

`Test` tries the values before you `Save` them. These settings belong to this computer: backups never contain them.

Without `Connect through the proxy`, WebDAV requests go out directly unless `Use proxy` or `System Proxy` is on (see [Network requests](@/reference/privacy.md#network-requests)). Throne checks the server's TLS certificate unless `Ignore TLS errors` is on.

### Restore or delete a backup {#webdav-restore}

1. Click `Restore from WebDAV...`. Throne lists the backups, newest first, with their device, platform, version and parts.
2. Select a backup and click `Restore` (or double-click it).
3. Continue from step 2 of [Back up and restore](#backup-restore).

Throne checks each download against the backup list. Files that are not in the list, such as ones you copied there yourself, show "Unknown device" and cannot be checked. To delete a backup, select it and click `Delete`.

| Message | What to do |
| --- | --- |
| "Wrong username or password." | Check `Username` and `Password`. Services with a two-step login often need an app password. |
| "Permission denied." | The account may not write to this `Folder`. Choose another one, or check the rights on the server. |
| "The server redirected to …; check the server URL." | Enter the address from the message as `Server URL`. |
| "The server's folder listing could not be read." | `Server URL` is not a WebDAV address, for example the web page of your storage. |
| "The server did not answer in time." | Check the connection, or try `Connect through the proxy`. |
| "Request with proxy but no profile started." | Start a profile, or turn off `Connect through the proxy`, `Use proxy` and `System Proxy`. |

### WebDAV on Android {#webdav-android}

In Throne for Android, open `Settings` → `Backup & restore` (or `Tools` in the drawer). The `WebDAV backup` card has `Back up to WebDAV`, `Restore from WebDAV` and `WebDAV settings`. To share backups with the desktop, enter the same server and folder: the desktop's `Folder` is `Backup path` here. `Back up to WebDAV` uploads the parts ticked in the `Local backup` card.

Android has no proxy option for WebDAV: in VPN mode, the requests go through the VPN like other apps' traffic. It never ignores TLS certificate errors.

## Move between desktop and Android {#desktop-android}

The desktop app and Throne for Android use the same backup format, so you can move your data both ways, as a file or through [WebDAV](#webdav).

From the desktop to a phone:

1. Create a backup on the desktop and copy the `.thrbackup` file to the phone.
2. On the phone, open `Settings` → `Backup & restore`, tap `Restore` and choose the file (or open it from a file manager).
3. Tick the parts to restore and confirm.

From a phone to the desktop: on the phone, open `Settings` → `Backup & restore`, tick the parts and tap `Create backup` (`Share` sends the file to another app). Copy the file to the computer and [restore it](#backup-restore).

A desktop backup changes a few things when you restore it on Android:

| Item | On Android |
| --- | --- |
| `OTP profiles`, `IP lists and scans`, `Custom icons`, and the `Server Address` of groups | Not restored. |
| Settings that both apps have | Take the backup's values. For example, the TUN MTU becomes 1500 (Android default: 9000). |
| `Allow connections from the LAN` | Turned off, with a warning, if the backup allows other devices to connect but your phone did not. |
| Routing rules with conditions that Android does not support | Skipped, with a warning. |
| Raw routing profiles | Kept, but read-only and unused. If one was active, the first usable routing profile becomes active. |
| OpenVPN and OpenConnect endpoints of a routing profile | Kept, but they do not run. |
| Tailscale and Extra Core profiles | Restored, but they cannot be started. |

In the other direction, rules that match Android apps and the per-app proxy settings have no effect on the desktop.

## Update the desktop app {#updating}

An update keeps your profiles and settings (they live in the [data folder](@/reference/files.md#data-folder)), but create a backup first.

1. Open `Program` → `Check For Update` (before 1.4.0: `Tools` → `Check For Update`).
2. If there is a newer version, the `Update` window shows what changed. Click `Update`.
3. When Throne asks to restart, click `Yes`. The updater installs the new version.

The one-click `Update` works only for ZIP copies and the Windows installer's default install for the current user, and not while a background Throne runs from the same installation. Other installs update like this:

| Installed with | How to update |
| --- | --- |
| ZIP file | `Update`, or replace the program files with the new ZIP. Keep the `config` folder. |
| Windows installer, current user (default) | `Update`, or run the new installer. |
| Windows installer, all users | Run the new installer. |
| `.deb` or `.rpm` package | Install the new package. |
| Linux install script | Run the script again. |
| macOS | Replace `Throne.app` with the new version. See [Installation](@/get_started/installation.md#macos). |
| WinGet, Scoop, AUR, Nix, Homebrew, or the APT or RPM repository | Use the package manager. See [Package managers](@/get_started/installation.md#package-managers). |

If you reinstall with the Windows installer, answer `No` when the uninstaller asks whether to also delete your profiles, settings and logs.

Some antivirus programs flag the updater because it replaces program files. If the update fails, check that Throne is not inside your Downloads folder ([#1109](https://github.com/throneproj/Throne/issues/1109)) and try again, or download the new version from the [download page](@/downloads.md).

## Beta versions {#beta}

Beta versions (pre-releases) get features and fixes first, but can have more bugs. Make a backup before you install one.

- **Desktop:** tick `Settings` → `Basic Settings` → `Miscellaneous` → `Allow updating to beta versions` (in Simple mode, under `Network Settings` on the `Common` tab).
- **Android:** turn on `Settings` → `General` → `Allow updating to beta versions`. See [the in-app updater](@/android/installation.md#updater).

To go back, turn the option off. The updater only offers newer versions, so you stay on the beta until a newer stable release comes out.

## Old versions {#old-versions}

- **Throne desktop older than 1.1.0:** 1.1.0 moved all data into `throne.db` and does not convert older configurations. Add your subscriptions and profiles again, or try the community scripts in [#1202](https://github.com/throneproj/Throne/issues/1202). Backups from 1.1.3 or later restore in newer versions.
- **Nekoray or NekoBox:** see [Coming from Nekoray / NekoBox](@/help/migrating.md).
- **Throne for Android older than 2.0.0:** 2.0.0 is signed with a new key, so uninstall the old app first. 2.0.0 starts empty and cannot restore old backups (`throne_backup_*.json` or WebDAV `.zip` files), only `.thrbackup` files. Note your subscription links before you uninstall, or move everything with a desktop backup. See [Installation & Upgrade](@/android/installation.md#upgrading).
