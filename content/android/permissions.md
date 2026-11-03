+++
title = "Permissions & Background"
description = "Which permissions Throne for Android asks for, what happens if you deny them, and how to keep the VPN running in the background."
weight = 20
toc = true
+++

Throne asks for a few Android permissions. This page explains why it needs each one, what happens if you deny it, and where to change it later. It also explains how to stop Android from closing Throne in the background.

| Permission | When Throne asks | If you deny it |
| --- | --- | --- |
| VPN connection | At the first connect in `VPN` mode | VPN mode cannot start |
| Notifications (Android 13+) | At the first start | The VPN works, but its notification is hidden |
| Battery optimization | At the first start | Android may stop the VPN in the background |
| Location | When you use Wi-Fi rules | Wi-Fi rules do not match |
| Installed apps | Not asked on most devices | App lists are empty |
| Camera | When you scan a QR code | The scanner closes |
| Install apps | At the first in-app update | Download updates in the browser instead |

You can change most of them later in Android's app settings for Throne. The permission to install apps is explained in [In-app updater](@/android/installation.md#updater).

## VPN permission {#vpn-permission}

In `VPN` mode, the default `Service mode`, Throne uses Android's VPN service to send the traffic of your apps through the selected profile.

The first time you tap the connect button, Android asks whether Throne may set up a VPN connection. Allow it. Android may ask again after you used another VPN app. If a widget or the Quick Settings tile starts Throne before you allowed it, Throne opens the request. When the screen is locked, the request appears after you unlock it.

