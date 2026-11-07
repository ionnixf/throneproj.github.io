+++
title = "Installation & Upgrade"
description = "Choose the right APK, install Throne for Android, upgrade from 1.x or NekoBox, and keep it up to date."
weight = 10
toc = true
+++

Throne for Android needs Android 7.0 or newer. It runs on phones, tablets and Android TV.

## Choose the right APK {#choose-apk}

Each release has one APK per processor type (ABI):

| APK | Typical devices |
| --- | --- |
| `Throne-<version>-arm64-v8a.apk` | Almost all phones and tablets from recent years |
| `Throne-<version>-armeabi-v7a.apk` | Older 32-bit phones, many Android TV boxes and sticks |
| `Throne-<version>-x86_64.apk` | Emulators, some Chromebooks and PCs |

What counts is the Android system, not the processor: many TV boxes have a 64-bit processor but run 32-bit Android, so they need `armeabi-v7a`. With adb, this command prints the ABI:

```bash
adb shell getprop ro.product.cpu.abi
```

If you are not sure, try `arm64-v8a`. If Android does not install it, use `armeabi-v7a`.

## Download and verify {#download}

Download the APK from [Downloads](@/downloads.md) or from the [GitHub releases](https://github.com/throneproj/ThroneForAndroid/releases). To check that the file is intact (optional), put it in one folder with the release's `SHA256SUMS` file and run:

```bash
sha256sum -c --ignore-missing SHA256SUMS
```

It must print `OK` for your file. On macOS, run `shasum -a 256 Throne-2.0.6-arm64-v8a.apk`, on Windows `Get-FileHash .\Throne-2.0.6-arm64-v8a.apk -Algorithm SHA256`, and compare the checksum with the line for your file in `SHA256SUMS`.

## Install {#install}

If Throne for Android 1.x is installed, read [Upgrading from 1.x or NekoBox](#upgrading) first.

1. Open the downloaded APK.
2. If Android blocks the installation, tap `Settings` in the message and turn on `Allow from this source`. On Android 7, turn on `Unknown sources` in Android's security settings instead.
3. Go back and tap `Install`.
4. Open Throne and answer the first-start questions. See [Permissions & Background](@/android/permissions.md).

For Android TV, see [Android TV](@/android/tv.md#install-tv).

## Upgrading from 1.x or NekoBox {#upgrading}

Throne for Android 2.0.0 and later are signed with a new key and use a new data format. Android cannot install them over 1.x, they start with no profiles, groups or settings, and they cannot restore old backups (`throne_backup_….json` and WebDAV `.zip` files), only `.thrbackup` files.

{% alert_warning() %}
Uninstalling the old app deletes all its profiles, groups, routing rules and settings. Save what you need first.
{% end %}

1. In the old app, copy the URL of each subscription: drawer → `Group`, tap the edit button (pencil) of the group and copy `Subscription Link`. Do not use `Share Subscription`: it copies an `sn://` link that the new app cannot read.
2. Export the profiles that do not come from a subscription: the group's menu → `Export` → `Export to file`. WireGuard, SSH, ShadowTLS, Mieru, chain and custom-config profiles are exported as `sn://` links, which cannot be imported, so note their settings.
3. If you use Throne desktop with the same profiles, you can instead create a backup there and restore it on the phone. See [Moving between desktop and Android](@/guides/backup.md#desktop-android).
4. Uninstall the old app and [install](#install) the new one.
5. Add your subscriptions again (`Add profile` → `Import from clipboard` → `Create new subscription group`), import the exported file (`Add profile` → `Import from file`), or restore the desktop backup (drawer → `Tools` → `Restore`).

The original **NekoBox for Android** has a different package name, so Throne installs next to it. Throne cannot read its data or backups: move your subscriptions by their links.

## In-app updater {#updater}

The builds from GitHub update themselves:

1. Open `About` in the drawer and tap `Check for updates`.
2. If a newer version exists, tap `Update`.
3. The first time, Throne asks for permission to install apps. Tap `Open settings`, allow it and go back.
4. Throne downloads and checks the APK. Confirm the installation when Android asks.

The connection stops while the app is replaced and starts again afterwards.

In `Settings` → `General` → `Updates`, `Check for updates daily` notifies you of new versions, and `Allow updating to beta versions` also offers pre-releases from the GitHub releases page. Both are off by default.

Android installs an update only when it is signed with the same key as the installed app. A build from another source and a GitHub build therefore cannot update each other. To switch, create a backup (drawer → `Tools` → `Create backup`), uninstall the app, install the other build and restore the backup. For updater errors, see [Update problems](@/android/troubleshooting.md#updates).

Throne for Android is not on F-Droid yet ([#37](https://github.com/throneproj/ThroneForAndroid/issues/37)).

## Preview builds {#preview}

Preview builds have the newest changes and are meant for testing, so they can have bugs. The developers build one by hand when they need testers. It is attached to its GitHub Actions run for 14 days, and downloading it needs a GitHub account.

A preview has its own package name, `com.nb4a.throne.debug`, so it installs next to the stable app with its own profiles and settings. It does not update itself, and the in-app updater does not offer it. In adb commands and automation apps, use `com.nb4a.throne.debug` instead of `com.nb4a.throne`.
