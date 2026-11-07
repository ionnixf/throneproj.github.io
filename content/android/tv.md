+++
title = "Android TV"
description = "Install and use Throne for Android on TV boxes, sticks and smart TVs."
weight = 50
toc = true
+++

## Install on a TV {#install-tv}

Many TV boxes and sticks need the `armeabi-v7a` APK, even with a 64-bit processor. See [Choose the right APK](@/android/installation.md#choose-apk). If Throne for Android 1.x is installed, read [Upgrading from 1.x or NekoBox](@/android/installation.md#upgrading) first.

- **File manager:** copy the APK to a USB drive (or download it on the TV), open it in a file manager app and allow that app to install unknown apps.
- **adb:** on the TV, turn on USB debugging in the developer options (on many TVs, press OK seven times on Build in About to show them). On a computer in the same network, run these commands and allow the debugging request on the TV:

```bash
adb connect 192.168.1.50
adb install Throne-2.0.6-armeabi-v7a.apk
```

## Use the remote {#navigation}

| Button | What it does |
| --- | --- |
| Left at the left edge | Opens the navigation drawer (Right in right-to-left languages). |
| Down at the end of the list | Moves the focus to the connect button, then to the connection bar. OK on the bar tests the connection. |
| OK on a profile | Selects the profile, or switches to it if Throne is connected. OK on the selected profile starts or stops the connection. |
| Play/Pause | Starts or stops the connection while Throne is open. |

The toolbar of the profile list has `Previous server` and `Next server` buttons. While Throne is connected, they switch the connection to the previous or next profile of the current group; while it is stopped, they only change the selected profile.

To reorder profiles or groups, use `Move up` and `Move down` in their ⋮ menu. They are hidden while a search filter is active.

## Add profiles without a camera {#import}

- **Subscription URL:** open `Groups` → `New group`, set `Type` to `Subscription`, enter the `URL` and save. Then press `Update subscription` on the group, because a new group is not updated by itself.
- **Text file:** save share links or a subscription URL in a text file on a USB drive, then use `Add profile` → `Import from file`.
- **Backup:** open a `.thrbackup` file from your phone or computer in a file manager on the TV, or use `Tools` → `Restore`. See [Move between desktop and Android](@/guides/backup.md#desktop-android).
- **WebDAV:** upload a backup from your phone (`Tools` → `Back up to WebDAV`) or computer (`Upload to WebDAV`), enter the same server and folder in `Tools` → `WebDAV settings` on the TV and use `Restore from WebDAV`. See [WebDAV backups](@/guides/backup.md#webdav).

With adb, you can also open a `throne://` or share link in Throne. If the link contains `&`, put it in single quotes inside the double quotes.

```bash
adb shell am start -a android.intent.action.VIEW -d "throne://addsub/<base64>"
```

`Scan QR code` is hidden on devices without a camera. To read QR codes from images, open the scanner with adb; it opens the image picker:

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.ScannerActivity
```

## VPN permission on TV {#vpn-consent}

Some TV and AOSP builds have no screen for the VPN request, and Throne shows "VPN confirmation unavailable" when you connect. Grant the permission once with adb or use `Proxy only` mode. See [VPN permission](@/android/permissions.md#vpn-permission).

In `Proxy only` mode, apps that follow the system proxy use Throne if you set the proxy of the TV's network connection to host `127.0.0.1` and port `2080` (the `Proxy port`). Remove it when you stop using Throne, or these apps cannot connect.

## Save and open files {#files}

On TVs without a screen for choosing where to save a file, Throne saves backups, exported profiles and logs to `Download/Throne` (on Android 9 and older, `Android/data/com.nb4a.throne/files`) and shows the full path.

Opening a file needs a file picker. If Throne says that your device lacks an Android standard file selector, install a file manager that provides one, such as Material Files. To copy a file to the TV with adb:

```bash
adb push Throne-backup-20260925-120000.thrbackup /sdcard/Download/
```
