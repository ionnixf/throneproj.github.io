+++
title = "Installation & Upgrade"
description = "Choose the right APK, install Throne for Android, upgrade from 1.x or NekoBox, and keep the app up to date."
weight = 10
toc = true
+++

This page explains which APK to download, how to install it, how to move from an older version, and how updates work. Throne for Android needs Android 7.0 or newer. It runs on phones, tablets and Android TV devices.

## Choose the right APK {#choose-apk}

Each release has one APK per processor type (ABI). There is no universal APK that runs on every device, so pick the file that matches yours:

| APK | Typical devices |
| --- | --- |
| `Throne-<version>-arm64-v8a.apk` | Almost all phones and tablets from recent years |
| `Throne-<version>-armeabi-v7a.apk` | Older 32-bit phones, many Android TV boxes and sticks |
| `Throne-<version>-x86_64.apk` | Emulators, some Chromebooks and PCs |

There is no build for 32-bit x86 devices.

What counts is the Android system, not the processor. Many TV boxes have a 64-bit processor but run 32-bit Android, and they need `armeabi-v7a`.

To find the ABI of your device, open a device information app and look for the ABI or the instruction set. If you have a computer with adb, this command prints it:

```bash
adb shell getprop ro.product.cpu.abi
```

If you are not sure, try `arm64-v8a` first. If Android says the app is not compatible, or does not install it, use `armeabi-v7a`.

## Download and verify {#download}

