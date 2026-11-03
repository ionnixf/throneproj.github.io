+++
title = "Android TV"
description = "Install Throne on Android TV boxes and sticks, control it with the remote, add profiles without a camera, and fix a missing VPN permission screen."
weight = 50
toc = true
+++

Throne for Android also runs on Android TV devices, such as TV boxes, TV sticks and smart TVs. It does not need a touchscreen or a camera, and you can control it with the remote. This page explains what is different from a phone.

## Install on a TV {#install-tv}

Download the APK from the [Downloads](@/downloads.md) page and install it yourself. After the installation, Throne appears in the list of apps on the TV home screen.

### Choose the APK {#tv-apk}

Many TV boxes and sticks run a 32-bit Android system, even when their processor is 64-bit. They need the `armeabi-v7a` APK. TVs with a 64-bit Android system need `arm64-v8a`.

To check your TV, connect to it with adb (see [Install with adb](#install-adb)) and run the command in [Choose the right APK](@/android/installation.md#choose-apk). Without adb, try `arm64-v8a` first. If Android refuses to install it, use `armeabi-v7a`.

### Install with a file manager {#install-file-manager}

1. Copy the APK to a USB drive and connect the drive to the TV, or download the APK on the TV.
2. Open the APK with a file manager app on the TV.
3. When Android asks, allow the file manager to install unknown apps.
4. Confirm the installation.

### Install with adb {#install-adb}

1. On the TV, turn on the developer options and USB debugging. On many devices, you open About in the TV settings and press OK on Build seven times. The debugging switch is then in the developer options. The names differ between devices.
2. On a computer in the same network, run `adb connect` with the IP address of the TV.
3. Allow the debugging request that appears on the TV.
4. Run `adb install` with the APK file.

```bash
adb connect 192.168.1.50
adb install Throne-2.0.0-armeabi-v7a.apk
```

Throne 2.0.0 cannot be installed over an older version. See [Upgrading](@/android/installation.md#upgrading).

## Use the remote {#navigation}

| Remote button | What it does |
| --- | --- |
| Left at the left edge of the screen | Opens the navigation drawer. Buttons, tabs and toolbar items to the left get the focus first. |
| Right while the drawer is open | Closes the drawer. |
| Down at the end of the list | Moves the focus to the connect button, and then to the connection bar at the bottom. |
| OK on a profile | Selects the profile. If Throne is connected, it switches to this profile. |
| OK on the selected profile | Starts or stops the connection. |
| Holding OK on a profile | Starts the multi-selection, so that you can act on several profiles at once. |
| Play/Pause | Starts or stops the connection while Throne is open. |

In right-to-left languages, the drawer is on the right side, so Left and Right swap.

While connected, the connection bar at the bottom shows "Connected, tap to check connection". Move the focus to it and press OK to test the connection.

### Switch servers {#switch-servers}

TV devices usually have no home-screen widgets or notification buttons. On a TV, the toolbar of the profile list has two extra buttons: `Previous server` and `Next server`.

- While Throne is connected, they switch the connection to the previous or next profile of the current group.
- While Throne is stopped, they only change the selected profile.

They follow the order of the profile list. After the last profile they start again at the first.

### Reorder profiles {#reorder}

You cannot drag profiles with a remote. Open the ⋮ menu of a profile and choose `Move up` or `Move down` instead. Since 2.0.5, these items appear only on Android TV devices; in 2.0.4 and older they exist on every device. They are hidden while a search filter is active. On the `Groups` screen, the ⋮ menu of a group has `Move up` and `Move down` too.

## Add profiles without a camera {#import}

A TV usually has no camera, and copying text to its clipboard is hard. Use one of these ways instead:

- **Type a subscription URL.** Open `Groups` → `New group`, set `Type` to `Subscription`, enter the `URL` and save. Then press `Update subscription` on the new group. A new group is not updated automatically.
- **Import a text file.** Save share links or a subscription URL in a text file on a USB drive. In the profile list, open `Add profile` → `Import from file` and choose the file.
- **Restore a backup file.** Create a `.thrbackup` backup on your phone or computer and copy it to a USB drive. Open the file from a file manager on the TV, and Throne offers to restore it. You can also use `Tools` → `Restore`. See [Backup, Updates & Migration](@/guides/backup.md#desktop-android).
- **Restore from WebDAV.** Upload a backup from your phone (`Tools` → `Back up to WebDAV`) or, since Throne desktop 1.4.0, from your computer (`Upload to WebDAV`). On the TV, enter the same server and folder in `Tools` → `WebDAV settings`, then use `Restore from WebDAV`. See [WebDAV backups](@/guides/backup.md#webdav).

### With adb {#import-adb}

If you use adb, you have two more ways. To open a `throne://` link or a share link in Throne, send it as a link:

```bash
adb shell am start -a android.intent.action.VIEW -d "throne://addsub/<base64>"
```

If a link contains `&`, put it in single quotes inside the double quotes.

On devices without a camera, `Scan QR code` is hidden in the app. To read a QR code from an image, open the scanner with adb:

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.ScannerActivity
```

Throne shows "No camera: choose an image with a QR code" and opens the image picker. Choose one or more images that contain QR codes.

## VPN permission on TV {#vpn-consent}

The first time you connect in VPN mode, Android asks you to allow the VPN. Select OK.

Some TV and AOSP builds have no screen for this question. Throne then shows "VPN confirmation unavailable". You can allow the VPN once with an adb command, or use `Proxy only` mode. See [VPN permission](@/android/permissions.md#vpn-permission).

In `Proxy only` mode, only apps that you point at the local proxy use Throne. If your TV has a proxy setting for its network connection, set it to host `127.0.0.1` and port `2080` (the `Proxy port`). Apps that follow the system proxy settings then use Throne. Remove this setting when you stop using Throne, because these apps cannot connect while the proxy is not running.

## Save and open files {#files}

Some TV devices have no system screen for choosing where to save a file. When you save a backup, export profiles or save logs on such a device, Throne saves the file to `Download/Throne` and shows the full path in a "No file picker" message. On Android 9 and older, the folder is `Android/data/com.nb4a.throne/files`.

Opening a file, for example with `Import from file` or `Restore`, needs a file picker. If Throne shows "Your device lacks an Android standard file selector, please install one, such as Material Files.", install a file manager app that provides one. To copy a file to the TV with adb, use `adb push`, for example:

```bash
adb push Throne-backup-20260925-120000.thrbackup /sdcard/Download/
```