If you decline, Throne shows "Permission denied to create a VPN service". Tap the connect button again to see the request again. If you do not want a VPN, use `Proxy only` mode instead. See [VPN & Proxy Modes](@/android/modes.md#proxy-only).

Android runs only one VPN at a time. Starting Throne disconnects another VPN app. If another VPN app is set to Always-on VPN in Android's settings, Android does not show the request, and Throne reports that the permission was denied. Turn off Always-on VPN for that app first.

Some Android TV and AOSP devices have no screen for this request. Throne then shows "VPN confirmation unavailable" with three buttons:

- `Use proxy mode` switches the service mode to `Proxy only` and connects.
- `Copy command` copies the adb command that grants the permission.
- `Cancel` closes the message.

To grant the permission, connect the device to a computer with adb, run this command once, then connect again in Throne:

```bash
adb shell appops set com.nb4a.throne ACTIVATE_VPN allow
```

On a TV, `Proxy only` mode can also work with the TV's own proxy setting. See [Android TV](@/android/tv.md#vpn-consent).

## Notifications {#notifications}

While the VPN runs, Throne shows a notification with the profile name, the speed, and buttons to stop the VPN and to switch servers. On Android 13 and newer, apps need a permission to show notifications. Throne asks for it once, at the first start. It does not ask on Android TV.

If the permission was already denied at that time, Throne first explains: "Throne shows a notification while the VPN runs, with buttons to stop it and to switch servers. Allow notifications to see it." Tap `Continue` to see Android's request, or `Not now`. Throne does not ask again later.

Without the permission, the VPN still works, but the notification and its buttons are hidden. To check or change it later, open `Settings` → `Appearance` → `Notifications`. It shows "Allowed", or "Blocked: the service notification and its buttons are hidden. Tap to allow." Tap it to open Android's notification settings for Throne.

To choose the buttons of the notification, see [Widgets, Tile & Automation](@/android/widgets.md#notification).

## Battery optimization {#battery}

After the notification question, Throne asks once: "Keep Throne running". The message explains: "Android may stop or delay apps in the background to save battery. Let Throne run without battery optimization so the VPN stays connected with the screen off and switching servers from the notification, tile or widget keeps working."

Tap `Allow` and confirm in the Android dialog. If you tap `Not now`, Throne reminds you: "You can allow this later in Settings › General › Battery optimization".

Since 2.0.5, Throne asks the first-start questions again after a reinstall, also when Android restored the app's data from its backup.

`Settings` → `General` → `Battery optimization` shows the current state:

| State | Meaning |
| --- | --- |
| "Unrestricted" | Android does not limit Throne. This is what you want. |
| "Optimized: Android may stop the VPN in the background" | Android can pause or stop Throne to save battery. |
| "Restricted by the system: background starts are blocked" | Android blocks Throne in the background, and starts from the notification, a widget or at boot can fail. |

Tap the item to open the right Android screen. For "Optimized", allow Throne to run without battery optimization. For "Restricted", Throne opens Android's app info for Throne. Open its battery settings and choose the option without restrictions. On Android 12 and newer, this option is called `Unrestricted`.

On devices without these screens, such as many TVs, Throne shows "This device has no battery optimization settings".

## Manufacturer settings {#oem}

Some manufacturers add their own background limits on top of Android's. They can stop Throne even when battery optimization is off.

`Settings` → `General` → `Background & auto-start` ("Device-specific settings that can stop Throne in the background") opens the manufacturer's screen on these devices:

- Xiaomi, Redmi and POCO
- Huawei and Honor
- OPPO and realme
- vivo
- Samsung
- OnePlus
- ASUS

On other devices, or when that screen cannot be opened, it opens the page for your manufacturer on [dontkillmyapp.com](https://dontkillmyapp.com). There, allow Throne to start automatically and to run in the background. The names of these options differ between manufacturers and system versions. dontkillmyapp.com has step-by-step guides for many devices.

## Always-on VPN {#always-on}

Always-on VPN is an Android feature. Android starts the VPN app by itself when the device starts and tries to keep it connected. It works only in `VPN` mode.

1. Open `Settings` → `General` → `Always-on VPN`. Throne opens Android's VPN settings.
2. Open the settings of Throne in the list (usually a gear icon).
3. Turn on Always-on VPN.

The item in Throne shows the current state: "Off. Set it in the system VPN settings", "On: Android starts Throne in VPN mode by itself" or "On, blocking connections without VPN". On Android 9 and older, Throne cannot read the state and shows "Set it in the system VPN settings".

Throne needs a selected profile. On Android 10 and newer:

- Without a selected profile, Throne shows the notification "Always-on VPN has no profile": "Select a profile in Throne; until then the always-on VPN cannot connect."
- When Android starts Throne while the service mode is `Proxy only`, Throne switches it to `VPN`.

On Android 7 to 9, Throne does neither: it shows no notification without a profile, and in `Proxy only` mode the always-on start does not connect. Keep `Service mode` on `VPN` there.

Android's option "Block connections without VPN" blocks all network traffic while Throne is not connected. Throne for Android has no kill switch of its own, unlike [Throne desktop](@/advanced/kill_switch.md), so this option is the closest thing on Android. While it is on, apps have no internet when Throne is stopped or cannot connect, and Android ignores [Allow apps to bypass VPN](@/android/modes.md#allow-bypass). To turn it off, open Android's VPN settings again.

## Auto connect {#auto-connect}

`Settings` → `General` → `Auto connect` is off by default. When it is on, Throne starts the selected profile:

- after the device starts, once you unlock it for the first time;
- after Throne is updated.

It uses the current `Service mode` and needs a selected profile. In `VPN` mode, allow the VPN request once before you rely on it. The setting's summary says "if it was running before", but Throne 2.0.0 starts the selected profile even if it was not connected before.

When Always-on VPN is on, `Auto connect` shows "Superseded by the always-on VPN". In `VPN` mode, Always-on VPN is the better choice because Android itself keeps the VPN running. `Auto connect` is useful in `Proxy only` mode, where Always-on VPN does not apply. If starts at boot fail, check [Battery optimization](#battery).

## Location for Wi-Fi rules {#location}

Throne needs location access only for routing rules that match a Wi-Fi name (SSID) or access point (BSSID). As the app explains: "Android hides the connected Wi-Fi from apps without it. Throne only reads the connected network, never your location."

Wi-Fi rules need:

- precise location access;
- location access "all the time" on Android 10 and newer, because the VPN runs while Throne is closed. Choose `Allow all the time` when Android asks;
- location turned on, on Android 9 and newer. When it is off, Throne offers a `Location settings` button.

Throne asks when you save a rule with Wi-Fi conditions, and once when you connect with a routing profile that has such rules. If access is missing while Throne runs, the notification "Wi-Fi rules are inactive" appears. Tap it to fix it. If you denied the request permanently, Throne says "Location access is denied. Allow it in the app settings for Wi-Fi rules to match." and offers `Open system settings`.

Other rules work without location access. This includes the conditions `Network type` and `Metered network` (since 2.0.2): they check the kind of the current network, such as Wi-Fi or mobile data, not which network it is. For routing profiles and rules, see [Routing](@/guides/routing.md#profiles) and [Routing on Android](@/guides/routing.md#android).

## Installed apps {#installed-apps}

Throne reads the list of installed apps for per-app proxy (`Settings` → `TUN / VPN` → `Apps VPN mode`) and for the `Apps` field of routing rules. Most devices do not ask for this.

Some systems, for example on Xiaomi devices, have a separate permission for it. If it is denied, the list is empty and Throne shows "Unable to read installed apps." Tap `Open system settings` and allow Throne to read the list of apps.

## Camera {#camera}

Throne uses the camera only to scan QR codes. If you deny it, the scanner closes. Allow the camera in Android's app settings for Throne, or import the link with `Import from clipboard` or `Import from file`. On devices without a camera, `Scan QR code` is hidden. For other ways to add profiles there, see [Android TV](@/android/tv.md).
