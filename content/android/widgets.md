+++
title = "Widgets, Tile & Automation"
description = "Control Throne for Android from home-screen widgets, the notification, the Quick Settings tile, launcher shortcuts and automation apps such as Tasker."
weight = 40
toc = true
+++

You can start, stop and switch Throne without opening the app. This page covers the home-screen widgets, the notification, the Quick Settings tile, launcher shortcuts, and automation apps such as Tasker or MacroDroid.

## Widgets {#widgets}

Throne has two home-screen widgets:

| Widget | Default size | What it shows | What you can do |
| --- | --- | --- | --- |
| `Toggle` | 1×1 | The connection state. When you make the widget wider, it also shows the profile name. | Tap it to start or stop the connection. |
| `Status` | 4×1 | The profile name, its group and the state. While connected, the proxy upload and download speed replaces the state. | Tap the middle to start or stop the connection. Tap the arrows to switch to the previous or next profile. |

To add a widget:

1. Touch and hold an empty area of the home screen.
2. Open the widget list of your launcher.
3. Find Throne and drag `Toggle` or `Status` to the home screen.

The arrows of the `Status` widget move through the group of the current profile, in the order of the profile list. After the last profile they start again at the first. They are hidden when the group has only one profile. While Throne is connected, an arrow switches the connection to the other profile. While Throne is stopped, an arrow only changes the selected profile.

The speed on the `Status` widget updates at most every 3 seconds, and only while the screen is on. If no profile is selected, the widgets show "No profile" instead of a profile name.

## Notification {#notification}

While Throne runs, it shows a notification. Tap the notification to open Throne.

- The title is the profile name. With `Show group name in notification` on, the title is `[group] profile`.
- The text shows the current speed. With `Show direct speed` on (the default), it shows two lines, `Proxy: …↑ …↓` and `Direct: …↑ …↓`. With it off, it shows only the proxy speed.
- The small text shows the proxy traffic since the connection started.

These options are in `Settings` → `Appearance`, in the Notification section.

### Notification buttons {#notification-buttons}

Choose the buttons in `Settings` → `Appearance` → `Notification buttons`. You can choose up to 3 buttons. The default buttons are `Stop`, `Next` and `Switch…`. The buttons always appear in the order of this table:

| Button | What it does |
| --- | --- |
| `Stop` | Stops the connection. |
| `Previous` | Switches to the previous profile in the current group. |
| `Next` | Switches to the next profile in the current group. |
| `Switch…` | Opens a list of your profiles. Choose one to switch to it. |
| `Reset connections` | Closes all open connections, so that apps connect again. |

