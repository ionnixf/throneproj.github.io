+++
title = "Health Checks & Logs"
description = "Check whether a computer can run headless Throne, ask a running Throne how it is doing, and find its logs."
weight = 40
toc = true
+++

## Throne --check {#check}

`Throne --check` tells you whether this computer can run headless Throne as it is set up. Run it as Throne's user, with the options and variables Throne starts with. It changes nothing, and it runs beside a running Throne.

```text
$ sudo /opt/Throne/Throne --check
[INFO] data dir /var/lib/throne (root default): does not exist yet; the first start creates it
[ OK ] port 127.0.0.1:9095 (the Throne API, the headless default): free
...
result: 0 failed, 0 warnings
```

It checks the data directory, the settings, the core, TUN, the kill switch, the API key, the ports, the restart mode and the [device ID](@/headless/_index.md#device-id), with `fix:` lines where there is a fix. Only what keeps Throne from working at all is `[FAIL]`, and then the command exits with code 1. Missing privileges for TUN or the kill switch are `[WARN]`, with the `setcap` or `chmod u+s` commands that fix them, or `--cap-add NET_ADMIN` and `--device /dev/net/tun` in a container.

## Throne --health {#health}

`Throne --health` asks the running Throne and prints one line of JSON. Run it as Throne's user, for the same data directory:

```bash
$ sudo /opt/Throne/Throne --health
{"api":"listening","core_restarts":0,"degraded":null,"headless_dialogs":0,"ok":true,"previous_crash":false,"previous_exit":{"at":1760700000,"code":75,"reason":"restart requested"},"ready":true,"state":"running","stuck_s":0}
```

It exits with code 0 when Throne is healthy; see [Exit codes](@/headless/command_line.md#exit-codes) for the others. The Docker image uses it as its health check.

| Field | Meaning |
| --- | --- |
| `ok` | Throne has started, the core is connected, nothing is stuck, and the kill switch is off or works. It never depends on a remote server or a running profile, so restarting Throne when it is `false` does not loop during a server outage. |
| `ready` | Throne has finished starting. |
| `state` | `starting`, `idle`, `connecting`, `running` or `stopping`. |
| `degraded` | `null`, or why Throne cannot work as set up (see below). |
| `stuck_s` | Seconds a profile has been connecting beyond 90 seconds, or stopping beyond 45. While it is above 0, `ok` is `false`. |
| `headless_dialogs` | Questions skipped because there is no window. It should stay 0; the log names them in `HEADLESS-DIALOG` lines. |
| `previous_exit` | How the previous run ended, as `{code, reason, at}`, or `null`. `previous_crash` is `true` when it did not end cleanly. |
| `api` | The API listener: `off`, `listening`, `key_invalid`, `failed`, `retrying` (waiting up to 30 seconds for its address) or `address_missing`. |

The values of `degraded`, in the order Throne reports them when several apply:

| Value | Meaning |
| --- | --- |
| `core_gave_up` | The core kept failing. Throne exits with code 70, or, after three such starts in a row, [stays up](@/headless/recovery.md#protection) and tries the core again every 5 minutes. |
| `core_unavailable` | The core process is not up. |
| `kill_switch_failed` | The kill switch is on but failed, so profiles are refused. See [Kill Switch](@/advanced/kill_switch.md). |
| `api_unavailable` | The API is on but does not listen, such as when another program holds its port or its key file cannot be used. `ok` stays `true`. |
| `tun_unavailable` | TUN was on when Throne last stopped and cannot start now: Throne lacks the privileges, or something else holds TUN (a [TUN conflict](@/headless/_index.md#beside-desktop)). The profile did not start. Run `thronectl tun off`, then `thronectl connect`. |
| `resume_suppressed` | Throne did not resume the last profile, because the last three times it did, Throne or its core stopped within a minute. Start the profile with `thronectl connect`. |
| `vpn_auth_failed` | A VPN profile's server rejected its saved credentials. It clears when the profile stops. |
| `vpn_challenge_pending` | A VPN profile's server asks for something Throne cannot answer on its own, such as a web sign-in. Throne cancels it after 5 minutes at most. |

The [API specification](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#headless-throne) has the details. For more fields, such as `pid`, `version` and those of `thronectl status`, a script can send `HEALTH` to the socket `config/.instance` in the data directory (not on Windows):

```bash
echo HEALTH | sudo socat -t 3 - UNIX-CONNECT:/var/lib/throne/config/.instance
```

## systemd: readiness, status and watchdog {#systemd}

The shipped unit is `Type=notify`:

- **`READY=1`** once Throne holds the data directory, the database is open, the API listens, the core is connected and the kill switch has settled, usually within 20 seconds. Until then Throne extends the start timeout, so `TimeoutStartSec=90` ends only a start that hangs.
- **`STATUS=`**, which `systemctl status` shows, such as `Idle`, `Connected <profile> (Tun)` or `Degraded: <reason>`.
- **`WATCHDOG=1`** every 20 seconds (`WatchdogSec=60`). If the main loop hangs, systemd sends `SIGABRT` and starts Throne again.

`KillMode=mixed` is required: systemd sends `SIGTERM` to Throne alone, and Throne stops its core and the kill switch in order, then exits (`TimeoutStopSec=45`). A second `SIGTERM` kills the core at once. Do not use `systemctl kill`: it signals the core and the kill switch helper directly.

## Logs {#logs}

Throne writes its log to standard error and to `config/logs/throne.log` in the data directory:

| How Throne runs | Log |
| --- | --- |
| systemd system unit | `journalctl -u throne -f` |
| systemd user unit | `journalctl --user -u throne -f` |
| Docker | `docker compose logs -f throne` |
| macOS LaunchDaemon | `/Library/Logs/Throne/throne.log` |
| Windows service | `C:\ProgramData\Throne\config\logs\throne.log` |

`thronectl logs -n 500` prints the last 500 lines through the API, and `thronectl logs -f` keeps following, across restarts too. It is an [admin request](@/headless/thronectl.md#remote). Throne keeps the newest 5000 lines, core output included; the `Logs` page of the [web UI](@/headless/web.md) shows them too.

`--debug` adds debug lines. `--log-format` sets the format of standard error: `journal` (syslog priorities for journald), `plain` (local times), or `auto`, which picks `journal` when standard error goes to the journal.

Lines worth searching for:

- `EXIT <code>: <reason>`: why Throne exited. See [Exit codes](@/headless/command_line.md#exit-codes).
- `Ready <n> ms after launch`: the start has finished.
- `[Resume] …`: whether the last profile was started again.
- `[Kill switch] Not active (<code>): …`: why the kill switch does not work.
- `HEADLESS-DIALOG`: a question nobody could answer.

The log of a run that did not end cleanly is kept as `config/logs/crashed-<time>.log`, and crash reports go to `config/crashes/`.
