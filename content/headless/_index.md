+++
title = "Headless Throne"
description = "Run Throne without a window on a server, a router, a Raspberry Pi or in a container, and control it with thronectl."
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` runs Throne without a window: on a server, a router, a Raspberry Pi, a NAS or in a container. It is the same program as the desktop app, with the same core, profiles, routing profiles and settings, but it shows no window, tray icon or dialog. You control it from a terminal with `thronectl`, through the [Throne API](@/advanced/api.md), which headless Throne turns on at the first start.

Headless Throne is available since Throne 1.4.0 on Linux, macOS and Windows. Throne for Android has no headless mode.

Typical uses:

- **A proxy box.** Throne runs a profile and other devices on the network use its mixed (HTTP and SOCKS) inbound. See [Share the proxy with other devices](@/guides/proxy_modes.md#lan-sharing).
- **A gateway.** Throne runs in TUN mode on a box that routes traffic for the network.
- **A container.** The Docker image runs Throne with its data in a volume.
- **Your own computer, without the window.** A systemd user unit keeps your desktop profiles connected after you log out.

The pages in this section:

- [Installation & Services](@/headless/installation.md): Linux packages, Docker, macOS and the Windows service.
- [Managing with thronectl](@/headless/thronectl.md): connect `thronectl`, add subscriptions, move a desktop setup over, change settings.
- [Web UI](@/headless/web.md): manage Throne from a browser with `thronectl web`.
- [Command Line & Data Directory](@/headless/command_line.md): every option and variable, where the data lives, restarts and exit codes.
- [Health Checks & Logs](@/headless/monitoring.md): `Throne --check`, `Throne --health`, systemd status and logs.
- [Lockout Protection & Recovery](@/headless/recovery.md): how Throne keeps the API within reach, and what to do when settings lock you out.
- [Troubleshooting](@/headless/troubleshooting.md): common problems and their fixes.

## Quick start {#quick-start}

On Debian 10, Ubuntu 20.04 or newer (amd64):

1. Download `Throne-<version>-debian-amd64.deb` from the [releases page](https://github.com/throneproj/Throne/releases). On Debian 12, Ubuntu 22.04 or newer, you can add the [APT repository](@/get_started/installation.md#apt-repository) instead.
2. Install the package and start the service:

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   systemctl status throne
   ```

   `systemctl status` shows what Throne is doing, for example `Status: "Idle"`.