`Previous` and `Next` move through the group in the same way as the arrows of the `Status` widget (see [Widgets](#widgets)).

If Throne is connected when you change the buttons, tap `Apply` when Throne shows "Reload proxy service to apply changes".

If you do not see the notification, notifications may be blocked for Throne. See [Notifications](@/android/permissions.md#notifications).

## Quick Settings tile {#quick-settings-tile}

The `Throne` tile starts and stops Throne from Quick Settings. Before 2.0.2 it was called `Switcher`. To add it:

1. Swipe down from the top of the screen to open Quick Settings.
2. Tap the edit button. On most phones it is a pencil icon.
3. Drag the `Throne` tile to your active tiles.

How the tile works:

- Tap the tile to start or stop the connection. If the phone is locked, Android asks you to unlock it first.
- While connected, the tile shows the profile name. Otherwise it shows "Throne".
- Touch and hold the tile to open Throne.

## Shortcuts {#shortcuts}

### Launcher shortcuts {#launcher-shortcuts}

On Android 7.1 and later, touch and hold the Throne icon to see these shortcuts. You can drag a shortcut to the home screen. The shortcuts appear after you open Throne for the first time.

| Shortcut | What it does |
| --- | --- |
| `Toggle` | Starts the selected profile if Throne is stopped. Stops the connection if it is running. |
| `Enable` | Starts the selected profile if Throne is stopped. |
| `Disable` | Stops the connection if it is running. |
| `Scan QR code` | Opens the QR code scanner. It appears only on devices with a camera. |

### Profile shortcuts {#profile-shortcuts}

On Android 8.0 and later, you can put a shortcut for one profile on the home screen:

1. Open the profile with the `Edit` button on its row. This button is disabled while the profile runs. In the `Double column` layout and, since 2.0.5, with `Compact profile rows`, the row has no `Edit` button: use ⋮ → `Edit` on the row. See [Profile list layout](@/android/_index.md#layout).
2. In the profile editor, tap ⋮ → `Create shortcut`.
3. Confirm in the dialog of your launcher.

The shortcut has the name of the profile. When you tap it:

- If Throne is stopped, it starts with this profile.
- If another profile is running, Throne switches to this profile.
- If this profile is running, Throne stops.

`Create shortcut` is available only for profiles that are already saved.

## Automation {#automation}

Automation apps such as Tasker or MacroDroid can start the shortcut activities of Throne. The package name of Throne is `com.nb4a.throne`.

| Class | What it does |
| --- | --- |
| `io.nekohasekai.sagernet.QuickToggleShortcut` | Starts the selected profile if Throne is stopped. Stops the connection if it is running. Accepts the `profile` extra (see below). |
| `io.nekohasekai.sagernet.ui.QuickEnableShortcut` | Starts the selected profile if Throne is stopped. |
| `io.nekohasekai.sagernet.ui.QuickDisableShortcut` | Stops the connection if it is running. |
| `io.nekohasekai.sagernet.ui.ScannerActivity` | Opens the QR code scanner. On devices without a camera, it opens the image picker instead. |

The first three activities show nothing and close at once. Always enter the full class name as shown. The class names do not start with the package name, so a short form such as `.QuickToggleShortcut` does not work.

`QuickToggleShortcut` accepts an optional extra named `profile` with the ID of a profile. With this extra, it works like a [profile shortcut](#profile-shortcuts) for that profile: it starts it, switches to it, or stops it.

The extra must have the type long (a 64-bit number). If your automation app sends it as a normal integer or as text, Throne ignores it and toggles the connection as if there were no extra.

The profile ID is a number that Throne gives each profile internally. No screen of the app lists it; it appears only in a few messages, such as "Missing server (#…)". If you only want to switch to one profile from the home screen, use a profile shortcut instead.

### Example {#automation-example}

In your automation app, add the action that starts an activity with an intent. It is often called "Send Intent" (with the target set to Activity) or "Launch activity". Fill in these fields:

| Field | Value |
| --- | --- |
| Target | Activity |
| Package | `com.nb4a.throne` |
| Class | `io.nekohasekai.sagernet.QuickToggleShortcut` |
| Action | `android.intent.action.MAIN`, or leave it empty |
| Extra (optional) | Name `profile`, type long, value: the profile ID |

You can send the same intents from a computer with adb, for example to test them:

```bash
adb shell am start -n com.nb4a.throne/io.nekohasekai.sagernet.ui.QuickEnableShortcut
adb shell am start -a android.intent.action.MAIN -n com.nb4a.throne/io.nekohasekai.sagernet.QuickToggleShortcut --el profile 12
```

`--el` sends a long extra. Replace `12` with the ID of your profile.

Tips:

- Connect once from the Throne app before you automate it, so that Android has already granted the VPN permission.
- If nothing happens, read the help of your automation app about starting activities from the background. On many phones the automation app needs permission to display over other apps.
- To connect when the phone starts, use `Auto connect` or the always-on VPN instead. See [Permissions & Background](@/android/permissions.md#auto-connect).
- Automation apps can also open `throne://` links, for example to add a subscription. See [Deep Links](@/advanced/deeplinks.md#android).

{% alert_info() %}
The internal broadcasts of Throne, such as `io.nekohasekai.sagernet.SWITCH_NEXT`, `io.nekohasekai.sagernet.RELOAD` and `io.nekohasekai.sagernet.CLOSE`, are protected by a signature permission. Other apps cannot send them. Use the activities above instead.
{% end %}
