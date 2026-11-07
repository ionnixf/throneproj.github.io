+++
title = "Troubleshooting"
description = "Common problems with headless Throne, from a unit that will not start to TUN conflicts and refused API requests, and what to include in a bug report."
weight = 60
toc = true
+++

Start with `Throne --check`, `Throne --health` and the end of the log (see [Health Checks & Logs](@/headless/monitoring.md)). The line `EXIT <code>: <reason>` says why Throne exited; see [Exit codes](@/headless/command_line.md#exit-codes). Most error messages say how to fix them. The table covers the ones that do not.

## Common problems {#symptoms}

| Symptom | Fix |
| --- | --- |
| Exit code 3: `… is in use by …`, or the desktop app says `Throne is running in the background` | Two Throne instances share one data directory, such as the desktop app and a user unit. Stop one (`systemctl --user stop throne`), or manage the headless one with `thronectl`. See [Running beside a desktop Throne](@/headless/_index.md#beside-desktop). |
| `--print-connection`: `The API key is not in a file` | The settings hold their own key, as the desktop app's do. Give thronectl that key (desktop app: `Integration Settings` → `Throne API` → `Copy for scripts`), or start Throne with `--api-key-file`. |
| thronectl: `forbidden` | An [admin request](@/headless/thronectl.md#remote) from another device. Start Throne with `--api-admin-lan`, or run the command on Throne's device. |
| thronectl: no connection | Throne is not running, or `THRONE_ADDR` points elsewhere. Check `systemctl status throne` and `Throne --print-connection`. |
| `thronectl tun on`: `needs_privileges` | Run `Throne --check` and see [Privileges on Linux](@/headless/installation.md#linux-privileges). |
| `tun_conflict` | Another network uses Throne's TUN range: pick an unused one, such as with `thronectl settings set tun_ipv4_cidr=172.29.0.1/24`. Another Throne or VPN runs TUN: only one can, so stop it. Another program holds `dns_in_port`: set another port. Then run `thronectl tun on` again. |
| Profiles refused: `kill_switch_inactive` | The kill switch is on but cannot work. `kill_switch_error` in `thronectl --json status` says why: `privilege`, `unsupported`, `config` or `overridden`. `thronectl settings set kill_switch=false` turns it off. See [Kill Switch](@/advanced/kill_switch.md). |
| `degraded: vpn_challenge_pending` or `vpn_auth_failed` | A VPN profile needs a sign-in that headless Throne cannot give, or its saved credentials were rejected. Give the profile an [OTP account](@/advanced/vpn_profiles.md#otp-manager), or fix the credentials in a desktop Throne and move them over with a [backup](@/headless/thronectl.md#from-desktop). |
| Other `degraded:` values | See the [list of values](@/headless/monitoring.md#health). |
| Docker: the published port 2080 does not answer | The inbound listens on `127.0.0.1`. Run `thronectl settings set inbound_address=0.0.0.0` and start the profile again. |
| Docker: the container comes back after `thronectl shutdown` | `restart: unless-stopped` starts it again. Use `docker compose stop`. |
| macOS: Throne starts every 10 seconds and exits | launchd retries a configuration error. Read `/Library/Logs/Throne/throne.log`. |
| Windows: the service stops at once | `sc.exe query Throne` shows Throne's exit code as `SERVICE_EXIT_CODE`; the log is in `C:\ProgramData\Throne\config\logs`. Code 78 often means a problem with [the data folder](@/headless/installation.md#windows-data-folder). |
| Windows: `tun_failed` with `open existing adapter` | Windows still holds the TUN adapter of a run that did not end cleanly. Run `thronectl connect last`; if it keeps failing, restart Windows. |
| Locked out by the kill switch or TUN | Start Throne once with `--no-resume --no-kill-switch`. See [Recovering access](@/headless/recovery.md#recovery). |

## What to include in a bug report {#reporting}

If the problem stays, [report a bug](@/help/bug_reports.md) with:

- the output of `Throne --check` and `Throne --health`,
- how Throne runs: the package or image, its version and the service manager,
- the log from the start until the problem, with `--debug` if you can reproduce it, and for a crash also `config/logs/crashed-<time>.log`.

Remove server addresses, subscription URLs and keys first. Never post `api.key`.
