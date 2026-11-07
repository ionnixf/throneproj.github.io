+++
title = "Widgets, Tile & Automation"
description = "Control Throne for Android from home-screen widgets, the notification, the Quick Settings tile, shortcuts and automation apps such as Tasker."
weight = 40
toc = true
+++

## Widgets {#widgets}

| Widget | What it does |
| --- | --- |
| `Toggle` (1×1) | Shows the connection state; tap it to start or stop. Made wider, it also shows the profile name. |
| `Status` (4×1) | Shows the profile, its group and the state, or the proxy speed while connected. Tap the middle to start or stop, and the arrows to switch to the previous or next profile. |

To add one, touch and hold an empty area of the home screen, open your launcher's widget list and drag `Toggle` or `Status` to the home screen.

The arrows move through the group of the current profile, in the order of the profile list. While Throne is connected, they switch the connection; while it is stopped, they only change the selected profile.

## Notification {#notification}

While Throne runs, it shows a notification with the profile name and the current speed. Tap it to open Throne. Its options are in `Settings` → `Appearance`: `Show group name in notification`, `Show direct speed` (on by default) and `Notification buttons`.

You can choose up to three buttons. The defaults are `Stop`, `Next` and `Switch…`.

| Button | What it does |
| --- | --- |
| `Stop` | Stops the connection. |
| `Previous`, `Next` | Switches to the previous or next profile in the current group, like the arrows of the `Status` widget. |
| `Switch…` | Opens a list of your profiles to switch to. |
| `Reset connections` | Closes all open connections, so that apps connect again. |

If Throne is connected when you change the buttons, tap `Apply` when Throne shows "Reload proxy service to apply changes". If you do not see the notification, see [Notifications](@/android/permissions.md#notifications).

## Quick Settings tile {#quick-settings-tile}

To add the `Throne` tile, open Quick Settings, tap the edit button (a pencil on most phones) and drag the tile to your active tiles. Tap the tile to start or stop the connection; on a locked phone, Android asks you to unlock first. Touch and hold it to open Throne.

## Shortcuts {#shortcuts}

On Android 7.1 and newer, touch and hold the Throne icon for the `Toggle`, `Enable`, `Disable` and `Scan QR code` shortcuts. You can drag them to the home screen. They appear after you have opened Throne once.

To put one profile on the home screen (Android 8.0 and newer):

1. Open the profile with the `Edit` button of its row, or with ⋮ → `Edit` in the double-column layout and in compact rows.
2. In the profile editor, tap ⋮ → `Create shortcut` and confirm in your launcher's dialog.

The shortcut starts Throne with this profile, switches to it if another profile is running, or stops Throne if this profile is running.

## Automation {#automation}

Automation apps such as Tasker or MacroDroid can start these activities of Throne (package `com.nb4a.throne`). Enter the full class name: a short form such as `.QuickToggleShortcut` does not work.

| Class | What it does |
| --- | --- |
| `io.nekohasekai.sagernet.QuickToggleShortcut` | Starts the selected profile, or stops the connection if it is running. |
| `io.nekohasekai.sagernet.ui.QuickEnableShortcut` | Starts the selected profile if Throne is stopped. |
| `io.nekohasekai.sagernet.ui.QuickDisableShortcut` | Stops the connection if it is running. |
| `io.nekohasekai.sagernet.ui.ScannerActivity` | Opens the QR code scanner, or the image picker on devices without a camera. |

In the automation app, use the action that starts an activity with an intent, often called "Send Intent" (target Activity) or "Launch activity". Set the package and the class, and leave the action empty or set `android.intent.action.MAIN`.

`QuickToggleShortcut` accepts an optional extra `profile` with a profile ID, which makes it work like a [profile shortcut](#shortcuts) for that profile. The extra must have the type long; sent as an integer or text, it is ignored. No screen of the app lists profile IDs, so for a single profile a profile shortcut is easier.

To test from a computer with adb (`--el` sends a long extra; replace `12` with the profile ID):

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.QuickEnableShortcut
adb shell am start -a android.intent.action.MAIN -n com.nb4a.throne/io.nekohasekai.sagernet.QuickToggleShortcut --el profile 12
```

- Connect once from the Throne app first, so that Android has granted the VPN permission.
- If nothing happens, the automation app may need permission to start activities from the background; on many phones this is the permission to display over other apps.
- To connect when the phone starts, use [Auto connect](@/android/permissions.md#auto-connect) or the always-on VPN instead.
- Automation apps can also open `throne://` links, for example to add a subscription. See [Deep Links](@/advanced/deeplinks.md#android).
- Throne's internal broadcasts, such as `io.nekohasekai.sagernet.SWITCH_NEXT`, are protected by a signature permission, so other apps cannot send them.
