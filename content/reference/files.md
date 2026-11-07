+++
title = "Command Line, Files & Logs"
description = "Command-line options of Throne desktop, where it keeps its data and logs, and how to reset it."
weight = 30
toc = true
+++

## Command line {#command-line}

| Option | What it does |
| --- | --- |
| `-appdata` | Keeps the data in your user folder instead of next to the program. See [Data folder](#data-folder). |
| `-appdata <folder>` | Keeps the data in a `config` folder inside `<folder>`. |
| `-tray` | Starts in the tray, with the window hidden. |
| A `throne://` link | Handles the link as if you had clicked it. See [Deep Links](@/advanced/deeplinks.md). |
| File paths or `file://` URLs | Imports the files, like `Program` → `Add profile from File(s)`. |
| `--headless` | Runs Throne without a window, as a service or in a container. See [Headless Throne](@/headless/_index.md); its options are in [Command Line & Data Directory](@/headless/command_line.md). |
| `--check`, `--health`, `--print-connection` | Check whether this computer can run headless Throne, ask a running one how it is doing, or print the settings for `thronectl`. |

```bash
./Throne -appdata "$HOME/throne-test"
```

```powershell
.\Throne.exe -appdata D:\ThroneData
```

- Only one Throne runs per data folder. Starting it again passes the link or files to the running Throne and brings its window to the front. If a headless Throne uses the folder, the new start shows "Throne is running in the background" and exits, see [Troubleshooting](@/help/troubleshooting.md#background-throne).
- **macOS:** to pass options, run the program inside the app bundle: `/Applications/Throne.app/Contents/MacOS/Throne -tray`.

## Data folder {#data-folder}

Throne keeps your data in a folder named `config`. `Settings` → `Open Config Folder` opens it (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

| How you installed Throne | Data folder |
| --- | --- |
| Windows or Linux ZIP | `config` in the unpacked `Throne` folder (portable) |
| Windows installer, for the current user (default) | `config` in the install folder (portable), by default `%LOCALAPPDATA%\Throne\config` |
| Windows installer, for all users | `%LOCALAPPDATA%\Throne\config` of each user |
| Linux `.deb`, `.rpm` or install script | `~/.config/Throne/config`, or `$XDG_CONFIG_HOME/Throne/config` if you set `XDG_CONFIG_HOME` |
| macOS | `~/Library/Preferences/Throne/config` |

- A portable copy uses the per-user folder (`%LOCALAPPDATA%\Throne\config` on Windows, `~/.config/Throne/config` on Linux) when it starts with `-appdata`. It also switches there by itself when it cannot write to its own folder, such as in `Program Files`: it copies the existing `config` once and keeps using the per-user folder, even when you run it as administrator.
- Packages from other sources (WinGet, Scoop, AUR, Nix) may use other folders. `Open Config Folder` always shows the right one.
- The Xray geo files `geoip.dat` and `geosite.dat` and the `diagnostics` folder (from `Basic Settings` → `Diagnostics`) are next to the `config` folder, not inside it.

### Files in the config folder {#config-files}

| File or folder | What it holds |
| --- | --- |
| `throne.db` | Profiles, groups, routing profiles, OTP profiles and settings (SQLite). `throne.db-wal` and `throne.db-shm` appear next to it while Throne runs. |
| `throne_stats.db` | Traffic history for `Tools` → `Traffic Stats`: per minute for 48 hours, per hour for 90 days. `Basic Settings` → `Style` → `Disable Traffic Aggregation` stops it. |
| `cache.db` | The core's cache: downloaded rule-sets, the last choice of each auto selector and, with `Save Cache To File` on, the DNS cache. |
| `logs/` | Log files, see [Log file](#log-file). |
| `crashes/` | Crash dumps (Windows only). The newest 5 are kept. |
| `icons/` | Custom tray icons for `Basic Settings` → `Style` → `Enable Custom Icons`: `Off.png`, `Throne.png` (a profile runs), `Proxy.png`, `Tun.png` and `Connecting.png`. |
| `hwid` | Linux only: the device ID that Throne created because the system has no usable `/etc/machine-id` or `/var/lib/dbus/machine-id`. See [HWID](@/reference/privacy.md#hwid). |
| `.lock` | Shows that a Throne uses this folder. Throne never deletes it. |
| `dashboard/` | A web dashboard for the Clash API. Replace the placeholder page with the dashboard files. |
| `sb-dashboard/` | The web dashboard that `Tools` → `Open Web dashboard` opens. |

To move your data to another computer or to Android, use a `.thrbackup` backup instead of copying files. See [Backup and restore](@/guides/backup.md#backup-restore).

## Logs {#logs}

The `Logs` tab at the bottom of the main window shows messages from Throne and the core.

Log settings are in `Settings` → `Basic Settings` → `Logging` (Advanced mode only):

| Setting | Default | What it does |
| --- | --- | --- |
| `Max log lines` | `500` | How many lines the `Logs` tab keeps. |
| `Sing-box Log level` | `info` | How much the core writes: `trace`, `debug`, `info`, `warn`, `error`, `fatal` or `panic`. |
| `Xray Log level` | `warning` | How much Xray writes: `debug`, `info`, `warning`, `error` or `none`. |
| `Log Filtering` | Off | `Enable Include Rules` shows only lines that match your keywords or regular expressions, `Enable Exclude Rules` hides them. Hidden lines still go to the log file. |

To record a detailed log for a bug report, follow [Reporting a Bug](@/help/bug_reports.md#logs).

### Log file {#log-file}

Throne also writes the log, with more detail, to `logs/throne.log` in the config folder.

- Throne starts a new file at every start. At 4 MB, it renames the file to `throne.log.1` and starts a new one, keeping up to three old parts (`throne.log.1` to `throne.log.3`).
- If Throne did not quit cleanly, the log of that session is kept as `crashed-YYYYMMDD-HHMMSS.log` (the newest 5), and the next start shows a warning with the log folder in the `Logs` tab.

{% alert_warning() %}
Logs can contain the domains you visited and the addresses of your servers. Check a log before you share it publicly.
{% end %}

**Android:** open `Logs` in the drawer. `Share logs` and `Save logs…` export the log, with sensitive data hidden by default. The log level is in `Settings` → `Core` → `Log level` (default `warn`). See [Android Troubleshooting](@/android/troubleshooting.md#logs).

## Reset Throne {#reset}

Throne has no reset button. To start over with default settings, give it a new, empty config folder:

1. To keep anything, create a backup first: `Settings` → `Basic Settings` → `Backup and Restore` → `Create Backup...`.
2. `Settings` → `Open Config Folder` shows where the folder is.
3. Quit Throne with `Exit` in the tray menu or the `Program` menu. Closing the window is not enough.
4. Rename the `config` folder, for example to `config-old`.
5. Start Throne. It creates a new `config` folder with default settings.

To bring back parts of your old data, use `Restore from Backup...` on the same tab and select only those parts. To undo the reset, quit Throne, delete the new `config` folder and rename `config-old` back to `config`.

A reset does not touch what is stored outside the config folder: `Start with system` (an autostart entry of the system), the `throne://` link registration and the Xray geo files.

**Windows:** uninstalling a copy from the installer offers to delete the config folder too.

**Android:** `Settings` → `Restore default settings` resets all settings but keeps your profiles, groups and routing profiles, then restarts the app. To remove everything, clear the app's data in the Android app settings. File managers cannot open the app's data, so use a `.thrbackup` backup to copy it out.