Download the APK from the [Downloads](@/downloads.md) page or from the [GitHub releases](https://github.com/throneproj/ThroneForAndroid/releases) page. Release tags start with `v`, for example `v2.0.0`.

Each release also contains `SHA256SUMS`, the SHA-256 checksums of its APKs. Checking the checksum is optional. It confirms that the file was not changed or damaged during the download. Put the APK and `SHA256SUMS` in the same folder on a computer, then run the command for your system.

**Linux:**

```bash
sha256sum -c --ignore-missing SHA256SUMS
```

The output must show `OK` for your file, for example `Throne-2.0.0-arm64-v8a.apk: OK`.

**macOS:**

```bash
shasum -a 256 Throne-2.0.0-arm64-v8a.apk
```

**Windows:**

```powershell
Get-FileHash .\Throne-2.0.0-arm64-v8a.apk -Algorithm SHA256
```

On macOS and Windows, compare the printed checksum with the line for your file in `SHA256SUMS`. Upper and lower case do not matter.

## Install {#install}

If Throne for Android 1.x is installed, read [Upgrading from 1.x or NekoBox](#upgrading) first.

1. Open the downloaded APK, for example from your browser's downloads or from a file manager.
2. If Android blocks the installation, allow the app you opened the file with to install apps. On Android 8 and newer, tap `Settings` in the message and turn on `Allow from this source`. On Android 7, turn on `Unknown sources` in Android's security settings.
3. Go back and tap `Install`.
4. Open Throne and answer the first-start questions. See [Permissions & Background](@/android/permissions.md).

For Android TV, see [Android TV](@/android/tv.md#install-tv).

## Upgrading from 1.x or NekoBox {#upgrading}

Throne for Android 2.0.0 is rebuilt on the desktop core. It is signed with a new key and uses a new data format:

- Android cannot install 2.0.0 over Throne for Android 1.x. The installation fails until you uninstall the old version.
- 2.0.0 starts with no profiles, groups or settings. It cannot read the data of the old app.
- It cannot restore old backups: `throne_backup_….json` files and the `.zip` files on WebDAV. It restores only `.thrbackup` files, the backup format of Throne desktop.

{% alert_warning() %}
Uninstalling the old app deletes all its data: profiles, groups, routing rules and settings. Save what you need before you uninstall it.
{% end %}

1. In the old app, copy the URL of each subscription and keep it in a safe place, for example a private note. To find it, open `Group` in the drawer, tap the edit button (pencil icon) of the group and copy `Subscription Link`. Do not use `Share Subscription`: it copies an `sn://` link that 2.0.0 cannot read.
2. Export the profiles that do not come from a subscription: open the menu of the group and choose `Export` → `Export to file`. The file contains the profiles as share links. For some types, such as WireGuard, SSH, ShadowTLS, Mieru, chain and custom-config profiles, the old app writes `sn://` links, which 2.0.0 cannot import. Note the settings of these profiles, so that you can create them again.
3. If you use Throne desktop with the same profiles, you can instead create a backup there (`Settings` → `Basic Settings` → `Backup and Restore`) and restore it on the phone later. See [Moving between desktop and Android](@/guides/backup.md#desktop-android).
4. Uninstall the old app.
5. Install 2.0.0 as described in [Install](#install).
6. Add your subscriptions again (`Add profile` → `Import from clipboard` → `Create new subscription group`), import the exported file (`Add profile` → `Import from file`), or restore the desktop backup (drawer → `Tools` → `Restore`).

**NekoBox for Android:** the original NekoBox app has a different package name, so Android installs Throne next to it. Throne cannot read the data or backups of NekoBox. Move your subscriptions by their links. Only one VPN app can be connected at a time.

## Stable and preview builds {#preview}

Stable releases are published on GitHub as normal releases, for example `v2.0.2`. Preview builds contain the newest changes and are meant for testing, so they can have bugs.

Until 2.0.0, preview builds were published as GitHub pre-releases with tags like `v<version>-pre.<number>`. Since 2.0.1 they are no longer published on the releases page. The developers build a preview by hand when they need testers. It is attached to the GitHub Actions run that built it for 14 days, and downloading it needs a GitHub account.

These newer preview builds have their own package name, `com.nb4a.throne.debug`. Android installs a preview next to the stable app as a second app, with its own profiles and settings. The in-app updater does not offer previews, and a preview does not update itself. In adb commands and automation apps, use `com.nb4a.throne.debug` instead of `com.nb4a.throne` for a preview.

A preview build shows a warning when the app opens: "This application is a preview version and may contain many problems. If you do not want to test it, please go to GitHub to download the release version!" The warning appears again at every start until you tap `Don't show again`. After an update to a newer preview, it appears again.

## In-app updater {#updater}

The builds from GitHub can update themselves:

1. Open `About` in the drawer and tap `Check for updates`. The line below it shows the channel: "Channel: stable releases" or "Channel: stable and pre-releases".
2. If a newer version exists, Throne shows "New version available" with the version, the download size and the release notes. Tap `Update`.
3. On Android 8 and newer, Throne asks "Allow installing updates" the first time. Tap `Open settings`, allow Throne to install apps, then go back.
4. Throne downloads and checks the APK. Then Android asks you to confirm the installation.

The connection stops while the app is replaced and starts again after the update.

Since 2.0.4, the release notes show the changes of every version after yours, up to the new one, taken from the changelog of the app. When the new version is a stable release, its pre-releases are not listed separately. If Throne cannot load the changelog, it shows the notes of the GitHub release instead. Links in the notes open in the browser.

The update settings are in `Settings` → `General`, in the `Updates` group:

| Setting | Default | What it does |
| --- | --- | --- |
| `Allow updating to beta versions` | Off | Also offers pre-releases from the GitHub releases page. Newer preview builds are not published there. See [Stable and preview builds](#preview). |
| `Check for updates daily` | Off | Checks once a day and shows the notification "Throne … is available". The notification has a `Skip this version` button. |

Before anything is installed, Throne checks the download:

- its size and SHA-256 checksum, as listed in the update manifest of the release (`throne-update.json`);
- that it is Throne and newer than the installed version;
- that it is signed with the same key as the installed app, and with the Throne release key.

If a check fails, nothing is installed. If your copy was not signed with the Throne release key, for example a build from another source, the updater says "The download is signed with a different key than this build, so Android cannot install it as an update. Back up, uninstall this build and install the release from GitHub."

The updater also works like this:

- It installs the APK for the ABI you installed, for example `arm64-v8a`. It never switches to another ABI.
- Some releases cannot be installed from the app: releases without an update manifest, and releases without an APK for your ABI. Throne then says "This update cannot be installed from the app (…). Download it from the release page." and offers `Open in browser`.
- When `Use proxy` is on (`Settings` → `Subscriptions`) or the service mode is `Proxy only`, and Throne is connected, the check and the download go through Throne's local proxy. When no profile runs, they connect directly. With `Disable mixed inbound` on, they do not use the local proxy.
- TLS certificate errors are never ignored for updates, even when `Ignore TLS errors` is on.

## F-Droid build {#fdroid}

The source code has a second build variant, `fdroid`, meant for F-Droid. It has no in-app updater: `About` → `Check for updates` and the `Updates` settings are missing, and the app does not ask for permission to install apps. Throne for Android is not on F-Droid yet. A listing was requested in [#37](https://github.com/throneproj/ThroneForAndroid/issues/37).

Android installs an update only when it is signed with the same key as the installed app. So a build signed with another key and a build from GitHub cannot update each other. To switch, back up your data (drawer → `Tools` → `Create backup`), uninstall the app, install the other build and restore the backup.
