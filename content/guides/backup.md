+++
title = "Backup, Updates & Migration"
description = "Back up and restore Throne, move your data between desktop and Android, update the desktop app, and upgrade from old versions."
weight = 70
toc = true
+++

This page shows how to save your profiles and settings to a file or a WebDAV server, restore them, and move them between Throne desktop and Throne for Android. It also explains how to update the desktop app and what to do when you come from an old version. Make a backup before every update.

## Back up and restore {#backup-restore}

Open `Settings` → `Basic Settings` → `Backup and Restore`.

To create a backup:

1. Under `Create Backup`, tick the parts you want. All parts are ticked by default.
2. Click `Create Backup...` and choose where to save the file. The suggested name is `Throne-backup.thrbackup` in your home folder.
3. Throne confirms the file and lists the parts it included.

| Part | What it contains |
| --- | --- |
| `Profiles (groups and proxies)` | Your groups, subscriptions and profiles. |
| `Routing profiles` | Your routing profiles and their rules. |
| `Settings` | All settings, including DNS, TUN, WARP and inbound settings. |
| `OTP profiles` | The entries of the OTP Manager. |
| `IP lists and scans` | Your IP lists and the scans of the [IP scanner](@/guides/testing.md#ip-scanner), with their results (since 1.4.0). |
| `Custom icons` | Your custom tray icons. |

A backup does not contain traffic history, logs or the core's cache files. It also does not remember whether `Tun Mode` or `System Proxy` was on. Since 1.4.0, a backup also records the Throne version and the name of the device that created it: the computer's name, or the `Device name` from the [WebDAV settings](#webdav-settings).

{% alert_warning() %}
A backup contains your server passwords, subscription links and other secrets. Keep the file private and never attach it to a bug report.
{% end %}

To restore a backup:

1. Click `Restore from Backup...` and choose a `.thrbackup` file.
2. Throne shows when the backup was created and, if the file records it, the device that created it ("Created by: …"). Tick the parts you want to restore. Parts that the file does not contain are greyed out.
3. Click `Restore`. Each selected part replaces your current data. This cannot be undone.
4. Throne restarts.

Backups from older versions of Throne restore normally. A backup from a newer version can be refused with "Unsupported backup format version"; update Throne first. Since 1.3.2, if the backup has routing rules with conditions that this version of Throne does not support, for example rules from a newer version, Throne leaves those rules out and tells you how many it skipped.

Since 1.4.0, when you restore only one of `Profiles (groups and proxies)` and `Routing profiles`, groups that have their own routing profile go back to the default routing profile. Throne for Android does the same since 2.0.5.

To back up or restore a headless Throne, or to send a desktop backup to one, see [thronectl](@/headless/thronectl.md#from-desktop).

## WebDAV backups {#webdav}

Since 1.4.0, Throne desktop can keep backups on a WebDAV server, such as Nextcloud, a NAS or Yandex Disk. Throne for Android can do the same. Both apps use the same folder layout and the same list of backups, so each app can restore what the other one uploaded. See [WebDAV on Android](#webdav-android).

The `WebDAV` group in `Settings` → `Basic Settings` → `Backup and Restore` has three buttons:

| Button | What it does |
| --- | --- |
| `WebDAV Settings...` | Sets the server, the folder and the name of this computer. |
| `Upload to WebDAV` | Creates a backup of the parts ticked under `Create Backup` and uploads it. |
| `Restore from WebDAV...` | Lists the backups on the server, so that you can restore or delete one. |

Throne uploads only when you click `Upload to WebDAV`. There are no automatic or scheduled uploads.

{% alert_warning() %}
Backups are not encrypted. Anyone who can read the WebDAV folder can read your server passwords and subscription links. Use a server that you trust, and connect to it with `https://`.
{% end %}

### WebDAV settings {#webdav-settings}

Click `WebDAV Settings...`. Until you set a server, the other two buttons say "Enter your WebDAV server in WebDAV Settings first." and open this window.

| Field | Default | What it does |
| --- | --- | --- |
| `Server URL` | Empty | The WebDAV address of your storage. It must start with `http://` or `https://`, for example `https://cloud.example.com/remote.php/dav/files/me/` for Nextcloud. |
| `Username`, `Password` | Empty | Your login for the server. Throne sends them with HTTP basic authentication, so over `http://` they are not encrypted. |
| `Folder` | `Throne` | The folder below the server URL that holds the backups. Separate subfolders with `/`, for example `Backups/Throne`. Throne creates missing folders. An empty field means `Throne`. |
| `Device name` | The computer's name | The name that the backup list shows for the backups of this computer. |
| `Connect through the proxy` | Off | Sends the WebDAV requests through Throne's local proxy port. A profile must be running. |

`Test` tries the values in the window, also before you save them. It logs in, creates the folder if it is missing, and shows "Connected. The backup folder is ready." or "Connection failed:" with the reason. `Save` keeps the values.

The WebDAV settings and the device name belong to this computer. Backups never contain them, and a restore does not change them.

Without `Connect through the proxy`, WebDAV requests follow the same rule as subscription updates: they go out directly, unless `Use proxy` or `System Proxy` is on. See [Network requests](@/reference/privacy.md#network-requests). Throne checks the TLS certificate of the server, unless `Ignore TLS errors` is on (`Settings` → `Basic Settings` → `Miscellaneous`, Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

### Upload a backup {#webdav-upload}

1. Under `Create Backup`, tick the parts you want.
2. Click `Upload to WebDAV`.
3. Throne shows "Backup uploaded:" and the name of the new file.

Each upload is a new file named `throne_backup_<version>_<date>_<time>_<code>.thrbackup`, for example `throne_backup_1.4.0_20261024_153000_a3f9.thrbackup`. The date and time are in UTC, and the last four characters are random, so that two devices never write the same file. Next to the backups, Throne keeps the backup list `throne_backups.json`. For each backup, it records the time, the size, the SHA-256 checksum, the device name and a random device ID, the platform, the Throne version and the parts. If only the list could not be updated, Throne says "Backup uploaded, but the backup list could not be updated: …". The backup itself is on the server.

Throne never deletes old backups by itself. Delete the ones you no longer need, as described below.

### Restore or delete a backup {#webdav-restore}

1. Click `Restore from WebDAV...`. Throne lists the backups in the folder, newest first, with the columns `Created`, `Device`, `Platform`, `Version`, `Parts` and `Size`. Backups of this computer show "(this device)".
2. Select a backup and click `Restore`, or double-click it. Throne downloads it.
3. The restore window opens. Continue from step 2 of [Back up and restore](#backup-restore).

Before the restore window opens, Throne compares the download with the size and the SHA-256 checksum in the backup list. If they differ, it stops with "The downloaded backup does not match the backup list; it may have been replaced or damaged."

The list shows every `.thrbackup` file in the folder. A file that is not in the backup list, for example one that you copied there yourself, shows "Unknown device", and Throne cannot check its download. If the folder has no backups, Throne says "There are no backups in the WebDAV folder yet."

To delete a backup, select it, click `Delete` and confirm "Delete this backup from the WebDAV server? This cannot be undone." Throne removes the file and its entry in the backup list.

If a WebDAV action fails, the message says why:

| Message | What to do |
| --- | --- |
| "Wrong username or password." | Check `Username` and `Password`. Services with a two-step login often need an app password here. |
| "Permission denied." | The account may not write to this folder. Choose another `Folder`, or check the rights on the server. |
| "Not found." | The backup is no longer on the server, or `Server URL` is wrong. Open the list again, or check `Server URL`. |
| "The server redirected to …; check the server URL." | Enter the address from the message as `Server URL`. Throne does not follow redirects when it uploads, lists or deletes. |
| "Could not create the folder on the server: …" | Create the folder on the server yourself, or choose another `Folder`. |
| "The server's folder listing could not be read." | The URL is not a WebDAV address, for example the web page of your storage. Use the WebDAV address that your service names. |
| "The server did not answer in time." | Throne waits 30 seconds for an answer, and 2 minutes while a backup is transferred. Check the connection, or try `Connect through the proxy`. |
| "Request with proxy but no profile started." | Start a profile, or turn off `Connect through the proxy`, `Use proxy` and `System Proxy`. |
| "Request with proxy but the mixed inbound is disabled and Tun Mode is off." | `Disable Mixed Inbound` is on. Turn on `Tun Mode`, or turn off the proxy options. |

### WebDAV on Android {#webdav-android}

In Throne for Android, open `Tools` in the drawer, or `Settings` → `Backup & restore`. The `WebDAV backup` card has `Back up to WebDAV`, `Restore from WebDAV` and `WebDAV settings`. The settings are `Server address`, `Username`, `Password`, `Backup path` (default `Throne`) and `Test connection`, and since 2.0.4 also `Device name`. `Back up to WebDAV` uploads the parts ticked in the `Local backup` card.

To share backups between the apps, enter the same server and the same folder in both: `Folder` on the desktop is `Backup path` on Android. A desktop backup changes a few things when you restore it on Android. See [Move between desktop and Android](#desktop-android).

The Android app works differently in a few points:

- In 2.0.3, it does not use the backup list. `Restore from WebDAV` shows the names of the `.thrbackup` files in the folder, and a tap on one opens the restore window. The desktop shows the backups that 2.0.3 uploads as "Unknown device".
- Since 2.0.4, it uses the same backup list as the desktop. `Restore from WebDAV` shows the date, the device, the platform, the version, the contents and the size of each backup. Tap a backup to see its details, then `Restore` or `Delete`. Downloads are checked against the list as on the desktop. The default `Device name` is the device name from Android's settings, or the manufacturer and model.
- It has no proxy option for WebDAV. Its WebDAV requests never use the local proxy. In VPN mode, while Throne is connected, they pass through the VPN like the traffic of other apps.
- It never ignores TLS certificate errors for WebDAV.

## Move between desktop and Android {#desktop-android}

Throne desktop and Throne for Android use the same backup format, so you can move your data in both directions.

From the desktop to a phone:

1. On the desktop, create a backup as described above.
2. Copy the `.thrbackup` file to the phone.
3. On the phone, open `Settings` → `Backup & restore` (or `Tools` in the drawer), tap `Restore` and choose the file. Opening the file from a file manager also offers to restore it.
4. Tick the parts to restore and confirm. Throne restarts.

From a phone to the desktop:

1. On the phone, open `Settings` → `Backup & restore`, tick the parts and tap `Create backup`. `Share` sends the file to another app.
2. Copy the file to the computer.
3. On the desktop, restore it as described above.

You can also move your data through a WebDAV server: upload a backup in one app and restore it in the other. See [WebDAV backups](#webdav).

A desktop backup changes a few things when you restore it on Android:

| Item | What happens on Android |
| --- | --- |
| `OTP profiles`, `IP lists and scans` and `Custom icons` | Not restored. Android has no IP scanner, so the `Server Address` of groups is not restored either. |
| Settings that both apps have | Take the values from the backup. For example, the TUN MTU becomes 1500 (Android default: 9000) and the log level becomes `info` (Android default: `warn`). |
| Settings that only Android has | Keep their current values. |
| Connections from other devices | When you restore `Settings` and the backup allows other devices to connect, but your phone did not, Throne turns `Allow connections from the LAN` off and shows a warning. |
| Routing rules with conditions that Android does not support | Skipped, with a warning that says how many (since 2.0.2). |
| Raw routing profiles | Kept, but read-only. Android cannot use them; if one was active, the first usable routing profile becomes active. |
| OpenVPN and OpenConnect endpoints of a routing profile | Kept with the profile, but they do not run on Android. |
| Tailscale and Extra Core profiles | Restored, but they cannot be started on Android. |

In the other direction, rules that match Android apps and the per-app proxy settings have no effect on the desktop.

## Update the desktop app {#updating}

Your profiles and settings live in the data folder, which an update keeps (see [data folder](@/reference/files.md#data-folder)). Still, create a backup first.

1. Open `Program` → `Check For Update` (before 1.4.0: `Tools` → `Check For Update`). Throne asks GitHub for the latest release. If you already run it, you see "No update". To see which version you run, open `Program` → `About`.
2. If there is a newer version, the `Update` window says "Throne … is available, you are using …." with the buttons `Update`, `Open in browser` and `Close`.
3. Click `Update`. Throne downloads the new version and asks "Update is ready, restart to install?".
4. Click `Yes`. Throne closes and the updater installs the new version.

Since 1.4.0, the window shows the changes of every version after yours, up to the new one, taken from the changelog of Throne. When the new version is a stable release, its pre-releases are not listed separately, because the release lists their changes. If Throne cannot load the changelog, the window says "Release notes are unavailable." Links in the notes open in your browser. `Open in browser` opens the release page on GitHub.

The one-click `Update` works only for ZIP copies and for the Windows installer's default install for the current user, where Throne keeps its data next to the program. Other installs only get `Open in browser`. While a background Throne runs from the same installation, the window has no `Update` button and says "Update is unavailable while a background Throne (PID …) runs from this installation; stop it first." On macOS, with the system-Qt packages and with the Linux install script, no updater ships with Throne, so `Check For Update` is greyed out. Update each kind of install like this:

| Installed with | How to update |
| --- | --- |
| ZIP file | `Update`, or download the new ZIP and replace the program files. Keep the `config` folder. |
| Windows installer, for the current user (default) | `Update`, or download and run the new installer. |
| Windows installer, for all users | Download and run the new installer. |
| `.deb` or `.rpm` package | Download and install the new package. |
| Linux install script | Run the script again. |
| macOS | Download the new version and replace `Throne.app`. See [Installation](@/get_started/installation.md#macos). |
| WinGet, Scoop, AUR, Nix, Homebrew, or the APT or RPM repository | Use the package manager. See [Package managers](@/get_started/installation.md#package-managers). |

If you uninstall and reinstall with the Windows installer, answer `No` when the uninstaller asks "Also delete your Throne profiles, settings and logs?". Otherwise your data is deleted.

Some antivirus programs flag the updater, because it replaces program files. If the update fails, check that Throne is not inside your Downloads folder ([#1109](https://github.com/throneproj/Throne/issues/1109)) and try again, or download the new version from the [download page](@/downloads.md).

## Beta versions {#beta}

Beta versions (pre-releases) get new features and fixes first, but they can have more bugs. Make a backup before you install one.

- **Desktop:** turn on `Settings` → `Basic Settings` → `Miscellaneous` → `Allow updating to beta versions`. In Simple mode, this checkbox is in the `Network Settings` section of the `Common` tab (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). `Check For Update` then also offers pre-releases, and for a pre-release the window title is "Update (Pre-release)".
- **Android:** turn on `Settings` → `General` → `Allow updating to beta versions`. `About` → `Check for updates` then shows "Channel: stable and pre-releases". See [the in-app updater](@/android/installation.md#updater).

To go back, turn the option off. The updater only offers versions newer than the one you run, so you stay on the beta until a newer stable release comes out, unless you install a stable release yourself.

## Old versions {#old-versions}

- **Throne desktop older than 1.1.0.** Version 1.1.0 moved all data into one database file, `throne.db`. Older configurations are not converted. Add your subscriptions and profiles again, or try the community scripts in [#1202](https://github.com/throneproj/Throne/issues/1202). `Backup and Restore` exists since 1.1.3, so backups from 1.1.3 or later restore in newer versions.
- **Nekoray or NekoBox.** See [Coming from Nekoray / NekoBox](@/help/migrating.md).
- **Throne for Android older than 2.0.0.** Version 2.0.0 is signed with a new key, so it cannot be installed over an older version. Uninstall the old app first; 2.0.0 starts empty. Old Android backups (`throne_backup_*.json` files and WebDAV `.zip` files) cannot be restored, only `.thrbackup` files. Before you uninstall, note your subscription links. If you also use Throne desktop, you can move everything with a desktop backup instead. See [Installation & Upgrade](@/android/installation.md#upgrading).
