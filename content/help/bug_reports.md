+++
title = "Reporting a Bug"
description = "What to check first, how to collect logs and diagnostics, and what to put in the issue."
weight = 30
toc = true
+++

Report bugs on GitHub:

- Throne desktop: [github.com/throneproj/Throne/issues](https://github.com/throneproj/Throne/issues)
- Throne for Android: [github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues)

For questions about using Throne desktop, use the [Q&A category of GitHub Discussions](https://github.com/throneproj/Throne/discussions/categories/q-a) instead.

{% alert_warning() %}
Issues are public. Remove subscription URLs, tokens, passwords, UUIDs, private keys and server addresses from logs, screenshots and configs before you post. Throne desktop does not remove them for you, and a failed subscription update writes the full subscription URL to the log. If you post a secret by mistake, ask your provider to reset it.
{% end %}

## Before you report {#before}

1. Update Throne with `Program` → `Check For Update`, or from the [downloads](@/downloads.md) page.
2. Search the open and closed issues for your error message, and read the [FAQ](@/help/faq.md) and [Troubleshooting](@/help/troubleshooting.md).
3. Test with a [fresh configuration](@/reference/files.md#reset) that has only what shows the problem, and say in the report whether the problem happens there too.
4. If you use a WinGet, Scoop, AUR or Nix package, test the official build too; the developers do not support these packages.
5. If only one server fails, test it in another app. If it fails there too, contact your provider.

## Collect logs {#logs}

### Desktop {#desktop-logs}

The `Logs` tab at the bottom of the main window shows the log, and Throne writes the same lines to `logs/throne.log` in the config folder (`Settings` → `Open Config Folder`). The file starts again every time Throne starts, so copy it before you restart.

To record a detailed log:

1. In `Settings` → `Basic Settings` → `Logging` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)), set `Sing-box Log level` to `debug`, and for Xray profiles also `Xray Log level`.
2. Raise `Max log lines`, for example to `2000`, so that Throne does not drop log lines.
3. Click `OK` and restart Throne with `Program` → `Restart Program`.
4. Reproduce the problem and attach `logs/throne.log`. If you copy from the `Logs` tab instead, close its filter bar first.
5. Set the log levels back to `info` (sing-box) and `warning` (Xray).

After a crash, attach `logs/crashed-<date>-<time>.log`, and on Windows the `.txt` and `.dmp` files from the `crashes` folder.

### Android {#android-logs}

Open the drawer and tap `Logs`. Keep `Hide sensitive data` on (the default), then tap `Share logs` or `Save logs…`. For a detailed log, first set `Settings` → `Core` → `Log level` to `debug` (and `Xray log level` for Xray profiles). See [Android Troubleshooting](@/android/troubleshooting.md#logs).

## Diagnostics {#diagnostics}

For freezes, hangs and high CPU use, the developers may ask for a Diagnostics file, a 30-second recording of what the core does.

1. Bring Throne into the state with the problem, for example wait until it hangs. Do not restart Throne.
2. In `Settings` → `Basic Settings` → `Diagnostics`, tick the options the developer asked for: `Lock contention`, `Execution trace (larger file)` or `Include Throne logs`.
3. Click `Start` and wait 30 seconds. `Start` works only while the core runs.
4. Click `Show in folder` and attach the `throne-profile-<date>-<time>.zip` file.

The file lists your Throne version, system and a few settings, but none of your configs. `Include Throne logs` adds your logs, which contain visited domains and server addresses. On Android, use `Settings` → `Diagnostics`; see [Diagnostics](@/android/troubleshooting.md#diagnostics).

## What to include {#what-to-include}

Use the issue template and fill in every field:

| Item | Where to find it |
| --- | --- |
| Throne version | `Program` → `About`, which lets you copy all component versions. On Android: `About`. |
| Installation type | ZIP, Windows installer, `.deb`, `.rpm`, install script, repository or package manager, and whether you use [headless Throne](@/headless/_index.md). |
| Mode | `Tun Mode`, `System Proxy` or both, and whether the [kill switch](@/advanced/kill_switch.md) is on. |
| Profile type | The `Type` column, for example `VLESS (Xray)`. Not the link. |

For routing problems, use the `Routing Issue` template and add your routing profile: in `Settings` → `Routing Settings` → `Route`, select it and click `Export` to copy a `throne://route/…` link. Check it for private domains and addresses first.

If a developer asks for your configuration, right-click the profile → `Share` → `Export config` (Advanced mode only) and copy the `sing-box` tab, or the `Xray` tab if asked. Replace passwords, UUIDs, keys and server addresses with `xxxx` first.

For headless Throne, see also [What to include in a bug report](@/headless/troubleshooting.md#reporting).