3. Throne keeps its data in `/var/lib/throne` and its API key in `/var/lib/throne/config/api.key`, which only root can read. Copy the key to a file of your own:

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. The package also installs `thronectl`, the command-line client, as `/usr/bin/thronectl`. To manage Throne from another computer, see [Install thronectl](@/headless/thronectl.md#install-thronectl).
5. Add your subscription and connect:

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   Replace `12` with the ID of a profile from the `thronectl profiles` list, or use `thronectl connect best` to pick the one with the lowest latency.

Throne now runs the profile, and starts it again after every reboot until you run `thronectl disconnect`. To check that the box can run Throne at all before you start it, run `sudo /opt/Throne/Throne --check`. See [Throne --check](@/headless/monitoring.md#check).

## How headless Throne works {#how-it-works}

Throne never sends itself to the background. A service manager starts it at boot, starts it again after a failure and stops it: systemd on Linux, launchd on macOS, the Service Control Manager on Windows, or Docker. The Throne repository has a ready file for each:

| File | What it is |
| --- | --- |
| [`dist/linux/throne.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.service) | systemd system unit, runs as root. The `.deb` and `.rpm` packages install it as `/usr/lib/systemd/system/throne.service`. |
| [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) | Environment file for that unit, installed as `/etc/throne/throne.env`. Every variable in it is a commented-out example. |
| [`dist/linux/caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf) | Optional drop-in: run the unit as the user `throne` with capabilities instead of root. |
| [`dist/linux/throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service) | systemd user unit, without root. |
| [`dist/linux/throne-web.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.service), [`throne-web.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.env) | systemd unit for the [web UI](@/headless/web.md#systemd), which runs `thronectl web` as a user of its own, and its environment file. The packages install them as `/usr/lib/systemd/system/throne-web.service` and `/etc/throne/web.env`. |
| [`dist/macos/moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist) | launchd LaunchDaemon. |
| [`dist/windows/install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat), [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat) | Install and remove the Windows service. |
| [`docker/compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) | Docker Compose example for the image `ghcr.io/throneproj/throne`. |

What happens when headless Throne runs:

- It keeps its profiles, groups, routing profiles and settings in a [data directory](@/headless/command_line.md#data-dir), in the same `throne.db` database as the desktop app.
- The Throne API listens on `127.0.0.1:9095` from the first start, with a key that Throne generates into `config/api.key` in the data directory. `thronectl` uses that key. See [Connect thronectl to Throne](@/headless/thronectl.md#connect).
- It remembers the running profile and whether TUN was on, and starts them again at every start until you disconnect. `--no-resume` turns that off for one run.
- It reports its state to the service manager and watches its own progress. When the core keeps failing, a profile hangs or the main loop stops, it exits with a code that tells the service manager to start it again. See [Exit codes](@/headless/command_line.md#exit-codes).
- `Throne --health` asks the running Throne how it is doing, and `Throne --check` tells you whether the computer can run it. See [Health Checks & Logs](@/headless/monitoring.md).

## Differences from the desktop app {#differences}

Headless Throne asks nothing. What the desktop app asks in a dialog, headless Throne decides on its own or writes to the log:

| Situation | Headless Throne |
| --- | --- |
| A notice or an error | Writes it to the log. |
| TUN needs privileges | Refuses with `needs_privileges`. `Throne --check` says what is missing. |
| An Xray profile needs the geoip or geosite files | Downloads them and tries the start once more. |
| The [kill switch](@/advanced/kill_switch.md) cannot work | Logs why, reports `kill_switch_failed`, and refuses to start profiles. |
| A VPN profile asks for a one-time code | Answers it from the profile's [OTP account](@/advanced/vpn_profiles.md#otp-manager), if it has one. |
| A VPN profile asks for anything else, such as a web sign-in | Cancels the question when it expires, after 5 minutes at most (`vpn_challenge_pending`). Saved credentials that the server rejects give `vpn_auth_failed`. |
| A setting needs a program restart | Logs it. It applies after the next restart (`thronectl restart`). |
| The WARP terms of service | Never accepts them. |

These features need a window and are not available in headless Throne:

- The system proxy (`thronectl proxy on` fails with `system_proxy_unsupported`), the tray icon, global hotkeys and the editors.
- The built-in updater, and restarting elevated or with TUN. Update through the package, the Docker image or the installer.
- `throne://` [deep links](@/advanced/deeplinks.md) and file associations. Import with `thronectl import` instead.
- Sign-in prompts of VPN profiles, such as SSO, beyond one-time codes from an OTP account.
- WARP registration.
- Settings that the API does not manage, and restoring Extra Core profiles through the API.

Set these things up in a desktop Throne and move them over with a [backup](@/headless/thronectl.md#from-desktop). For a systemd user unit, stop the unit and open the desktop app: both use the same data.

## Running beside a desktop Throne {#beside-desktop}

TUN's interface (`throne-tun`), the core's DNS port (`dns_in_port`, 5533 by default) and TUN's routes belong to the whole computer, so only one Throne at a time can run TUN on it. Headless Throne turns TUN on only when:

- no other `throne-tun` interface exists (checked on Linux and Windows),
- no network interface already holds the TUN address (`tun_ipv4_cidr`, `172.19.0.1/24` by default), and
- the DNS port is free while no profile runs.

Otherwise `thronectl tun on` fails with `tun_conflict`, and `thronectl status` shows what holds TUN. When TUN cannot come back at start, `Throne --health` reports `tun_unavailable`. If a Docker bridge or another network uses Throne's TUN range, move TUN to a free one, then turn it on again:

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24
thronectl tun on
```

When another program holds the DNS port, give Throne a free port with `thronectl settings set dns_in_port=PORT`.

Headless Throne also marks that it runs: in `/run/throne` as root (`/var/run/throne` on macOS), in `$XDG_RUNTIME_DIR/throne` for a user, and on Windows as `Global\ThroneHeadless` (`Local\ThroneHeadless` when it is neither elevated nor a service, seen only in the same Windows session). A desktop Throne on the same computer then:

- warns at start that a headless Throne runs,
- refuses TUN while the headless one runs, and
- does not update itself while the headless one runs from the same installation.

Two Thrones never share a data directory. If a headless user unit uses the desktop app's data directory, the desktop app says that Throne is running in the background and exits. Stop the unit first with `systemctl --user stop throne`. A second headless Throne with its own data directory can run beside the first; the checks above keep the two from running TUN at the same time.

## Device ID {#device-id}

Some subscriptions ask for a device ID (the `x-hwid` header, see [User-Agent and HWID](@/guides/subscriptions.md#user-agent-and-hwid)). On Linux, Throne takes it from `/etc/machine-id`, else from `/var/lib/dbus/machine-id`. A system without either file, such as a container, gets a generated ID in `config/hwid` at the first start. That ID stays with the data directory.

The official Docker image ships without a machine ID, so every container gets its own. If you build your own image, do not put a `machine-id` file into it: every container made from the image would send the same ID.

macOS uses the hardware UUID, and Windows uses the `MachineGuid` from the registry.
