+++
title = "Headless Throne"
description = "Run Throne without a window on a server, a router, a Raspberry Pi or in a container, and control it with thronectl."
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` runs Throne without a window: on a server, a router, a Raspberry Pi or in a container. It is the same program as the desktop app, with the same core, profiles and settings. You control it with `thronectl` through the [Throne API](@/advanced/api.md), which is on from the first start. It needs Throne 1.4.0 or newer on Linux, macOS or Windows. Throne for Android has no headless mode.

Typical setups: a proxy box whose mixed inbound [other devices use](@/guides/proxy_modes.md#lan-sharing), a TUN gateway for the network, a Docker container, or a [systemd user unit](@/headless/installation.md#user-unit) that keeps your desktop profiles connected after you log out.

## Quick start {#quick-start}

On Debian 10, Ubuntu 20.04 or newer (amd64):

1. Download `Throne-<version>-debian-amd64.deb` from the [releases page](https://github.com/throneproj/Throne/releases), or add the [APT repository](@/get_started/installation.md#apt-repository) (Debian 12, Ubuntu 22.04 or newer).
2. Install it and start the service:

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   ```

3. Copy the API key, which only root can read, to a file of your own:

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. Add your subscription and connect with `thronectl`, which the package installs:

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   Replace `12` with an ID from the list, or use `thronectl connect best` for the profile with the lowest latency.

Throne starts this profile again after every reboot, until you run `thronectl disconnect`. If something does not work, run `sudo /opt/Throne/Throne --check` (see [Throne --check](@/headless/monitoring.md#check)). For Docker, macOS, Windows and other Linux installs, see [Installation & Services](@/headless/installation.md).

## How headless Throne works {#how-it-works}

- A service manager starts, restarts and stops it: systemd, launchd, the Windows Service Control Manager or Docker. Throne never sends itself to the background.
- It keeps profiles, groups, routing profiles and settings in a [data directory](@/headless/command_line.md#data-dir), in the same `throne.db` as the desktop app.
- The API listens on `127.0.0.1:9095`, with a key that Throne creates in `config/api.key` in the data directory. See [Connect thronectl to Throne](@/headless/thronectl.md#connect).
- At every start it starts the last profile again, with TUN if TUN was on. `--no-resume` skips this once.
- When the core keeps failing or a profile hangs, it exits with a code that makes the service manager start it again. See [Exit codes](@/headless/command_line.md#exit-codes).

## Differences from the desktop app {#differences}

Headless Throne asks nothing. It decides on its own or writes to the log:

- TUN without the needed privileges fails with `needs_privileges`. `Throne --check` says what is missing.
- If the [kill switch](@/advanced/kill_switch.md) cannot work, Throne reports `kill_switch_failed` and refuses to start profiles.
- A VPN profile that asks for a one-time code gets it from its [OTP account](@/advanced/vpn_profiles.md#otp-manager). Any other question, such as a web sign-in, is cancelled when it expires, after 5 minutes at most (`vpn_challenge_pending`). Rejected saved credentials give `vpn_auth_failed`.
- A setting that needs a program restart applies after `thronectl restart`.

There is no system proxy (`thronectl proxy on` fails with `system_proxy_unsupported`), no tray icon, no global hotkeys and no built-in updater: update through the package, the Docker image or the installer. `throne://` [deep links](@/advanced/deeplinks.md) and file associations do not work; use `thronectl import`.

Set up OTP accounts, VPN credentials and the settings that the API does not manage in a desktop Throne, and move them over with a [backup](@/headless/thronectl.md#from-desktop). A systemd user unit shares its data with the desktop app: stop the unit and open the app.

## Running beside a desktop Throne {#beside-desktop}

Only one Throne at a time can run TUN on a computer, because the `throne-tun` interface, TUN's address range (`tun_ipv4_cidr`, `172.19.0.1/24` by default) and the core's DNS port (`dns_in_port`, 5533 by default) belong to the whole computer. When one of them is taken, `thronectl tun on` fails with `tun_conflict`, and `thronectl status` shows what holds it. If a Docker bridge or another network uses the TUN range, move TUN to a free range; if another program holds the DNS port, give Throne another one:

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24    # or: dns_in_port=PORT
thronectl tun on
```

While a headless Throne runs, a desktop Throne on the same computer warns at start, refuses TUN, and does not update itself if both run from the same installation.

Two Thrones never share a data directory. If a user unit uses the desktop app's data directory, the desktop app says that Throne is running in the background and exits. Stop the unit first: `systemctl --user stop throne`.

## Device ID {#device-id}

Some subscriptions ask for a device ID (see [User-Agent and HWID](@/guides/subscriptions.md#user-agent-and-hwid)). On Linux, Throne reads `/etc/machine-id`, else `/var/lib/dbus/machine-id`. Without either, as in a container, it creates an ID in `config/hwid` at the first start, which stays with the data directory. macOS uses the hardware UUID, Windows the `MachineGuid` from the registry.

The official Docker image has no machine ID, so every container gets its own. If you build your own image, do not put a `machine-id` file into it, or every container made from it sends the same ID.
