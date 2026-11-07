+++
title = "Permissions & Background"
description = "Which permissions Throne for Android needs, and how to keep the VPN running in the background."
weight = 20
toc = true
+++

| Permission | When Throne asks | If you deny it |
| --- | --- | --- |
| VPN connection | At the first connect in `VPN` mode | VPN mode cannot start |
| Notifications (Android 13+) | At the first start | The VPN works, but its notification is hidden |
| Battery optimization | At the first start | Android may stop the VPN in the background |
| Location | When you use Wi-Fi rules | Wi-Fi rules do not match |
| Installed apps | Not asked on most devices | App lists are empty |
| Camera | When you scan a QR code | The scanner closes. Use `Import from clipboard` or `Import from file`. |
| Install apps | At the first [in-app update](@/android/installation.md#updater) | Download updates in the browser instead |

You can change them later in Android's app settings for Throne.

## VPN permission {#vpn-permission}

The first time you connect in `VPN` mode, Android asks whether Throne may set up a VPN connection. Allow it. If you declined, tap the connect button to see the request again, or use [`Proxy only` mode](@/android/modes.md#proxy-only).

If another VPN app is set to Always-on VPN, Android refuses without asking. Turn off Always-on VPN for that app first.

Some Android TV and AOSP devices have no screen for this request, and Throne shows "VPN confirmation unavailable". Tap `Use proxy mode` to connect in `Proxy only` mode, or grant the permission once with adb (`Copy command` copies the command) and connect again:

```bash
adb shell appops set com.nb4a.throne ACTIVATE_VPN allow
```

On a TV, also see [VPN permission on TV](@/android/tv.md#vpn-consent).

## Notifications {#notifications}

While the VPN runs, Throne shows a notification with the profile name, the speed and buttons to stop it and to switch servers. On Android 13 and newer, Throne asks for the notification permission once, at the first start. Without it the VPN still works, but the notification and its buttons are hidden.

`Settings` → `Appearance` → `Notifications` shows whether notifications are allowed. Tap it to open Android's notification settings for Throne. To choose the buttons, see [Notification](@/android/widgets.md#notification).

## Battery optimization {#battery}

At the first start, Throne asks to run without battery optimization, so that the VPN stays connected with the screen off and the buttons of the notification, tile and widgets keep working. Tap `Allow` and confirm in the Android dialog.

`Settings` → `General` → `Battery optimization` shows the current state. It should say "Unrestricted". If it does not, tap it: allow Throne to run without battery optimization, or, for "Restricted by the system", open the battery settings in Android's app info for Throne and choose the option without restrictions (`Unrestricted` on Android 12 and newer).

## Manufacturer settings {#oem}

Some manufacturers add their own background limits, which can stop Throne even when battery optimization is off. `Settings` → `General` → `Background & auto-start` opens the manufacturer's screen on Xiaomi, Redmi, POCO, Huawei, Honor, OPPO, realme, vivo, Samsung, OnePlus and ASUS devices, and the page for your manufacturer on [dontkillmyapp.com](https://dontkillmyapp.com) on others. Allow Throne to start automatically and to run in the background.

## Always-on VPN {#always-on}

With Always-on VPN, Android starts Throne when the device starts and keeps it connected. It works only in `VPN` mode.

1. Open `Settings` → `General` → `Always-on VPN`. Throne opens Android's VPN settings.
2. Open the settings of Throne in the list (usually a gear icon) and turn on Always-on VPN.

Throne needs a selected profile. On Android 10 and newer, it shows the notification "Always-on VPN has no profile" when none is selected, and switches `Proxy only` mode to `VPN` when Android starts it. On Android 7 to 9, keep `Service mode` on `VPN`.

Android's option "Block connections without VPN" blocks all traffic while Throne is not connected. Throne for Android has no kill switch of its own, so this option comes closest. While it is on, apps have no internet when Throne is stopped or cannot connect, and Android ignores [Allow apps to bypass VPN](@/android/modes.md#allow-bypass).

## Auto connect {#auto-connect}

`Settings` → `General` → `Auto connect` (off by default) starts the selected profile after the device starts, once you unlock it, and after Throne is updated. It starts it even if it was not running before, despite what the setting's summary says. It uses the current `Service mode`, so in `VPN` mode allow the VPN request once before you rely on it.

In `VPN` mode, Always-on VPN is the better choice, because Android itself keeps the VPN running. `Auto connect` is for `Proxy only` mode. If starts at boot fail, check [Battery optimization](#battery).

## Location for Wi-Fi rules {#location}

Routing rules that match a Wi-Fi name (SSID) or access point (BSSID) need location access, because Android hides the connected Wi-Fi from apps without it. Throne never reads your location. The rules need:

- precise location access;
- `Allow all the time` on Android 10 and newer, because the VPN runs while Throne is closed;
- location turned on, on Android 9 and newer.

Throne asks when you save a rule with Wi-Fi conditions. If access is missing while Throne runs, the notification "Wi-Fi rules are inactive" appears; tap it to fix it. Other rules, including `Network type` and `Metered network`, work without location access.

## Installed apps {#installed-apps}

Per-app proxy and the `Apps` field of routing rules need the list of installed apps. Some systems, for example on Xiaomi devices, have a separate permission for it. If it is denied, the list is empty and Throne shows "Unable to read installed apps." Tap `Open system settings` and allow Throne to read the list of apps.
