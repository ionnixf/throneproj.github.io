+++
title = "Command Line & Data Directory"
description = "Options, environment variables, data directory, restart modes and exit codes of headless Throne."
weight = 30
toc = true
+++

The desktop app's options are in [Command line](@/reference/files.md#command-line).

## Options and variables {#options}

`Throne --headless --help` lists the options. An option wins over its variable. With the systemd unit, set variables in `/etc/throne/throne.env` and options with a drop-in (see [Linux packages](@/headless/installation.md#linux-packages)).

| Option | Variable | What it does |
| --- | --- | --- |
| `--headless` | `THRONE_HEADLESS=1` | Run without a window. |
| `--data-dir DIR` | `THRONE_DATA_DIR` | The [data directory](#data-dir). |
| `--restart-mode MODE` | `THRONE_RESTART_MODE` | `auto` (default), `exit` or `exec`. See [Restart modes](#restart-modes). |
| `--no-resume` | `THRONE_RESUME=0` | Do not start the last profile at start. |
| `--no-kill-switch` | `THRONE_KILL_SWITCH=0` | Keep the [kill switch](@/advanced/kill_switch.md) off for this run. Profiles stay refused until the `kill_switch` setting is off. For [recovery](@/headless/recovery.md#recovery). |
| `--log-format FORMAT` | `THRONE_LOG_FORMAT` | `auto` (default), `journal` or `plain`. See [Logs](@/headless/monitoring.md#logs). |
| `--api-listen LISTEN` | `THRONE_API_LISTEN` | `PORT` (on `127.0.0.1`), `IPv4:PORT`, `[IPv6]:PORT`, `*:PORT` or `off`. Default: `127.0.0.1:9095`, or what the saved settings say. |
| `--api-key-file FILE` | `THRONE_API_KEY_FILE` | Read the API key from this existing file (`thronectl keygen -o FILE` makes one). Default: `config/api.key` in the data directory. |
| `--api-admin-lan` | `THRONE_API_ADMIN_LAN=1` | Answer [admin requests](@/headless/thronectl.md#remote) from the network too. |
| `--api-allow LIST` | `THRONE_API_ALLOW` | Addresses and subnets that may use a listener that is not on loopback, separated by commas or spaces. Unset allows every address; an empty list exits with code 64. |
| `--debug` | | Debug logging. |
| `--service` | | Windows only: run under the Service Control Manager. |
| `--health` | | Ask the running Throne [how it is doing](@/headless/monitoring.md#health). |
| `--print-connection` | | Print `THRONE_ADDR` and `THRONE_KEY_FILE` for [thronectl](@/headless/thronectl.md#connect). |
| `--check` | | [Check](@/headless/monitoring.md#check) whether this computer can run headless Throne. |

- `THRONE_RESUME`, `THRONE_KILL_SWITCH` and `THRONE_API_ADMIN_LAN` take `1`, `true`, `yes`, `on`, `0`, `false`, `no` or `off`. Anything else exits with code 64.
- The `--api-*` options and variables override the saved API settings at every start and are never saved, so a bad API setting can always be fixed from the command line.
- `--health`, `--print-connection` and `--check` work without `--headless`. Use one at a time, and never with `--service`.
- Links and files on the command line exit with code 64. Import them with [`thronectl import`](@/headless/thronectl.md#profiles).

## Data directory {#data-dir}

Headless Throne uses the first of these that applies:

1. `--data-dir`, then `THRONE_DATA_DIR`.
2. `STATE_DIRECTORY`, which systemd sets for `StateDirectory=`.
3. As root: `/var/lib/throne` on Linux, `/Library/Application Support/Throne` on macOS. On Windows, as a service or as LocalSystem: `C:\ProgramData\Throne`.
4. The desktop app's folder: `~/.config/Throne` on Linux, `~/Library/Preferences/Throne` on macOS, `%LOCALAPPDATA%\Throne` on Windows.

It never uses a portable installation's folder next to the program; pass that folder with `--data-dir`.

On Linux and macOS, Throne creates missing directories readable only by its user. As root, it refuses a data directory that other users can write to (code 78), because the settings name programs that Throne starts. On Windows, see [The data folder](@/headless/installation.md#windows-data-folder).

| Path | Contents |
| --- | --- |
| `config/throne.db` | Profiles, groups, routing profiles and settings, as in the desktop app |
| `config/api.key` | The API key, readable only by Throne's user |
| `config/logs/throne.log` | The log of this run. `crashed-<time>.log` keeps the log of a run that did not end cleanly. |
| `config/crashes/` | Crash reports |
| `config/hwid` | The [device ID](@/headless/_index.md#device-id), when the system has none |
| `web/` | Docker image only: the password hash and TLS certificate of the [web UI](@/headless/web.md#docker) |

## Restart modes {#restart-modes}

`thronectl restart`, a backup restore and `SIGHUP` restart Throne. `--restart-mode` decides how:

| Mode | What happens |
| --- | --- |
| `exit` | Throne exits with code 75, and the service manager starts it again. |
| `exec` | Throne starts again in the same process (Linux, macOS). On Windows it starts a new Throne process and exits with code 0. |
| `auto` | `exit` under systemd, launchd or a container init, or as a Windows service. `exec` otherwise, such as in a terminal. |

The shipped units, the plist and the Docker image need `auto` or `exit`; a Windows service always uses `exit`. `Throne --check` shows what `auto` picks. In a terminal, closing the terminal stops Throne with code 0 instead of restarting it.

## Exit codes {#exit-codes}

Throne writes `EXIT <code>: <reason>` to the log before it exits.

| Code | Meaning |
| --- | --- |
| 0 | Stopped: `SIGTERM`, `SIGINT`, `thronectl shutdown`, a Windows service stop or `Ctrl+C` |
| 1 | The start failed for a reason that may pass: the lock, the database, the core's IPC listener, or an `--api-listen` address that did not appear within 30 seconds |
| 3 | Another Throne uses the data directory |
| 64 | A bad option, option value or variable |
| 70 | The core kept failing, or was not usable for 60 seconds |
| 71 | A profile was connecting for 180 seconds or stopping for 90 |
| 75 | Restart requested, in `exit` mode or as a Windows service |
| 78 | Configuration error: the data directory (unsafe permissions, or no `HOME` to find one), the API key file, the `--api-listen` listener, or a database file that cannot be used |

What the shipped service files do:

- **systemd unit:** starts Throne again 3 seconds after 1, 70, 71 and 75, and leaves it down after 0, 3, 64 and 78. It has no start limit (`StartLimitIntervalSec=0`): every restart, restore and `SIGHUP` is a new start, and a limit would leave Throne down with no API.
- **Docker** (`restart: unless-stopped`): starts it again after every exit except a `docker stop`.
- **launchd plist:** starts it again after every code except 0, at most every 10 seconds, so it also retries 3, 64 and 78.
- **Windows service:** runs its [recovery actions](@/headless/installation.md#windows) after every code except 0.

After three starts in a row without a working core, Throne [stays up](@/headless/recovery.md#protection) instead of exiting with 70 or 71. For code 78 with a database, the log says what to do: move a damaged `config/throne.db` aside together with its `-wal` and `-shm` files, or put back a copy. Make read-only files writable by Throne's user.

The check commands have their own codes, and exit with 64 for a bad option:

| Command | 0 | 1 | 2 |
| --- | --- | --- | --- |
| `--health` | Healthy | Not healthy, or still starting | No Throne answered, or the Windows prompt is not elevated |
| `--check` | No `[FAIL]` line | A `[FAIL]` line | |
| `--print-connection` | Printed both lines | | The API is off or not listening, its key is not in a file, or the Windows prompt is not elevated |

`--health` and `--print-connection` exit with code 78 when they find no data directory (no `HOME`); name it with `--data-dir`.
