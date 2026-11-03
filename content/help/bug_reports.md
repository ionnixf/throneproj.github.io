+++
title = "Reporting a Bug"
description = "What to check before you report a bug, how to collect logs and diagnostics, and what to include in the issue."
weight = 30
toc = true
+++

A good bug report helps the developers find the cause quickly. This page explains what to check first, how to collect logs, and what to write in the issue.

Report bugs here:

- Throne desktop: [github.com/throneproj/Throne/issues](https://github.com/throneproj/Throne/issues)
- Throne for Android: [github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues)

For questions about how to use Throne desktop, use the [Q&A category of GitHub Discussions](https://github.com/throneproj/Throne/discussions/categories/q-a) instead of an issue.

{% alert_warning() %}
Issues are public. Never post subscription URLs, tokens, passwords, UUIDs, private keys or server addresses. Remove them from logs, screenshots and configs before you post. Throne desktop does not remove them for you, and a failed subscription update writes the full subscription URL to the log. This has already happened: a user posted a subscription link with its token in a public issue and had to reset it. If you post a secret by mistake, ask your provider to reset it.
{% end %}

## Before you report {#before}

1. Update to the latest version with `Program` → `Check For Update` (`Tools` → `Check For Update` before 1.4.0), or from the [downloads](@/downloads.md) page. Many bugs are already fixed.
2. Search the existing issues, open and closed, for your error message.
3. Read the [FAQ](@/help/faq.md) and [Troubleshooting](@/help/troubleshooting.md).
4. Try a fresh configuration: exit Throne, rename the `config` folder, start Throne, and set up only what you need to show the problem. Write in the report whether the problem also happens there. To go back, exit Throne, delete the new `config` folder and rename the old one back.
5. If you installed Throne from WinGet, Scoop, the AUR or Nix, test the official build too. The developers do not support these packages.
6. If only one server fails, test it in another app. If it fails there too, contact your provider.

## Collect logs {#logs}

### Desktop {#desktop-logs}

Throne shows its log in the `Logs` tab at the bottom of the main window, and writes the same lines to `logs/throne.log` in the config folder (`Settings` → `Open Config Folder`, in Advanced mode only; see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

To record a detailed log:

1. Open `Settings` → `Basic Settings` → `Logging` (Advanced mode only).
2. Set `Sing-box Log level` to `debug`. For `VLESS (Xray)` and other Xray profiles, also set `Xray Log level` to `debug`.
3. Raise `Max log lines`, for example to `2000`. Throne drops core log lines above this number per second, and the `Logs` tab keeps only this many lines.
4. Click `OK`, then restart Throne with `Program` → `Restart Program`.
5. Reproduce the problem.
6. Attach `logs/throne.log` to the issue. You can also click in the `Logs` tab, press `Ctrl+A`, then `Ctrl+C`, and paste the text. If the filter bar of the `Logs` tab is open, close it first, so that the tab shows all lines.
7. When you are done, set the sing-box log level back to `info` and the Xray log level back to `warning`.

Good to know:

- `throne.log` starts again every time Throne starts, so copy it before you restart Throne. When it grows beyond 4 MB, the older parts are kept as `throne.log.1` to `throne.log.3`.
- The first lines of `throne.log` show your Throne version, Qt version, operating system and CPU architecture.
- After a crash, the next start keeps the log of the crashed session as `logs/crashed-<date>-<time>.log` and writes "Throne did not shut down cleanly last time" to the log. The last five crash logs are kept.
- **Windows:** when Throne crashes, it shows "Throne crashed" and saves a report in the `crashes` folder: a `.txt` file and a `.dmp` file named `Throne_<version>_<architecture>_<date>-<time>`. Attach the `.txt` file, and the `.dmp` file if you can.

### Android {#android-logs}

1. Open the drawer and tap `Logs`.
2. Keep `Hide sensitive data` on. It is on by default and hides URLs, credentials, UUIDs, keys, public IP addresses and Wi-Fi names. Turn on `Hide destinations` to also hide the domains you visited.
3. Tap `Share logs` or `Save logs…` and attach the file to the issue.

For a detailed log, set `Settings` → `Core` → `Log level` to `debug` before you reproduce the problem. Since 2.0.5, the log also contains the Xray log of Xray profiles; for them, also set `Xray log level` on the same screen. After a crash, the app offers a cleaned-up log. See [Android Troubleshooting](@/android/troubleshooting.md#logs).

## Diagnostics {#diagnostics}

For freezes, hangs and high CPU use, the developers may ask for a Diagnostics file. It records what the core does for 30 seconds.

1. Bring Throne into the state with the problem, for example start the profile and wait until it hangs. Do not restart Throne.
2. Open `Settings` → `Basic Settings` → `Diagnostics`.
3. Tick the options the developer asked for: `Lock contention`, `Execution trace (larger file)` or `Include Throne logs`.
4. Click `Start` and wait 30 seconds.
5. Click `Show in folder` and attach the `throne-profile-<date>-<time>.zip` file. Throne saves these files in a `diagnostics` folder next to the `config` folder, not inside it; see [Data folder](@/reference/files.md#data-folder).

`Start` works only while the core is running. The recording contains no information about your configs, but it lists your Throne version, operating system and a few settings, such as whether TUN mode is on and the type of the running profile. `Include Throne logs` adds your logs, which contain visited domains and server addresses.

**Android:** since 2.0.3, the app has the same feature in `Settings` → `Diagnostics`. Tap `Share` or `Save…` to get the file. See [Diagnostics](@/android/troubleshooting.md#diagnostics).

## What to include {#what-to-include}

Use the issue template and fill in every field:

| Item | Where to find it |
| --- | --- |
| Throne version | Desktop: `Program` → `About` (since 1.4.0), which also shows the versions of sing-box, Xray-core, Qt and Go, and lets you select and copy them; or the window title, for example `Throne 1.3.1`, or the first lines of `throne.log`. Android: `About`. |
| Operating system and version | For example Windows 11 24H2, Ubuntu 24.04, macOS 15 or Android 14. |
| Installation type | ZIP, Windows installer, `.deb`, `.rpm`, Linux install script, the APT or RPM repository, or a package manager (WinGet, Scoop, AUR, Nix, Homebrew). Say whether you use the window or [headless Throne](@/headless/_index.md). |
| Mode | `Tun Mode`, `System Proxy` or both, and whether the [kill switch](@/advanced/kill_switch.md) is on. |
| Profile type | The `Type` column of the profile list, for example `VLESS (Xray)`. Not the link. |
| Steps to reproduce | Numbered steps that show the problem from the start. |
| Expected and actual result | What you expected, and what happened instead. Copy error messages as text. |
| Logs | A debug log, crash files or a Diagnostics file, as described above. |

For routing problems, choose the `Routing Issue` template. It asks for your routing profile: open `Settings` → `Routing Settings` → `Route`, select the profile and click `Export`. This copies a `throne://route/…` link. Check it for private domains or addresses before you post it.

If a developer asks for your configuration, right-click the profile → `Share` → `Export config` (`Export Sing-box config` before 1.4.0; Advanced mode only), and click `Copy` on the `sing-box` tab, or on the `Xray` tab if the developer asks for it. Replace passwords, UUIDs, keys and server addresses with `xxxx` before you post it.

For headless Throne, also see [What to include in a bug report](@/headless/troubleshooting.md#reporting).
