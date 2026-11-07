+++
title = "Keyboard Shortcuts & Tray"
description = "Keyboard shortcuts, the command palette, global hotkeys and the tray icon of Throne desktop."
weight = 20
toc = true
+++

Shortcuts work while the Throne window is active. **macOS:** `Ctrl` means `Cmd` and `Alt` means `Option`, so `Ctrl+Shift+S` is `Cmd+Shift+S`. On a Mac laptop keyboard, `Del` is `Fn+Delete`.

## Main window {#main-window}

The `Settings` and `Routing` menus have no shortcuts. The "Can change" column shows whether you can set another shortcut, see [Change a shortcut](#change-shortcuts). Simple mode hides some items, such as `Resolve Domain for group`, `Export config` and `Reset Traffic` (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

In the status bar, click the running profile to URL-test it, or `Mixed: …` to open `Basic Settings`.

### Program menu {#program-menu}

| Action | Shortcut | Can change |
| --- | --- | --- |
| `Hide window` | `Ctrl+H` (macOS: also `Cmd+W`) | Yes |
| `New profile` | `Ctrl+N` | Yes |
| `Add profile from clipboard` | `Ctrl+V` | No |
| `Add profile from File(s)` | `Ctrl+O` | Yes |
| `Scan QR Code` | `Ctrl+Shift+Q` | Yes |

You can also drop files, QR code images or links on the window to import them.

### Groups menu {#groups-menu}

Group actions work on the group of the open tab.

| Action | Shortcut | Can change |
| --- | --- | --- |
| `Url Test Group` | `Ctrl+Shift+G` | Yes |
| `Speedtest Group` | `Ctrl+Alt+P` | Yes |
| `Resolve Domain for group` | `Ctrl+Shift+I` | Yes |
| `Clear Group test result` | `Ctrl+Shift+C` | Yes |
| `Update subscription` | `Ctrl+U` | Yes |
| `Clean up` → `Remove Duplicates` | `Ctrl+Shift+D` | Yes |
| `Clean up` → `Remove Unavailable` | `Ctrl+Shift+R` | Yes |
| `Clean up` → `Remove Invalid` | `Ctrl+Alt+I` | Yes |
| `Stop Testing` | `Ctrl+.` | Yes |

`Stop Testing` appears only while a test is running. Right-click a group tab for the group's menu; drag tabs to reorder the groups.

### Tools menu {#tools-menu}

| Action | Shortcut | Can change |
| --- | --- | --- |
| `Search Settings` | `Ctrl+K` | Yes |
| `Speedtest Current` | `F6` | Yes |

`Search Settings` opens the [command palette](#command-palette). `Speedtest Current` works only while a profile is running.

## Command palette {#command-palette}

The command palette finds menu commands and settings by name. Press `Ctrl+K` in the main window, or click `Tools` → `Search Settings`.

- Type one or more words. An entry matches when its name or path, such as `Basic Settings › Common › Inbound Settings`, contains all of them. In a translated interface, English words also find many entries.
- Start with `>` to search only commands, or with `@` to search only settings. `@` alone lists all settings.
- Press `Enter` or double-click an entry to run it. `Esc` closes the palette.

The palette finds the settings of `Basic Settings`, `Routing Settings`, `Tun Settings`, `Preset Settings` and `Integration Settings`, and the commands that the menus show at the moment, including the profile list menu and your routing profiles.

In Simple mode, the palette also finds the hidden settings, under `Settings in Advanced mode`; choosing one switches Throne to Advanced mode. Hidden menu commands are not listed.

Only one of these windows can be open at a time: a settings window, `Manage Groups`, `OTP Manager`, `Traffic Stats` and `About`. While one is open, the palette greys out entries that would open another one.

## Profile list {#profile-list}

Right-click the profile list for its menu. Besides the "add" actions of the `Program` menu, it has:

| Action | Shortcut | Can change |
| --- | --- | --- |
| `Start` | `Enter` | No |
| `Stop` | `Ctrl+S` | No |
| `Share` → `Export config` | `Ctrl+E` | Yes |
| `Share` → `Copy links of selected` | `Ctrl+C` | No |
| `Share` → `Copy links of selected (Deep Links)` | `Ctrl+Alt+C` | No |
| `Delete` | `Del` | No |
| `Select All` | `Ctrl+A` | No |
| `Clone` | `Ctrl+D` | Yes |
| `Url Test Selected` | `Ctrl+Shift+S` | Yes |
| `Speedtest Selected` | `Ctrl+Shift+P` | Yes |
| `Reset Traffic` | `Ctrl+R` | Yes |

Other keys and mouse actions in the list:

| Key or action | What it does |
| --- | --- |
| Double-click a profile | Opens the profile editor. |
| `Esc` | Clears the selection. In the filter row, closes the filter row. |
| Drag a row | Moves the profile. This does not work while a filter is active. |
| `Ctrl+F` | Shows or hides the filter row. Hiding it clears the filters. |
| `Up` on the first row, `Down` in the filter row | Moves between the list and the filter row. |

### Actions without a default shortcut {#no-default}

You can give a shortcut to `QR Code and link`, `Copy Test Result`, `Resolve Selected Domain`, `Resolve Selected Out IP`, `Clear Test Result`, `Resolve out IP for group`, `Update all subscriptions`, `Remove Insecure Configs`, `IP Scanner` and `Refresh Column Widths`.

## Change a shortcut {#change-shortcuts}

1. Open `Settings` → `Integration Settings` → `Shortcuts` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).
2. Click the field next to an action and press the new key combination. `Backspace` removes the shortcut.
3. Click `OK`.

Shortcuts marked "No" above, and `Ctrl+F`, cannot be changed. If another action or a global hotkey already uses the combination, the window names the conflict and does not save until you change one of them.

## Global hotkeys {#global-hotkeys}

Global hotkeys work in any app, even when the Throne window is hidden. All of them are empty by default.

1. Open `Settings` → `Integration Settings` → `Global Hotkeys`.
2. Click the field of a hotkey and press the key combination.
3. Click `OK`.

| Hotkey | What it does |
| --- | --- |
| `Trigger main window` | Shows the main window, or hides it if it is active, like a click on the tray icon. |
| `Show groups` | Opens `Manage Groups`. |
| `Show routes` | Opens `Routing Settings`. |
| `Proxy mode` | Opens the `Operation Mode` menu (`System Proxy`, `Tun Mode`) at the mouse pointer. |
| `Toggle System Proxy` | Turns `System Proxy` on or off. |
| `Start/Stop Profile` | Stops the running profile, or starts the last profile when none runs. |
| `Toggle Tun Mode` | Turns `Tun Mode` on or off. |

- A hotkey needs `Ctrl`, `Alt` or `Meta` (`Cmd` on macOS), unless it is a function key such as `F9`.
- A combination that another program or the system already uses does not work. After you click `OK`, Throne lists the hotkeys it could not register.
- Global hotkeys are paused while `Integration Settings` is open.
- **Linux:** global hotkeys work in X11 and KDE Plasma Wayland sessions (on KDE Plasma they also appear in the system shortcut settings, under `Throne`), but not in other Wayland sessions.

To control Throne from a macro pad, a Stream Deck or a script, use [thronectl](@/advanced/api.md#example-hotkey).

## Tray icon {#tray}

Throne shows an icon in the system tray (on macOS, in the menu bar). The icon changes while a profile runs and when `System Proxy` or `Tun Mode` is on. Hover over it to see the running profile and mode.

- **Windows and Linux:** click the icon to show or hide the window. Right-click it for the tray menu.
- **macOS:** click the icon for the tray menu.

Closing the window does not quit Throne: it keeps running in the tray. To quit, choose `Exit` in the tray menu or the `Program` menu.

**macOS:** while the window is hidden, Throne has no Dock icon. If you keep Throne in the Dock, click its Dock icon to show the window, or open Throne again from Finder.

**Linux:** GNOME shows tray icons only with the AppIndicator extension ([#1623](https://github.com/throneproj/Throne/issues/1623)). Without a tray icon, start Throne again to bring back its hidden window.

### Tray menu {#tray-menu}

| Item | What it does |
| --- | --- |
| `Connect` / `Disconnect` | Stops the running profile, or starts the profile selected in the list, else the last profile, else the first profile of the open group. |
| `Remember last profile` | At the next start, Throne starts the profile that was running when it quit, and turns `System Proxy` and `Tun Mode` back on if they were on. A profile you stopped yourself is not remembered. |
| `Allow other devices to connect` | Opens the local proxy port to your network, see [LAN sharing](@/guides/proxy_modes.md#lan-sharing). |
| `Select Profile` | Starts a profile chosen by group, or found by typing its name. |
| `Select Routing` | Makes a routing profile active and restarts the running profile. |
| `OTP Codes` | Click an OTP profile to copy its current code. Hidden in Simple mode. |

`Start with system` starts Throne when you log in. It adds a Task Scheduler task on Windows, a login item on macOS, and `Throne.desktop` with the option `-tray` to `~/.config/autostart` on Linux. On Windows and macOS, the window opens at login unless `Hide dashboard at startup` is on. On Windows, each start of Throne gives the task the rights of the running Throne: if Throne runs as administrator, the task also starts it as administrator, unless `Always Start as Standard User` is on.

### Tray settings {#tray-settings}

In `Settings` → `Basic Settings` → `Style`:

| Setting | Default | What it does |
| --- | --- | --- |
| `Hide dashboard at startup` | Off | Throne starts with only the tray icon, like the command-line option `-tray`. |
| `Disable tray` | Off | Removes the tray icon. Closing the window then quits Throne. |

{% alert_warning() %}
Do not turn on `Disable tray` together with `Hide dashboard at startup` or `-tray`: Throne would start with no window and no tray icon. If this happens, start Throne again, and the running Throne shows its window.
{% end %}
